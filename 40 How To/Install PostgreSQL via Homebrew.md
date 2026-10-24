---
note_type: How To
created: 2026-10-17
---

# Install PostgreSQL via Homebrew

One-time prerequisite. Any local app backed by Postgres depends on this; per-app setup (creating the database, applying a schema) lives in the app's own template — see [[Next.js App with Postgres Template]].

```bash
brew install postgresql@18
echo 'export PATH="/opt/homebrew/opt/postgresql@18/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
brew services start postgresql@18
```

Verify:

```bash
psql -c "SELECT version();"
psql -c "SHOW listen_addresses;"   # want: localhost
```

## Which version to pin

Pin `@18` because that's Homebrew's current *stable* formula as of this writing (Postgres 18.x). Don't follow an RC onto data you'll restore from. The unversioned `postgresql` formula is just an alias that moves — the day the next major goes GA, `brew upgrade` can replace the server under your data directory, and Postgres majors are not in-place upgrades (dump, install, restore). Versioned formulae stay put until you change them.

**Pause and predict:** if Homebrew's unversioned formula already tracks stable, why pin `@18` at all?

**Answer:** because aliases move and versioned kegs don't. `brew upgrade` on an alias can swap majors under an existing data directory and leave you with a cluster the new binary won't open. The pin makes major upgrades a decision, not a side effect.

If a machine already has an older major's data directory (`/opt/homebrew/var/postgresql@16`), stay on that major until you `pg_dump` / `pg_restore` across. Don't install the new major over an existing cluster and expect it to open.

## What Homebrew's defaults give you

`postgresql.conf` binds to `localhost` and `pg_hba.conf` uses `trust` for 127.0.0.1/::1 and `peer` for the Unix socket — no passwords flying around, nothing reachable from your LAN. The superuser role is your macOS username, which is why `createdb` needs no sudo.

**Pause and predict:** `brew services start postgresql@18` — what does that command *actually* install that makes Postgres come back after a reboot?

**Answer:** nothing magic. It writes `homebrew.mxcl.postgresql@18.plist` into `~/Library/LaunchAgents/` and bootstraps it with launchd — the exact same mechanism app templates hand-roll for their own servers. Once you see that, "auto-start on boot" stops being a brew feature and becomes a plist you can write for anything.

## Useful paths

| Thing | Path |
|---|---|
| Binaries | `/opt/homebrew/opt/postgresql@18/bin/` |
| Data directory | `/opt/homebrew/var/postgresql@18/` |
| Logs | `/opt/homebrew/var/log/postgresql@18.log` |
| LaunchAgent | `~/Library/LaunchAgents/homebrew.mxcl.postgresql@18.plist` |
