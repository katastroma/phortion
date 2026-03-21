# Katastroma

Platform entry point Helm chart for the katastroma platform. Renders ArgoCD
Applications and repo credentials for platform infrastructure components.

## Bootstrap

Use [holkos](https://github.com/katastroma/holkos) to bootstrap the platform:

```sh
mise bootstrap
```

Holkos installs ArgoCD from the `argo-cd/` kustomization, then installs the
katastroma Helm chart with operator-provided values. ArgoCD takes over and
self-manages from there.

After bootstrap, parameter changes go through ArgoCD UI/CLI, not holkos.

## How It Works

Katastroma has two parts:

- `argo-cd/` — a kustomization containing upstream ArgoCD raw manifests,
  namespace, and config patches (e.g., `server.insecure`). Applied directly by
  holkos before the Helm chart so that ArgoCD CRDs exist.
- Helm chart (root) — renders repo-creds Secrets and ArgoCD Applications with
  sync waves for platform infrastructure.

## Teardown

Platform teardown is initiated by deleting the root Application. See
[pedalion](https://github.com/katastroma/pedalion) for the teardown mechanism.

## Values Flow

- `global:` values flow to all subcharts via Helm's global mechanism.
- `epibathra:` values are passed as `spec.source.helm.parameters` on the
  epibathra Application — ArgoCD uses them to render the epibathra chart.
- `repo:` values are used by katastroma's repo-creds templates.
- Sane platform defaults live in epibathra's `values.yaml` (repo URLs, DNS
  upstream, traefik config). Operator-specific values (domain, TLS, gateway
  config) are provided only by the operator at bootstrap.
- Katastroma's `values.yaml` contains empty passthrough structures for all
  templated values to prevent nil pointer errors when ArgoCD re-renders from git
  without operator values.
- `ignoreDifferences` on `/spec/source` for system and epibathra Applications
  prevents ArgoCD from overwriting operator-provided values with empty defaults.

## Secret Handling

Repo credentials (git + OCI) are katastroma's responsibility — they're ArgoCD
infrastructure, created as Secrets in the `argocd` namespace by katastroma's
templates. Operator provides the sensitive values (GitHub App private key, OCI
credentials) at bootstrap via the holkos values file.

TLS certificates flow through katastroma's helm parameters to the gateway chart.

`ignoreDifferences` on Secret data/stringData + `RespectIgnoreDifferences=true`
prevents ArgoCD from overwriting any secret values during sync.

## Design

- **Helm chart** — katastroma is a Helm chart so it can template operator values
  into Application specs and repo-creds Secrets. The `argo-cd/` kustomization is
  separate because ArgoCD installs from raw upstream manifests (not the Helm
  chart, which has incompatible label selectors).
- **Idempotent bootstrap** — ArgoCD kustomization uses `--server-side` with
  `--force-conflicts`. Helm uses `upgrade --install`.
- **Self-management** — after bootstrap, ArgoCD manages katastroma via the
  System Application. The bootstrap tooling is only needed once.
- **Parameter ownership** — after bootstrap, ArgoCD takes field ownership of
  Application helm parameters via server-side apply. This prevents accidental
  overwrites from holkos re-runs. Parameter changes go through ArgoCD.
