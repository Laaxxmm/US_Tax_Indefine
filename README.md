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

## Form submissions — how the lead form works

The contact form on both pages submits via **FormSubmit** (formsubmit.co) — a free, no-signup email-forwarding service. Every submission is POSTed as JSON to:

```
https://formsubmit.co/ajax/rajkumar@indefine.in
```

Rajkumar receives each lead as an email with a structured table of all form fields, plus a Reply-To header set to the lead's own email so replies go straight to the prospect.

### First-time activation (one-time, required)

The very first submission FormSubmit receives for `rajkumar@indefine.in` will trigger a verification email to that address. **Rajkumar must click the activation link in that email** before FormSubmit will forward any real leads.

To activate immediately (before traffic arrives):

1. Visit your live site (or local preview)
2. Submit the contact form with any test content
3. Check `rajkumar@indefine.in` inbox (and spam folder — the activation email comes from `noreply@formsubmit.co`)
4. Click the activation link
5. Done — all subsequent submissions will forward to that inbox

### Moving to a more robust setup later

FormSubmit's free tier is generous but not infinite (~50 submissions/day soft limit, some rate limiting on bursts). Once volumes grow or you want full control over deliverability, swap it for one of these:

1. **Formspree paid tier** — Same pattern, better deliverability, $10/month
2. **Vercel serverless + Resend** — Add `/api/contact.js` that uses the Resend SDK to send from an authenticated `@indefine.in` sender. Best for brand consistency and no third-party dependency
3. **HubSpot Forms embed** — Drop in a HubSpot form instead. Right call if HubSpot is your CRM

All three require changing only the form's submit handler — the form HTML stays the same.

### Spam protection

The form includes a hidden honeypot field (`_honey`) that legitimate users never fill. Bots typically fill every field, so FormSubmit discards any submission with that field populated. If spam becomes a problem, flip `_captcha` from `false` to `true` in both HTML files to enable FormSubmit's built-in captcha.

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
