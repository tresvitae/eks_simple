# Cluster Autoscaler on AWS

eksctl create cluster --config-file=autoscaler.yaml
https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md


## Deploy the autoscaler
the autoscaler itself:
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```

put required annotation to the deployment:
```bash
kubectl -n kube-system annotate deployment.apps/cluster-autoscaler cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

get the autoscaler image version:  
open https://github.com/kubernetes/autoscaler/releases and get the latest release version matching your Kubernetes version, e.g. Kubernetes 1.14 => check for 1.14.n where "n" is the latest release version

edit deployment and set your EKS cluster name:
```bash
kubectl -n kube-system edit deployment.apps/cluster-autoscaler
```

* set the image version at property ```image=k8s.gcr.io/cluster-autoscaler:1.21.x```  replace version in image
* set your EKS cluster name at the end of property ```- --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<<EKS cluster name>>```   your cluster name

## test the autoscaler

### check nodes 
```bash
kubectl get nodes
```

### view cluster autoscaler logs
```bash
kubectl -n kube-system logs deployment.apps/cluster-autoscaler | grep -A5 "Expanding Node Group"

kubectl -n kube-system logs deployment.apps/cluster-autoscaler | grep -A5 "removing node"
```
