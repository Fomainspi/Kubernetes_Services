# Day 08 — Services

## Definition

A Service provides a stable network endpoint for a changing group of Pods.

~~~text
             Service
                |
       +--------+--------+
       v        v        v
     Pod 1    Pod 2    Pod 3
~~~

![Kubernetes Service](../images/kubernetes-service.svg)

## Service types

| Type | Purpose |
|---|---|
| ClusterIP | Internal cluster access; default |
| NodePort | Exposes a port on each Node |
| LoadBalancer | Requests an external/cloud load balancer |
| ExternalName | Maps a Service name to an external DNS name |

## port vs targetPort

~~~text
Client -> Service :80 -> Pod :8080

Service port = 80
Pod targetPort = 8080
~~~

## Lab

~~~bash
kubectl create deployment web --image=nginx
kubectl scale deployment web --replicas=3
kubectl expose deployment web --port=80 --target-port=80
kubectl get svc
kubectl describe svc web
kubectl get endpoints
kubectl get endpointslices
kubectl port-forward svc/web 8080:80
~~~

Open http://localhost:8080.

## Service discovery

Inside the cluster, Services can be reached through DNS.

~~~text
service.namespace.svc.cluster.local
~~~

Example:

~~~text
web.default.svc.cluster.local
~~~

## Troubleshooting

~~~bash
kubectl describe svc web
kubectl get pods --show-labels
kubectl get endpoints
kubectl get endpointslices
~~~

A very common problem is a Service selector that does not match Pod labels.

### Challenge

Create a Deployment with three replicas, expose it with ClusterIP, verify endpoints, and access it with port-forwarding.

## Key takeaway

A Service decouples clients from changing Pod IP addresses.
