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
On my linux distro you'd open ports like this:
```
sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
sudo firewall-cmd --zone=public --add-port=30303/udp --permanent
sudo firewall-cmd --reload
```

# BSC-RETH
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
# MODE
https://docs.mode.network/tools/node-operators

# BSC
First grab a snapshot from here https://github.com/48Club/bsc-snapshots and be sure to use the same options used to collect the snapshot.
You also need to download a genesis file: https://docs.bnbchain.org/bnb-smart-chain/developers/node_operators/full_node/#sync-from-snapshot-recommended

```
./geth --config ./config.toml --datadir ./geth.fast --rpc.allow-unprotected-txs --history.transactions=0 --syncmode=full --tries-verify-mode=none --pruneancient --db.engine=pebble # --cache 8000

# config.toml 
[Eth]
NetworkId = 56
LightPeers = 100
TrieTimeout = 150000000000

[Eth.Miner]
GasCeil = 140000000
GasPrice = 3000000000
Recommit = 10000000000

[Eth.TxPool]
Locals = []
NoLocals = true
Journal = "transactions.rlp"
Rejournal = 3600000000000
PriceLimit = 3000000000
PriceBump = 10
AccountSlots = 200
GlobalSlots = 8000
AccountQueue = 200
GlobalQueue = 4000

[Eth.GPO]
Blocks = 20
Percentile = 60
OracleThreshold = 1000

[Node]
IPCPath = "geth.ipc"
HTTPHost = "localhost"
InsecureUnlockAllowed = false
HTTPPort = 5645
HTTPVirtualHosts = ["localhost"]
HTTPModules = ["eth", "net", "web3", "txpool", "parlia"]
WSPort = 5646
WSModules = ["net", "web3", "eth"]

[Node.P2P]
MaxPeers = 200
NoDiscovery = false
StaticNodes = []
ListenAddr = ":30311"
EnableMsgEvents = false

[Node.LogConfig]
FilePath = "bsc.log"
MaxBytesSize = 10485760
Level = "info"
FileRoot = ""
```
