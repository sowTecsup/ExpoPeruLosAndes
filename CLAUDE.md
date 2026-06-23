# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A two-app toolkit built for a live session ("Artesanía Inteligente") at **Expo Perú Los Andes**, aimed at ~8,000 Andean textile artisans (alpaca/vicuña/llama fiber) from Huancavelica, Cusco, Apurímac and Junín. The apps are demoed live from a laptop in the expo dome and handed to artisans to keep. Primary user is a Spanish-speaking artisan with low technical literacy; UI text is Spanish (with bilingual ES/EN output where it serves international buyers).

`CONTEXTO_PROYECTO.md` is the source of truth for project intent, the event, and design decisions. `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md` holds the market research and the identified "pain points" that justify each feature and seed future ones — read it before proposing new tools or changing data like prices/markets.

## Architecture — critical constraints

Each app is a **single self-contained `.html` file** with inline `<style>` and `<script>` (vanilla JS, no framework, no dependencies, no build step). This is deliberate, not legacy:

- **Offline-first is a hard requirement** — internet in the dome is unreliable. Never add a CDN link, external font/script, `fetch()` to a live API, or anything requiring network. Everything must work from `file://`.
- **No build tooling.** Do not introduce npm/bundlers/transpilation. To run: open the `.html` file directly in a browser. To "test": open it and click through the flows.
- Keep each app one file. Don't split into separate JS/CSS unless explicitly asked.

### `vitrina_andina_ia.html` — "Darse a conocer" (visibility & commercialization)
4-step linear wizard. State lives in the in-memory `artisanData` object + `currentStep` (no persistence — it's a fresh demo each time). `goStep(n)` drives navigation. Steps: profile → bilingual catalog description (template-based "AI" generation in `generateCatalog`, ES/EN via `switchLang`) → commercial intelligence (6 international markets with hardcoded reference prices) → social captions (`genCaption(type)` for 4 caption types + hashtags).

### `gestion_textil_ia.html` — "Optimizar procesos" (process optimization)
4 modules switched via `showPanel(name)`. **This app persists to `localStorage`** under keys prefixed `gti_` (`gti_orders`, `gti_oid`). Modules: orders/clients (CRUD + kanban status, `saveOrder`/`deleteOrder`/`updateStatus`/`renderOrders`) → quality control (15-point camelid-fiber checklist, scored ring via `updateScore`, `.txt` export in `exportQuality`) → cost/price calculator (`recalculate`, outputs S/ and USD + per-market projection) → packaging tiers (`selectPack`).

When changing persisted data shapes in the gestión app, account for existing `localStorage` data on returning users (migrate or guard reads of `gti_*`).

## Conventions

- All user-facing copy is Spanish; keep tone accessible (the audience is not technical). Bilingual ES/EN only where buyer-facing.
- Reference data (export prices, target markets, certifications) should trace back to `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md`; that file cites its sources. Don't invent market figures.
- Function naming is plain camelCase verbs tied to UI actions (`genCaption`, `selectPack`, `exportQuality`); match that style.
