# ARTIFACT-FOR-001
# Carnet de Voluntaria (volunteer ID card generation)
# Status: ready
# ICP: Volunteers (individual member, self-service)
# Depends on: none

---

## Why we are building this

**Customer problem:**
A registered volunteer has no way to obtain her group ID card ("carnet") herself — today it requires a manual, ad hoc request to the team, and volunteers without a photo on file can't get one at all.

**Evidence:**
| Interview | ICP | Signal |
|-----------|-----|--------|
| conversacion-carnet-grupo-cuidadoras-caracas-2026-07-10 | team | "Pueden sacar su carnet por la web? ... Es para ir haciendole uno a una chica que no tiene" |
| conversacion-carnet-grupo-cuidadoras-caracas-2026-07-10 | team | "Se le pide a las chicas, sería una opción de que ellas puedan subir una foto como de perfil y q puedan usar para el carnet" |

**ICP-weighted signal score:** 0.5
**Confidence:** low — sole evidence is one internal team WhatsApp thread (not yet processed via `synthesize-meeting`), treated as a direct volunteer-originated request per VG3 resolution; no formal customer interview yet.

---

## Job to be done

A registered volunteer generates a printable ID card with her photo so she can prove her affiliation with the group in the field.

---

## Scope

### In scope
- [ ] Optional photo upload field added to `voluntarios-registro` (registration form)
- [ ] Photo upload/edit available post-registration in the member dashboard/portal, for volunteers already registered without a photo
- [ ] "Generate carnet" action in the member dashboard, async (queue → poll/notify → download)
- [ ] Carnet rendering is config-driven per group (`group_id` → template config/assets), natively rebuilt (not Canva API); Cuidadoras Caracas is the one live template at launch
- [ ] Output is a print-ready PDF/PNG at correct physical ID-card dimensions, with foto, nombre, apellido, cédula

### Out of scope
- Moderator/admin bulk-generation of carnets on behalf of volunteers — no admin surface requested yet
- Physical printing/fulfillment/mailing of cards — digital output only is in scope
- Canva Connect API / live sync back into Canva — rejected in favor of a natively rebuilt template; Autofill/Bulk Create requires Canva Enterprise and a runtime dependency on Canva's domain
- QR code / scannable verification on the carnet — not mentioned in evidence, nice-to-have
- Self-serve template editor/designer UI for PMs to author new group templates — adding template #2 is a manual dev task for now

### Success definition
A volunteer who has uploaded a photo can generate and download a print-ready carnet from her dashboard within seconds, without asking a moderator for help.

### MVP boundary
Optional registration photo field + post-registration photo edit + single live template (Cuidadoras Caracas) rendered via a config-driven per-group system + async generate-and-download flow.

---

## Acceptance criteria

- [ ] Given a new volunteer filling out the registration form, when she reaches the photo field, then she can optionally upload a photo or leave it blank and still complete registration.
- [ ] Given a volunteer already registered without a photo, when she opens the photo section of her member dashboard, then she can upload one and it is saved as her profile photo.
- [ ] Given a volunteer with an existing profile photo, when she uploads a new one, then it replaces the previous photo used for the carnet.
- [ ] Given a volunteer with a profile photo, when she selects "Generate carnet" in her dashboard, then a generation job is queued and she sees a pending/processing status rather than a blocking wait.
- [ ] Given a queued carnet generation job, when it completes, then the volunteer sees a ready status and can download the carnet.
- [ ] Given a volunteer without a profile photo, when she opens "Generate carnet", then she is prompted to upload a photo first rather than being allowed to generate an incomplete card.
- [ ] Given a volunteer in a group with a configured carnet template (Cuidadoras Caracas), when her carnet is generated, then it renders using that group's template config with foto, nombre, apellido, cédula placed per the reference design.
- [ ] Given a volunteer in a group without a configured template, when she attempts to generate a carnet, then the system shows a "not available yet" state instead of failing silently or calling out to Canva.
- [ ] Given a completed carnet generation, when the volunteer downloads it, then the file is a PDF/PNG at the print-ready dimensions defined by the reference PDF, matching her current registration data.

---

## Existing surfaces affected

| Surface | Change |
|---------|--------|
| `voluntarios-registro` | Add optional profile-photo upload field to the registration form |
| `grupos-voluntarios-entidad` | Used as the key for per-group carnet template config (multi-tenant model already supports this) |

## New capabilities required

| Capability | Description |
|------------|-------------|
| `voluntarios-foto-perfil` | Profile photo upload/edit, at registration and post-registration, storage + retrieval for use in carnet rendering |
| `carnet-generacion` | Config-driven per-group carnet template rendering, async generation job, print-ready PDF/PNG export |

---

## Dependencies

| Feature | Status |
|---------|--------|
| none (blocking) | — |

Note: `Context/capabilities-inventory.md` references a pending, not-yet-shipped "edición de voluntarias" UI (`forvzla-app/docs/spec-cuidadoras-coordinacion-ui.md`). VG1 confirmed this feature does not duplicate that effort, but implementation should stay aware of it since both touch volunteer profile editing. `Roadmap/` has no snapshot entries yet to cross-check against.

---

## Mockup

No mockup.html. Reference assets provided by PM (not yet copied into `Context/source-docs/` — see editorial note below):
- Sample rendered carnet: "asly monjes .pdf" (Ashly Monjes) — defines print dimensions and field layout (foto, nombre, apellido, cédula)
- Canva template: https://canva.link/03vyok16l02lf14 (Cuidadoras Caracas design; source for the native rebuild, not a runtime dependency)

UX agent in ADWF pipeline to produce dashboard/registration UI mockups.

---

## Stack notes

- React + Vite + Supabase + PostHog (ForVzla)
- Native template rendering (not Canva API) — needs a server-side render path (e.g. HTML/CSS-to-image or programmatic canvas composition) capable of async execution, since Canva Autofill/Bulk Create requires Canva Enterprise and was ruled out
- Async job: queue + status field (e.g. Supabase table + Edge Function), not a synchronous request/response
- Storage: Supabase Storage for volunteer profile photos and generated carnet files
- Template config keyed by `group_id`, consistent with the existing `grupos_voluntarios` multi-tenant model
- Exact print dimensions to be extracted from the reference PDF/Canva export once copied into `Context/source-docs/`

---

## ADWF pipeline gates

| Gate | Trigger | Approver |
|------|---------|----------|
| Spec review | Before analyser | PM |
| Plan review | After planner | PM |
| PR review | After builder | Eng lead |

---

## Validation gates resolved

| Gate | Question | Answer |
|------|----------|--------|
| VG1 | Does this duplicate the pending "edición de voluntarias" UI in spec-cuidadoras-coordinacion-ui.md? | Confirmed by PM — no duplication; the two efforts are separate and compatible. |
| VG2 | What physical dimensions should the carnet be for print purposes? | Use the dimensions from the reference PDF ("asly monjes .pdf", matching the Canva template) as the print spec. Source PDF/Canva export should be saved to `Context/source-docs/` for exact mm/px values. |
| VG3 | Does self-service carnet generation solve real volunteer pain, confirmed by a direct volunteer/ICP signal rather than team-observed only? | Confirmed by PM — the WhatsApp thread is itself a direct request: Dari asked on behalf of a specific volunteer who needed a carnet urgently and didn't have one. Treated as a real, direct volunteer-originated signal. |

---

## Linked artifacts

scope_file: Features/carnet-voluntaria/scope.yaml
mockup_file: null
interview_ids: [conversacion-carnet-grupo-cuidadoras-caracas-2026-07-10]
linear_issue_id: null
pr_url: null
