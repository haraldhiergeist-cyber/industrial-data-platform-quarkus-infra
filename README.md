# Industrial Data Platform – Quarkus Infrastructure

Kubernetes and Fleet GitOps configuration for the Quarkus implementation of the
Industrial Data Platform.

This repository deploys the Quarkus application services in a separate
`industrial-quarkus` namespace while reusing the existing shared platform:

- Strimzi Kafka in namespace `kafka`
- Redis in namespace `redis`
- external PostgreSQL at `postgres.lab.harald-hiergeist.de`
- Keycloak at `https://keycloak.lab.harald-hiergeist.de`
- ingress-nginx and the existing `*.harald-hiergeist.de` TLS wildcard certificate

The Spring Boot deployment remains untouched in namespace `industrial`.

## Runtime architecture

```text
PLC / simulator
      |
      v
plc-collector-service (Quarkus + PLC4X)
      |
      v
Kafka topic: plc.raw.readings
      |-------------------------------|
      v                               v
plc-persist-service              plc-query-service
(Quarkus + Hibernate ORM)        (Quarkus + Redis + WebSocket)
      |                               |            |
      v                               v            v
PostgreSQL                         Redis      Angular UI
```

## Repository structure

```text
industrial-data-platform-quarkus-infra/
├── api/
│   ├── plc-collector-service/
│   ├── plc-persist-service/
│   └── plc-query-service/
├── web/
│   └── plc-industrial-ui/
├── database/
│   ├── database.yaml
│   └── role.yaml
├── namespaces/
├── secrets/
└── shared-platform/
```

Every application contains a reusable Kustomize `base`, a `dev` overlay and a
Fleet `fleet.yaml`. The GitLab pipeline in the application repository updates
the image tag in the matching overlay.


Database provisioning

The database/ directory contains declarative CloudNativePG resources for the Industrial Data Platform.
- role.yaml manages the PostgreSQL role plcuser.
- database.yaml provisions the plcdb database in the existing platform-postgres cluster.
Database credentials are provided through Kubernetes Secrets and are not stored in this repository.

## Hosts

- UI: `https://plc-ui-quarkus.lab.harald-hiergeist.de`
- Query API and WebSocket: `https://plc-query-quarkus.lab.harald-hiergeist.de`
- OpenAPI: `https://plc-query-quarkus.lab.harald-hiergeist.de/v3/api-docs`
- Swagger UI: `https://plc-query-quarkus.lab.harald-hiergeist.de/swagger-ui`

## One-time preparation

1. Create the Harbor project `industrial-quarkus` and grant the GitLab robot
   account push permission.
2. Create the application namespace:

   ```bash
   kubectl apply -f namespaces/industrial-quarkus-namespace.yaml
   ```

3. Create or copy the Harbor pull secret and TLS secret as described in
   `secrets/README.md`.
4. Optionally create `industrial-db-secret` from the provided template.
5. Add `https://plc-ui-quarkus.cluster.lab/*` as a valid redirect URI and
   `https://plc-ui-quarkus.cluster.lab` as a web origin for the existing
   Keycloak client `plc-industrial-ui`.
6. Configure Fleet GitRepo paths for the four directories containing
   `fleet.yaml`.

## GitLab integration

The application pipeline expects this repository at:

```text
gitlab.cluster.lab/harald/industrial-data-platform-quarkus-infra.git
```

It updates these files:

```text
api/plc-collector-service/overlays/dev/kustomization.yaml
api/plc-persist-service/overlays/dev/kustomization.yaml
api/plc-query-service/overlays/dev/kustomization.yaml
web/plc-industrial-ui/overlays/dev/kustomization.yaml
```

Required GitLab CI/CD variables:

- `HARBOR_USERNAME`
- `HARBOR_PASSWORD`
- `INFRA_REPO_TOKEN`

## Quarkus endpoints used by Kubernetes

- startup: `/q/health/started`
- readiness: `/q/health/ready`
- liveness: `/q/health/live`
- metrics: `/q/metrics`

## Shared platform ownership

Kafka, Redis and Keycloak are deliberately not deployed a second time from this
repository. They remain owned by the original infrastructure repository. This
prevents two GitOps repositories from managing the same Kubernetes resources.
