# Cellar Functions

While there are multiple versions of the Cellar contract architecture, most strategists will only be using Cellar v2.5. Therefore, this document will focus on the Cellar v2.5 functions.

The protobuf definitions can be found [here](). A `CellarV2_5` message is the top level type that is passed in the `call_data` field of a `ScheduleRequest`.

```protobuf
message CellarV2_5 {
    oneof call_type {
        // Represents a single function call
        FunctionCall function_call = 1;
        // Represents multiple, ordered function calls
        Multicall multicall = 2;
    }
}
```

## Fields

There is only one field in the `CellarV2_5` type, `call_type`. It determines the type of function call to execute. As this is a `oneof` field, only one of the following fields can be set:

- `function_call`: A single function call.
- `multicall`: Multiple function calls that will execute in a single transaction.

## Functions

The actual function types are defined inside of the `CellarV2_5` message and can be seen [here](https://github.com/PeggyJV/steward/blob/fe82aa23542bdf6c28233b59ba507b9beb52014a/proto/steward/v4/cellar_v2.proto#L788-L1012) or read on for the details of each function.

Note, some functions are destined to be gated by governance and are only temporarily availabe while we migrate strategists to the new workflow. These will be marked as such below.

Additionally, the Cellar contract can be found [here](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol). Each of the definitions below map to a function of the same name in the solidity code.

Sure! Here is the complete Markdown v2 documentation in one code block:

```markdown
### AddPosition

Insert a trusted position to the list of positions used by the cellar at a given index.

Represents function `addPosition(uint32 index, uint32 positionId, bytes configurationData, bool inDebtArray)`

#### Fields
- `index`: (`uint32`) Index at which to add the position
- `position_id`: (`uint32`) The position's ID in the cellar registry
- `configuration_data`: (`bytes`) Data used to configure how the position behaves
- `in_debt_array`: (`bool`) Whether to add position in the debt array, or the credit array

### CallOnAdaptor

Allows strategists to manage their Cellar using arbitrary logic calls to adaptors.

Represents function `callOnAdaptor(AdaptorCall[] memory data)`

#### Fields
- `data`: (`AdaptorCall[]`) Array of adaptor calls

### RemovePosition

Remove the position at a given index from the list of positions used by the cellar.

Represents function `removePosition(uint32 index, bool inDebtArray)`

#### Fields
- `index`: (`uint32`) Index at which to remove the position
- `in_debt_array`: (`bool`) Whether to remove position from the debt array, or the credit array

### SetHoldingPosition

Set the holding position used of the cellar.

Represents function `setHoldingIndex(uint8 index)`

#### Fields
- `position_id`: (`uint32`) ID (index) of the new holding position to use

### SetStrategistPayoutAddress

Sets the Strategists payout address.

Represents function `setStrategistPayoutAddress(address payout)`

#### Fields
- `payout`: (`string`) Payout address

### SwapPositions

Swap the positions at two given indeces.

Represents function `swapPositions(uint32 index1, uint32 index2)`

#### Fields
- `index_1`: (`uint32`) Index of the first position
- `index_2`: (`uint32`) Index of the second position
- `in_debt_array`: (`bool`) Whether to switch positions in the debt array, or the credit array

### SetShareLockPeriod

Allows share lock period to be updated.

Represents function `setShareLockPeriod()`

#### Fields
- `new_lock`: (`string`) New lock period

### InitiateShutdown

Shutdown the cellar. Used in an emergency or if the cellar has been deprecated.

Represents function `initiateShutdown()`

### LiftShutdown

Allows the owner to restart a shut down Cellar

Represents function `liftShutdown()`

### Multicall

Allows caller to call multiple functions in a single TX.

Represents function `multicall(bytes[] data)`

#### Fields
- `function_calls`: (`FunctionCall[]`) Array of function calls

### RemoveAdaptorFromCatalogue

Allows callers to remove adaptors from this cellar's catalogue

Represents function `removeAdaptorFromCatalogue(address adaptor)`

#### Fields
- `adaptor`: (`string`) Adaptor address

### RemovePositionFromCatalogue

Allows caller to remove positions from this cellar's catalogue

Represents function `removePositionFromCatalogue(uint32 positionId)`

#### Fields
- `position_id`: (`uint32`) Position ID

### DecreaseShareSupplyCap

Allows strategist to decrease the share supply cap

Represents function `decreaseShareSupplyCap(uint192)`

#### Fields
- `new_cap`: (`string`) New share supply cap

### SetAlternativeAssetData

Allows the strategist to add, or update an existing alternative asset deposit.

Represents function `setAlternativeAssetData(ERC20 _alternativeAsset, uint32 _alternativeHoldingPosition, uint32 _alternativeAssetFee)`

#### Fields
- `alternative_asset`: (`string`) The address of the alternative asset
- `alternative_holding_position`: (`uint32`) The holding position to direct alternative asset deposits to
- `alternative_asset_fee`: (`uint32`) The fee to charge for depositing this alternative asset

### DropAlternativeAssetData

Allows the strategist to stop an alternative asset from being deposited.

Represents function `dropAlternativeAssetData(ERC20 _alternativeAsset)`

#### Fields
- `alternative_asset`: (`string`) The address of the alternative asset

### AddAdaptorToCatalogue

Allows the owner to add an adaptor to the Cellar's adaptor catalogue

Represents function `addAdaptorToCatalogue(address adaptor)`

#### Fields
- `adaptor`: (`string`) Adaptor address

### AddPositionToCatalogue

Allows the owner to add a position to the Cellar's position catalogue

Represents function `addPositionToCatalogue(uint32 positionId)`

#### Fields
- `position_id`: (`uint32`) Position ID

### SetRebalanceDeviation

Changes the cellar's allowed rebalance deviation, which is the percent the total assets of a cellar may deviate during a `callOnAdaptor`(rebalance) call. The maximum allowed deviation is 100000000000000000 (0.1e18), or 10%.

Represents function `setRebalanceDeviation(uint256)`

#### Fields
- `new_deviation`: (`string`) New deviation

### SetStrategistPlatformCut

Allows strategist to set the platform cut for the cellar.

Represents function `setStrategistPlatformCut(uint64 cut)`

#### Fields
- `new_cut`: (`uint64`) The new strategist platform cut

### IncreaseShareSupplyCap

Allows the caller to increase the share supply cap

Represents function `increaseShareSupplyCap(uint192 _newShareSupplyCap)`

#### Fields
- `new_cap`: (`string`) New share supply cap

### SetSharePriceOracle

Allows the caller to set the share price oracle contract

Represents function `setSharePriceOracle(uint256 _registryId, ERC4626SharePriceOracle _sharePriceOracle)`

#### Fields
- `registry_id`: (`string`) The oracle registry ID
- `share_price_oracle`: (`string`) The oracle contract address

### CachePriceRouter

Updates the cellar to use the latest price router in the registry.

Represents function `cachePriceRouter(bool checkTotalAssets, uint16 allowableRange, address expectedPriceRouter)`

#### Fields
- `check_total_assets`: (`bool`) Whether to check the total assets of the cellar
- `allowable_range`: (`uint32`) The allowable range of the cellar's total assets to deviate between old and new routers
- `expected_price_router`: (`string`) The expected price router address
```
