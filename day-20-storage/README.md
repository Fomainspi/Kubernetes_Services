# Day 20 — Kubernetes Storage: Volumes, PVs & PVCs

> **Level:** Intermediate  
> **Module:** Persistent Storage

## 1. Definition

Kubernetes storage separates how a Pod mounts storage from how cluster storage is provisioned and supplied.

## 2. Mental model

Volume → mounted storage | PVC → request | PV → storage resource

    Pod
       | 
       v
    PVC
       | 
       v
    PV
       | 
       v
    StorageClass/provisioner
       | 
       v
    storage backend

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Storage durability
A PVC does not magically guarantee durability. Actual persistence, replication, backup, and recovery depend on the storage backend and its configuration.


## 4. Core concepts

1. **Volumes**
2. **emptyDir**
3. **hostPath**
4. **PV**
5. **PVC**
6. **StorageClass**
7. **dynamic provisioning**
8. **access modes**
9. **reclaim policy**
10. **StatefulSet storage**
11. **Pending PVC troubleshooting**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl get pvc; kubectl get pv; kubectl get storageclass

### Step 2 — Inspect

    kubectl describe pvc <pvc>; kubectl describe pv <pv>; kubectl get events --sort-by=.lastTimestamp

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
      name: training-20
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

## 8. Day 20 challenge

Create a PVC, mount it into a Pod, write a file, recreate the Pod, and verify persistence.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Storage: Volumes, PVs & PVCs?**  
Kubernetes storage separates how a Pod mounts storage from how cluster storage is provisioned and supplied.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Pods consume storage through volumes; applications request persistent storage through PVCs; PVs and StorageClasses connect those requests to real storage.

## 10. Certification checklist

- [ ] Define Storage: Volumes, PVs & PVCs.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Pods consume storage through volumes; applications request persistent storage through PVCs; PVs and StorageClasses connect those requests to real storage.**

**Next:** [Day 21 — StorageClasses](../day-21-storageclasses/README.md)

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
