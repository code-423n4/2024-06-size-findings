## Findings Summary

| Label | Description |
| - | - |
| [L-01] | validateLiquidate() lack of deadline limitation|
| [L-02] |  isCreditPositionTransferrable() Using isUserUnderwater() is risky|
## [L-01] validateLiquidate() lack of deadline limitation
Currently `validateLiquidate()` only validates that ` params.minimumCollateralProfit > liquidatorProfitCollateralToken`
and does not limit the `deadline`
If the price of the collateral falls sharply, without the `deadline` limit, the liquidator may experience losses 
```diff
    function validateLiquidate(State storage state, LiquidateParams calldata params) external view {
...
        if (!state.isDebtPositionLiquidatable(params.debtPositionId)) {
            revert Errors.LOAN_NOT_LIQUIDATABLE(
                params.debtPositionId,
                state.collateralRatio(debtPosition.borrower),
                state.getLoanStatus(params.debtPositionId)
            );
        }

+       // validate deadline
+       if (params.deadline < block.timestamp) {
+           revert Errors.PAST_DEADLINE(params.deadline);
+       }
        // validate minimumCollateralProfit
        // N/A
    }
```

## [L-02] isCreditPositionTransferrable() Using isUserUnderwater() is risky
The `isCreditPositionTransferrable()` user determines if `CreditPosition` is transferrable, using `isUserUnderwater()`.
If `CreditPosition` is infinitely close to the clearing line, it is still transferrable, which gives the receiver a lot of risk.
It is recommended to use `>=openingLimitBorrowCR` or the new configuration

```diff
    function isCreditPositionTransferrable(State storage state, uint256 creditPositionId)
        internal
        view
        returns (bool)
    {
+       addredss borrower = state.getDebtPositionByCreditPositionId(creditPositionId).borrower
+       uint256 openingLimitBorrowCR = Math.max(
+           state.riskConfig.crOpening,
+           state.data.users[account].openingLimitBorrowCR // 0 by default, or user-defined if SetUserConfiguration has been used
+       );

        return state.getLoanStatus(creditPositionId) == LoanStatus.ACTIVE
-           && !isUserUnderwater(state, state.getDebtPositionByCreditPositionId(creditPositionId).borrower);
+           && collateralRatio(state, borrower) >= openingLimitBorrowCR
    }
```

