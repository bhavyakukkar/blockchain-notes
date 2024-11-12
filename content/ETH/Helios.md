

- [[Ethereum#Light Client|light client]] for [[Ethereum]] written in Rust
- Implements the [[Ethereum#Light Client|Altair light-client]] specification which introduces a specification for a client that is able to sync with the chain without the need of the `active validator set` with the help of a `sync committee` - a list of validators that is tracked per block in the [[Ethereum#State|beacon state]]
- Supports both `Ethereum mainnet` as well as `Optimism` and `Base` L2 opstack rollup chains
- Depends on the [[Ethereum#Alloy]] library & the [[Ethereum#revm]] library



# How Does it Work?
- Its consensus-layer uses a previously known [[Ethereum#Proof of Stake|beacon-chain]] blockhash and a connection to an untrusted RPC (to receive a current blockhash) to verifiably sync to the current block
- Its execution-layer uses the authenticated beacon-chain blocks with the untrusted execution-layer RPC to prove arbitrary information about the chain state such as account balances, contract storage, transaction receipts and smart-contract call results using `Merkle Proofs`.
- If Helios knows the makeup of the current sync committee, it can confidently track the head of the chain by asking an untrusted RPC for the most recent sync committee signature
- Once synced, [various Ethereum RPC methods](https://github.com/a16z/helios/blob/master/rpc.md) can be used on the local RPC simulated by helios



# Components
There are 5 crates in the `helios` package workspace:


## cli
- Command-line-interface to run helios for the desired chain (`ethereum` or `optimism` or `base`) and with the desired configuration overrides
- Entry binary, this is what is built when using `heliosup`


## core
- Core logic of `helios`
- trait `Consensus` to be implemented for consensus clients with notable methods:
  + `block_recv`: receive blocks being received by the client
  + `finalized_block_recv`: receive `finalized` blocks being received by the client


## ethereum
- Implementation-specific logic to implement `core` for connections to the mainnet chain
- Implements trait `Consensus` for struct `ConsensusClient`


## consensus-core
- Ethereum-specific consensus logic


## opstack
- Implementation-specific logic to implement `core` for connections to the op-stack L2 chains `optimism` and `base`
- Implements trait `Consensus` for struct `ConsensusClient`




# Links
+ [Article on Building Helios by its author](https://a16zcrypto.com/posts/article/building-helios-ethereum-light-client/)
+ [Github Repository](https://github.com/a16z/helios)
