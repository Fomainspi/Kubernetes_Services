# Day 05 — Labels & Selectors

## Labels

A label is a key/value pair attached to a Kubernetes object.

Example:

~~~yaml
labels:
  app: web
  environment: production
  tier: frontend
~~~

Labels organize and identify resources.

## Selectors

A selector finds objects by their labels.

~~~text
Pods
 +-- app=web
 +-- app=api
 +-- app=web

selector: app=web
        |
        +-- Pod 1
        +-- Pod 3
~~~

## Lab

~~~bash
kubectl get pods --show-labels
kubectl get pods -l app=web
kubectl label pod POD_NAME environment=dev
kubectl get pods -l environment=dev
kubectl label pod POD_NAME environment-
~~~

## Services use selectors

~~~text
Client
  |
  v
Service
  |
  | selector: app=web
  v
+---------+---------+
| Pod     | Pod     |
| app=web | app=web |
+---------+---------+
~~~

If a Service has no endpoints, check labels and selectors first.

~~~bash
kubectl describe svc SERVICE
kubectl get pods --show-labels
kubectl get endpoints
kubectl get endpointslices
~~~

### Challenge

Create two web Pods and one API Pod. Use selectors to list only the web Pods.

## Key takeaway

Labels describe objects. Selectors find objects.
