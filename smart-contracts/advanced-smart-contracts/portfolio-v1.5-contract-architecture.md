# Portfolio (V1.5) Contract Architecture

A number of iterations of Sommelier Cellars have been deployed since July 2022, when the first V1 cellar was launched. The purpose of the rest of this documentation is to describe the current architecture of the Sommelier Cellars platform, dubbed Cellars V2. However, for developers who find the need to work with older Cellar architecture iterations, there are a number of important differences to keep in mind.

Before the generalized architecture of Cellars V2, there were deployments of Cellar iterations dubbed V1 and V1.5. The [AAVE Stablecoin Cellar](https://etherscan.io/address/0x7bAD5DF5E11151Dc5Ee1a648800057C5c934c0d5) is the only cellar built on V1 architecture, which is purpose-built for the single use case of interacting with AAVE.

### V1.5 Cellars

While there is only one V1 Cellar, there are a number of Cellars built on the [V1.5 architecture](https://github.com/PeggyJV/cellar-v1\_5), called "Portfolio Cellars". This name derives from the fact that the V1.5 architecture is built to enable a single Cellar to hold a basket of ERC20 tokens outright and swap between them. In this way, the Cellar can manage an allocated portfolio, but can not perform ad hoc DeFi operations in the way that V2 cellars can.

V1.5 Cellars do not support adaptors - instead, they support a `rebalance` [operation](https://github.com/PeggyJV/cellar-v1\_5/blob/release/src/base/Cellar.sol#L1429), which allows the strategist to swap any owned tokens for other tokens, through either Uniswap V2 or Uniswap V3. Like in V2, constraints around allowed rebalance deviations and price router support for allocated assets also apply.

Due to their streamlined nature, certain strategies may remain a good fit for the simplified V1.5 architecture. Strategies which require nothing more than outright ownership of positions according to a target allocation can be fully supported by V1.5, without the additional integration complexity of V2 adaptors. It's recommended that Cellar developers consider their specific use case when building strategies or extending Cellar functionality, and in same cases V1.5 may remain a good starting point.
