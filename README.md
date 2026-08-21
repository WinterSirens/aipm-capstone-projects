# AI PM Bootcamp — Capstone Projects Gallery

A static, single-page showcase of capstone prototypes built by students of the
**AI PM Bootcamp** (AI Product Academy, Dr. Marily Nika). The audience is
**future students** — the page lets them see what people actually build in the
bootcamp. Each card links out to a live student prototype in a new tab.

It's shared by link, not found via search (`noindex, nofollow`). It is a
showcase, not a product.

> Preview: serves from `public/` — no build step, no framework, no dependencies.

---

## Project structure

```
public/
  index.html      # entire app: markup + inlined vanilla JS renderer
  styles.css      # all styling (brand tokens + components)
  projects.json   # the content — edit this to add projects
  access.json     # the shared access code — edit this to change/revoke it
  assets/         # logo.png, cover-hero.png
DEPLOY.md         # deploy instructions (Cloudflare Pages, Netlify Drop)
AGENTS.md         # notes for AI coding agents working on this repo
```

Only `public/` gets deployed. Everything else is project management docs.

---

## Run it locally

`fetch('projects.json')` needs HTTP, so opening `index.html` straight from the
filesystem shows an empty grid. Serve it instead:

```bash
python3 -m http.server -d public 8000
```

Then open <http://localhost:8000>. You'll be asked for an access code (see below).

To stop the server: focus that terminal window and press `Control + C`.

---

## Adding a project

Edit `public/projects.json`. Nothing in `index.html` needs to change.

It's a single flat `projects` array — no grouping, no cohorts. Append and the
card appears:

```json
{
  "id": "kebab-case-slug",
  "title": "Project Name",
  "prototypeUrl": "https://..."
}
```

**Required:** `id`, `title`, `prototypeUrl`.
**Optional (rendered conditionally):**
- `builder` — adds a "By <name>" line and an avatar circle.
- `description` — a paragraph under the title.
- `thumbnailUrl` — overrides the auto screenshot.
- `accent` — `red` / `orange` / `magenta` / `indigo`; otherwise assigned
  round-robin by grid position.

Cards render in array order and paginate at 18 per page. The numbered pager
appears once there are more than 18 projects.

### About the prototype URLs

These are student projects on free tiers (Render, Vercel, Netlify, GitHub
Pages, base44, Lovable) — they sleep, go stale, and occasionally die:
- A `200` response proves little; SPA shells and host error pages return `200`
  with a broken app. Opening the page is the only real check.
- Render free-tier apps cold-start slowly (several seconds on first hit).
  Slow isn't broken.
- If a prototype is genuinely dead, leave it out and note which one and why — a
  broken card undercuts the point of the page.

### Thumbnails

Cards without `thumbnailUrl` fall back to WordPress mShots:

```
https://s.wordpress.com/mshots/v1/<encoded url>?w=640&h=360
```

mShots serves a small placeholder while it captures the shot, so the renderer
retries once after 3s if the image looks like a placeholder, and drops the
`<img>` on error so the card falls back to its glyph. A brand-new URL is often
a placeholder on first view and correct on reload — expected, not a bug.

---

## Access gate (shared code)

The gallery is behind a soft access gate. On first visit, the user enters a
code; on success the code is cached in `localStorage` so they don't re-enter it
on that browser/device (it survives closing tabs and restarting the browser).
They only get re-prompted if they clear site data, use a different
device/browser, or you rotate the code.

**To change or revoke the code:** edit one string in `public/access.json`:

```json
{ "code": "APA-BUILDFUTURE" }
```

On every load, a returning visitor's cached code is re-validated against the
current `access.json` (fetched with `cache: no-store`). So changing the code
revokes **all** stale sessions on their next visit — that's the "reset
everyone" lever.

> ⚠️ **This is a soft gate, not real security.** This is a fully static site
> with no backend, so the code ships to the client and is readable by anyone
> who opens DevTools. That's acceptable here because the page is `noindex` and
> shared by link only — the goal is to keep casual viewers out, not to protect
> secrets. If genuine secrecy is required, it needs a backend (e.g. a
> Cloudflare Worker validating against a secret list).

---

## Deploy

See [`DEPLOY.md`](DEPLOY.md) for full instructions. The short version:

```bash
npx wrangler pages deploy public --project-name=aipm-capstone-projects
```

Or drag the `public/` folder onto <https://app.netlify.com/drop> for a quick
one-off. For auto-deploy-on-push, connect the GitHub repo to Cloudflare Pages
with build output directory set to `public` (no build command).

---

## Conventions

- Vanilla ES5-style JS in an IIFE, `'use strict'`, `var` throughout.
- All interpolated content is escaped via `esc()` — never build HTML with raw
  project fields.
- Colors come from CSS custom properties (`--apa-red`, `--apa-orange`,
  `--apa-magenta`, `--apa-indigo-800`), not hex in JS.
- Motion is gated on `prefers-reduced-motion`.
- No build step, no framework, no new dependencies. Keep it static.

---

## Credits

Built in the AI PM Bootcamp. Dr. Marily Nika · [marily.substack.com](https://marily.substack.com)