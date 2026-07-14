# Release: Galerías de jornadas para voluntarias — 2026-07-14

**Spec:** ARTIFACT-FOR-002  
**Epic:** ARTIFACT-FOR-002 (manual tracker)  
**Stories:** jornada-media-backend · jornada-media-ui  
**Ship:** https://github.com/anyelisataveras/ForVzla/commit/9fc4ad81a6b8a140ccc9a7a3863406ec77f74561 (push directo a `master`)

## What shipped

Una voluntaria autenticada puede ver los recuerdos (fotos y videos) de las jornadas que las coordinadoras ya subían al panel — miniaturas en el listado, galería completa y visor fullscreen en el detalle, sin depender de WhatsApp.

### Changes

- RPC `media_jornada_voluntario` y `resumen_media_jornadas_voluntario` con read tokens (patrón `voluntario_foto_read_tokens`)
- Policy storage `jornada-media` ampliada para lectura con token
- Fix migración `20250714153000` — CTE fuera de scope que bloqueaba el listado
- Módulo `public/cuidadoras-caracas/jornada-media.js` (fetch, thumbs, grid, lightbox)
- UI: `mi-cuenta.html`, `jornadas.html`, `jornada.html` — sección «Recuerdos de la jornada»
- Mockup: `public/mockup-voluntaria-galerias-jornadas.html`

### Acceptance criteria delivered

| AC | Status |
|----|--------|
| Thumbs + badge + CTA galería en Mi cuenta (jornada con media) | covered |
| Tab Próximas sin thumbs | **deviation** — thumbs en cualquier tab si hay media (PM jul-2026) |
| Jornada pasada sin media → sin thumbs, «Ver resumen» | covered |
| Grid fotos/videos en detalle | covered |
| Lightbox con navegación y contador | covered |
| Jornada próxima sin fotos → placeholder | covered |
| Visitante anónimo no ve galería | covered |
| RPC solo grupo + signed URLs | covered |

## Deferred

- Feed horizontal «Recuerdos recientes» en dashboard — alternativa en mockup
- Subida de media por voluntarias
- Galería pública sin login
- Compartir nativo / descarga explícita desde visor
- Badge «Nuevas» (fotos últimos 7 días)
- QA manual formal con checklist firmado
- PR dedicado (entrega vía push directo a master)

## Known limitations

- Solo lectura; coordinadoras siguen siendo quienes suben en `coord`
- Read tokens 5 min; signed URLs 1 h en cliente
- Sin tests automatizados
- `workspace/runs/` gitignored — planes y design-notes solo locales

---

## Tracker — paste manual

### Por story

**jornada-media-backend**

```
Delivered in https://github.com/anyelisataveras/ForVzla/commit/9fc4ad8

AC: RPC lectura voluntaria covered. Deviations: none. Deferred: none.
Release note: forvzla-product/Progress/adwf-handoffs/2026-07-14-galerias-jornadas-voluntarias.md
```

**jornada-media-ui**

```
Delivered in https://github.com/anyelisataveras/ForVzla/commit/9fc4ad8

AC: 7/8 covered. Deviations: thumbs también en tab Próximas cuando hay media.
Deferred: QA manual formal, feed Recuerdos recientes.
Release note: forvzla-product/Progress/adwf-handoffs/2026-07-14-galerias-jornadas-voluntarias.md
```

### Epic

```
Epic delivered — 2026-07-14

Stories: jornada-media-backend, jornada-media-ui
Ship: https://github.com/anyelisataveras/ForVzla/commit/9fc4ad8 (master)
Full release note: forvzla-product/Progress/adwf-handoffs/2026-07-14-galerias-jornadas-voluntarias.md
Deferred: feed Recuerdos recientes, upload voluntarias, badge Nuevas, QA checklist
```
