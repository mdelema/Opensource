### Install Minikube

##### 1) Update Your System
Before starting the minikube installation, it is recommended to install all available updates on your system. Run following command.
```ssh
$ sudo apt update
$ sudo apt upgrade -y
```
Once all the updates are installed then reboot your system.
```ssh
$ sudo reboot
```

##### 2) Install Docker
Minikube requires either docker or VirtualBox, in this post, we will be installing docker on Ubuntu 22.04 system. Run the following set of command one after the another to docker apt repository.
```ssh
$ sudo apt install ca-certificates curl gnupg wget apt-transport-https -y
$ sudo install -m 0755 -d /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ sudo chmod a+r /etc/apt/keyrings/docker.gpg
$ echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt update
```

Next, install docker by running the following command.
```ssh
$ sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Add your local user to docker group so that your local user run docker commands without sudo.
```ssh
$ sudo usermod -aG docker $USER
$ newgrp docker
```

##### 3) Download and Install Minikube Binary

To download and install minikube binary, run following commands,
```ssh
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
To verify the minikube version, run
```ssh
$ minikube version
```

##### 4) Install Kubectl tool
Kubectl is a command line tool, used to interact with your Kubernetes cluster. So, to install kubectl run beneath curl command.
```ssh
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```

Next, set the executable permission on it and move to /usr/local/bin
```ssh
$ chmod +x kubectl
$ sudo mv kubectl /usr/local/bin/
```
Verify the kubectl version, run
```ssh
$  kubectl version -o yaml
```

##### 5) Start Minikube Cluster
Now that Minikube is installed, start a Kubernetes cluster using the following command:
```ssh
$ minikube start --driver=docker
```

Once the minikube has started, verify the status of your cluster, run
```ssh
$ minikube status
```

##### 6) Interact with Your Minikube Cluster
Use kubectl to interact with your Minikube Kubernetes cluster. For example, you can check the nodes in your cluster:
```ssh
$ kubectl get nodes
$ kubectl cluster-info
```


Try to deploy a sample nginx deployment, run following set of commands.
```ssh
$ kubectl create deployment nginx-web --image=nginx
$ kubectl expose deployment nginx-web --type NodePort --port=80
$ kubectl get deployment,pod,svc
```


##### 7) Managing Minikube Addons
If you want to add some additional functionality toy Kubernetes cluster like Kubernetes dashboard, ingress controller and more. You can enable these with addons. To view all the available addons, run
```ssh
$ minikube addons list
```

In order to enable addons, run
```ssh
$ minikube addons enable dashboard
$ minikube addons enable ingress
```

To start the Kubernetes dashboard run below command, it will automatically launch the dashboard in the web browser as shown below:
```ssh
$ minikube dashboard
```

##### 8) Managing Minikube Cluster
To stop and start the minikube cluster, run beneath commands.
```ssh
$ minikube stop
$ minikube start
```
In order to delete the minikube cluster, run
```ssh
$ minikube delete
```