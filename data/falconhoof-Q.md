## [L-01] Minimum collateral in Self Liquidations

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SelfLiquidate.sol#L15-L18

Credit position holders who liquidate themselves via `selfLiquidate()` should be able to specify a `minimumCollateralProfit` parameter. This parameter, used in standard liquidations, ensures the liquidator does not receive less collateral than expected if, for example, the value of the collateral crashed when the transaction was in the mempool.

Self-liquidators should have the same protection, allowing them to leave the credit position unliquidated if the collateral received is less than anticipated. This way, they can wait in the hope that the position regains value and moves out of liquidation.

```
struct SelfLiquidateParams {
    // The credit position ID
    uint256 creditPositionId;
}
```

## [L-02] Events may emit incorrect information

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L125-L142

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L131-L145

The events defined in `BuyCreditMarket()` and `SellCreditMarket()` are emitted at the beginning of the functions and emit some which, further down the functions, may change.

In `BuyCreditMarket()` the `BuyCreditMarket` event is emitted with the values for `borrower` & `tenor` which were passed in the params. However, the values depend on whether the credit is bought from a new position or an existing position

```
        emit Events.BuyCreditMarket(
            params.borrower, params.creditPositionId, params.tenor, params.amount, params.exactAmountIn
        );

        CreditPosition memory creditPosition;
        uint256 tenor;
        address borrower;
        if (params.creditPositionId == RESERVED_ID) {
>>>         borrower = params.borrower;
>>>         tenor = params.tenor;
        } else {
            DebtPosition storage debtPosition = state.getDebtPositionByCreditPositionId(params.creditPositionId);
            creditPosition = state.getCreditPosition(params.creditPositionId);

>>>         borrower = creditPosition.lender;
>>>         tenor = debtPosition.dueDate - block.timestamp;
        }
```

In `SellCreditMarket()` the `SellCreditMarket` event is emitted with the values for `tenor` which were passed in the params. However, the values depend on whether the credit is bought from a new position or an existing position

```
     if (params.creditPositionId == RESERVED_ID) {
>>>         tenor = params.tenor;
        } else {
            DebtPosition storage debtPosition = state.getDebtPositionByCreditPositionId(params.creditPositionId);
            creditPosition = state.getCreditPosition(params.creditPositionId);

>>>         tenor = debtPosition.dueDate - block.timestamp;
        }
```

## [L-03] Redundant Tenor Check

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L98-L100

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/YieldCurveLibrary.sol#L121-L122

The tenor check below in [`validateSellCreditMarket()`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L98-L100) is redundant because a subsequent check in the execution ensures that the tenor is not greater than the timestamp of the final tenor.

```
        if (block.timestamp + tenor > loanOffer.maxDueDate) {
            revert Errors.DUE_DATE_GREATER_THAN_MAX_DUE_DATE(block.timestamp + tenor, loanOffer.maxDueDate);
        }
```

See below the subsequent check in [`getAPR()`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/YieldCurveLibrary.sol#L121-L122) which ensures the tenor is not greater than the timestamp of the final tenor

```
        if (tenor < curveRelativeTime.tenors[0] || tenor > curveRelativeTime.tenors[length - 1]) {
            revert Errors.TENOR_OUT_OF_RANGE(tenor, curveRelativeTime.tenors[0], curveRelativeTime.tenors[length - 1]);
        }
```