## [L-01] -  `debtPosition.liquidityIndexAtRepayment` not updated on self liquidate 
The [selfLiquidate](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L223C1-L226C6) function is meant to be called by the lender to liquidate a loan in the case where the loan becomes undercollaterized or gets into a loss. As it is also a type of liquidation under the Size protocol mechanism, it follows the core liquidation logic but differs in the sense that it can only be executed by the lender. 

The self liquidate function will read the credit position of the lender and debit position of the borrower from storage and reduce both credit and debit positions, then transfer the collateral token from the borrower to the lender. But it doesnt update all necessary debitPositon struct values in storage. selfLiquidate doesnt update the `debtPosition.liquidityIndexAtRepayment` value during the liquidation like the sister liquidating functions  [liquidate()](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Liquidate.sol#L124) and `liquidateWithReplacement()` do. 

Since liquidations are a kind of "forced repayments" themselves, they should all update `debtPosition.liquidityIndexAtRepayment`. 

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SelfLiquidate.sol#L59C1-L71C6
```solidity
    function executeSelfLiquidate(State storage state, SelfLiquidateParams calldata params) external {
        emit Events.SelfLiquidate(params.creditPositionId);


        CreditPosition storage creditPosition = state.getCreditPosition(params.creditPositionId);
        DebtPosition storage debtPosition = state.getDebtPositionByCreditPositionId(params.creditPositionId);


        uint256 assignedCollateral = state.getCreditPositionProRataAssignedCollateral(creditPosition);


        // debt and credit reduction
        state.reduceDebtAndCredit(creditPosition.debtPositionId, params.creditPositionId, creditPosition.credit);


        state.data.collateralToken.transferFrom(debtPosition.borrower, msg.sender, assignedCollateral);
    }
```


## [L-02] - value of Tenor can change after the event emission 

In `executeSellCreditMarket`, the event `SellCreditMarket` is emitted just before the possible change to the tenor value. If the `params.creditPositionId` is not `RESERVED_ID`, the tenor value will be recalculated and the earlier event emitted will not show the actual tenor value used by the function. 

It is better to emit the event at a later part of the code, after all computation so that the tenor value that was actually used can be emitted with the event. 

Same issue is found in [executeBuyCreditMarket()](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L121C1-L142) as the event `BuyCreditMarket` is emitted first before changes to the final values of params.borrower and params.tenor. Tenor and borrower can change later in the code.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L127C1-L146C1
```solidity
    function executeSellCreditMarket(State storage state, SellCreditMarketParams calldata params)
        external
        returns (uint256 cashAmountOut)
    {
        emit Events.SellCreditMarket(
            params.lender, params.creditPositionId, params.tenor, params.amount, params.tenor, params.exactAmountIn
        );


        // slither-disable-next-line uninitialized-local
        CreditPosition memory creditPosition;
        uint256 tenor;
        if (params.creditPositionId == RESERVED_ID) {
            tenor = params.tenor;
        } else {
            DebtPosition storage debtPosition = state.getDebtPositionByCreditPositionId(params.creditPositionId);
            creditPosition = state.getCreditPosition(params.creditPositionId);


            tenor = debtPosition.dueDate - block.timestamp;
        }
```


## [L-03] -  Missing validation `d.weth` parameter
There is no validation for `InitializeDataParams.weth` address in the `Initialize.validateInitializeDataParams()` code. This means it can be a null/invalid address. It should be checked to not be a null address just like it's done for [d.variablePool](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Initialize.sol#L164-L166)

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Initialize.sol#L146-L167
```solidity 
struct InitializeDataParams {
    address weth;
    address underlyingCollateralToken;
    address underlyingBorrowToken;
    address variablePool;
}

    function validateInitializeDataParams(
        InitializeDataParams memory d
    ) internal view {
        // validate underlyingCollateralToken
        if (d.underlyingCollateralToken == address(0)) {
            revert Errors.NULL_ADDRESS();
        }
        if (IERC20Metadata(d.underlyingCollateralToken).decimals() > 18) {
            revert Errors.INVALID_DECIMALS(
                IERC20Metadata(d.underlyingCollateralToken).decimals()
            );
        }

        // validate underlyingBorrowToken
        if (d.underlyingBorrowToken == address(0)) {
            revert Errors.NULL_ADDRESS();
        }
        if (IERC20Metadata(d.underlyingBorrowToken).decimals() > 18) {
            revert Errors.INVALID_DECIMALS(
                IERC20Metadata(d.underlyingBorrowToken).decimals()
            );
        }

        // validate variablePool
        if (d.variablePool == address(0)) {
            revert Errors.NULL_ADDRESS();
        }

        //@audit missing validation for d.weth parameter
    }
```

## [L-04] -  `overdueCollateralProtocolPercent` < `collateralProtocolPercent` is not enforced or implemented in code as described by the doc

Per the docs, is the `overdueCollateralProtocolPercent` penalty paid for overdue loans that are still well collaterized and `collateralProtocolPercent` is the penalty paid for undercollaterized loans, overdue or not. The docs also describe that the system/protocol's operational logic is to ascribe a lower penalty to overdue loans that are well collaterized 

https://docs.size.credit/non-technical/position-health-and-liquidations#liquidations-1
![28C159B7-C3AD-4C01-A0F5-9BA2EBE2BCA2_4_5005_c](https://github.com/adeolu98/audits-draft/assets/39372980/bb3250b4-5fe5-4c4f-a7da-ec80cff988a4)

But when setting this values in storage, there is not sufficent check to validate that `overdueCollateralProtocolPercent` value is indeed lower than the `collateralProtocolPercent` as seen in the [validateInitializeFeeConfigParams()](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Initialize.sol#L70-L94) logic. The logic only enforces that the values should be below `PERCENT` i.e 100%. 

### Recommended Mitigation
add check that enforces `overdueCollateralProtocolPercent` value is indeed lower than the `collateralProtocolPercent` and reverts if `overdueCollateralProtocolPercent` value is greater than the `collateralProtocolPercent`. 

Add this check as last logic after modification of storage values in `Initialize.executeInitializeFeeConfig()` and `UpdateConfig.executeUpdateConfig()` functions. 


## [L-05] - value of compensation amount can change after the event Compensate emission 

In `executeCompensate`, the event `Compensate` is emitted just before the possible change to the compensation amount value. `param.amount` is the amount to compensate. 
 The code does a comparison of the `param.amount` value with `creditPositionWithDebtToRepay.credit` or 
`creditPositionToCompensate.credit` (depending on if `param.creditPositionToCompensate` is RESERVED_ID or not) and then chooses the smallest value as the amount for compensation. 
The event doesnt recognise this later logic and instead emits `params.amount` as the amount for compensation and this may not be true as the compensated amount in all cases. 

It is better to emit the event at a later part of the code, after all computation so that the  actual compensation amount used can be emitted with the event. 

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Compensate.sol#L106-L157
```solidity
        emit Events.Compensate(
            params.creditPositionWithDebtToRepayId, params.creditPositionToCompensateId, params.amount
        );

        CreditPosition storage creditPositionWithDebtToRepay =
            state.getCreditPosition(params.creditPositionWithDebtToRepayId);
        DebtPosition storage debtPositionToRepay =
            state.getDebtPositionByCreditPositionId(params.creditPositionWithDebtToRepayId);

        uint256 amountToCompensate = Math.min(params.amount, creditPositionWithDebtToRepay.credit);

        CreditPosition memory creditPositionToCompensate;
        if (params.creditPositionToCompensateId == RESERVED_ID) {
            creditPositionToCompensate = state.createDebtAndCreditPositions({
                lender: msg.sender,
                borrower: msg.sender,
                futureValue: amountToCompensate,
                dueDate: debtPositionToRepay.dueDate
            });
        } else {
            creditPositionToCompensate = state.getCreditPosition(params.creditPositionToCompensateId);
            amountToCompensate = Math.min(amountToCompensate, creditPositionToCompensate.credit);
        }
```

## [L-06] -`debtPosition.dueDate` is not updated on repayment
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/LoanLibrary.sol#L21-L23

```solidity
    // The due date of the loan
    // Updated on debt reduction
    uint256 dueDate;
```

The comments in the code indicate that the dueDate of a loan should be updated upon sucessful repayment of that loan but in [repayDebt()](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Repay.sol#L46) function, the debtPosition.dueDate is never updated on debt reduction/repayment. debtPosition.dueDate will always be the same value set at debtPosition creation even after its been paid already. 

`debtPosition.dueDate` should be updated on full repayment so as to not cause confusion or error as a paid loan position may still look like its overdue should in case curent time > the debtPosition.dueDate