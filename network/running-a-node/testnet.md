---
description: Nomic Testnet v8.1 has been released, upgrade your nodes now!
---

# Testnet

{% hint style="info" %}
**NOTE:** this testnet spawns a new network (_nomic-testnet-6_), with a fresh state. You'll need to declare your validator as detailed here: [validating.md](../validating.md "mention")
{% endhint %}

This version comes with new features such as:

* A system for communicating with EVM-based blockchains (starting with the Ethereum Sepolia testnet), allowing nBTC to be used as an ERC-20 token ([https://blog.nomic.io/nbtc-ethereum-upgrade-c297ded38514](https://blog.nomic.io/nbtc-ethereum-upgrade-c297ded38514))
* The implementation of the Osmosis revenue sharing agreement, as approved by governance of both protocols ([https://forum.osmosis.zone/t/nbtc-revenue-share-proposal/2791](https://forum.osmosis.zone/t/nbtc-revenue-share-proposal/2791))
* Enhancements to the Bitcoin relayer, and onchain Bitcoin fee handling
* Other improvements and fixes

```console
# from `nomic` directory
git pull
git checkout v8.1.0
cargo install --path . --locked --bin nomic

# relaunch node:
nomic start --network testnet

# make sure to declare yourself as a validator, this is a new network!

# relaunch signer:
nomic signer --network testnet
```

If desired, you can run an Ethereum relayer to help test it.

```
nomic relay-ethereum --private-key <hex_privkey> --eth-rpc-url <sepolia_rpc_url>
```

Make sure your private key has a Sepolia ETH balance in order to pay for gas fees. You can find RPC providers here: https://chainlist.org/chain/11155111

Thanks for keeping your nodes running. Stay tuned, there will be more upgrades in the near future.
