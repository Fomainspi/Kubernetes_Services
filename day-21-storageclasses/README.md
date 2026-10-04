# Day 21 — StorageClasses

## 🎯 Learning Objectives

Understand StorageClasses, dynamic provisioning, CSI drivers, reclaim policies, binding modes, expansion, and Pending PVC troubleshooting.

## 📖 Definition

A **StorageClass** defines how Kubernetes dynamically provisions persistent storage. A PVC requests storage; the StorageClass tells Kubernetes how to create it.

### Mental Model

    Application → Pod → PVC → StorageClass → CSI Provisioner → Storage Backend → PV

### Static vs Dynamic Provisioning

Static: Admin → PV → PVC → Pod

Dynamic: PVC → StorageClass → Provisioner → PV → PVC → Pod

## 🔑 Important Fields

**provisioner** — identifies the CSI driver responsible for provisioning.

**reclaimPolicy** — commonly Delete or Retain.

**volumeBindingMode** — commonly Immediate or WaitForFirstConsumer.

**allowVolumeExpansion** — permits supported drivers to enlarge volumes.

## Example StorageClass

    apiVersion: storage.k8s.io/v1

    kind: StorageClass

    metadata:

      name: fast-storage

    provisioner: example.csi.driver

    reclaimPolicy: Delete

    volumeBindingMode: WaitForFirstConsumer

    allowVolumeExpansion: true

Use the real CSI provisioner installed in your cluster; the example value is illustrative.

## PVC Using a StorageClass

    apiVersion: v1

    kind: PersistentVolumeClaim

    metadata:

      name: app-data

    spec:

      storageClassName: fast-storage

      accessModes: [ReadWriteOnce]

      resources:

        requests:

          storage: 5Gi

Inspect it with:

    kubectl apply -f pvc.yaml

    kubectl get pvc

    kubectl get pv

    kubectl describe pvc app-data

## 🧪 Hands-On Lab

1. Inspect available classes: kubectl get sc

2. Identify the default class.

3. Create a 1Gi PVC using an available class.

4. Watch it with kubectl get pvc -w.

5. Inspect the resulting PV.

6. Mount the PVC into an nginx Pod and write a test file.

## 🔧 Troubleshooting

PVC Pending can mean no default StorageClass, wrong storageClassName, missing CSI driver, unsupported access mode, insufficient capacity, topology constraints, or backend failure.

Use:

    kubectl describe pvc <name>

    kubectl get sc

    kubectl get pv

    kubectl get events --sort-by=.lastTimestamp

With WaitForFirstConsumer, provisioning may wait until a Pod is scheduled. That can be normal.

## ⚠️ Production Notes

StorageClass choice affects performance, cost, topology, availability, and deletion behavior. A PVC does not automatically mean the data is backed up.

## 🎯 Challenge

Compare two StorageClasses. Document their provisioners, reclaim policies, binding modes, expansion support, and the best choice for a production database.

## 🎓 Interview Questions

1. What is a StorageClass?

2. Static vs dynamic provisioning?

3. StorageClass vs PV vs PVC?

4. What does WaitForFirstConsumer solve?

5. What is CSI?

6. Delete vs Retain?

7. Why can a PVC stay Pending?

## ✅ Certification Checklist

- [ ] Explain dynamic provisioning

- [ ] Inspect StorageClasses

- [ ] Explain CSI and binding modes

- [ ] Understand reclaim policies

- [ ] Troubleshoot Pending PVCs

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
