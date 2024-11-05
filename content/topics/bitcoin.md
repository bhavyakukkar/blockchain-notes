
# Bitcoin
- The bitcoin whitepaper proposes a model where the proof-of-work is in the form of a key such that, when appended to the hash of a block of transactions and hashed, yields a digest starting with an agreed upon number of zeroes.

## Attackers
- Due to the hash of a previous block being an input into the hash of the current block, changes at any point in time in a transaction in a block will cause an inconsistency not just in that block but all blocks after it. Hence, an attacker trying to broadcast a faulty block of transactions onto the current consensus branch will need to continue to maintain the branch with the modified transaction.
- However, the more the number of honest special nodes (called `miners` in relation to an analogy of gold-miners) that bear computing resources to determine the proof-of-work key, the smaller the chance for an attacker broadcasting a faulty chain of blocks to be the first to arrive at the proof-of-work.

## Links
+ [Bitcoin whitepaper](https://bitcoin.org/bitcoin.pdf)
+ [Message types in Bitcoin's blockchain protocol](https://en.bitcoin.it/wiki/Protocol_documentation#Message_types)
