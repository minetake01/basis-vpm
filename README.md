# Minetake Basis VPM Repository

VPM hub for `net.minetake.basis.*` packages. Package zips are hosted on GitHub Releases; the repository index is served at:

**https://basis.minetake.net/vpm-repo.json**

Users must also add the [Basis official VPM repo](https://raw.githubusercontent.com/basisvr/Basis/vpm/vpm-repo.json) so `vpmDependencies` resolve.

See [SETUP.md](SETUP.md) for one-time GitHub, DNS, and first-release steps.

## Maintainer workflow

Publishing is **manual only** — no push or cron triggers.

### 1. Develop in the Basis monorepo

Work on a per-package branch (e.g. `feat/transparent-mirror`). Bump `version` in `package.json` before publishing.

### 2. Sync (staging)

In the **Basis** repository, run **Sync Minetake VPM** (`workflow_dispatch`):

| Input | Example |
|---|---|
| `source_branch` | `feat/transparent-mirror` |
| `package` | `net.minetake.basis.transparent-mirror` |

This copies `Basis/Packages/<package>/` into this repo's `Packages/<package>/` on `main`. Nothing is published to VPM yet.

Review the staged commit on `main` before continuing.

### 3. Publish (release)

In **this** repository, run **VPM Publish** (`workflow_dispatch`):

| Input | Example |
|---|---|
| `packages` | `net.minetake.basis.transparent-mirror` |
| `stable` | `true` |
| `tag_override` | `v1.0.0` |

This builds zips, creates a GitHub Release, and updates `vpm-repo.json` on the `vpm` branch.

### Adding a new package

1. Add the folder name to [`.github/vpm-packages.txt`](.github/vpm-packages.txt).
2. Sync from the Basis monorepo (step 2 above).
3. Publish (step 3 above).

## GitHub Pages and custom domain

The `vpm` branch is served via GitHub Pages at `basis.minetake.net`.

### One-time setup

1. **basis-vpm** → Settings → Pages
2. Source: **Deploy from a branch** → branch `vpm` / `/ (root)`
3. Custom domain: `basis.minetake.net`
4. DNS at your registrar:
   - `basis.minetake.net` → CNAME → `minetake.github.io` (if user/org Pages)
   - Or use the A records GitHub documents for apex domains
5. Enable **Enforce HTTPS** after the certificate is issued
6. Verify: `curl -s https://basis.minetake.net/vpm-repo.json | head`

Zip downloads remain on `github.com` (standard VPM). Only the index JSON uses the custom domain.

## Secrets (Basis monorepo)

| Secret | Used by |
|---|---|
| `BASIS_VPM_SYNC_TOKEN` | Sync Minetake VPM — PAT with write access to this repo |

## Local build test

```bash
python3 .github/scripts/vpmpackagegen.py build \
  --packages net.minetake.basis.transparent-mirror \
  --out out
```
