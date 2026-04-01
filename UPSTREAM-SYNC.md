# Upstream Sync Workflow

This repository is a fork of `jurialmunkey/plugin.video.themoviedb.helper`.

## Branch Strategy

- `nexus`: mirror branch for upstream `nexus` (do not commit custom changes here)
- `mildman/headless-libsync`: custom branch for headless Docker Kodi library sync fixes

## One-Time Setup

```bash
git remote add upstream https://github.com/jurialmunkey/plugin.video.themoviedb.helper.git
git fetch upstream --prune
```

## Regular Upstream Update

Run these commands from this repository root:

```bash
# 1) Update local refs
git fetch upstream --prune
git fetch origin --prune

# 2) Keep mirror branch aligned with upstream
git checkout nexus
git reset --hard upstream/nexus
git push --force-with-lease origin nexus

# 3) Replay custom changes on top of latest upstream
git checkout mildman/headless-libsync
git rebase nexus

# 4) Push updated custom branch
git push --force-with-lease origin mildman/headless-libsync
```

If a rebase conflict happens:

```bash
git status
# resolve files
git add <resolved-files>
git rebase --continue
```

Abort rebase if needed:

```bash
git rebase --abort
```

## Release/Pipeline Recommendation

- Build release ZIPs from `mildman/headless-libsync`.
- Keep the addon id unchanged only if this fork is intended to replace upstream installation directly.
- Use a fork-specific version suffix strategy (example: `x.y.z.9001`) so your releases are clearly distinguishable.

## Publish to `mildman1848.github.io`

After creating a new release ZIP from this fork:

1. Copy ZIP and metadata artifacts into the Kodi repo structure in `mildman1848.github.io/repo/`.
2. Regenerate/update `addons.xml` and checksum files.
3. Commit and push changes in the `mildman1848.github.io` repository.

## Safety Rules

- Never develop directly on `nexus`.
- Keep custom patches focused and atomic to reduce future rebase conflicts.
- Prefer small, well-labeled commits for headless service fixes.
