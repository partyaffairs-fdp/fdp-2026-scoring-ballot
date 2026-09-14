# 2026 Outreach Grant — Scoring Ballot

Static, single-file scoring app (`index.html`) for grant reviewers. All 233
applications are embedded in the page; each scorer's in-progress scores are
saved locally in their own browser and submitted by downloading/emailing a
CSV. There is no server-side data store.

## How access is protected

The page shows a password prompt before revealing any content
(`index.html` lines near `<body class="locked">`). The password isn't stored
in plain text in the file — only its SHA-256 hash is — and entering it once
unlocks the page on that device going forward (stored in `localStorage`).

**Important limitation:** this is a deterrent, not real security. GitHub
Pages only serves fully public sites, so the entire page — including every
application's data — is downloaded to any visitor's browser whether or not
they enter the password. Someone who opens the browser's dev tools, uses
"view source," or fetches the raw file directly can read everything without
ever seeing the prompt. This keeps out casual/accidental visitors and search
engines that don't index it, but it does **not** stand up to anyone who
actively tries to bypass it. That tradeoff was chosen deliberately to keep
hosting free and GitHub-only — see the git history / conversation for the
alternatives (a free Netlify/Vercel edge function for real server-enforced
auth, or a private GitHub repo with no public page at all) if that ever
needs to change.

**The current password is not written down in this repo** (this repo is
public, so anything here is world-readable — putting the password in this
file would defeat the gate entirely). Whoever last rotated it should share
it with scorers directly (e.g. by email or text), out of band from GitHub.

## 1. Push this repo to GitHub

```bash
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git push -u origin main
```

(Repo can be public — it needs to be, for GitHub Pages to work on a free
account. Given the caveat above, treat the password as a light gate, not a
guarantee of confidentiality.)

## 2. Enable GitHub Pages

1. On the repo's GitHub page: **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: `main`, folder: `/ (root)`. Save.
4. GitHub gives you a URL like `https://<your-username>.github.io/<repo-name>/`
   within a minute or two. Share that URL (and the password, separately)
   with scorers.

## Changing the password later

1. Pick a new password.
2. Compute its SHA-256 hex hash, e.g.:
   ```bash
   node -e "console.log(require('crypto').createHash('sha256').update('NEW-PASSWORD-HERE').digest('hex'))"
   ```
3. In `index.html`, find `var HASH = "..."` and replace the hex string with
   the new hash. Also bump `var KEY = "fdp2026_unlocked_v1"` to `..._v2` (or
   any new value) so previously-unlocked browsers are asked for the new
   password instead of staying unlocked on the old one.
4. Commit and push — GitHub Pages redeploys automatically.

## Updating the ballot data

Edit `index.html` and push to `main`; GitHub Pages redeploys automatically,
usually within a minute or two.
