# Tracking plan — Comunidades de Taquión

Versión 0.1 · Fuente de verdad de eventos. **Ninguna funcionalidad se da por terminada sin sus eventos.**

## 1. Convenciones
- Nombres de evento en `snake_case`, formato **objeto_acción en pasado** (`message_sent`).
- Propiedades en `snake_case`. Sin contenido de mensajes ni PII (nombre, teléfono, email) en propiedades.
- Todo evento server-side lleva `event_id` (UUID) para deduplicar.
- `distinct_id` PostHog = `member_id` = `user_id` GA4. Antes del registro: ID anónimo + `join_token`.

## 2. Propiedades comunes (todos los eventos)
| Propiedad | Tipo | Ejemplo |
|---|---|---|
| `event_id` | uuid | `…` |
| `member_id` | string | `mem_2f9a` |
| `community_id` | string | `com_01` |
| `platform` | enum | `web` · `pwa` · `whatsapp` · `server` |
| `source`, `medium`, `campaign` | string | UTM de primer toque (persistidos en el miembro) |
| `consent_version` | string | `v1` |
| `app_version` | string | `0.1.0` |

## 3. Eventos

### Adquisición y onboarding
| Evento | Origen | Propiedades clave | Destino | Wireframe |
|---|---|---|---|---|
| `landing_viewed` | cliente | `utm_*`, `referrer` | GA4 + PostHog | 04 |
| `cta_clicked` | cliente | `cta_id` | GA4 + PostHog | 04 |
| `wa_flow_started` | ManyChat→webhook | `flow_id`, `subscriber_id` | PostHog | 04 |
| `interest_selected` | ManyChat→webhook | `interest` | PostHog | 04 |
| `join_link_sent` | ManyChat→webhook | `join_token` | PostHog | 04 |
| `signup_started` | cliente | `join_token` | PostHog | 04 |
| `consent_given` | servidor | `consent_version` | PostHog | 04 |
| `member_joined` | servidor | `join_token`, `source` | **GA4 + PostHog** | 04 |
| `onboarding_step_done` | cliente | `step_id` | PostHog | 04 |
| `member_activated` ⭐ | servidor | `activation_type` (`message`/`reactions`), `hours_since_join` | **GA4 (conversión) + PostHog** | 04 |

### Participación en la comunidad
| Evento | Origen | Propiedades clave | Destino | WF |
|---|---|---|---|---|
| `channel_viewed` | cliente | `channel_id`, `unread_count` | PostHog | 02, 08 |
| `channel_joined` | servidor | `channel_id` | PostHog | 02 |
| `composer_focused` | cliente | `channel_id`, `thread_id?` | PostHog | 02 |
| `message_sent` | servidor | `channel_id`, `thread_id?`, `length_bucket`, `has_attachment`, `mentions_count` | PostHog | 01, 02, 08 |
| `reaction_added` | servidor | `channel_id`, `message_id`, `emoji` | PostHog | 01, 02, 08 |
| `thread_opened` | cliente | `thread_id` | PostHog | 02, 08 |
| `thread_replied` | servidor | `thread_id`, `reply_index` | PostHog | 01, 02, 08 |
| `poll_voted` | servidor | `poll_id` | PostHog | 02 |
| `pinned_opened` / `resource_opened` | cliente | `resource_id`, `type` | PostHog | 02 |
| `live_event_rsvp` | servidor | `event_id` | PostHog | 02 |
| `streak_extended` | servidor | `streak_days` | PostHog | 02 |

### Notificaciones y automatización
| Evento | Origen | Propiedades clave | WF |
|---|---|---|---|
| `notification_sent` | servidor | `notif_id`, `channel` (`push`/`whatsapp`), `template_id` | 04, 06, 08 |
| `notification_clicked` | cliente | `notif_id` | 04, 08 |
| `automation_triggered` | servidor | `rule_id`, `run_id` | 06 |
| `member_reactivated` | servidor | `rule_id`, `days_inactive` | 06 |
| `churn_risk_flagged` | servidor | `rule_id`, `health_score` | 06 |

### Admin y moderación
| Evento | Origen | Propiedades clave | WF |
|---|---|---|---|
| `admin_nav_clicked` | cliente | `section` | 01 |
| `inbox_filter_applied` | cliente | `filter_type` | 01 |
| `conversation_labeled` | servidor | `label`, `actor_id` | 01 |
| `quick_reply_used` / `internal_note_added` | servidor | `actor_id` | 01 |
| `moderation_action` | servidor | `action`, `actor_id`, `target_type` | 01 |

## 4. Reenvío a GA4
Solo se envían a GA4 los eventos de **adquisición y conversión** (`landing_viewed`, `cta_clicked`, `member_joined`, `member_activated`) vía Measurement Protocol, con `client_id` + `user_id`. El resto vive en PostHog para evitar ruido y límites de cuota. `member_activated` se marca como **evento clave (conversión)** en GA4 para optimizar campañas hacia activación real.

## 5. Métricas derivadas (definiciones)
| Métrica | Definición |
|---|---|
| **WAU** | Miembros distintos con ≥ 1 evento de participación (`message_sent`, `reaction_added`, `thread_replied`, `poll_voted`) en 7 días |
| **% que escribe** | Miembros con ≥ 1 `message_sent` / miembros con `channel_viewed`, en 30 días |
| **Lurker** | Miembro con `channel_viewed` pero sin eventos de participación en 30 días |
| **Msgs por activo** | `message_sent` / WAU |
| **TTFR** | Mediana de (primera respuesta − pregunta) en `#dudas` |
| **Retención D30** | % de cohorte de alta con ≥ 1 evento de participación entre día 28 y 32 |
| **Salud (0–100)** | Combinación ponderada de recencia, frecuencia y profundidad (a calibrar con datos reales) |
| **CPA activado** | Gasto (Windsor.ai) / `member_activated` por fuente |

## 6. Calidad de datos
- Cobertura objetivo: 100 % de eventos del plan en 7 días de piloto (WF-07).
- Alertas: evento sin propiedades obligatorias, caída > 30 % vs. semana previa, error de envío a GA4.
- Revisión del plan en cada PR que agregue funcionalidad.
