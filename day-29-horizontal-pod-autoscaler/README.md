# Day 29 — Horizontal Pod Autoscaler

## 🎯 Learning Objectives

Understand horizontal scaling, Metrics Server, CPU utilization, HPA configuration, behavior, and troubleshooting.

## 📖 Definition

The **Horizontal Pod Autoscaler (HPA)** automatically changes the number of Pod replicas based on observed metrics.

### Mental Model

    Metrics → Metrics API → HPA → desired replicas → Deployment → Pods

Horizontal scaling means 3 Pods can become 6 Pods. It does not make one Pod larger.

## Metrics and Requests

For CPU utilization targets, requests matter. Example: 250m usage / 500m request = 50% utilization.

## Example Deployment Resources

    resources:

      requests:

        cpu: 100m

        memory: 128Mi

      limits:

        cpu: 500m

        memory: 256Mi

## Example HPA

    apiVersion: autoscaling/v2

    kind: HorizontalPodAutoscaler

    metadata:

      name: web

    spec:

      scaleTargetRef:

        apiVersion: apps/v1

        kind: Deployment

        name: web

      minReplicas: 2

      maxReplicas: 10

      metrics:

      - type: Resource

        resource:

          name: cpu

          target:

            type: Utilization

            averageUtilization: 60

## Inspect

    kubectl get hpa

    kubectl describe hpa web

    kubectl get deployment web

    kubectl get pods

    kubectl get hpa -w

## 🧪 Hands-On Lab

1. Verify kubectl top nodes and kubectl top pods work.

2. Deploy nginx with CPU requests.

3. Create an autoscaling/v2 HPA with min 2 and max 10.

4. Generate sustained HTTP load.

5. Watch HPA and Pod counts.

Exact scaling speed depends on metrics collection and HPA behavior.

Example client:

    kubectl run load-generator --rm -it --image=busybox:1.36 -- sh

    while true; do wget -qO- http://web; done

## HPA vs VPA vs Cluster Autoscaler

HPA → more/fewer Pods.

VPA → changes resource recommendations/requests.

Cluster Autoscaler → more/fewer Nodes.

## 🔧 Troubleshooting

If HPA reports unknown metrics, inspect Metrics Server and the metrics API.

    kubectl top pods

    kubectl describe hpa web

    kubectl get apiservice | grep metrics

If it does not scale, verify requests, current utilization, min/max values, target configuration, and actual workload.

## Stabilization

HPA behavior can control scale-up and scale-down rates and stabilization windows. This reduces oscillation in workloads with changing demand.

## 🎯 Challenge

Configure min 2, max 8, CPU target 50%. Generate load and record current metrics, desired replicas, actual replicas, and response time.

## 🎓 Interview Questions

1. What is HPA?

2. Why do CPU requests matter?

3. What does Metrics Server provide?

4. What is autoscaling/v2?

5. How do you troubleshoot unknown metrics?

6. What is stabilization?

## ✅ Certification Checklist

- [ ] Explain HPA

- [ ] Verify metrics

- [ ] Configure CPU HPA

- [ ] Inspect HPA status

- [ ] Understand min/max

- [ ] Troubleshoot scaling

---

**Foundation of Mastering Automation (FOMA)**

DevOps from zero to hero → https://foma.life

#foma.life
