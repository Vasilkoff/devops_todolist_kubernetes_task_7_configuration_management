# Instructions

This change moves the app settings out of the code and into Kubernetes.

- `configMap.yml` stores PYTHONUNBUFFERED.
- `secret.yml` stores SECRET_KEY.
- The Deployment reads both as environment variables.
- `settings.py` now takes SECRET_KEY from the environment instead of a hardcoded value.

## Apply the changes

Create the namespace first if it does not exist:

```bash
kubectl create namespace todoapp
```

Apply the ConfigMap and the Secret before the Deployment, so the Pods can find them:

```bash
kubectl apply -f configMap.yml
kubectl apply -f secret.yml
kubectl apply -f deployment.yml
```

If the Deployment was already running, restart it to pick up the new variables:

```bash
kubectl rollout restart deployment todoapp -n todoapp
```

## Validate the changes

Check that everything was created and the Pods are running:

```bash
kubectl get configmap -n todoapp
kubectl get secret -n todoapp
kubectl get pods -n todoapp
```

Check that the variables are set inside the container (use the real Pod name from the list above):

```bash
kubectl exec -n todoapp <pod-name> -- printenv PYTHONUNBUFFERED
kubectl exec -n todoapp <pod-name> -- printenv SECRET_KEY
```

PYTHONUNBUFFERED should print `1`, and SECRET_KEY should print the value from the Secret.

Check that the Secret value is stored encoded:

```bash
kubectl get secret app-secret -n todoapp -o jsonpath='{.data.SECRET_KEY}' | base64 --decode
```

Check that the app starts without errors:

```bash
kubectl logs -n todoapp <pod-name>
```

## Cleanup

```bash
kubectl delete -f deployment.yml
kubectl delete -f secret.yml
kubectl delete -f configMap.yml
```