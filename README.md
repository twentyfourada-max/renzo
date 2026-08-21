# JRPA Calculator

A simple web calculator built with plain HTML, CSS, and JavaScript. No frameworks, no build step.

**JRPA Group Inc. — internal project**

---

## Branch strategy

This repo runs a three-environment pipeline. Work moves in one direction only:

```
dev  ──▶  staging  ──▶  production
```

| Branch | Purpose | Direct pushes | Deploys to |
|---|---|---|---|
| `dev` | All active development happens here | Allowed | Not deployed |
| `staging` | Pre-release testing | Blocked — PR only | `/staging/` |
| `production` | Live site (default branch) | Blocked — PR only | site root |

### Two rules that don't bend

1. **All work starts on `dev`.** Never write code directly on `staging` or `production` — not even a one-line fix, not even an urgent one. If production is broken, the fix goes on `dev` and travels the same path as everything else.
2. **Staging is never skipped.** Nothing merges into `production` until it has been deployed to staging and tested there. There is no fast lane.

Branch protection enforces both rules on GitHub, so the shortcut isn't available even under deadline pressure.

---

## Day-to-day workflow

```bash
# 1. Work on dev
git checkout dev
# ...make changes...
git add .
git commit -m "Add divide operation"
git push origin dev

# 2. When dev is ready to test, open a PR: dev -> staging
#    Merging it auto-deploys to the staging URL.

# 3. Test on staging. If something's wrong, fix it on dev and repeat step 2.

# 4. When staging is verified, open a PR: staging -> production
#    This is the approval gate. Merging it goes live.

# 5. Tag the release
git checkout production
git pull origin production
git tag v1.0
git push origin v1.0
```

---

## Local development

There is no build step. Open the file directly, or serve it locally:

```bash
git checkout dev
python3 -m http.server 8000 --directory src
# then visit http://localhost:8000
```

---

## Project structure

```
.
├── .github/
│   └── workflows/
│       └── deploy.yml     # Deploys staging + production to GitHub Pages
├── src/                   # Everything in here is published
│   ├── .nojekyll          # Tells GitHub Pages to skip Jekyll processing
│   ├── index.html
│   └── style.css
└── README.md
```

Only the contents of `src/` are published. Anything outside it (README, workflows) stays in the repo but never reaches the live site.

---

## How deployment works

A single workflow (`.github/workflows/deploy.yml`) handles both environments and decides where to publish based on which branch was pushed:

- Push to `staging` → publishes `src/` into the `staging/` subfolder of the Pages site
- Push to `production` → publishes `src/` to the Pages site root

Both write into the same `gh-pages` branch, so the workflow uses a concurrency group to stop two deploys from colliding, and `keep_files: true` so a production deploy doesn't wipe the `staging/` folder next to it.

Each deploy stamps the page with its environment name, the short commit SHA, and the deploy time — so you can always confirm which build you're looking at.

> **Note:** because `keep_files` is on, a file deleted from `src/` will linger on the live site. If that ever matters, delete the `gh-pages` branch and re-run both deploys from the Actions tab.

---

## First-time setup

See `SETUP.md` for the one-time steps: creating the repo, pushing the branches, enabling Pages, and applying branch protection.
