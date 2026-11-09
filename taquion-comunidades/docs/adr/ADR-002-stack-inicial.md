# ADR-002 · Stack inicial: núcleo propio + integraciones; Chatwoot como referencia

**Estado:** Propuesta · **Fecha:** 2026-11-09

## Contexto
Necesitamos un inbox para ver todos los chats (referencia Chatwoot), medición con PostHog/GA4, captación con ManyChat, atribución con Windsor.ai, y despliegue sencillo (Easypanel).

## Decisión
- **Núcleo de comunidad propio** (Next.js + Postgres + tiempo real; Supabase como acelerador opcional).
- **Chatwoot = referencia de UX** para el inbox admin; en fase 2, instancia opcional para soporte 1:1.
- **PostHog** (producto) + **GA4** (adquisición, conversiones server-side) + **Windsor.ai** (atribución/pauta).
- **ManyChat** para captación y avisos en WhatsApp/IG.
- **Easypanel** sobre VPS para app, Postgres, Redis y (opcional) Chatwoot; PostHog en Cloud al inicio.

## Razones
Chatwoot modela conversaciones 1:1 cliente–agente; no ofrece canales grupales, hilos ni presencia, que son el corazón de la comunidad. Construir esa capa nos da control total de eventos.

## Consecuencias
- Más trabajo inicial que adoptar una herramienta cerrada, a cambio de control y medición.
- Si el tiempo apremia: Plan B con Zulip/Mattermost (instrumentación más difícil).

## Por validar
- Conectores reales de Windsor.ai (ManyChat/PostHog).
- Capacidad del VPS elegido; costo de PostHog Cloud vs. self-host según volumen.
