# Building Adaptors

Adaptors are the building blocks of the Sommelier Cellars protocol. Adaptors are used to connect Cellars to the larger DeFi ecosystem: for each external DeFi protocol Sommelier Cellars support, there is a corresponding adaptor smart contract.

> _Note: \`CellarAdaptor.sol\` can be used to integrate with \`ERC4626Vaults\` in the wild. An example of this is how the \`CellarAdaptor\` is used to integrate with \`sDAI\`, or with AuraPools (in development)._

Adaptors are standalone contracts that should enable single actions on single protocols (for example, providing LP liquidity on Uniswap V3). There are multiple different motivations for building adaptors:

* Smart Contract developers contributing to the Sommelier ecosystem may build adaptors to new or high-TVL protocols to enable new design possibilities for Cellars.
* Strategists may build adaptors if their proprietary strategies require integrations with previously-unsupported protocols. For instance, a strategist who wants to build an NFT trading cellar may need to build a Seaport adaptor.
* DeFi protocol developers may build adaptors for their own protocols, in order to use Sommelier Cellars as another funnel through which to grow TVL. This can be very effective for protocols that also build corresponding strategies centered around their protocol's utility.

### Starting an Adaptor

These documents are oriented towards technologies adopted by the Sommelier Protocol. It should be noted that only a few adjustments are needed to actually make these adaptors ERC4626 agnostic, and therefore the Sommelier project strives to provide more public-good oriented adaptors as the project develops.&#x20;

**When considering how to build an adaptor, the below steps serve as a good high level guide:**

1. Think about what you want the ERC 4626 Vaults you're working with to do with the external protocol.
2. Outline "Strategist Functions," aka functions that enact external function calls to the external protocol with the Vault funds / context.
3. Implement these "Strategist Functions."
4. Implement any 'hooks' from your respective protocol. Ex.) Sommelier Protocol Architecture allows having their ERC4626 Vaults to have a \`holdingPosition\`. The \`holdingPosition\` is where Vault funds can be automatically transferred using a Strategist Function with a respective ERC4626 Adaptor.
5. Implement any 'pricing' mechanisms required to have the ERC4626 Adaptor compatible with the Vault architecture you are working with.

The Sommelier protocol github repo actually stores fully-ready Sommelier-Oriented adaptors [here](https://github.com/PeggyJV/cellar-contracts/tree/main/src/modules/adaptors).

If you are interested in creating Sommelier-Specific adaptors, continue to the next sections where key components of Sommelier-Adaptor implementation are discussed.
