# Platform State – Quarkus deployment

## Application namespace

- `industrial-quarkus`

## Shared services

- Kafka bootstrap: `industrial-kafka-kafka-bootstrap.kafka.svc.cluster.local:9092`
- Kafka topic: `plc.raw.readings`
- Redis: `redis://redis.redis.svc.cluster.local:6379`
- PostgreSQL: `jdbc:postgresql://postgres.cluster.lab:5432/plcdb`
- Keycloak issuer: `https://keycloak.cluster.lab/realms/industrial`

## Public hosts

- `plc-ui-quarkus.cluster.lab`
- `plc-query-quarkus.cluster.lab`

## Container registry

- `harbor.cluster.lab/industrial-quarkus/plc-collector-service`
- `harbor.cluster.lab/industrial-quarkus/plc-persist-service`
- `harbor.cluster.lab/industrial-quarkus/plc-query-service`
- `harbor.cluster.lab/industrial-quarkus/plc-industrial-ui`

## Required namespace-scoped secrets

- `harbor-regcred`
- `cluster-lab-tls`
- optional: `industrial-db-secret`

## Deployment model

- Fleet + Kustomize
- one Fleet bundle per application
- image tags updated by the Quarkus application GitLab pipeline
- Spring Boot and Quarkus variants can run in parallel
