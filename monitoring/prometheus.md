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