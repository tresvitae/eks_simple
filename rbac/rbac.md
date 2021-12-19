# Providing RBAC to IAM users

## Add a cluster admin

1. Create 'clusteradmin' IAM user with credential keys
2. Add user to ~/.aws/credentials by creating a new section as 'k8sclusteradmin'
3. Fetch current configmap before adding our user mapping
```bash
kubectl -n kube-system get configmap aws-auth -o yaml > aws-auth-configmap.yaml
```
4. Edit the yaml file and add a "mapUsers" section
```bash
  mapUsers: |
    - userarn: arn:aws:iam::xxxxxxxxx:user/cluster-admin
      username: cluster-admin
      groups:
        - system:masters
```
5. Check which user is currently active
```bash
aws sts get-caller-identity
export AWS_PROFILE="k8sclusteradmin"
aws sts get-caller-identity
```

## Add read-only user for particular namespace
```bash
kubectl create namespace production
```

1. Create 'prod-viewer' IAM user and grab access-key
2. Add user to ~/.aws/credentials by creating a new section as 'k8sproductionviewer'
3. Create a role
` kubectl apply -f role.yaml `
4. Create a rolebinding
` kubectl apply -f rolebinding.yaml `
5. Add AWS user to aws-auth configmap
` kubectl -n kube-system get configmap aws-auth -o yaml > aws-auth-configmap.yaml `

    - userarn: arn:aws:iam::xxxxxxxxx:user/prod-viewer
      username: prod-viewer ## AWS User name
      groups:
        - prod-viewer-role  ## K8s role.yaml

` kubectl apply -f aws-auth-configmap.yaml `

6. Set this user as the active one
```bash
aws sts get-caller-identity
export AWS_PROFILE="k8sproductionviewer"
aws sts get-caller-identity
```

## Test
```
kubectl get po
kubectl -n production get po
kubectl run nginx --image=nginx --restart=Never -n production
```