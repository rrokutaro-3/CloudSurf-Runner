# ☁️ CloudSurf Runner

GitHub Actions workflow that keeps a CloudSurf Codespace alive — waking it automatically every 5 minutes via multiple fallback strategies.

---

## How it works

The workflow runs on a schedule and tries three escalating tricks to wake the Codespace:

1. **`gh codespace start`** — official CLI wake command
2. **SSH ping** — forces the VM runtime to spin up even if the API alone doesn't stick
3. **REST API POST `/start`** — hits the GitHub API directly, bypassing the CLI layer

If none of the tricks immediately return `Available`, it polls every 10 seconds for up to 5 minutes.

---

## Setup

### 1. Fork or clone this repo

### 2. Generate a GitHub Personal Access Token

Go to **GitHub → Avatar → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token**

Check these scopes:
- `repo` — full control of private repositories
- `user` — needed for the REST API call
- `codespace` — full control of codespaces

Copy the token — GitHub only shows it once.

### 3. Add secrets

Go to your repo → **Settings → Secrets and variables → Actions → New repository secret**

| Secret | Value |
|--------|-------|
| `CLOUDSURF_PAT` | Personal access token from step 2 |
| `CLOUDSURF_CODESPACE_NAME` | Your codespace name (run `gh codespace list` to find it) |

> Both values are stored as encrypted secrets and never exposed in logs.

### 4. Push to main

The workflow lives at `.github/workflows/runner.yml` and runs automatically on schedule once pushed.

---

## Schedule

Runs every 5 minutes, continuously.

To change the frequency, edit the `cron` line in `.github/workflows/runner.yml`:

```yaml
- cron: '*/5 * * * *'
```

---

## Manual trigger

Go to **Actions → CloudSurf Runner → Run workflow** to fire it anytime.

---

## Workflow output

Each run logs the codespace state at every step:
📋 Current state: Shutdown
🚀 Trick 1: gh codespace start
State after start: Shutdown
🔌 Trick 2: SSH ping
State after SSH ping: Available
🎉 Done (trick 2)!

If all tricks fail, it polls for X minutes before exiting with a non-zero code (visible as a failed run in Actions).

---

## Related

- [CloudSurf](https://github.com/rrokutaro/CloudSurf) — the main Codespace repo with the browser farm UI
