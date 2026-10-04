# Day 03 — Pods

## Definition

A Pod is the smallest deployable unit in Kubernetes. It represents one or more containers that are scheduled together on the same Node.

### Mental model

~~~text
Node
 |
 +-- Pod
      +-- Application container
      +-- Optional sidecar
      +-- Shared network namespace
      +-- Shared volumes
~~~

Containers in the same Pod share the Pod network namespace and can communicate through localhost. Pods are normally ephemeral and are created by controllers such as Deployments, StatefulSets, DaemonSets, and Jobs.

## Pod lifecycle

~~~text
Pending -> Running -> Succeeded
              |
              +----> Failed
~~~

## Lab 1 — Create a Pod

~~~bash
kubectl run nginx-pod --image=nginx
kubectl get pods
kubectl get pod nginx-pod -o wide
kubectl describe pod nginx-pod
~~~

## Lab 2 — Access the application

~~~bash
kubectl port-forward pod/nginx-pod 8080:80
~~~

Open http://localhost:8080.

## Lab 3 — Execute commands

~~~bash
kubectl exec -it nginx-pod -- /bin/bash
hostname
cat /etc/os-release
exit
~~~

## Lab 4 — Logs

~~~bash
kubectl logs nginx-pod
kubectl logs -f nginx-pod
~~~

## Troubleshooting

~~~bash
kubectl get pod
kubectl describe pod nginx-pod
kubectl logs nginx-pod
kubectl logs nginx-pod --previous
kubectl get events --sort-by=.lastTimestamp
~~~

### Challenge

Create an NGINX Pod, access it with port-forwarding, inspect its IP and Node, view its logs, delete it, and explain why it does not automatically come back.

## Key takeaway

Pods run containers. Controllers normally create and replace Pods so applications remain at the desired state.
