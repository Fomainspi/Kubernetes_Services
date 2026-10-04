# Day 06 — ReplicaSets

## Definition

A ReplicaSet maintains a desired number of matching Pods.

~~~text
ReplicaSet
    |
    +-- Pod 1
    +-- Pod 2
    +-- Pod 3
~~~

If the desired number is three and one Pod disappears, the ReplicaSet creates a replacement.

## Lab

Create replicaset.yaml:

~~~yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: web-rs
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
          image: nginx:1.27
~~~

Apply:

~~~bash
kubectl apply -f replicaset.yaml
kubectl get rs
kubectl get pods
~~~

## Self-healing lab

Delete one Pod:

~~~bash
kubectl delete pod POD_NAME
kubectl get pods -w
~~~

A replacement should appear.

## Scale

~~~bash
kubectl scale rs web-rs --replicas=5
kubectl get rs
kubectl get pods
~~~

## ReplicaSet vs Deployment

| ReplicaSet | Deployment |
|---|---|
| Maintains Pods | Manages ReplicaSets |
| Basic self-healing | Rolling updates |
| Basic scaling | Rollback and revision history |
| Rarely used directly | Standard for stateless applications |

### Challenge

Scale to five replicas, delete two Pods, and observe self-healing.

## Key takeaway

A ReplicaSet maintains the desired number of matching Pods. Deployments normally manage ReplicaSets for you.
