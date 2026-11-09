# ADR-001 · La conversación vive en un canal propio

**Estado:** Propuesta · **Fecha:** 2026-11-09

## Contexto
El servicio emula un grupo de WhatsApp. El canal no es nuestro, por lo que no podemos medir lectura, participación ni atribución.

## Decisión
La comunidad se aloja en una **app propia (web/PWA)**. WhatsApp e Instagram (vía ManyChat) quedan como **canales de captación y avisos** que llevan a la app con links medibles.

## Consecuencias
- ✅ Medición completa de eventos en PostHog/GA4; identidad unificada por miembro.
- ✅ Datos y relación con el miembro son propiedad nuestra.
- ⚠️ Riesgo de fricción/abandono en la migración → mitigar con onboarding simple, push y avisos por WhatsApp (ver spec R1).
- ⚠️ Hay que construir la capa de comunidad (chat, hilos, moderación).

## Alternativas descartadas
- Mantener el grupo de WhatsApp y “medir por fuera”: imposible medir lectura/participación.
- Discord/Circle/Mighty: mismo problema de control de datos.
