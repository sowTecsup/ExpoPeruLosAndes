# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A two-app toolkit built for a live session ("Artesanía Inteligente") at **Expo Perú Los Andes**, aimed at ~8,000 Andean textile artisans (alpaca/vicuña/llama fiber) from Huancavelica, Cusco, Apurímac and Junín. The apps are demoed live from a laptop in the expo dome and handed to artisans to keep. Primary user is a Spanish-speaking artisan with low technical literacy; UI text is Spanish (with bilingual ES/EN output where it serves international buyers).

`CONTEXTO_PROYECTO.md` is the source of truth for project intent, the event, and design decisions. `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md` holds the market research and the identified "pain points" that justify each feature and seed future ones — read it before proposing new tools or changing data like prices/markets.

## Architecture — critical constraints

Three self-contained `.html` files, each with inline `<style>` and `<script>` (vanilla JS, no framework, no dependencies, no build step): `index.html` (landing page linking both apps + a self-URL share QR) and the two apps below. This is deliberate, not legacy:

- **Offline-first is a hard requirement** — internet in the dome is unreliable. Every feature must degrade gracefully to a working offline path. Don't add CDN links, external fonts, or build tooling. Open the `.html` directly to run; "test" = open it and click through (plus the Node verification harness below for the QR encoder).
- **Deployment model: static, "each user brings their own key" (decided with the user).** No backend, no proxy. Hosting = GitHub Pages from `main`/root (repo already exists). The Anthropic API key is never shipped — each user pastes their own into the in-app settings; it lives only in their browser's `localStorage`.
- Keep each app one file. Don't split into separate JS/CSS unless explicitly asked.

### Hybrid AI (`AI` helper, present in both apps)
The "IA" is **real, not decorative**: an `AI` object calls the Claude API directly from the browser (`model: claude-opus-4-8`, `output_config.effort: low`, header `anthropic-dangerous-direct-browser-access: true`). `AI.available()` gates every call on having a key **and** `navigator.onLine`; on no-key / offline / any error it returns `null` and the caller **falls back to a local template / manual path**. Keys are per-app in `localStorage`: `va_key` (Vitrina), `gti_key` (Gestión). The header status pill shows IA real / Offline. When adding an AI feature, always write the offline fallback first and tag the result with `srcTag(ai)` (IA real vs plantilla/manual).

### `vitrina_andina_ia.html` — "Darse a conocer"
5-step wizard; state in the in-memory `D` object (no persistence). `goStep(n)` drives nav. Steps: profile → bilingual catalog (`generateCatalog`, hybrid AI → `tplDescES/EN` fallback, ES/EN via `switchLang`) → commercial intelligence (`MARKETS`, hardcoded) → social captions (`genCaption(type)`, hybrid → `tplCaption`) → **Pasaporte Digital** (`generatePassport`): a traceability card + a genuinely scannable offline **QR** encoding a compact text payload.

### `gestion_textil_ia.html` — "Optimizar procesos"
4 modules switched via `showPanel(name)`. Persists to `localStorage` under `gti_` keys (`gti_orders`, `gti_oid`). Modules: **quality control by AI vision** (`onPhoto` → camera capture via `<input capture>`, image resized in `fileToResized`, `AI.vision` with the `QA_SCHEMA` structured-output schema → `renderQAResult`; offline fallback = 15-point manual checklist `showManualChecklist`/`CHECKLIST`) → orders/clients (`saveOrder`/`deleteOrder`/`updateStatus`/`renderOrders`) → cost/price calculator (`calcCost`, S/ + USD + `marketPositioning`) → packaging tiers (`selectPack`/`PACKS`). When changing persisted shapes, guard reads of `gti_*` for returning users.

### The QR encoder (verified — do not "tidy" casually)
Both `vitrina` and `index` embed a hand-written byte-mode QR encoder (`QR.encode`, versions 1–10, EC level L) — no external library, for offline use. It was **verified by decoding its output with jsQR** (94/94 cases incl. accents/UTF-8). Two real bugs were fixed during that process: format-info bits were row/col transposed, and alignment patterns on the timing row/col (v7+) were wrongly skipped. If you touch it, re-run the verification: `qrtest.js` / `decode_test.js` in the session scratchpad render the matrix to a pixel buffer and decode with `jsqr` — never trust structural checks (finders/dark-ratio) alone; they passed even when the QR was unscannable.

## Conventions

- All user-facing copy is Spanish; keep tone accessible (audience is not technical). Bilingual ES/EN only where buyer-facing.
- Reference data (export prices, target markets) should trace back to `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md`; that file cites its sources. Don't invent market figures.
- Shared visual identity across all three files (Andean roots + vanguard): deep-indigo dark theme, natural-fiber palette (`--vicuna`/`--terra`/`--cochi`/`--jade`/`--gold`), chakana SVG logo, diamond textile band. Keep new UI consistent with these CSS variables.
- Function naming is plain camelCase verbs tied to UI actions; match that style.
