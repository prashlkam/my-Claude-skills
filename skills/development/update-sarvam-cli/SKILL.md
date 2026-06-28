---
name: update-sarvam-cli
description: >-
  End-to-end maintenance workflow for the Sarvam-CLI fork of opencode: pull the
  latest upstream opencode, re-apply the Sarvam customizations, rebuild the
  binary, and publish the result to the user's GitHub fork (origin/main). Use
  this whenever the user asks to "update sarvam-cli", "update to the latest
  version", "pull upstream", "rebuild", "bump opencode", or "push the latest
  sarvam-cli to my github" while working inside the Sarvam-CLI repository — even
  if they only mention one part (just updating, or just pushing). It also covers
  repairing a corrupt local git object store, which commonly blocks the fetch
  step, and discovering the user's ssh-agent socket so the force-push can
  authenticate.
---

# Update & publish Sarvam-CLI

Sarvam-CLI is a fork of [opencode](https://github.com/sst/opencode). The fork
keeps the Sarvam customizations (provider, default model `sarvam/sarvam-m`,
rebrand `opencode` → `sarvam-cli`, install script, README) as commits *on top of*
upstream, and carries them forward by **rebasing onto the latest upstream**.

The repo ships its own update tool at `script/update-sarvam.sh` that does the
heavy lifting (rebase → verify customizations survived → build → smoke-test).
This skill wraps that tool with the two things that aren't in the script: a
**git-repair preflight** and the **publish-to-GitHub** step.

The whole job is four phases. Do them in order, and stop to report if any phase
fails — never paper over a failure with a destructive shortcut.

## Phase 0 — Orient

Run from the repo root. Confirm the layout matches expectations before changing
anything:

```bash
git rev-parse --show-toplevel          # must be the Sarvam-CLI repo
git remote -v                          # expect: origin -> user's fork, upstream -> sst/opencode
git branch                             # note which branch is checked out
git status --short                     # note uncommitted work
```

The fork branch is whatever branch holds the Sarvam commits and tracks
`origin/main` — normally **`main`**. The update script defaults its fork branch
to `sarvam`, so always pass `SARVAM_FORK_BRANCH=main` (see Phase 2) to operate
directly on `main` and avoid a confusing extra branch.

## Phase 1 — Repair the git object store if needed

The local repo intermittently loses objects from upstream history, which makes
`git fetch upstream` fail with `pack has NNN unresolved deltas` /
`could not read <sha>`. This is the single most common blocker. Check first:

```bash
git fsck --full 2>&1 | grep -E 'missing|broken' || echo "object store OK"
```

If it reports a missing/broken commit, a normal incremental fetch can't fix it
(git tries to delta against the very object that's missing). Force a
**self-contained** fetch instead — `negotiationAlgorithm=noop` tells git not to
claim it already has objects, so the server sends a complete pack:

```bash
git -c fetch.negotiationAlgorithm=noop fetch upstream dev
git fsck --full 2>&1 | grep -E 'missing|broken' || echo "repaired"
```

Re-run `fsck`; it should be clean before continuing. Don't reclone or reset to
"fix" this — the repair above is non-destructive and preserves local work.

## Phase 2 — Update and rebuild

Let the repo's own tool do the rebase + verify + build. Operate on `main`:

```bash
SARVAM_FORK_BRANCH=main script/update-sarvam.sh
```

Useful flags (combine as needed):

- `--check` — report whether an update exists; change nothing. Good for a dry run.
- `--fast` — skip embedding the web UI (much faster build; no in-binary web UI).
- `--no-build` — update source only.
- `--install` — after a successful build, install to `~/.sarvam-cli/bin`.
- `--force` — rebuild even if already up to date.

What the script guarantees, so you don't have to re-verify it manually:

- It creates a backup branch (`<fork>-backup-<timestamp>`) before any rewrite.
- A rebase conflict is **aborted** and the tree restored — it never leaves a
  half-merged source tree. If this happens, surface the conflict to the user and
  resolve it deliberately; do not retry blindly.
- After rebasing it greps for required Sarvam "markers" (provider id, default
  model, rebrand, scriptName). If upstream refactored one away, the update
  **rolls back** instead of shipping a broken binary.
- It smoke-tests the fresh binary: `--version` runs and the zero-config default
  model resolves to `sarvam-m`.

The build is long (it compiles the web UI). If running it via a tool that
streams output, run it in the background and watch for the final
`Sarvam-CLI updated to opencode X.Y.Z` line plus the two green smoke-test checks.

When it finishes, the new binary path and backup branch name are printed. Keep
the backup branch until the user confirms they're happy.

## Phase 3 — Publish to the user's GitHub fork

The updated `main` history diverges from `origin/main` (it was rebased), so the
push is **non-fast-forward** and needs a force push. This is intentional for this
fork — but it is still a force-push to `main`, so confirm with the user before
doing it unless they've already asked you to push in this session.

### 3a. Make the ssh-agent reachable

GitHub auth here is SSH. The agent that holds the user's GitHub key often lives
in the user's interactive shell and is **not** visible to a fresh/sandboxed
shell (`SSH_AUTH_SOCK` empty, `ssh-add -l` says "Could not open a connection").
When executing Bash for the push, the user's agent socket may sit outside the
default tool sandbox — if your Bash tool supports it, disable the sandbox for the
push commands so they can reach the socket and the network.

Find the socket and confirm it has a key:

```bash
# 1. Does the current shell already see an agent?
ssh-add -l

# 2. If not, hunt for the socket (most-likely locations first):
ls -la ~/.ssh/agent/ 2>/dev/null              # custom agent dir (seen on this machine)
find /tmp -maxdepth 2 -type s -name 'agent.*' 2>/dev/null
ls -la /run/user/$(id -u)/ 2>/dev/null | grep -i ssh   # gpg-agent ssh socket, etc.

# 3. Point SSH at the socket you found and list its keys:
export SSH_AUTH_SOCK=<path-to-socket>
ssh-add -l
```

If `ssh-add -l` says **"The agent has no identities"**, the agent is running but
the key isn't loaded — ask the user to `ssh-add <their-github-key>` and re-check.
Do **not** silently substitute a different key (e.g. `~/.ssh/id_ed25519`); on
this machine that key is *not* registered with GitHub and will be rejected.

Verify GitHub actually accepts the key before pushing:

```bash
ssh -o BatchMode=yes -T git@github.com   # success line: "Hi <user>! You've successfully authenticated"
```

### 3b. Push

```bash
SSH_AUTH_SOCK=<path-to-socket> git push --force-with-lease origin main
```

Prefer `--force-with-lease` over a bare `--force`: it still overwrites the
rebased branch but refuses if `origin/main` changed unexpectedly, which protects
against clobbering work pushed from elsewhere. Only fall back to `--force` if the
user explicitly asks.

A pre-push hook runs typechecks across the workspace — expect ~20s of `tsgo`
output before the ref update. The success line looks like:

```
 + <old>...<new> main -> main (forced update)
```

## Wrap up

Report: the opencode version it moved to, that the markers/smoke-test passed, the
new binary path, the backup branch name (with the one-liner to delete it once
happy: `git branch -D <backup-branch>`), and the push result. If `--install`
wasn't used, remind the user of the install command the script printed.
