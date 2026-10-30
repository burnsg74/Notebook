---
note_type: Note
created: 2026-10-29 06:24
updated: 2026-10-29 06:24
---
> Pattern for multiple local-only web apps: named URLs (`pm.localhost`), auto-start on login, vault files as data, no framework unless it earns its keep.

This note is for small local tools (PM, budget-style UIs, etc.) that live in the browser and read/write this vault. It is not the Alfred desktop app (Tauri), and it is not the older Next.js + Postgres + port plan in [[App Setup Plan]].

## Goals

| Want | How |
| --- | --- |
| Named URL, not a port | `http://{app}.localhost` via nginx on port 80 |
| Nothing to start by hand | nginx via `brew services`; APIs via pm2 startup + save |
| Several apps, no collisions | One nginx; one conf + one backend port per app (registry below) |
| Simple stack | Static Svelte SPA; add a tiny Node API only when the browser must touch vault files |
| Local only | Loopback binds; no auth; never expose beyond this machine |

Day-to-day: open the URL. After reboot, apps are already up.

## Shared once (machine)

Install and leave running:

- Node (Homebrew)
- nginx (`brew services start nginx` so it comes back on login)
- pm2 (`npm i -g pm2`, then one-time `pm2 startup` and after each new app `pm2 save`)

Browsers resolve `*.localhost` to loopback (RFC 6761). Safari on macOS often does not, so each app's setup still adds `/etc/hosts` lines for `127.0.0.1` and `::1`. nginx listens on IPv4 and IPv6 (`listen 80;` and `listen [::]:80;`).

Apps live under something like `~/Code/Local Apps/{app}/`. Vault data stays in this vault under a folder the app owns (for example `10 Clients/Work/{App}/data/`).

## App registry

Claim a hostname and API port here before scaffolding. Frontend is always port 80 via the hostname; the port is only for the backend behind nginx.

| Hostname | API port | App | pm2 name | Notes |
| --- | --- | --- | --- | --- |
| `pm.localhost` | 4000 | PM | `pm-api` | |
| `budget.localhost` | 4001 | *(reserve)* | | |
| `jobhunt.localhost` | 4002 | *(reserve)* | | |

Next free API port: **4003**. Never let two apps share a port or hostname.

## Default shape of one app

```
{app}/
  frontend/          Vite + Svelte 5 SPA, plain JS (no TypeScript)
  backend/           only if the app needs vault I/O (see below)
  deploy/
    nginx/{app}.conf
    ecosystem.config.cjs   # only if there is a backend
    setup.sh               # idempotent: deps, build, nginx, hosts, pm2, verify
    deploy.sh              # later updates: build, copy, reload/restart
  README.md
```

**Frontend.** Vite + Svelte SPA. Build to static files. nginx serves them with SPA fallback (`try_files $uri /index.html`). No SvelteKit unless you need its router/SSR features (you don't for these apps). Dev: `vite` with a `/api` proxy to the backend when one exists.

**Backend — only when needed.** The browser cannot write Dropbox/vault paths. If the UI must list/read/write `.md` / `.csv` in the vault, add a small Node process. Use Node's built-in `node:http` (or `node:http` + a few helpers). Do **not** add Fastify, Express, or similar unless the API grows enough that a framework clearly saves work.

Minimal API when you have a backend:

- `GET /api/health` — env + vault/data paths readable
- `GET /api/files` — list `.md` / `.csv` under `DATA_DIR` (relative paths only)
- `GET /api/files/*` — read one file (markdown text + frontmatter if present; CSV as row objects)
- `PUT /api/files/*` — create/replace; refuse `..` and absolute paths

`VAULT_PATH` defaults to this vault root. `DATA_DIR` is relative to it. Frontend talks only to `/api/*`, never to disk.

**No backend.** Pure static UI (or UI that only calls remote APIs): skip `backend/`, skip pm2, nginx only serves the build.

**nginx.** One server block per app: `server_name {app}.localhost;`, static root, SPA fallback, and if there is an API: `location /api/` → `http://127.0.0.1:{port}`. Symlink into nginx's enabled sites; `nginx -t` then reload.

**Auto-start.** setup.sh starts/reloads nginx through brew services and `pm2 start` + `pm2 save` for the API. After a reboot you should not need to run anything.

## Dev vs prod

| | Prod | Dev |
| --- | --- | --- |
| URL | `http://{app}.localhost` | Vite dev URL (proxy `/api`) |
| Data | `{vault-app}/data/` | `{vault-app}/data/dev/` |
| API | pm2, `NODE_ENV=production` | `node --watch` on the API port |
| Frontend | nginx static build | Vite |

Same layouts in `data/` and `data/dev/`. Seed both with example `.md` / `.csv` so first run has something to show. Optional: `backend/.env.development` and `.env.production` (gitignored; ship `.env.example`) with `VAULT_PATH` and `DATA_DIR`.

## Backups

Dropbox already syncs the vault. Optional: a small `backup.sh` that tars `DATA_DIR` into `~/backups/{app}/` and keeps the last 14; `restore.sh` for a test restore into `data/dev/`.

## Scaffold prompt

Paste into Cursor when creating an app. Replace `{APP_NAME}` (folder/pm2 slug, e.g. `pm`) and claim the next registry row first.

```
Set up a local web app "{APP_NAME}" at http://{APP_NAME}.localhost.
Create the files and scripts, run setup/verify, then give a short summary
(URLs, ports, paths, how to tear down). No step-by-step narration.

Constraints:
- Local-only, plain JS (ESM), no TypeScript, no auth/hardening.
- Frontend: Vite + Svelte 5 SPA. nginx serves the build with SPA fallback.
- URL is the point: browser uses {APP_NAME}.localhost on port 80, never a
  typed port. Add /etc/hosts for 127.0.0.1 and ::1 (Safari).
- Backend: only if this app reads/writes vault markdown/CSV. Then use
  node:http (no Fastify/Express) with GET /api/health and GET/PUT
  /api/files as in 20 Notes/Local web app setup.md. pm2 runs it;
  nginx proxies /api/. Bind loopback only. Claim API port from the
  registry in that note.
- If no vault I/O, omit backend and pm2 entirely.
- VAULT_PATH default:
  /Users/greg/Library/CloudStorage/Dropbox/Notebooks/Work
  App data under a dedicated vault folder; never write outside the vault.
- deploy/setup.sh is idempotent and leaves the app auto-starting
  (brew services nginx + pm2 save). deploy/deploy.sh for later updates.
- Ask before overwriting existing nginx conf, vault data, or a taken port.

Verify: curl -4/-6 http://{APP_NAME}.localhost/ (and /api/health if any),
SPA deep link returns index.html, pm2 online if applicable, seed files
list/read via API if applicable.
```

## Verify and tear down

**Verify:** `curl -4` and `curl -6` to the hostname; deep frontend route returns the SPA; pm2 shows the API if there is one; file API lists and reads seed data.

**Tear down:** remove the nginx site symlink and reload; `pm2 delete {app}-api` and `pm2 save`; remove hosts lines; optionally delete the app folder and vault data folder (ask first).

## Related

- Older port-based Next + Postgres pattern: [[App Setup Plan]]
- Per-app stubs: `01 Projects/PM web app`, `01 Projects/Budget web app`, `01 Projects/Job Hunting web app`
- Alfred (different stack: Tauri, not this): `10 Clients/Work/BWS/Projects/Alfred/Notes`
