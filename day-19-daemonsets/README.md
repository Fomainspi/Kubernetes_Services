# Day 19 — Kubernetes DaemonSets

> **Level:** Intermediate  
> **Module:** Node-Level Workloads

## 1. Definition

A DaemonSet ensures a Pod runs on every eligible Node according to its scheduling rules.

## 2. Mental model

Deployment → replica count | DaemonSet → node coverage

    DaemonSet controller
       | 
       v
    eligible Nodes
       | 
       v
    one agent Pod per Node

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Why not a Deployment?
A Deployment manages a desired number of replicas. A DaemonSet expresses the different requirement: coverage of every eligible Node.


## 4. Core concepts

1. **Logging agents**
2. **monitoring agents**
3. **security agents**
4. **nodeSelector**
5. **affinity**
6. **tolerations**
7. **new Nodes**
8. **troubleshooting**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl apply -f daemonset.yaml; kubectl get daemonset; kubectl get pods -o wide

### Step 2 — Inspect

    kubectl describe daemonset <name>; kubectl describe node <node>

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
      name: training-19
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

## 8. Day 19 challenge

Build a node agent that prints its hostname, then add a Node and observe automatic placement.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of DaemonSets?**  
A DaemonSet ensures a Pod runs on every eligible Node according to its scheduling rules.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
DaemonSet provides node-level coverage across eligible Nodes.

## 10. Certification checklist

- [ ] Define DaemonSets.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **DaemonSet provides node-level coverage across eligible Nodes.**

**Next:** [Day 20]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
