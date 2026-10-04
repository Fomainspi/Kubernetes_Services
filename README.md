# Kubernetes Services 🌐

A practical, beginner-friendly guide to understanding and working with **Kubernetes Services**.

> **Core idea:** A Kubernetes Service provides a stable network endpoint for a changing set of Pods and routes traffic to the Pods selected by its labels.

---

## 📚 What You Will Learn

- Why Kubernetes needs Services
- How a Service finds Pods using labels and selectors
- `ClusterIP`, `NodePort`, `LoadBalancer`, and `ExternalName`
- `port` vs `targetPort` vs `nodePort`
- Service discovery with Kubernetes DNS
- Endpoints and EndpointSlices
- Service troubleshooting
- Hands-on practice with Minikube
- Important Kubernetes interview questions

---

## 🧠 1. Why Do We Need a Service?

Pods are **ephemeral**. A Pod can be deleted and recreated, and its IP address can change.

For example:

```text
Frontend
   |
   +----> Pod 1  10.1.0.10
   |
   +----> Pod 2  10.1.0.11
   |
   +----> Pod 3  10.1.0.12
```

If Pod 2 is recreated:

```text
Pod 2 ❌  10.1.0.11
          |
          v
Pod 4 ✅  10.1.0.25
```

The frontend should not have to keep discovering changing Pod IPs.

A Service gives applications a **stable endpoint**:

```text
              Kubernetes Service
             backend-service
                    |
          +---------+---------+
          |         |         |
          v         v         v
        Pod 1     Pod 2     Pod 3
```

### Golden rule

> **Pod = runs the application**  
> **Service = provides stable network access to the application**

---

# 🏢 2. Think of a Service as a Receptionist

Imagine a company with three backend engineers:

```text
                 🏢 COMPANY
                     |
               👩 Receptionist
                     |
          +----------+----------+
          |          |          |
          v          v          v
       👨 Dev 1   👨 Dev 2   👨 Dev 3
```

A visitor does not need to know which engineer is available.

The visitor simply asks the receptionist:

> "I need someone from the backend team."

Kubernetes works in a similar way:

```text
                 Service
                    |
          +---------+---------+
          |         |         |
          v         v         v
       Pod 1      Pod 2      Pod 3
```

The Service uses a **selector** to find the Pods that belong to it.

---

# 🎯 3. How a Service Finds Pods

This is one of the most important Kubernetes concepts.

Suppose our Pods have this label:

```yaml
labels:
  app: backend
```

The Service has:

```yaml
selector:
  app: backend
```

Kubernetes matches the selector against the Pod labels:

```text
                 Service
             selector:
             app=backend
                   |
        +----------+----------+
        |          |          |
        v          v          v
      Pod 1      Pod 2      Pod 3
      app=       app=       app=
      backend    backend    backend
```

### Important

The following must match:

```yaml
# Service
selector:
  app: backend
```

and

```yaml
# Pod
labels:
  app: backend
```

If they do not match, the Service may have **no backend endpoints**.

---

# 🧩 4. Service YAML

Here is a typical internal Service:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: backend-service

spec:
  selector:
    app: backend

  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080

  type: ClusterIP
```

## What does each field mean?

### `metadata.name`

```yaml
name: backend-service
```

The Service name is used by other workloads for service discovery.

---

### `selector`

```yaml
selector:
  app: backend
```

Selects Pods whose labels match `app=backend`.

---

### `port`

```yaml
port: 80
```

The port exposed by the Service.

---

### `targetPort`

```yaml
targetPort: 8080
```

The destination port on the selected Pod.

Conceptually:

```text
Client
  |
  |  :80
  v
Service
  |
  |  :8080
  v
Pod / Application
```

### Remember

```text
port       = Service port
targetPort = Pod/application port
```

---

# 🚦 5. Kubernetes Service Types

Kubernetes supports these main Service types:

| Type | Purpose | Typical Access |
|---|---|---|
| **ClusterIP** | Internal communication | Inside the cluster |
| **NodePort** | Expose a Service through a Node port | Node IP + port |
| **LoadBalancer** | Expose through an external load balancer | External clients |
| **ExternalName** | Map a Service name to an external DNS name | External DNS |

---

# 🟢 6. ClusterIP

**ClusterIP is the default Service type.**

It is primarily used for communication **inside the cluster**.

```text
       Kubernetes Cluster
+----------------------------------+
|                                  |
|  Frontend Pod                    |
|       |                          |
|       v                          |
|  backend-service                 |
|       |                          |
|   +---+---+---+                  |
|   |       |   |                  |
|   v       v   v                  |
| Pod 1   Pod 2 Pod 3              |
|                                  |
+----------------------------------+
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
  type: ClusterIP
```

Because `ClusterIP` is the default, the following is also valid:

```yaml
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
```

### Common use cases

- Frontend → Backend
- Backend → Internal API
- Application → Redis
- Application → Database Service

---

# 🟡 7. NodePort

A NodePort exposes a Service using a port on each Kubernetes Node.

Example:

```text
Node IP: 192.168.1.20
NodePort: 30080

http://192.168.1.20:30080
```

Traffic conceptually flows like this:

```text
Browser
   |
   | :30080
   v
+-------------------+
| Kubernetes Node   |
|                   |
| NodePort :30080   |
+---------+---------+
          |
          v
       Service
          |
     +----+----+
     |    |    |
     v    v    v
   Pod 1 Pod 2 Pod 3
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 30080
  type: NodePort
```

### Common use

Useful for simple external access, development, testing, and learning.

> NodePort normally uses the Kubernetes NodePort range **30000–32767**.

---

# 🔵 8. LoadBalancer

A `LoadBalancer` Service is commonly used with a cloud provider to expose an application through an external load balancer.

```text
                  🌍 Internet
                      |
                      v
             Cloud Load Balancer
                      |
                      v
              Kubernetes Service
                      |
           +----------+----------+
           |          |          |
           v          v          v
         Pod 1      Pod 2      Pod 3
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 8080
  type: LoadBalancer
```

### Typical cloud environments

- Amazon EKS
- Google Kubernetes Engine
- Azure Kubernetes Service

The cloud integration is responsible for provisioning the actual external load balancer.

---

# 🟣 9. ExternalName

`ExternalName` is different from the other common Service types.

It maps a Service name to an **external DNS name**.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-api
spec:
  type: ExternalName
  externalName: api.example.com
```

An application can use:

```text
external-api
```

and Kubernetes DNS can resolve it to:

```text
api.example.com
```

### Important

`ExternalName` does **not** create a normal proxying path to Pods.

---

# ⭐ 10. Service Types — Big Picture

```text
                      🌍 INTERNET
                           |
                           v
                 +-------------------+
                 |   LoadBalancer    |
                 +---------+---------+
                           |
                           v
                 +-------------------+
                 |     Service       |
                 +---------+---------+
                           |
                 +---------+---------+
                 |         |         |
                 v         v         v
               Pod 1     Pod 2     Pod 3
```

A simple mental model:

```text
ClusterIP     -> internal door
NodePort      -> door on every Node
LoadBalancer  -> public/cloud front door
ExternalName  -> DNS sign pointing elsewhere
```

---

# 🔥 11. Service vs Pod

| Pod | Service |
|---|---|
| Runs containers | Provides network access |
| Can be recreated | Provides a stable endpoint |
| Pod IP can change | Service IP/DNS stays stable |
| Represents workload | Represents network access to workload |

### Remember

```text
Pod      = "Where the application runs"
Service  = "How clients reach the application"
```

---

# 🚦 12. Service vs Ingress

An **Ingress is not a Service type**.

Ingress provides HTTP/HTTPS routing to Services.

```text
                  🌍 Internet
                       |
                       v
                    Ingress
                  /         \
                 /           \
                v             v
       frontend-service   backend-service
              |                 |
            Pods              Pods
```

Example routing:

```text
example.com/          -> frontend-service
example.com/api       -> backend-service
example.com/products  -> product-service
```

Ingress can provide hostname/path based HTTP(S) routing and TLS termination.

> For new Kubernetes networking designs, also learn the **Gateway API**, which is the successor direction for more expressive traffic management.

---

# 🔍 13. Service Discovery

Kubernetes provides DNS-based service discovery.

A Service named:

```text
backend-service
```

can normally be reached by name from another Pod in the same namespace:

```text
backend-service
```

Fully qualified names can look like:

```text
backend-service.production.svc.cluster.local
```

Where:

```text
backend-service = Service
production       = Namespace
svc              = Kubernetes Service DNS zone
cluster.local    = Cluster domain
```

This lets applications communicate by **name instead of hard-coded Pod IPs**.

---

# 🧭 14. Endpoints and EndpointSlices

A Service needs to know which Pods are currently available behind it.

Useful commands:

```bash
kubectl get endpoints
```

and:

```bash
kubectl get endpointslices
```

If a Service has no matching Pods, you may see no useful endpoints.

Conceptually:

```text
Service
   |
   v
EndpointSlice
   |
   +---- Pod IP : Port
   +---- Pod IP : Port
   +---- Pod IP : Port
```

EndpointSlices are the modern way Kubernetes tracks Service backend endpoints.

---

# 🧪 15. Hands-On Lab with Minikube

## Step 1 — Create a Deployment

Create `deployment.yaml`:

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
          ports:
            - containerPort: 80
```

Apply it:

```bash
kubectl apply -f deployment.yaml
```

Check the Pods:

```bash
kubectl get pods --show-labels
```

You should see three Pods with:

```text
app=web
```

---

## Step 2 — Create a ClusterIP Service

Create `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

Apply it:

```bash
kubectl apply -f service.yaml
```

Check it:

```bash
kubectl get svc
```

Example:

```text
NAME          TYPE        CLUSTER-IP      PORT(S)
web-service   ClusterIP   10.96.120.10    80/TCP
```

---

# 🔎 16. Important Commands

### List Services

```bash
kubectl get svc
```

### List Services in all namespaces

```bash
kubectl get svc -A
```

### Describe a Service

```bash
kubectl describe svc web-service
```

### Get Service YAML

```bash
kubectl get svc web-service -o yaml
```

### Check Service endpoints

```bash
kubectl get endpoints
```

### Check EndpointSlices

```bash
kubectl get endpointslices
```

### Show Pod labels

```bash
kubectl get pods --show-labels
```

### Show labels only for a specific app

```bash
kubectl get pods -l app=web
```

### Port-forward a Service

```bash
kubectl port-forward svc/web-service 8080:80
```

Then open:

```text
http://localhost:8080
```

---

# 🧰 17. Service Troubleshooting

When a Service does not work, check these areas in order.

## 1️⃣ Is the Service present?

```bash
kubectl get svc
```

## 2️⃣ Does the selector match the Pod labels?

Service:

```yaml
selector:
  app: web
```

Pods:

```yaml
labels:
  app: web
```

## 3️⃣ Does the Service have endpoints?

```bash
kubectl get endpoints
kubectl get endpointslices
```

If no endpoints exist, investigate the selector and Pod readiness.

## 4️⃣ Is `targetPort` correct?

For example:

```text
Service port:       80
targetPort:         8080
Application:        8080
```

All three must make sense together.

## 5️⃣ Are the Pods Ready?

```bash
kubectl get pods
```

A Pod can be Running but not Ready.

## 6️⃣ Inspect the Service

```bash
kubectl describe svc web-service
```

## 7️⃣ Test from inside the cluster

Create a temporary Pod:

```bash
kubectl run curl --rm -it \
  --image=curlimages/curl -- \
  curl http://web-service
```

---

# ⚠️ 18. Common Mistakes

### Mistake 1 — Selector does not match labels

```yaml
# Service
selector:
  app: backend
```

but:

```yaml
# Pod
labels:
  app: frontend
```

Result: Service cannot select the intended Pods.

---

### Mistake 2 — Wrong `targetPort`

If the container listens on:

```text
8080
```

but the Service targets:

```yaml
targetPort: 80
```

traffic may fail.

---

### Mistake 3 — Confusing `port` and `targetPort`

Remember:

```text
Client -> Service: port
Service -> Pod: targetPort
```

---

### Mistake 4 — Expecting ClusterIP to be public

ClusterIP is primarily for **internal cluster access**.

For external access, consider:

- NodePort
- LoadBalancer
- Ingress
- Gateway API

depending on your architecture.

---

# 🎯 19. Interview Questions

## Q1. What is a Kubernetes Service?

A Kubernetes Service is an abstraction that provides a stable network endpoint for a set of Pods.

## Q2. Why do we need Services?

Because Pods are ephemeral and their IP addresses can change.

## Q3. What is ClusterIP?

The default Service type used for internal cluster communication.

## Q4. What is NodePort?

A Service type that exposes a Service on a port of each Node.

## Q5. What is LoadBalancer?

A Service type commonly used with cloud providers to expose an application through an external load balancer.

## Q6. How does a Service find Pods?

Through **label selectors**.

## Q7. What is the difference between `port` and `targetPort`?

```text
port       = Service port
targetPort = destination port on the Pod
```

## Q8. How do you troubleshoot a Service with no traffic?

Start with:

```bash
kubectl get svc
kubectl describe svc <service>
kubectl get endpoints
kubectl get endpointslices
kubectl get pods --show-labels
```

Then verify the selector, labels, readiness, and ports.

---

# 🧠 20. The Golden Rule

When a Service is not working, check these three things first:

```text
1️⃣ Service selector
        ↓
2️⃣ Pod labels
        ↓
3️⃣ Service port -> targetPort
```

And remember:

```text
                 SERVICE
                    |
            Stable network endpoint
                    |
          +---------+---------+
          |         |         |
          v         v         v
        Pod 1     Pod 2     Pod 3

Service  = stable access
Selector = finds Pods
Pods     = run the application
```

---

# 📂 Suggested Lab Structure

```text
Kubernetes_Services/
├── README.md
├── deployment.yaml
├── service-clusterip.yaml
├── service-nodeport.yaml
└── service-loadbalancer.yaml
```

---

# 📖 References

- Kubernetes Services: https://kubernetes.io/docs/concepts/services-networking/service/
- Kubernetes Networking: https://kubernetes.io/docs/concepts/services-networking/
- Kubernetes Ingress: https://kubernetes.io/docs/concepts/services-networking/ingress/
- Kubernetes EndpointSlices: https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/

---

## 🚀 Practice Challenge

Build a small application with:

```text
Frontend Deployment
        |
        v
Frontend Service
        |
        v
Backend Service
        |
        v
Backend Pods
```

Then practice:

1. Scale the backend from 1 to 3 replicas.
2. Confirm that the Service still works.
3. Delete one backend Pod.
4. Watch Kubernetes recreate it.
5. Check the EndpointSlices.
6. Change the Service selector and observe what happens.
7. Convert the Service from ClusterIP to NodePort.
8. Test it with Minikube.

---

**Foundation of Mastering Automation — #foma.life**

Happy Kubernetes learning! ☸️
