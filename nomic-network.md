---
description: >-
  The Nomic Network is a decentralized network of nodes (blockchain) powering
  Nomic's decentralized custody engine for Bitcoin.
icon: block-brick
---

# Nomic Network

## Infrastructure

Nomic is built on Orga. This page gives a high-level overview of the components.

### Orga

Nomic is built on [Orga](https://github.com/turbofish-org/orga), a custom stack for writing Proof-of-Stake blockchains in Rust, authored and maintained by [Turbofish](https://turbofish.org). The scope of Orga is similar to the [Cosmos SDK](https://docs.cosmos.network/), but written in Rust with a focus on safety, performance, and expressiveness through composability. Under the hood, Orga depends on [CometBFT](https://cometbft.com) (f.k.a. Tendermint) as its consensus engine.

### Merk

[Merk](https://github.com/turbofish-org/merk) is a high-performance Merkle key/value store - more specifically, it's a Merkle AVL tree built on top of RocksDB. Merk prioritizes throughput and reliability, and supports fast and efficient proofs required for Nomic's IBC interaction with other networks, and for end-user light clients.

## NOM Token

NOM is the staking token used to secure the Nomic network. Stakers can receive both NOM and nBTC staking rewards.

The NOM distribution is inspired by the OSMO token. The network will start with a supply of 21,000,000 NOM, and the maximum supply after 9 years of inflation is 210,000,000 NOM (as an homage to Bitcoin’s 21,000,000 BTC).

#### Initial Distribution

**Total:** 21,000,000 NOM

*   **Airdrop I** - 3,500,000 NOM

    Targeting ATOM holders and stakers - see Airdrop 1.
*   **Reserve for Airdrops II & III** - 7,000,000 NOM

    These tokens were reserved at genesis. See Airdrop 2, which was executed on August 24, 2023.
*   **Strategic Reserve** - 10,500,000 NOM

    Based on standards from $OSMO and other Cosmos projects, the strategic reserve is used to create strategic partnerships for Nomic. It is held in a multisig by the Nomic DAO Foundation and is strictly for the purpose of achieving the long-term goals of Nomic. It will not be used to market sell and strategic partners will be subject to vesting periods.

    At the discretion of the foundation, the reserve may also be used to delegate to validators who are providing high value services to Nomic, such as operating infrastructure like block explorers and relayers, or contributing open source tooling/resources to the ecosystem. In order to maintain decentralization of the network, the strategic reserve will not “overstake” such to have a controlling share of the network.

#### Inflation Schedule

**Total Distributed After Genesis:** 189,000,000 NOM

NOM will be distributed over 9 years, reducing the inflation rate by 1/3 each year.

Inflation begins 24 hours after the launch of the Stakenet - Feb 1, 2022 at 20:40 UTC. This gives the community time to make delegations so that the initial validators do not receive an unfair advantage for claiming the staking rewards.

*   **Staking Rewards** - 47,250,000 NOM

    Rewards will be distributed to NOM validators and delegators in a similar fashion to other Cosmos chains.
*   **Protocol Incentives** - 85,050,000 NOM

    This share of the NOM supply will be used for future incentives to be decided by governance or proposed in later upgrades. For instance, potential incentives could be paid to nBTC holders or liquidity providers for NOM or nBTC pools on Osmosis.

    Excess protocol incentive NOM accumulates into a pool to be distributed later by protocol enhancements.
*   **Developer Vesting** - 47,250,000 NOM

    The developer share will be vested over time to the core contributors and founders of Nomic, Turbofish. Note that since the network is decentralized, in the event the Turbofish team is no longer the primary contributor to development, the developer share can be redirected to vest to other teams or individuals.
*   **Community Pool** - 9,450,000 NOM

    A share of NOM will be collected into a pool which can be spent by network governance discretionarily. The network does not yet include a mechanism to spend these funds, so they will accumulate until a later upgrade which adds coordination mechanisms for fund allocation.

### Airdrop 1

The Stakenet launch occurred on January 31, 2022 and included an airdrop to ATOM holders and stakers.

**Eligibility:**

* ATOM holders/stakers with balance of at least 1.5 ATOM
* Snapshot: Cosmos block 9,150,000 (Jan 21st, 2022 at 11:22:43 UTC)

**Distribution:**

* Total amount: 3,500,000 NOM
* Formula:
  * `(min(liquidBalance, 1000) + (4 * min(stakedBalance, 1000))) / 20.299325`
  * (A 1000 ATOM cap on both liquid balance and staked balance, with a 4x multiplier on staked balance)

### Airdrop 2

Airdrop 2 executed on August 24th with the activation of the Stakenet v6 upgrade.

**Eligibility:**

* ATOM, OSMO, JUNO, EVMOS, or KUJI stakers
* Snapshot: September 27th 2022

**Distribution:**

* Total amount: 3,500,000 NOM
* Formula:
  * Linear for staked ATOM, OSMO, JUNO, EVMOS, or KUJI for validators outside the top 20 on each network
  * (A max of 10,000 tokens per network are counted)
