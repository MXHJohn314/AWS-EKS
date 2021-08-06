## Activity 30

### AWS EKS - Decentralized load-balancing with Kubernetes

#### In this activity, we set up all the tools for working with Kubernetes (also referred to as K8s) locally.

#### NOTE!</br>make sure you have DOcker installed, along with one of the following set of reqirements:
 - for Linux and MacOS:
   - a hypervisor (like [hyperkit](https://github.com/moby/hyperkit))
 - Winows:
   - [Windows Subsystem for Linux [WSL]](https://docs.microsoft.com/en-us/windows/wsl/install-win10) 
   - Docker with "Install required Windows Components for WSL 2" checked upon installation

`kubectl`, the cli tool, is also the name of the bash Terminal command that we use to manage our cluster from our virtual testbed, `minkube`. Following are a few commands to get `kubectl` set up.

### Steps

### Step 1 - Download and install `kubectl`, `minkube`, and `eksctl`
Note: The following script has commands for the Linux distro Ubuntu on an AMD 64-bit machine. If you have a different computer architecture, consult the installation steps [here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) for your architecture.

- Make sure you are updated and upgraded

  - ```
    sudo apt update -y && sudo apt upgrade -y
    ```
    
- Download `kubectl` and the checksum to make sure the download checks out. Then install it (you may need sudo and either `apt` or `yum`). Also note that your commands will differ slightly for different computer achitecture. Check [here]() to make sure.

  - ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```

  -  ```
     curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
     ```

  -  ```
     echo "$(<kubectl.sha256) kubectl" | sha256sum --check
     ```

  -  ```
     sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
     ```

  -  Optional - The following command installs auto completion for ease of use. You can skip adding auto completion if you want.
     - ```
       sudo apt install bash-completion
       ```
     
     - ```
       curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
       ```
     
     - ```
       kubectl completion bash >/etc/bash_completion.d/kubectl
       ```

- Install `minikube` (check for the commands [here](https://minikube.sigs.k8s.io/docs/start/) for your OS).
  - ```
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    sudo install minikube-linux-amd64 /usr/local/bin/minikube
    ```
    
- Install `eksctl` following the instructions seen [here](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html) for your OS
  - ```
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
    ```
    
  - ```
    sudo mv /tmp/eksctl /usr/local/bin
    ```
  
  - ```
    eksctl version
    ```
    
- **Reload your terminal at this point.**

### Step 2 - Start some clusters
- For WSL 2 with Docker's compatibility layer installed:</br>
  - ```
    minikube start
    ```
- For hypervisor use:</br> 
  ```--hypervisor=<your_hypervisor>```. For example, if you installed `hyperkit`:
  - ```
    minikube start --hypervisor=hyperkit
    ```

- Make an EKS cluster in the cloud
  - ```
    eksctl create cluster \
    --name activity30v2 \ 
    --region us-west-1 \ 
    --nodegroup-name linux-nodes \ 
    --node-type t2.micro \
    --nodes 2
    ```
