# Day 22 — Ingress

## 🎯 Learning Objectives

Understand Ingress, Ingress Controllers, host and path routing, TLS termination, IngressClass, and troubleshooting.

## 📖 Definition

An **Ingress** is a Kubernetes API object describing HTTP/HTTPS routing from an external entry point to Services.

### Mental Model

    Internet → Ingress Controller → Ingress Rules → Service → Pods

Important: the Ingress resource defines rules; an **Ingress Controller** implements those rules. Creating an Ingress object does not install a controller.

## Routing

Host-based:

    app.example.com → web-service

    api.example.com → api-service

Path-based:

    example.com/     → web-service

    example.com/api  → api-service

## Example

    apiVersion: networking.k8s.io/v1

    kind: Ingress

    metadata:

      name: app-ingress

    spec:

      ingressClassName: nginx

      rules:

      - host: example.local

        http:

          paths:

          - path: /

            pathType: Prefix

            backend:

              service:

                name: web-service

                port:

                  number: 80

          - path: /api

            pathType: Prefix

            backend:

              service:

                name: api-service

                port:

                  number: 80

## Path Types

**Prefix** matches a path prefix. **Exact** matches the exact path. **ImplementationSpecific** depends on the controller.

## 🔐 TLS

Ingress can terminate HTTPS using a TLS Secret containing the certificate and private key.

    tls:

    - hosts: [example.com]

      secretName: example-tls

## 🔍 Commands

    kubectl get ingress

    kubectl describe ingress app-ingress

    kubectl get ingressclass

    kubectl get endpointslices

## 🧪 Hands-On Lab

1. Verify your Ingress Controller and IngressClass.

2. Create web and API Deployments.

3. Expose both with ClusterIP Services.

4. Create host/path routing.

5. Test each route.

6. Add TLS if your lab supports it.

## 🔧 Troubleshooting

No ADDRESS: inspect the controller, IngressClass, events, and controller logs.

404: verify host, path, Service name, Service port, and EndpointSlices.

TLS failure: verify the TLS Secret, host name, certificate, and Ingress configuration.

Useful commands:

    kubectl describe ingress <name>

    kubectl get ingressclass

    kubectl get svc

    kubectl get endpointslices

## Ingress vs Service

Service provides stable connectivity to Pods. Ingress provides HTTP/HTTPS routing into Services.

## 🎯 Challenge

Build app.example.local/ for frontend and app.example.local/api for backend. Add HTTPS and document the complete request path.

## 🎓 Interview Questions

1. Ingress vs Ingress Controller?

2. Ingress vs LoadBalancer Service?

3. Host vs path routing?

4. What is ingressClassName?

5. How does TLS termination work?

6. Why can an Ingress return 404?

## ✅ Certification Checklist

- [ ] Explain Ingress

- [ ] Explain controllers and IngressClass

- [ ] Configure host/path routing

- [ ] Configure TLS

- [ ] Troubleshoot 404 and missing ADDRESS

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
