# Day 23 — Gateway API

## 🎯 Learning Objectives

Understand Gateway API, GatewayClass, Gateway, HTTPRoute, listeners, route attachment, and how Gateway API extends the Ingress model.

## 📖 Definition

**Gateway API** is a Kubernetes API family for expressive traffic routing. It separates infrastructure entry points from application routing rules.

### Mental Model

    GatewayClass → Gateway → Listener → HTTPRoute → Service → Pods

**GatewayClass** identifies the implementation. **Gateway** defines listeners and the traffic entry point. **HTTPRoute** defines HTTP matching and forwarding.

## Why Gateway API?

Gateway API is designed for richer routing, clearer ownership, multiple protocols, and extensibility. It is more expressive than the traditional Ingress API.

## Example Gateway

    apiVersion: gateway.networking.k8s.io/v1

    kind: Gateway

    metadata:

      name: app-gateway

    spec:

      gatewayClassName: example-gateway-class

      listeners:

      - name: http

        protocol: HTTP

        port: 80

The GatewayClass must exist in your cluster.

## Example HTTPRoute

    apiVersion: gateway.networking.k8s.io/v1

    kind: HTTPRoute

    metadata:

      name: app-route

    spec:

      parentRefs:

      - name: app-gateway

      rules:

      - matches:

        - path:

            type: PathPrefix

            value: /api

        backendRefs:

        - name: api-service

          port: 80

## Ingress vs Gateway API

| Ingress | Gateway API |

| --- | --- |

| Ingress | HTTPRoute |

| IngressClass | GatewayClass |

| Ingress object combines concerns | Gateway and Route separate concerns |

## 🧪 Hands-On Lab

1. Check whether Gateway API resources exist.

    kubectl api-resources | grep -i gateway

    kubectl get gatewayclass

    kubectl get gateway -A

    kubectl get httproute -A

2. Use an installed Gateway implementation.

3. Create a Gateway.

4. Create an HTTPRoute to a Service.

5. Inspect status conditions.

## 🔧 Troubleshooting

Gateway not programmed: inspect GatewayClass and controller health.

HTTPRoute not accepted: inspect status conditions and parentRefs.

Backend unresolved: verify Service name, port, namespace, and reference permissions.

Commands:

    kubectl describe gateway <name>

    kubectl describe httproute <name>

## 🎯 Challenge

Design routing for api.example.com/api → api-service and www.example.com/ → web-service. Explain the responsibility of each resource.

## 🎓 Interview Questions

1. What is Gateway API?

2. GatewayClass vs Gateway?

3. Gateway vs HTTPRoute?

4. Why use Gateway API instead of Ingress?

5. What is a listener?

## ✅ Certification Checklist

- [ ] Explain GatewayClass

- [ ] Explain Gateway

- [ ] Explain HTTPRoute

- [ ] Compare Gateway API and Ingress

- [ ] Troubleshoot route status

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
