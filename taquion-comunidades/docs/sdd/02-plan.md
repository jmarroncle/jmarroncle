# 02 · Plan técnico (cómo)

Estado: **BORRADOR v0.1** · Depende de `01-spec.md`. Decisiones puntuales en `docs/adr/`.

## 1. Arquitectura lógica

```
 Anuncios (Meta/Google) ─► Landing (GTM+GA4+PostHog)
                              │  CTA
                              ▼
 WhatsApp/IG ◄────► ManyChat (flows, tags, avisos)
        │  join_token                     ▲ API/webhooks
        ▼                                 │
 ┌──────────────── App Comunidad (Web/PWA) ────────────────┐
 │  Frontend Next.js  ─►  API (NestJS/Next route handlers) │
 │  Realtime (WebSocket)   Auth (OTP/magic link)           │
 │  Postgres (miembros, canales, mensajes, hilos)          │
 │  Redis (presencia, colas)   S3/MinIO (adjuntos)         │
 └───────┬───────────────────────────────┬─────────────────┘
         │ eventos (cliente + servidor)  │ webhooks/API
         ▼                               ▼
   PostHog  ──────────►  GA4 (Measurement Protocol)     Chatwoot (DMs/soporte)
         │                      │
         └────► Windsor.ai ◄────┴── Meta Ads / Google Ads
                    │
                    ▼
        BigQuery / Looker Studio  (atribución y costo por miembro activo)
```

**Idea clave:** el **servidor** de la app es la fuente de verdad de eventos (`message_sent`, etc.) y los reenvía a PostHog y GA4. El cliente solo aporta eventos de UI (vistas, foco, clics). Así no se pierden eventos por bloqueadores y se evita la doble contabilidad.

## 2. Opciones de stack

### 2.1 Núcleo de comunidad (la decisión grande)
| Opción | Qué es | Pros | Contras | Veredicto |
|---|---|---|---|---|
| **A. Custom: Next.js + Postgres + WebSocket** | Construimos la capa de comunidad | Control total de eventos y UX; sin lock-in; encaja con la misión | Más trabajo inicial (chat en tiempo real, hilos, moderación) | ✅ **Recomendada** |
| **B. Custom sobre Supabase** (Postgres + Realtime + Auth + Storage) | Igual que A pero con BaaS | Muy rápido para MVP; auth/realtime incluidos; autoalojable | Dependencia de Supabase (aunque open source) | ✅ **Alternativa para acelerar** |
| C. Base OSS adaptada (**Zulip**, **Mattermost**, **Rocket.Chat**, **Matrix/Element**) | Reutilizar un chat existente | Chat maduro, hilos, apps | UX no es “comunidad de marca”; instrumentar eventos es más difícil; UI propia limitada | Plan B si el tiempo apremia |
| D. Plataforma SaaS (Circle, Mighty Networks, Discord) | Comprar comunidad | Salida inmediata | **Vuelve a perder control de datos** — contradice la misión | ❌ |
| E. Solo **Chatwoot** | Usar Chatwoot como comunidad | Ya tiene inbox, etiquetas, automatizaciones, widget | Modelo 1:1 (cliente–agente), sin canales/hilos grupales | ❌ como núcleo; ✅ como referencia y para DMs/soporte |

### 2.2 Chatwoot: ¿qué tomamos?
- **UX de referencia** para el inbox (WF-01): layout 4 zonas, filtros, etiquetas, notas internas, respuestas rápidas, reglas de automatización.
- **Opcional (fase 2):** desplegar Chatwoot para **soporte 1:1** (DMs de ayuda) y conectar WhatsApp Cloud API; los datos de contacto se sincronizan con `member_id`.
- No es el motor de los canales grupales.

### 2.3 Medición
| Capa | Herramienta | Rol |
|---|---|---|
| Producto (comportamiento) | **PostHog** (Cloud o autoalojado) | Eventos, funnels, cohortes, retención, session replay *solo UI* (enmascarando mensajes) |
| Adquisición web | **GA4 + GTM** | Landing, UTM, conversiones; `member_activated` vía **Measurement Protocol** para optimizar campañas |
| Atribución y pauta | **Windsor.ai** | Unifica costos/atribución (Meta, Google, GA4) → BigQuery/Looker. *Validar conectores exactos* |
| Captación y avisos | **ManyChat** | Flows en WhatsApp/IG, tags, avisos con link medible |

**Regla de oro:** `distinct_id` (PostHog) = `member_id` = `user_id` (GA4). El `join_token` une el contacto de ManyChat con el miembro al registrarse.

### 2.4 Infraestructura y despliegue (Easypanel)
Easypanel gestiona Docker con plantillas de un clic. Propuesta inicial en **un VPS** (4 vCPU / 8 GB para empezar):

| Servicio | Cómo | Notas |
|---|---|---|
| App comunidad (Next.js + API) | App desde GitHub (Dockerfile) | Despliegue por push |
| Postgres | Plantilla Easypanel | Backups programados a S3 |
| Redis | Plantilla Easypanel | Presencia y colas |
| MinIO / S3 | Plantilla o S3 externo | Adjuntos |
| PostHog | **Cloud** al inicio (autoalojado exige ≥ 8–16 GB RAM) | Pasar a self-host si el volumen/privacidad lo exige |
| Chatwoot (fase 2) | Plantilla Easypanel | Postgres + Redis propios |
| Proxy / TLS | Traefik integrado de Easypanel | Dominios y certificados automáticos |

### 2.5 Stack inicial recomendado (MVP)
- **Frontend**: Next.js (App Router) + TypeScript + Tailwind + shadcn/ui · PWA (service worker + Web Push)
- **Backend/DB**: Postgres + Supabase *(o NestJS + Prisma si preferimos control total)* + WebSocket (Supabase Realtime o Socket.io)
- **Auth**: OTP / magic link por WhatsApp (vía ManyChat) y email como respaldo
- **Eventos**: SDK `posthog-js` + `posthog-node` + cola → GA4 Measurement Protocol
- **Orquestación**: ManyChat (flows) + webhooks hacia la app
- **Infra**: Easypanel en VPS · GitHub Actions para CI
- **Diseño**: HTML/CSS estático ahora (esta maqueta) → Figma/Storybook cuando se elija dirección visual

## 3. Modelo de datos (borrador)
`member` (id, nombre, teléfono_hash, consentimiento, origen/utm, manychat_id, created_at) · `community` · `channel` (tipo, privacidad) · `membership` · `message` (id, channel_id, author_id, thread_id?, created_at) · `reaction` · `thread` · `poll`/`vote` · `event`/`rsvp` · `notification` (id, canal, estado) · `automation_rule`/`run` · `moderation_action`.
> El contenido del mensaje vive **solo** en Postgres; a analítica solo van metadatos.

## 4. Seguridad y privacidad
- Teléfonos cifrados/hasheados; PII fuera de eventos.
- Consentimiento versionado (`consent_version`) y opción de retirarlo.
- Roles: miembro · moderador · admin; RLS/policies por comunidad.
- Retención configurable; borrado de miembro propaga a PostHog (delete person) y ManyChat.

## 5. Hitos
| Fase | Entregable | Resultado |
|---|---|---|
| **F0 (actual)** | Spec, moodboards, wireframes, tracking plan | Alinear visión |
| F1 | Diseño UI alta fidelidad + prototipo clickeable | Validar con usuarios |
| F2 | MVP: auth, canales, mensajes, hilos, eventos a PostHog/GA4 | Piloto con 1 comunidad |
| F3 | Inbox admin, perfil 360, dashboard, ManyChat embudo | Medición completa |
| F4 | Automatizaciones, push/WhatsApp, Chatwoot (soporte), Windsor.ai | Escalar y optimizar |
