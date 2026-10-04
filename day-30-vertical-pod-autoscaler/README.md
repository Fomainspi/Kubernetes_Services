# Day 30 — Vertical Pod Autoscaler

## 🎯 Learning Objectives

Understand vertical scaling, VPA recommendations, update modes, resource policies, Pod recreation, and safe HPA/VPA design.

## 📖 Definition

The **Vertical Pod Autoscaler (VPA)** helps recommend or adjust CPU and memory resources for Pods.

### Mental Model

    Current Pod → VPA → Recommendation → Updated resources → Pod

Horizontal scaling changes the number of Pods. Vertical scaling changes the resources assigned to each Pod.

## HPA vs VPA vs Cluster Autoscaler

    HPA → number of Pods

    VPA → Pod resource sizing

    CA  → number of Nodes

## VPA Update Modes

**Off** — recommendations only; safest starting point.

**Initial** — applies recommendations when Pods are initially created.

**Recreate** — can evict/recreate Pods to apply recommendations.

**InPlaceOrRecreate** — on supported Kubernetes/VPA environments, attempts in-place updates and falls back to recreation when required.

Always verify the installed VPA version and cluster capabilities.

## Example VPA

    apiVersion: autoscaling.k8s.io/v1

    kind: VerticalPodAutoscaler

    metadata:

      name: web-vpa

    spec:

      targetRef:

        apiVersion: apps/v1

        kind: Deployment

        name: web

      updatePolicy:

        updateMode: Off

      resourcePolicy:

        containerPolicies:

        - containerName: "*"

          minAllowed:

            cpu: 50m

            memory: 64Mi

          maxAllowed:

            cpu: 1

            memory: 1Gi

## 🧪 Hands-On Lab

1. Verify that VPA is installed; it is not present by default on every cluster.

    kubectl api-resources | grep -i vertical

2. Deploy an application with realistic requests.

3. Create a VPA in Off mode.

4. Generate representative workload.

5. Inspect recommendations after enough usage history exists.

    kubectl get vpa

    kubectl describe vpa web-vpa

6. Decide whether to update requests manually.

## ⚠️ Availability

Automatic VPA updates can recreate Pods. Consider replica count, startup time, graceful shutdown, PodDisruptionBudgets, and application state before enabling automatic updates.

## HPA + VPA

Using both can be useful, but careless configuration can create feedback loops when both react to the same CPU or memory signals. Design their responsibilities deliberately.

## 🔧 Troubleshooting

No recommendations: check VPA components, metrics availability, target reference, and whether enough historical data exists.

Unexpected restarts: inspect updateMode and VPA status.

## 🎯 Challenge

Deploy a workload with intentionally low requests. Run VPA in Off mode, inspect recommendations, compare them with current requests, and explain why recommendation-only mode is safer for the first experiment.

## 🎓 Interview Questions

1. What is VPA?

2. HPA vs VPA?

3. What does Off mode do?

4. Why can VPA recreate Pods?

5. Why can HPA and VPA conflict?

6. What should you check before enabling automatic updates?

## ✅ Certification Checklist

- [ ] Explain vertical scaling

- [ ] Explain VPA

- [ ] Understand update modes

- [ ] Inspect recommendations

- [ ] Understand Pod recreation

- [ ] Design HPA/VPA safely

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
