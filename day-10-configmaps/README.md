# Day 10 — ConfigMaps

## Definition

A ConfigMap stores non-confidential configuration separately from a container image.

Examples:

- Application mode
- Feature flags
- URLs
- Configuration files
- Log levels

~~~text
              ConfigMap
              /                   v        v
       Environment    File mount
             \        /
              \      /
              Application
~~~

Do not store passwords, tokens, private keys, or other sensitive data in ConfigMaps.

## Lab 1 — Create

~~~bash
kubectl create configmap app-config   --from-literal=APP_MODE=development   --from-literal=LOG_LEVEL=info

kubectl get configmap
kubectl describe configmap app-config
~~~

## Lab 2 — Environment variables

Use:

~~~yaml
envFrom:
  - configMapRef:
      name: app-config
~~~

Or a single key:

~~~yaml
env:
  - name: APP_MODE
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_MODE
~~~

## Lab 3 — Mount as files

~~~yaml
volumeMounts:
  - name: config
    mountPath: /etc/app

volumes:
  - name: config
    configMap:
      name: app-config
~~~

The ConfigMap keys become files in the mounted directory.

## Important behavior

When configuration is injected as environment variables, changing the ConfigMap does not automatically change the environment of an already-running process.

Mounted ConfigMap files can be updated by Kubernetes, but the application may need to reload its configuration.

## Troubleshooting

~~~bash
kubectl get configmap app-config -o yaml
kubectl describe pod POD_NAME
kubectl exec -it POD_NAME -- env
~~~

### Challenge

Create a ConfigMap containing APP_NAME, APP_ENV, and LOG_LEVEL. Inject the values into a Pod and verify them.

## Key takeaway

ConfigMaps separate application configuration from the container image, making the same image reusable across environments.
