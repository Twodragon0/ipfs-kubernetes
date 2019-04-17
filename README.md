
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
- kubernetes
- docker
- go-ipfs

# Installation

Install jsipfs CLI 
```sh
$ npm install ipfs@0.35.0 --global 
```

Run daemon with pub-sub flag 
```sh
$ jsipfs daemon --enable-pubsub-experiment 
```
