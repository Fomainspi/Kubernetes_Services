# Day 17 — Kubernetes CronJobs

> **Level:** Intermediate  
> **Module:** Scheduled Batch Workloads

## 1. Definition

A CronJob creates Jobs according to a recurring schedule.

## 2. Mental model

CronJob → Job → Pod → completion

    Schedule
       | 
       v
    Job
       | 
       v
    worker Pod
       | 
       v
    result

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Operational reliability
CronJobs should not be assumed to provide exactly-once business execution. Design scheduled operations to be idempotent and safe if retried.


## 4. Core concepts

1. **Cron syntax**
2. **time zones**
3. **suspend**
4. **concurrencyPolicy**
5. **history limits**
6. **manual triggering**
7. **idempotency**
8. **failure handling**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl apply -f cronjob.yaml; kubectl get cronjob; kubectl get jobs

### Step 2 — Inspect

    kubectl describe cronjob <name>; kubectl create job --from=cronjob/<name> manual-test

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
      name: training-17
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

## 8. Day 17 challenge

Build a recurring backup-style CronJob with Forbid concurrency and limited Job history.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of CronJobs?**  
A CronJob creates Jobs according to a recurring schedule.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
CronJob schedules recurring work; Job executes the work. Design scheduled work to tolerate retries.

## 10. Certification checklist

- [ ] Define CronJobs.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **CronJob schedules recurring work; Job executes the work. Design scheduled work to tolerate retries.**

**Next:** [Day 18]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
