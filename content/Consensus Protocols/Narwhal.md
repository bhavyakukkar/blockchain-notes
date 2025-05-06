
- Narwhal is a DAG-based mempool abstraction protocol

- It was proposed with the approach of first splitting the concerns of:
  + making transactions available across the network
  + preparing a total-order in consensus with the honest nodes of the network
- [[Tusk]] is the sample consensus protocol made in the Narwhal paper to demonstrate this new way of doing things 
  
- Blocks of transactions in the narwhal mempool contain not just a serial ordering of transactions but also a serial ordering of past referenced transaction blocks (which can be done with their headers, without the need of the block contents)

---

- It uses a __key-store__ to record transaction blocks indexed by their digests (hashes) with the following operations:
  + `read(d)` returns the block indexed by the provided digest
  + `write(d,b)` writes a given block to the store
  + `read_causal(d)` returns the transitive set of all blocks that the block identified by _d_ references (transitive meaning that the definition is recursive and for a block *a* referenced by a block *b* referenced by a block *c*, block *a* is also a valid element in block *c*'s defined operation)

---

- With the above operations allowed on it, the Narwhall mempool guarantees the following properties:
  1. any two invocations of reading the block for a given digest by honest parties returns the same value (if any value at all)
  2. an honest party that successfully invoked writing a new block will successfully invoke reading that same block for its digest
  3. the set returned from `read_causal(d)` is an improper superset of the set returned by one of the blocks that the block indexed with _d_ references
  4. the set returned by invoking `read_causal(d)` returns at least 66% of the blocks written successfully before the invocation of writing them (see point 2)
  5. at least 50% of the blocks returned by an invocation of `read_causal(d)` were written by honest parties


# Source Code
Analysis of source of a reference implementation of Narwhal-Tusk written by Alberto Sonnino, one of the authors of the whitepaper:

## Crates

| **Crate** | **Description** |
| --------- | --------------- |
| [node](https://github.com/facebookresearch/narwhal/blob/main/node/src/main.rs#L18) | Main entry point binary cli (node/src/main.rs) that can either [generate keys](https://github.com/facebookresearch/narwhal/blob/main/node/src/main.rs#L59), [spawn a primary process](https://github.com/facebookresearch/narwhal/blob/main/node/src/main.rs#L100) (which also starts a consensus thread to run the Tusk consensus protocol) or [spawn a worker process](https://github.com/facebookresearch/narwhal/blob/main/node/src/main.rs#L124) |
| [primary](https://github.com/facebookresearch/narwhal/blob/main/primary/src/primary.rs#L61) | Contains the logic to start the primary process which listens for connections [from its workers](https://github.com/facebookresearch/narwhal/blob/main/primary/src/primary.rs#L110) and [primary nodes on other validators](https://github.com/facebookresearch/narwhal/blob/main/primary/src/primary.rs#L91) `TODO`|
| [worker](https://github.com/facebookresearch/narwhal/blob/main/worker/src/worker.rs#L42) | Contains the logic to start the worker process with logic to handle messages [from client transactions](https://github.com/facebookresearch/narwhal/blob/main/worker/src/worker.rs#L137), [from workers on other validators](https://github.com/facebookresearch/narwhal/blob/main/worker/src/worker.rs#L197), and finally [from the primary on the same validator](https://github.com/facebookresearch/narwhal/blob/main/worker/src/worker.rs#L102) `TODO`|
| [consensus](https://github.com/facebookresearch/narwhal/blob/main/consensus/src/lib.rs#L63) | Contains the logic for the Tusk consensus protocol `TODO` |
| [store](https://github.com/facebookresearch/narwhal/blob/main/store/src/lib.rs#L23) | Contains the __key-store__ which receives the following commands:<ul><li>`Read(key, channel)` _"immediately send value corresp. to this key to me"_</li><li>`NotifyRead(key, send)` _"send value corresp. to this key the next time a value is written to it"_</li><li>`Write(key, value)` _"immediately write a value at this key"_</li></ul> |
| [network](https://github.com/facebookresearch/narwhal/tree/main/network/src) | `Utility` library for abstracting message sending ([unreliable](https://github.com/facebookresearch/narwhal/blob/main/network/src/simple_sender.rs#L20) and [reliable](https://github.com/facebookresearch/narwhal/blob/main/network/src/reliable_sender.rs#L27)) and [message receiving](https://github.com/facebookresearch/narwhal/blob/main/network/src/receiver.rs#L29) over a TCP stream |
| [crypto](https://github.com/facebookresearch/narwhal/blob/main/crypto/src/lib.rs) | `Utility` library for cryptography-relevant types and functions (Digest, Hash, PublicKey & SecretKey, generate_production_keypair, generate_keypair, Signature & SignatureService) |
| [config](https://github.com/facebookresearch/narwhal/blob/main/config/src/lib.rs#L62) | Logic to read & deserialize a JSON config file to configure Narwhal, including the [Committee composition](https://github.com/facebookresearch/narwhal/blob/main/config/src/lib.rs#L141) | 


# Links
- [Narwhal and Tusk: A DAG-based Mempool and Efficient BFT Consensus](https://arxiv.org/pdf/2105.11827)
- [ConsensusDays 21 / S1.4 / Narwhal and Tusk - Alberto Sonnino](https://www.youtube.com/watch?v=K5ph4-7vvHk)
- [Narwhal and Bullshark: DAG-based Mempool and Efficient BFT Consensus](https://www.youtube.com/watch?v=xKDDuPrYUag)
- [DAG Meets BFT: The Next Generation of BFT Consensus with Alexander Spiegelman | a16z crypto research](https://www.youtube.com/watch?v=NGOXVSFzYdI)
