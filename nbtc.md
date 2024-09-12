---
icon: bitcoin
---

# nBTC

## What is nBTC?

When BTC is moved into Nomic's decentralized custody, it can be turned into an asset called nBTC. The creation of nBTC only happens when deposits of BTC are detected by the Nomic protocol, so nBTC is always backed exactly 1:1 by BTC held in the decentralized custody reserves. Holders of nBTC can withdraw BTC from the decentralized custody at any point.

nBTC is not a synthetic asset, and is not like other BTC-like assets which only offer price exposure. Since nBTC is truly backed by BTC, it gives the holder ownership of the equivalent amount of BTC.

## How it Works

Nomic's decentralized custody is operated through the decentralized protocol rules of the Nomic blockchain - there are no central authorities or trusted parties to rely on for the secure operation of the decentralized custody.

A **reserve** of Bitcoin is maintained in a decentralized way through use of a special multisig script. The collective whole of the network validators cooperates to hold or disburse funds as **signatories** of the reserve, since their signatures are required to control the funds on the Bitcoin blockchain.

To disburse funds from the reserve, more than 90% of the signatory set must sign the Bitcoin transaction (weighted by voting power). This is enforced on the Bitcoin blockchain through the **reserve script**, which looks something like this:

```
<pubkey1> OP_CHECKSIG
OP_IF
  <voting_power1>
OP_ELSE
  0
OP_ENDIF

OP_SWAP
<pubkey2> OP_CHECKSIG
OP_IF
  <voting_power2>
  OP_ADD
OP_ENDIF

OP_SWAP
<pubkey...> OP_CHECKSIG
OP_IF
  <voting_power...>
  OP_ADD
OP_ENDIF

OP_SWAP
<pubkeyN> OP_CHECKSIG
OP_IF
  <voting_powerN>
  OP_ADD
OP_ENDIF

<90%_of_total_voting_power>
OP_GREATERTHAN
```

Periodically, the network creates **checkpoint transactions**, which spend all incoming deposits, as well as an output form the previous checkpoint transaction. Checkpoints have the following structure:

**Inputs:**

* The reserve output of the previous checkpoint transaction.
* All unspent deposit outputs, if any.

**Outputs:**

* The **reserve output**, equal to the amount of Bitcoin which are held in reserve. Paid to the updated reserve script based on the most recent signatory set.
* All pending withdrawals, if any.

The Nomic blockchain maintains a light client of the Bitcoin blockchain, verifying the proof-of-work of each header and attempting to stay up-to-date on the heaviest chain. By verifying transactions against the Bitcoin blockchain, the protocol can detect incoming deposits by checking for outputs which pay to a recent signatory set reserve script (along with a commitment to the destination on the Nomic chain, which can be the address of a Nomic account or an account on a remote IBC chain).

Whenever a new Bitcoin block is mined, or a deposit transaction is confirmed on the Bitcoin network, the data will need to be carried to the Nomic chain. Conversely, when a transaction is signed by the signatory set in the checkpointing process, it will need to be broadcast to the Bitcoin network. This job is done by **relayer** nodes, which can be any node with knowledge of both networks running software to broadcast the relayed data.

Note that no trust is placed in the relayer nodes and the system operates correctly as long as at least a single relayer node is active and up-to-date on the canonical Bitcoin chain.

**Relayed from Bitcoin to Nomic:**

* _Bitcoin block mined_ - header is relayed
* _Deposit transaction is confirmed_ - transaction and Merkle proof are relayed

**Relayed from Nomic to Bitcoin:**

* _Checkpoint signed by signatory set_ - assembled transaction is relayed

## Key Security Features

Security is the main priority in the Nomic design. To make the decentralized custody as safe as possible, various security features have been included to cover different threat models and risks.

### **Emergency Disbursal**

In the case of an extended liveness failure, all deposited funds would be frozen in place on the Bitcoin blockchain with no recourse other than manually resolving the situation with the signatories.

To protect against this case, as part of the checkpointing process signatories also sign a set of **"emergency disbursal"** transactions which spend the entire reserve and pay out to each individual nBTC-holder on the Bitcoin blockchain. Signatories publish these signatures to the network at the time of the checkpoint so that relayers may assemble them. These transactions are timelocked 2 weeks past the checkpoint, so the emergency disbursal only happens if a checkpoint has not been created for an extended period of time.

Note that to be included in the emergency disbursal, an nBTC holder must first set their “recovery script” on the Nomic chain, representing something such as their personal wallet address.

#### **90% Signatory Set Threshold**

When signing checkpoint transactions, the network is able to use a high signature threshold - 90% of the signatory set voting power must sign to create valid Bitcoin transactions. Even with this high threshold, the network is secure against liveness faults due to the Emergency Disbursal mechanism. The high threshold means that only 10% of the voting power needs to be honest to ensure there are no unexpected spends of the reserve.

### **Circuit Breakers**

Circuit breaker mechanisms are often used in engineering to provide safety by shutting down the system when extreme conditions are detected. In the case of Nomic, the circuit breaker detects when a large amount of funds are leaving the decentralized custody in a 24-hour period, or if there is a large shift in signatory voting power. When the mechanism is tripped, signatories will automatically stop signing checkpoint transactions, giving the network time to verify the transaction and respond accordingly before any funds leave the reserve.

### **Fully-Verifying**

Many decentralized bridge designs make security compromises in the interest of being easier to implement. For instance, the common model is to trust the validators to report on the state of the remote chain. This gives network validators the power to mint bridge assets at will, which means a lot of trust is placed in them to operate the bridge honestly.

Nomic, on the other hand, provides a stronger security guarantee by maintaining an in-protocol light client of the Bitcoin blockchain, verifying the headers and proof-of-work, and verifying inclusion of transactions via Merkle proofs. This means that nBTC can only be minted if an equivalent amount of BTC is moved into the decentralized custody on the Bitcoin chain.

## Fees

Bitcoin miner fees are paid when depositing into the decentralized custody since the deposit output must be spent to collect the funds into the reserve. A small miner fee is also taken from withdrawals based on the size of the output, usually totaling in the hundreds of satoshis. The Nomic protocol constantly adjusts the amount of fees paid to miners, to pay at current Bitcoin fee market rates.

In addition to Bitcoin miner fees, the Nomic protocol collects bridge fees when BTC is deposited, and when nBTC is transferred to a remote IBC chain. No bridge fees are collected when withdrawing or for IBC transfers to the Nomic chain to ensure that holding 1 nBTC is tied to the ability to receive 1 BTC on the Bitcoin blockchain (minus Bitcoin miner fees).

Bridge fees collected by the protocol are first paid into the **network fee fund**, which maintains a small balance in order to pay for the miner fees for checkpoint transactions (for the bytes of the transaction not covered by depositors and withdrawal initiators). All nBTC collected in fees beyond what is required for checkpoints is paid into the **reward pool**, which is distributed to stakers on the Nomic chain over time (at a rate of 1/2377 of the current pool balance every 2 minutes).

## Capacity Limits

During the early stages of the bridge, capacity limits are in place to slow the growth of the reserve. When the bridge reaches its capacity limit, clients will not allow generating deposit addresses so that users can not deposit more BTC.

In the pre-audited state of the bridge, Nomic has a capacity limit of **21 BTC**. Like the bridge fee rates, this parameter will be controlled by Nomic DAO governance in a future upgrade.
