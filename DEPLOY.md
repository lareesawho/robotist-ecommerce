# Deployment Pipeline — Robotist

## How It Works

This repository uses a **GitHub → Render auto-deploy pipeline**. Every commit pushed to the `main` branch automatically triggers a fresh deploy on Render. No manual steps are required after pushing.

```
Code change → git commit → git push to main → Render detects push → re-deploys automatically
```

---

## What Gets Deployed

| Service | Type | What it does |
|---|---|---|
| `robotist` | Static web | The public-facing Robotist e-commerce site |

The service is defined in `render.yaml` at the root of this repo with `autoDeploy: true`.

---

## The Blueprint File

The file `render.yaml` at the root of this repo is the **Render Blueprint** — it tells Render which branch to watch, where the static files are (`staticPublishPath: .`), and that it should auto-deploy on every push.

**Critical rule:** If `autoDeploy: true` is ever removed or the file is deleted, auto-deploy stops. Render will only deploy manually from the dashboard.

---

## Branch

- **Static site (`robotist`):** watches `main`

Pushes to any other branch (e.g. `claude/...` feature branches) do **not** trigger a deploy.

---

## GitHub Actions: Automatic Image Compression

This repo also has a GitHub Actions workflow at `.github/workflows/compress-images.yml`.

**What it does:** Whenever a push to `main` (or a pull request) includes image files (`.jpg`, `.jpeg`, `.png`, `.webp`, `.gif`), the action automatically compresses them using `calibreapp/image-actions` and commits the optimised versions back to the branch before Render picks them up.

**Quality settings:**
- JPEG / WebP: quality 82 (visually near-lossless, ~40–60% smaller)
- PNG: compression level 5

**Note:** This action only runs when image files are changed. Text-only commits skip it entirely.

> **History note:** This workflow was accidentally deleted in the "Full SEO/GEO implementation" commit on Apr 14 2026 and was restored on Jun 23 2026.

---

## Troubleshooting Auto-Deploy

If a push is not triggering a deploy on Render:

1. **Check `render.yaml` exists** at the repo root and contains `autoDeploy: true`.
2. **Check the branch** — the push must be to `main`, not another branch.
3. **Check Render dashboard** → service → **Events** tab to see if the deploy was triggered.
4. **Check the GitHub webhook** — Render registers a webhook on the repo. Go to GitHub → repo Settings → Webhooks and confirm a Render webhook is listed and showing green ticks.
5. **Re-sync the blueprint** — in Render dashboard, go to the service → Settings → scroll to "Blueprint" and click "Sync" to force Render to re-read `render.yaml`.
