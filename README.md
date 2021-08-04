# AWS-KubernetesWithDocker Tutorial

`kubectl`, the cli tool, is also the name of the bash Terminal command that we use to manage our cluster from our virtual testbed, `minkube`. Following are a few commands to get `kubectl` set up.

## Steps

### Step 1 - Download and install kubectl and minkube
Note: The following script has commands for the Linux distro Ubuntu. If you are on another OS, consult https://kubernetes.io/docs/tasks/tools/ for details.
These install commands are for AMD 64-bit machines. Consult the installation steps at https://kubernetes.
io/docs/tasks/tools/install-kubectl-linux/ for your architecture.

- Download kubectl and the checksum to make sure the download checks out. Then install it (you may need sudo and either `apt` or `yum`).
>```curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl```

>```curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"```

>```echo "$(<kubectl.sha256) kubectl" | sha256sum --check```

>```install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl```

- Optional - The following command installs auto completion for ease of use. You can skip adding auto completion if you want.
>```apt install bash-completion```

> ```curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64```

>```kubectl completion bash >/etc/bash_completion.d/kubectl```

Lastly, install minikube (check for the command for your architecture).

>```sudo apt install minikube-linux-amd64 /usr/local/bin/minikube```

Don't forget to reload your terminal after these steps!
