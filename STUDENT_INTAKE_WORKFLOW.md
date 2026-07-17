# Student Intake → Gallery Workflow (Option B · Manual)

This is the streamlined, **manual** process for capturing what students build and getting it onto the site. It's deliberately low‑tech so you can run it yourself and understand every step. No code changes are needed to add projects — you only ever edit **one file** (`projects.json`) and redeploy.

> Pair this with `FABLE5_SPEC.md` (the build spec) and `projects.json` (the data file the site reads).

---

## The flow at a glance

```
Students submit a short form
        ↓
You collect submissions (Google Sheet / email)
        ↓
You paste each project as one entry in  projects.json
        ↓
You commit + push  →  Cloudflare Pages auto‑deploys
        ↓
Live on the shareable URL (thumbnails auto‑generate)
```

The thumbnail of each project's homepage is **auto‑generated from the prototype URL** (WordPress mShots) — so you never take or upload screenshots yourself unless you want to.

---

## Step 1 — Send students the intake form

Use a **Google Form** (free, dumps into a Google Sheet). Create a form titled **"AI PM Bootcamp — Capstone Project Submission"** with exactly these fields:

| # | Field | Type | Required | Notes / helper text |
|---|---|---|---|---|
| 1 | Your full name | Short answer | yes | Used for "By ____" and the avatar initial. |
| 2 | Project name | Short answer | yes | The title shown on the card. |
| 3 | One‑line description | Paragraph | yes | 1–2 sentences, max ~180 chars. What it does + why AI. |
| 4 | Live prototype URL | Short answer | yes | Must be a public `https://` link (Vercel, Netlify, Cloudflare, etc.). This is what the card opens. |
| 5 | Anything we should know? | Paragraph | no | Optional context (tools used, persona, etc.). |

> Keep it to **5 fields, 4 required.** The shorter the form, the higher the completion rate. (Cohort grouping is added by *you* in Step 3 — students don't pick it.)

**Tip:** turn on "Collect email addresses" in Google Forms so you can follow up if a link is broken.

---

## Step 2 — (One time) collect submissions

- Google Form responses land in a linked Google Sheet automatically (Forms → "Link to Sheets").
- Each row = one student submission. Columns match the fields above + a timestamp.

---

## Step 3 — Add each project to `projects.json`

Open `projects.json` in any text editor (VS Code, TextEdit, or directly on GitHub). Add one object per project.

### If this is a brand‑new cohort, first add the cohort
```json
"cohorts": [
  { "id": "cohort-1", "name": "Cohort 1", "order": 1 },
  { "id": "cohort-2", "name": "Cohort 2", "order": 2 }
]
```
- `id` is a stable slug (never change it once used).
- `order` controls tab order — **higher = newer**, so the newest cohort shows first.
- `name` is the tab label.

### Then add each project
```json
{
  "id": "crewos",
  "cohortId": "cohort-2",
  "title": "CrewOS",
  "builder": "Unekwu Ojoshaibu",
  "description": "Agentic podcast production — auto-generates show notes, socials, and newsletters while learning creator preferences.",
  "prototypeUrl": "https://crewos.vercel.app",
  "accent": "magenta"
}
```

### Field‑by‑field (how to fill each from the form)
| Field | Where it comes from | Rules |
|---|---|---|
| `id` | You make it up | lowercase‑hyphenated slug, unique (e.g. `crewos`, `my-kahani`). Use the project name. |
| `cohortId` | You set it | Must match a `cohorts[].id`. |
| `title` | Form field 2 | Copy as‑is. |
| `builder` | Form field 1 | Copy as‑is. |
| `description` | Form field 3 | Trim to ~180 chars. No emoji. |
| `prototypeUrl` | Form field 4 | Must start with `https://`. **Open it once to verify it loads** before publishing. |
| `thumbnailUrl` | (omit it) | If omitted, the site auto‑generates a screenshot from `prototypeUrl` via mShots. Only set this manually if you want a specific image. |
| `accent` | (optional) | One of `red`, `orange`, `magenta`, `indigo`. If omitted, the card color rotates automatically. |

> You do **not** need to set `thumbnailUrl`. That's the whole point of the streamlined flow — the homepage screenshot generates itself from the URL.

---

## Step 4 — Commit & deploy

If the repo is connected to **Cloudflare Pages** (one‑time setup, see below), deploying is just:

1. Edit `projects.json` (locally or on github.com).
2. **Commit + push** to the main branch.
3. Cloudflare Pages builds & deploys automatically (~30–60s).
4. Visit the shareable URL to confirm.

No rebuild of the UI in Fable is needed — the site reads `projects.json` fresh on every page load.

### One‑time: connect to Cloudflare Pages
1. Push the Fable‑generated site to a GitHub repo.
2. Cloudflare Dashboard → **Workers & Pages → Create → Pages → Connect to Git**.
3. Pick the repo. Build command: whatever Fable's framework needs (or *none* if it's plain static HTML). Output directory: `.` (or `/dist`, `/public` per Fable's output).
4. Deploy. You get a URL like `apa-projects.pages.dev`. You can rename the project for a cleaner subdomain, or add a custom domain later.

---

## Step 5 — Quick QA (before sharing the URL)
For each new project:
- [ ] Card appears under the right cohort tab.
- [ ] Title + builder + description render correctly.
- [ ] **Click the card** → prototype opens in a new tab and **loads**.
- [ ] Thumbnail shows the homepage screenshot (first load may show a placeholder for a few seconds — refresh once; mShots caches it).
- [ ] No JSON syntax errors (if the page is blank, you likely have a trailing comma or missing quote in `projects.json` — paste it into jsonlint.com to check).

---

## Optional upgrades (later, not now)
- **Self‑serve submissions:** wire the Google Sheet to the site via a small fetch + a Cloudflare Worker, so approved rows publish automatically. (This is Option C in the original plan.)
- **Better thumbnails:** swap mShots for `screenshotone.com` or `urlbox.io` (paid) for higher reliability / custom viewport.
- **Per‑project detail pages:** add the PRD/workflow/video view the reference site has, once you want to showcase *how* a project was built, not just the result.
- **Search / tag by AI tool used** (v0, Claude Code, Lovable…) — add a `tools` array to the schema and a filter pill row.

---

## Ready‑to‑send message to students (copy/paste)

> **Subject:** Share your capstone on the AI Product Academy project gallery
>
> Hi all — congrats on shipping! We're publishing a gallery of this cohort's capstone projects so peers (and recruiters) can browse and click into your live prototypes.
>
> Submit yours here: **[Google Form link]**
>
> You'll need:
> - Your name + project name
> - A one‑line description (what it does + why AI, ~180 chars)
> - The **public URL** of your live prototype (Vercel/Netlify/etc. — make sure it loads without a login)
>
> That's it — the homepage screenshot generates automatically. Deadline: **[date]**.
>
> — Mitchell

---

## One‑page runbook (print this)

```
EACH COHORT:
  1. Create cohort in Google Form? (No — same form, you tag cohort later.)
  2. Add cohort to projects.json → "cohorts" (id, name, order=new highest).

EACH PROJECT:
  3. Copy row from Google Sheet → add object to projects.json → "projects".
     id, cohortId, title, builder, description, prototypeUrl (verify it loads!).
  4. Commit + push.
  5. Open the site, click the card, confirm the prototype opens.

GOTCHA:
  - Bad/typo'd URL or a prototype behind a login = broken card. Always click‑test.
  - Trailing comma in JSON = blank page. Use jsonlint.com if unsure.
```