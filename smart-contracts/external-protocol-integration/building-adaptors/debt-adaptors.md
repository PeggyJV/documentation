# Debt Adaptors

Sommelier Cellars can incur leverage by borrowing assets from any lending protocol supported by an adaptor - to do this, a [debt adaptor](https://github.com/PeggyJV/cellar-contracts/blob/main/src/modules/adaptors/Aave/AaveDebtTokenAdaptor.sol) can be built. Debt adaptors are subject to the following guidelines:

* `isDebt` must return true.
* User deposits and withdrawals from the debt position should not be supported.
* `balanceOf` should report the total liability outstanding.
* Any function which incurs additional debt should only borrow up to liquidation thresholds and not cause a strategist to be able to liquidate their own Cellar.
