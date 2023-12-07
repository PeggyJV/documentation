# Adaptor Configuration

Each Cellar can use a given adaptor with a specific configuration, as specified by the `adaptorData` and `configurationData` fields specified in the [Registry](https://github.com/PeggyJV/cellar-contracts/blob/main/src/Registry.sol#L347). When a set of adaptor functionality is paired with a specific configuration, the underlying functionality is termed a **position**.

When Cellars are configured to use certain adaptors, their usage is tied to a specific configuration. In most cases, this configuration specifies the tokens a given Cellar can interact with. For instance, a Cellar that wishes to LP WETH/USDC can be approved for a **position** that uses the Uniswap V3 adaptor, with WETH and USDC specified in `adaptorData`.

Where `adaptorData` specifies a position in terms of the underlying assets interacted with, `configurationData` can specify certain Cellar-specific parameters that affect the operational constraints of the position. For instance, `configurationData` is used with the AAVE adaptor to enforce cellar-specific minimum health factors.

Setting proper `adaptorData` and `configurationData` is the responsibility of governance when approving new positions - see "Roles & Permissions" for more information. Strategists do not have the ability to change or override these configured values - to do so, new positions with the same adaptor and different configurations must be approved for the Cellar by governance.
