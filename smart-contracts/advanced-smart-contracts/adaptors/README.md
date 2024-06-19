---
description: A detailed breakdown of the BaseAdaptor.sol contract.
---

# Adaptors

## Adaptors

Adaptors are the mechanisms by which strategists can interact with external protocols and allocate Cellar assets. All adaptors inherit from [BaseAdaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/BaseAdaptor.sol), which spells out a number of requirements for an adaptor to be used with Cellars:

* Each adaptor must have a unique `identifier`. Updated adaptors using the same protocol should increment a version number in their identifier.
* Each adaptor should report its base asset, via `assetOf`, and any other assets used, with `assetsUsed`.&#x20;
* An adaptor should report whether or not it allows the Cellar to take on debt, via `isDebt`.
* An adaptor should be able to report its own balance in terms of the `assetOf` asset, via the invocation of `balanceOf`.
* An adaptor should contain `deposit`, `withdraw`, and `withdrawableFrom` functions that allow Cellars to move assets into and out of the position in response to user deposit/withdrawal requests. For adaptors that do not represent liquid positions (such as adaptors for debt positions, like the [AaveDebtTokenAdaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/Aave/AaveDebtTokenAdaptor.sol)), the implementor can have `withdrawableFrom` return `0`.

For more information on adaptor architecture, see ["Building Adaptors."](../../external-protocol-integration/building-adaptors.md)

See the next page for a current API for `BaseAdaptor.sol`.
