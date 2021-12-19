# Load Balancers
Available after configure a Load Balancer Controller
[url](https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html)

```bash
# Create IAM OIDC provider
eksctl utils associate-iam-oidc-provider --cluster <CLUSTER_NAME> --approve
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.3.0/docs/install/iam_policy.json

# Deploy Controller to EKS cluster
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
eksctl create iamserviceaccount --cluster=<CLUSTER_NAME> --namespace=kube-system --name=aws-load-balancer-controller --attach-policy-arn=ARN:AWS:IAM::111122223333:POLICY/AWSLoadBalancerControllerIAMPolicy --override-existing-serviceaccounts --approve

# Install Controller using Helm
helm repo add eks https://aws.github.io/eks-charts
helm repo update
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=<CLUSTER_NAME> --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller 

# Verify
kubectl get deployment -n kube-system aws-load-balancer-controller
```
## Expose app on AWS Network Load Balancer
type: LoadBalancer with annotations
` kubectl expose deployment deploy-autoscaler --name=nginx-service --dry-run=client -o yaml > service-nlb.yaml `

Add to metadata
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: external
    service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: ip
    service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing

` kubectl apply -f service-nlb.yaml `

## Expose app on AWS Application Load Balancer
type: Ingress (ingress.yaml) with annotations
```bash
kubectl expose deployment deploy-autoscaler --name=alb-nginx-service --dry-run=client -o yaml > service-alb.yaml
kubectl apply -f service-alb.yaml
kubectl apply -f ingress.yaml
```