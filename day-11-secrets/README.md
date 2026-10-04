# Day 11 — Kubernetes Secrets

> **Level:** Intermediate  
> **Module:** Configuration & Security

## 1. Definition

A Secret stores sensitive configuration such as passwords, tokens, API keys, and certificates. Secrets separate credentials from application images and ordinary configuration.

## 2. Mental model

ConfigMap → non-sensitive configuration | Secret → sensitive configuration

    Pod
       | 
       v
    Secret
       | 
       v
    environment variables or mounted files

## 3. Why this object matters

Kubernetes objects exist to express desired state. The controller or scheduler continuously works toward that state.


## Security note
Base64 is an encoding format, not encryption. Do not commit real credentials to Git. Restrict Secret access with RBAC, consider encryption at rest, rotate credentials, and consider an external secret-management solution for mature production environments.


## 4. Core concepts

1. **ConfigMap vs Secret**
2. **stringData/data**
3. **env and envFrom**
4. **volume mounts**
5. **TLS and registry Secrets**
6. **RBAC**
7. **encryption at rest**
8. **rotation**
9. **external secret managers**

## 5. Hands-on laboratory

### Step 1 — Start

    kubectl create secret generic db-secret --from-literal=DB_USER=appuser --from-literal=DB_PASSWORD=change-me

### Step 2 — Inspect

    kubectl get secret; kubectl describe secret db-secret

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
      name: training-11
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

## 8. Day 11 challenge

Explain why base64 is encoding, not encryption. Build a Deployment that receives DB_USER and DB_PASSWORD from a Secret.

Then document:

- What you expected.
- What Kubernetes actually did.
- Which controller or component made the decision.
- How you verified the result.
- What you would change for production.

## 9. Interview questions

**What is the main purpose of Secrets?**  
A Secret stores sensitive configuration such as passwords, tokens, API keys, and certificates. Secrets separate credentials from application images and ordinary configuration.

**What should you inspect first when it does not behave as expected?**  
Start with kubectl get, kubectl describe, Events, and application/container logs.

**What is the most important operational lesson?**  
Base64 is not encryption; protect Secret access with RBAC and appropriate encryption-at-rest controls.

## 10. Certification checklist

- [ ] Define Secrets.
- [ ] Explain its architecture.
- [ ] Create it from YAML.
- [ ] Inspect it with kubectl.
- [ ] Troubleshoot a failure.
- [ ] Explain when to use it.
- [ ] Explain when not to use it.
- [ ] Complete the hands-on challenge.

## Key takeaway

> **Base64 is not encryption; protect Secret access with RBAC and appropriate encryption-at-rest controls.**

**Next:** [Day 12]

---

## Foundation of Mastering Automation

**FOMA — DevOps from Zero to Hero**

Learn. Practice. Automate. Deploy.

#foma.life
