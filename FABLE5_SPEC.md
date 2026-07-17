# Fable 5 Build Spec — AI Product Academy · Capstone Projects Gallery

> Feed this entire document into Fable 5 as the build brief. It is self‑contained: it references the **AI Product Academy Design System** for all visual decisions and gives exact token values so Fable does not have to re‑derive them.

---

## 1. Project goal

A public, shareable website that lets visitors **browse capstone projects built by students** of Dr. Marily Nika's **AI Product Academy** bootcamp. Visitors scroll a gallery grouped by **cohort**, see a **thumbnail of each project's homepage**, read the project name + builder, and **click through to play with the live prototype** in a new tab.

This is our version of the reference site `cohort‑projects.pages.dev` (Product Faculty), rebranded with the AI Product Academy identity and simplified to a direct **card → prototype** flow (no separate detail page in v1).

### Primary jobs‑to‑be‑done
- **Visitor:** "Show me what past cohorts built, and let me click into a prototype to play with it."
- **Student:** "Find my project on the site and share the link with recruiters / peers."
- **Mitchell (operator):** "Add a new cohort's projects by editing one data file, then redeploy — no code changes."

### Out of scope (v1)
- Per‑project detail pages, video demos, PRD/workflow viewers (the reference has these; we intentionally skip them for v1 — cards link straight to the prototype).
- Search, tagging, sorting, accounts, auth, backend, CMS.
- A submission portal (intake is a manual data‑file flow — see `STUDENT_INTAKE_WORKFLOW.md`).

---

## 1a. Reference site (visit this first)

**Live reference (study the structure, then rebrand):** https://cohort-projects.pages.dev/

Fable should crawl/visit this URL to internalize the layout, pacing, and interaction model before building. It is a Cloudflare Pages site built with Astro, powered by a spreadsheet. Use it as the **structural template**, not the visual template — we replace its "Product Faculty" navy/yellow theme entirely with the AI Product Academy brand (§2).

**What to take from the reference (keep):**
- The single‑page flow: sticky topbar → hero (eyebrow pill + big title + subtitle) → cohort tab pills with count badges → responsive card grid → footer.
- The card anatomy: a 16:9 media/thumbnail area on top, then a header row (avatar initial + title + "By builder"), a 2–3 line clamped description, and a pill CTA.
- The responsive grid: **1 column < 640px, 2 columns ≥ 640px, 3 columns ≥ 1024px** (gaps 22 / 24 / 28px).
- Cohort filtering via tabs (the reference has "Cohort 9" / "Cohort 8" pills with project counts).
- The overall feel: a browsable portfolio gallery where each card opens the student's work.

**What to change (rebrand to AI Product Academy):**
- Drop the reference's dark‑navy `#0E1525` / yellow `#FFCC00` / ivory / Outfit‑font theme entirely. Use the tokens in §4 (indigo `#13106B`, magenta `#D60FE4`, warm gradient, Poppins, pill shapes, indigo‑tinted shadows).
- The reference's card links to a **per‑project detail page** (`/projects/{slug}`) with a video player + PRD/Workflow/Documentation actions. **Skip all of that in v1** — our card links **directly** to the student's live `prototypeUrl` in a new tab. No detail page, no video, no PRD viewer.
- The reference loads its data from a spreadsheet via SheetJS. Ours loads from a static **`/projects.json`** at runtime (§6) — simpler and manual.
- The reference's thumbnails are Google Drive images or video posters. Ours auto‑generate from the prototype URL via mShots (§8).

**Example reference URLs to inspect:**
- Home/gallery: `https://cohort-projects.pages.dev/`
- A detail page (to see what we are *not* building in v1, but could add later): `https://cohort-projects.pages.dev/projects/ai-assisted-tournament-scheduler`

> Note: the reference sets `<meta name="robots" content="noindex, nofollow">`. Do the same on our site so student prototypes aren't indexed without consent.

---

## 2. Brand source of truth

**Design system folder (provide to Fable as the brand kit):**
`/Users/mitchelldyer/Downloads/AI Product Academy Design System`

Key files Fable should ingest:
- `styles.css` — single entry point (imports tokens)
- `tokens/colors.css`, `tokens/typography.css`, `tokens/spacing.css`, `tokens/fonts.css` — CSS custom properties
- `assets/logo.png` — the wordmark oval (transparent corners)
- `assets/cover-hero.png` — brand hero image
- `components/core/*.jsx` — React primitives (Button, Card, Badge, Eyebrow, Input) showing exact styling behavior
- `slides/*.card.html` — standalone visual specimens (title, section divider, compare, roadmap, quote, closing)

**If Fable cannot ingest a folder**, inline the tokens below (Section 4) and copy `assets/logo.png` into the project's image folder.

### Brand essence (do not deviate)
- **Vibe:** electric + optimistic. Warm orange→yellow gradient (energy) colliding with deep indigo + neon magenta (AI/future). High contrast, bold, confident. Never pastel, never corporate‑blue.
- **Signature shape:** the **pill** (`border-radius: 999px`) for buttons, chips, the logo oval.
- **Shadows:** always **indigo‑tinted**, never neutral gray.
- **No emoji.** Use the **→** arrow to show flow/outcome.
- **Motion:** quick & confident, 120–360ms, `cubic-bezier(0.22,1,0.36,1)`. Cards lift 3px on hover. Buttons scale to 0.97 on press. No bounces, no infinite loops on content.

### Voice & copy (from the brand)
Practitioner‑mentor: direct, encouraging, action‑first. Addresses the learner as **"you."** Big moments use **ALL CAPS**; key abstractions get scare‑quotes (the **'What'**, the **'How'**). Phases are numbered (*Phase 01 / Phase 02*). Tools named explicitly (v0, Claude Code, Lovable, Vercel…). Concrete over abstract.

Echo examples:
> "See capstone projects built by students."
> "Pick one to play with the prototype."
> "Built in the AI PM Bootcamp → shipped to real users."

---

## 3. Information architecture (single page)

One page, top to bottom. A sticky top bar + five regions:

```
┌─────────────────────────────────────────────────────────┐
│ TOPBAR  (glassy indigo, sticky)   · AI Product Academy ·  │
├─────────────────────────────────────────────────────────┤
│ HERO    (indigo gradient + magenta/warm radial glows)     │
│   eyebrow pill · big title · subtitle · logo watermark    │
├─────────────────────────────────────────────────────────┤
│ COHORT TABS  (pill buttons, active = indigo, count badge) │
├─────────────────────────────────────────────────────────┤
│ GALLERY GRID  (light background, responsive 1 / 2 / 3 col)│
│   project cards → click opens prototype in new tab        │
├─────────────────────────────────────────────────────────┤
│ FOOTER  (indigo field + radial glow + logo)               │
└─────────────────────────────────────────────────────────┘
```

---

## 4. Exact design tokens (author against these)

### Color
```
/* Brand core */
--apa-indigo-900:#0B0D62;  --apa-indigo-800:#13106B;  --apa-indigo-700:#1E1488;
--apa-indigo-600:#2B1278;  --apa-indigo-500:#4733B0;
--apa-magenta:#D60FE4;     --apa-magenta-bright:#E83FF0;  --apa-pink:#E0309E;
--apa-orange:#FF871C;      --apa-yellow:#F8F11D;        --apa-red:#E30632;

/* Neutrals */
--apa-ink:#14132B;  --apa-ink-soft:#44425C;  --apa-ink-faint:#7B7990;
--apa-line:#E3E1EC; --apa-cloud:#F4F3F9;     --apa-paper:#FFFFFF;

/* Signature gradients */
--apa-grad-warm: linear-gradient(125deg,#FF871C 0%,#F8F11D 100%);
--apa-grad-warm-deep: linear-gradient(125deg,#E30632 0%,#FF871C 55%,#F8F11D 100%);
--apa-grad-neon: linear-gradient(120deg,#D60FE4 0%,#6B2BE0 100%);
--apa-grad-indigo: linear-gradient(160deg,#1E1488 0%,#0B0D62 100%);

/* Semantic aliases */
--text-strong: var(--apa-ink);  --text-body: var(--apa-ink-soft);  --text-muted: var(--apa-ink-faint);
--text-on-dark: #fff;  --text-on-dark-soft: rgba(255,255,255,.72);  --text-accent: var(--apa-magenta);
--surface-page: #fff;  --surface-tint: var(--apa-cloud);  --surface-card: #fff;
--surface-dark: var(--apa-indigo-800);  --surface-dark-raised: var(--apa-indigo-700);
--border-default: var(--apa-line);  --border-on-dark: rgba(255,255,255,.16);
--accent-primary: var(--apa-indigo-800);  --accent-secondary: var(--apa-magenta);  --accent-warm: var(--apa-orange);
--focus-ring: var(--apa-magenta);
```

### Type (load via Google Fonts)
```
--font-display:'Poppins',system-ui,sans-serif;   /* headlines + UI */
--font-body:'Poppins',system-ui,sans-serif;
--font-serif:'Arsenal',Georgia,serif;            /* editorial accents only */
--font-editorial:'Radley',Georgia,serif;          /* pull-quotes only */
--font-script:'Allura',cursive;                   /* decorative flourish only */

Weights: 300/400/500/600/700.  Display = 700, tracking -0.02em.
Scale: 12 / 13 / 15 / 16 / 18 / 22 / 28 / 36 / 48 / 64 / 88px.
Eyebrow/labels: uppercase, 13px, weight 600, letter-spacing 0.22em.
Google Fonts import:
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,300;0,400;0,500;0,600;0,700;1,400&family=Arsenal:ital,wght@0,400;0,700;1,400&family=Radley:ital@0;1&family=Allura&display=swap');
```

### Spacing / radius / shadow / motion
```
--space-1..9: 4,8,12,16,24,32,48,64,96px   (4px base grid)
--radius-md:16px (cards) · --radius-lg:24px (panels) · --radius-pill:999px
--shadow-sm:0 2px 8px rgba(19,16,107,.10)   · --shadow-md:0 8px 24px rgba(19,16,107,.12)
--shadow-lg:0 18px 48px rgba(19,16,107,.16) · --shadow-glow-magenta:0 0 24px rgba(214,15,228,.45)
--shadow-glow-warm:0 10px 32px rgba(255,135,28,.35)
--ease-out:cubic-bezier(0.22,1,0.36,1) · --dur-fast:120ms --dur-base:200ms --dur-slow:360ms
--container-lg:1200px (max content width)
```

---

## 5. Region‑by‑region spec

### 5.1 Topbar (sticky, glassy)
- `position: sticky; top: 0; z-index: 50;` height ~64px.
- Background: `rgba(11,13,98,.72)` + `backdrop-filter: blur(8px)`; bottom hairline `var(--border-on-dark)`.
- Centered brand: the `logo.png` wordmark at ~36px tall, OR spaced‑caps text "AI PRODUCT ACADEMY" in Poppins 600, letter‑spacing 0.22em, white. (Prefer the logo image.)
- No nav links in v1 (single page).

### 5.2 Hero (indigo field — mirrors the "section divider" slide)
- Full‑width band, `background: var(--apa-grad-indigo)`, min‑height ~420px, centered content, `padding: var(--space-8) var(--space-6)`.
- **Decorative glows** (absolutely positioned, pointer‑events none):
  - top‑right: `radial-gradient(circle, rgba(214,15,228,.42), transparent 65%)`, ~520px circle, bleeding off the top‑right edge.
  - bottom‑left: `radial-gradient(circle, rgba(255,135,28,.28), transparent 65%)`, ~420px circle, bleeding off the bottom‑left edge.
- **Eyebrow pill** (centered, above title): `display:inline-flex; gap:8px; padding:10px 20px; border-radius:var(--radius-pill); background:rgba(255,255,255,.08); border:1px solid var(--border-on-dark);` Poppins 500, 14px, letter‑spacing 0.22em, uppercase, color `var(--text-on-dark-soft)`. Text: **"AI PM BOOTCAMP · CAPSTONE PROJECTS"**.
- **Title** (h1): Poppins 700, `clamp(2.4rem, 5.4vw, 4.2rem)`, line‑height 1.04, letter‑spacing ‑0.02em, white, max‑width ~860px, centered. Render:
  > **See what our students built.**
  Put a trailing accent: a magenta period or a `<span>` "built." with `color: var(--apa-yellow)` — pick one and keep it consistent.
- **Subtitle**: Poppins 400, `clamp(1rem,1.6vw,1.2rem)`, color `var(--text-on-dark-soft)`, max‑width 580px, centered. Render:
  > Pick a cohort, find a project, and click through to play with the prototype.
- **Logo watermark**: `logo.png` at ~150px wide, absolutely positioned bottom‑right of the hero, `opacity:.92`, `filter: drop-shadow(0 24px 60px rgba(0,0,0,.5))`.

### 5.3 Cohort tabs (pill group, centered)
- Row of pill buttons, centered, `gap:10px`, `margin: var(--space-7) auto var(--space-5)`, wrap on small screens.
- **Tab (inactive):** `padding:9px 20px; border-radius:var(--radius-pill); background:transparent; color:var(--apa-ink-faint);` Poppins 600, 14px, letter‑spacing 0.04em, `cursor:pointer`.
- **Tab (active):** `background: var(--apa-indigo-800); color:#fff;` (the brand's workhorse pill) with `box-shadow: var(--shadow-sm)`.
- **Count badge** inside each tab: a small `var(--apa-cloud)` chip with indigo text, e.g. `Cohort 9  · 69`. Render the count in a `<span>` at 12px, weight 600, `background:rgba(19,16,107,.08)`, `padding:2px 8px`, `border-radius:var(--radius-pill)`.
- Behavior: clicking a tab filters the grid to that cohort (see §7). Default = latest cohort active.
- `role="tablist"` / `role="tab"` / `aria-selected`; keyboard arrows move between tabs.

### 5.4 Gallery grid (light, content‑dense)
- `background: var(--surface-page)` (white) with a faint top‑edge tint optional.
- `max-width: var(--container-lg); margin: 0 auto; padding: 0 var(--space-5) var(--space-8);`
- **Grid:** `display:grid; gap:24px;`
  - default (mobile): `grid-template-columns: 1fr;`
  - `@media (min-width:640px): repeat(2, 1fr);` gap 24px
  - `@media (min-width:1024px): repeat(3, 1fr);` gap 28px
- **Section heading** above the grid (optional, only on first cohort): an `Eyebrow` ("THE GALLERY") in magenta + an h2 "Cohort 9 capstones" in Poppins 700, 36px, `var(--text-strong)`. Hide/re‑render when tabs switch.

### 5.5 Project card (the core unit)
A clickable card that opens the prototype URL in a new tab. Maps to the design system's **Card** component (light tone, accent top rule, indigo‑tinted shadow, 3px lift on hover).

Layout (flex column, `min-height` ~360px so rows align):
```
┌───────────────────────────────────┐
│ ▔▔▔ accent top rule (4px) ▔▔▔▔▔▔▔ │  ← rotating brand color per card
│ ┌───────────────────────────────┐ │
│ │   16:9 thumbnail (screenshot)  │ │  ← <img> cover; placeholder while loading
│ └───────────────────────────────┘ │
│  ●  Project Name            (h3)  │  ← avatar initial + title
│     By Builder Name        (muted) │
│     One-line description (2–3 ln)  │
│     Open prototype →   (pill btn)  │
└───────────────────────────────────┘
```

Exact card styling:
- `background: var(--surface-card); border:1px solid var(--border-default); border-radius: var(--radius-lg);` `box-shadow: var(--shadow-sm);` `overflow:hidden; position:relative;`
- `transition: transform var(--dur-base) var(--ease-out), box-shadow var(--dur-base) var(--ease-out);`
- **Hover:** `transform: translateY(-3px); box-shadow: var(--shadow-lg);` (matches the Card component's `interactive` behavior).
- **Accent top rule:** a 4px bar across the top, color **rotates** through the brand palette per card index to give the grid life — `['var(--apa-red)','var(--apa-orange)','var(--apa-magenta)','var(--apa-indigo-800)']` (same order as the roadmap slide). Use `card.dataset.accent` or `:nth-child()` to assign.
- **Whole card is a link:** wrap the card in `<a href="{prototypeUrl}" target="_blank" rel="noopener noreferrer">` OR make the card an `<article>` with a stretched `<a>` (`::after{position:absolute;inset:0}`) so the entire surface is clickable. The visible "Open prototype →" pill is the affordance; the click target is the whole card.
- `text-decoration:none; color:inherit;` on the link wrapper.

Internal regions:
- **Thumbnail:** `aspect-ratio: 16/9;` `background: var(--surface-tint);` `<img loading="lazy" src="{thumbnailUrl}" alt="Screenshot of {projectTitle}" style="width:100%;height:100%;object-fit:cover;">` If no thumbnail, show a centered magenta play/arrow glyph on the cloud tint.
- **Body:** `padding: var(--space-5); display:flex; flex-direction:column; gap:8px; flex-grow:1;`
- **Header row:** `display:flex; gap:12px; align-items:center;`
  - **Avatar:** 44px pill, `background: linear-gradient(135deg, rgba(214,15,228,.18), rgba(255,135,28,.10)); border:1px solid var(--border-default); color: var(--apa-indigo-800); font-weight:700; font-size:16px;` shows the builder's first initial.
  - **Title:** h3, Poppins 700, `var(--fs-lg)` (22px), `var(--text-strong)`, line‑height 1.2, `margin:0`. Clamp to 2 lines.
  - **Builder:** `<p>By {builderName}</p>`, Poppins 400, 13px, `var(--text-muted)`, `margin:0`.
- **Description:** Poppins 400, 14px, line‑height 1.55, `var(--text-body)`, clamp to 3 lines (`-webkit-line-clamp:3`).
- **CTA pill** (affordance, inside the link): `align-self:flex-start; margin-top:auto;` `padding:10px 18px; border-radius:var(--radius-pill);` `background: rgba(19,16,107,.08); color: var(--apa-indigo-800);` Poppins 600, 13px, letter‑spacing 0.02em; content: **"Open prototype →"**. On card hover, the pill brightens to `background: var(--apa-indigo-800); color:#fff;`.

### 5.6 Empty / loading states
- **No projects in a cohort:** a centered card on `--surface-tint`, eyebrow "COMING SOON", h3 "This cohort's projects haven't been added yet", body "Check back after the final presentations."
- **Thumbnail loading:** the mShots service returns a placeholder on the very first request and the real screenshot shortly after. Implement a one‑shot retry: on `<img>`, if `naturalWidth` is tiny/placeholder after load, reload the `src` once after ~3s. (See §8.)

### 5.7 Footer (indigo closing field — mirrors the "closing" slide)
- `background: var(--apa-indigo-900);` full‑width, `padding: var(--space-8) var(--space-6)`, centered column.
- Decorative `radial-gradient(80% 60% at 50% 40%, rgba(214,15,228,.30), transparent 60%)` overlay.
- `logo.png` at ~120px, white drop‑shadow.
- Line: Poppins 600, 18px, white: **"Built in the AI PM Bootcamp."** + sub‑line Poppins 400, 14px, `var(--text-on-dark-soft)`: "Dr. Marily Nika · marily.substack.com".
- A ghost‑on‑dark pill link "AI Product Academy →" pointing to `https://marily.substack.com` (confirm final URL with operator).

---

## 6. Data model — `projects.json` (single source of truth)

The site **must load project data at runtime** from `/projects.json` via `fetch()` (see §7). This is a hard requirement so the operator can add projects by editing one JSON file and redeploying — **without regenerating the UI in Fable**.

### Schema
```json
{
  "cohorts": [
    {
      "id": "cohort-1",
      "name": "Cohort 1",
      "order": 1
    }
  ],
  "projects": [
    {
      "id": "crewos",
      "cohortId": "cohort-1",
      "title": "CrewOS",
      "builder": "Unekwu Ojoshaibu",
      "description": "Agentic podcast production — perceive-decide-act-learn pipeline that auto-generates show notes, socials, and newsletters.",
      "prototypeUrl": "https://crewos.vercel.app",
      "thumbnailUrl": "https://s.wordpress.com/mshots/v1/https%3A%2F%2Fcrewos.vercel.app?w=640&h=360",
      "accent": "magenta"
    }
  ]
}
```

### Field rules
| Field | Required | Notes |
|---|---|---|
| `id` | yes | URL‑safe slug, unique. Used as React key. |
| `cohortId` | yes | Must match a `cohorts[].id`. Determines which tab it appears under. |
| `title` | yes | Display name. |
| `builder` | yes | Full name; first initial drives the avatar. |
| `description` | yes | 1–2 sentences, max ~180 chars. Clamped to 3 lines. |
| `prototypeUrl` | yes | The live prototype. Card opens this in a new tab. Must include `https://`. |
| `thumbnailUrl` | recommended | Screenshot URL. If omitted, derive from `prototypeUrl` via mShots (§8) or show the placeholder glyph. |
| `accent` | optional | One of `red`, `orange`, `magenta`, `indigo`. If omitted, rotate by index. |

A starter file is provided at `./projects.json` with 3 sample projects.

---

## 7. Rendering & interaction behavior

1. On `DOMContentLoaded`, `fetch('/projects.json')` and parse.
2. Build the **cohort tabs** from `data.cohorts` (sorted by `order` desc — newest first). Activate the first (latest) cohort.
3. Render the **grid** with only the active cohort's `projects` (sort within cohort by the order they appear in the JSON, or alphabetically by title — pick one and keep consistent; recommend JSON order so the operator curates sequence).
4. **Tab click** → swap active tab, re‑render the grid with that cohort's projects, update `aria-selected`. Smooth, no page reload.
5. **Card click** → open `prototypeUrl` in a new tab (`target="_blank" rel="noopener noreferrer"`). The whole card is the click target (stretched link).
6. **Reveal animation** (optional, on‑brand): cards fade‑up on first paint with a staggered `animation-delay` of `index * 60ms`, capped at ~480ms. Use `opacity 0→1` + `translateY(12px→0)` over `var(--dur-slow) var(--ease-out)`. Only on the gallery, only once. Respect `prefers-reduced-motion`.
7. **Keyboard:** tabs are arrow‑navigable; cards are focusable links (the stretched `<a>`); visible focus ring = `0 0 0 3px rgba(214,15,228,.30)` (the Input component's focus treatment).

---

## 8. Thumbnail strategy (zero‑config, recommended)

Use **WordPress mShots** — free, no API key, returns a real JPEG screenshot from a URL:
```
https://s.wordpress.com/mshots/v1/{urlEncodedPrototypeUrl}?w=640&h=360
```
Example for `https://crewos.vercel.app`:
```
https://s.wordpress.com/mshots/v1/https%3A%2F%2Fcrewos.vercel.app?w=640&h=360
```
- The operator only stores the `prototypeUrl`; `thumbnailUrl` can be auto‑derived in JS if absent:
  ```js
  const thumb = p.thumbnailUrl || `https://s.wordpress.com/mshots/v1/${encodeURIComponent(p.prototypeUrl)}?w=640&h=360`;
  ```
- **First‑load caveat:** mShots returns a small placeholder GIF while it captures the shot, then caches the real JPEG. Implement a one‑shot retry: after the `<img>` loads, if `img.naturalWidth < 200`, set a 3s timeout to reassign `img.src` (append `&r=1` to bust cache) once.
- **Fallbacks (if mShots is insufficient):** `https://api.microlink.io/?url={encoded}&screenshot=true&embed=screenshot.url` (free tier), or `screenshotone.com` / `urlbox.io` (paid, higher reliability). Or the operator drops manual screenshots into `/screenshots/{id}.jpg` and sets `thumbnailUrl` explicitly.

> mShots was verified working in this environment: a 640×360 JPEG is returned for a given URL.

---

## 9. Responsive behavior (match the reference breakpoints)

| Viewport | Grid | Hero title | Tabs |
|---|---|---|---|
| < 640px | 1 column, gap 22px | clamp lower bound ~2.4rem | wrap, full‑width pills |
| ≥ 640px | 2 columns, gap 24px | — | centered row |
| ≥ 1024px | 3 columns, gap 28px | clamp upper bound ~4.2rem | centered row |

- Topbar collapses brand to the logo mark only on < 640px.
- Card body padding drops to `var(--space-4)` on < 640px.
- Hero padding drops to `var(--space-7) var(--space-4)` on < 640px.
- Container max width `1200px`, side gutters `var(--space-5)` (16px) mobile / `var(--space-6)` (32px) ≥1024px.

---

## 10. Accessibility (must‑haves)
- Cohort tabs: `role="tablist"`, `role="tab"`, `aria-selected`, roving `tabindex`, arrow‑key navigation.
- Cards: the stretched link is a real `<a>` with a meaningful accessible name (e.g. "Open {title} prototype"). `alt` text on thumbnails describes the screenshot.
- Color contrast: body text on white uses `--text-body` (#44425C) and `--text-muted` (#7B7990) — both pass AA at 14px+. On dark hero/footer, use `--text-on-dark` / `--text-on-dark-soft`.
- Focus visible everywhere with the magenta focus ring.
- `prefers-reduced-motion: reduce` → disable reveal animations and the 3px hover lift.
- `lang="en"`, semantic `<header>`/`<main>`/`<section>`/`<footer>`, `<h1>` once in hero, `<h2>` per cohort section, `<h3>` per card.

---

## 11. Tech approach & hosting

- **Output:** a **static site** (single `index.html` + `projects.json` + `styles.css` + assets). Fable may emit Astro/React/HTML; the requirement is that it deploys as static files and loads `projects.json` at runtime via `fetch`.
- **Hosting:** **Cloudflare Pages** (matches the reference's `.pages.dev` URL and gives the cleanest shareable link, e.g. `apa‑projects.pages.dev`; a custom domain can be added later). Connect the Git repo; Cloudflare auto‑deploys on push.
- **Fonts:** load Poppins/Arsenal/Radley/Allura from Google Fonts via the import in §4 (or self‑host if preferred).
- **No backend, no build step required for content updates:** editing `projects.json` + pushing is enough. (If Fable emits a framework that needs a build, the build must not bake `projects.json` into the HTML — it must remain a runtime fetch.)

### Expected file structure (deliverable)
```
/
├─ index.html            (or Fable's framework entry; loads projects.json at runtime)
├─ projects.json         (operator edits this — single source of truth)
├─ styles.css            (AI Product Academy tokens; can be the design system's styles.css)
├─ assets/
│  ├─ logo.png
│  └─ cover-hero.png
└─ screenshots/          (optional, only if using manual thumbnails instead of mShots)
```

---

## 12. Deliverables checklist for Fable
- [ ] Single‑page site with Topbar, Hero, Cohort tabs, Gallery grid, Footer.
- [ ] All visuals use the AI Product Academy tokens (§4) — no invented colors/fonts.
- [ ] Cards render from `projects.json` at runtime; whole card links to `prototypeUrl` (new tab).
- [ ] Cohort tab filtering works (click swaps the grid).
- [ ] Responsive 1/2/3 columns at 640/1024px.
- [ ] mShots thumbnails with one‑shot retry (§8).
- [ ] Hover lift + magenta focus ring + reduced‑motion support.
- [ ] `projects.json` starter with 3 sample projects (provided).
- [ ] Deploys static to Cloudflare Pages.

---

## 13. Sample copy (drop‑in, on‑brand)
- Eyebrow pill: **"AI PM BOOTCAMP · CAPSTONE PROJECTS"**
- H1: **"See what our students built."**
- Subtitle: "Pick a cohort, find a project, and click through to play with the prototype."
- Cohort tab: **"Cohort 1"** + count badge
- Card CTA: **"Open prototype →"**
- Empty state eyebrow: **"COMING SOON"** · h3: "This cohort's projects haven't been added yet." · body: "Check back after the final presentations."
- Footer line: **"Built in the AI PM Bootcamp."** · sub: "Dr. Marily Nika · marily.substack.com"