# AGENTS.md

Read this at the start of every session. It is the project memory file.
For the full original spec see `CLAUDE.md`; this file mirrors and extends it.

## What this is

A static, single-page gallery of capstone prototypes built by students of the
AI PM Bootcamp (AI Product Academy, Dr. Marily Nika).

Audience is **future students**: the page shows them what people actually build
in the bootcamp. Each card links out to a live student prototype in a new tab.
That purpose drives most judgment calls — getting real student work in front of
people matters more than ceremony.

Shared by link, not by search (`noindex, nofollow`). It is **not** a real
product; it is a showcase.

## Layout

```
public/
  index.html      # entire app: markup + inlined vanilla JS renderer
  styles.css      # all styling
  projects.json   # the content — edit this to add projects
  assets/         # logo.png, cover-hero.png
DEPLOY.md         # deploy instructions (Cloudflare Pages, or Netlify Drop)
FABLE5_SPEC.md    # original design/build spec
STUDENT_INTAKE_WORKFLOW.md
preview-*.png     # committed screenshots of the rendered page
```

No build step, no framework, no dependencies. `public/` is served as-is and is
the only thing that gets deployed.

## The common task: adding a project

Edit `public/projects.json`. Nothing in `index.html` needs to change.

Single flat `projects` array — no grouping, no cohorts. Append and the card
appears.

```json
{
  "id": "kebab-case-slug",
  "title": "Project Name",
  "prototypeUrl": "https://..."
}
```

Required: `id`, `title`, `prototypeUrl`. Optional (rendered conditionally):
- `builder` — adds a "By <name>" line and an avatar circle.
- `description` — a paragraph under the title.
- `thumbnailUrl` — overrides the auto screenshot.
- `accent` — `red` / `orange` / `magenta` / `indigo`; otherwise assigned
  round-robin by grid position.

Cards render in array order and paginate at 18 per page. The numbered pager
appears once there are more than 18 projects and stays hidden below that.

## Prototype URLs — reality checks

These are student projects on free tiers (Render, Vercel, Netlify, GitHub
Pages, base44, Lovable). They sleep, go stale, and occasionally die:

- A `200` proves little — SPA shells and host error pages return `200` with an
  empty/broken app. Opening the page is the only real check.
- Render free-tier apps cold-start slowly (several seconds on first hit). Slow
  isn't broken.
- If a prototype is genuinely dead, leave it out and say which one and why — a
  broken card undercuts the point of the page.

## Thumbnails

Cards without `thumbnailUrl` fall back to WordPress mShots:

```
https://s.wordpress.com/mshots/v1/<encoded url>?w=640&h=360
```

mShots serves a small placeholder while it captures the shot, so `wireThumb()`
retries once after 3s when `naturalWidth < 200`, and drops the `<img>` on error
so the card falls back to its placeholder glyph. A brand-new URL is often a
placeholder on first view and correct on reload — expected, not a bug.

## Conventions (match these)

- Vanilla ES5-style JS in an IIFE, `'use strict'`, `var` throughout.
- Interpolated content goes through `esc()` — never build card HTML with raw
  project fields.
- Colors come from CSS custom properties (`--apa-red`, `--apa-orange`,
  `--apa-magenta`, `--apa-indigo-800`), not hex in JS.
- Motion is gated on `prefers-reduced-motion`; preserve that in new animation.
- No build step, no framework, no new dependencies. Keep it static.

## Security posture (important for any "auth"-style feature)

This is a fully static site with no backend. Any client-side check (codes,
passwords, gates) runs in the browser and is therefore **inspectable** — codes
ship to the client. That is acceptable here because the page is `noindex` and
link-only: the goal is a soft gate to keep casual viewers out, not real
security. If genuine secrecy is required, it needs a backend (e.g. a Cloudflare
Worker validating against a secret list) — flag that tradeoff rather than
pretending a client-side check is secure.

## Verifying a change

`fetch('projects.json')` needs HTTP, so opening `index.html` from the
filesystem shows an empty grid. Serve it:

```
python3 -m http.server -d public 8000
```

"Couldn't load the projects" in the grid means `projects.json` is invalid JSON.

## Decision log

Keep a short, dated record of non-obvious decisions here so future sessions
have context.

- 2025-01 — Access gate shipped. Single shared code stored in
  `public/access.json` (`{ "code": "..." }`). Page shows a gate screen on
  load; on valid entry the code is cached in `localStorage` (key
  `apa_access_code`) and the gallery is revealed. On every load the cached
  code is re-validated against the current `access.json` (fetched with
  `cache: no-store`), so changing the code revokes all stale sessions on
  their next visit. Soft gate only — the code ships to the client and is
  inspectable; acceptable because the page is `noindex`/link-only. To rotate:
  edit `access.json`. Initial code: `APA-BUILDFUTURE`.