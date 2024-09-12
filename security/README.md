# Emergency Disbursal

## Introduction

In the event of an extended liveness failure, for example, downtime caused by a bug or an attack, funds in typical custody systems and bridges remain inaccessible for an indefinite amount of time. The funds are stuck until the issue is resolved and signatories manually intervene, leaving depositors with no recourse.

Nomic protects against this by including an Emergency Disbursal mechanism, making user funds accessible even during a liveness failure.

## Mechanism

The Emergency Disbursal is conceptually simple: the network produces and signs timelocked Bitcoin transactions each time a new checkpoint transaction is made. If the next checkpoint confirms on the Bitcoin network, the UTXOs spent by the previous checkpoint transactions are now spent by the checkpoint. This invalidates the old Emergency Disbursal which is now atomically replaced by the new one.

### Transaction Structure

Nomic periodically moves funds in “[checkpoint transaction](https://app.gitbook.com/s/wouPLII4HXZ3uzgba1H6/nbtc#how-it-works)s”, which result in a “reserve output” that contains a pool of funds held in decentralized custody. The Emergency Disbursal makes up a tree of transactions: a set of “final transactions”, each including a large set of outputs to the recipients of the Emergency Disbursal and a single input, and a single “intermediate transaction” with outputs corresponding to the inputs of final transactions and a single input spending from the reserve output.

Outputs are split across multiple final transactions because of size constraints - Bitcoin’s default policies limit transactions to 100,000 virtual bytes. Assuming there are 100,000 accounts on the network, and each of these accounts receive a pay-to-pubkey-hash UTXO (34 bytes each), this would require over 3.4M vbytes of transaction data (a small amount of additional space is required for the base transaction fields and the inputs). This structure could then be made up of about 36 transactions, 1 intermediate transaction which directly spends the reserve output and has 35 outputs, and 35 transactions which spend outputs from the intermediate transaction and pay out to many P2PKH outputs (one for each account).

All final transactions and the intermediate transaction include a timelock at a timestamp in the future based on some parameter - e.g., two weeks. Since Bitcoin signatures commit to the inputs they spend, signed emergency disbursals will automatically be invalidated as soon as a newer checkpoint happens since it will spend the same reserve UTXO and become confirmed before the emergency disbursal unlocks.

For atomicity in moving the funds to a new checkpoint, the network first coordinates signing of the next checkpoint’s Emergency Disbursal transactions before advancing to the signatures for the checkpoint transaction. This ensures that if a liveness failure happens during the signing process, it is guaranteed that the most recently confirmed checkpoint has valid and signed Emergency Disbursal transactions spending from it.

### Fees

In order to pay for the high cost of Bitcoin network fees for the Emergency Disbursal transactions to be included in blocks, the proper fee amounts based on the network’s last known fee levels and the transaction and witness sizes are deducted from final transaction outputs. This cost is shared across all outputs, and results in any accounts which have too small of a balance to pay their share of the fee being pruned and becoming wholly paid into the shared fee.

In the event the Emergency Disbursal transactions pay too low of a fee and are not being confirmed in blocks, the transactions can be fee-bumped collectively by any recipients of final transactions if they simply spend their (unconfirmed) outputs with a high fee rate. Bitcoin mempools use the child-pays-for-parent fee rules meaning any recipient can help contribute additional fees to the network’s Emergency Disbursal transactions.

### Outputs

To convert onchain accounts into recipients of the Emergency Disbursal, we have to derive a Bitcoin script for each to include in a transaction output. Depending on the type of account, there are different ways to build Bitcoin scripts which can be spent by the intended user.

#### nBTC on Nomic

Native accounts on the Nomic chain each have an optional “recovery script” which can be set by the user at any point, to define the output script for their emergency disbursal payouts. The user simply has to pick a destination address and input it in their Nomic protocol client (e.g. [app.nomic.io](https://app.nomic.io/bitcoin)). Each new Emergency Disbursal will include an output with their latest recovery script and balance.

In the future, the protocol could potentially derive an output for all accounts, even those that do not set a recovery script. This is possible because Nomic account address hashes and keys are compatible with Bitcoin script (`ripemd160`, `sha256`, and `secp256k1`).

#### nBTC on Remote IBC Chains

If funds have been moved over a channel to a remote IBC chain based on Tendermint or CometBFT consensus, the Nomic network needs a different way to reason about paying out via the Emergency Disbursal. Instead of paying to individual users, since their balances may change and are not known on the Nomic chain, a multisig wallet is constructed instead, made from the keys of the top 40 validators.

To power IBC, the Nomic network already accepts relayed proofs of the current validator set of the remote chain. The validator set gets retained in the state, and additional proofs about the remote chain’s state are accepted in order to relay and verify the validator’s `secp256k1` operator keys. This process can happen automatically by third-party relayer nodes which carry data between both networks.

In the future, it could be possible to create an IBC standard to allow remote protocols, contracts, and users to set their own recovery scripts and have their balances tracked by the Nomic protocol to include individuals as recipients outside of the chain-wide multisig. However, care must be taken since this could mean there would be a short time where a user can hold a balance which is not currently reflected in the most recently created Emergency Disbursal transactions.

#### nBTC on Ethereum/EVM

On Ethereum, nBTC is deployed as an ERC-20 token representing BTC. For its Emergency Disbursal scheme, accounts can opt to set a recovery script, similar to native Nomic accounts. Once opted-in, transfers to and from the user will result in a call to the Nomic decentralized custody contract's `setEmergencyDisbursalBalance` function, queueing up a message to be relayed to the Nomic protocol which updates the user’s output in the next Emergency Disbursal.

Note that unlike native Nomic accounts, this system does not reflect users’ balance changes into their Emergency Disbursal output until the next checkpoint update. However, this is only a temporary condition (at most, a period of hours but often a period of minutes) - nBTC transfers can be considered to have “soft finality” when the ERC-20 token transfer confirms on Ethereum, and “hard finality” once signed into a Nomic checkpoint.

#### Decentralized Custody Contracts on EVM

On EVM-based networks, usage of the Nomic network happens via bridge contracts deployed by different projects that opt to use Nomic’s decentralized BTC custody. These consumers of the bridge contract can arbitrarily assign the BTC they hold into Emergency Disbursal outputs at any time via the `setEmergencyDisbursalBalance(bytes script, u64 amount)` contract call.

This means that consumers of Nomic’s decentralized custody can choose their own paradigm for updating the Emergency Disbursal payouts for their users - a necessary flexibility since contracts could represent relationships more complex than simple user accounts (for instance, the BTC may be collateral in a loan, or be in a DAO-owned treasury).
