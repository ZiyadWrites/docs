---
description: >-
  When running a node, care must be put into handling the various keys to ensure
  they are not lost or leaked.
---

# Key Management

If your node is in the validator set or signatory set, you have an important responsibility to keep signing so that the network can remain live. Also, if these keys are stolen the security of the bridge is at risk.

## List of Keys

* **Consensus key (validators)**
  * `~/.nomic-stakenet-3/tendermint/config/prev_validator_key.json`
  * Used to sign blocks on the Nomic blockchain.
* **Signatory key (signatories)**
  * `~/.nomic-stakenet-3/signer/xpriv`
  * Used to sign Bitcoin transactions for the bridge.
* **Wallet key (all nodes)**
  * `~/.orga-wallet/privkey`
  * Used to sign transactions created through the Nomic CLI (declaring a validator, transferring tokens, etc.)

_On testnet, the path will instead start with `~/.nomic-testnet-4d`_

## Backing Up

It is important to make backups of these keys, since losing them can be hard to recover from.

When backing up, ensure your keys are copied somewhere other than where you run your nodes - preferably on an offline machine, encrypted with a passphrase, or stored on other medium not vulnerable to malware such as paper.

## Migrating Validator Nodes

Sometimes it is necessary to move your keys to a different machine to start operating your node there. When you do this, make sure to transfer all the keys as listed above into their respective paths.

Additionally, make sure to transfer `~/.nomic-stakenet-3/tendermint/data/priv_validator_state.json`. This file ensures your node will not accidentally cause a double-sign, which is a slashable offense that will jail your validator.
