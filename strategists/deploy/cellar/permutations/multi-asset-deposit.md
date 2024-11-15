# Multi Asset Deposit

To enable multi asset deposit, the alternative deposit assets need to be added to the cellar.
This should be done after adding positions.

``` solidity
cellar.setAlternativeAssetData(USDC, ERC20_USDC_POSITION, 0.0005e8);
```
First argument is the ERC20 token which is usually imported from [address](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/test/resources/Arbitrum/ArbitrumAddressesPeggyJV.sol) file.
`usdcPosition` is the ERC20 adaptor position for the token, and it usually comes from [Position Ids file](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/resources/PositionIds.sol).
Last argument is the fee to charge for depositing this alternative asset.
