# Capabilities inventory

> Hand-edited. What the product can do today (links to repos/services).

<!-- agent-managed — populated 2026-07-10 from ForVzla (ForVzla) state: supabase/migrations/, public/, docs/. PM: review, correct wording/slugs, then mark ✎ HAND-EDITED once confirmed. Re-run capability-inventor-style pass after each Progress/ delivery to keep this current. -->

Single repo today: **ForVzla** (ForVzla) — static PWA + Supabase, no separate backend service except the ingestion scraper.

## SOS map (emergency core)

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `sos-mapa-necesidades` | Live map: needs by urgency, collapsed buildings, collection centers; points/heatmap toggle | `public/index.html` | Realtime via Supabase channel |
| `sos-pedir-ayuda` | Request help with minimal typing (GPS or tap-a-building → type grid → urgency); only phone required | `public/index.html`, `necesidades` table | |
| `sos-antiduplicados` | Duplicate detection (<200m + same type); offers "confirm" instead of creating a duplicate | `necesidades_cercanas`, `confirmar_necesidad` RPCs | Haversine, no PostGIS |
| `sos-ayudar-cercania` | "Nearest to me" list sorted by real distance, direct contact (call / WhatsApp prefilled / route) | `public/index.html`, `necesidades_cercanas` RPC | |
| `sos-centros-ofrecer` | Browse collection centers by proximity; offer a resource | `centros_acopio`, `recursos` tables | |
| `sos-edificios-colapsados` | Collapsed-building layer, reports can link to a specific building | `edificios_colapsados` table | 44-record La Guaira seed |

## Social listening / ingestion

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `ingesta-redes-scraper` | IG/TikTok/Twitter/Telegram scrape → Claude classify → geocode → dedup → insert | `scraper/ingesta_redes.js` | Requires Apify + Anthropic tokens; not deployed as a service, run manually |
| `moderacion-posts-redes` | Moderation queue: raw social posts → admin approves → becomes a `necesidad` | `posts_redes` table, `public/admin.html` | Originally PIN-gated, replaced by `admin-auth-magic-link` |

## Rescue operations

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `rescate-triage-workflow` | Rescue-lead status flow per need (`nuevo → verificando → confirmada → atendida/falsa`); link-only access, no login by design | `public/rescatistas.html` | Independent from the public-facing `estado` field |

## Admin & moderation

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `admin-auth-magic-link` | Admin auth via Supabase magic link (`is_admin()`) | `admin_users` table, `public/admin.html` | Replaced the earlier shared-PIN gate |
| `admin-gestion-contenido` | Approve/edit/close/restore needs and centers, delete content | `public/admin.html` | Several incremental migrations (marcar cubierta, restaurar, eliminar) |

## Professional advisory directory

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `asesores-directorio` | Free professional-advice directory (no geolocation), browsable by category | `asesores` table | |
| `asesores-organizaciones` | Organization-type advisors (one row per country, multiple phone numbers) alongside individual advisors | `asesores` table (`tipo`/`pais`/`telefonos`) | Seeded: Psicólogos sin Fronteras |

## Volunteer group coordination (piloto: Cuidadoras Caracas — reusable template)

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `grupos-voluntarios-entidad` | Multi-tenant "group" entity — voluntarios/brigadas/sitios/jornadas hang off a group slug | `grupos_voluntarios` table | Built reusable beyond the Cuidadoras Caracas pilot |
| `voluntarios-registro` | Volunteer self-registration per group | `public/cuidadoras-caracas.html`, `voluntarios` table | |
| `brigadas-afiliacion` | Long-term "brigade" affiliation (interest/skill based) per group | `brigadas` table | |
| `brigadas-asignacion-por-fit` | Suggests a brigade from profile fit (profession/skills/tasks/strengths), no AI; volunteer can change it later | `asignar_brigadas_por_fit` migration | |
| `jornadas-eventos` | Dated field activities ("jornadas") per group, with site and mission | `jornadas` table | |
| `tareas-inscripciones` | Task sign-up within a jornada | `tareas_jornada`, `inscripciones` tables | |
| `sitios-catalogo` | Reusable site/location catalog per group | `sitios` table | |
| `inventario-checklist` | Reusable inventory-item catalog + per-jornada material checklist | `items_inventario`, `necesidades_jornada` tables | |
| `moderadoras-por-grupo` | Per-group moderator roles, invite flow, RLS-scoped | `moderadores_grupo` table | |
| `jornada-media` | Photo/video log per jornada (coordinadoras upload; voluntarias read via RPC) | `jornada_media` table, `jornada-media.js` | |
| `jornada-media-voluntario-lectura` | Volunteers view jornada galleries (thumbs, grid, lightbox) with signed URLs | `media_jornada_voluntario`, `resumen_media_jornadas_voluntario` RPCs | ARTIFACT-FOR-002, jul-2026 |
| `voluntarios-foto-perfil` | Volunteer profile photo for carnet (private bucket + RPC) | `voluntario-fotos`, `carnet.js` | ARTIFACT-FOR-001 |
| `carnet-generacion` | Async PDF carnet generation per group template | `carnet_generaciones`, Edge Function `render-carnet` | ARTIFACT-FOR-001, Cuidadoras Caracas |

## Transparency & contributions

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `transparencia-donaciones-gastos` | Public ledger of donations and expenses per group (money, food, clothing, meds, hygiene, supplies) | `donaciones_grupo`, `gastos_grupo`, `gasto_recibos` tables | |
| `donaciones-soportes` | Proof/receipt attachments on a donation | `donacion_soportes` table | |
| `aportes-necesidad` | Public "contributions declared" progress tracker per need (by person, institution, or group) | `aportes_necesidad` table | |
| `aportes-grupo` | Pledged contributions to a group, not tied to a specific need | `aportes_grupo` table | Confirmed pledges can be moved into `donaciones_grupo` |

## External integrations (linked, not owned by this repo)

| Slug | Capability | Entry point | Notes |
|---|---|---|---|
| `guau-refugios-link-out` | Cross-link contract to Guau (guau.app) for animal-shelter sponsor/adopt flows | `docs/guau-forvzla-integration.md`, `public/mockup-guau-refugios.html` | ForVzla does not implement sponsorship/adoption itself — link-out only |

## Design-only — not shipped, do not treat as capabilities

- `public/mockup-home-evolucion.html`
- `public/mockup-forvzla-guau.html`
- Larger Cuidadoras Caracas UI described in `ForVzla/docs/spec-cuidadoras-coordinacion-ui.md` (landing, panel moderadoras, edición de voluntarias, RSVP) — still pending, track in `Roadmap/`, not here.

## Open questions for PM

- Confirm the slugs above match how you want to reference these in `scope.yaml`'s `related_capabilities` / `new_capabilities_needed`.
- `HANDOFF.md` (last updated 2026-06-28) is stale relative to migrations through 2026-07-08 — this inventory reflects the newer state; consider refreshing `HANDOFF.md` too.
