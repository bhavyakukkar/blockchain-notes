
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


# Links
- [Narwhal and Tusk: A DAG-based Mempool and Efficient BFT Consensus](https://arxiv.org/pdf/2105.11827)
- [ConsensusDays 21 / S1.4 / Narwhal and Tusk - Alberto Sonnino](https://www.youtube.com/watch?v=K5ph4-7vvHk)
- [Narwhal and Bullshark: DAG-based Mempool and Efficient BFT Consensus](https://www.youtube.com/watch?v=xKDDuPrYUag)
- [DAG Meets BFT: The Next Generation of BFT Consensus with Alexander Spiegelman | a16z crypto research](https://www.youtube.com/watch?v=NGOXVSFzYdI)
