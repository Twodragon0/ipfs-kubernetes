
## IPFS Private Network in Docker

Dockerized-ipfs node using Docker-compose:
```sh
cd..
docker-compose up
```

Dockerized-ipfs-cluster services using Docker Swarm:
```sh
docker stack deploy -c docker-compose.yml wrl
```

In docker, remove public bootstrap. Make swarm.key:
```sh
ipfs bootstrap rm all
ipfs bootstrap add /ip4/192.241.*.*/tcp/4001/p2p/Qma4bvcDe5sXZbBqiexX*v
vi /data/ipfs/swarm.key
export LIBP2P_FORCE_PNET=1 
ipfs swarm peers
```
Container Restart
```sh
docker stop <container_id>
docker start <container_id>
docker exec -it <container_id> /bin/ash
# ipfs swarm peers
```

In Raspberry pi4, IPFS used to arm32v7:
```sh
docker run -d --name ipfs_host -v $ipfs_staging:/export -v $ipfs_data:/data/ipfs -p 4003:4003 -p 127.0.0.1:8082:8082 -p 127.0.0.1:5003:5003 yrzr/go-ipfs-arm32v7:latest
docker exec -it <container_id> /bin/ash
```

Shift host File to container images inside:
```sh
sudo apt install xfsprogs -y
xfs_mkfile 10M 10MB_file.txt
docker cp ~/10MB_file.txt ab572d62b1:/
```

Shift container images to host File inside:
```sh
sudo apt install xfsprogs -y
xfs_mkfile 10M 10MB_file.txt
docker cp 7f12878cd1:/QmUSdoVvGg9y ~
```

If can not join node, can be use this addr:
```sh
docker swarm init --advertise-addr 172.17.0.1 --listen-addr 0.0.0.0
```

Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on interface enpf0 (2002:cbff:fb2d:0:3Xb:X7:3X5:7X6 and 2002:cXf:fXd:0:6Xe:2c7e:fXf:8b9a) - specify one with --advertise-addr = should be remove ipv6
```sh
sudo su
ifconfig enpf0 del 2002:cbff:fb2d:0:3Xb:X7:3X5:7X6/64
```

Error response from daemon: rpc error: code = Unavailable desc = all SubConns are in TransientFailure, latest connection error: connection error: desc = "transport: Error while dialing dial tcp 172.17.0.1:2377: connect: connection refused" = Manager node should be check multiple ipv6
