# Day 04 — YAML & Kubernetes Manifests 📄

## Definition

A **manifest** is a YAML or JSON representation of a Kubernetes API object.

The four fields you will constantly see are:

```yaml
apiVersion:
kind:
metadata:
spec:
```

| Field | Definition |
|---|---|
| apiVersion | API group and version |
| kind | Type of resource |
| metadata | Identity, labels, namespace, annotations |
| spec | Desired configuration |

## Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

## Labs

```bash
kubectl apply -f deployment.yaml
kubectl get deployment web -o yaml
kubectl explain deployment.spec
kubectl explain pod.spec.containers
kubectl diff -f deployment.yaml
```

### Challenge

Modify the manifest to run 5 replicas, add an environment variable, add a label, and expose container port 80.
