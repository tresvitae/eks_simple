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



