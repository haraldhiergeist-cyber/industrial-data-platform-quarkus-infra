# Namespace-scoped secrets

Do not commit real secret values.

## Harbor pull secret

Create a registry secret in the new namespace:

```bash
kubectl -n industrial-quarkus create secret docker-registry harbor-regcred \
  --docker-server=harbor.cluster.lab \
  --docker-username='<robot-user>' \
  --docker-password='<robot-token>'
```

## TLS secret

Kubernetes secrets cannot be referenced across namespaces. Copy the existing
wildcard certificate from the Spring namespace:

```bash
kubectl get secret cluster-lab-tls -n industrial -o json \
  | jq 'del(.metadata.uid,.metadata.resourceVersion,.metadata.creationTimestamp,.metadata.managedFields) | .metadata.namespace="industrial-quarkus"' \
  | kubectl apply -f -
```

Alternatively, create a new TLS secret named `cluster-lab-tls` in
`industrial-quarkus`.

## PostgreSQL credentials

The deployments can read `username` and `password` from
`industrial-db-secret`. The references are optional so that the current
application defaults continue to work during the first test. For a proper
deployment, copy the template, replace the values and apply it locally:

```bash
cp industrial-db-secret.template.yaml industrial-db-secret.local.yaml
kubectl apply -f industrial-db-secret.local.yaml
```
