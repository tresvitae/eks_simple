# Load Balancers by default

## Expose app on port from every node instance
type: NodePort
```bash
kubectl expose deployment deploy-autoscaler --type=NodePort --name=nginx-service --dry-run=client -o yaml > service.yaml
kubectl apply -f service.yaml
```
` kubectl get svc `
http://localhost:port/

## Expose app on AWS Classic Load Balancer
type: LoadBalancer
```bash
kubectl expose deployment deploy-autoscaler --type=NodePort --name=nginx-service --dry-run=client -o yaml > service.yaml
sed 's/type: NodePort/type: LoadBalancer/' service.yaml > service-clb.yaml
kubectl apply -f service-clb.yaml
```