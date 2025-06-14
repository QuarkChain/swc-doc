## Beta Testnet


### Get Test QKC for Beta Testnet On L1


Steps:

1. Ensure you have some sepolia gas, otherwise go [here](https://www.alchemy.com/faucets/ethereum-sepolia) for faucet.
2. Invoke the `drop` function on etherscan [here](https://sepolia.etherscan.io/address/0x274a6990dE7AaE06452cbEFa266c0C6a568F0D5B#writeContract).

Or simply run this:
```bash
export L1_RPC_URL='http://65.108.230.142:8545'
export PRIVATE_KEY=''# input your own pk

cast send 0x274a6990dE7AaE06452cbEFa266c0C6a568F0D5B 'drop()' --private-key $PRIVATE_KEY -r $L1_RPC_URL
```

After that you can cross the claimed `Test QKC` to L2 via [the bridge](https://bridge.beta.testnet.l2.quarkchain.io) or follow the instructions [here](https://github.com/ethereum-optimism/specs/discussions/140#discussioncomment-9426636).

### Get Beta Testnet Soul Gas Token On L2

```bash
export SOUL_GAS_TOKEN=0x4200000000000000000000000000000000000800
export L2_RPC_URL='http://5.9.87.214:8545'
export PRIVATE_KEY=''# input your own pk

cast send --value <amount, e.g., 10ether> $SOUL_GAS_TOKEN 'deposit()' --private-key $PRIVATE_KEY -r $L2_RPC_URL
```


Then if you import `0x4200000000000000000000000000000000000800` into metamask, you'll see your balance of `Soul Gas Token`.

### Faucet

>Go to [https://qkc-l2-faucet.eth.sep.w3link.io](https://qkc-l2-faucet.eth.sep.w3link.io).


## Devnet

### Get Test QKC for Devnet On L1


Steps:

1. Ensure you have some sepolia gas, otherwise go [here](https://www.alchemy.com/faucets/ethereum-sepolia) for faucet.
2. Invoke the `drop` function on etherscan [here](https://sepolia.etherscan.io/address/0x274a6990dE7AaE06452cbEFa266c0C6a568F0D5B#writeContract).

Or simply run this:
```bash
export L1_RPC_URL='http://65.108.230.142:8545'
export PRIVATE_KEY=''# input your own pk

cast send 0x274a6990dE7AaE06452cbEFa266c0C6a568F0D5B 'drop()' --private-key $PRIVATE_KEY -r $L1_RPC_URL
```

After that you can cross the claimed `Test QKC` to L2 following the instructions [here](https://github.com/ethereum-optimism/specs/discussions/140#discussioncomment-9426636).

### Get Devnet Soul Gas Token On L2

```bash
export SOUL_GAS_TOKEN=0x4200000000000000000000000000000000000800
export L2_RPC_URL='http://142.132.154.16:8545'
export PRIVATE_KEY=''# input your own pk

cast send --value <amount, e.g., 10ether> $SOUL_GAS_TOKEN 'deposit()' --private-key $PRIVATE_KEY -r $L2_RPC_URL
```


Then if you import `0x4200000000000000000000000000000000000800` into metamask, you'll see your balance of `Soul Gas Token`.
