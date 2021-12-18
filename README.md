# eks_simple

## Table of contents
* [On-demand](cluster/cluster1.yaml)
* [AWS Node Termination Handler](#node-termination-handler)
* [Kubernetes Metrics & Dashboard](#kubernetes-metrics-dashboard)
* [Prometheus](#prometheus)
* [Grafana](#grafana)

## AWS Node Termination Handler
[url](https://github.com/aws/aws-node-termination-handler)
`kubectl apply -f https://github.com/aws/aws-node-termination-handler/releases/download/v1.14.0/all-resources.yaml`

## Kubernetes Metrics & Dashboard
1) https://github.com/kubernetes-sigs/metrics-server
2) https://github.com/kubernetes/dashboard


Deploy Kubernetes Metrics Server:
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml


Deploy Kubernetes Dashboard:

check: https://github.com/kubernetes/dashboard/releases
export VERSION=v2.4.0
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/$VERSION/aio/deploy/recommended.yaml

admin-service-account.yaml:
apiVersion: v1
kind: ServiceAccount
metadata:
  name: eks-course-admin
  namespace: kube-system

---
  
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: eks-course-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: eks-course-admin
  namespace: kube-system


kubectl apply -f admin-service-account.yaml
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-course-admin | awk '{print $1}')
kubectl proxy
http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/



Remove the admin ServiceAccount and ClusterRoleBinding:
kubectl -n kubernetes-dashboard delete serviceaccount eks-course-admin
kubectl -n kubernetes-dashboard delete clusterrolebinding eks-course-admin


## Prometheus
Since eksctl out of the box adds storage class "gp2" we will use this for Prometheus data storage.  
In real world scenarios you may use IO optmized storage, like e.g. _io1_ , for better performance.

```bash
kubectl create namespace prometheus
helm install prometheus stable/prometheus \
    --namespace prometheus \
    --set alertmanager.persistentVolume.storageClass="gp2" \
    --set server.persistentVolume.storageClass="gp2"
```
check:  
```kubectl get svc -n prometheus```
...and grab the port from the output !

set port forwarding to access Prometheus UI:

```bash
kubectl port-forward svc/prometheus-server 8888:80
```

access Prometheus UI from your *local* browser:
* http://localhost:8888



## Grafana
```bash
kubectl create namespace grafana
helm install grafana stable/grafana \
    --namespace grafana \
    --set persistence.storageClassName="gp2" \
    --set adminPassword='GrafanaAdm!n' \
    --set datasources."datasources\.yaml".apiVersion=1 \
    --set datasources."datasources\.yaml".datasources[0].name=Prometheus \
    --set datasources."datasources\.yaml".datasources[0].type=prometheus \
    --set datasources."datasources\.yaml".datasources[0].url=http://prometheus-server.prometheus.svc.cluster.local \
    --set datasources."datasources\.yaml".datasources[0].access=proxy \
    --set datasources."datasources\.yaml".datasources[0].isDefault=true \
    --set service.type=LoadBalancer
```

get admin password for logging in

```bash
kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

open Grafana

grab the ELB URL from the Grafana Loadbalancer, and open it in the browser
```kubectl get svc - grafana grafana```

### import dashboard(s)
* goto https://grafana.com/grafana/dashboards and select the *ID* of the dashboard you want to import  , pick 'Cluster Monitoring for Kubernetes'
* in Grafana click on *+* , click *Import* and put the *ID* from the previous step into the text field
