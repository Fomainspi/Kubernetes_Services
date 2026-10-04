# Day 15 — Kubernetes Taints & Tolerations

> **Level:** Intermediate  
> **Module:** Scheduling Control

## 1. Definition

A taint repels Pods from a Node unless the Pod has a matching toleration.

## 2. Mental model

Taint → repel | Toleration → allow

    Node + taint
       | 
       v
    non-tolerating Pod rejected; tolerating Pod eligible

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Taint effects
NoSchedule prevents new non-tolerating Pods. PreferNoSchedule is a softer preference. NoExecute can evict existing non-tolerating Pods. tolerationSeconds can make NoExecute tolerance temporary.


## 4. Core concepts

1. **NoSchedule**
2. **PreferNoSchedule**
3. **NoExecute**
4. **tolerationSeconds**
5. **dedicated Nodes**
6. **GPU workloads**
7. **maintenance**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl taint nodes <node> dedicated=database:NoSchedule

### Step 2 — Inspect

    kubectl describe node <node>; kubectl describe pod <pod>

### Step 3 — Observe behavior

Run:

    kubectl get pods -o wide
    kubectl get events --sort-by=.lastTimestamp

Compare the observed state with the desired state in your manifest.

### Step 4 — Troubleshoot

Use:

    kubectl describe pod <pod>
    kubectl describe node <node>
    kubectl logs <pod>

Do not change several things at once. Identify the first failing component, inspect its Events/logs, then make one controlled change.

## 6. Complete example

    apiVersion: v1
    kind: Pod
    metadata:
      name: training-15
    spec:
      # Build your production-style manifest here.
      # Keep configuration explicit and version controlled.

Use kubectl apply -f manifest.yaml and then inspect the resulting object.

## 7. Troubleshooting checklist

1. Is the object in the correct Namespace?
2. Does the manifest have the correct apiVersion and kind?
3. Are labels/selectors correct?
4. Are resource requests realistic?
5. Are scheduling constraints satisfiable?
6. Are referenced Secrets, ConfigMaps, Services, PVCs, or Nodes present?
7. What do kubectl describe and Events report?
8. What do container logs report?

## 8. Day 15 challenge

Create a dedicated database Node using both a taint/toleration and a nodeSelector.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Taints & Tolerations?**  
A taint repels Pods from a Node unless the Pod has a matching toleration.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Taints repel; tolerations allow. A toleration does not force placement.

## 10. Certification checklist

- [ ] Define Taints & Tolerations.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Taints repel; tolerations allow. A toleration does not force placement.**

**Next:** [Day 16]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
