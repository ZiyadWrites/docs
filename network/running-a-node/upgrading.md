# Upgrading

Nomic has its own unique upgrade system. Once you switch to the latest release, your node will signal its readiness, and the network will automatically switch to the new version once enough voting power has signalled.

To prepare your Nomic Stakenet node for a network upgrade, simply compile the new version then restart:

```bash
# from `nomic` directory
git pull
git checkout v9.0.0
cargo install --path . --locked --bin nomic

# relaunch node with:
nomic start

# run your signer with:
nomic signer
```

{% hint style="warning" %}
**Remember to keep your Bitcoin key safe, and to keep your signer running** The key is located at `~/.nomic-stakenet-3/signer/xpriv` - it's important to back it up. If you are moving your validator node from another machine, remember to copy this key in addition to the other usual keys (`priv_validator_key.json`, `node_key.json`, etc). For nodes in the top 20, not running a signer for 20 checkpoints will result in jailing.
{% endhint %}
