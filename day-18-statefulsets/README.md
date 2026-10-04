# Day 18 — Kubernetes StatefulSets

> **Level:** Intermediate  
> **Module:** Stateful Workloads

## 1. Definition

A StatefulSet manages workloads that need stable identity, stable network naming, and/or stable storage associations.

## 2. Mental model

Deployment → interchangeable Pods | StatefulSet → stable identity

    StatefulSet
       | 
       v
    db-0/db-1/db-2
       | 
       v
    individual identity and storage

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Important warning
A StatefulSet does not automatically provide database replication, quorum, backups, failover, or disaster recovery. Those require application/database architecture.


## 4. Core concepts

1. **Stable ordinals**
2. **headless Services**
3. **serviceName**
4. **ordered lifecycle**
5. **volumeClaimTemplates**
6. **scaling**
7. **storage**
8. **database limitations**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl apply -f statefulset.yaml; kubectl get statefulset; kubectl get pods

### Step 2 — Inspect

    kubectl get pvc; kubectl describe statefulset <name>; kubectl describe pod <pod>

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
      name: training-18
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

## 8. Day 18 challenge

Create a 3-replica StatefulSet with a headless Service and one PVC per replica.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of StatefulSets?**  
A StatefulSet manages workloads that need stable identity, stable network naming, and/or stable storage associations.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
StatefulSet provides identity and storage patterns; it does not automatically make a database highly available.

## 10. Certification checklist

- [ ] Define StatefulSets.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **StatefulSet provides identity and storage patterns; it does not automatically make a database highly available.**

**Next:** [Day 19]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
