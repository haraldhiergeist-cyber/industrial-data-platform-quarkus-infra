# Changes from the Spring Boot infrastructure repository

- application namespace changed from `industrial` to `industrial-quarkus`
- Harbor project changed from `industrial` to `industrial-quarkus`
- public hosts changed to `plc-ui-quarkus.cluster.lab` and
  `plc-query-quarkus.cluster.lab`
- Spring environment variables replaced by Quarkus configuration variables
- Actuator probe paths replaced by Quarkus SmallRye Health paths
- Prometheus path changed from `/actuator/prometheus` to `/q/metrics`
- WebSocket ingress timeouts added for the Quarkus WebSockets Next connection
- Kafka, Redis and Keycloak remain shared and are not redeployed
- optional PostgreSQL credentials are read from `industrial-db-secret`
- image tags remain managed by the new GitLab pipeline
