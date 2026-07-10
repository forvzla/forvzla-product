# Product baseline

> Hand-edited. Current product state summary.

<!-- ✎ HAND-EDITED -->
## Mission

> "Unificar en un solo sitio quien quiera ayudar con quien lo necesite" — "ayudar a que la gente se ayude."
> — Cris, PM venezolana (ground-truth input for the project)

North star: fast, organic, no bureaucracy. Don't trade that away for process.

## Trigger / context

Built as SOS relief coordination in response to the **Terremoto Yaracuy, 24-jun-2026** (Venezuela). Official figures cited in `HANDOFF.md` as of 26-jun-2026: ~235 dead, +4,300 injured, La Guaira declared disaster zone — **treat as stale**, not a live figure.

## Team

### Product development team

| Person | Role | Location |
|---|---|---|
| Maria Cristina | Project lead | Spain |
| Anyelisa | Dev / product | Spain |

### Volunteers

| Person | Team | Role | Location |
|---|---|---|---|
| Cris | ForVzla | Venezuelan PM — ground-truth from the field | Venezuela |
| Ren (Renata Zavala) | Madres Voluntarias (Cuidadoras Caracas) | General coordinator | Mexico |
| Dari | Madres Voluntarias | Systems / volunteer registry | Falcón, Venezuela |
| Yeudi | Madres Voluntarias | Lawyer lead — permits, shelters | — |

<!-- agent-managed -->
## Repos (see `Context/org-repositories.md`)

| Repo | Role |
|---|---|
| **{{APP_REPO}}** (`forvzla-app`) | Application delivery — WholeLoop v0.2 managed |
| **{{SCRAPER_REPO}}** | Social ingestion — today lives inside `forvzla-app/scraper/`, not a separate deployed service |
| **forvzla-product** | This repo — discovery, specs, scope |
| **Guau** (`guau.app`) | Separate external product (pet-care coordination). Link-out contract only — see `capabilities-inventory.md#guau-refugios-link-out`. Not delivered through this repo's WholeLoop pipeline. |

## How this fits WholeLoop

Per `CLAUDE.md`: `Context → Interviews + Surveys → Analytics → Product Meetings → OST → Roadmap → Features → (handoff) {{APP_REPO}}/inbox`.

This file is a **Context/** anchor — read before scoping (`brainstorm-feature`) or spec-building (`build-spec`). Once a `Features/<slug>/scope.yaml` is build-spec'd, the artifact is copied to `{{APP_REPO}}/inbox/` and enters WholeLoop v0.2 on the app side: `spec-review → (optional ui-ux-designer) → planner → builder|manual → reviewer → pr-agent → handoff`. `handoff` writes back into this repo (`Progress/adwf-handoffs/`, `delivery_notes` in `scope.yaml`, `Progress/signals/`), which is what keeps this baseline and `capabilities-inventory.md` from going stale.

## Product summary

Single-page PWA. No build step, no framework, no bundler — by design (30s deploys, works on low connectivity/coverage). Cost: **$0/month**. Deploy: Vercel serving `public/`.

**Stack:** HTML/CSS/JS inline + Supabase (DB, Auth, Realtime) + Leaflet/OSM (no API key) + Node scraper (Apify → Claude Haiku classification).

## Architecture

```
Mobile users
    │
public/index.html (PWA, single file)
  ├── Leaflet + leaflet.heat   (OSM tiles via CDN)
  └── supabase-js              (RPC + realtime, via CDN)
    │  RPC contract: necesidades_cercanas(), confirmar_necesidad()
    ▼
Supabase (project ebsgvamzaegjgpjkpick)
  ├── necesidades, recursos, centros_acopio, edificios_colapsados   (SOS core)
  ├── asesores                                                      (advisory directory)
  ├── grupos_voluntarios, brigadas, jornadas, sitios, ...           (volunteer coordination)
  └── donaciones_grupo, gastos_grupo, aportes_*                     (transparency)
    ▲
scraper/ingesta_redes.js (Node 18, ESM, run manually — not a deployed service)
  Apify (IG/TikTok public) → Claude Haiku (classify) → geocode → dedup → insert
```

## Current modules

Full slug-level detail lives in `Context/capabilities-inventory.md`. At a glance:

1. **SOS map** — core emergency flow (request help, nearby-help, anti-duplicates, collection centers, collapsed buildings)
2. **Social listening / ingestion** — scraper + moderation queue
3. **Rescue triage** — rescue-lead status workflow, link-only access
4. **Admin & moderation** — magic-link auth, content management
5. **Advisory directory** — `asesores` (individuals + organizations)
6. **Volunteer group coordination** — reusable `grupos_voluntarios` entity; piloted with Cuidadoras Caracas (brigades, jornadas, tasks, sites, inventory, per-group moderators, media log)
7. **Transparency & contributions** — donations/expenses ledger, receipts, pledges
8. **External** — Guau shelters/adoption link-out (not implemented here)

## Hard constraints / standing decisions

Don't relitigate these without an explicit ask — they're deliberate, not oversights (source: `forvzla-app/.cursorrules`, `HANDOFF.md`):

- `index.html` stays a single file — no React/Vite/bundler migration without explicit request.
- No `localStorage`/`sessionStorage` — state lives in memory + Supabase.
- `anon`/publishable key is intentionally public in the HTML; security comes from RLS. `service_role` key is never committed — scraper-only, via env var.
- `necesidades_cercanas` / `confirmar_necesidad` RPCs are a front↔DB contract — a signature change requires updating both sides together.
- UX: minimal typing — GPS, icons, buttons over text fields. Only required field is phone. Venezuelan Spanish, no technical jargon in UI.
- Anti-duplicate logic (<200m + same type) must stay intact for both citizen reports and scraper ingestion.
- Schema changes go through `supabase/migrations/` (one migration per change). `db/setup_supabase.sql` is a monolithic manual backup for the SQL Editor, **not** the source of truth.

## Known gaps / stale docs

- `HANDOFF.md` is dated 2026-06-28; `supabase/migrations/` run through 2026-07-08 — HANDOFF is stale relative to shipped schema (`capabilities-inventory.md` reflects the newer state as of 2026-07-10).
- `HANDOFF.md`'s P0–P2 backlog (initial map zoom/geolocation default, RLS hardening, `personas_buscadas` module, general volunteer module, notifications, réplica/aftershock alerts, dedicated coordinator panel, offline service worker) has not been reconciled against what has since shipped under Cuidadoras Caracas — needs a pass to mark done / superseded / still open.
- `forvzla-app/docs/spec-cuidadoras-coordinacion-ui.md` describes UI beyond the current registration form (group landing, moderator panel, volunteer editing, RSVP) that is **not yet shipped** — v1 explicitly excludes AI-assisted brigade assignment (only the non-AI fit heuristic is shipped), WhatsApp API integration, and legal/minor-PII case handling.

<!-- ✎ HAND-EDITED -->
## Strategic context

*(PM: add current priorities / constraints here.)*

## Open questions

*(PM: carry forward from `HANDOFF.md` §8 — initial map zoom, RLS hardening timing, lat/lng verification for centers/buildings, custom domain — and anything new.)*

## Editorial notes

*(PM: anything future agents should know that doesn't fit above.)*
