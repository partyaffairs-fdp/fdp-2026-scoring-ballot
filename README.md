# 2026 Outreach Grant — Scoring Ballot

Static, single-file scoring app (`index.html`) for grant reviewers. All 233
applications are embedded in the page; each scorer's in-progress scores are
saved locally in their own browser and submitted by downloading/emailing a
CSV. There is no server-side data store, so hosting only needs to solve one
problem: **keep the page itself private to invited scorers.**

## Why Cloudflare, not plain GitHub Pages

GitHub Pages on a free account only serves **public** repos — anyone with the
link (or who finds the `*.github.io` URL) could read every application in
this file, including names, funding asks, and conflict-of-interest notes.
Making it private on GitHub Pages requires GitHub Enterprise Cloud
(~$21/user/mo) just for access control.

Instead: keep the source on GitHub (private repo is fine), and deploy it with
**Cloudflare Pages** (free) + **Cloudflare Access** (free for up to 50 users).
Cloudflare Pages builds straight from a private GitHub repo, and Access puts a
real login wall (email one-time code) in front of the whole site before
anyone reaches `index.html`.

Total cost: **$0/month**, no domain required (you get a free
`your-project.pages.dev` URL).

## 1. Push this repo to GitHub

```bash
git init
git add index.html README.md
git commit -m "Add 2026 Outreach Grant scoring ballot"
```

Create a new repo on GitHub (private is fine — Cloudflare Pages doesn't need
it public) named e.g. `fdp-2026-scoring-ballot`, then:

```bash
git remote add origin https://github.com/<your-username>/fdp-2026-scoring-ballot.git
git branch -M main
git push -u origin main
```

## 2. Deploy with Cloudflare Pages

1. Go to https://dash.cloudflare.com/ and sign up (free) if you don't have an
   account.
2. **Workers & Pages → Create → Pages → Connect to Git.**
3. Authorize Cloudflare to access your GitHub account, pick the
   `fdp-2026-scoring-ballot` repo.
4. Build settings: **no build command, output directory = `/`** (it's a
   static file, nothing to build).
5. Deploy. You'll get a URL like `https://fdp-2026-scoring-ballot.pages.dev`.

## 3. Lock it down with Cloudflare Access (free, up to 50 users)

1. In the Cloudflare dashboard, go to **Zero Trust** (left sidebar) → if
   prompted, pick the **Free** plan.
2. **Access → Applications → Add an application → Self-hosted.**
3. Application domain: select the `pages.dev` domain from step 2 (or a
   subdomain path if you only want to gate part of a site — here, gate the
   whole thing).
4. **Identity providers:** leave the built-in **One-time PIN** enabled (no
   extra setup — Cloudflare emails a login code, nothing to configure).
5. **Policies:** create a policy, e.g. "Scorers", rule: **Emails** — include
   the list of scorer email addresses. Add your own email first to test.
   You can come back and add/remove scorer emails anytime in this policy —
   no redeploy needed.
6. Save. Now visiting the `.pages.dev` URL prompts for an email + one-time
   code before anything loads, and only allow-listed emails can get in.

## Updating the ballot data later

Any time you edit `index.html` (e.g. new applications, corrected data) and
push to `main`, Cloudflare Pages auto-redeploys — no manual step needed.

## Adding/removing scorers

Zero Trust → Access → Applications → this app → Policies → edit the email
list. Takes effect immediately, nothing to redeploy.
