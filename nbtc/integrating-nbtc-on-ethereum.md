---
description: Nomic supports bridging Bitcoin to Ethereum.
---

# Integrating nBTC on Ethereum

## Contract Addresses

Nomic's decentralized custody bridging contract can be found at the following contract addresses:

<table><thead><tr><th width="185">Network</th><th>Bridge Contract Address</th></tr></thead><tbody><tr><td>Ethereum Sepolia</td><td><a href="https://sepolia.etherscan.io/address/0x794bdA49337C667ED03265618821b944Ed11bcED">0x794bdA49337C667ED03265618821b944Ed11bcED</a></td></tr><tr><td>Ethereum Holešky</td><td><em>TBA</em></td></tr><tr><td>Ethereum Mainnet</td><td><em>TBA</em></td></tr></tbody></table>

nBTC on Ethereum is issued as an ERC-20 token at the following contract addresses:

<table><thead><tr><th width="185">Network</th><th>Token Contract Address</th></tr></thead><tbody><tr><td>Ethereum Sepolia</td><td><a href="https://sepolia.etherscan.io/token/0xA229EaE06B1F8137461A9D309478da3C8d910E53">0xA229EaE06B1F8137461A9D309478da3C8d910E53</a></td></tr><tr><td>Ethereum Holešky</td><td><em>TBA</em></td></tr><tr><td>Ethereum Mainnet</td><td><em>TBA</em></td></tr></tbody></table>

Additional network contract addresses will be listed on here in the future, as well as documentation for creating your own customizable deployments in any EVM environment.

## Interchain Deposits

Interchain Deposits allow the generation of Bitcoin addresses which commit to a destination on an EVM-based chain, automatically forwarding any received funds as nBTC to a contract on that chain.

The Ethereum destination may be either:

* an Ethereum address to receive the nBTC;
* a contract call to be executed with the received nBTC.

Interchain Deposits require communication with [Bitcoin relayers](https://app.gitbook.com/s/WbEPY1r8LNsuYuBKLe23/bitcoin-relayer). The set of relayers used by your front-end should be selected with care.

See [`nomic-bitcoin-js`](https://www.npmjs.com/package/nomic-bitcoin) for more information on generating and displaying Bitcoin deposit addresses.

### Withdrawing to Bitcoin

Bitcoin may be withdrawn to a Bitcoin address directly via contract calls on Ethereum.

First, `approve` must be called on the token contract (see above table):

```typescript
web3.eth.abi.encodeFunctionCall({
    name: 'approve',
    type: 'function',
    inputs: [
    {
        type: 'address',
        name: 'spender'
    },
    {
        type: 'uint256',
        name: 'amount'
    }
    ]
}, [tokenContractAddress, usatAmount]);
```

Next, initiate the withdrawal to a Bitcoin address with a call to the bridge contract:

<pre class="language-typescript"><code class="lang-typescript"><strong>import { buildDestination } from 'nomic-bitcoin'
</strong><strong>
</strong><strong>let destination = buildDestination({
</strong><strong>    bitcoinAddress: 'tb1...'
</strong><strong>})
</strong><strong>
</strong><strong>web3.eth.abi.encodeFunctionCall({
</strong>    name: 'sendToNomic',
    type: 'function',
    inputs: [
    {
        type: 'address',
        name: '_tokenContract'
    },
    {
        type: 'string',
        name: '_destination'
    },
    {
        type: 'uint256',
        name: '_amount'
    }
    ]
}, [tokenContractAddress, destination, usatAmount]);
</code></pre>
