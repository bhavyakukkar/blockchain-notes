

- [[Ethereum#Light Client|light client]] for [[Ethereum]] written in Rust
- Implements the [[Ethereum#Light Client|Altair light-client]] specification which introduces a specification for a client that is able to sync with the chain without the need of the `active validator set` with the help of a `sync committee` - a list of validators that is tracked per block in the [[Ethereum#State|beacon state]]
- Supports both `Ethereum mainnet` as well as `Optimism` and `Base` L2 opstack rollup chains
- [Article on Building Helios by its author](https://a16zcrypto.com/posts/article/building-helios-ethereum-light-client/)
- Depends on the [[Ethereum#Alloy]] library & the [[Ethereum#revm]] library
- [Github Repository](https://github.com/a16z/helios)



# How Does it Work?
- Its consensus-layer uses a previously known [[Ethereum#Proof of Stake|beacon-chain]] blockhash and a connection to an untrusted RPC (to receive a current blockhash) to verifiably sync to the current block
- Its execution-layer uses the authenticated beacon-chain blocks with the untrusted execution-layer RPC to prove arbitrary information about the chain state such as account balances, contract storage, transaction receipts and smart-contract call results using `Merkle Proofs`.
- If Helios knows the makeup of the current sync committee, it can confidently track the head of the chain by asking an untrusted RPC for the most recent sync committee signature
- Once synced, [various Ethereum RPC methods](https://github.com/a16z/helios/blob/master/rpc.md) can be used on the local RPC simulated by helios


## Example Tracelog
```
$ helios ethereum --execution-rpc https://eth-mainnet.g.alchemy.com/v2/<API_KEY>

2024-11-13T09:06:49.202334Z  INFO helios::rpc: rpc server started at 127.0.0.1:8545
2024-11-13T09:06:53.868899Z  INFO helios::consensus: sync committee updated
2024-11-13T09:06:54.608314Z  INFO helios::consensus: sync committee updated
2024-11-13T09:06:56.032273Z  INFO helios::consensus: finalized slot             slot=10388640  confidence=99.41%  age=00:00:18:33
2024-11-13T09:06:56.032348Z  INFO helios::consensus: updated head               slot=10388731  confidence=99.41%  age=00:00:00:21
2024-11-13T09:06:56.043222Z  INFO helios::consensus: consensus client in sync with checkpoint: 0x6a8e066ddc57a2526e433654f77c223da9f044dcf722e924fb57b846e08b890a
2024-11-13T09:06:59.204307Z  INFO helios::consensus: saved checkpoint to DB: 0xa9ba220e7573cf1e46f1647c17d3984101fe12363a029620dacee110745e2df5
2024-11-13T09:07:16.817607Z  INFO helios::consensus: updated head               slot=10388733  confidence=99.02%  age=00:00:00:17
2024-11-13T09:07:28.919104Z  INFO helios::consensus: updated head               slot=10388734  confidence=96.48%  age=00:00:00:17
2024-11-13T09:07:40.680692Z  INFO helios::consensus: updated head               slot=10388735  confidence=98.83%  age=00:00:00:17
2024-11-13T09:07:52.444045Z  INFO helios::consensus: finalized slot             slot=10388672  confidence=82.42%  age=00:00:13:05
2024-11-13T09:07:52.444116Z  INFO helios::consensus: updated head               slot=10388736  confidence=82.42%  age=00:00:00:17
2024-11-13T09:07:58.746485Z  INFO helios::consensus: saved checkpoint to DB: 0x347d0f1ade48e2acec07238f2894b4f8b70c5cd598408197eb546ba80abafa32
2024-11-13T09:08:04.843803Z  INFO helios::consensus: updated head               slot=10388737  confidence=97.66%  age=00:00:00:17
2024-11-13T09:08:16.690804Z  INFO helios::consensus: updated head               slot=10388738  confidence=98.63%  age=00:00:00:17
2024-11-13T09:08:28.932963Z  INFO helios::consensus: updated head               slot=10388739  confidence=90.43%  age=00:00:00:17
...
2024-11-13T09:11:28.417376Z  INFO helios::consensus: updated head               slot=10388754  confidence=99.22%  age=00:00:00:17
2024-11-13T09:11:40.431509Z  INFO helios::consensus: updated head               slot=10388755  confidence=99.22%  age=00:00:00:17
2024-11-13T09:11:52.487063Z  INFO helios::consensus: updated head               slot=10388756  confidence=99.80%  age=00:00:00:17
2024-11-13T09:11:58.776081Z  INFO helios::runner: shutting down... press ctrl-c 2 more times to force quit
2024-11-13T09:11:58.816012Z  INFO helios::client: shutting down
```



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
