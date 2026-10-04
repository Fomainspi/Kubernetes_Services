# Day 13 — Kubernetes Resource Requests & Limits

> **Level:** Intermediate  
> **Module:** Resource Management

## 1. Definition

Requests describe resources considered for scheduling; limits constrain configured container resource usage.

## 2. Mental model

Requests → scheduling | Limits → runtime constraint

    Pod requests
       | 
       v
    Scheduler
       | 
       v
    eligible Node
       | 
       v
    kubelet

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## QoS classes
Kubernetes assigns Pods a QoS class: Guaranteed, Burstable, or BestEffort. Resource configuration influences eviction and runtime behavior during resource pressure.


## 4. Core concepts

1. **CPU millicores**
2. **memory units**
3. **requests**
4. **limits**
5. **QoS classes**
6. **OOM kills**
7. **CPU throttling**
8. **ResourceQuota**
9. **LimitRange**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl describe pod <pod>; kubectl describe node <node>

### Step 2 — Inspect

    kubectl get pod <pod> -o jsonpath='{.status.qosClass}'

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
      name: training-13
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

## 8. Day 13 challenge

Create Pods with different requests and observe scheduling. Create an intentionally oversized request and diagnose Pending.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Resource Requests & Limits?**  
Requests describe resources considered for scheduling; limits constrain configured container resource usage.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Requests influence placement; limits constrain resource usage.

## 10. Certification checklist

- [ ] Define Resource Requests & Limits.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Requests influence placement; limits constrain resource usage.**

**Next:** [Day 14]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
