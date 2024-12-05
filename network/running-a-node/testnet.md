---
description: Nomic Testnet v10.1 has been released.
---

# Testnet

{% hint style="info" %}
**NOTE:** this testnet spawns a new network (_nomic-testnet-9_), with a fresh state. To join the validator set, you'll need to declare your validator as detailed here: [validating.md](../validating.md "mention")
{% endhint %}

```console
# from `nomic` directory
git pull
git checkout v10.1.0
cargo install --path . --locked --bin nomic

# launch node:
nomic start --network testnet
```

Thanks for keeping your nodes running. Stay tuned, there will be more upgrades in the near future.
