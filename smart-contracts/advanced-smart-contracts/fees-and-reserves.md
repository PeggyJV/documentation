# Fees And Reserves

Fee generation is a core element of the Sommelier Cellars incentive model: when Cellars earn returns, fees can be disbursed to both the strategist and the network. Incentivizing strategists encourages the continued development of new and effective strategies for users, and incentivizing the network contributes to the security of the Sommelier Chain.

### Base Cellar Fee-Related Functionality

Sommelier Cellars are deployed with a configured `FeeData` [struct](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol#L464), which defines four properties:

* `strategistPlatformCut` - the percentage of fees that will go to the strategist, with 16 units of precision (`1e18 == 100%`).&#x20;
* `platformFee` - the percentage of total assets that will be assessed as fees, with 16 units of precision. This value is currently **deprecated,** and has been replaced by FeesAndReserves (see "FeesAndReserves Functionality" below).
* `lastAccrual` - the time fees were last accrued, for accounting purposes. This value is currently **deprecated,** and has been replaced by FeesAndReserves.
* `strategistPayoutAddress` - the address that will receive the strategist portion of earned fees.

Currently, `strategistPlatformCut` and `strategistPayoutAddress` for a given Cellar can be updated by Sommelier Governance.

While the Cellar itself stores fee configuration in `FeeData`, and exposes getters and setters for that configuration, the Cellar itself contains no logic for the assessment or disbursal of fees. In order for the strategist and platform to account for, accrue, and disburse fees, a Cellar must use a `FeesAndReserves` contract with a `FeesAndReservesAdaptor` (more below).

### FeesAndReserves Functionality

The [FeesAndReserves](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/FeesAndReserves.sol) contract contains logic which reads Cellar fee configuration and is subsequently able to accrue and set aside fees for withdrawal. The FeesAndReserves contract contains specific accounting configuration for each Cellar in [the MetaData struct](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/FeesAndReserves.sol#L43).

Using FeesAndReserves, Cellars can assess two types of fees:

* **Management Fees** are flat-rate, annualized fees on total Cellar TVL. The `MetaData` struct contains a `managementFee` field, expressed in basis points.
* **Performance Fees** are earned any time a Cellar generates profit past its high-watermark (specified by `exactHighWatermark` in `MetaData`). The `MetaData` struct contains a `performanceFee` field, also expressed in basis points.&#x20;

Management and performance fees can be changed by the strategist using the `updatePerformanceFee` and `updateManagementFee` functions. When fees are changed, they only take effect _after_ the next fee accrual.

When fees accrue and are prepared for distribution to the platform, they _cannot_ come from currently-allocated positions. Instead, any future fees must be "reserved" by the strategist in the FeesAndReserves contract.

Strategists can reserve assets for fee disbursement by calling the function `addAssetsToReserves` (through the `FeesAndReservesAdaptor`, further described below). When assets are added to reserves, they are held by the `FeesAndReserves` contract and continue to contribute to Cellar TVL, but can not be deployed in positions. Strategists can also withdraw assets from reserves using `withdrawAssetsFromReserves`, meaning that those assets can no longer be used for the disbursal of fees.

It is optimal for the strategist to keep reserves funded equal to the level of eligible fees for disbursal. If too few assets are added to reserves, only the reserved amount can be withdrawn even if a larger amount of fees can be accounted for. The balance of fees can be later withdrawn after topping up reserves. Conversely, if too many assets are added to reserves, it will be a drag on the cellar's performance, reducing the fee-earning potential of the Cellar.&#x20;

Note that reserves must be funded in a single, pre-specified asset only - this is the asset specified by `reserveAsset` in `MetaData`.

#### Fee Accrual Mechanism

Fee accrual runs with a keeper-based architecture: accounting is performed and new fees are assessed only when triggered, reducing extra accounting overhead for user-facing Sommelier Cellar transactions.&#x20;

External parties can call the `checkUpkeep` [function](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/FeesAndReserves.sol#L440) to determine if new fees have been earned, and if new accounting should take place to accrue these fees to the platform. Each cellar can define its own upkeep parameters that determine when fees should be accrued:

* Cellars should set `frequency` in `MetaData` to set a cooldown time between fee accrual calls.
* Cellars can set `maxGas` in `MetaData` to make sure that fee accrual calls only happen when gas conditions are favorable. Since every position in a cellar must be read to accrue fees, the process can be gas-intensive for complex Cellars.

As long as cooldown periods and gas requirements allow it, for each Cellar, the accounting mechanism within `_calculateFees` will perform the following steps to determine new eligible fees:

1. The cellar's total assets and total supply will be checked. These values will be divided to determine the exact pre-share price of the Cellar.
2. The cellar will then calculate the new management fee: this is assessed on the delta in `totalAssets` between the current calculation and the last accrual, and is prorated such that `managementFee` is an annualized value.
3. If the cellar's share price has surpassed the previous `exactHighWatermark`, new performance fees will be calculated. This is assessed on the delta in `totalAssets` between the current calculation and the last accrual, multiplied by the difference between the current share price and the previous high watermark. The fee is prorated such that `performanceFee` is an annualized value.

Once `checkUpkeep` completes, the caller will receive a list of Cellars that are eligible for new fee accrual, and how many new fees an accrual operation will accrue. Note that `checkUpkeep` is a `view` function and does **not perform the accrual itself** - `performUpkeep` must be called to update `MetaData` with any new fee accrual values.

Calling `performUpkeep` will perform the same operations as `checkUpkeep`, but create new storage entries that update the amount of fees owed based on the above calculations. The Chainlink automation registry can bypass repeated calculation and pass in the input of `checkUpkeep` directly, encouraging established and approved Keepers to use this feature.

Once fees have been accrued and `MetaData` updated with new owed fees, new share prices and TVL, and the latest accrual time, fees can be prepared for disbursement.

#### Fee Disbursement Mechanism

When the `metaData` struct for a given Cellar reflects new `feesOwed`, those fees can be withdrawn from reserves and disbursed to the platform. To do so, the `prepareFees` function should be called. This function will:

* Debit `feesOwed` for the amount being prepared.
* Debit `reserves` for the amount being prepared. The reserves for the Cellar must contain this amount to prepare fees.
* Credit `feesReadyForClaim`, moving the assets out of Cellar `MetaData` accounting and into the withdrawal holding pool.

Once fees are in the withdrawal holding pool, they can be sent out to the platform and strategist using the `sendFees` function. Only `feesReadyForClaim` can be disbursed, and only the entire balance of ready fees can be sent. When called, `sendFees` will split earned fees into strategist and network portions, send strategist to the `strategistPayoutAddress` defined by the cellar. Platform fees will be sent to the Sommelier Chain via the Gravity Bridge.

Since disbursed fees are withdrawn from reserves, they are always delivered in the form of the `reserveAsset`. Strategists may need to re-deposit to reserves after a disbursement in order to facilitate future fee accruals.

#### Resetting High Watermark

In certain scenarios, Governance may reduce the high-watermark of a given Cellar. In these scenarios, the high-watermark cannot be increased. The `resetPercent` argument specifies in basis points what percentage of the delta between the cellar's current share price and the old high watermark should be removed from the new high-watermark. For instance, a `resetPercent` of `10000` will remove the entirety of the specified delta and reset the new high watermark to the current share price.

When resetting the high watermark, any owed fees are removed. Therefore in certain cases strategists should make sure to call `prepareFees` before the high watermark is reset.

### FeesAndReserves Adaptor Functionality

Like other adaptors , the [FeesAndReservesAdaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/FeesAndReserves/FeesAndReservesAdaptor.sol) wraps the relevant methods from the FeesAndReserves contract. The FeesAndReserves logic itself does not belong to the adaptor, since reserves are stored in an external contract and not in the cellar itself. The FeesAndReservesAdaptor enables all strategist-triggered functionality as specified in the documentation above.

Sommelier Cellar builders who wish to apply fees should use an already-deployed FeesAndReserves contract, and must have the FeesAndReservesAdaptor approved for their cellar.

