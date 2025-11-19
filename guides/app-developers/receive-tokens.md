## Delta Testnet


### Get Test QKC for Delta Testnet On L1


Steps:

1. Ensure you have some sepolia gas, otherwise go [here](https://www.alchemy.com/faucets/ethereum-sepolia) for faucet.
2. Invoke the `mint` function on etherscan [here](https://sepolia.etherscan.io/address/0xC359FCF9328143f798C197B86856e656411aBC48#writeContract).

Or simply run this:
```bash
export L1_RPC_URL='https://65.108.230.142:8545'
export PRIVATE_KEY=''# input your own pk

cast send 0xC359FCF9328143f798C197B86856e656411aBC48 'mint()' --private-key $PRIVATE_KEY -r $L1_RPC_URL
```

After that you can cross the claimed `Test QKC` to L2 via [the migration page](https://migration.delta.testnet.l2.quarkchain.io).

### Get Delta Testnet Soul Gas Token On L2

```bash
export SOUL_GAS_TOKEN=0x4200000000000000000000000000000000000800
export L2_RPC_URL='https://rpc.delta.testnet.l2.quarkchain.io:8545'
export PRIVATE_KEY=''# input your own pk

cast send --value <amount, e.g., 10ether> $SOUL_GAS_TOKEN 'deposit()' --private-key $PRIVATE_KEY -r $L2_RPC_URL
```


Then if you import `0x4200000000000000000000000000000000000800` into metamask, you'll see your balance of `Soul Gas Token`.

### Faucet

>Go to [https://qkc-l2-delta-faucet.eth.sep.web3gateway.dev](https://qkc-l2-delta-faucet.eth.sep.web3gateway.dev).

