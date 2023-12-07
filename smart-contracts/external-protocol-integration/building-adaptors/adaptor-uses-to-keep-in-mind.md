# Adaptor Uses to Keep in Mind

A core aspect of developing a new Sommelier Cellar is determining which adaptors the Cellar may use, and how those adaptors work together. Adaptor developers should keep the following guidelines in mind when designing support for strategies that require multiple adaptors to interact to support a strategy.

### Multicall

The nature of `callOnAdaptor` in [Cellar.sol](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol) is that a strategist can submit multiple adaptor calls, to multiple different adaptors, and have them executed atomically and serially. Additionally, Cellars support [Multicall](https://docs.uniswap.org/contracts/v3/reference/periphery/base/Multicall). Therefore, adaptor developers should gear their designs towards single-responsibility principles, knowing that atomicity across multiple adaptors can still be achieved.

For example, a common use case for LP-based strategies is the "zap" - where half of one asset is swapped for another asset right before entering a liquidity pool for those two assets. There is no need for a single adaptor to support both "swap" and "deposit LP" functionality in a single adaptor call. Atomicity and slippage protection are basically achieved by chaining multiple adaptor calls together - one to an adaptor designed for swapping tokens, then, subsequently, one designed for depositing to an LP pool. Strategists should be instructed on how to submit "batches" that achieve these multi-step processes, as opposed to having multi-step processes be native to adaptors themselves.

### Untracked Assets

The `totalAssets` check ensures that any rebalance made via adaptor calls does not affect the total value of _tracked assets_. However, untracked assets can be used as intermediate positions within a series of adaptor calls, as long as the asset is eventually swapped to a tracked one in adaptor calls.\


For instance, in a Cellar that is set up to only hold USDC, WETH, and WBTC, a strategist can perform an intermediate swap from USDC to AAVE, as long as the AAVE tokens are subsequently swapped to one of the three approved assets. Additionally, Cellars can use adaptor calls to claim farming/staking rewards that may be denominated in untracked assets, as long as they are swapped to a tracked asset before the series of adaptor calls finishes and rebalance deviations are checked.



### Vesting

In some cases, adaptor developers may build scenarios by which a Cellar earns previously-untracked profit outside of the rebalance deviation. The most common use case for this is the claiming of staking and/or liquidity mining rewards.\
\
In these cases, even if swapped to a tracked asset, the profit cannot be immediately deposited into a Cellar due to the `totalAssets` check. In these cases, a [Vesting Adaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/VestingSimpleAdaptor.sol) and underlying vesting contract should be used.&#x20;

Vesting adaptors are designed such that over time, they can increase the TVL of a cellar. Funds deposited into a vesting adaptor are initially untracked - meaning that do not cause deviations in TVL. Over time, deposits into the vesting contract are progressively added to the TVL, and can be withdrawn back to the Cellar and moved to other positions.
