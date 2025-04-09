# Private Permissioned Besu Test Network Running QBFT

> [!TIP]
> Based on the [Create a private network using QBFT](https://besu.hyperledger.org/private-networks/tutorials/qbft) tutorial from the official docs.

## Setup

### 1. Check [`qbft-config.json`](qbft-config.json)

This file includes the base configuration of the network.

### 2. Generate network files (genesis file + node keys)

```console
$ ./scripts/create-configs
```

This script should generate node keypairs and a `genesis.json` file under [`networkfiles/`](networkfiles/).

### 3. Copy network files to node data directories

```console
$ ./scripts/copy-configs
```

This script copies `genesis.json` to the root directory of this test _project_ and copies node keys to their corresponding node data directories (e.g. [`node1/data/`](node1/data/)).

### 4. Start nodes

```console
$ docker compose up -d
```
You can monitor the results with
```console
$ docker compose logs -f
```

### 5. Update the node allowlists at every node

```console
$ ./scripts/update-allowlists
```

### 6. Connect peers

```console
$ ./scripts/update-peers
```

## Using the network

### Using the QBFT BESU API over JSON-RPC

Confirm that there are four validators:
```console
$ xh :8545 jsonrpc=2.0 method=qbft_getValidatorsByBlockNumber params:='[latest]' id=1
```

> [!TIP]
> You can of course send this request to any of the four nodes; just change the port


### Sending Transactions

You can check `genesis.json` for the initial accounts with their balances and import it to your wallet of choice.
Alternatively, just use the [`web3`](https://github.com/gochain/web3) tool, e.g.:
```console
$ web3 --rpc-url http://localhost:8545 balance 0x627306090abaB3A6e1400e9345bC60c78a8BEf57
```

Example transaction:
```console
$ web3 --rpc-url http://localhost:8545 transfer \
    --pk c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3 \
    1 to 0xf17f52151EbEF6C7334FAD080c5704D77216b732

$ web3 --rpc-url http://localhost:8545 balance 0x627306090abaB3A6e1400e9345bC60c78a8BEf57
$ web3 --rpc-url http://localhost:8545 balance 0xf17f52151EbEF6C7334FAD080c5704D77216b732
```

> [!TIP]
> You can store the RPC URL for `web3` in an environment variable for convenience:
> ```console
> $ export WEB3_RPC_URL=http://localhost:8545
> ```
