## What this is

A static, single-page gallery of capstone prototypes built by students of the
AI PM Bootcamp (AI Product Academy, Dr. Marily Nika).

The audience is **future students**: the page exists to show them the vision of
what people actually build in the bootcamp. Each card links out to a live
student prototype, opened in a new tab. That purpose drives most judgment calls
here — this is a showcase, and getting real student work in front of people
matters more than ceremony.

It's shared by link, not found via search (`noindex, nofollow`).

## Layout

```
public/
  index.html      # entire app: markup + inlined vanilla JS renderer
  styles.css      # all styling
  projects.json   # the content — this is what you edit to add projects
  assets/         # logo.png, cover-hero.png
DEPLOY.md         # deploy instructions (Cloudflare Pages, or Netlify Drop)
FABLE5_SPEC.md    # original design/build spec
STUDENT_INTAKE_WORKFLOW.md
preview-*.png     # committed screenshots of the rendered page
```

No build step, no framework, no dependencies. `public/` is served as-is, and
it's the only thing that gets deployed.

## Adding projects — the common task

Edit `public/projects.json`. Nothing in `index.html` needs to change.

The file is a single flat `projects` array — no grouping, no cohorts. Append to
it and the card shows up.

```json
{
  "id": "kebab-case-slug",
  "title": "Project Name",
  "prototypeUrl": "https://..."
}
```

Those three fields are required. Optional and rendered conditionally:
`builder` (adds a "By <name>" line and an avatar circle), `description` (a
paragraph under the title), `thumbnailUrl` (overrides the auto screenshot),
`accent` (`red` / `orange` / `magenta` / `indigo`, otherwise assigned
round-robin by grid position).

Cards render in array order and paginate at 18 per page; the numbered pager
appears on its own once there are more than 18 projects and stays hidden below
that.

## About the prototype URLs

These are student projects on free tiers — Render, Vercel, Netlify, GitHub
Pages, base44, Lovable — so they sleep, go stale, and occasionally die. Useful
context when a link looks off:

- A `200` doesn't prove much. SPA shells and host error pages return `200` with
  an empty or broken app, so opening the page is the only real check.
- Render free-tier apps cold-start slowly, several seconds on first hit. Slow
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

## Conventions

- Vanilla ES5-style JS in an IIFE, `'use strict'`, `var` throughout. Match it.
- Interpolated content goes through `esc()` — never build card HTML with raw
  project fields.
- Colors come from CSS custom properties (`--apa-red`, `--apa-orange`,
  `--apa-magenta`, `--apa-indigo-800`), not hex in JS.
- Motion is gated on `prefers-reduced-motion`; preserve that in new animation.

## Verifying a change

`fetch('projects.json')` needs HTTP, so opening `index.html` from the
filesystem shows an empty grid. Serve it:

```
python3 -m http.server -d public 8000
```

"Couldn't load the projects" in the grid means `projects.json` is invalid JSON.
