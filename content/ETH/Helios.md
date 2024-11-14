

- [[Ethereum#Light Client|light client]] for [[Ethereum]] written in Rust
- Implements the [[Ethereum#Light Client|Altair light-client]] specification which introduces a specification for a client that is able to sync with the chain without the need of the `active validator set` with the help of a `sync committee` - a list of validators that is tracked per block in the [[Ethereum#State|beacon state]]
- Supports both `Ethereum mainnet` as well as `Optimism` and `Base` L2 opstack rollup chains
- [Article on Building Helios by its author](https://a16zcrypto.com/posts/article/building-helios-ethereum-light-client/)
- Depends on the [[Ethereum#Alloy]] library & the [[Ethereum#revm]] library
- [Github Repository](https://github.com/a16z/helios)



# How Does it Work?
- Its consensus-layer gets a previously known [[Ethereum#Proof of Stake|beacon-chain]] blockhash from a trusted `consensus client RPC endpoint` and a connection to an `untrusted execution client RPC endpoint`, to verifiably sync to the current block by proving arbitrary information about the chain state such as account balances, contract storage, transaction receipts and smart-contract call results
- If Helios knows the makeup of the current sync committee, it can confidently track the head of the chain by asking an untrusted RPC for the most recent sync committee signature
- Once synced, [various Ethereum RPC methods](https://github.com/a16z/helios/blob/master/rpc.md) can be used on the local RPC simulated by helios



# Crates
There are 5 crates in the `helios` package workspace

| *Crate* | *Description* |
| ------- | ------------- |
| `cli` | Command-line-interface to run helios for the desired chain (`ethereum` or `optimism` or `base`) and with the desired configuration overrides; this is what is built when using `heliosup` |
| `core` | Core logic & state of the consensus-layers & execution-layers of helios, including traits to be implemented for any new __network__, __consensus__ and __execution rpc endpoints__ |
| `ethereum` | Implementation-specific logic to implement `core` for ethereum chains, with implementations of core's __network__ & __consensus__ |
| `consensus-core` | Implementation-specific logic for consensus specification for the `ethereum` network |
| `opstack` | Implementation-specific logic to implement `core` for opstack L2 chains `optimism` and `base`, with implementations of core's __network__ & __consensus__ |



# Types in Helios Core
The important types that need to be implemented or communicated with for a new chain or protocol, listed in order from primitive to complex types:


> ## 1. Network Specification
> | | |
> |-|-|
> | __where:__ | `trait NetworkSpec` in `core/src/network_spec.rs` |
> | __implemented for:__ | <ul><li>`Ethereum` in `ethereum/src/spec.rs`</li><li>`OpStack` in `opstack/src/spec.rs`</li></ul> |
> | __what to implement:__ |<ul> <li>`fn encode_receipt()`</li> <li>`fn receipt_contains()`</li> <li>`fn receipt_logs()`</li> <li>`fn tx_env()`</li> <li>`fn block_env()`</li> </ul> |
> 
> 
> ## 2. Consensus
> | | |
> |-|-|
> | __where:__ | `trait Consensus` in `core/src/consensus.rs` |
> | __determined by:__ | `network-spec` (specifically the type of its `transaction-response`) |
> | __implemented for:__ |<ul> <li>`ConsensusClient` in `ethereum/src/consensus.rs`</li> <li>`ConsensusClient` in `opstack/src/consensus.rs`</li> </ul>|
> 
> 
> ## 3. Execution Types
> <details>
>   <summary>Types relevant to execution</summary>
> 
> > ### a. Execution RPC
> > | | |
> > |-|-|
> > | __where:__ | `trait ExecutionRpc` in `core/src/execution/rpc/mod.rs` |
> > | __determined by:__ | `network-spec` (specifically the type of its `transaction-request`) |
> > | __implemented for:__ |<ul> <li>`HttpRpc` in `core/src/execution/rpc/http_rpc.rs`</li> <li>`MockRpc` in `core/src/execution/rpc/mock_rpc.rs`</li> </ul>|
> > 
> > 
> > ### b. Execution Client
> > | | |
> > |-|-|
> > | __where:__ | `struct ExecutionClient` in `core/src/execution/mod.rs` |
> > | __contains:__ | an `rpc` and a `state` |
> > | __determined by:__ | `network-spec` (because it determines both `rpc` and `state`) |
> > 
> > 
> > ### c. Inner
> > | | |
> > |-|-|
> > | __where:__ | `struct Inner` in `core/src/execution/state.rs` |
> > | __determined by:__ |<ul> <li>`network-spec` (specifically the type of its `transaction-response`)</li> <li>`execution-rpc` (any implemented `execution-rpc` that also uses the chosen implementation of `network-spec`)</li> </ul>|
> > | __contains:__ |<ul> <li>`blocks`: `TODO`</li> <li>`finalized_block`: `TODO`</li> <li>`hashes`: `TODO`</li> <li>`txs`: `TODO`</li> <li>`history_length`: `TODO`</li> <li>`rpc`: `TODO`</li> </ul>|
> > 
> > 
> > ### d. State
> > | | |
> > |-|-|
> > | __where:__ | `struct State` in `core/src/execution/state.rs` |
> > | __contains:__ | reference to a shared `inner` |
> > | __determined by:__ | `network-spec` (because it determines `inner`) |
> 
> </details>
> 
> ---
> 
> ## 4. Node
> | | |
> |-|-|
> | __determined by:__ |<ul> <li>`network-spec` (because it determines `execution-client`)</li> <li>`consensus` (any implemented `consensus` that also uses the chosen implementation of `network-spec`'s transaction-response)</li> </ul>|
> | __where:__ | `struct Node` in `core/src/client/node.rs` |
> | __contains:__ | an `execution client` and a `consensus` |
> 
> 
> ## 5. RPC
> | | |
> |-|-|
> | __where:__ | `struct Rpc` in `core/src/client/rpc.rs` |
> | __contains:__ | reference to a shared `node` |
> | __determined by:__ |<ul> <li>`network-spec` (because it determines `node`)</li> <li>`consensus` (any implemented `consensus` that also uses the chosen implementation of `network-spec`'s transaction-response)</li> </ul>|
> 
> 
> ## 6. Client
> | | |
> |-|-|
> | __where:__ | `struct Client` in `core/src/client/mod.rs` |
> | __contains:__ | reference to a shared `node` and an `rpc` |
> | __determined by:__ |<ul> <li>`network-spec` (because it determines `node` and `rpc`)</li> <li>`consensus` (any implemented `consensus` that also uses the chosen implementation of `network-spec`'s transaction-response)</li> </ul>|



# Example Tracelog

Ethereum:
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

Optimism:
```
$ helios opstack --network optimism --execution-rpc https://optimism-mainnet.public.blastapi.io

2024-11-13T10:30:40.647005Z  INFO helios::rpc: rpc server started at 127.0.0.1:8545
2024-11-13T10:30:43.142556Z  INFO helios_opstack::consensus: unsafe head updated: block=127947532 age=2s
2024-11-13T10:30:50.987552Z  INFO helios_opstack::consensus: unsafe head updated: block=127947536 age=1s
2024-11-13T10:30:51.826510Z  INFO helios_core::execution::state: backfilled: block=127947535
2024-11-13T10:30:52.224873Z  INFO helios_core::execution::state: backfilled: block=127947534
2024-11-13T10:30:52.734938Z  INFO helios_core::execution::state: backfilled: block=127947533
2024-11-13T10:30:52.841325Z  INFO helios_opstack::consensus: unsafe head updated: block=127947537 age=1s
2024-11-13T10:30:54.902852Z  INFO helios_opstack::consensus: unsafe head updated: block=127947538 age=1s
...
2024-11-13T10:31:20.549098Z  INFO helios_opstack::consensus: unsafe head updated: block=127947551 age=1s
2024-11-13T10:31:28.141499Z  INFO helios_opstack::consensus: unsafe head updated: block=127947554 age=2s
2024-11-13T10:31:28.795196Z  INFO helios_core::execution::state: backfilled: block=127947553
2024-11-13T10:31:28.992555Z  INFO helios_opstack::consensus: unsafe head updated: block=127947555 age=1s
2024-11-13T10:31:29.158332Z  INFO helios_core::execution::state: backfilled: block=127947552
2024-11-13T10:31:31.464082Z  INFO helios_opstack::consensus: unsafe head updated: block=127947556 age=2s
...
2024-11-13T10:31:46.582418Z  INFO helios_opstack::consensus: unsafe head updated: block=127947564 age=1s
2024-11-13T10:31:49.593729Z  INFO helios_opstack::consensus: unsafe head updated: block=127947565 age=2s
```
