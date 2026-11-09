# Comunidades de Taquión

Maqueta y especificación de una comunidad con **canal propio** (reemplaza el grupo de WhatsApp) para medir cada interacción con **PostHog** y **Google Analytics 4**, captando con **ManyChat** y atribuyendo con **Windsor.ai**. Metodología: **SDD** (Spec-Driven Development). Referencia de UX: **Chatwoot**. Despliegue previsto: **Easypanel**.

> Fase actual: **F0 · descubrimiento y diseño** — moodboards y wireframes. Todavía no hay código de producto.

## Cómo verlo
Son archivos HTML estáticos, sin build. Ábrelos en el navegador o sirve la carpeta:

```bash
cd taquion-comunidades
python3 -m http.server 8080
# http://localhost:8080/design/moodboards/index.html
# http://localhost:8080/design/wireframes/index.html
```

## Estructura
```
taquion-comunidades/
├── docs/
│   ├── sdd/
│   │   ├── 00-constitution.md   principios y flujo SDD
│   │   ├── 01-spec.md           qué y por qué (REQ/NFR, métricas, riesgos)
│   │   ├── 02-plan.md           arquitectura, opciones de stack, Easypanel
│   │   └── 03-tasks.md          tareas trazables a requisitos
│   ├── tracking-plan.md         eventos PostHog/GA4 (fuente de verdad)
│   └── adr/                     decisiones de arquitectura (001–003)
└── design/
    ├── moodboards/index.html    3 direcciones visuales
    └── wireframes/              01 Inbox · 02 Comunidad · 03 Métricas · 04 Onboarding
                                 05 Miembro 360 · 06 Automatizaciones · 07 Integraciones · 08 Mobile
```

## Próximos pasos
1. Responder las preguntas abiertas (`docs/sdd/01-spec.md` §9) y validar supuestos R2/R3.
2. Elegir dirección visual (ADR-003).
3. Iterar wireframes con usuarios → diseño de alta fidelidad (F1).
