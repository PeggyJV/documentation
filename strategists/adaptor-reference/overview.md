# Adaptor Reference Overview

## API Reference

The [Steward API reference](https://github.com/PeggyJV/steward/blob/main/docs/api/steward_api_doc.md) is generated directly from the protobuf files and appear in alphabetical order. 

## AdaptorCall type

The `CallOnAdaptor` Cellar function allows the strategist the leverage any adaptor contract that is approved by governance and not blocked by Steward. Adaptors are contracts that provide interfaces to other smart contracts such as utility contracts or DeFi protocols such as Aave, Uniswap, Balancer, etc. The source code for adaptor contracts can be found [in the cellar-contracts repository](https://github.com/PeggyJV/cellar-contracts/tree/main/src/modules/adaptors)

All adaptor calls are wrapped in the `AdaptorCall` type, which contains the address of the target adaptor, and the actual adaptor call data. The source code can be found [here](https://github.com/PeggyJV/steward/blob/fe82aa23542bdf6c28233b59ba507b9beb52014a/proto/steward/v4/cellar_v2.proto#L1273-L1350):

```protobuf
// Represents a call to an adaptor contract. The cellar must be authorized to call the target adaptor.
message AdaptorCall {
    // Address of the adaptor
    string adaptor = 1;
    // The function call data for the adaptor
    oneof call_data {
        // Represents function calls to the AaveATokenAdaptor V1
        AaveATokenAdaptorV1Calls aave_a_token_v1_calls = 2;
        // Represents function calls to the AavaDebtTokenAdaptor V1
        AaveDebtTokenAdaptorV1Calls aave_debt_token_v1_calls = 3;
        // Represents function calls to the CompoundCTokenAdaptor V2
        CompoundCTokenAdaptorV2Calls compound_c_token_v2_calls = 4;
        // Represents function calls to the AaveATokenV2Adaptor
        AaveATokenAdaptorV2Calls aave_a_token_v2_calls = 5;
        // Represents function calls to the AavaDebtTokenV2Adaptor
        AaveDebtTokenAdaptorV2Calls aave_debt_token_v2_calls = 6;
        // Represents function calls to the AaveATokenV1Adaptor
        AaveV3ATokenAdaptorV1Calls aave_v3_a_token_v1_calls = 7;
        // Represents function calls to the AavaDebtTokenV1Adaptor
        AaveV3DebtTokenAdaptorV1Calls aave_v3_debt_token_v1_calls = 8;
        // Represents function calls to the OneInchAdaptorV1
        OneInchAdaptorV1Calls one_inch_v1_calls = 9;
        // Represents function calls to the FeesAndReservesAdaptorV1
        FeesAndReservesAdaptorV1Calls fees_and_reserves_v1_calls = 10;
        // Represents functionc alls to the ZeroXAdaptorV1
        ZeroXAdaptorV1Calls zero_x_v1_calls = 11;
        // Represents function calls to the SwapWithUniswapAdaptorV1
        SwapWithUniswapAdaptorV1Calls swap_with_uniswap_v1_calls = 12;
        // Represents function calls to VestingSimpleAdaptor
        VestingSimpleAdaptorV2Calls vesting_simple_v2_calls = 13;
        // Represents function calls to the CellarAdaptor
        CellarAdaptorV1Calls cellar_v1_calls = 14;
        // Represents function calls to the UniswapV3Adaptor V2
        UniswapV3AdaptorV2Calls uniswap_v3_v2_calls = 15;
        // Represents function calls to the AaveV2EnableAssetAsCollatorAdaptor V1
        AaveV2EnableAssetAsCollateralAdaptorV1Calls aave_v2_enable_asset_as_collateral_v1_calls = 16;
        // Represents function calls to the FTokenAdaptor V1
        FTokenAdaptorV1Calls f_token_v1_calls = 17;
        // Represents function calls to the MorphoAaveV2AToken V1
        MorphoAaveV2ATokenAdaptorV1Calls morpho_aave_v2_a_token_v1_calls = 18;
        // Represents function calls to the MorphoAaveV2DebtToken V1
        MorphoAaveV2DebtTokenAdaptorV1Calls morpho_aave_v2_debt_token_v1_calls = 19;
        // Represents function calls to the MorphoAaveV3ATokenCollateral V1
        MorphoAaveV3ATokenCollateralAdaptorV1Calls morpho_aave_v3_a_token_collateral_v1_calls = 20;
        // Represents function calls to the MorphoAaveV3ATokenP2P V1
        MorphoAaveV3ATokenP2PAdaptorV1Calls morpho_aave_v3_a_token_p2p_v1_calls = 21;
        // Represents function calls to the MorphoAaveV3DebtToken V1
        MorphoAaveV3DebtTokenAdaptorV1Calls morpho_aave_v3_debt_token_v1_calls = 22;
        // Represents function calls to the BalancerPoolAdaptor V1
        BalancerPoolAdaptorV1Calls balancer_pool_v1_calls = 23;
        // Represents function calls to the LegacyCellarAdaptor V1
        LegacyCellarAdaptorV1Calls legacy_cellar_v1_calls = 24;
        // Represents function calls to the DebtFTokenAdaptor V1
        DebtFTokenAdaptorV1Calls debt_f_token_v1_calls = 25;
        // Represents function calls to the CollateralFTokenAdaptor V1
        CollateralFTokenAdaptorV1Calls collateral_f_token_v1_calls = 26;
        // Represents function call for the AaveV3DebtTokenAdaptorV1
        AaveV3DebtTokenAdaptorV1FlashLoanCalls aave_v3_debt_token_v1_flash_loan_calls = 27;
        // Represents function call for the BalancerPoolAdaptorV1
        BalancerPoolAdaptorV1FlashLoanCalls balancer_pool_v1_flash_loan_calls = 28;
        // Represents function calls for the ConvexCurveAdaptorV1
        ConvexCurveAdaptorV1Calls convex_curve_v1_calls = 29;
        // Represents function calls for the CurveAdaptorV1
        CurveAdaptorV1Calls curve_v1_calls = 30;
        // Represents function calls for the AuraERC4626AdaptorV1
        AuraERC4626AdaptorV1Calls aura_erc4626_v1_calls = 31;
        // Represents function calls for the MorphoBlueCollateralAdaptorV1
        MorphoBlueCollateralAdaptorV1Calls morpho_blue_collateral_v1_calls = 32;
        // Represents function calls for the MorphoBlueDebtAdaptorV1
        MorphoBlueDebtAdaptorV1Calls morpho_blue_debt_v1_calls = 33;
        // Represents function calls for the MorphoBlueSupplyAdaptorV1
        MorphoBlueSupplyAdaptorV1Calls morpho_blue_supply_v1_calls = 34;
        // Represents function calls for the ERC4626AdaptorV1 
        ERC4626AdaptorV1Calls erc4626_v1_calls = 35;
        // Represents function calls for the StakingAdaptorV1
        StakingAdaptorV1Calls staking_v1_calls = 36;
    }
}
```

Notice that each available call data type has a plural name, for example `StakingAdaptorV1Calls`. These types are wrappers for their actual respective call types, in this case `StakingAdaptorV1Call` (no 's' at the end) that allow multiple function calls to the same adaptor to be executed in one transaction.  

Each protocol has its own .proto file where types are defined for adaptor-specific functions. In general, only the latest version of an adaptor proto should be used. That is, if there is a file containing both MyAdaptorV1 and MyAdaptorV2 types, V1 is probably only relevant for older Cellars.

The full list of .proto files defining functions for each adaptor can be found [here](https://github.com/PeggyJV/steward/tree/main/proto/steward/v4)

## Flashloans

Some adaptors have a flashloan function. To avoid circular dependencies in the protos, seperate protobuf files are created for the flashloan functionality, and these files have their own near-identical `AdaptorCall` definitions. This means when using an adaptor call in a flashloan you will need to be sure you are importing the correct `AdaptorCall` type. For example, the [Aave V3 Debt Token Flash Loan](https://github.com/PeggyJV/steward/blob/main/proto/steward/v4/aave_v3_debt_token_flash_loan.proto) proto is for the same adaptor as `aave_v3_debt_token.proto`, and defines it's own `AdaptorCall` enum called `AdaptorCallForAaveV3FlashLoan`. 

## Adaptor Call Wrappers and Common Functions

All adaptor call types have a corresponding wrapper to allow multiple adaptor function calls in the same transaction. For example:

```protobuf
// Represents call data for the Aave AToken adaptor V2, used to manage lending positions on Aave
message AaveATokenAdaptorV2 {
    oneof function {
        /***** BASE ADAPTOR FUNCTIONS *****/

        // Represents function `revokeApproval(ERC20 asset, address spender)`
        RevokeApproval revoke_approval = 1;

        /***** ADAPTOR-SPECIFIC FUNCTIONS *****/

        // Represents function `depositToAave(ERC20 tokenToDeposit, uint256 amountToDeposit)`
        DepositToAave deposit_to_aave = 2;
        // Represents function `withdrawFromAave(ERC20 tokenToWithdraw, uint256 amountToWithdraw)`
        WithdrawFromAave withdraw_from_aave = 3;
    }
    
    // ... function definitions below
}

// Wrapper
message AaveATokenAdaptorV2Calls {
    repeated AaveATokenAdaptorV2 calls = 1;
}
```

Additionally, all adaptor call types support the `RevokeApproval` cellar function, which allows the strategist to revoke approval for an adaptor contract to spend a specific ERC20 token. This function is useful for security purposes, as it allows the strategist to limit the permissions of an adaptor contract.

## Function Type Definitions

Each adaptor call message defines it's own function types within itself. This way, function names are scoped only to the adaptor call type they are relevant to in the protobuf files. For example, the `AaveATokenAdaptorV2` message defines the `DepositToAave` function, which is specific to the Aave AToken adaptor V2, and can be found below the `oneof` shown in the earlier example:

```protobuf
// Represents call data for the Aave AToken adaptor V2, used to manage lending positions on Aave
message AaveATokenAdaptorV2 {
    oneof function {
        /***** BASE ADAPTOR FUNCTIONS *****/

        // Represents function `revokeApproval(ERC20 asset, address spender)`
        RevokeApproval revoke_approval = 1;

        /***** ADAPTOR-SPECIFIC FUNCTIONS *****/

        // Represents function `depositToAave(ERC20 tokenToDeposit, uint256 amountToDeposit)`
        DepositToAave deposit_to_aave = 2;
        // Represents function `withdrawFromAave(ERC20 tokenToWithdraw, uint256 amountToWithdraw)`
        WithdrawFromAave withdraw_from_aave = 3;
    }

    /*
    * Allows strategists to lend assets on Aave.
    *
    * Represents function `depositToAave(ERC20 tokenToDeposit, uint256 amountToDeposit)`
    */
    message DepositToAave {
        // The address of the ERC20 token to deposit
        string token = 1;
        // The amount to deposit
        string amount = 2;
    }

    // ... more function definitions below

}
```

This structure will be reflected in the location of each type in in the your generated proto bindings library.
