# Day 26 — RBAC

## 🎯 Learning Objectives

Understand Kubernetes Role-Based Access Control, Roles, ClusterRoles, bindings, verbs, ServiceAccounts, least privilege, and authorization testing.

## 📖 Definition

**RBAC** controls who can perform which actions on which Kubernetes resources.

### Mental Model

    Subject → RoleBinding → Role/ClusterRole → Resources + Verbs

RBAC is additive: standard RBAC does not define explicit deny rules.

## Core Objects

**Role** — permissions within one namespace.

**ClusterRole** — reusable or cluster-scoped permissions.

**RoleBinding** — grants permissions in a namespace.

**ClusterRoleBinding** — grants a ClusterRole cluster-wide.

## Common Verbs

get, list, watch, create, update, patch, delete, deletecollection

## Example Role

    apiVersion: rbac.authorization.k8s.io/v1

    kind: Role

    metadata:

      name: pod-reader

      namespace: development

    rules:

    - apiGroups: [""]

      resources: ["pods"]

      verbs: ["get", "list", "watch"]

## Example RoleBinding

    apiVersion: rbac.authorization.k8s.io/v1

    kind: RoleBinding

    metadata:

      name: developer-pod-reader

      namespace: development

    subjects:

    - kind: User

      name: developer

    roleRef:

      kind: Role

      name: pod-reader

      apiGroup: rbac.authorization.k8s.io

## 🧪 Hands-On Lab

Create a namespace and ServiceAccount:

    kubectl create namespace rbac-demo

    kubectl create serviceaccount developer -n rbac-demo

Create a Role:

    kubectl create role pod-reader --verb=get,list,watch --resource=pods -n rbac-demo

Bind it:

    kubectl create rolebinding developer-read --role=pod-reader --serviceaccount=rbac-demo:developer -n rbac-demo

Test:

    kubectl auth can-i get pods --as=system:serviceaccount:rbac-demo:developer -n rbac-demo

    kubectl auth can-i delete pods --as=system:serviceaccount:rbac-demo:developer -n rbac-demo

## 🔍 Inspection

    kubectl get roles -A

    kubectl get rolebindings -A

    kubectl get clusterroles

    kubectl get clusterrolebindings

    kubectl describe role <name> -n <namespace>

## 🔧 Troubleshooting

Authorization failures usually come from wrong subject names, namespaces, roleRef values, API groups, resources, or verbs.

Always test with kubectl auth can-i.

## ⚠️ Least Privilege

Avoid cluster-admin unless genuinely required. Prefer the smallest namespace, resource, and verb set that solves the task.

## 🎯 Challenge

Create a read-only Pod identity. Prove it can get/list/watch Pods but cannot delete Pods or create Deployments.

## 🎓 Interview Questions

1. Role vs ClusterRole?

2. RoleBinding vs ClusterRoleBinding?

3. What are RBAC verbs?

4. What is least privilege?

5. How do you test authorization?

6. Why is cluster-admin dangerous?

## ✅ Certification Checklist

- [ ] Explain RBAC

- [ ] Create Roles and bindings

- [ ] Understand ClusterRoles

- [ ] Use auth can-i

- [ ] Apply least privilege

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
