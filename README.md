# eks_simple

## Table of contents
* [On-demand](cluster/cluster1.yaml)
` eksctl create cluster -f cluster1.yaml`
* [50% On-demand & spot instances](cluster/cluster2.yaml)
` eksctl create nodegroup -f cluster2.yaml --include='ng-mixed' `
* [AWS Node Termination Handler](#node-termination-handler)
* [Kubernetes Metrics & Dashboard](monitoring/metrics-dashboard.md)
* [Prometheus](monitoring/prometheus.md)
* [Grafana](monitoring/grafana.md)

## AWS Node Termination Handler
[url](https://github.com/aws/aws-node-termination-handler)
`kubectl apply -f https://github.com/aws/aws-node-termination-handler/releases/download/v1.14.0/all-resources.yaml`
