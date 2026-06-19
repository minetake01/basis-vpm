# Minetake Basis VPM Repository

VPM hub for `net.minetake.basis.*` packages. Package zips are hosted on GitHub Releases; the repository index is served at:

**https://basis.minetake.net/vpm-repo.json**

Users must also add the [Basis official VPM repo](https://raw.githubusercontent.com/basisvr/Basis/vpm/vpm-repo.json) so `vpmDependencies` resolve.

See [SETUP.md](SETUP.md) for one-time GitHub, DNS, and first-release steps.

## Maintainer workflow

Publishing is controlled entirely from the **Basis** monorepo. This repository receives synced package files and runs **VPM Publish** automatically via `repository_dispatch`.

### Release a package (Basis repo only)

1. Work on a per-package branch (e.g. `feat/transparent-mirror`).
2. Bump `version` in `Packages/<package>/package.json` — this is the **only** version edit point.
3. Push the branch.
4. In the **Basis** repository → Actions → **Publish Minetake VPM** → Run workflow:
   - **Package**: select from dropdown
   - **Source branch**: `(workflow branch)` to use the branch selected in the Run workflow dropdown, or pick a named branch to override
5. The workflow syncs files here and triggers VPM publish automatically.

### Adding a new package

1. Add the folder name to `.github/minetake-vpm-packages.txt` in the Basis monorepo.
2. Add the package to the `package` choice list in `.github/workflows/publish-minetake-vpm.yml` (Basis repo).
3. Optionally add common development branches to the `source_branch` choice list in the same workflow.
4. Run **Publish Minetake VPM** from Basis.

## GitHub Pages and custom domain

The `vpm` branch is served via GitHub Pages at `basis.minetake.net`.

### One-time setup

1. **basis-vpm** → Settings → Pages
2. Source: **Deploy from a branch** → branch `vpm` / `/ (root)`
3. Custom domain: `basis.minetake.net`
4. DNS at your registrar:
   - `basis.minetake.net` → CNAME → `minetake.github.io` (adjust for your GitHub org/user)
5. Enable **Enforce HTTPS** after the certificate is issued
6. Verify: `curl -s https://basis.minetake.net/vpm-repo.json | head`

Zip downloads remain on `github.com` (standard VPM). Only the index JSON uses the custom domain.

## Secrets (Basis monorepo)

| Secret | Used by |
|---|---|
| `BASIS_VPM_SYNC_TOKEN` | Publish Minetake VPM — PAT with **contents: write** on this repo (sync push + `repository_dispatch`) |

## Local build test

```bash
python3 .github/scripts/vpmpackagegen.py build \
  --packages net.minetake.basis.transparent-mirror \
  --out out
```
