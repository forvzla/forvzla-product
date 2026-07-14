# Manual QA handoff — ARTIFACT-FOR-001 · Carnet de Voluntaria

**Epic:** ARTIFACT-FOR-001  
**PR:** https://github.com/anyelisataveras/ForVzla/pull/1 (merged → `master`)  
**Release note:** [2026-07-13-ARTIFACT-FOR-001.md](./2026-07-13-ARTIFACT-FOR-001.md)  
**Fecha handoff QA:** 2026-07-13

---

## Objetivo

Validar **QA manual** y **prueba con usuaria real** del flujo de carnet para Cuidadoras Caracas: registro, login, generación async, descarga PDF, fondo custom de coordinadora y contenido del PDF.

---

## Entorno y dispositivos

| Item | Detalle |
|------|---------|
| **Producción** | URL Vercel del proyecto ForVzla |
| **Local** | `npm run dev` → `http://localhost:3000/cuidadoras-caracas/` |
| **Dispositivos** | Mínimo **1 móvil** (iOS/Android) + **1 desktop** |
| **Rutas clave** | `/cuidadoras-caracas/`, `/entrar`, `/registro`, `/mi-cuenta`, `/carnet`, `/coord` |

**Credenciales:** voluntaria de prueba con foto y/o registro nuevo; cuenta de coordinadora con acceso a `coord`.

---

## Pre-requisitos (bloqueantes)

Marcar antes de empezar casos funcionales:

- [ ] Migraciones aplicadas en Supabase (`20250712200000` … `20250713100000`)
- [ ] Edge Function `render-carnet` desplegada en el proyecto Supabase
- [ ] Coordinadora subió PNG **vacío** en **Coord → pestaña 🪪 Carnet**

**Specs del PNG de fondo:**

- Tamaño: **7×5 pulgadas** (~2100×1500 px @ 300 DPI)
- Formato: PNG o JPEG, máx **10 MB**
- **Sin** nombres de ejemplo, watermark ni datos rellenos (plantilla vacía Canva)

---

## A. Coordinadora — plantilla de fondo

| ID | Pasos | Resultado esperado | OK | Notas |
|----|--------|-------------------|-----|-------|
| A1 | Entrar a `/cuidadoras-caracas/coord` → pestaña **Carnet** | Preview del fondo actual (o vacío si no hay) | ☐ | |
| A2 | Subir PNG vacío válido | Toast de éxito; preview actualizado | ☐ | |
| A3 | Subir archivo >10 MB o formato inválido | Error claro; página estable | ☐ | |
| A4 | Reemplazar fondo por otro PNG; voluntaria regenera carnet | PDF usa el fondo nuevo | ☐ | |

---

## B. Registro — foto opcional + CTA carnet

| ID | Pasos | Resultado esperado | OK | Notas |
|----|--------|-------------------|-----|-------|
| B1 | Registro **con** foto en paso 1 | Registro OK; CTA verde **Generar carnet** en éxito | ☐ | |
| B2 | Registro **sin** foto | Registro OK; CTA carnet; al generar pide subir foto | ☐ | |
| B3 | Registro con foto pero falla upload | Mensaje “registro guardado…”; puede continuar | ☐ | |
| B4 | Tras registro, **Entrar a mi cuenta** | Llega a mi-cuenta sin error | ☐ | |

---

## C. Login voluntaria — rutas (regresión 404)

| ID | Pasos | Resultado esperado | OK | Notas |
|----|--------|-------------------|-----|-------|
| C1 | Coord → **Salir** → inicio → **Ya soy voluntaria — entrar** | **No 404**; abre `/cuidadoras-caracas/entrar` | ☐ | |
| C2 | Login: red social + usuario + 4 dígitos cédula | Entra a **Mi cuenta** | ☐ | |
| C3 | Abrir `/cuidadoras-caracas/mi-cuenta` sin sesión | Redirige a entrar; vuelve tras login | ☐ | |
| C4 | Repetir C1–C3 en **móvil** | Mismo comportamiento que desktop | ☐ | |

---

## D. Mi cuenta — jornadas primero + menú carnet

| ID | Pasos | Resultado esperado | OK | Notas |
|----|--------|-------------------|-----|-------|
| D1 | Scroll inicial en mi-cuenta | **Jornadas** y **Mis brigadas** antes que carnet | ☐ | |
| D2 | **Datos de mi cuenta → Mi carnet** | Abre `/cuidadoras-caracas/carnet` | ☐ | |
| D3 | Volver con ← Mi cuenta | Regresa sin perder sesión | ☐ | |
| D4 | Cerrar sesión → entrar de nuevo | Flujo limpio | ☐ | |

---

## E. Generar carnet — primera vez

| ID | Pasos | Resultado esperado | OK | Notas |
|----|--------|-------------------|-----|-------|
| E1 | Sin foto: subir foto (3:4) → Generar | “Procesando…” → “listo” (&lt;90 s; anotar si tarda más) | ☐ | |
| E2 | Con foto guardada: Generar directo | PDF listo | ☐ | |
| E3 | Preview en pantalla | PDF visible; no pantalla en blanco ni error de iframe | ☐ | |
| E4 | **Descargar PDF** | Archivo abre; nombre `carnet-cuidadoras-{número}.pdf` | ☐ | |

---

## F. Contenido del PDF

| ID | Qué revisar | Esperado | OK | Notas |
|----|-------------|----------|-----|-------|
| F1 | Campos | **NOMBRE COMPLETO**, **CÉDULA**, **NÚMERO DE VOLUNTARIA** | ☐ | |
| F2 | Etiquetas | Etiqueta arriba + valor abajo en cada campo | ☐ | |
| F3 | Foto | Foto de la voluntaria en marco izquierdo | ☐ | |
| F4 | Sin fantasmas | No datos de otras personas (plantillas viejas) | ☐ | |
| F5 | Sin iconos duplicados | No círculos/iconos raros sobre el diseño Canva | ☐ | |
| F6 | Alineación | Texto legible y alineado con fondo; si solapa → captura | ☐ | |
| F7 | Impresión | PDF al **100%**; proporción **7×5 in** horizontal; legible al imprimir | ☐ | |

---

## G. Regenerar carnet

| ID | Pasos | Resultado esperado | OK | Notas |
|----|--------|-------------------|-----|-------|
| G1 | Carnet listo → **Regenerar** | Pide confirmación | ☐ | |
| G2 | Subir foto nueva | Obligatoria; no reutiliza foto vieja sola | ☐ | |
| G3 | Tras completar | PDF nuevo reemplaza anterior; datos actualizados | ☐ | |
| G4 | Coord cambia fondo → regenerar | PDF con fondo nuevo | ☐ | |

---

## H. Errores y límites

| ID | Escenario | Esperado | OK | Notas |
|----|-----------|----------|-----|-------|
| H1 | Generar sin foto cuando hace falta | Mensaje claro; no carnet incompleto | ☐ | |
| H2 | Doble tap en Generar | Un solo job; sin duplicados colgados | ☐ | |
| H3 | Grupo sin plantilla (si aplica) | Estado “no disponible”; no fallo silencioso | ☐ | |

---

## I. Prueba con usuaria real (usability)

**Participante:** 1 voluntaria real (ideal: quien pidió el carnet).

### Guion

1. Explicar en 1 frase qué es el carnet.
2. Pedir registro o login **sin ayuda** (intervenir solo si &gt;2 min atascada).
3. Observar: ¿encuentra entrar? ¿sube foto? ¿entiende Generar vs Regenerar?
4. Pedir descargar PDF y opinar si se reconoce y lo imprimiría.

### Preguntas post-prueba

| Pregunta | Respuesta (1–5 o texto) |
|----------|-------------------------|
| ¿Fue fácil sacar el carnet? | |
| ¿El PDF se ve profesional / como Canva? | |
| ¿Algo te asustó o no supiste qué hacer? | |

### Observaciones del moderador

```
Tiempo total:
Puntos de confusión:
Frases de la UI poco claras:
```

---

## Plantilla de reporte de bug

Por cada fallo:

- **ID caso** (ej. F6)
- **URL** exacta
- **Dispositivo** + navegador
- **Rol** (coord / voluntaria Nº …)
- **Pasos** para reproducir
- **Esperado** vs **obtenido**
- **Screenshot** o PDF adjunto
- **Severidad:** bloqueante / importante / menor

---

## Fuera de scope (anotar aparte)

- `listar_jornadas_coord` 404 en coord — no bloquea carnet; afecta listado de jornadas en portal coordinación.

---

## Cierre QA

| Campo | Valor |
|-------|--------|
| **Tester** | |
| **Fecha ejecución** | |
| **Entorno** | prod / local |
| **Resultado global** | pass / pass con issues / fail |
| **Bloqueantes abiertos** | |
| **Link a issues / capturas** | |

---

*Generado para handoff post-merge ARTIFACT-FOR-001. Completar checkboxes y devolver a PM/dev.*
