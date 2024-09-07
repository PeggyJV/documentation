# Environment setup

First lets go over what is needed to write and run scripts and smart contract in solidity. Hardhat or VSCode IDE with Solidity plugin is recommended for editing Solidity code.

To compile and deploy the contract we are also going to need Foundry toolchain. It can be installed by following installation guide [**here**](https://book.getfoundry.sh/getting-started/installation).

Next we can clone the cellar contracts [**repository**](https://github.com/PeggyJV/cellar-contracts).
``` bash
git clone https://github.com/PeggyJV/cellar-contracts.git
```

Inside the contract repository run:
``` bash
forge build
```
Now we need to fill the environment file. What variables are needed depends on what scripts the strategists wants to use.

Rename the ```sample.env``` file to ```.env```
What is definitely needed for deploying is the private key. The wallet should have enough funds to pay for the fees of deploying.

Whenever running a script you can check from the command which variables are needed. For example the following script needs a PRIVATE_KEY, ARBISCAN_KEY and ARBITRUM_RPC_URL from the environment file:

``` bash
source .env && forge script script/Arbitrum/peggyjv_production/DeployTestCellar.s.sol:DeployRealYieldProductsScript --evm-version london --etherscan-api-key $ARBISCAN_KEY --verify --resume --rpc-url $ARBITRUM_RPC_URL --private-key $PRIVATE_KEY
```
