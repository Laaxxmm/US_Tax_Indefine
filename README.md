# Indefine — US Accounting Services Landing Page

Static landing page for Indefine's US wing. Zero build step — pure HTML, CSS, and a few inline scripts. Deploys to Vercel as a static site.

## What's in this folder

| File | Purpose |
|------|---------|
| `index.html` | **Primary page** (long-form). Served at `/` |
| `short.html` | Tight scanner-optimized version. Served at `/short` |
| `vercel.json` | Vercel config — clean URLs, security headers, cache rules, redirects |
| `robots.txt` | Crawlers allowed on `/`, excluded from `/short` to avoid duplicate content |
| `sitemap.xml` | Points search engines at `/` — update the domain after custom domain is added |
| `.gitignore` | Ignores OS files, editor artifacts, Vercel build outputs |

## Deploy to Vercel via GitHub

### Step 1 — Create a GitHub repo

Sign in to [github.com](https://github.com) and create a new **private** repo named `indefine-site` (or similar). Leave it empty — no README, no license — you'll push from your local machine.

### Step 2 — Push this folder to GitHub

From your terminal, `cd` into this folder, then run these six lines. Replace `YOUR-USERNAME` with your GitHub username.

```bash
git init
git add .
git commit -m "Initial commit: Indefine landing page v1"
git branch -M main
git remote add origin git@github.com:YOUR-USERNAME/indefine-site.git
git push -u origin main
```

If you prefer HTTPS over SSH, use `https://github.com/YOUR-USERNAME/indefine-site.git` for the remote URL instead.

### Step 3 — Import into Vercel

Go to [vercel.com/new](https://vercel.com/new), sign in with your GitHub account, and click **Import** next to the `indefine-site` repo.

On the configuration screen:

- **Framework preset**: Other (Vercel auto-detects static HTML; no build command needed)
- **Root directory**: `./` (the default)
- **Build & output settings**: leave empty — it's a static site
- **Environment variables**: none needed

Click **Deploy**. First deployment takes about 30 seconds.

### Step 4 — You're live

Vercel will show you a URL like `indefine-site-abc123.vercel.app`. You can rename the project in Vercel → Settings → General → **Project Name** to change the subdomain to something cleaner like `indefine-us` → resulting URL `indefine-us.vercel.app`.

After renaming, update two things for SEO accuracy:

1. In `sitemap.xml`, replace `indefine-us.vercel.app` with your actual subdomain.
2. In `robots.txt`, update the `Sitemap:` line to match.

Commit and push — Vercel auto-redeploys.

## Ongoing workflow

Every `git push` to `main` triggers a new Vercel deploy. Typical cycle:

```bash
# Edit index.html or short.html
git add .
git commit -m "Update hero copy"
git push
```

Vercel automatically:
- Builds the new version (for static sites, this is basically instant)
- Creates a preview URL for the commit
- Promotes it to production at your main subdomain
- Invalidates the CDN

## Adding a custom domain later

When you're ready to move from `indefine-us.vercel.app` to something like `indefine.com` or `us.indefine.com`:

1. In Vercel → Project → **Settings → Domains**, click **Add**
2. Enter the domain you want to point at this project
3. Vercel will show you either:
   - Two DNS records to add (A + CNAME) if you're using an external registrar
   - An auto-config option if your DNS is on a supported provider
4. Add the records at your registrar (Cloudflare, Namecheap, GoDaddy, etc.)
5. Wait 5–15 minutes for DNS propagation + automatic HTTPS provisioning

Then update:

- `sitemap.xml` — replace the domain
- `robots.txt` — update the `Sitemap:` URL
- The Open Graph meta tags in `index.html` and `short.html` if you want link previews to show the custom domain

## Local preview

No build step, but for a quick local preview:

```bash
# Python 3
python3 -m http.server 4000

# Or Node (if you have it)
npx serve .
```

Then open [http://localhost:4000](http://localhost:4000).

## Form submissions — important next step

The contact form currently displays a success message on submit but doesn't actually send anywhere. Before driving traffic, wire it up. Three clean options:

1. **Formspree** (easiest) — Add `action="https://formspree.io/f/YOUR-ID"` to the `<form>` tag. Free tier: 50 submissions/month. No backend needed.
2. **Vercel Form handler** — Add a Vercel serverless function in `/api/contact.js` that validates and forwards to email or HubSpot.
3. **HubSpot Forms** — Replace the form with a HubSpot embed. Best if you're already using HubSpot for CRM.

Option 1 gets you live in under five minutes. Option 2 gives you total control and no third-party dependency. Option 3 is right if HubSpot is your system of record.

## A/B test the two versions

The two pages exist specifically to test which converts better:

- `indefine-us.vercel.app/` → v1 long-form
- `indefine-us.vercel.app/short` → v2 tight

For a clean 50/50 split on cold traffic, add a Vercel Edge Middleware file (`/middleware.js`) that randomly rewrites half the traffic from `/` to `/short`. Keep it simple at launch: just drive all SEO/paid traffic to `/`, share `/short` links when talking to partners directly, and compare conversion rates after 2–4 weeks of traffic.

## Technical notes

- External fonts: Google Fonts (Inter, Source Serif 4, Archivo Black)
- External logos: [Simple Icons CDN](https://simpleicons.org) for QuickBooks / Xero / Sage / Zoho / Gusto / ADP / Paychex
- No build tooling, no framework, no package.json
- Page weight: ~52 KB HTML + ~30 KB fonts cached on first load
- Lighthouse target: 95+ on Performance, Accessibility, Best Practices, SEO

## Folder tree

```
indefine-site/
├── index.html        (v1 — long-form, primary)
├── short.html        (v2 — tight)
├── vercel.json       (routing, headers)
├── robots.txt        (crawlers)
├── sitemap.xml       (SEO)
├── .gitignore
└── README.md         (this file)
```
