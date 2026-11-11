## Apply the changes

Create the namespace first if it does not exist:

```bash
kubectl create namespace todoapp
```

Run all commands from the repository root. Apply the ConfigMap and the Secret before the Deployment, so the Pods can find them:

```bash
kubectl apply -f .infrastructure/configMap.yml
kubectl apply -f .infrastructure/secret.yml
kubectl apply -f .infrastructure/deployment.yml
```

If the Deployment was already running, restart it to pick up the new variables:

```bash
kubectl rollout restart deployment todoapp -n todoapp
```