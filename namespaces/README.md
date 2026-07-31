# Namespace

Apply the namespace before Fleet deploys the applications:

```bash
kubectl apply -f industrial-quarkus-namespace.yaml
```

The separate namespace allows the Spring Boot and Quarkus versions to run in
parallel without name collisions.
