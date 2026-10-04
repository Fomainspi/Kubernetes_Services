# Day 27 — ServiceAccounts

## 🎯 Learning Objectives

Understand workload identity, ServiceAccounts, RBAC integration, token mounting, and how to minimize API credentials.

## 📖 Definition

A **ServiceAccount** provides an identity for processes running inside Kubernetes Pods.

### Mental Model

    Pod → ServiceAccount → RBAC Binding → Role → Allowed API actions

Applications such as controllers, operators, automation tools, and platform agents may use this identity.

## Create and Inspect

    kubectl create serviceaccount app-reader

    kubectl get serviceaccount

    kubectl describe serviceaccount app-reader

## Assign to a Pod

    apiVersion: v1

    kind: Pod

    metadata:

      name: api-client

    spec:

      serviceAccountName: app-reader

      containers:

      - name: app

        image: nginx

## ServiceAccount Tokens

Modern Kubernetes commonly uses short-lived, projected ServiceAccount tokens rather than automatically creating a permanent Secret token for every ServiceAccount.

Token mounting and API access should be enabled only when needed.

## RBAC Binding

    kind: RoleBinding

    subjects:

    - kind: ServiceAccount

      name: app-reader

      namespace: default

    roleRef:

      kind: Role

      name: pod-reader

      apiGroup: rbac.authorization.k8s.io

## Disable API Credentials

For workloads that do not need Kubernetes API access:

    automountServiceAccountToken: false

This can be set at the Pod or ServiceAccount level.

## 🧪 Hands-On Lab

1. Create namespace sa-demo.

2. Create app-reader ServiceAccount.

3. Create a Role allowing get/list/watch Pods.

4. Bind the ServiceAccount.

5. Test allowed and forbidden actions with kubectl auth can-i.

6. Attach the ServiceAccount to a Pod.

## 🔧 Troubleshooting

Check the Pod's serviceAccountName, RBAC bindings, token automount setting, and API permissions.

    kubectl get pod <pod> -o jsonpath='{.spec.serviceAccountName}'

    kubectl auth can-i get pods --as=system:serviceaccount:default:app-reader

## 🎯 Challenge

Create an application identity with read-only access to Pods in one namespace. Verify every permission and disable token automount on a workload that does not need API access.

## 🎓 Interview Questions

1. What is a ServiceAccount?

2. ServiceAccount vs human user?

3. How does RBAC use ServiceAccounts?

4. What does automountServiceAccountToken do?

5. Why prefer short-lived credentials?

## ✅ Certification Checklist

- [ ] Explain workload identity

- [ ] Create ServiceAccounts

- [ ] Bind with RBAC

- [ ] Test permissions

- [ ] Understand token mounting

- [ ] Apply least privilege

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
