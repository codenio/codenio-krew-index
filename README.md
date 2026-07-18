# codenio-krew-index

Custom [Krew](https://krew.sigs.k8s.io/) plugin index for [codenio](https://github.com/codenio) plugins.

## Plugins


| Plugin  | Description                                                                        |
| ------- | ---------------------------------------------------------------------------------- |
| `audit` | [kubectl-audit](https://github.com/codenio/kubectl-audit) — resource health audits |


## Use this index

After you create the GitHub repository and push this tree:

```bash
kubectl krew index add codenio https://github.com/codenio/codenio-krew-index.git
kubectl krew install codenio/audit
```

Use another local name if `codenio` is already taken:

```bash
kubectl krew index add codenio-krew https://github.com/codenio/codenio-krew-index.git
kubectl krew install codenio-krew/audit
```

Update the plugin later:

```bash
kubectl krew upgrade audit
```

## Releasing a new `audit` version

1. Tag and push a release on [kubectl-audit](https://github.com/codenio/kubectl-audit) (`v*.*.*`).
2. The release workflow uploads `audit-release.yaml` and triggers **Sync kubectl-audit manifest** in this repo (see [Automation](#automation) below).
3. If automation is not configured, run **Actions → Sync kubectl-audit manifest → Run workflow** manually.
4. Users upgrade with `kubectl krew upgrade audit`.

See also the Krew guide: [Hosting custom plugin indexes](https://krew.sigs.k8s.io/docs/developer-guide/custom-indexes/).

## Automation

When `kubectl-audit` publishes a release, it sends a `repository_dispatch` event (`sync-audit`) to this repository. The workflow downloads the latest `audit-release.yaml` and commits `plugins/audit.yaml` if it changed.

### One-time setup: `KREW_INDEX_SYNC_PAT` on kubectl-audit

Create a fine-grained personal access token and add it as a **repository secret** on `codenio/kubectl-audit`:

| Setting | Value |
| ------- | ----- |
| Secret name | `KREW_INDEX_SYNC_PAT` |
| Used by | `.github/workflows/release.yml` |

#### Fine-grained PAT (recommended)

1. Open [GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens](https://github.com/settings/personal-access-tokens/new).
2. **Token name:** `kubectl-audit-krew-index-sync` (or similar).
3. **Expiration:** choose a rotation period (e.g. 90 days or 1 year).
4. **Resource owner:** your user or the `codenio` org (if org-owned token is available).
5. **Repository access:** **Only select repositories** → `codenio/codenio-krew-index`.
6. **Permissions → Repository permissions:**
   - **Contents:** Read and write (workflow commits `plugins/audit.yaml`)
   - **Metadata:** Read-only (required)
   - **Actions:** Read and write (allows `repository_dispatch` to start workflows)
7. Generate the token and copy it once.
8. In **codenio/kubectl-audit** → **Settings → Secrets and variables → Actions → New repository secret**:
   - Name: `KREW_INDEX_SYNC_PAT`
   - Value: paste the token

#### Classic PAT (alternative)

1. [Settings → Developer settings → Personal access tokens → Tokens (classic)](https://github.com/settings/tokens/new).
2. Scopes: **`repo`** (full control of private repositories; for public repos this still grants dispatch + push on allowed repos).
3. Add the token as `KREW_INDEX_SYNC_PAT` on `codenio/kubectl-audit` as above.

#### Verify

After the secret is set, the next `kubectl-audit` tag release should show **Trigger Krew index sync** in the release workflow, then **Sync audit · vX.Y.Z** under Actions in this repo.

Manual fallback: **Actions → Sync kubectl-audit manifest → Run workflow**.