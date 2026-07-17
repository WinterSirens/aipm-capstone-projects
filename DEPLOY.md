# Deploy & Share

The site to publish lives in **`public/`** (index.html, styles.css, projects.json, assets/).
The internal docs and preview PNGs at the project root are **not** published.

---

## ✅ Recommended: Cloudflare Pages (clean `*.pages.dev` URL, matches the plan)

From this folder, run:

```bash
cd ~/Desktop/ai_pm_demo_page
npx wrangler pages deploy public --project-name=aipm-capstone-projects
```

- **First run only:** Wrangler opens your browser to log into (or create) a **free** Cloudflare account. Approve, come back to the terminal.
- It uploads `public/` and prints a live URL like:
  `https://aipm-capstone-projects.pages.dev`
- **Share that URL** with Marily and the team.

### Updating later (after you edit `public/projects.json`)
Just run the same command again — it redeploys in a few seconds:
```bash
npx wrangler pages deploy public --project-name=aipm-capstone-projects
```

> Tip: the first deploy also creates a permanent "production" URL. Each deploy additionally gives a unique preview URL (e.g. `https://<hash>.aipm-capstone-projects.pages.dev`) if you want to share a specific version.

---

## ⚡ Fastest, no terminal: Netlify Drop (good for a quick one-off preview)

1. Go to **https://app.netlify.com/drop**
2. Drag the **`public`** folder onto the page.
3. You instantly get a URL like `https://sunny-otter-123.netlify.app`.
4. Create a free account when prompted to keep it online + get a nicer name.

---

## 🔄 Best for ongoing edits: GitHub + Cloudflare Pages (auto-deploy on push)

Do this once you want "edit `projects.json` → push → it deploys itself."

```bash
cd ~/Desktop/ai_pm_demo_page
git init
git add .
git commit -m "AI Product Academy — capstone projects gallery"
gh auth login            # one-time browser login to GitHub
gh repo create aipm-capstone-projects --private --source=. --push
```

Then in the Cloudflare dashboard → **Workers & Pages → Create → Pages → Connect to Git**:
- Pick the `aipm-capstone-projects` repo
- **Build command:** _(leave empty)_
- **Build output directory:** `public`
- Deploy. Every `git push` now redeploys automatically.

---

## Notes
- No build step is needed — it's a static site that reads `public/projects.json` at runtime.
- A `.gitignore` keeps the internal spec docs and preview PNGs out of any repo (they're at the project root, and only `public/` is deployed regardless).
- Custom domain (e.g. `projects.aiproductacademy.com`) can be added later in the Cloudflare Pages project settings.
