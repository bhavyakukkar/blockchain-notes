
- Ethereum came about as a way to generalize the nature in which the blockchain may be used, by conducting for the entries on the blockchain to be **"transaction-based state machine"**
- The main or base ethereum blockchain is referred to as the `mainnet` chain or `Layer 1`

# Proof of Stake
- In 2022, Ethereum successfully switched from a [[Bitcoin|proof-of-work]] to an alternative consensus model called `proof-of-stake`
- Since changes to the chain specification require a coordinted fork, the chain with the new proof-of-stake consensus model was called the `beacon-chain`
- `The Merge` was the event when the beacon chain finally merged with the then main `mainnet` chain

## Links
+ [Ethereum's Proof of Stake consensus explained](https://www.youtube.com/watch?v=5gfNUVmX3Es)

# Smart Contracts
A stateful program that can be the recipient to decentralized transactions, where the parameters and results of the invoked program then get recorded into the blockchain.

## Links
+ [Example of sample Ethereum Smart-contract account](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48)

# EVM
[[ETH/Evm]]

# Layer 2
The main ethereum blockchain (called the `mainnet`) can only handle ~15 transactions per second, in order to increase scalability, layer 2 solutions are required, where "layer 2" or L2 describes any solution implemented on a separate chain than the mainnet.

## Rollups
Rollups are a L2 scaling solution that involve bundling a certain amount of transactions off-chain (not on the mainnet), uploading them as a single L1 transaction.

### Optimistic Rollups

### Zero-Knowledge Rollups
Tying back into the concept of [[Proofs#Zero Knowledge Proofs]], these are rollups that use ZKPs `TODO`

### Links
+ [Aligned Layer: Super Scaling Ethereum with Optimistic ZK Proofs](https://www.youtube.com/watch?v=DoVaJsJDxTg)

## Links
+ [Ethereum Layer 2](https://ethereum.org/en/layer-2/)

## Sharding
`TODO`

### Links
+ [Danksharding](https://ethereum.org/en/roadmap/danksharding/)

# Clients
Types of clients:

## Light Client
- Only requests necessary data from a provider (full node or centralized RPC server)
- Only processes block headers, but occasionally downloads block contents

### Links
+ [First Light-Client specification commit authored by Vitalik Buterin](https://github.com/ethereum/consensus-specs/commit/1f210fd1f84c00b7005915914f0ae8e94b18cd12)
+ [Altair Light-client specification (first light-client specification)](https://github.com/ethereum/consensus-specs/blob/dev/specs/altair/light-client/sync-protocol.md)

## Full Client

## Archive Client

## Links
+ [Ethereum Nodes & Clients](https://ethereum.org/en/developers/docs/nodes-and-clients/)
+ [Ethereum Explained: Research, Spec, EVM, Clients, & Nodes](https://www.youtube.com/watch?v=vzgNqO_obH4)
+ [Post-Merge Ethereum Client Architecture by Adrian Sutton | Devcon Bogotá](https://www.youtube.com/watch?v=6d4pkhL37Ao)


# Specifications

## Execution Client
- Using the JSON-RPC, the following will fetch the number of the most recent block in the Ethereum network (using an RPC node provided by Alchemy) `curl https://eth-mainnet.g.alchemy.com/v2/<YOUR-API-KEY> -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"eth_blockNumber","id":0}'`
### Links
+ [Specifications](https://github.com/ethereum/execution-apis)
+ [JSON-RPC Spec](https://ethereum.github.io/execution-apis/api-documentation/)

## Consensus Client
### Links
+ [Specifications](https://github.com/ethereum/consensus-specs)


# Implementations

## Alloy
`TODO`
### Links
+ [Docs](https://docs.rs/alloy/latest/alloy/)

## revm
- Rust implementation of the [[Ethereum#EVM]] specification
### Links
+ [Docs](https://docs.rs/revm/latest/revm/)

## Helios
[[ETH/Helios]]

# Links
+ [Whitepaper](https://ethereum.org/en/whitepaper/)
+ [Yellowpaper](https://ethereum.github.io/yellowpaper/paper.pdf)
+ [Ethereum Transactions](https://ethereum.org/en/developers/docs/transactions/)
+ [Example of a sample Ethereum transaction](https://etherscan.io/tx/0xd0dcbe007569fcfa1902dae0ab8b4e078efe42e231786312289b1eee5590f6a1)
