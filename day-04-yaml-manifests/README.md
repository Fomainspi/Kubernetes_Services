# Day 04 — YAML & Kubernetes Manifests

## Definition

A Kubernetes manifest is a YAML or JSON description of the desired state of an object.

The four fields you must recognize are:

~~~yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
~~~

| Field | Meaning |
|---|---|
| apiVersion | API group and version |
| kind | Kubernetes object type |
| metadata | Name, namespace, labels, annotations |
| spec | Desired configuration |

## Declarative model

~~~text
You declare desired state
        |
        v
Kubernetes controllers
        |
        v
Actual cluster state
~~~

## Lab — Deployment manifest

Create deployment.yaml:

~~~yaml
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
          image: nginx:1.27
          ports:
            - containerPort: 80
~~~

Apply:

~~~bash
kubectl apply -f deployment.yaml
kubectl get deployment
kubectl get pods
~~~

## Inspect the API schema

~~~bash
kubectl explain deployment
kubectl explain deployment.spec
kubectl explain deployment.spec.template.spec.containers
~~~

## Validate

~~~bash
kubectl diff -f deployment.yaml
kubectl apply --dry-run=client -f deployment.yaml
~~~

## Lab — Modify

Change replicas from 3 to 5, then:

~~~bash
kubectl apply -f deployment.yaml
kubectl get pods
~~~

## Common mistakes

- Incorrect indentation
- Wrong apiVersion
- Wrong kind
- Selector does not match labels
- Invalid field names
- Incorrect image
- Missing required fields

### Challenge

Create a Deployment with three replicas and a Service. Change the image version and observe the rollout.

## Key takeaway

YAML describes desired state. Kubernetes continuously works toward that state.
