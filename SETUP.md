# First-time setup

One-time steps to get this repo onto GitHub with all three branches and the deploy pipeline running. Takes about 10 minutes.

---

## 1. Create the repo on GitHub

Go to <https://github.com/new> and create a repository:

- **Name:** `jrpa-calculator`
- **Visibility:** Public *(required for GitHub Pages on a free account — if the JRPA org has a paid plan, private works too)*
- **Do NOT** check "Add a README", "Add .gitignore", or "Choose a license" — this repo already has them, and adding more creates a conflict on the first push.

Copy the repo URL from the page that appears.

---

## 2. Push the three branches

From inside this folder, run:

```bash
git remote add origin https://github.com/YOUR-USERNAME/jrpa-calculator.git

git push -u origin production
git push -u origin staging
git push -u origin dev
```

Replace `YOUR-USERNAME` with your GitHub username or the JRPA org name.

All three branches now exist on GitHub with identical content.

---

## 3. Set `production` as the default branch

**Settings → General → Default branch → switch to `production`**

This makes production the branch people land on, and the one pull requests target by default.

---

## 4. Enable GitHub Pages

**Settings → Pages → Build and deployment**

- **Source:** Deploy from a branch
- **Branch:** `gh-pages` / `(root)`
- Click **Save**

> The `gh-pages` branch doesn't exist yet — it's created automatically the first time the deploy workflow runs. If it isn't in the dropdown, finish step 5 first, push to `staging` once, then come back here.

---

## 5. Protect `staging` and `production`

**Settings → Branches → Add branch ruleset** (or "Add rule" on older GitHub UI)

Create a ruleset targeting **both** `staging` and `production`:

- ☑ **Restrict deletions**
- ☑ **Require a pull request before merging**
  - Required approvals: `0` if you're working solo, `1` once someone else joins
- ☑ **Block force pushes**

Leave `dev` unprotected — that's where the work happens.

This is what makes the two rules physically enforced: nobody can push straight to the live site, and nothing reaches production without passing through a pull request.

---

## 6. Verify the pipeline

Test staging first:

```bash
git checkout dev
git commit --allow-empty -m "Test the deploy pipeline"
git push origin dev
```

Then on GitHub, open a pull request from `dev` into `staging` and merge it.

Watch the **Actions** tab — the "Deploy" workflow should run and finish green. Then visit:

```
https://YOUR-USERNAME.github.io/jrpa-calculator/staging/
```

The banner at the top should say **staging** in amber, and show the commit and deploy time.

Now do the same for production: open a PR from `staging` into `production`, merge it, and visit:

```
https://YOUR-USERNAME.github.io/jrpa-calculator/
```

The banner should say **production** in green.

> First deploys can take 2–3 minutes to appear even after the workflow goes green — GitHub Pages caches aggressively. If you get a 404, wait a minute and hard-refresh.

---

## Done

If both URLs show the right environment name, the pipeline is working and Phase 2 can begin: building the actual calculator on `dev`.
