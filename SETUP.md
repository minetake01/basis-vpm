# One-time setup checklist

Complete these steps before the first VPM release.

## 1. Create GitHub repository

1. Create **minetake01/basis-vpm** on GitHub (empty, no README/license — this workspace copy is the initial content).
2. Push the contents of [`basis-vpm/`](../basis-vpm/) from this workspace:

   ```sh
   cd basis-vpm
   git init
   git add .
   git commit -m "chore: initial VPM hub scaffolding"
   git remote add origin https://github.com/minetake01/basis-vpm.git
   git branch -M main
   git push -u origin main
   ```

## 2. Basis monorepo secret

In the **Basis** fork (where `publish-minetake-vpm.yml` lives):

1. Settings → Secrets and variables → Actions
2. Add `BASIS_VPM_SYNC_TOKEN` — fine-grained PAT or classic token with **contents: write** on `minetake01/basis-vpm` (required for git push and `repository_dispatch`)

## 3. DNS and GitHub Pages

1. **basis-vpm** → Settings → Pages
2. Source: branch **`vpm`** / root
3. Custom domain: **`basis.minetake.net`**
4. At your DNS provider, add:
   - **CNAME** `basis.minetake.net` → `minetake.github.io` (adjust if using an org account)
5. Wait for TLS certificate; enable **Enforce HTTPS**
6. The publish workflow writes `CNAME` on the `vpm` branch automatically

Verify after first publish:

```sh
curl -sI https://basis.minetake.net/vpm-repo.json
```

## 4. First release (Basis repo only)

1. Bump `version` in `Basis/Packages/net.minetake.basis.transparent-mirror/package.json` (e.g. `1.0.0`).
2. Push your package branch.
3. **Basis** repo → Actions → **Publish Minetake VPM**:

| Input | Value |
|---|---|
| `package` | `net.minetake.basis.transparent-mirror` |
| `source_branch` | `(workflow branch)` — select your package branch in the Run workflow dropdown |

The workflow syncs to basis-vpm and automatically triggers VPM publish.

### Verify

- **basis-vpm** Actions → **VPM Publish** completed successfully
- Release asset: `net.minetake.basis.transparent-mirror-1.0.0.zip`
- `vpm` branch contains `vpm-repo.json` and `CNAME`
- `https://basis.minetake.net/vpm-repo.json` lists the package at version `1.0.0`

## Adding another package later

1. Add folder name to `Basis/.github/minetake-vpm-packages.txt`
2. Add option to `package` choice list in `Basis/.github/workflows/publish-minetake-vpm.yml`
3. Optionally add branch names to `source_branch` choice list
4. Run **Publish Minetake VPM** from Basis
