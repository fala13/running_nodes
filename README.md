# running_nodes
Brief instructions how to run your own nodes for various EVM blockchains.
The vast knowledge I have accumulated over the years - enjoy!

## General tips
I run my nodes on custom servers, often multiple nodes in one box. Mostly within dockers.
Keep track of ports you use for each blockchain, so they don't overlap. Open the p2p ports on your firewall and router.
If you run your L1 geth on same machine as an L2 docker instance, you need to do a trick with network mapping. Within the L2 docker files (e.g. docker-compose.yml) you'll use the L1 address as http://host.docker.internal:8545 (or whatever port you need) and add 
```
    extra_hosts:
      - "host.docker.internal:host-gateway"
```
to the respective docker image sections.

# BSC
https://github.com/bnb-chain/reth
```
git clone https://github.com/bnb-chain/reth.git bsc-reth
cd bsc-reth
export PROFILE=maxperf
make build-bsc

# put in something like bsc-reth.sh, make sure port don't conflict with your other nodes
./target/maxperf/bsc-reth node \
    --datadir=./datadir \
    --chain=bsc \
    --http \
    --http.addr=0.0.0.0 \
    --http.port=8545 \
    --http.api="eth, net, txpool, web3, rpc" \
    --ws \
    --ws.addr=0.0.0.0 \
    --ws.port=8546 \
    --nat=any \
    --log.file.directory ./datadir/logs
    --full \
    --ipcpath ./datadir/reth.ipc \
    --port 38303 \
    --discovery.port 38303 \
    --authrpc.port 8851
```
