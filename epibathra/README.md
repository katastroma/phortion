# Epibathra

Helm chart that composes the tenant management stack for
[katastroma](https://github.com/katastroma).

## Components

### Off-the-shelf

- **Auth/IdP** — tenant authentication and identity backbone for policy
  enforcement. Specific solution TBD.
- **Gatekeeper** — cluster-wide admission control for tenant isolation,
  namespace prefix enforcement, and cluster-scoped resource restriction.
  Specific solution TBD.

### Custom

- **[Grammateus](https://github.com/katastroma/grammateus)** — tenant management
  API server. Manages namespaces, hierarchy, service accounts, credentials, and
  resource queries.
- **[Prora](https://github.com/katastroma/prora)** — tenant self-service
  frontend. Consumes the grammateus API.
