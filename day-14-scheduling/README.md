# Day 14 — Kubernetes Scheduling

> **Level:** Intermediate  
> **Module:** Scheduling & Placement

## 1. Definition

The kube-scheduler selects an eligible Node for a newly created Pod based on resources and scheduling constraints.

## 2. Mental model

Pod → API Server → Scheduler → Node → kubelet

    Pod Pending
       | 
       v
    scheduler evaluates constraints
       | 
       v
    Node selected

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Troubleshooting Pending Pods
Start with kubectl describe pod <pod> and inspect Events. Common causes include insufficient CPU/memory, nodeSelector mismatch, affinity rules, taints, topology constraints, and storage constraints.


## 4. Core concepts

1. **Scheduler flow**
2. **node labels**
3. **nodeSelector**
4. **nodeName**
5. **required/preferred node affinity**
6. **pod affinity**
7. **anti-affinity**
8. **topology spread**
9. **resource requests**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl get pod <pod> -o wide; kubectl describe pod <pod>

### Step 2 — Inspect

    kubectl get nodes --show-labels; kubectl describe pod <pod>

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
      name: training-14
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

## 8. Day 14 challenge

Label Nodes and use nodeSelector, then extend the exercise with node affinity and topology spread.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Scheduling?**  
The kube-scheduler selects an eligible Node for a newly created Pod based on resources and scheduling constraints.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Scheduling decides where Pods run; resources, labels, affinity, taints, and topology influence the decision.

## 10. Certification checklist

- [ ] Define Scheduling.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Scheduling decides where Pods run; resources, labels, affinity, taints, and topology influence the decision.**

**Next:** [Day 15]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
