---
description: >-
  These are the typical integration development opportunities that would enable
  more Cellar capabilities with more external protocols.
---

# External Protocol Integration

The Sommelier protocol is fully decentralized and open-sourced, and thus anyone can submit PRs and/or governance proposals to implement any change. That said, the current vault (Cellar) architecture typically requires two types of integrative smart contracts to be made in order to integrate into a new protocol. These are:

1. Adaptors, and
2. Pricing Extensions

Adaptors, as outlined in ["Protocol (V2) Contract Architecture,"](../protocol-v2-contract-architecture/) are smart contracts that allow Cellars to get in and out of positions with a respective protocol (ex. [FTokenAdaptor.sol](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/Frax/FTokenAdaptor.sol)) or token (ex. [ERC20Adaptor.sol](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/ERC20Adaptor.sol)). A simplified outlook for adaptors is that they are ERC4626 Integration contracts to a specific protocol and/or general functionality (such as holding ERC20s).&#x20;

> _NOTE: The adaptor contracts used within the Sommelier protocol today can actually be generalized to be ERC4626 agnostic by removing Sommelier-specific concepts such as the `PriceRouter` and `Registry`._ [_These ERC4626 agnostic adaptors are being explored currently as a public goods effort to the DeFi space._](https://github.com/PeggyJV/cellar-contracts/pull/143)

Pricing `extensions` are part of the `PriceRouterV2` design, where custom `extensions` are used to price assets involved with a Cellar's adaptor position. When interacting with external protocols, it is common to see receipt ERC20 tokens sent or minted to the Cellar from the respective protocol. These need to be priced with respect to the base asset. An example of this is the [Balancer Pricing Extensions](https://github.com/PeggyJV/cellar-contracts/tree/main/src/modules/price-router/Extensions/Balancer) that price Stable Pool BPTs.&#x20;
