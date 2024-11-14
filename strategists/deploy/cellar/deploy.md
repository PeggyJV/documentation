# How to Deploy a Cellar Contract

Follow the instructions below to deploy a simple **[CellarWithOracle](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/script/Arbitrum/peggyjv_production/ExampleDeploy/DeployExampleCellar.s.sol)** contract using the provided script. In this example, we deploy minimal usable cellar.

This is the deploy script for the reference. Some helper functions are left out for simplicity.

``` solidity
contract DeployCellarScript is Script, ArbitrumAddresses, ContractDeploymentNames, PositionIds {
    using SafeTransferLib for ERC20;
    using Math for uint256;
    using stdJson for string;

    address public cellarOwner = dev0Address;
    uint256 public privateKey;
    
    Deployer public deployer = Deployer(deployerAddress);
    Registry public registry;
    PriceRouter public priceRouter;

    address public erc20Adaptor;
    address public aaveV3ATokenAdaptor;
    address public aaveV3DebtTokenAdaptor;


    CellarWithOracle public CELLAR;

    function setUp() external {
        privateKey = vm.envUint("PRIVATE_KEY");
        vm.createSelectFork(base);
        
        registry = Registry(deployer.getAddress(registryName));
        priceRouter = PriceRouter(deployer.getAddress(priceRouterName));
        erc20Adaptor = deployer.getAddress(erc20AdaptorName);
        aaveV3ATokenAdaptor = deployer.getAddress(aaveV3ATokenAdaptorName);
        aaveV3DebtTokenAdaptor = deployer.getAddress(aaveV3DebtTokenAdaptorName);
    }

    function run() external {
        ERC4626SharePriceOracle.ConstructorArgs memory args;
        args._heartbeat = 1 days;
        args._deviationTrigger = 0.005e4;
        args._gracePeriod = 1 days / 3;
        args._observationsToUse = 4;
        args._automationRegistry = automationRegistry;
        args._automationRegistrar = automationRegistrar;
        args._automationAdmin = devStrategist;
        args._link = address(LINK);
        args._startingAnswer = 1e18;
        args._allowedAnswerChangeLower = 0.75e4;
        args._allowedAnswerChangeUpper = 1.25e4;
        args._sequencerUptimeFeed = ARB_SEQUENCER_UPTIME_FEED;
        args._sequencerGracePeriod = 3_600;
        
        vm.startBroadcast(privateKey);

        // Deploy cellar.
        CELLAR = _createCellar(
            realYieldUsdName,
            "Example cellar",
            "CELLAR",
            USDC,
            ERC20_USDC_POSITION,
            abi.encode(true),
            0.1e6,
            0.8e18
        );

        // Add adaptors.
        CELLAR.addAdaptorToCatalogue(aaveV3ATokenAdaptor);
        CELLAR.addAdaptorToCatalogue(aaveV3DebtTokenAdaptor);

        // Add positions.
        CELLAR.addPositionToCatalogue(ERC20_USDC_POSITION);

        CELLAR.addPositionToCatalogue(AAVE_V3_LOW_HF_A_USDC_POSITION);
        CELLAR.addPositionToCatalogue(AAVE_V3_LOW_HF_DEBT_USDC_POSITION);

        // Create Share Price Oracle.
        args._target = CELLAR;
        ERC4626SharePriceOracle oracle = _createSharePriceOracle(realYieldUsdSharePriceOracleName, args);

        // Register cellar and oracle 
        registry.register(address(CELLAR));
        registry.register(address(oracle));

        // Set the oracle for cellar.
        CELLAR.setSharePriceOracle(4, oracle);

        // Initialize oracle.
        uint96 initialUpkeepFunds = 0.1e18;
        LINK.safeApprove(address(oracle), initialUpkeepFunds);

        oracle.initialize(initialUpkeepFunds);

        vm.stopBroadcast();
    }
```

### 1. Declarations

The script starts with importing some libraries. ArbitrumAddresses, ContractDeploymentNames, PositionIds are also included in the contract declaration. That allows us to access constants easily.

On the following lines there are some variables declared:


cellarOwner - The initial owner of the cellar, at first it should be dev0Address, later the ownership is transferred to Axelar Proxy.

privateKey - Has to be declared here, but will be assigned from the environment file.

deployer - Deployer will be used to get the deployer registry, price router and adaptor instances and to deploy Price Oracle and Cellar.

registry - Registry instance

priceRouter - Price Router instance

adaptors - `erc20Adaptor` is always needed in order for the cellar to interact with ERC20 tokens, 
other adaptors that the strategist wishes to use should be declared here like the `aaveV3ATokenAdaptor` and `aaveV3DebtTokenAdaptor`

### 2. setUp function
`setUp` function is run before the actual script. First we need to pull the private key from the environment file. 

The wallet that we use for deployment should have the initial deposit for the cellar and initial upkeep deposit for SharePriceOracle. 
Initial deposit to cellar can be decided by the strategist. Upkeep amounts for Chainlink automation is paid in LINK token and the minimal amounts can be found from their [docs](https://docs.chain.link/chainlink-automation/overview/supported-networks).

Next we are making a fork of base chain. That allows us to use the on-chain data.

On-chain data is needed on the next step where we use our deployer to get registry, price router and adaptor instances.

### 3. run function
Run function starts by defining constructor arguments for ERC4626SharePriceOracle. Meaning of these arguments is well explained in the contract [itself](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/ERC4626SharePriceOracle.sol).

After that the broadcast is started and the cellar is deployed. There is a helper function used for the deployment, that you can find on the original deploy contract.

Here are the cellars arguments explained. Further explanations can be found in [Cellar contract](https://github.com/PeggyJV/cellar-contracts/blob/zaki/PeggyJYDeployments/src/base/Cellar.sol)
``` solidity
CELLAR = _createCellar(
  realYieldUsdName,    -- Deployment name that is coming from ContractDeploymentNames and used by deployer
  "Example cellar",    -- Actual cellar name
  "CELLAR",            -- Cellar symbol
  USDC,                -- Cellar base asset ERC token, the value is coming from ArbitrumAddresses
  ERC20_USDC_POSITION, -- ERC20 adaptor postion for base asset, the value is coming from PositionIds
  abi.encode(true),    -- Configuration data for holding position
  0.1e6,               -- Initial deposit to cellar (0.1 USDC in that case)
  0.8e18               -- Platform Cut, determines how much platform fees go to strategist, 1e18 represents 100%, 0 represents 0%
);
``` 

We now have the Cellar instance. Next we can add our adaptors to it. 
There is no need to add ERC20 adaptor, but all the other ones should be added with the `CELLAR.addAdaptorToCatalogue(aaveV3DebtTokenAdaptor);` 

After adaptors we can add all the positions: `CELLAR.addPositionToCatalogue(AAVE_V3_LOW_HF_A_USDC_POSITION);`

That is almost all with the cellar configurations and we can move to ERC4626SharePriceOracle. 
First we add the cellar to already created arguments and after that deploy the Oracle with helper function.

Now lets register cellar and oracle in the Registry. After that we have to link the Oracle with the cellar.
For that we call `CELLAR.setSharePriceOracle(4, oracle);` 

The previous input assumes that the Price Router has been assigned to 2 on Registry. By registering in that order we added Cellar to 3 and Oracle to 4.
That is why we give "4" as the input for `setSharePriceOracle`. Now the cellar knows where in the registry the Oracle is found.

The last small step is to initialize the ERC4626SharePriceOracle. As it needs a LINK deposit we first have to approve the Oracle to use our wallets LINK.
After that we can call `oracle.initialize(initialUpkeepFunds);` with the initial funds.

In order to deploy run the following command in cellar-contracts repository.
```bash
source .env && forge script script/Arbitrum/peggyjv_production/ExampleDeploy/DeployTestCellar.s.sol:DeployCellarScript --evm-version london --with-gas-price 100000000 --slow --broadcast --rpc-url $ARBITRUM_RPC_URL --private-key $PRIVATE_KEY --etherscan-api-key $BASESCAN_KEY --verify
```

### 4. Verify
The `--verify` flag in deployment command should verify the contracts automatically however it doesn't always work. 
To verify contract manually use the following script:

```bash
source .env && forge verify-contract \
  0xfbe0ba2f0f05c47c4ee3026246c6e5e429bd2c6e \
  src/base/permutations/CellarWithOracle.sol:CellarWithOracle \
  --constructor-args $(cast abi-encode "constructor(address,address,address,string,string,uint32,bytes,uint256,uint64,uint192,address)" \
  dev0address \
  registry \
  baseasset token addresss \
  "Example cellar" \
  "CELLAR" \
  1 \
  0x0000000000000000000000000000000000000000000000000000000000000001 \
  100000 \
  800000000000000000 \
  6277101735386680763835789423207666416102355444464034512895) \
  --evm-version london \
  --etherscan-api-key $BASESCAN_KEY \
  --chain-id 42170 \
  --watch
```

It is important to provide the same constructor values as in the original deployment. 
Also make sure that the contract isn't modified in the meantime.
