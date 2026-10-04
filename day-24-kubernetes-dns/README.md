# Day 24 — Kubernetes DNS

## 🎯 Learning Objectives

Understand CoreDNS, Service discovery, namespace-qualified names, FQDNs, headless Services, and DNS troubleshooting.

## 📖 Definition

Kubernetes DNS provides service discovery so applications can use stable names instead of changing Pod IP addresses.

### Mental Model

    Application Pod → CoreDNS → Service name → ClusterIP → Endpoint Pods

## Service DNS

The common fully qualified Service name is:

    service-name.namespace.svc.cluster.local

Example:

    api-service.production.svc.cluster.local

Within the same namespace, api-service is usually sufficient. Across namespaces, use api-service.backend or the full FQDN.

## Why DNS?

Pod IPs can change when Pods are recreated. Services provide stable discovery while their backend endpoints change.

## CoreDNS

CoreDNS is commonly the cluster DNS server.

    kubectl get pods -n kube-system

    kubectl get svc -n kube-system kube-dns

    kubectl get configmap -n kube-system coredns -o yaml

Exact labels and configuration vary by distribution.

## Headless Services

A Service with clusterIP: None is headless. DNS can return endpoint addresses directly. This is useful with StatefulSets.

    db.default.svc.cluster.local → db-0, db-1, db-2

## 🧪 Hands-On Lab

Create a Deployment and Service:

    kubectl create deployment dns-web --image=nginx

    kubectl expose deployment dns-web --port=80 --name=dns-web

Run a client:

    kubectl run dns-client --rm -it --image=busybox:1.36 -- sh

Inside:

    nslookup dns-web

    nslookup dns-web.default.svc.cluster.local

    wget -qO- http://dns-web

Inspect /etc/resolv.conf from a Pod.

## 🔧 Troubleshooting

If a Service name fails to resolve, check the Service, EndpointSlices, CoreDNS Pods, and network connectivity.

    kubectl get svc

    kubectl get endpointslices

    kubectl get pods -n kube-system

    kubectl logs -n kube-system -l k8s-app=kube-dns

Remember: successful DNS resolution does not guarantee a healthy application.

## 🎯 Challenge

Create frontend and backend namespaces. Resolve a backend Service from a frontend Pod using service.backend.svc.cluster.local.

## 🎓 Interview Questions

1. What is CoreDNS?

2. What is a Service FQDN?

3. Why not use Pod IPs?

4. What is a headless Service?

5. How would you troubleshoot DNS?

## ✅ Certification Checklist

- [ ] Explain CoreDNS

- [ ] Use Service DNS names

- [ ] Understand FQDN and namespaces

- [ ] Explain headless Services

- [ ] Troubleshoot DNS

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
