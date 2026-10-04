# Day 16 — Kubernetes Jobs

> **Level:** Intermediate  
> **Module:** Batch Workloads

## 1. Definition

A Job creates Pods and ensures that finite work reaches successful completion.

## 2. Mental model

Deployment → continuous service | Job → finite work

    Job
       | 
       v
    Pod
       | 
       v
    task
       | 
       v
    success or retry
       | 
       v
    completion

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Job behavior
completions defines successful completions required. parallelism controls concurrent workers. backoffLimit controls failed retries. Job Pods normally use Never or OnFailure restart policies.


## 4. Core concepts

1. **batch/v1**
2. **completions**
3. **parallelism**
4. **restartPolicy**
5. **backoffLimit**
6. **failed/successful Pods**
7. **Job troubleshooting**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl apply -f job.yaml; kubectl get jobs; kubectl logs job/<job>

### Step 2 — Inspect

    kubectl describe job <job>; kubectl describe pod <pod>

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
    kind: Job
    metadata:
      name: training-16
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

## 8. Day 16 challenge

Create a Job with 8 completions and parallelism 2. Observe workers and retries.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Jobs?**  
A Job creates Pods and ensures that finite work reaches successful completion.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Deployment keeps applications running; Job makes finite work complete.

## 10. Certification checklist

- [ ] Define Jobs.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Deployment keeps applications running; Job makes finite work complete.**

**Next:** [Day 17]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
