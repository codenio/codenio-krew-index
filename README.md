# codenio-krew-index

Custom [Krew](https://krew.sigs.k8s.io/) plugin index for [codenio](https://github.com/codenio) plugins.

## Plugins

| Plugin   | Description                      |
| -------- | -------------------------------- |
| `audit`  | [kubectl-audit](https://github.com/codenio/kubectl-audit) — resource health audits |

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
kubectl krew upgrade codenio/audit
```

## Releasing a new `audit` version

1. Publish a release on [kubectl-audit](https://github.com/codenio/kubectl-audit) (artifacts + `audit_*_checksums.txt`).
2. Edit `plugins/audit.yaml`: set `spec.version`, each `uri` (tag in the path), and each `sha256` from the checksums file.
3. Optionally validate with [validate-krew-manifest](https://github.com/kubernetes-sigs/krew-index/blob/master/hack/validate-krew-manifest.md).
4. Commit and push; users run `kubectl krew upgrade codenio/audit`.

See also the Krew guide: [Hosting custom plugin indexes](https://krew.sigs.k8s.io/docs/developer-guide/custom-indexes/).
