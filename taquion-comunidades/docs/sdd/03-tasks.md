# 03 · Tareas

Cada tarea cita el requisito que cubre. Estado: ⬜ pendiente · 🟡 en curso · ✅ hecho.

## F0 · Descubrimiento y diseño (ahora)
- ✅ T-001 Constitución y spec borrador — `00`, `01`
- ✅ T-002 Plan técnico y opciones de stack — `02`
- ✅ T-003 Tracking plan inicial — `docs/tracking-plan.md` (REQ-06, NFR-04)
- ✅ T-004 Moodboards (3 direcciones) — `design/moodboards/`
- ✅ T-005 Wireframes 01–08 con eventos anotados — `design/wireframes/`
- ⬜ T-006 Responder preguntas abiertas (spec §9) y validar supuestos R2/R3
- ⬜ T-007 Elegir dirección visual → cerrar ADR-003
- ⬜ T-008 Revisar wireframes con 3–5 miembros reales (prueba de guion)

## F1 · Diseño de alta fidelidad
- ⬜ T-101 Design tokens (color, tipo, espaciado) de la dirección elegida
- ⬜ T-102 UI kit en Figma/Storybook: mensaje, hilo, canal, inbox, KPI
- ⬜ T-103 Prototipo clickeable de WF-04, 02 y 08 (REQ-01, 02)

## F2 · MVP
- ⬜ T-201 Repositorio base (Next.js+TS+Tailwind), CI, entorno Easypanel — NFR-06
- ⬜ T-202 Auth OTP/magic link + consentimiento — REQ-01, NFR-01
- ⬜ T-203 Canales + mensajes en tiempo real — REQ-02
- ⬜ T-204 Hilos, reacciones, menciones, adjuntos — REQ-02
- ⬜ T-205 Capa de eventos (cliente + servidor → PostHog + GA4 MP) — NFR-04
- ⬜ T-206 Flow ManyChat de bienvenida + `join_token` — REQ-01
- ⬜ T-207 PWA + Web Push — REQ-07
- ⬜ T-208 Piloto con 1 comunidad

## F3 · Operación y medición
- ⬜ T-301 Inbox admin (filtros, etiquetas, asignación, notas) — REQ-04, REQ-10
- ⬜ T-302 Perfil 360 — REQ-05
- ⬜ T-303 Dashboard de salud y embudo — REQ-06
- ⬜ T-304 Panel de integraciones + depurador — REQ-09

## F4 · Crecimiento
- ⬜ T-401 Motor de reglas + acciones ManyChat — REQ-08
- ⬜ T-402 Notificaciones WhatsApp medibles — REQ-07
- ⬜ T-403 Windsor.ai → BigQuery/Looker (costo por miembro activado)
- ⬜ T-404 Chatwoot para soporte 1:1 (opcional)
- ⬜ T-405 Encuestas y eventos en vivo — REQ-03
