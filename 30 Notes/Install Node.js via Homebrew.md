---
note_type: How To
created: 2026-10-17
---

# Install Node.js via Homebrew

One-time prerequisite. Any local app whose LaunchAgent calls `node` directly depends on this.

```bash
brew install node
```

Verify:

```bash
/opt/homebrew/bin/node --version
```

The absolute path matters more than the version: launchd doesn't read your `.zshrc`, so plists must point at `/opt/homebrew/bin/node`, not rely on PATH.

**Pause and predict:** brew's node is keg-adjacent but symlinked into `/opt/homebrew/bin`. If you later install nvm and let it shadow node, which binary does a LaunchAgent run?

**Answer:** brew's, always — plists use the absolute path, and nvm only manipulates your *shell's* PATH. That's the safe default: services get a stable binary, your terminal gets whatever nvm selects. If you ever want a service to use the nvm version instead, point `ProgramArguments` at `/Users/greg/.nvm/versions/node/vXX/bin/node` or wrap it in a script that sources `~/.nvm/nvm.sh` first — but know that brew's node is the one that survives a careless `nvm uninstall`.
