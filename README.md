
# ipfs-kubernetes or Docker Swarm
In life, video and log data are very large, and in order to effectively preserve and manage them, the environment of edge, fog, and cloud computing must be included. Therefore, the goal of this environment is to develop non-reversive storage management technologies based on blockchain and ipfs in a scalable cloud computing environment.


# Overview
This is an example of kubernetes between ipfs nodes. In this example, there are three nodes:

- master node (mac-mini)
- worker node1 (mac-mini)
- worker node2 (pi)

Nodes with name worker node1 and worker node2 running as a child process in kubernetes. Master node handle to the go-ipfs that start daemon with pub-sub-experiment flag using CLI command. When you want docker swarm, please click private-network folder.

# Requirements
- ubuntu LTS 18.04
- kubernetes 1.13
- docker 18.09.5
- go 1.12
- go-ipfs 0.4.19

Sharing url:
https://github.com/alexellis/k8s-on-raspbian

Static network ip for mac mini and raspberry pi:
https://altongmon.tistory.com/637, https://hiseon.me/2018/09/06/ubuntu-18-04-netplan/

# Installation

- Docker Install and update
```sh
curl -sSL get.docker.com | sh && \ 
sudo usermod -aG docker $USER \
sudo newgrp docker \
sudo apt-get update && sudo apt-get upgrade -y
```
If it doesn't work, we can use this:
```sh
sudo usermod NAME -aG docker
sudo reboot
```
Disable Swap and * Edit `/boot/cmdline.txt`
```sh
sudo swapoff -a
sudo dphys-swapfile swapoff && \
sudo dphys-swapfile uninstall && \
sudo update-rc.d dphys-swapfile remove
echo Adding "cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory" to /boot/cmdline.txt
```
- Now reboot!! do not skip this step.


- Add repo list and install `kubeadm`
```sh
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - && \
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list && \
sudo apt-get update -q && \
sudo apt-get install -qy kubeadm 
```

## Initialize your master node

 * You now have two new commands installed:
 * kubeadm - used to create new clusters or join an existing one
 * kubectl - the CLI administration tool for Kubernetes

* Pre-pull images

`kubeadm` now has a command to pre-pull the requisites Docker images needed to run a Kubernetes master, type in:

```sh
sudo kubeadm config images pull -v3
```
If using Flannel:

* Initialize your master node with a Pod network CIDR:

```sh
sudo kubeadm init --token-ttl=0 --pod-network-cidr=10.43.0.0/16
```
If using Weave Net

* Initialize your master node:

```sh
sudo kubeadm init --token-ttl=0
```
After the `init` is complete run the snippet given to you on the command-line:

```sh
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Your join token is valid for 24 hours, so save it into a text file. Here's an example of mine:

```sh
sudo kubeadm join 192.168.1.49:6443 --token 23oulg.on7eyrbnetdyolp8 --discovery-token-ca-cert-hash sha256:6a583e6385e895971dcc9dc78505e14c1ba6f429f66aee53ab1ac3d4abac733d
```
If you do not remember the join, check the kubeadm join history again ([preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/ error execution phase preflight: couldn't validate the identity of the API Server: expected a 32 byte SHA-256 hash, found 29 bytes):
```sh
sudo kubeadm token create --print-join-command
```
When a master node uses `$ kubectl get nodes`, we see `Not Ready`. By default, your cluster will not schedule pods on the master for security reasons. If you want to be able to schedule pods on the master, run: 
 ```sh
kubectl taint nodes --all node-role.kubernetes.io/master-
```
Install flannel 
```sh
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
If not install flannel
```sh
kubectl -n kube-system apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```
## Initialize your Work node
On each node that joins including the master:
```sh
sudo sysctl net.bridge.bridge-nf-call-iptables=1
```

```
$ kubectl get nodes
NAME            STATUS   ROLES    AGE   VERSION
23ipfscluster   Ready    <none>   25d   v1.14.1
wrl-01macmini   Ready    master   36d   v1.13.4
wrl-02macmini   Ready    <none>   25d   v1.14.1
```

## Initialize Dashboard

Sharing url:
https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/dashboard-tutorial.html

## Initialized Docker-ipfs for kubernetes

Containers packaged with all dependencies and software for IoT applications are portable, light and secure. Containerized IoT applications/services deployed using Docker containers:
```sh
sudo docker run -d -it --name docker-ipfs -v $ipfs_staging:/export -v $ipfs_data:/data/ipfs -p 4002:4002 -p 127.0.0.1:8081:8081 -p 127.0.0.1:5002:5002 shapna/go-ipfs_node1:Node1
```
Developled IoT services with P2P pubsub communication model based on Interplanetary File System (IPFS):
```sh
sudo docker run -d -it --name docker-ipfs -v $ipfs_staging:/export -v $ipfs_data:/data/ipfs -p 4003:4003 -p 127.0.0.1:8082:8082 -p 127.0.0.1:5003:5003 shapna/go-ipfs_node1:Node1 --enable-pubsub-experiment
```
containerized ipfs in raspberry pi 3b:
```sh
sudo docker run -d --name ipfs_host -v $ipfs_staging:/export -v $ipfs_data:/data/ipfs -p 4001:4001 -p 127.0.0.1:8080:8080 -p 127.0.0.1:5001:5001 yrzr/go-ipfs-arm32v7:latest --enable-pubsub-experiment
```
Sharing arm32v7-ipfs url: 
https://hub.docker.com/r/yrzr/go-ipfs-arm32v7/

A platform to orchestrate all these containers along with their varying workloads, computing, networking, and storage are in demand. And their Orchestration managed by Kubernetes. dockerized ipfs in raspberry pi using kubectl (+nodeselector):
```sh
kubectl run --generator=run-pod/v1 pi-ipfs3 --image=yrzr/go-ipfs-arm32v7:latest --overrides='{ "apiVersion": "v1"O, "spec": { "nodeSelector": { "worker-node": "pi" } } }' --port 8087 -- daemon --enable-pubsub-experiment 
```
dockerized ipfs in mac mini using kubectl (+nodeselector):
```sh
kubectl run --generator=run-pod/v1 mac2-ipfs1 --image=shapna/go-ipfs_node1:Node1 --overrides='{ "apiVersion": "v1", "spec": { "nodeSelector": { "worker-node": "mac" } } }' --port 8083 -- daemon --enable-pubsub-experiment 
```

# Troubleshooting

The connection to the server 192.168.1.48:6443 was refused - did you specify the right host or port? or after reboot kubectl command not work:
```sh
cd ~/.kube
rm -rf config
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
export KUBECONFIG=$HOME/.kube/config
sudo systemctl restart kubelet.service
sudo systemctl restart docker
sudo systemctl restart kubelet
```
ubuntu cgroup error:
```sh
sudo nano /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"
sudo grub-update && sudo reboot
```
The connection to the server localhost:8080 was refused – did you specify the right host or port? or Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")
To fix this issue, simply export the admin.conf on the master node:
```sh
sudo cp /etc/kubernetes/admin.conf $HOME
sudo chown $(id -u):$(id -g) $HOME/admin.conf
export KUBECONFIG=$HOME/admin.conf
```

if not kubeadm join, kubernetes clean delete:
```sh
systemctl stop kubelet \
systemctl stop docker \
kubeadm reset

rm -rf /var/lib/cni/
rm -rf /var/lib/kubelet/*
rm -rf /run/flannel
rm -rf /etc/cni/
rm -rf /etc/kubernetes
rm -rf /var/lib/etcd/
rm -rf ~/.kube
ip link delete cni0
ip link delete flannel.1
```
And then install, first uses `$ systemctl start docker`

`kube-system kube-flannel-ds-arm-7p5pl  CrashLoopBackOff` error:
```sh
kubectl delete pods deployment --namespace=kube-system kube-flannel-ds-arm-7p5pl
sudo reboot
```

## Other issues

heapster, monitoring-influxdb error and give label for worker node:
```sh
kubectl taint nodes --all node-role.kubernetes.io/master-
kubectl label node 25ipfscluster node-role.kubernetes.io/worker=worker
```

Shutdown kubectl proxy:
```sh
killall kubectl
```

### To enable SSH
Search for and install the openssh-server package from Ubuntu Software Center. Or run command below in console if you’re on Ubuntu Server without GUI:
```sh
sudo apt-get install openssh-server
```

To edit settings - To change the port 22, root login permission, you may edit the `/etc/ssh/sshd_config` file via:
```sh
sudo nano /etc/ssh/sshd_config
```
After you changed the configurations, press Ctrl+X followed by typing y and hitting enter to save the file.

Finally restart the SSH service to apply the changes:
```sh
sudo service ssh restart
```
if not, host connection this:
```sh
ssh-keygen -R 192.168.1.49
```

### Kubernetes Node selector
first, we have to check node label:
```sh
kubectl get nodes --show-labels
```
label create:
```sh
kubectl label nodes wrl-02macmini worker-node=mac
```
label delete:
```sh
kubectl label nodes wrl-02macmini worker_node-
```
kubectl select node and label for nginx:
```sh
kubectl run --generator=run-pod/v1 nginx2 --image=nginx --overrides='{ "apiVersion": "v1", "spec": { "nodeSelector": { "workernode": "1" } } }' 
```
kubectl select node and label:
```sh
kubectl run --generator=run-pod/v1 pi-ipfs --image=shapna/go-ipfs_node1:Node1 --overrides='{ "apiVersion": "v1", "spec": { "nodeSelector": { "workernode": "1" } } }' --port 8083 -- daemon --enable-pubsub-experiment 
```
