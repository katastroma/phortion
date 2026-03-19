# Epibathra

Platform control plane for [katastroma](https://github.com/katastroma).
Provisions and manages tenants, their namespaces, and the Applications and
RepoCredentials within them. Provides tenant isolation, self-service onboarding,
and managed deployments.

Packaged as a Helm chart. Phortion charts are consumed as Helm dependencies.

## What It Does

Epibathra is responsible for:

- **Tenant lifecycle** — provisioning tenants and their namespaces, creating
  Application and RepoCredential resources on their behalf. CRD types are
  defined in [tropis](https://github.com/katastroma/tropis).
  [Pedalion](https://github.com/katastroma/pedalion) reconciles them.
- **Namespace hierarchy** — managing parent-child relationships between tenant
  namespaces. Kubernetes namespaces are flat; epibathra enforces the hierarchy.
- **Tenant isolation** — enforcing that tenants can only operate within their
  boundaries at the cluster level.
- **Tenant self-service** — API and UI for tenants to manage their own
  registrations.
- **Platform identity** — authenticating tenant accounts and providing the
  identity backbone for policy enforcement.

## Components

### Off-the-shelf (deploy and configure)

These use existing solutions — no custom development required.

- **Auth/IdP** — tenant authentication and identity backbone for policy
  enforcement. OOB solution (e.g. Keycloak, Dex). Specific solution TBD.
- **Gatekeeper** — cluster-wide admission and policy enforcement using the IdP
  for tenant boundary decisions. OOB solution (e.g. OPA/Gatekeeper, Kyverno).
  Specific solution TBD. Should integrate well with whichever IdP is chosen.

### Custom (requires development)

- **[Grammateus](https://github.com/katastroma/grammateus)** — API server.
  Manages tenant namespaces and attaches Applications and RepoCredentials to
  them. Receives webhooks from tenant repositories.
- **[Prora](https://github.com/katastroma/prora)** — tenant self-service
  frontend. Consumes the grammateus API.

## Tenant Credentials Model

GitHub App model for repo credentials: the platform owns the GitHub App, tenants
install it on their repos and provide the installation ID at registration.

## Rollout Order

OOB components first, then custom:

1. Auth/IdP
2. Gatekeeper
3. API Server
4. Frontend
