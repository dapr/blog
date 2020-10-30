---
date: "2020-10-30T00:00:00-00:00"
title: "Dapr on Raspberry Pi with K3s"
linkTitle: "Dapr on Raspberry Pi with K3s"
author: "[Artur Souza](https://github.com/artursouza)"
type: blog
---

Since its announcement over one year ago, Dapr has shown how it can expedite development of cloud native applications via a standard API for common building blocks like pubsub, bindings or method invocation, for example. It is well known that Dapr can be deployed to Kubernetes running via [Minikube](https://docs.dapr.io/operations/hosting/kubernetes/cluster/setup-minikube/), [kind](https://github.com/dapr/dapr/pull/2144), or cloud providers like Azure's [AKS](https://docs.dapr.io/operations/hosting/kubernetes/cluster/setup-aks/). It has also been demonstrated how [Dapr can run on a Kubernetes cluster of Raspberry Pis](https://youtu.be/LAUDVk8PaCY?t=1251).

This post will explain how to deploy Dapr on [Rancher's K3s Kubernetes](https://rancher.com/docs/k3s/latest/en/) on a cluster of Rasperry Pis, showcasing an example of deployment for edge computing.

## Why?

I have used Dapr via Minikube, kind and AKS but I wanted to learn how to setup an on-prem Kubernetes cluster "from scratch" and use Dapr to validate it. In this case, "from scratch" meant purchasing and provisioning the hardware, in addition to installing Kubernetes. The setup also must have more than one node, to uncover challenges of provisioning multiple computers. For this exercise, I used 4 computers (or nodes).

## Planning

Purchasing the hardware was a no brainer, Raspberry Pi would be the go-to solution for a cheap DIY project. I picked the Raspberry Pi 4 with 4GB of RAM since it would give a good memory to CPU ratio: 1x1.5GHz core per GB of RAM. The 2GB version would not give enough memory per node (IMO). Apart from the higher price tag (x4 nodes), the 8GB version seems overkill for this as I guessed that under heavy load (too many pods), the nodes would run hot on CPU utilization before using all the memory. This is a "guestimate", so you can still decide to go with 2GB or 8GB version.

The details of the purchase of Raspberry Pi computer and accessories is outside the scope of this post. There are many blog posts detailing the hardware purchase experience. On the other hand, there are a few items that proved valuable for a hardware setup that are worth mentioning:

* Raspberry Pi can run hot even when idle, so it is recommended to install a cooling fan and heatsink.
* For Rasberry Pi 4 model B, make sure to have an USB-C power supply with an output of 5V and 3A.
* In case of multiple Raspberry Pis:
    - Make sure the surge protector that can plug all the power supplies. A power strip might not work since the power supplies might not all fit next to each other. A cube shaped power supply might be more practical.
    - Check if there are enough ethernet ports in your router for all the computers. An ethernet switch might be needed.

For the Operating System, I picked Ubuntu Server 20.04.1 LTS because it was a well known distribution with a 64 bits server (not desktop) version. At the time of this writing, the Raspberry Pi OS was only in 32 bits. Although no process will address more than 4 GB of RAM, I still want to validate ARM64 images on Dapr. [Flashing the OS image](https://www.raspberrypi.org/documentation/installation/installing-images/) and setting up the static IP + hostname + SSH was a manual step. It can still be automated to some extent but each node needs an unique configuration and each flash card will need to be individually flashed and inserted on the computer.

Next decision was which Kubernetes installation to go with. I was aware of [Rancher's K3s Kubernetes](https://rancher.com/docs/k3s/latest/en/) but also got to read about [Ubuntu's Microk8s](https://microk8s.io) and there is a [blog post on how to build a Raspberry Pi cluster with MicroK8s](https://ubuntu.com/blog/building-a-raspberry-pi-cluster-with-microk8s). Eventually I found Rancher's [Ansible Playbook for K3s](https://github.com/rancher/k3s-ansible) on how to install it on all nodes with minimal manual configuration. Although manually setting up 4 nodes is not the end of the world, I wanted to make the setup as automated as possible.

For those new to [Ansible](https://www.ansible.com) (like me), it automates provisoning and configuration, enabling infrastructure as code. I like Ansible for this job because it only requires the nodes to be accessible via ssh with private key authentication - no need to install an agent or anything else on the nodes. The only downside is that it does not work for Windows, so you need a Linux or MacOS host to kick off the Ansible Playbook.

{{< imgproc picluster Resize "600x" >}}
Raspberry Pi cluster of 4
{{< /imgproc >}}

## Pre-requisites

### Rasberry Pi or ARM64 emulator

As mentioned before, you can use one or more [Rasperry Pi](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/?resellerType=home&variant=raspberry-pi-4-model-b-4gb) computers. Alternativelly, you can use [QEMU](https://www.qemu.org/) to emulate a Raspberry Pi computer. The computers (or virtual machines) must:

* Have GNU/Linux installed. Instructions here assume [Ubuntu Server 20.04.1 LTS](https://ubuntu.com/download/raspberry-pi)
* Be Accessible via [ssh](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) and [authenticated via authorized key](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04).
* Static IP (or static DHCP IP lease) configured on all servers. Instructions here assume: 192.168.1.101 for master, 192.168.1.[102-104] for worker nodes.

#### External References

The following links can serve as a starting point to learn how to build your physical or virtual cluster. These are examples only and are not necessarily endorced by me.

* [Build a Raspberry Pi cluster computer](https://magpi.raspberrypi.org/articles/build-a-raspberry-pi-cluster-computer) by The MagPi Magazine
* [Raspberry Pi Cluster Emulation With Docker Compose](https://appfleet.com/blog/raspberry-pi-cluster-emulation-with-docker-compose/) by appFleet

### GNU/Linux or MacOS computer

Because the setup requires Ansible, Windows is not supported - only GNU/Linux or MacOS. Windows users can still follow instructions via a Virtual Machine running GNU/Linux.

Install the following on your desktop:

* [Git](https://git-scm.com/downloads)
* [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* [Dapr Cli](https://docs.dapr.io/getting-started/install-dapr/#install-the-dapr-cli)

## Step 1: Configure the Ansible Playbook

Clone the Ansible Playbook:

```sh
git clone git@github.com:rancher/k3s-ansible.git
```

Optionally, reset to the same revision used to create these instructions:
```sh
git checkout 721c3487027e42d30c60eb206e0fb5abfddd094f
```

Then, configure your cluster. Your IP addresses might be different from the configuration below:

```
cp -R inventory/sample inventory/my-cluster
cat << EOF | tee inventory/my-cluster/hosts.ini
[master]
192.168.1.101

[node]
192.168.1.[102:104]

[k3s_cluster:children]
master
node
EOF
```

Update the username used in the cluster. Default is `debian`. Since we are using Ubuntu, this will be updated to `ubuntu`:

On GNU/Linux:
```sh
sed -i 's/debian/ubuntu/'  inventory/my-cluster/group_vars/all.yml
```

On MacOS:
```sh
sed -i .bak 's/debian/ubuntu/'  inventory/my-cluster/group_vars/all.yml
```

## Step 2: Install K3s via Ansible Playbook

Run the Ansible Playbook to have K3s installed on your cluster.
```sh
ansible-playbook site.yml -i inventory/my-cluster/hosts.ini
```

Once completed, you shold see an output that ends like this:
```txt
=============================================================================== 
k3s/master : Enable and check K3s service ------------------------------------------------------------------------------------------------------------------------------- 24.58s
Gathering Facts --------------------------------------------------------------------------------------------------------------------------------------------------------- 10.48s
k3s/node : Enable and check K3s service ---------------------------------------------------------------------------------------------------------------------------------- 8.49s
Gathering Facts ---------------------------------------------------------------------------------------------------------------------------------------------------------- 8.07s
Gathering Facts ---------------------------------------------------------------------------------------------------------------------------------------------------------- 7.13s
download : Download k3s binary arm64 ------------------------------------------------------------------------------------------------------------------------------------- 6.76s
k3s/master : Change file access node-token ------------------------------------------------------------------------------------------------------------------------------- 3.53s
k3s/master : Copy K3s service file --------------------------------------------------------------------------------------------------------------------------------------- 2.03s
raspberrypi : Test for raspberry pi /proc/device-tree/model -------------------------------------------------------------------------------------------------------------- 1.91s
k3s/node : Copy K3s service file ----------------------------------------------------------------------------------------------------------------------------------------- 1.86s
k3s/master : Replace https://localhost:6443 by https://master-ip:6443 ---------------------------------------------------------------------------------------------------- 1.85s
k3s/master : Copy config file to user home directory --------------------------------------------------------------------------------------------------------------------- 1.35s
k3s/master : Read node-token from master --------------------------------------------------------------------------------------------------------------------------------- 1.17s
k3s/master : Wait for node-token ----------------------------------------------------------------------------------------------------------------------------------------- 1.13s
k3s/master : Create crictl symlink --------------------------------------------------------------------------------------------------------------------------------------- 1.10s
k3s/master : Restore node-token file access ------------------------------------------------------------------------------------------------------------------------------ 1.07s
k3s/master : Create directory .kube -------------------------------------------------------------------------------------------------------------------------------------- 1.01s
k3s/master : Create kubectl symlink -------------------------------------------------------------------------------------------------------------------------------------- 1.01s
raspberrypi : Test for raspberry pi /proc/cpuinfo ------------------------------------------------------------------------------------------------------------------------ 0.96s
prereq : Enable IPv6 forwarding ------------------------------------------------------------------------------------------------------------------------------------------ 0.92s
```

Copy the cluster config from the master node (your cluster's master IP address might differ).
```sh
scp ubuntu@192.168.1.101:~/.kube/config ~/.kube/piconfig
```

Export the environment variable below to use the new cluster:
```sh
export KUBECONFIG=~/.kube/piconfig
```

Optionally, you can merge `~/.kube/piconfig` into `~/.kube/config`:
```sh
KUBECONFIG=~/.kube/config:~/.kube/piconfig kubectl config view --flatten | tee ~/.kube/config && kubectl config use-context default
```

Finally, list all the nodes in the new cluster:
```sh
kubectl get nodes
```

Output should be similar to this (depending on your cluster setup):
```txt
NAME        STATUS   ROLES    AGE   VERSION
raspi-003   Ready    <none>   11m   v1.17.5+k3s1
raspi-002   Ready    <none>   11m   v1.17.5+k3s1
raspi-004   Ready    <none>   11m   v1.17.5+k3s1
raspi-001   Ready    master   11m   v1.17.5+k3s1
```

## Step 3: Optionally, install Kubernetes Dashboard

Deploy Kubernetes Dashboard:
```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
```

Create credentials for Kubernetes Dashboard:
```sh
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF

cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

Copy the token displayed by the command below into your clipboard:
```sh
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```

On a new terminal, start Kubernetes proxy:
```sh
KUBECONFIG=~/.kube/piconfig kubectl proxy
# Note: environment variable KUBECONFIG is probably not setup in a new terminal window.
```

Now, open the following URL on your browser: [http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

On the first screen on the Kubernetes Dashboard, you are expected to paste the token previously copied.

## Step 4: Install Dapr and Apps

Deploy Dapr on your cluster:
```sh
dapr init -k
```

After a few minutes, check the status (dapr-operator might take longer than the other services to be healthy):
```sh
dapr status -k
```

```txt
  NAME                   NAMESPACE    HEALTHY  STATUS   REPLICAS  VERSION  AGE  CREATED              
  dapr-placement         dapr-system  True     Running  1         0.11.3   1m   2020-10-23 15:56.15  
  dapr-sidecar-injector  dapr-system  True     Running  1         0.11.3   1m   2020-10-23 15:56.15  
  dapr-dashboard         dapr-system  True     Running  1         0.3.0    1m   2020-10-23 15:56.15  
  dapr-operator          dapr-system  True     Running  1         0.11.3   1m   2020-10-23 15:56.15  
  dapr-sentry            dapr-system  True     Running  1         0.11.3   1m   2020-10-23 15:56.15
```

On a separate terminal, open Dapr dashboard, so you can check the status of the apps on Dapr.
```sh
KUBECONFIG=~/.kube/piconfig dapr dashboard -k
# Note: environment variable KUBECONFIG is probably not setup in a new terminal window.
```

Back on your first terminal window, follow [these instructions](https://github.com/dapr/quickstarts/tree/master/hello-kubernetes) to deploy an app using Dapr on Kubernetes. Don't forget to check back on the Dapr Dashboard on your browser's window.

## Step 5: Clean Up

In case you don't want to keep this setup in your cluster (or want to redo it), uninstall K3s with the following Ansible Playbook:

```sh
ansible-playbook reset.yml -i inventory/my-cluster/hosts.ini
```

## Thank You

Thanks a lot for trying Dapr on Raspberry Pi with K3s.