
Cross-chain refers to communication between different blockchain protocols

Established protocols like **IBC**, **CCIP** & **Wormhole** aim to provide standards to enabling bridging communications between different chains


# IBC

IBC (Inter-Blockchain Communication) is one such protocol

## Token Transfer

- [ICS-20: Transfer of fungible tokens over an IBC channel between two modules on separate chains](https://github.com/cosmos/ibc/blob/main/spec/app/ics-020-fungible-token-transfer/README.md)



## CCPI

## Token Minting

- [How to Mint a Cross-Chain NFT (ERC-721) using Chainlink's CCIP](https://www.youtube.com/watch?v=HDf6WZUkQaI)
- [How to Mint an NFT Across Different Blockchains using Chainlink CCIP](https://www.quicknode.com/guides/cross-chain/CCIP/cross-chain-nft)
- 4 contracts are responsible for the process:
  | | |
  |-|-|
  | **MyNFT.sol:** | ERC-721 token contract deployed on destination chain |
  | **DestionationMinter.sol:** | Receives & executes cross-chain minting requests for NFTs, deployed on the destination chain |
  | **SourceMinter.sol:** | Allows users to initiate the minting process on a destination chain by sending a CCIP message; also handles fees either in native tokens or Chainlink's LINK token; deployed on the source chain |
  | **Withdraw.sol:** | Provides functions for withdrawing ETH and ERC-20 tokens from SourceMinter.sol. |

