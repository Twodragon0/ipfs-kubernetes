
## IPFS Private Network in Docker

Dockerized-ipfs node using Docker-compose:
```sh
cd..
docker-compose up
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

Shift Host File to container images inside:
```sh
docker cp ~/daygeek6.txt ab572d62b1:/
```
