# Day 08 — Kubernetes Services

> **Level:** Beginner → Intermediate  
> **Module:** Kubernetes Networking  
> **Goal:** Understand how Kubernetes provides stable network access to Pods even though Pods are temporary and their IP addresses can change.

---

## 1. What is a Kubernetes Service?

A **Service** is a Kubernetes API object that provides a stable network endpoint for a group of Pods.

Pods are ephemeral:

- Pods can be deleted and recreated.
- Deployments replace Pods during updates.
- Pods can move between Nodes.
- Pod IP addresses can change.

Applications should therefore not depend directly on Pod IP addresses.

### Without a Service

    Pod A → 10.244.1.10
    Pod B → 10.244.1.11
    Pod C → 10.244.2.15

If Pod B is deleted and recreated:

    Pod A → 10.244.1.10
    Pod C → 10.244.2.15
    Pod D → 10.244.3.20

The client should not have to discover these changing addresses.

### With a Service

    Client
       |
       v
    web-service
       |
       +--------+--------+
       |        |        |
       v        v        v
     Pod A    Pod C    Pod D

The client communicates with the Service, while Kubernetes manages the changing backend Pods.

![Kubernetes Service](../images/kubernetes-service.svg)

---

## 2. Why do we need Services?

A Service provides:

1. **Stable networking** — a stable virtual endpoint.
2. **Service discovery** — applications can find Services through DNS.
3. **Traffic distribution** — traffic can be sent to eligible backend Pods.
4. **Decoupling** — clients do not need to know Pod IP addresses.
5. **Application communication** — Services are fundamental to microservice communication.

Typical architecture:

    Frontend
       |
       v
    frontend-service
       |
       v
    Backend Pods
       |
       v
    database-service
       |
       v
    Database Pods

---

## 3. How a Service finds Pods

Services normally use **label selectors**.

Pod:

    metadata:
      labels:
        app: web

Service:

    selector:
      app: web

The Service selects Pods whose labels match its selector.

    Service
       |
       | selector: app=web
       |
       +----------+----------+
       |          |          |
       v          v          v
     Pod 1      Pod 2      Pod 3
     app=web    app=web    app=web

If no Pod matches the selector, the Service has no usable backend endpoints.

---

## 4. Service manifest structure

A typical Service looks like this:

    apiVersion: v1
    kind: Service
    metadata:
      name: web-service
    spec:
      type: ClusterIP
      selector:
        app: web
      ports:
        - protocol: TCP
          port: 80
          targetPort: 8080

Important fields:

| Field | Meaning |
|---|---|
| apiVersion | Kubernetes API version |
| kind | Resource type |
| metadata.name | Service name |
| spec.type | Exposure method |
| spec.selector | Pods selected by the Service |
| port | Service port |
| targetPort | Port on the selected Pod |
| protocol | TCP or UDP |

---

## 5. port vs targetPort

This is one of the most important Kubernetes networking concepts.

Suppose the application listens on port 8080, but clients connect to the Service on port 80.

    Client
      |
      | :80
      v
    Service :80
      |
      | forwards to :8080
      v
    Pod :8080

Configuration:

    ports:
      - port: 80
        targetPort: 8080

### port

The port exposed by the Service.

### targetPort

The port on the selected Pod/application.

Remember:

    Service port → targetPort → application

Example:

    80 → 8080

---

## 6. Named target ports

Container ports can be named:

    containers:
      - name: web
        image: nginx
        ports:
          - name: http
            containerPort: 80

The Service can use:

    ports:
      - port: 80
        targetPort: http

Named ports can make larger manifests easier to understand.

---

# 7. Service Types

Kubernetes provides four main Service types:

| Type | Purpose |
|---|---|
| ClusterIP | Internal cluster access; default |
| NodePort | Exposes a port on each Node |
| LoadBalancer | Integrates with an external/cloud load balancer |
| ExternalName | Maps a Service name to an external DNS name |

---

# 8. ClusterIP

ClusterIP is the default Service type.

It exposes the Service inside the Kubernetes cluster.

    Cluster
    +--------------------------------+
    |                                |
    | Client → ClusterIP Service     |
    |                    |           |
    |              +-----+-----+     |
    |              |     |     |     |
    |              v     v     v     |
    |             Pod   Pod   Pod    |
    |                                |
    +--------------------------------+

Create a ClusterIP Service:

    kubectl expose deployment web       --name=web-service       --type=ClusterIP       --port=80       --target-port=80

Inspect:

    kubectl get svc web-service
    kubectl describe svc web-service

ClusterIP is normally used for internal application-to-application communication.

---

# 9. NodePort

NodePort exposes a Service through a port on each Node.

    External Client
          |
          v
    NodeIP:30080
          |
          v
       Service
          |
      +---+---+
      |   |   |
      v   v   v
     Pod Pod Pod

Example:

    apiVersion: v1
    kind: Service
    metadata:
      name: web-nodeport
    spec:
      type: NodePort
      selector:
        app: web
      ports:
        - port: 80
          targetPort: 80
          nodePort: 30080

Create:

    kubectl expose deployment web       --name=web-nodeport       --type=NodePort       --port=80       --target-port=80

Inspect:

    kubectl get svc web-nodeport

A NodePort normally uses a port from Kubernetes' configured NodePort range, commonly 30000-32767.

**Important:** NodePort does not mean the Pod is running on that Node. It is an exposure mechanism for the Service.

---

# 10. LoadBalancer

LoadBalancer is commonly used when Kubernetes runs on a cloud platform.

    External Client
          |
          v
    Cloud Load Balancer
          |
          v
    Kubernetes Service
          |
      +---+---+---+
      |   |   |   |
      v   v   v   v
     Pod Pod Pod Pod

Example:

    apiVersion: v1
    kind: Service
    metadata:
      name: web-loadbalancer
    spec:
      type: LoadBalancer
      selector:
        app: web
      ports:
        - port: 80
          targetPort: 80

Create:

    kubectl expose deployment web       --name=web-loadbalancer       --type=LoadBalancer       --port=80       --target-port=80

Check:

    kubectl get svc web-loadbalancer

In managed cloud environments, Kubernetes/cloud integration can provision or associate an external load balancer.

**Minikube note:** A LoadBalancer can remain pending locally unless the appropriate Minikube mechanism, such as minikube tunnel, is used.

---

# 11. ExternalName

ExternalName is different from the other Service types because it does not normally select Pods.

It creates a DNS alias to an external hostname.

    apiVersion: v1
    kind: Service
    metadata:
      name: external-database
    spec:
      type: ExternalName
      externalName: database.example.com

A workload can use:

    external-database

and DNS resolves it to:

    database.example.com

This is useful when applications should use a Kubernetes Service name while the actual destination exists outside the cluster.

---

# 12. Endpoints and EndpointSlices

Kubernetes needs to know which backend addresses belong to a Service.

Inspect endpoints:

    kubectl get endpoints web-service

Inspect EndpointSlices:

    kubectl get endpointslices

For a specific Service:

    kubectl get endpointslices       -l kubernetes.io/service-name=web-service

Conceptually:

    Service
       |
       v
    EndpointSlice
       |
       +---- 10.244.1.10
       +---- 10.244.1.11
       +---- 10.244.2.15
       |
       v
      Pods

EndpointSlices are the scalable, modern representation of Service endpoints.

---

# 13. Service DNS

Kubernetes provides DNS-based Service discovery.

Common forms:

    service-name

    service-name.namespace

    service-name.namespace.svc.cluster.local

Example:

    web-service.default.svc.cluster.local

For a Service named api in namespace production:

    api.production.svc.cluster.local

This is extremely important for microservices.

Example:

    frontend
       |
       | http://api.production.svc.cluster.local
       v
    api Service
       |
       v
    API Pods

Applications can therefore communicate using stable names rather than changing Pod IP addresses.

---

# 14. DNS laboratory

Create a temporary Pod:

    kubectl run dns-test       --image=busybox:1.36       --restart=Never       -- sleep 3600

Enter it:

    kubectl exec -it dns-test -- sh

Test:

    nslookup web-service

Test the fully qualified name:

    nslookup web-service.default.svc.cluster.local

Exit:

    exit

Delete:

    kubectl delete pod dns-test

If the selected image does not contain the DNS utility you need, use a dedicated network-debugging image.

---

# 15. Service and readiness

Readiness is important because a Pod should receive normal Service traffic only when it is ready to serve requests.

Example:

    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10

When troubleshooting a Service, always check:

    kubectl get pods

Look for Pods that are not Ready.

---

# 16. Service selectors and namespaces

Service selectors operate within the Service's namespace.

For example:

    production/web-service

selects Pods in production.

It does not automatically select:

    default/web-pod

Always check the namespace:

    kubectl get svc -n production
    kubectl get pods -n production
    kubectl get endpoints -n production

---

# 17. Headless Services

A normal Service normally receives a ClusterIP.

A headless Service uses:

    clusterIP: None

Example:

    apiVersion: v1
    kind: Service
    metadata:
      name: database
    spec:
      clusterIP: None
      selector:
        app: database
      ports:
        - port: 5432
          targetPort: 5432

Headless Services are useful when clients need discovery of individual Pods.

They are especially important with StatefulSets.

    database.default.svc.cluster.local
              |
       +------+------+------+
       |      |      |
       v      v      v
     Pod-0  Pod-1  Pod-2

---

# 18. Session affinity

By default, Service traffic can be distributed among eligible endpoints.

Kubernetes also supports client-IP-based session affinity:

    sessionAffinity: ClientIP

Example:

    apiVersion: v1
    kind: Service
    metadata:
      name: sticky-service
    spec:
      selector:
        app: web
      sessionAffinity: ClientIP
      ports:
        - port: 80
          targetPort: 80

Use sticky sessions carefully. Modern applications often prefer stateless workloads with shared or external session storage.

---

# 19. Service without a selector

A Service does not always have to use a selector.

Advanced configurations can create a Service without a selector and manage endpoint information separately.

This can be useful when a stable Kubernetes Service name must represent a destination that is not a normal Pod-selected backend.

For most applications, the standard pattern is:

    Service
       +
    selector
       +
    Pod labels

---

# 20. Service networking mental model

Keep this mental model:

    Client
      |
      v
    Service DNS
      |
      v
    Service IP
      |
      v
    Service dataplane
      |
      +---------+---------+
      |         |         |
      v         v         v
    Pod A     Pod B     Pod C

The exact packet-processing implementation can vary by Kubernetes distribution and networking stack, but the abstraction remains the same.

---

# 21. Hands-on Lab 1 — Create a ClusterIP Service

### Objective

Create a Deployment with three Pods and expose it internally.

### Step 1 — Create Deployment

    kubectl create deployment web --image=nginx:1.27

### Step 2 — Scale

    kubectl scale deployment web --replicas=3

### Step 3 — Inspect Pods

    kubectl get pods -o wide

### Step 4 — Inspect labels

    kubectl get pods --show-labels

### Step 5 — Create Service

    kubectl expose deployment web       --name=web-service       --type=ClusterIP       --port=80       --target-port=80

### Step 6 — Inspect

    kubectl get svc web-service
    kubectl describe svc web-service
    kubectl get endpoints web-service
    kubectl get endpointslices -l kubernetes.io/service-name=web-service

### Step 7 — Test locally

    kubectl port-forward svc/web-service 8080:80

Open:

    http://localhost:8080

---

# 22. Hands-on Lab 2 — Break the Service

This is an important troubleshooting exercise.

Suppose the Pods have:

    app=web

but the Service selector is changed to:

    app=wrong-label

Check:

    kubectl get endpoints web-service

There should be no matching backend endpoints.

Investigate:

    kubectl describe svc web-service
    kubectl get pods --show-labels

Fix the selector:

    selector:
      app: web

Apply again:

    kubectl apply -f web-service.yaml

Verify:

    kubectl get endpoints web-service

### Lesson

When a Service has no endpoints, check the Service selector and Pod labels first.

---

# 23. Hands-on Lab 3 — Scale the application

Start with three replicas:

    kubectl scale deployment web --replicas=3

Check:

    kubectl get endpoints web-service

Scale to five:

    kubectl scale deployment web --replicas=5

Check again:

    kubectl get endpoints web-service

The Service remains stable while the backend set changes.

---

# 24. Hands-on Lab 4 — Delete a Pod

List Pods:

    kubectl get pods

Delete one:

    kubectl delete pod <pod-name>

Watch:

    kubectl get pods -w

The Deployment should create a replacement.

Check:

    kubectl get endpoints web-service

The Service continues to provide a stable endpoint.

---

# 25. Hands-on Lab 5 — NodePort with Minikube

Create:

    kubectl expose deployment web       --name=web-nodeport       --type=NodePort       --port=80       --target-port=80

Inspect:

    kubectl get svc web-nodeport

On Minikube:

    minikube service web-nodeport --url

This provides a convenient way to access the NodePort Service locally.

---

# 26. Hands-on Lab 6 — Complete Deployment + Service YAML

Create web-app.yaml:

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
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: web-service
    spec:
      type: ClusterIP
      selector:
        app: web
      ports:
        - name: http
          port: 80
          targetPort: 80

Apply:

    kubectl apply -f web-app.yaml

Verify:

    kubectl get deployment web
    kubectl get pods -l app=web
    kubectl get service web-service
    kubectl get endpoints web-service

Test:

    kubectl port-forward service/web-service 8080:80

---

# 27. Troubleshooting a Service

Use this sequence when traffic does not reach an application.

### Step 1 — Check the Service

    kubectl get svc

### Step 2 — Describe it

    kubectl describe svc web-service

Pay attention to:

    Selector:
    Endpoints:
    Port:
    TargetPort:

### Step 3 — Check Pods

    kubectl get pods --show-labels

### Step 4 — Test the selector

If the selector is app=web:

    kubectl get pods -l app=web

If this returns no Pods, the selector or labels are wrong.

### Step 5 — Check endpoints

    kubectl get endpoints web-service

and:

    kubectl get endpointslices       -l kubernetes.io/service-name=web-service

### Step 6 — Check readiness

    kubectl get pods

Look for Pods that are not Ready.

### Step 7 — Check application logs

    kubectl logs <pod-name>

### Step 8 — Test from inside the cluster

Create a temporary network client:

    kubectl run curl-test       --rm -it       --image=curlimages/curl       -- sh

Then:

    curl http://web-service

This helps distinguish a DNS/Service problem from an application problem.

---

# 28. Common Service problems

| Problem | What to check |
|---|---|
| No endpoints | Selector vs Pod labels |
| Connection refused | targetPort and application listener |
| DNS failure | Service name, namespace, CoreDNS |
| Service works locally but not externally | Service type and exposure |
| Some Pods receive no traffic | Readiness and endpoint membership |
| Wrong environment | Namespace |
| NodePort inaccessible | Node address, firewall, Minikube/network |
| LoadBalancer pending | Cloud integration or local environment |
| Wrong port | port vs targetPort |

---

# 29. Service vs Ingress

Do not confuse these resources.

| Service | Ingress |
|---|---|
| Stable endpoint for Pods | HTTP/HTTPS routing |
| Uses selectors to find backends | Routes using host/path rules |
| ClusterIP, NodePort, LoadBalancer, etc. | Requires an Ingress Controller |
| Fundamental networking abstraction | Higher-level HTTP routing |

Typical architecture:

    Internet
       |
       v
    Ingress Controller
       |
       +-------------+
       |             |
       v             v
    web-service   api-service
       |             |
      Pods          Pods

Ingress will be covered later in the course.

---

# 30. Service vs Pod IP

Avoid hard-coding Pod IPs.

Bad:

    frontend → 10.244.1.20

Better:

    frontend → backend-service

For cross-namespace communication:

    frontend → backend-service.production.svc.cluster.local

The Service provides the stable abstraction while Pods can change.

---

# 31. Important commands

### List Services

    kubectl get svc

### List Services in all namespaces

    kubectl get svc -A

### Inspect a Service

    kubectl describe svc <service-name>

### Get Service YAML

    kubectl get svc <service-name> -o yaml

### Get endpoints

    kubectl get endpoints <service-name>

### Get EndpointSlices

    kubectl get endpointslices

### Find matching Pods

    kubectl get pods -l app=web

### Expose a Deployment

    kubectl expose deployment <deployment-name> --port=80 --target-port=80

### Port-forward a Service

    kubectl port-forward svc/<service-name> 8080:80

### Delete a Service

    kubectl delete svc <service-name>

Deleting a Service does not delete the Pods behind it.

---

# 32. Certification & Interview Questions

### Q1. What is a Kubernetes Service?

A stable network abstraction used to access a group of Pods.

### Q2. Why do we need Services?

Because Pod IP addresses are ephemeral.

### Q3. What is the default Service type?

ClusterIP.

### Q4. What is the difference between port and targetPort?

port is the Service port. targetPort is the destination port on the selected Pod.

### Q5. How does a Service find Pods?

Using label selectors.

### Q6. What happens when a selector matches no Pods?

The Service has no matching endpoints and cannot route traffic to those Pods.

### Q7. What is NodePort?

A Service type that exposes the Service through a port on each Node.

### Q8. What is LoadBalancer?

A Service type designed to integrate with an external load balancer, commonly through cloud-provider integration.

### Q9. What is a headless Service?

A Service with clusterIP set to None, often used for direct Pod discovery and StatefulSet workloads.

### Q10. Does deleting a Service delete its Pods?

No. They are separate Kubernetes resources.

### Q11. How do you troubleshoot a Service with no traffic?

Start with:

    kubectl describe svc <service>
    kubectl get pods --show-labels
    kubectl get endpoints <service>
    kubectl get endpointslices
    kubectl get pods
    kubectl logs <pod>

---

# 33. Certification Checklist

Before moving to Day 09, you should be able to:

- [ ] Explain why Services are needed.
- [ ] Explain Service selectors.
- [ ] Explain port and targetPort.
- [ ] Create a ClusterIP Service.
- [ ] Create a NodePort Service.
- [ ] Explain LoadBalancer.
- [ ] Explain ExternalName.
- [ ] Explain Service DNS.
- [ ] Inspect Endpoints and EndpointSlices.
- [ ] Troubleshoot a Service with no endpoints.
- [ ] Explain headless Services.
- [ ] Use kubectl port-forward.
- [ ] Explain why Pod IPs should not be hard-coded.
- [ ] Explain Service vs Ingress.
- [ ] Explain the role of readiness in Service traffic.

---

# 34. Day 08 Challenge

Build this architecture:

    Client
      |
      v
    web-service
      |
      +---------+---------+
      |         |         |
      v         v         v
    web-1     web-2     web-3
      |         |         |
      +---------+---------+
                |
           Deployment

Requirements:

1. Create a Deployment named web.
2. Run 3 replicas.
3. Add label app=web.
4. Create a ClusterIP Service named web-service.
5. Expose Service port 80.
6. Use targetPort 80.
7. Verify all three Pods are selected.
8. Inspect endpoints.
9. Port-forward the Service to local port 8080.
10. Open the application in a browser.
11. Delete one Pod.
12. Verify the Deployment recreates it.
13. Verify the Service continues working.
14. Scale the Deployment to 5 replicas.
15. Verify the Service endpoints change accordingly.

### Bonus challenge

Create an API Deployment with:

    app=api

Create:

    api-service

Then launch a temporary debugging Pod and verify:

    curl http://api-service

Also test:

    api-service.default.svc.cluster.local

---

# 35. Key Takeaways

> **Pods are temporary. Services provide stable access to them.**

Remember:

    1. Service = stable network endpoint
    2. Selector = finds matching Pods
    3. port = Service port
    4. targetPort = application/Pod port
    5. DNS = Service discovery
    6. EndpointSlice = tracks backend endpoints

The most important architecture is:

    Client
      |
      v
    Service DNS
      |
      v
    Service
      |
      | selector: app=web
      |
      +---------+---------+
      |         |         |
      v         v         v
    Pod 1     Pod 2     Pod 3

Once you understand this model, Kubernetes networking becomes much easier to reason about.

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life

**Next lesson:** [Day 09 — Namespaces](../day-09-namespaces/README.md)
