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
The "IA" is **real, not decorative**. Provider is **Google Gemini (Google AI Studio)** — chosen with the user because its keys are free with no credit card and callable from the browser without a special CORS header (artisans generate their own at aistudio.google.com/apikey; the settings modal links there). The `AI` object POSTs to `https://generativelanguage.googleapis.com/v1beta/models/${GEMINI_MODEL}:generateContent?key=…`. `GEMINI_MODEL` is a constant (`gemini-2.0-flash`) — adjustable if a key needs another model. Body uses `system_instruction` + `contents[].parts`; vision adds an `inline_data` part; structured output (Gestión QC) uses `generationConfig.responseMimeType: "application/json"` + the defensive `parseJSON` helper (no responseSchema, to avoid Gemini schema-enum pitfalls). `AI.available()` gates on key **and** `navigator.onLine`; on no-key/offline/error it returns `null` and the caller **falls back to a local template / manual path**. Keys are per-app in `localStorage`: `va_key`, `gti_key`. When adding an AI feature, write the offline fallback first and tag results with `srcTag(ai)`. (History note: a prior version used the Claude API client-side; the verification harness and QR work predate the Gemini switch but are provider-independent.)

### `vitrina_andina_ia.html` — "Darse a conocer"
5-step wizard; state in the in-memory `D` object (no persistence). `goStep(n)` drives nav. Steps: profile → bilingual catalog (`generateCatalog`, hybrid AI → `tplDescES/EN` fallback, ES/EN via `switchLang`) → commercial intelligence (`renderMarkets`: a real-data bar chart of `EXPORT_DATA` = ADEX Jan–May 2025 export figures from the research doc, **not invented reference prices** — click a bar to reveal its note + source link; sources cited inline) → social posts (`genCaption(type)` → renders a styled post-preview card + share buttons: `shareWhatsApp` opens `wa.me/?text=`, `copyAndOpenIG`; `D.lastPost` holds caption+hashtags) → **Pasaporte Digital** (`generatePassport`): traceability card + scannable offline QR; `composePassportImage` draws the whole card to a canvas, and `sharePassport` uses the Web Share API (`navigator.share({files})`) to attach the PNG to WhatsApp on mobile, falling back to download + `wa.me` text on desktop.

### `gestion_textil_ia.html` — "Optimizar procesos"
5 tabs switched via `showPanel(name)`. Persists to `localStorage` under `gti_` keys (`gti_orders`, `gti_oid`, `gti_qa` = saved quality scores). Tabs: **inicio dashboard** (`renderDashboard`: pending/por-cobrar/vendido stat cards + a pedidos-por-estado bar chart + calidad-promedio from `gti_qa`; refreshed by `saveOrders` and `saveQA`) → **quality control by AI vision** (`onPhoto` → camera via `<input capture>`, `fileToResized`, `AI.vision` returns parsed JSON → `renderQAResult`; offline fallback = 15-point manual checklist; "Guardar en historial" → `saveQA`) → orders/clients (`renderOrders`) → cost/price (`calcCost`, S/+USD+`marketPositioning`) → packaging (`selectPack`/`PACKS`). When changing persisted shapes, guard reads of `gti_*`.

### Cross-cutting UI (both apps)
A shared **help modal** (`openHelp`, "❓ Ayuda" header button): how to get a free Gemini key, glossary, FAQs — written for low-literacy artisans. Vitrina also has a **progress tracker** (`progress` flags + `markDone(key)` + `renderProgress`): a bar + completion-based step checkmarks driven as the artisan finishes each step (`perfil`/`catalogo`/`mercados`/`redes`/`pasaporte`). Both apps close modals via a generic backdrop-click handler over `.modal`.

### The QR encoder (verified — do not "tidy" casually)
Both `vitrina` and `index` embed a hand-written byte-mode QR encoder (`QR.encode`, versions 1–10, EC level L) — no external library, for offline use. It was **verified by decoding its output with jsQR** (94/94 cases incl. accents/UTF-8). Two real bugs were fixed during that process: format-info bits were row/col transposed, and alignment patterns on the timing row/col (v7+) were wrongly skipped. If you touch it, re-run the verification: `qrtest.js` / `decode_test.js` in the session scratchpad render the matrix to a pixel buffer and decode with `jsqr` — never trust structural checks (finders/dark-ratio) alone; they passed even when the QR was unscannable.

## Conventions

- All user-facing copy is Spanish; keep tone accessible (audience is not technical). Bilingual ES/EN only where buyer-facing.
- Reference data (export prices, target markets) should trace back to `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md`; that file cites its sources. Don't invent market figures.
- Shared visual identity across all three files (Andean roots + vanguard): deep-indigo dark theme, natural-fiber palette (`--vicuna`/`--terra`/`--cochi`/`--jade`/`--gold`), chakana SVG logo, diamond textile band. Keep new UI consistent with these CSS variables.
- Function naming is plain camelCase verbs tied to UI actions; match that style.
