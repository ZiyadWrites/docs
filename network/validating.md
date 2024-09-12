# Validating

Becoming a validator comes with extra responsibility compared to running a non-validating full node. Your node becomes an authority for producing blocks and a signatory for the Bitcoin held in the bridge.

### 1. Acquiring nBTC and staking for voting power

First, find your address by running `nomic balance` (for now this must be run on the same machine as your active full node). You'll need to fund this Nomic account with nBTC.

You can declare your node as a validator and delegate to yourself with this command:

```bash
nomic declare \
  <validator_consensus_key> \
  <amount> \
  <commission_rate> \
  <max_commission_rate> \
  <max_commission_rate_change_per_day> \
  <min_self_delegation> \
  <moniker> \
  <website> \
  <identity> \
  <details>
```

If you do not have NOM, you can deposit BTC and set `amount` to 0 for 100 satoshis of nBTC.

{% hint style="warning" %}
**IMPORTANT NOTE:** Carefully double-check all the fields since you will not be able to modify the `commission_max` or `commission_max_change` after declaring. If you make a mistake, you will have to declare a new validator instead.
{% endhint %}

* The `validator_consensus_key` field is the base64 pubkey `value` field found under `"validator_info"` in the output of [http://localhost:26657/status](http://localhost:26657/status).
* The `identity` field is the 64-bit hex key suffix found on your Keybase profile, used to get your profile picture in wallets and block explorers.

For example:

```bash
nomic declare \
  ohFOw5u9LGq1ZRMTYZD1Y/WrFtg7xfyBaEB4lSgfeC8= \
  100000 \
  0.042 \
  0.1 \
  0.01 \
  100000 \
  "Foo's Validator" \
  "https://foovalidator.com" \
  37AA68F6AA20B7A8 \
  "Please delegate to me!"
```

### 2. Run your Bitcoin signer

Validating on Nomic means you partake in the Bitcoin signing process of the funds in the Bitcoin decentralized custody. It is very important that you run a [signer](signer.md).
