# 01 · Spec — Comunidades de Taquión

Estado: **BORRADOR v0.1** · Se refina con los wireframes (`design/wireframes/`).

## 1. Problema
El servicio “Comunidades de Taquión” emula un grupo de WhatsApp interactivo. Como el canal es de WhatsApp (no nuestro), **no podemos medir la interacción** de las personas: no sabemos quién lee, quién escribe, qué contenido funciona ni qué anuncio trajo a los miembros activos.

## 2. Objetivo
Pasar la comunidad a un **canal propio** con eventos medibles en **PostHog** (producto) y **Google Analytics 4** (adquisición), con captación y avisos vía **ManyChat** (WhatsApp/IG) y atribución consolidada con **Windsor.ai**.

## 3. Alcance
**Dentro (MVP):** comunidad web/PWA con canales, hilos y reacciones · inbox para el equipo (ver todos los chats) · captación ManyChat → app · tracking plan completo · dashboard de salud · automatizaciones básicas de re-enganche.
**Fuera (por ahora):** app nativa iOS/Android · pagos/membresías · marketplace · video en vivo propio (se enlaza a herramienta externa) · IA conversacional avanzada.

## 4. Actores
| Actor | Necesidad principal |
|---|---|
| **Miembro** | Conversar fácil, no perderse nada, recibir avisos útiles |
| **Moderador / equipo** | Ver todos los chats, responder rápido, detectar problemas |
| **Dueño / marketing** | Entender interacción y atribución para decidir inversión |
| **Admin técnico** | Mantener integraciones y calidad de datos |

## 5. Requisitos funcionales
| ID | Requisito | Criterio de aceptación (resumen) | WF |
|---|---|---|---|
| REQ-01 | Un miembro puede unirse desde un link de WhatsApp y entrar a la comunidad sin fricción | Link con `join_token` → registro en < 60 s → entra a #general; identidades vinculadas | 04 |
| REQ-02 | Canales públicos y privados, mensajes en tiempo real, hilos, reacciones, menciones, adjuntos | Mensaje visible a otros en < 1 s (p95); hilos y reacciones persistentes | 02, 08 |
| REQ-03 | Encuestas rápidas y eventos en vivo con RSVP | Voto único por miembro; RSVP emite evento y recordatorio | 02 |
| REQ-04 | El equipo ve **todos** los chats (canales, DMs, hilos, WhatsApp) en un inbox unificado con filtros, etiquetas, asignación y notas internas | Filtrar por canal/estado/etiqueta; asignar; nota interna invisible al miembro | 01 |
| REQ-05 | Perfil 360 del miembro con línea de tiempo de eventos, atribución e identidades | Muestra últimos 100 eventos y fuente/UTM | 05 |
| REQ-06 | Dashboard de salud: WAU, msgs/activo, % que escribe, TTFR, retención, embudo, heatmap, atribución | Datos coinciden con PostHog ±1 % | 03 |
| REQ-07 | Notificaciones push (PWA) y por WhatsApp (ManyChat) con link profundo medible | `notification_sent` → `notification_clicked` enlazados por `notif_id` | 04, 08 |
| REQ-08 | Reglas de automatización basadas en eventos (bienvenida, re-enganche, pregunta sin responder) | Regla dispara flow de ManyChat y registra `automation_triggered` | 06 |
| REQ-09 | Panel de integraciones con estado y depurador de eventos | Muestra último evento, errores GA4/PostHog, cobertura del tracking plan | 07 |
| REQ-10 | Moderación: silenciar, eliminar, fijar, reportar; registro de acciones | Acción auditada con `actor_id` | 01 |

## 6. Requisitos no funcionales
| ID | Requisito |
|---|---|
| NFR-01 | **Privacidad**: sin contenido de mensajes en analítica; consentimiento versionado; derecho de borrado (RGPD/LGPD) |
| NFR-02 | **Rendimiento**: carga inicial móvil < 2.5 s (LCP, 4G); entrega de mensaje p95 < 1 s |
| NFR-03 | **Disponibilidad**: 99.5 % MVP; respaldo diario de la base |
| NFR-04 | **Calidad de datos**: ≥ 95 % de eventos con propiedades obligatorias; deduplicación por `event_id` |
| NFR-05 | **Accesibilidad**: WCAG 2.1 AA en flujos clave |
| NFR-06 | **Operación**: despliegue reproducible con Docker/Easypanel; configuración por variables de entorno |

## 7. Métricas de éxito (norte)
- **Activación**: % de nuevos miembros con ≥ 1 mensaje o ≥ 3 reacciones en 24 h → meta inicial 40 %.
- **WAU / miembros totales** → meta 60 %.
- **Retención D30** → meta 50 %.
- **Cobertura de medición**: 100 % de interacciones visibles en PostHog (hoy ≈ 0 %).
- **Atribución**: costo por *miembro activado* por fuente disponible en dashboard.

## 8. Riesgos y supuestos (verificar)
| # | Riesgo / supuesto | Mitigación |
|---|---|---|
| R1 | Mover la gente de WhatsApp a una app puede bajar la participación | Onboarding de baja fricción (OTP por WhatsApp), push + avisos por WhatsApp, PWA instalable; piloto con un grupo pequeño |
| R2 | **Supuesto a verificar**: la API oficial de WhatsApp Business no está pensada para grupos grandes de conversación; no se puede medir un grupo estándar de WhatsApp | Tratar WhatsApp como canal de entrada/avisos; revisar el estado actual de la Groups API de Meta antes de descartar |
| R3 | **Supuesto a verificar**: qué conectores exactos ofrece Windsor.ai (¿ManyChat? ¿PostHog?) | Validar en su catálogo; si falta, enviar a BigQuery vía webhook/ETL propio |
| R4 | Chatwoot es un inbox 1:1 de soporte, no una plataforma de comunidad con canales | Usarlo como **referencia de UX y bandeja de DMs/soporte**, no como motor de comunidad (ver ADR-002) |
| R5 | Cumplimiento de políticas de WhatsApp (plantillas, opt-in, ventana de 24 h) | Opt-in explícito en registro; usar plantillas aprobadas desde ManyChat |

## 9. Preguntas abiertas
1. ¿Cuántas comunidades y cuántos miembros hoy? (dimensiona infraestructura)
2. ¿Idioma(s) y país(es)? (cumplimiento de privacidad y plantillas WhatsApp)
3. ¿Hay planes de monetizar (membresías) en 6–12 meses?
4. ¿Equipo técnico disponible (tamaño y stack preferido)?
5. ¿Ya existe cuenta de ManyChat Pro, PostHog, GA4 y Windsor.ai?
6. ¿Se necesita mensajería de voz/video propia o basta con enlaces externos?
