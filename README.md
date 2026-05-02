# ☁️ CloudSurf

Free cloud browser farm — run persistent Chrome profiles in GitHub Codespaces, fully automated via GitHub Actions.

---

## How it works

- Each browser profile runs in its own Xvfb display with VNC + NoVNC
- A Flask UI at port 7860 manages all profiles
- GitHub Actions keeps the Codespace alive and running on a schedule
- On every Codespace start, CloudSurf launches automatically

---

## First-time setup

### 1. Create your Codespace
Go to your repo → **Code → Codespaces → New codespace**

Wait for it to build and CloudSurf to launch automatically at port 7860.

### 2. Create your browser profiles
Open the CloudSurf UI and create your profiles (up to 9 for GPU Colab sessions).

Note down the profile IDs — you'll need them for auto-launch.

### 3. Set the Codespace name for GitHub Actions
Go to your repo → **Settings → Secrets and variables → Actions → Variables**

| Variable | Value |
|----------|-------|
| `CLOUDSURF_CODESPACE_NAME` | your codespace name (run `gh codespace list` to get it) |

### 4. Push the workflow
Place `.github/workflows/launcher.yml` in your repo and push to main — it will run automatically on schedule.

---

## Schedule

The action runs every 10 minutes during two daily windows (UTC):

| Window | Time (UTC) | Best for |
|--------|-----------|---------|
| Night | 2am – 8am | Lowest Colab traffic, best GPU availability |
| Midday | 2pm – 4pm | Secondary window |

To change the schedule, edit the `cron` lines in `.github/workflows/launcher.yml`.

---

## Manual trigger

Go to **Actions → CloudSurf Runner → Run workflow** to start it anytime.

---

## Scripts

Drop `.js` files into the `scripts/` folder to automate actions in Chrome via Puppeteer (CDP).

Each script receives:
- `CLOUDSURF_CDP_URL` — WebSocket URL for Puppeteer
- `CLOUDSURF_CDP_PORT` — raw CDP port
- `CLOUDSURF_PROFILE_ID` — profile ID string
- `DISPLAY` — Xvfb display for this profile

---

## Stopping

```bash
bash stop.sh
```

---

## Logs

| Log | Description |
|-----|-------------|
| `logs/setup.log` | First-time setup output |
| `logs/manager.log` | Flask manager + profile activity |
| `logs/keepalive.log` | Keep-alive pings |
