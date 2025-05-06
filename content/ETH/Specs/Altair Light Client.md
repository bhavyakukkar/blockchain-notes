
- Light-clients are Ethereum clients that can connect to the Ethereum chain while maintaining a very small portion of the chain state data

- The Altair beacon-chain fork brought about chain-level changes (changes in the chain protocol rather than changes in the chain client) that make it possible for light-clients to only have to download the block headers `TODO`

- This change was included in the Altair beacon-chain fork

- Light clients depend on full-nodes to fetch information from them, but validate that information themself, thus leading to "trustless" access to the chain, something that a raw connection to a remote full-node RPC cannot provide, thus making light clients a significant part of Ethereum's vision

- The fork added [2 new fields to the beacon state](https://github.com/ethereum/consensus-specs/blob/dev/specs/altair/beacon-chain.md#beaconstate) and [1 new field to the beacon block body](https://github.com/ethereum/consensus-specs/blob/dev/specs/altair/beacon-chain.md#beaconblockbody):
  ```diff
    class BeaconState(Container):
        # Versioning
        genesis_time: uint64
        genesis_validators_root: Root
        ...
  +     # Sync
  +     current_sync_committee: SyncCommittee  # [New in Altair]
  +     next_sync_committee: SyncCommittee  # [New in Altair]

    class BeaconBlockBody(Container):
        randao_reveal: BLSSignature
        eth1_data: Eth1Data  # Eth1 data vote
        graffiti: Bytes32  # Arbitrary data
        ...
  +     sync_aggregate: SyncAggregate  # [New in Altair]
  ```


# Sync Committee

The sync-committee is a list of 512 addresses derived from the blockchain state. At any instance of the state, the current sync committee & the next sync committee are known, as seen in the fields added to the beacon state container

```python
# The committee as stored in the beacon state, that gets changed every 256 epochs
class SyncCommittee(Container):
    pubkeys: Vector[BLSPubkey, 512]
    aggregate_pubkey: BLSPubkey

# The committee signature included in every block
class SyncAggregate(Container):
    # bit field corresponding to each validator in the committee and whether they have participated
    sync_committee_bits: Bitvector[512]
    # aggregated signature resulting from the signatures of the validators in the sync committee that participated
    sync_committee_signature: BLSSignature
```


# Sync Process

A [light-client-enabled](https://ethereum.github.io/beacon-APIs/) consensus-layer full-node provider is required to return the `LightClientBootstrap` to a light-client that requests it:
```python
class LightClientBootstrap(Container):
    # Header matching the requested beacon block root
    header: LightClientHeader
    # Current sync committee corresponding to `header.beacon.state_root`
    current_sync_committee: SyncCommittee
    # Merkle branch of current_sync_committee within BeaconState
    current_sync_committee_branch: CurrentSyncCommitteeBranch

class LightClientHeader(Container):
    # Beacon block header
    beacon: BeaconBlockHeader
```

- Using the `LightClientBootstrap` received from the provider, the light-client can:
  1. Use the `current_sync_committee_branch` merkle proof to verify that `current_sync_committee` is the correct sync-committee for the block, by reconstructing the state-root hash and checking that it matches to `header.beacon.state_root`.
    However, this does not establish trust yet, as a dishonest provider could construct a fabricated block-header. To establish trust, the light-client needs to prove the block header in the context of a previous block header
  2. Since both the current & next sync-committees are known in the beacon state, a checkpoint finalized block in one sync-period can be used to validate the block in the next sync-period, since the `next_sync_committee` of the first block should match the `current_sync_committee` of the second block

<!--
```python
class LightClientUpdate(Container):
    # Header attested to by the sync committee
    attested_header: LightClientHeader
    # Next sync committee corresponding to `attested_header.beacon.state_root`
    next_sync_committee: SyncCommittee
    next_sync_committee_branch: NextSyncCommitteeBranch
    # Finalized header corresponding to `attested_header.beacon.state_root`
    finalized_header: LightClientHeader
    finality_branch: FinalityBranch
    # Sync committee aggregate signature
    sync_aggregate: SyncAggregate
    # Slot at which the aggregate signature was created (untrusted)
    signature_slot: Slot
```
-->

# Links
- [PEEPanEIP #35: Altair Beacon chain upgrade with Vitalik Buterin & Danny Ryan](https://www.youtube.com/watch?v=iaAEGs1DMgQ)
- [Altair Light-client specification (renamed from the first light-client specification after new versions came along)](https://github.com/ethereum/consensus-specs/blob/dev/specs/altair/light-client/sync-protocol.md)
