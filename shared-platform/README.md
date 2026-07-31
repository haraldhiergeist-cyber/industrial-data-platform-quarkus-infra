# Shared platform

The Quarkus applications reuse the platform already deployed by the original
infrastructure repository. This repository therefore contains no duplicate
Kafka, Redis or Keycloak workloads.

Expected services:

| Component | Endpoint / namespace |
|---|---|
| Kafka | `industrial-kafka-kafka-bootstrap.kafka.svc.cluster.local:9092` |
| Redis | `redis.redis.svc.cluster.local:6379` |
| PostgreSQL | `postgres.cluster.lab:5432` |
| Keycloak | `https://keycloak.cluster.lab/realms/industrial` |

The existing Keycloak client `plc-industrial-ui` must allow the Quarkus UI
origin and redirect URI.
