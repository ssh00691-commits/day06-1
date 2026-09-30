# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Korean class guestbook web app (방명록): classmates leave a name and a one-line greeting. The whole frontend is a single static file, `index.html` (HTML + CSS + JS inline). There is no build step, package manager, linter, or test suite. `PRD.md` holds the requirements and `STEPS.md` the staged checklist (step 1: post/list, step 2: mobile design; both done). Docs and UI text are in Korean.

## Running

Serve the folder over localhost (opening the file directly is not the intended check):

```bash
python -m http.server 8000
```

`.claude/launch.json` defines this as the `guestbook` preview config (port 8000) for the in-app browser.

## Architecture

- `index.html` uses supabase-js v2 from the jsDelivr CDN and talks directly to Supabase from the browser with the publishable key (safe to expose; access is limited by RLS). There is no backend.
- Supabase project is named **`day06`** (id `bpzwirparqtrcvsxvmee`, ap-northeast-2), not "Practice" as the PRD says. The user confirmed using it.
- Table `public.guestbook`: `id` (identity PK), `name`, `message` (both `not null` with a non-blank check), `created_at` (default `now()`). RLS is on; role `anon` has only `select` and `insert` (both the grants and the policies are needed — the policies alone gave a 42501 permission error). No update/delete for anon, so test rows must be removed via the Supabase dashboard/SQL.
- Schema changes are applied as Supabase migrations (`create_guestbook`, `grant_guestbook_anon`), not kept as SQL files in the repo.

## Requirements to preserve

- Render user input with `textContent` (never `innerHTML`); a message like `<b>x</b>` must display literally.
- Trim name and message and refuse to save when either is empty; show a status message instead.
- List is ordered by `created_at` descending and refreshes after a successful post.
- Layout is designed for a ~375px phone width, no horizontal scroll, content column capped at 480px and centered.
- Visual theme follows `pheladii-dad-9513646_1920.jpg` (near-black night sky, white line art); the page loads that image as its header via a relative path, so keep it next to `index.html`.
