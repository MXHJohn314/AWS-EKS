### In this activity, we set up all the tools for working with Kubernetes locally.

#### NOTE!</br>make sure you have a hypervisor (such as [hyperkit](https://github.com/moby/hyperkit) for Linux and MacOS, or in Windows, the [Windows Subsystem for Linux [WSL]](https://docs.microsoft.com/en-us/windows/wsl/install-win10)) or virtual machine platform (such as [VirtualBox](https://www.virtualbox.org/)) installed on your computer before continuing with this tutorial. You must have a virtual machine or hypervisor to continue!

`kubectl`, the cli tool, is also the name of the bash Terminal command that we use to manage our cluster from our virtual testbed, `minkube`. Following are a few commands to get `kubectl` set up.

## Steps

### Step 1 - Download and install kubectl, minkube, and eksctl
Note: The following script has commands for the Linux distro Ubuntu on an AMD 64-bit machine. If you have a different computer architecture, consult the installation steps [here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) for your architecture.

- Download kubectl and the checksum to make sure the download checks out. Then install it (you may need sudo and either `apt` or `yum`). Also note that your commands will differ slightly for different computer achitecture. Check [here]() to make sure.
  ```curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"```
  - >```curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"```
  - >```echo "$(<kubectl.sha256) kubectl" | sha256sum --check```
  - >```install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl```

- Optional - The following command installs auto completion for ease of use. You can skip adding auto completion if you want.
  - >```apt install bash-completion```
  - > ```curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64```
  - >```kubectl completion bash >/etc/bash_completion.d/kubectl```

- Install minikube (check for the command for your architecture).
  - >```sudo apt install minikube-linux-amd64 /usr/local/bin/minikube```
    
- Lastly, install eksctl following the instructions [here](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html)
- **Don't forget to reload your terminal after these steps!**

### Step 2 - Start a minikube cluster
- For WSL:</br>
  - >```minikube start```
- For MacOS or Linux:</br> 
  ```--hypervisor=<your_hypervisor>```. For example, if you installed `hyperkit`:
  - >```minikube start --hypervisor=hyperkit```

- Create an OIDC (OpenID Connect) provider for your cluster
  - aws eks describe-cluster --name activity30 --query "cluster.identity.oidc.issuer" --output text
    - https://oidc.eks.us-west-1.amazonaws.com/id/291A5E8FBD7F1B56614ABE6FDA67BD73
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
  - We need to install helmV3 (the package manager for Kubernetes). Follow the instructions [here](https://helm.sh/docs/intro/install/).
  
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
