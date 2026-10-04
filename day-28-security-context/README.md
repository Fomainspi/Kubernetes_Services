# Day 28 — Security Context

## 🎯 Learning Objectives

Understand Pod and container security contexts, non-root execution, user/group IDs, capabilities, privilege escalation, read-only filesystems, seccomp, and Pod Security Standards.

## 📖 Definition

A **SecurityContext** defines security settings for a Pod or container.

### Mental Model

    Pod SecurityContext → identity, fsGroup, seccomp

    Container SecurityContext → capabilities, privilege escalation, filesystem controls

## Run as Non-Root

    securityContext:

      runAsNonRoot: true

      runAsUser: 1000

      runAsGroup: 1000

      seccompProfile:

        type: RuntimeDefault

Container example:

    securityContext:

      allowPrivilegeEscalation: false

      capabilities:

        drop: [ALL]

      readOnlyRootFilesystem: true

Use an image that supports the selected non-root user.

## Linux Capabilities

Capabilities split privileged Linux operations into smaller units. A hardened workload can drop ALL and add only a capability that is genuinely required.

## Read-Only Root Filesystem

readOnlyRootFilesystem prevents writes to the container root filesystem. Applications needing temporary writes can use an emptyDir mounted at the required path.

## Privilege Escalation

allowPrivilegeEscalation: false reduces the ability of a process to gain additional privileges.

## 🧪 Hands-On Lab

Use a non-root compatible image:

    apiVersion: v1

    kind: Pod

    metadata:

      name: security-demo

    spec:

      securityContext:

        runAsNonRoot: true

        runAsUser: 1000

        runAsGroup: 1000

        seccompProfile:

          type: RuntimeDefault

      containers:

      - name: app

        image: nginxinc/nginx-unprivileged:stable

        securityContext:

          allowPrivilegeEscalation: false

          capabilities:

            drop: [ALL]

Inspect:

    kubectl exec security-demo -- id

    kubectl get pod security-demo -o yaml

## 🔧 Troubleshooting

runAsNonRoot failures often mean the image requires root or has incompatible user metadata.

Read-only filesystem errors mean the application is trying to write where it cannot. Mount writable temporary storage where appropriate.

Volume permission errors require reviewing runAsUser, runAsGroup, fsGroup, and backend behavior.

## Pod Security Standards

Common profiles are Privileged, Baseline, and Restricted. Production namespaces should use the most restrictive profile compatible with the workload.

## 🎯 Challenge

Harden a Deployment: non-root, no privilege escalation, drop unnecessary capabilities, RuntimeDefault seccomp, and read-only root filesystem where supported.

## 🎓 Interview Questions

1. Why avoid root?

2. runAsUser vs runAsGroup?

3. What is fsGroup?

4. What are capabilities?

5. What does readOnlyRootFilesystem do?

6. What is seccomp?

7. What are Pod Security Standards?

## ✅ Certification Checklist

- [ ] Explain SecurityContext

- [ ] Run containers as non-root

- [ ] Drop capabilities

- [ ] Disable privilege escalation

- [ ] Use seccomp

- [ ] Understand Pod Security Standards

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
