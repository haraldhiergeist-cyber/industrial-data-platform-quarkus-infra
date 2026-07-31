# plc-industrial-ui

Angular frontend for the Quarkus services.

The production build points to:

- API/WebSocket: `https://plc-query-quarkus.cluster.lab`
- UI: `https://plc-ui-quarkus.cluster.lab`
- Keycloak client: `plc-industrial-ui`

The image tag in `overlays/dev/kustomization.yaml` is updated automatically by
the application GitLab pipeline.
