
# ipfs-kubernetes
In life, video and log data are very large, and in order to effectively preserve and manage them, the environment of edge, fog, and cloud computing must be included. Therefore, the goal of this environment is to develop non-reversive storage management technologies based on blockchain and ipfs in a scalable cloud computing environment.


# Overview
This is an example of kubernetes between ipfs nodes. In this example, there are three nodes:

- master node (mac-mini)
- worker node1 (mac-mini)
- worker node2 (pi)

Nodes with name worker node1 and worker node2 running as a child process in kubernetes. Master node handle to the go-ipfs that start daemon with pub-sub-experiment flag using CLI command. 

# Requirements
- ubuntu LTS 18.04
- kubernetes 1.13
- docker 18.09.5
- go 1.12
- go-ipfs 0.4.19

# Installation

- Docker Install
```sh
$ curl -sSL get.docker.com | sh && \ 
sudo usermod -aG docker $USER \
newgrp docker
```
If it doesn't work, we can use this:
```sh
$ sudo usermod NAME -aG docker
$ sudo reboot
```

- Disable Swap and * Edit `/boot/cmdline.txt`
```sh
$ sudo swapoff -a
$ sudo dphys-swapfile swapoff && \
sudo dphys-swapfile uninstall && \
sudo update-rc.d dphys-swapfile remove
$ echo Adding "cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory" to /boot/cmdline.txt
```
- Now reboot!! do not skip this step.


- Add repo list and install `kubeadm`
```sh
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - && \
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list && \
sudo apt-get update -q && \
sudo apt-get install -qy kubeadm 
```

### Initialize your master node

* You now have two new commands installed:
 * kubeadm - used to create new clusters or join an existing one
 * kubectl - the CLI administration tool for Kubernetes

* Pre-pull images

`kubeadm` now has a command to pre-pull the requisites Docker images needed to run a Kubernetes master, type in:

```sh
$ sudo kubeadm config images pull -v3
```
If using Flannel:

* Initialize your master node with a Pod network CIDR:

```sh
$ sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.43.0.0/16
```
After the `init` is complete run the snippet given to you on the command-line:

```sh
$ mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Your join token is valid for 24 hours, so save it into a text file. Here's an example of mine:

```sh
$ sudo kubeadm join --ignore-preflight-errors=all wc0mxl.tw2chfnr3hxf8b54 --discovery-token-ca-cert-hash 192.168.1.48:6443 sha256:0e1806591201ba365c5c04d93d12788675be98b8c5bbe10f5541f2644e720f23 
```
If you do not remember the join, check the kubeadm join history again:
```sh
$ sudo kubeadm token create --print-join-command
``

