# eks_simple

## Table of contents
* [On-demand](cluster/cluster1.yaml)
` eksctl create cluster -f cluster1.yaml`
* [50% On-demand & spot instances](cluster/cluster2.yaml)
` eksctl create nodegroup -f cluster2.yaml --include='ng-mixed' `
* [Managed spot instances](cluster/spot-cluster.yaml)
* [Unmanaged Nodegroups](#unmanaged-nodegroups)
* [Cluster Autoscaler](cluster/cluster-autoscaler.md)
* [Scale](#scale)
* [Nginx Deployment](deployment/deployment.md)
* [Delete](#delete)
* [Managing Users & RBAC](rbac/rbac.md)
* [AWS Node Termination Handler](#aws-node-termination-handler)
* [Kubernetes Metrics & Dashboard](monitoring/metrics-dashboard.md)
* [Prometheus](monitoring/prometheus.md)
* [Grafana](monitoring/grafana.md)

## Unmanaged Nodegroups
Spot allocation strategy options:
spotAllocationStrategy: "capacity-optimized"
spotAllocationStrategy: "capacity-optimized-prioritized"
By default: "lowest-price" strategy
## Scale
```bash
eksctl scale nodegroup --cluster=my-cluster --nodes=4 --name=ng-1
```
## Delete
```bash
eksctl delete nodegroup --cluster=my-cluster --name=ng-mixed
eksctl delete clusterr -f cluster.yaml
```
## AWS Node Termination Handler
[url](https://github.com/aws/aws-node-termination-handler)
`kubectl apply -f https://github.com/aws/aws-node-termination-handler/releases/download/v1.14.0/all-resources.yaml`
