# Day 25 — NetworkPolicy

## 🎯 Learning Objectives

Understand ingress and egress policies, Pod and namespace selectors, default deny, CNI enforcement, and network troubleshooting.

## 📖 Definition

A **NetworkPolicy** controls allowed network traffic to and from selected Pods.

### Mental Model

    frontend → backend → database

    frontend ──X──→ database

Start with a deny posture, then explicitly allow required communication.

## Ingress vs Egress

Ingress controls traffic entering selected Pods. Egress controls traffic leaving selected Pods.

NetworkPolicy enforcement depends on the cluster network plugin supporting it. Creating a policy object alone does not guarantee enforcement.

## Default Deny Ingress

    apiVersion: networking.k8s.io/v1

    kind: NetworkPolicy

    metadata:

      name: default-deny-ingress

    spec:

      podSelector: {}

      policyTypes:

      - Ingress

podSelector: {} selects all Pods in that namespace.

## Allow Frontend → Backend

    apiVersion: networking.k8s.io/v1

    kind: NetworkPolicy

    metadata:

      name: allow-frontend

    spec:

      podSelector:

        matchLabels:

          app: backend

      policyTypes: [Ingress]

      ingress:

      - from:

        - podSelector:

            matchLabels:

              app: frontend

        ports:

        - protocol: TCP

          port: 8080

## Namespace Selectors

namespaceSelector can select source namespaces by labels. Combining namespaceSelector and podSelector can narrow traffic to Pods in specific namespaces.

## 🧪 Hands-On Lab

1. Create a namespace netpol-demo.

2. Deploy nginx and expose it.

3. Test access from a temporary client.

4. Apply default-deny ingress.

5. Verify the connection is blocked if your CNI enforces policy.

6. Add an allow rule for a labeled client.

Inspect:

    kubectl get networkpolicy -A

    kubectl describe networkpolicy <name>

## 🔧 Troubleshooting

Check Pod labels, namespace labels, selected ports, policy namespace, and CNI capabilities.

Common failures include wrong selectors, missing namespace labels, wrong ports, and unsupported enforcement.

## Production Notes

Egress policies can accidentally block DNS, monitoring, external APIs, package repositories, or other dependencies. Test policies systematically.

## 🎯 Challenge

Implement frontend → backend → database. Prove frontend cannot reach database directly while backend can reach database.

## 🎓 Interview Questions

1. What is NetworkPolicy?

2. Ingress vs egress?

3. What does podSelector: {} mean?

4. Does every cluster enforce NetworkPolicy?

5. How do selectors work?

6. Why use default deny?

## ✅ Certification Checklist

- [ ] Explain NetworkPolicy

- [ ] Configure ingress and egress

- [ ] Use Pod selectors

- [ ] Use namespace selectors

- [ ] Understand CNI enforcement

- [ ] Troubleshoot blocked traffic

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
