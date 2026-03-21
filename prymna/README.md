# Prymna

Helm chart that deploys the GitOps engine for
[katastroma](https://github.com/katastroma).

## Components

- **[Pharos](https://github.com/katastroma/pharos)** — webhook server. Receives
  git push events and orchestrates the pipeline.
- **[Phortizo](https://github.com/katastroma/phortizo)** — retriever. Fetches
  source from repositories.
- **[Orpheus](https://github.com/katastroma/orpheus)** — renderer. Renders
  manifests from source content.
- **[Histia](https://github.com/katastroma/histia)** — provisioner. Applies
  manifests to the cluster via impersonation. Only component requiring cluster
  RBAC (`impersonate` verb).
