# One-time setup checklist

Complete these steps before the first VPM release.

## 1. Create GitHub repository

1. Create **minetake/basis-vpm** on GitHub (empty, no README/license — this workspace copy is the initial content).
2. Push the contents of [`basis-vpm/`](../basis-vpm/) from this workspace:

   ```sh
   cd basis-vpm
   git init
   git add .
   git commit -m "chore: initial VPM hub scaffolding"
   git remote add origin https://github.com/minetake/basis-vpm.git
   git branch -M main
   git push -u origin main
   ```

## 2. Basis monorepo secret

In the **Basis** fork (where `sync-minetake-vpm.yml` lives):

1. Settings → Secrets and variables → Actions
2. Add `BASIS_VPM_SYNC_TOKEN` — fine-grained PAT or classic token with **contents: write** on `minetake/basis-vpm`

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

## 4. First release

### Sync (Basis repo → Actions → Sync Minetake VPM)

| Input | Value |
|---|---|
| `source_branch` | your package branch (e.g. `feat/transparent-mirror`) |
| `package` | `net.minetake.basis.transparent-mirror` |

### Publish (basis-vpm → Actions → VPM Publish)

| Input | Value |
|---|---|
| `packages` | `net.minetake.basis.transparent-mirror` |
| `stable` | `true` |
| `tag_override` | `v1.0.0` |

### Verify

- Release asset: `net.minetake.basis.transparent-mirror-1.0.0.zip`
- `vpm` branch contains `vpm-repo.json` and `CNAME`
- `https://basis.minetake.net/vpm-repo.json` lists the package at version `1.0.0`
