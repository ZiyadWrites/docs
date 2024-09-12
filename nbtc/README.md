---
description: >-
  IBC is a blockchain interoperability protocol used by 110+ chains. This page
  gives a high-level overview of how to integrate nBTC into any IBC-compatible
  chain.
---

# Integrating nBTC via IBC

## Prerequisites

As a prerequisite, follow the instructions to configure an [IBC relayer](https://app.gitbook.com/s/WbEPY1r8LNsuYuBKLe23/ibc-relayer) to work with Nomic. IBC transactions require the relayer account to be funded with a small amount of nBTC, as IBC-related transactions are charged a fee of 1 Satoshi each.

## Creating a channel

Once `hermes` is [configured for Nomic](https://app.gitbook.com/s/WbEPY1r8LNsuYuBKLe23/ibc-relayer), the next step is to create an IBC channel with Nomic.

```bash
hermes create channel --a-chain <your-chain-id> --b-chain nomic-stakenet-3 --a-port
transfer --b-port transfer --new-client-connection
```

Nomic currently requires both ends of the channel to use the "transfer" port.

## Relaying operator keys

To ensure that nBTC is recoverable by the remote chain's validator set in the event of an Emergency Disbursal, run:

```bash
nomic relay-op-keys <COUNTERPARTY-RPC> <CLIENT_ID>
```

This command may be re-run anytime to refresh the operator keys of the remote chain's validator set. If an Emergency Disbursal occurs on Nomic, a portion of the Bitcoin reserves equal to the nBTC held in channels backed by the specified client will become spendable by 2/3+ of the voting power of that network's top 30 validators.

## Interchain Deposits

Interchain Deposits allow the generation of Bitcoin addresses which commit to an ICS-20 token transfer packet, automatically forwarding any received funds to an address on the counterparty chain.

Interchain Deposits require communication with a [Bitcoin relayer](https://app.gitbook.com/s/WbEPY1r8LNsuYuBKLe23/bitcoin-relayer). The set of relayers used by your front-end should be selected with care.

After a channel has been opened between your chain and Nomic, see [`nomic-bitcoin-js`](https://www.npmjs.com/package/nomic-bitcoin) for information on generating and displaying deposit addresses.

### Withdrawals to Bitcoin

nBTC may be withdrawn as Bitcoin directly from the counterparty chain. ICS-20 transfer packets support a `memo` field. Providing a memo of the form "withdraw:\<dest>" for an incoming nBTC transfer packet to Nomic will trigger a withdrawal of the Bitcoin to `<dest>` at the next checkpoint. `<dest>` may be either:

1. A Bech32 Bitcoin address.
2. A hex-encoded Bitcoin script.
