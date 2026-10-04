# Day 09 — Namespaces

## Definition

A Namespace provides a logical scope for many Kubernetes resources.

Namespaces are useful for organization, environment separation, RBAC boundaries, and resource governance.

~~~text
Kubernetes Cluster
|
+-- dev
|    +-- Pods
|    +-- Services
|    +-- Deployments
|
+-- production
     +-- Pods
     +-- Services
     +-- Deployments
~~~

## Namespaced vs cluster-scoped

Namespaced examples:

- Pods
- Deployments
- Services
- ConfigMaps
- Secrets
- Jobs

Cluster-scoped examples:

- Nodes
- PersistentVolumes
- Namespaces
- ClusterRoles

## Lab 1 — Create

~~~bash
kubectl create namespace dev
kubectl create namespace production
kubectl get namespaces
~~~

## Lab 2 — Deploy into a namespace

~~~bash
kubectl create deployment web --image=nginx -n dev
kubectl get deployments -n dev
kubectl get pods -n dev
kubectl get pods
kubectl get pods -A
~~~

## Lab 3 — Set default namespace

~~~bash
kubectl config set-context --current --namespace=dev
kubectl get pods
~~~

## Lab 4 — Cleanup

~~~bash
kubectl delete namespace dev
~~~

Be careful: deleting a Namespace deletes the namespaced resources inside it.

### Challenge

Create dev, test, and production. Deploy an application with the same name in each and verify that the resources can coexist.

## Key takeaway

A Namespace is a logical scope, not a complete security boundary. Use RBAC and other controls for security.
