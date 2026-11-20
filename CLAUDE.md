# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repo shape

This is a monorepo with two apps sharing one Supabase backend (Postgres + Auth + RLS):

- **`apps/mobile/`** — the Flutter/Android app (study guide + quiz games for memorizing product
  codes). See **`apps/mobile/CLAUDE.md`** for its commands and architecture — read that file
  before working on anything under `apps/mobile/`.
- **`apps/web/`** — a Vite/React SPA (admin panel) for browsing the loaded product catalog,
  editing each product's mnemotecnia and bulk-updating classification from Excel. No backend of
  its own — talks to Supabase directly with the anon key; Google login restricted to approved
  admins, with access enforced by RLS. See **`apps/web/CLAUDE.md`** before working on anything
  under `apps/web/`.

`Data/` (root, gitignored) holds Python scripts that load/reconcile the catalog into Supabase from
per-sede Excel files — shared context for both apps since they read the same catalog. See
`Data/reglas_carga.md` before writing a new load/reconciliation script.

`formato-mayoreo/` (root, gitignored — internal brand material) is the Grupo Mayoreo visual and
voice system both apps follow. Read its `SKILL.md` before changing anything with appearance or UI
copy.

`PLAN_PRUEBA_NEON.md` (root) records a Neon evaluation that was **discarded on 2026-08-01** (its
test project was deleted). It is history only — Supabase `yzlhvabujsyuiqgaffrc` is the sole
production database.

Both apps read/write the *same* Supabase project — a schema change (tables, RLS) made for one app
affects the other. `apps/mobile/CLAUDE.md`'s "Database schema notes" section documents non-obvious
schema quirks (e.g. Supabase truncating unpaginated reads at 1000 rows) that apply equally to
`apps/web/` and to `Data/` scripts.

## Working in this repo

There is no root-level build/test — each app is self-contained under `apps/<name>/` with its own
commands (see that app's CLAUDE.md/README). `cd` into the relevant app directory before running
its tooling (e.g. `flutter` commands must run from `apps/mobile/`, `npm` commands from
`apps/web/`).

This project has a read-only `supabase` MCP server configured in `.mcp.json`. Because it is
read-only, schema/RLS changes (e.g. `apps/web/supabase/*.sql`) can't be
applied from a session — the user runs them in the Supabase SQL Editor.

**The GitHub repo (`mvasquez-cyber/SKUs_App`) is public, and a push to `main` deploys `apps/web`
to production on Vercel.** The repo root routinely holds untracked internal files (Excel exports,
`.rar` archives, `Backups/` SQL dumps, `MIGRACION_CUENTAS.md`) that must never be committed —
stage files by explicit path, never `git add -A` / `git add .`. The mobile app also reads its
in-app updates from this repo's GitHub releases (see `apps/mobile/CLAUDE.md`).
