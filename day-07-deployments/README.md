# Day 07 — Deployments

## Definition

A Deployment manages ReplicaSets and provides controlled rollout and rollback for stateless applications.

~~~text
Deployment
    |
    +-- ReplicaSet v1 -> Pods v1
    |
    +-- ReplicaSet v2 -> Pods v2
~~~

## Why Deployments?

- Replica management
- Rolling updates
- Rollback
- Scaling
- Revision history
- Declarative application management

## Lab 1 — Create

~~~bash
kubectl create deployment web --image=nginx:1.27
kubectl get deployment
kubectl get rs
kubectl get pods
kubectl scale deployment web --replicas=3
~~~

## Lab 2 — Rolling update

~~~bash
kubectl set image deployment/web nginx=nginx:1.28
kubectl rollout status deployment/web
kubectl rollout history deployment/web
kubectl get rs
~~~

## Lab 3 — Rollback

~~~bash
kubectl rollout undo deployment/web
kubectl rollout status deployment/web
~~~

## Deployment strategy

The default Deployment strategy is RollingUpdate.

~~~text
Old Pods:  ██████
New Pods:  ██

Old Pods:  ███
New Pods:  █████

Old Pods:
New Pods:  ██████
~~~

## Troubleshooting

~~~bash
kubectl describe deployment web
kubectl get rs
kubectl get pods
kubectl get events --sort-by=.lastTimestamp
~~~

Common rollout failures include bad images, failing readiness probes, insufficient resources, and invalid configuration.

### Challenge

Deploy three NGINX replicas, upgrade the image, deliberately use a bad image, inspect the failure, and roll back.

## Key takeaway

Deployment is the standard Kubernetes object for managing stateless application releases.
