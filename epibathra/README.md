# Epibathra

Helm chart that composes the tenant management stack for
[katastroma](https://github.com/katastroma). Deploys
[grammateus](https://github.com/katastroma/grammateus),
[prora](https://github.com/katastroma/prora), and off-the-shelf components as
dependencies.

## Components

### Off-the-shelf (deploy and configure)

These use existing solutions — no custom development required.

- **Auth/IdP** — tenant authentication and identity backbone for policy
  enforcement. OOB solution (e.g. Keycloak, Dex). Specific solution TBD.
- **Gatekeeper** — cluster-wide admission and policy enforcement using the IdP
  for tenant boundary decisions. OOB solution (e.g. OPA/Gatekeeper, Kyverno).
  Specific solution TBD. Should integrate well with whichever IdP is chosen.

### Custom

- **[Grammateus](https://github.com/katastroma/grammateus)** — API server.
  Manages tenant namespaces and attaches Applications and RepoCredentials to
  them. Receives webhooks from tenant repositories.
- **[Prora](https://github.com/katastroma/prora)** — tenant self-service
  frontend. Consumes the grammateus API.

## Tenant Credentials Model

GitHub App model for repo credentials: the platform owns the GitHub App, tenants
install it on their repos and provide the installation ID at registration.

## Rollout Order

Off-the-shelf components first, then custom:

1. Auth/IdP
2. Gatekeeper
3. Grammateus
4. Prora
