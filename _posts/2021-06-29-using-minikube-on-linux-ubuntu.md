---
layout: post
title: Using Minikube on Linux (Ubuntu)
date: 2021-06-29 16:47 +0530
---

Minikube quickly sets up a local Kubernetes cluster, which is great for learning!

These are the pre-requisites, as of today, as per the minikube documentation:

> 2 CPUs or more  
> 2GB of free memory  
> 20GB of free disk space  
> Internet connection  
> Container or virtual machine manager, such as: Docker, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMWare

Follow these steps to get started with minikube -

### Installation

```sudo apt update```

Use ```arch``` command to get the architecture:  
In our case, it is: __x86_64__

#### Install minikube

```curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb``` 
```sudo dpkg -i minikube_latest_amd64.deb```

[Install minikube on x86-64 Linux using Debian package](https://minikube.sigs.k8s.io/docs/start/)

If you see the below error, you would need to install one of the Supported Drivers for Minikube. In this case, we will be using Docker.

```TEXT
$ minikube start
😄  minikube v1.21.0 on Ubuntu 20.04
👎  Unable to pick a default driver. Here is what was considered, in preference order:
    ▪ docker: Not installed: exec: "docker": executable file not found in $PATH
    ▪ kvm2: Not installed: exec: "virsh": executable file not found in $PATH
    ▪ vmware: Not installed: exec: "docker-machine-driver-vmware": executable file not found in $PATH
    ▪ podman: Not installed: exec: "podman": executable file not found in $PATH
    ▪ virtualbox: Not installed: unable to find VBoxManage in $PATH

❌  Exiting due to DRV_NOT_DETECTED: No possible driver was detected. Try specifying --driver, or see https://minikube.sigs.k8s.io/docs/start/
```

#### Install Docker

Install docker from here: [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

We would get the following error if we try to start minkube now:

```text
$ sudo minikube start
😄  minikube v1.21.0 on Ubuntu 20.04
✨  Automatically selected the docker driver. Other choices: none, ssh
🛑  The "docker" driver should not be used with root privileges.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/

❌  Exiting due to DRV_AS_ROOT: The "docker" driver should not be used with root privileges.

```

#### Manage Docker as non-root user

Follow the steps mentioned here to manage Docker as a non-root user: [https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)

You are ready now ready to start your minikube single node cluster.

### Using Minikube

#### Start Minikube

```bash
$ minikube start
😄  minikube v1.21.0 on Ubuntu 20.04
✨  Automatically selected the docker driver. Other choices: none, ssh
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.20.7 preload ...
    > preloaded-images-k8s-v11-v1...: 492.20 MiB / 492.20 MiB  100.00% 97.36 Mi
    > gcr.io/k8s-minikube/kicbase...: 359.09 MiB / 359.09 MiB  100.00% 12.86 Mi

🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.20.7 on Docker 20.10.7 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

#### Test

You can now use the minikube kubectl command to test if everything works.

```bash
$ minikube kubectl -- get pods -A
    > kubectl.sha256: 64 B / 64 B [--------------------------] 100.00% ? p/s 0s
    > kubectl: 38.36 MiB / 38.36 MiB [-------------] 100.00% 1.09 GiB p/s 200ms
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-74ff55c5b-ht448            0/1     Running   0          31s
kube-system   etcd-minikube                      0/1     Running   0          46s
kube-system   kube-apiserver-minikube            1/1     Running   0          46s
kube-system   kube-controller-manager-minikube   0/1     Running   0          46s
kube-system   kube-proxy-pq5hc                   1/1     Running   0          31s
kube-system   kube-scheduler-minikube            0/1     Running   0          46s
kube-system   storage-provisioner                1/1     Running   0          44s
```

#### Install kubectl

The most common way to connect with you Kubernetes Cluster is by using kubectl command line tool.

Install Kubectl from here: [LINK](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

#### Use kubectl

```bash
$ kubectl get pods -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-74ff55c5b-ht448            1/1     Running   0          2m27s
kube-system   etcd-minikube                      1/1     Running   0          2m42s
kube-system   kube-apiserver-minikube            1/1     Running   0          2m42s
kube-system   kube-controller-manager-minikube   1/1     Running   0          2m42s
kube-system   kube-proxy-pq5hc                   1/1     Running   0          2m27s
kube-system   kube-scheduler-minikube            1/1     Running   0          2m42s
kube-system   storage-provisioner                1/1     Running   1          2m40s
```

#### Other useful commands

Access the Minikube dashboard remotely:  
```kubectl proxy --address='0.0.0.0' --disable-filter=true```

Get dashboard URL:  
```minikube dashboard --url```
