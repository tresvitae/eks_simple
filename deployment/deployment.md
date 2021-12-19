### create a deployment of nginx

```bash
kubectl apply -f nginx-deployment.yaml
```

### scale the deployment

```bash
kubectl scale --replicas=3 deployment/deploy-autoscaler
```

### check pods

```bash
kubectl get pods -o wide --watch
```