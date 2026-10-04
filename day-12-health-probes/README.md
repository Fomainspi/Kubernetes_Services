# Day 12 — Kubernetes Health Probes

> **Level:** Intermediate  
> **Module:** Reliability

## 1. Definition

Health probes let Kubernetes determine whether a container has started, is ready for traffic, or is still functioning.

## 2. Mental model

Startup → Readiness → Liveness

    Pod
       | 
       v
    readiness=false
       | 
       v
    Service excludes Pod; liveness failure
       | 
       v
    container restart

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Readiness vs liveness
Readiness failure normally removes a Pod from normal Service endpoints. Liveness failure can cause a container restart. A startup probe protects slow-starting applications from premature liveness failures.


## 4. Core concepts

1. **HTTP**
2. **TCP**
3. **exec**
4. **timing fields**
5. **readiness vs liveness**
6. **startup probes**
7. **probe design**
8. **restart loops**
9. **troubleshooting**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl get pods; kubectl describe pod <pod>; kubectl get events --sort-by=.lastTimestamp

### Step 2 — Inspect

    kubectl describe pod <pod>; kubectl logs <pod>

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
      name: training-12
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

## 8. Day 12 challenge

Create an application with startup, readiness, and liveness probes. Intentionally break each probe and observe the different behavior.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Health Probes?**  
Health probes let Kubernetes determine whether a container has started, is ready for traffic, or is still functioning.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Readiness protects traffic; liveness supports recovery; startup protects initialization.

## 10. Certification checklist

- [ ] Define Health Probes.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Readiness protects traffic; liveness supports recovery; startup protects initialization.**

**Next:** [Day 13]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
