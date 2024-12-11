
- Ethereum came about as a way to generalize the nature in which the blockchain may be used, by conducting for the entries on the blockchain to be **"transaction-based state machine"**
- `Ether (ETH)` is the cryptocurrency used for many things on the Ethereum network
- The main or base ethereum blockchain is referred to as the `mainnet` chain or `Layer 1`
- [Ethereum Whitepaper](https://ethereum.org/en/whitepaper/)
- [Ethereum Yellowpaper](https://ethereum.github.io/yellowpaper/paper.pdf)
- [Transactions](https://ethereum.org/en/developers/docs/transactions/)
- [Example of a sample Ethereum transaction](https://etherscan.io/tx/0xd0dcbe007569fcfa1902dae0ab8b4e078efe42e231786312289b1eee5590f6a1)



# Proof of Stake
- In 2022, Ethereum successfully switched from a [[Bitcoin|proof-of-work]] to an alternative consensus model called `proof-of-stake`
- `TODO`
- Since changes to the chain specification require a coordinted fork, the chain with the new proof-of-stake consensus model was called the `beacon-chain`
- `The Merge` was the event when the beacon chain finally merged with the then main `mainnet` chain
- Proof-of-stake uses `ether` to validate and propose blocks on the mainnet
- [Ethereum's Proof of Stake consensus explained](https://www.youtube.com/watch?v=5gfNUVmX3Es)



# Accounts
- An Ethereum account is an entity with an ether (ETH) balance that can send transactions on Ethereum
- There are 2 types of Ethereum accounts:


## EOA
- Externally-owned accounts are controlled by anyone with their ECDSA (Elliptic Curve Digital Signature Algorithm) private key


## Contract Accounts
- An Ethereum account, aside from identifying a participating node in the Ethereum network, can also be a contract account, taking the place of a single invokable contract on the Ethereum network
- Transactions made to a contract-account expect the name of the called function and its input parameters in the `data` field of the transaction so that the contract may be invoked


## Account Abstraction
Before the abstract of an `account` introduced in [ERC-4337](https://eips.ethereum.org/EIPS/eip-4337), the authorization process included checking only the following:
1. Account's key pair (elliptive-curve cryptography or ECDSA)
2. Account's nonce (Number-once)
3. Account's balance (prevention of overspending)

However, the `account` abstraction was introduced, without any changes to the consensus protocol, to allow "pseudo-transactions" called `UserOperations` that end up being executed from a separate mempool into packages called `bundle transactions` by a special class of actors called `bundlers` after they make a call to the `EntryPoint` contract.



# Smart Contracts
- A stateful program that can be the recipient to decentralized transactions, where the parameters and results of the invoked program then get recorded into the blockchain
- [Example of sample Ethereum Smart-contract account](https://etherscan.io/address/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48)



# EVM
[[ETH/Evm]]



# State
- The state of the Ethereum blockchain is its composition, consisting of all ether transactions (from an EOA to another EOA or a smart-contract) and all contract invocations, welded into a single series of blocks through consensus among the nodes in the Ethereum network
- The state of the ethereum blockchain is encoded into a [[ETH/Merkle-Patricia Trie]], which itself is a modified version of the [[Bitcoin|Merkle Tree]]
- There is a [roadmap](https://ethereum.org/en/roadmap/verkle-trees/) to move to a [[ETH/Verkle Tree]] structure so that Ethereum can support `stateless clients` (clients that can participate in the Ethereum networks without needing to store any history of the ethereum state)



# Layer 2
The main ethereum blockchain (called the `mainnet`) can only handle ~15 transactions per second, in order to increase scalability, layer 2 solutions are required, where "layer 2" or L2 describes any solution implemented on a separate chain than the mainnet.
- Generally speaking, transactions are submitted to these layer 2 nodes instead of being submitted directly to layer 1
- [Layer 2 Scaling](https://ethereum.org/en/developers/docs/scaling/#layer-2-scaling)
- [More on layer 2](https://ethereum.org/en/layer-2/)


## Rollups
- Rollups are the most obvious L2 scaling solution to offload some traffic from L1 by bundling a certain amount of transactions off-chain (not on the mainnet), and finally uploading them as a single L1 transaction instead and thus reducing network traffic as well as gas fees
- Rollups have been criticized to decrease the decentralization factor since they are often hosted by a centralized server or a clusters of servers, though they might also be hosted by individual nodes much like L1
- [Aligned Layer: Super Scaling Ethereum with Optimistic ZK Proofs](https://www.youtube.com/watch?v=DoVaJsJDxTg)
- [Addressing common rollup misconceptions](https://www.reddit.com/r/ethereum/comments/pmvbbh/addressing_common_rollup_misconceptions/)


### Optimistic Rollups
- Optimistic rollups, when sending the bundled transactions, assume that all the transactions are valid and accurate unless disputed via `fraud proofs`
- **Fraud Proof:** A full node, upon seeing an invalid state transition being gossiped around the network, could quickly generate a small piece of data demonstrating that a proposed state transition could not possibly arise from a given set of transactions and broadcast that data to peers
- Popular Optimistic Rollups include [Arbitrum](https://docs.arbitrum.io/intro/glossary#arbitrum-rollup-protocol), [Base](https://docs.base.org/docs/terms-of-service/#1-base-and-bridging-smart-contracts) & [Optimism](https://docs.optimism.io/stack/rollup)

### Zero-Knowledge Rollups
- Tying back into the concept of [[ZKP/Zero Knowledge Proofs]], these are rollups that use ZKPs to attach a proof of the consensus to the rollups which the target L1 branch can verify
- Rollups consist of `Prover` nodes that generate proofs about the input transactions and the resultant change in the state, whereby a smart-contract on the beacon chain can act as a verifier to verify the proof and add the sum transaction upon successful verification
- [Ethereum Scaling: ZK Roll-Ups](https://www.youtube.com/watch?v=3C0g-60bAWc)
- Popular ZK Rollups include [Scroll](https://docs.scroll.io/en/learn/)


## Sharding
`TODO`
- [Danksharding](https://ethereum.org/en/roadmap/danksharding/)
- [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844)


## Bridges
- [0xPARC - A zk-SNARK enabled light client and bridge for Eth2 chains](https://www.youtube.com/watch?v=20MmjozezdM&t=3m25s): "What if we were able to verify the consensus of a source chain in the execution layer of a target chain?"



# Clients
- [Ethereum Nodes & Clients](https://ethereum.org/en/developers/docs/nodes-and-clients/)
- [Ethereum Explained: Research, Spec, EVM, Clients, & Nodes](https://www.youtube.com/watch?v=vzgNqO_obH4)
- [Post-Merge Ethereum Client Architecture by Adrian Sutton | Devcon Bogotá](https://www.youtube.com/watch?v=6d4pkhL37Ao)
- There are 3 types of Ethereum clients:


## Light Client
- Only requests necessary data from a provider (full node or centralized RPC server)
- Only processes block headers, but occasionally downloads block contents
- [First Light-Client specification commit authored by Vitalik Buterin](https://github.com/ethereum/consensus-specs/commit/1f210fd1f84c00b7005915914f0ae8e94b18cd12)
- [Altair Light-client specification (renamed from the first light-client specification after new versions came along)](https://github.com/ethereum/consensus-specs/blob/dev/specs/altair/light-client/sync-protocol.md)


## Full Client
`TODO`


## Archive Client
`TODO`



# Client Specifications


## Execution Client
- Using the JSON-RPC, the following will fetch the number of the most recent block in the Ethereum network (using an RPC node provided by Alchemy) `curl https://eth-mainnet.g.alchemy.com/v2/<YOUR-API-KEY> -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"eth_blockNumber","id":0}'`
- [Specifications](https://github.com/ethereum/execution-apis)
- [JSON-RPC Spec](https://ethereum.github.io/execution-apis/api-documentation/)
- [How to verify a proof received from the `eth_getProof` RPC method](https://github.com/ethereum/EIPs/issues/1186#issuecomment-401161169)


## Consensus Client
- [Specifications](https://github.com/ethereum/consensus-specs)



# Implementations


## Alloy
- [Docs](https://docs.rs/alloy/latest/alloy/)


## revm
- Rust implementation of the [[Ethereum#EVM]] specification
- [Docs](https://docs.rs/revm/latest/revm/)


## Helios
[[ETH/Helios]]
