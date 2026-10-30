---
note_type: Note
created: 2026-10-16 06:14
updated: 2026-10-17
---
# Next.js App with Postgres 

How to stand up any local-only Next.js (React) app with a Postgres database on this machine: auto-start at login, loopback only, nightly backups, zero root.

Replace `APPNAME` with your app name and `PORT` with its registry port everywhere — files, directories, labels, database. 

**Prerequisites:**

* [[Install Node.js via Homebrew]] — provides `/opt/homebrew/bin/node`
* [[Install PostgreSQL via Homebrew]] — provides the server on `127.0.0.1:5432`

**Design decision:** apps run on `127.0.0.1:300x` — one port per app, no hostnames, no port-80 redirect. An earlier budget-specific draft used a `*.localhost` name with a pfctl kernel redirect and a root LaunchDaemon so the URL could drop `:3000`. All of that existed to remove four characters from a URL you'll never type, and it bought a hand-edited `/etc/pf.conf` Apple can overwrite, a pf service that's off by default after reboot, and a root Daemon claiming *all* loopback port-80 traffic. This design's entire root surface is: nothing. User LaunchAgents only, zero `sudo` in steady state. The trade is typing a port number, which a Raycast Quicklink makes irrelevant on day one.

## Dev and prod on one machine

Same code, two worlds, one Mac. The separation is three cuts — database, port, process — and Next.js wires the switch for you: `next dev` loads `.env.development`, `next start` loads `.env.production`. The code never knows which world it's in.

| | Production | Development |
|---|---|---|
| Database | `APPNAME` | `APPNAME_dev` |
| Port | `PORT` (registry) | `PORT + 100` |
| Process | LaunchAgent, `next start` | manual, `next dev -p` |
| Data | real, backed up nightly | disposable seed data |

**Pause and predict:** you cd into the app and run `npm run dev` while the production service is holding port 3000. What happens?

**Answer:** `EADDRINUSE` — `next dev` defaults to 3000 and the LaunchAgent already owns it. That collision is why dev gets its own port by rule, not accident: dev is always prod port + 100 (`next dev -p 3100` for budget). A rule you can compute beats a port you have to remember.

## The whole system in one diagram

```
Mac boots → you log in
  ├─ launchd → postgresql@18     (brew's LaunchAgent)   127.0.0.1:5432
  └─ launchd → next start        (your LaunchAgent)     127.0.0.1:PORT

Browser → http://127.0.0.1:PORT
```

Three pieces, none of them root.

---

## Port registry

One app, one port, registered here *before* the plist is written. This table is the source of truth — the day app #2 exists, "300x" becomes a numbering scheme, and numbering schemes without a registry become a Saturday of "why is Life OS serving the budget schema."

| Port | Dev port | App                         | LaunchAgent label             |
| ---- | -------- | --------------------------- | ----------------------------- |
| 3000 | 3100     | budget                      | `dev.budget.next`             |
| 3001 | 3101     | *(next app — reserve here)* | —                             |
| 5432 | —        | postgres                    | `homebrew.mxcl.postgresql@18` |

Dev port is always prod port + 100. The prod port is claimed forever; the dev port is just arithmetic.

Rule: never reuse a port, never let an app pick its own. Check the table, claim the next number, write the plist.

### The cookie gotcha (read once, before you ever add auth)

`127.0.0.1:3000` and `127.0.0.1:3001` are different origins — `localStorage`, `fetch`/CORS, and service workers isolate correctly. **Cookies do not.** Cookie scope is host-only with no port, so every cookie one app sets is sent to every other app you run on `127.0.0.1`. Harmless today with no auth. The day you add sessions, prefix cookie names per app (`budget_session`, `lifeos_session`) or enjoy debugging phantom cross-app logouts on a machine only you use. The same trap exists *within* one app: dev on 3100 and prod on 3000 share cookies too, so a sloppy `session` cookie in dev evicts prod's. Prefix per environment as well (`budget_dev_*`) the day auth arrives.

---

## 1. Scaffold the app

```bash
mkdir -p ~/Dev && cd ~/Dev
npx create-next-app@latest APPNAME --typescript --app --no-tailwind --no-eslint --no-src-dir --import-alias "@/*"
cd APPNAME
npm install pg
```

Plain-text schema, because migrations you can `cat` are migrations you can trust. Create `db/schema.sql` — example shape, adjust per app:

```sql
CREATE TABLE IF NOT EXISTS items (
  id         BIGSERIAL PRIMARY KEY,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  data       JSONB       NOT NULL DEFAULT '{}'
);
CREATE INDEX IF NOT EXISTS idx_items_created ON items (created_at DESC);
```

Create `db/pool.ts`:

```ts
import { Pool } from "pg";

const globalForPg = globalThis as unknown as { pool?: Pool };

export const pool =
  globalForPg.pool ??
  new Pool({
    connectionString: process.env.DATABASE_URL,
    max: 5,
    idleTimeoutMillis: 30_000,
  });

// In dev, HMR reloads modules — reuse the pool or you leak connections per reload.
if (process.env.NODE_ENV !== "production") globalForPg.pool = pool;
```

Create both env files — Next picks the one matching the command, which is the entire dev/prod switch:

`.env.development` (loaded by `next dev`):

```
DATABASE_URL=postgres://localhost:5432/APPNAME_dev
```

`.env.production` (loaded by `next start`):

```
DATABASE_URL=postgres://localhost:5432/APPNAME
```

Add both to `.gitignore` now, while they hold no secrets — trust auth means the URLs are harmless today, but the habit needs to exist before the files do.

An example API route, `app/api/items/route.ts`:

```ts
import { NextResponse } from "next/server";
import { pool } from "@/db/pool";

export async function GET() {
  const { rows } = await pool.query(
    "SELECT id, created_at, data FROM items ORDER BY created_at DESC LIMIT 50"
  );
  return NextResponse.json(rows);
}
```

A health endpoint that tests the real dependency, `app/api/health/route.ts`:

```ts
import { NextResponse } from "next/server";
import { pool } from "@/db/pool";

export async function GET() {
  await pool.query("SELECT 1");
  return NextResponse.json({ ok: true });
}
```

`lsof` tells you a socket is listening; `/api/health` tells you Next, the pool, and Postgres are all alive in one request. Keep this route in every app — the "Prove it" section depends on it.

And a server component, `app/page.tsx`, to prove the React → Postgres loop with no client-side anything:

```tsx
import { pool } from "@/db/pool";

export const dynamic = "force-dynamic";

export default async function Home() {
  const { rows } = await pool.query("SELECT COUNT(*) AS n FROM items");
  return <main style={{ fontFamily: "monospace", padding: "2rem" }}>Rows: {rows[0].n}</main>;
}
```

**Pause and predict:** that `export const dynamic = "force-dynamic"` line looks like boilerplate. Delete it, build, start the service, insert a row, reload the page. What does the count show?

**Answer:** the old count, forever, until the next build. A server component with no dynamic API (`cookies()`, `searchParams`) looks static to Next, so `npm run build` **prerenders it** — connecting to Postgres at build time and baking the result into the HTML. The API route stays live, so `/api/items` tells the truth while the homepage lies. This is the "it worked in dev" class of bug: dev never prerenders. One line opts the route out. Leave it in.

**Pause and predict:** the auto-start service runs `next start` against a production build. Why not just leave `next dev` running as the service — it's already what you use all day?

**Answer:** `next dev` is a compiler with a watcher attached: slow boot, hundreds of MB of overhead, and hot-reload machinery that exists to be poked by humans. Worse, if it ever crashes, `KeepAlive` restarts it, and a restart-looping dev server will happily chew your CPU all day while you're in the truck. `next start` boots a prebuilt bundle in ~100 ms and then sits quiet. Build once (`npm run build`), serve forever.

---

## 2. Per-app databases

The server itself is a one-time install — see [[Install PostgreSQL via Homebrew]]. Each app gets **two** databases — one per world:

```bash
createdb APPNAME_dev
createdb APPNAME
psql APPNAME_dev -f db/schema.sql
psql APPNAME -f db/schema.sql
psql APPNAME_dev -c "INSERT INTO items (data) VALUES ('{\"seed\": true}');"
```

Seed data goes in dev only. Prod starts empty — never with fake rows you'll have to delete before the numbers mean anything.

Verify the server is loopback-only — "local only" is a property you confirm, not assume:

```bash
psql APPNAME_dev -c "SHOW listen_addresses;"   # want: localhost
```

---

## 3. The LaunchAgent: Next.js starts when you log in

Build the production bundle, make log and backup dirs, then write the plist:

```bash
cd ~/Dev/APPNAME && npm run build && mkdir -p logs backups bin
```

Create `~/Library/LaunchAgents/dev.APPNAME.next.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>dev.APPNAME.next</string>

  <key>ProgramArguments</key>
  <array>
    <string>/opt/homebrew/bin/node</string>
    <string>/Users/greg/Dev/APPNAME/node_modules/next/dist/bin/next</string>
    <string>start</string>
    <string>-p</string>
    <string>PORT</string>
    <string>-H</string>
    <string>127.0.0.1</string>
  </array>

  <key>EnvironmentVariables</key>
  <dict>
    <key>PATH</key>
    <string>/opt/homebrew/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
    <key>NODE_ENV</key>
    <string>production</string>
  </dict>

  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>

  <key>StandardOutPath</key>
  <string>/Users/greg/Dev/APPNAME/logs/next.log</string>
  <key>StandardErrorPath</key>
  <string>/Users/greg/Dev/APPNAME/logs/next.err.log</string>
</dict>
</plist>
```

Details doing real work: `-H 127.0.0.1` binds loopback only, so the app is invisible to your LAN even without a firewall rule; `RunAtLoad` starts it at login; `KeepAlive` restarts it if it dies. Absolute paths matter — launchd doesn't read your `.zshrc` (see [[Install Node.js via Homebrew]] for why brew's node is the right binary to point at).

Load it with the modern syntax:

```bash
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/dev.APPNAME.next.plist
```

One housekeeping note: `StandardOutPath` appends forever. These logs grow unbounded — either add a `newsyslog` entry later or accept a quarterly `truncate`. Naming it here so it's a choice, not a surprise.

**Pause and predict:** in the old design, pfctl got a *Daemon* while the app got an *Agent*. That distinction is still worth knowing — what's the actual difference?

**Answer:** Daemons run as root at boot, before anyone logs in — right for a kernel rule, wrong for a web app holding a database connection. Agents run as *you* at login, with your `$HOME` and your file permissions. Under this design, nothing needs a Daemon at all: the app's steady state is user Agents only (yours and brew's) and zero root.

---

## 4. Backups: the part availability docs forget

Everything above is about the app being *up*. Nothing above stops a dead SSD from taking the data with it. Data on one laptop with zero copies is the actual risk in this system, and it costs one more LaunchAgent to fix.

Create `~/Dev/APPNAME/bin/backup.sh`:

```bash
#!/bin/bash
set -e
/opt/homebrew/opt/postgresql@18/bin/pg_dump -Fc APPNAME > "$HOME/Dev/APPNAME/backups/APPNAME-$(date +%F).dump"
find "$HOME/Dev/APPNAME/backups" -name "*.dump" -mtime +14 -delete
```

```bash
chmod +x ~/Dev/APPNAME/bin/backup.sh
```

Create `~/Library/LaunchAgents/dev.APPNAME.backup.plist` (set an hour you'll be logged in — 18:00 shown):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>dev.APPNAME.backup</string>

  <key>ProgramArguments</key>
  <array>
    <string>/Users/greg/Dev/APPNAME/bin/backup.sh</string>
  </array>

  <key>StartCalendarInterval</key>
  <dict>
    <key>Hour</key>
    <integer>18</integer>
    <key>Minute</key>
    <integer>0</integer>
  </dict>

  <key>StandardErrorPath</key>
  <string>/Users/greg/Dev/APPNAME/logs/backup.err.log</string>
</dict>
</plist>
```

```bash
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/dev.APPNAME.backup.plist
~/Dev/APPNAME/bin/backup.sh   # run once now — an untested backup is a rumor
ls -la ~/Dev/APPNAME/backups/
```

Nightly dump, two weeks retained, one command to verify. It covers the production database only — `APPNAME_dev` is disposable by design. If the backups folder sits under Dropbox it gets off-machine for free; if not, copy it somewhere off this laptop occasionally. Restore is `pg_restore -d APPNAME_new file.dump`.

---

## 5. Prove it

```bash
lsof -nP -iTCP:PORT -sTCP:LISTEN              # next is up
lsof -nP -iTCP:5432 -sTCP:LISTEN              # postgres is up
curl -s http://127.0.0.1:PORT/api/health      # {"ok":true} — next + pool + postgres
curl -s http://127.0.0.1:PORT/api/items       # your seed row, as JSON
open http://127.0.0.1:PORT                    # page renders, fresh every load
```

Then the only test that matters — reboot, log in, don't touch anything:

```bash
sudo reboot
# ... after login ...
curl -s http://127.0.0.1:PORT/api/health   # should answer on its own
```

If it doesn't, the logs tell you which layer lied: `tail ~/Dev/APPNAME/logs/next.err.log`, `tail /opt/homebrew/var/log/postgresql@18.log`, `tail ~/Dev/APPNAME/logs/backup.err.log`.

Final step: bookmark it, or add a Raycast Quicklink named `APPNAME` pointing at `http://127.0.0.1:PORT`. That's the "easy name" — delivered by your launcher, where it costs nothing.

---

## 6. Adding the next app (the checklist)

This is what makes the doc a base instead of a one-off. App #2 in twenty minutes:

1. **Claim a port** in the registry table above. Never reuse, never let the app pick.
2. **Scaffold** — same `create-next-app` command, new directory.
3. **Database** — `createdb APPNAME_dev` and `createdb APPNAME`, apply `db/schema.sql` to both, point each env file at its own database.
4. **Copy the plist** — duplicate the previous app's, change exactly three things: `Label` (`dev.APPNAME.next`), every path, and the port. Bootstrap it.
5. **Backups** — same for `backup.sh` / the backup plist, or generalize the script to loop over databases.
6. **Update this doc** — registry row, cheat-sheet entries. If it's not in the doc, it doesn't exist.

Everything else — the pool pattern, `force-dynamic` on db-backed pages, the health route, `-H 127.0.0.1` — carries over unchanged. And remember the cookie note: per-app cookie prefixes the moment any of them grow auth.

---

## Cheat sheet

Replace `APPNAME` and `PORT` per app.

| Task | Command |
|---|---|
| Restart app after code changes | `cd ~/Dev/APPNAME && npm run build && launchctl kickstart -k gui/$(id -u)/dev.APPNAME.next` |
| Stop the app | `launchctl bootout gui/$(id -u)/dev.APPNAME.next` |
| Start the app again | `launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/dev.APPNAME.next.plist` |
| App logs | `tail -f ~/Dev/APPNAME/logs/next.err.log` |
| Postgres logs | `tail -f /opt/homebrew/var/log/postgresql@18.log` |
| Health check | `curl -s http://127.0.0.1:PORT/api/health` |
| DB console (prod) | `psql APPNAME` |
| DB console (dev) | `psql APPNAME_dev` |
| Dev server | `cd ~/Dev/APPNAME && npm run dev -- -p 3100` |
| Backup now | `~/Dev/APPNAME/bin/backup.sh` |
| Restore a backup | `pg_restore -d APPNAME_new ~/Dev/APPNAME/backups/APPNAME-YYYY-MM-DD.dump` |
| Service states | `brew services list` · `launchctl print gui/$(id -u)/dev.APPNAME.next` |
| New schema change | dev first: `psql APPNAME_dev -f db/schema.sql`, test on :3100, then `psql APPNAME -f db/schema.sql` |

The pattern generalizes: any local app, any port — one plist, one registry row, one backup. A new app is a copy-paste with three edits, not a re-read of this document.
