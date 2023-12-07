---
description: A detailed breakdown of the Registry.sol contract.
---

# Registry Details

<figure><img src="../../../.gitbook/assets/Registry-Focused High-Level Schematic.jpg" alt=""><figcaption><p>Basic Permissions Schematic</p></figcaption></figure>

The [Registry](https://github.com/PeggyJV/cellar-contracts/blob/main/src/Registry.sol) is owned by Sommelier governance and is responsible for providing on-chain pointers to canonical addresses used by Cellars and other components of the protocol.

Cellars and other smart contracts ask the Registry for the address pointers to other resources in the Sommelier ecosystem. The Registry supports the following flows:

* New canonical address pointers can be registered by governance via `register`. Every registered contract is stored in an append-only array. Therefore, integrating contracts [must be aware](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol#L437) of any needed resources' slot within the `getAddress` array. Failing to use the correct slot may result in getting the wrong address pointer, potentially leading to bugs.\

* The fee collector address on the Sommelier chain can be changed by governance via `setFeesDistributor`. Changing the fee collector address means that fees earned by Cellars will be sent to a new address when bridged back onto the native Sommelier Chain. The Fee Collector address on the Sommelier chain is responsible for redistributing fees to Sommelier protocol validators. See [Fees And Reserves](../fees-and-reserves.md) for more information.\

* Adaptors (new external token or protocol integrations) can be added to the registry by governance via `trustAdaptor`.\
  \
  Once an adaptor has been trusted in the registry, the Registry can also trust positions that use the adaptor (see [`trustPosition`](https://github.com/PeggyJV/cellar-contracts/blob/51a3f88a497d32f79290b803498128c0e8184b2b/src/Registry.sol#L416)). \

* Positions (specific market positions that can be taken through the use of adaptors) can be added to the Registry by governance via `trustPosition`. The `adaptor` argument in `trustPosition` should represent a smart contract that allows Cellar integration with an external token or protocol (see "Adaptors"[^1]). Compared to adaptors, positions are registered with specific payloads (see [`adaptorData`](https://github.com/PeggyJV/cellar-contracts/blob/51a3f88a497d32f79290b803498128c0e8184b2b/src/Registry.sol#L342C1-L343C1)), that specify position-specific data, such as assets used.\
  \
  Once a position has been trusted in the registry, Cellars can add the position to their own list of tracked positions and integrations according to risk rules. Adaptor and Cellar risks must be coordinated as outlined before.&#x20;

#### Example Use Case: Supporting DAI/USDC Uniswap V3 LP Positions

1. First, an [Adaptor contract](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/Uniswap/UniswapV3Adaptor.sol) must be built that contains all smart contract logic for integrating with the Uniswap V3 protocol. Once complete, that adaptor should be deployed on-chain, to an address - for example: `0xF00`.\

2. Once the adaptor is deployed to `0xF00`, and proper off-chain controls have taken place (audits, security review, governance votes), governance must trust the adaptor using the `trustAdaptor` function in the registry. \

3. Once the Uniswap adaptor is deployed and trusted, governance can start allowing Cellars to take specific positions that use that adapter. To enable the DAI/USDC pool, governance must call the `trustPosition` function in the registry, using the `0xF00` adaptor address, and [encode the DAI/USDC pool](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/UniSwap/UniswapV3Adaptor.sol#L25) in `adaptorData`. Once the position is trusted in the Registry, it will be assigned a unique `positionId`.\

4. Once the Registry has trusted the UniswapV3Adaptor and the DAI/USDC pool, Cellars can start using the adaptor.
5. Cellars now call `addAdaptorToCatalogue()` and `addPositionToCatalogue()`. These calls allow Strategists to make Strategist Calls with the specific Cellar without governance approval. This applies to Cellars with v2 architecture and onward.

> For Cellars with v1 & v1.5 architecture, the `adaptorCatalogue` was not in use yet. Instead, the `Cellar` setup steps taken instead of using the `adaptorCatalogue` were as follows: First, for each target Cellar, either governance or the strategist should call `setupAdaptor`, passing the `0xF00` address in as an argument. In its execution logic, the Cellar will call `cellarSetupAdaptor` in the registry.&#x20;

6. Once the adaptor is set up in each Cellar, the strategist can set up their DAI/USDC position by calling `addPosition`. The strategist should specify the `index` (see ["Position Ordering" in the Cellars section](#user-content-fn-2)[^2]), the `positionId` generated in Step 3, and should specify with the `inDebtArray` boolean whether the position is a debt position (in this example case, the value should be `false`). [For some positions, the strategist may also need to supply `configurationData`,](#user-content-fn-3)[^3] but in the case of Uniswap V3, this is not used. In its execution logic, the Cellar will call `cellarAddPosition` in the registry.\

7. Once the position is added to the Cellar, the strategist can add/remove liquidity from the DAI/USDC Uniswap V3 pool, and perform other protocol-specific operations allowed by the adaptor contract, via invocations to `callOnAdaptor`.

See the next page for a current API for `Registry.sol`.

[^1]: Link?

[^2]: Insert link

[^3]: Maybe add a link to further info on this?
