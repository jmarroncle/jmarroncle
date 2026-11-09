# 00 · Constitución del proyecto

> SDD (Spec-Driven Development): la **spec es la fuente de verdad**; el código, los diseños y los tests se derivan de ella. Este documento fija los principios que no se negocian.

## Misión
Que “Comunidades de Taquión” sea un **canal propio** (no un grupo de WhatsApp) donde cada interacción de cada miembro sea **medible, atribuible y accionable**.

## Principios
1. **El canal es nuestro.** La conversación vive en nuestra app. WhatsApp/Instagram son canales de *captación y avisos*, no de conversación.
2. **Si no se mide, no existe.** Toda funcionalidad define sus eventos *antes* de construirse (ver `docs/tracking-plan.md`). Una historia de usuario sin eventos no está “lista”.
3. **Medir sin espiar.** Se miden *metadatos de comportamiento*, nunca el contenido de los mensajes. Consentimiento explícito. Mínimo dato necesario.
4. **Una identidad por miembro.** `member_id` único vinculado a ManyChat, PostHog, GA4 y Chatwoot.
5. **Familiar antes que ingenioso.** La UX debe sentirse tan simple como WhatsApp; la ventaja está en hilos, recursos y datos, no en complejidad.
6. **Construir lo diferencial, comprar lo commodity.** Inbox/ticketing, analítica y automatización de WhatsApp se *integran*; la capa de comunidad y su medición se *construye*.
7. **Autoalojable y portable.** Preferir componentes open source desplegables con Easypanel/Docker; evitar lock-in sin salida.
8. **Spec → Plan → Tareas → Código.** Ningún cambio de código sin una tarea trazable a un requisito (`REQ-xx`).

## Flujo SDD
```
Constitución → Spec (qué/por qué) → Plan (cómo) → Tareas (pasos) → Implementación → Verificación vs. spec
      ▲                                                                              │
      └────────────────────── ajustes de la spec (ADR si es decisión) ◄───────────────┘
```

## Definición de “hecho”
- Cumple los criterios de aceptación de su `REQ-xx`.
- Sus eventos se emiten con las propiedades del tracking plan y aparecen en el depurador (WF-07).
- Accesible (WCAG AA básico) y usable en móvil.
- Documentación y ADR actualizados si cambió una decisión.
