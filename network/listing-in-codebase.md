# Listing in Codebase

If running a reliable node or relayer, you can help the network by listing them in the network config files so that others can automatically connect.

* Add public RPC URL(s) with port (preferably an HTTPS URL, which may require using a reverse proxy) to `state_sync_rpc`
* Add your Tendermint peer id (found in the `/status` RPC endpoint) and IP with port to `--p2p.seeds` in `tendermint_flags`
* If running a Bitcoin relayer, add the URL (preferably an HTTPS URL, which may require using a reverse proxy) to `btc_relayer`

## Stakenet

For Stakenet please make a GitHub issue or PR to the below file:

{% @github-files/github-code-block url="https://github.com/nomic-io/nomic/blob/develop/networks/stakenet.toml" %}

## Testnet

For Stakenet please make a GitHub issue or PR to the below file:

{% @github-files/github-code-block url="https://github.com/nomic-io/nomic/blob/develop/networks/testnet.toml" %}
