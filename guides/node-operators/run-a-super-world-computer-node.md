# Run a Super World Computer(SWC) node

This guide will help you get SWC node up and running.

## Hardware requirements

Hardware requirements for SWC nodes can vary depending on the type of node you plan to run. Archive nodes generally require significantly more resources than full nodes. Below are suggested minimum hardware requirements for each type of node.

- 8GB RAM
- 300 GB SSD (full node) or 500 GB SSD (archive node)
- Reasonably modern CPU

## Sync modes

For full nodes, the following configurations are available ([explanation](https://docs.optimism.io/operators/node-operators/management/snap-sync#enable-snap-sync-for-your-node)):

|                         | `op-node`(CL)                              | `op-geth`(EL)                   |
| ----------------------- | ------------------------------------------ | ------------------------------- |
| Full nodes EL snap sync | `--syncmode=execution-layer (not default)` | `--syncmode=snap (default)`     |
| Full nodes EL full sync | `--syncmode=execution-layer (not default)` | `--syncmode=full (not default)` |
| Full nodes CL sync      | `--syncmode=consensus-layer (default)`     | `--syncmode=full (not default)` |

For archive nodes, please add `--gcmode=archive` to `op-geth`.

## Mainnet

### Steps

1. Follow the following steps to download the executable binaries.

 - 1.1 Download `op-geth`  
    ```bash
    export VERSION=v1.0.0
    curl -L "https://github.com/QuarkChain/op-geth/releases/download/${VERSION}/op-geth.${VERSION}.linux-amd64.tar.gz" \
      | tar -xz && mv "op-geth.${VERSION}" op-geth
    ```
- 1.2 Download `op-node`
    ```bash
    export VERSION=v1.0.0
    curl -L "https://github.com/QuarkChain/optimism/releases/download/op-node%2F${VERSION}/op-node.${VERSION}.linux-amd64.tar.gz" \
      | tar -xz --strip-components=1 && mv "${VERSION}" op-node
    ```
2. Setup `op-geth`:

    > The default settings are for full nodes with snap sync. For configurations related to full sync or archive nodes, please refer to the [Sync modes](#sync-modes) section.

    ```bash
        # assume op-node and op-geth are located at ./op-node and ./op-geth

        cd op-geth
        # prepare mainnet_genesis.json
        curl -LO https://raw.githubusercontent.com/QuarkChain/pm/refs/heads/main/L2/assets/mainnet_genesis.json
        ./build/bin/geth init --datadir=datadir --state.scheme hash mainnet_genesis.json
        openssl rand -hex 32 > jwt.txt

        export PUBLIC_IP=<YOUR_PUBLIC_IP>

        # The rpc port is the default one: 8545.
        ./build/bin/geth --datadir ./datadir   \
            --http \
            --http.corsdomain="*" \
            --http.vhosts="*" \
            --http.addr=0.0.0.0 \
            --http.api=web3,eth,txpool,net \
            --ws \
            --ws.addr=0.0.0.0 \
            --ws.port=8546 \
            --ws.origins="*" \
            --ws.api=eth,txpool,net \
            --nat="extip:${PUBLIC_IP}" \
            --networkid=100011 \
            --authrpc.vhosts="*" \
            --authrpc.port=8551 \
            --authrpc.jwtsecret=./jwt.txt \
            --rollup.disabletxpoolgossip \
            --rollup.sequencerhttp=https://rpc.mainnet.l2.quarkchain.io:8545 \
            --rollup.enabletxpooladmission 2>&1 | tee -a geth.log -i
    ```

3. Setup `op-node`:

    > ⚠️ The `op-node` admin RPC should not be exposed publicly. If left exposed, it could accidentally expose admin controls to the public internet. 

    > Sync mode is set to `--syncmode=execution-layer` to enable snap sync.

    ```bash
        # assume op-node and op-geth are located at ./op-node and ./op-geth
        # copy jwt.txt from the op-geth directory above to ./op-node
        cp ./op-geth/jwt.txt ./op-node 
        cd ./op-node

        export L1_RPC_KIND=basic
        export L1_RPC_URL=<YOUR_RPC_URL>
        export L1_BEACON_URL=<YOUR_BEACON_URL>
        # prepare delta_testnet_rollup.json
        curl -LO https://raw.githubusercontent.com/QuarkChain/pm/refs/heads/main/L2/assets/mainnet_rollup.json
        mkdir safedb

        export PUBLIC_IP=<YOUR_PUBLIC_IP>

        ./build/bin/op-node --l2=http://localhost:8551 \
            --l2.jwt-secret=./jwt.txt \
            --verifier.l1-confs=4 \
            --rollup.config=./mainnet_rollup.json \
            --rpc.port=8547 \
            --p2p.listen.ip=0.0.0.0 \
            --p2p.listen.tcp=9003 \
            --p2p.listen.udp=9003 \
            --p2p.advertise.ip=$PUBLIC_IP \
            --l1=$L1_RPC_URL \
            --l1.rpckind=$L1_RPC_KIND \
            --l1.beacon=$L1_BEACON_URL \
            --l1.beacon-archiver=https://archive.mainnet.ethstorage.io:9645 \
            --l1.cache-size=0 \
            --safedb.path=safedb \
            --syncmode=execution-layer | tee -a node.log -i

## Delta Testnet

### Software dependencies

| Dependency                                        | Version | Version Check Command |
| ------------------------------------------------- | ------- | --------------------- |
| [git](https://git-scm.com/)                       | `^2`    | `git --version`       |
| [go](https://go.dev/)                             | `^1.21` | `go version`          |
| [make](https://linux.die.net/man/1/make)          | `^3`    | `make --version`      |
| [just](https://just.systems/man/en/packages.html) | `^1.34` | `just --version`  

### Steps

1. Follow the following steps to build a node. The steps are basically the same as in [Optimism's documentation](https://docs.optimism.io/builders/node-operators/tutorials/node-from-source), the only difference is that here we use the `delta_testnet` branch of both [our optimism fork](https://github.com/QuarkChain/optimism/tree/delta_testnet) and [our op-geth fork](https://github.com/QuarkChain/op-geth/tree/delta_testnet) instead.

- 1.1 Build `op-geth`
    ```bash
    git clone -b delta_testnet https://github.com/QuarkChain/op-geth.git
    pushd op-geth && make geth && popd
    ```
- 1.2 Build `op-node`
    ```bash
    git clone -b delta_testnet https://github.com/QuarkChain/optimism.git
    pushd optimism && make op-node && popd
    ```

2. Setup `op-geth`:

    > The default settings are for full nodes with snap sync. For configurations related to full sync or archive nodes, please refer to the [Sync modes](#sync-modes) section.

    ```bash
        # assume optimism and op-geth repo are located at ./optimism and ./op-geth

        cd op-geth
        # prepare gamma_testnet_genesis.json
        curl -LO https://raw.githubusercontent.com/QuarkChain/pm/main/L2/assets/delta_testnet_genesis.json
        ./build/bin/geth init --datadir=datadir --state.scheme hash delta_testnet_genesis.json
        openssl rand -hex 32 > jwt.txt

        export PUBLIC_IP=<YOUR_PUBLIC_IP>

        # The rpc port is the default one: 8545.
        ./build/bin/geth --datadir ./datadir   \
            --http \
            --http.corsdomain="*" \
            --http.vhosts="*" \
            --http.addr=0.0.0.0 \
            --http.api=web3,eth,txpool,net \
            --ws \
            --ws.addr=0.0.0.0 \
            --ws.port=8546 \
            --ws.origins="*" \
            --ws.api=eth,txpool,net \
            --nat="extip:${PUBLIC_IP}" \
            --networkid=110011 \
            --authrpc.vhosts="*" \
            --authrpc.port=8551 \
            --authrpc.jwtsecret=./jwt.txt \
            --rollup.disabletxpoolgossip \
            --rollup.sequencerhttp=http://65.109.110.98:8545 \
            --rollup.enabletxpooladmission 2>&1 | tee -a geth.log -i
    ```

3. Setup `op-node`:

    > ⚠️ The `op-node` admin RPC should not be exposed publicly. If left exposed, it could accidentally expose admin controls to the public internet. 

    > Sync mode is set to `--syncmode=execution-layer` to enable snap sync.

    ```bash
        # assume optimism and op-geth repo are located at ./optimism and ./op-geth
        # copy jwt.txt from the op-geth directory above to optimism/op-node
        cp op-geth/jwt.txt optimism/op-node 
        cd optimism/op-node

        export L1_RPC_KIND=basic
        export L1_RPC_URL=http://65.108.230.142:8545
        export L1_BEACON_URL=http://65.108.230.142:3500
        # prepare delta_testnet_rollup.json
        curl -LO https://raw.githubusercontent.com/QuarkChain/pm/main/L2/assets/delta_testnet_rollup.json

        export PUBLIC_IP=<YOUR_PUBLIC_IP>
        mkdir safedb
        # Ensure to replace --p2p.static with the sequencer's address.
        # Note: p2p is enabled for unsafe block.
        ./bin/op-node --l2=http://localhost:8551 \
            --l2.jwt-secret=./jwt.txt \
            --verifier.l1-confs=4 \
            --rollup.config=./delta_testnet_rollup.json \
            --rpc.port=8547 \
            --p2p.listen.ip=0.0.0.0 \
            --p2p.listen.tcp=9003 \
            --p2p.listen.udp=9003 \
            --p2p.advertise.ip=$PUBLIC_IP \
            --l1=$L1_RPC_URL \
            --l1.rpckind=$L1_RPC_KIND \
            --l1.beacon=$L1_BEACON_URL \
            --l1.beacon-archiver=https://archive.testnet.ethstorage.io:9635 \
            --l1.cache-size=0 \
            --safedb.path=safedb \
            --syncmode=execution-layer | tee -a node.log -i
    ```
