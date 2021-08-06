# AWS EKS - Decentralized load-balancing with Kubernetes

### In this activity, we will use Amazon's Elastic Kubernetes Service to load-balance an application with on-premise nodes (on your computer) and AWS nodes. 

## Steps


- Create an OIDC (OpenID Connect) provider for your cluster
  - aws eks describe-cluster --name activity30 --query "cluster.identity.oidc.issuer" --output text
    - output looks like:
      > https[]()://oidc.eks.us-west-1.amazonaws.com/id/291A5E8FBD7F1B56614ABE6FDA67BD73
  - eksctl utils associate-iam-oidc-provider --cluster activity30 --approve
  - aws iam list-open-id-connect-providers | grep 291A5E8FBD7F1B56614ABE6FDA67BD73
    - "Arn": "arn:aws:iam::971507148491:oidc-provider/oidc.eks.us-west-1.amazonaws.com/id/291A5E8FBD7F1B56614ABE6FDA67BD73"

- Deploy the AWS Load Balancer Controller to the Amazon EKS cluster
  - curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.2.0/docs/install/iam_policy.json
  - > ```
    > aws iam create-policy \
    > --policy-name AWSLoadBalancerControllerIAMPolicy \
    > --policy-document file://iam_policy.json
    > ```
    - Response should look like this: 
      ```
      {
          "Policy": {
              "PolicyName": "AWSLoadBalancerControllerIAMPolicy",
              "PolicyId": "ANPA6EMSWU3FQXU54ZP2H",
              "Arn": "arn:aws:iam::971507148491:policy/AWSLoadBalancerControllerIAMPolicy",
              "Path": "/",
              "DefaultVersionId": "v1",
              "AttachmentCount": 0,
              "PermissionsBoundaryUsageCount": 0,
              "IsAttachable": true,
              "CreateDate": "2021-08-05T13:35:20Z",
              "UpdateDate": "2021-08-05T13:35:20Z"
          }
      }
      ```
  - Create the IAM Service Account for this cluster with your AWS ID number
    ``` 
      eksctl create iamserviceaccount \
      --cluster=activity30 \
      --namespace=kube-system \
      --name=aws-load-balancer-controller \
      --attach-policy-arn=arn:aws:iam::<your_aws_id>:policy/AWSLoadBalancerControllerIAMPolicy \
      --override-existing-serviceaccounts \
      --approve          
      ```
  - We need to install helmV3 (the package manager for K8s). Follow the instructions [here](https://helm.sh/docs/intro/install/).
  
    - ```
      helm repo add eks https://aws.github.io/eks-charts
      ```
    - ```
      helm upgrade -i aws-load-balancer-controller eks/aws-load-balancer-controller \
        --set clusterName=activity30 \
        --set serviceAccount.create=false \
        --set serviceAccount.name=aws-load-balancer-controller \
        -n kube-system
      ```





ignore these
- docker pull  galacticwafer/fahrenheit2celsius
- docker container run -d -it --name activityKLB galacticwafer/fahrenheit2celsius
  - c3ed5b182810d721c4edcef7fcc56c88f4ffd39ee8504273c68a6cf0874c2a96
  

```
echo '{"apiVersion": "v1","kind": "Namespace","metadata": {"name": "activity30","labels": {"name": "activity30"}}}' | jq . >> activity30_namespace.json
kubectl create -f activity30_namespace.json
rm activity30_namespace.json
``` 
