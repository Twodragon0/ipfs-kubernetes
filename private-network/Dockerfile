FROM ipfs/go-ipfs:latest
COPY .ipfs/swarm.key /data/ipfs/swarm.key
COPY init.sh /usr/local/bin/ipfs
RUN chown -R $(whoami) /usr/local/bin && chmod +x /usr/local/bin/ipfs
