
- Multichain [[Ethereum#Light Client|light client]] for [[Ethereum]] written in Rust that uses trustless verification
- Implements the [[Ethereum#Light Client|Altair light-client]] specification
- Depends on [[Ethereum#Alloy]] & [[Ethereum#revm]]

# How Does it Work?
- Its consensus-layer uses a previously known [[Ethereum#Proof of Stake|beacon-chain]] blockhash and a connection to an untrusted RPC (to receive a current blockhash) to verifiably sync to the current block by leveraging [[Proofs#Zero Knowledge Proofs|Zero-knowledge proof]] mechanisms
- Its execution-layer uses the authenticated beacon-chain blocks with the untrusted execution-layer RPC to prove arbitrary information about the chain state such as account balances, contract storage, transaction receipts and smart-contract call results

# Components
`TODO`

# Transaction Verifications
`TODO`

# Links
+ [Article on Building Helios by its author](https://a16zcrypto.com/posts/article/building-helios-ethereum-light-client/)
+ [Github Repository](https://github.com/a16z/helios)
