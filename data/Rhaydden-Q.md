# Quality Assurance for Size

## Table of Contents

| Issue ID | Description |
| -------- | ----------- |
| [QA-01](#qa-01-missing-validation-for-borrowatokencap-in-validateinitializeriskconfigparams-function) | Missing Validation for `borrowATokenCap` in `validateInitializeRiskConfigParams` Function |
| [QA-02](#qa-02-unbounded-maxduedate-in-buycreditlimit-allows-setting-loan-offers-beyond-the-maximum-tenor-potentially-violating-risk-params) | Unbounded `maxDueDate` in `BuyCreditLimit` allows setting loan offers beyond the maximum tenor, potentially violating risk params |
| [QA-03](#qa-03-liquidation-with-replacement-can-leave-borrower-below-opening-limit-borrow-cr-potentially-causing-unintended-protocol-state) | Liquidation with Replacement Can Leave Borrower Below Opening Limit Borrow CR, Potentially Causing Unintended Protocol State |
| [QA-04](#qa-04-inconsistent-handling-of-negative-aprs-in-getadjustedapr-function-can-lead-to-unexpected-reverts) | Inconsistent Handling of Negative APRs in `getAdjustedAPR` Function Can Lead to Unexpected Reverts |
| [QA-05](#qa-05-fix-the-calculation-of-maxcashamountin-and-maxcredit-for-partial-purchases-in-executebuycreditmarket-function-to-account-for-other-cases) | Fix the Calculation of `maxCashAmountIn` and `maxCredit` for Partial Purchases in `executeBuyCreditMarket` Function to Account for Other Cases |
| [QA-06](#qa-06-error-handling-in-getcreditamountin-function-leading-to-misleading-not_enough_cash-revert) | Error Handling in `getCreditAmountIn` Function Leading to Misleading `NOT_ENOUGH_CASH` Revert |
| [QA-07](#qa-07-incorrect-parameter-order-in-not_enough_credit-error-message-in-getcreditamountout-function) | Incorrect Parameter Order in `NOT_ENOUGH_CREDIT` Error Message in `getCreditAmountOut` Function |
| [QA-08](#qa-08-inconsistent-token-scaling-in-withdrawal-function-may-lead-to-incorrect-token-burning) | Inconsistent Token Scaling in Withdrawal Function May Lead to Incorrect Token Burning |
| [QA-09](#qa-09-potential-silent-failure-in-getloanstatus-due-to-unverified-debt-position-existence-leading-to-incorrect-loan-status) | Potential Silent Failure in `getLoanStatus` Due to Unverified Debt Position Existence Leading to Incorrect Loan Status |
| [QA-10](#qa-10-incorrect-fee-calculation-in-getcashamountout-results-in-overcharging-users) | Incorrect fee calculation in `getCashAmountOut()` results in overcharging users |
| [QA-11](#qa-11-missing-tenor-validation-for-existing-credit-positions-in-validatebuycreditmarket-function-allows-out-of-range-tenors) | Missing Tenor Validation for Existing Credit Positions in `validateBuyCreditMarket` Function Allows Out-of-Range Tenors |
| [QA-12](#qa-12-excess-collateral-not-returned-to-borrower-in-high-collateral-liquidation-scenarios) | Excess Collateral Not Returned to Borrower in High-Collateral Liquidation Scenarios |
| [QA-13](#qa-13-chainlink-price-feed-addresses-should-not-be-immutable-to-allow-for-updates) | Chainlink price feed addresses should not be immutable to allow for updates |
| [QA-14](#qa-14-pricefeedsol-lacks-check-for-min-and-max-price-thresholds) | PriceFeed.sol Lacks Check for Min and Max Price Thresholds |
| [QA-15](#qa-15-rounding-in-nontransferrablescaledtokens-transferfrom-function-could-cause-token-loss) | Rounding in `NonTransferrableScaledToken`'s `transferFrom` function could cause token loss |
| [QA-16](#qa-16-unhandled-chainlink-latestrounddata-revert-can-lock-price-oracle-access-in-pricefeedsol) | Unhandled Chainlink `latestRoundData` revert can lock price oracle access in `PriceFeed.sol` |
| [QA-17](#qa-17-unvalidated-null-offer-storage-in-buycreditlimit-lib-allows-insertion-of-invalid-data) | Unvalidated null offer storage in `BuyCreditLimit` lib allows insertion of invalid data |
| [QA-18](#qa-18-chainlinks-latestrounddata-might-return-stale-or-incorrect-results-in-pricefeedsol) | Chainlink's `latestRoundData` might return stale or incorrect results in PriceFeed.sol |
| [QA-19](#qa-19-incomplete-collateral-ratio-check-in-validateselfliquidate-function-allows-edge-cases) | Incomplete collateral ratio check in `validateSelfLiquidate` function allows edge cases |
| [QA-20](#qa-20-fix-for-a-case-of-mismatch-between-msgvalue-and-paramsamount-in-executedeposit-function-is-required) | Fix For a Case of Mismatch Between `msg.value` and `params.amount` in `executeDeposit` Function is Required |
| [QA-21](#qa-21-lack-of-slippage-protection-in-liquidation-and-withdrawal-functions) | Lack of Slippage Protection in Liquidation and Withdrawal Functions |
| [QA-22](#qa-22-unchecked-erc-20-return-value-in-different-instances) | Unchecked ERC-20 Return Value in Different Instances |
| [QA-23](#qa-23-incorrect-liquidation-ratio-validation-enables-unsafe-protocol-states) | Incorrect Liquidation Ratio Validation Enables Unsafe Protocol States |
| [QA-24](#qa-24-incorrect-use-of-transferfrom-instead-of-transfer-in-executeclaim-function-could-lead-to-failed-claims) | Incorrect Use of `transferFrom` Instead of `transfer` in `executeClaim` Function Could Lead to Failed Claims |
| [QA-25](#qa-25-potential-logic-issue-in-executeliquidatewithreplacement-function-in-liquidatewithreplacement-library) | Potential Logic Issue in `executeLiquidateWithReplacement` Function in `LiquidateWithReplacement` Library |
| [QA-26](#qa-26-incorrect-natspec-comment) | Incorrect Natspec Comment |
| [QA-27](#qa-27-inaccurate-logic-in-getadjustedapr-function-causes-unintended-reverts) | Inaccurate Logic in `getAdjustedAPR` Function Causes Unintended Reverts |
| [QA-28](#qa-28-redundant-initialization-of-creditposition-variable-in-executebuycreditmarket-function) | Redundant Initialization of `creditPosition` Variable in `executeBuyCreditMarket` Function |
| [QA-29](#qa-29-fix-natspec) | Fix Natspec |



## [QA-01] Missing Validation for `borrowATokenCap` in `validateInitializeRiskConfigParams` Function

### Impact
This absence of validation for the `borrowATokenCap` parameter could lead to issues if this value is set to 0.

### Proof of Concept
In the `validateInitializeRiskConfigParams` function, there is no validation for the `borrowATokenCap` parameter: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Initialize.sol#L98-L128

```solidity
function validateInitializeRiskConfigParams(InitializeRiskConfigParams memory r) internal pure {
    // validate crOpening
    if (r.crOpening < PERCENT) {
        revert Errors.INVALID_COLLATERAL_RATIO(r.crOpening);
    }

    // validate crLiquidation
    if (r.crLiquidation < PERCENT) {
        revert Errors.INVALID_COLLATERAL_RATIO(r.crLiquidation);
    }
    if (r.crOpening <= r.crLiquidation) {
        revert Errors.INVALID_LIQUIDATION_COLLATERAL_RATIO(r.crOpening, r.crLiquidation);
    }

    // validate minimumCreditBorrowAToken
    if (r.minimumCreditBorrowAToken == 0) {
        revert Errors.NULL_AMOUNT();
    }

    // validate underlyingBorrowTokenCap
    // N/A

    // validate minTenor
    if (r.minTenor == 0) {
        revert Errors.NULL_AMOUNT();
    }

    if (r.maxTenor <= r.minTenor) {
        revert Errors.INVALID_MAXIMUM_TENOR(r.maxTenor);
    }
}
```

[The comment on line 117](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Initialize.sol#L117) mentions validating `underlyingBorrowTokenCap` but the actual parameter name is `borrowATokenCap`. There should be a check to ensure this value is not 0.

### Recommended Mitigation Steps
Add a validation check for the `borrowATokenCap` parameter to ensure it is not 0, similar to the checks done for the other parameters:

```solidity
// validate borrowATokenCap
if (r.borrowATokenCap == 0) {
    revert Errors.NULL_AMOUNT();
}
```







## [QA-02] Unbounded `maxDueDate` in `BuyCreditLimit` allows setting loan offers beyond the maximum tenor, potentially violating risk params

### Impact

This issue allows users to create loan offers with due dates that exceed the maximum tenor specified in the risk configuration. This could lead to loans with durations longer than intended by the protocol, potentially increasing risk exposure and violating the established risk management parameters.

### Proof of Concept

In the `validateBuyCreditLimit` function, the `maxDueDate` is only checked against the minimum tenor. Look at this: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditLimit.sol#L42-L43

```solidity
if (params.maxDueDate < block.timestamp + state.riskConfig.minTenor) {
    revert Errors.PAST_MAX_DUE_DATE(params.maxDueDate);
}
```

However, there is no check to ensure that `maxDueDate` does not exceed the maximum tenor specified in `state.riskConfig.maxTenor`.

### Recommended Mitigation Steps

Add an additional check in the `validateBuyCreditLimit` function to ensure that the `maxDueDate` does not exceed the maximum allowed tenor. This can be done by adding the following code after the existing `maxDueDate` check:

```solidity
if (params.maxDueDate > block.timestamp + state.riskConfig.maxTenor) {
    revert Errors.MAX_DUE_DATE_EXCEEDS_MAX_TENOR(params.maxDueDate);
}
```

Also, define a new error in the `Errors` library to handle this specific case:

```solidity
error MAX_DUE_DATE_EXCEEDS_MAX_TENOR(uint256 maxDueDate);
```










## [QA-03] Liquidation with Replacement Can Leave Borrower Below Opening Limit Borrow CR, Potentially Causing Unintended Protocol State

### Impact

This can lead to borrowers being left in a vulnerable financial position after a liquidation with replacement, contrary to the intended protocol safeguards. Specifically:

1. It may allow liquidations that leave borrowers in a worse position than the protocol intends, potentially increasing their risk of further liquidations or insolvency.
2. It could create inconsistent protocol states where a borrower's position is technically invalid according to the protocol's rules, but the liquidation has already been executed and cannot be easily reversed.
3. If the post-liquidation check fails, it's unclear how the protocol would handle this situation, potentially leading to locked funds or failed transactions.
4. It undermines the effectiveness of the opening limit borrow CR as a risk management tool, as positions can end up below this threshold through liquidations.
5. It may lead to unexpected behavior in other parts of the protocol that assume all borrower positions meet the minimum CR requirements.

### Proof of Concept

Look at this part of the `liquidateWithReplacement` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L229-L245

```solidity
function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
    onlyRole(KEEPER_ROLE)
    returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
{
    state.validateLiquidateWithReplacement(params);
    uint256 amount;
    (amount, liquidatorProfitCollateralToken, liquidatorProfitBorrowToken) =
        state.executeLiquidateWithReplacement(params);
    state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
    state.validateMinimumCollateralProfit(params, liquidatorProfitCollateralToken);
    state.validateVariablePoolHasEnoughLiquidity(amount);
}
```

The issue is that `state.executeLoadniquidateWithReplacement(params)` is called before `state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower)`. This means the liquidation is executed before checking if it leaves the borrower in an acceptable position.

### Recommended Mitigation Steps

I'm proposing the following:

1. Create a new function that simulates the effects of the liquidation with replacement without actually executing it. This function should calculate the borrower's resulting position after the hypothetical liquidation.

2. Use this simulation function to check if the liquidation would leave the borrower above the opening limit borrow CR before executing the actual liquidation.

3. Only proceed with the liquidation if the simulated result passes all necessary checks, including the CR validation.

4. If possible, consider making the `executeLiquidateWithReplacement` function atomic, so that if any post-execution checks fail, the entire transaction can be reverted safely.












## [QA-04] Inconsistent Handling of Negative APRs in `getAdjustedAPR` Function Can Lead to Unexpected Reverts

### Impact
This issue can cause unexpected reverts when dealing with negative APRs, potentially disrupting the normal operation of the yield curve calculations. It may hinder the protocol from accurately representing market conditions where negative interest rates are possible, leading to reduced functionality or incorrect pricing as the case may be.

### Proof of Concept

Take a look at the `getAdjustedAPR` function of the `YieldCurveLibrary`: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/YieldCurveLibrary.sol#L87-L107

```solidity
function getAdjustedAPR(int256 apr, uint256 marketRateMultiplier, VariablePoolBorrowRateParams memory params)
    internal
    view
    returns (uint256)
{
    if (marketRateMultiplier == 0) {
        return SafeCast.toUint256(apr);
    } else if (
        // ... (other conditions)
    ) {
        // ... (error handling)
    } else {
        return SafeCast.toUint256(
            apr + SafeCast.toInt256(Math.mulDivDown(params.variablePoolBorrowRate, marketRateMultiplier, PERCENT))
        );
    }
}
```

[The NatSpec comment states:](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/YieldCurveLibrary.sol#L81) 

```solidity
/// @dev Reverts if the final result is negative
```

Albeit, the function will actually revert if the input `apr` is negative, not just if the final result is negative. This is because `SafeCast.toUint256(apr)` is called directly on the input `apr` when `marketRateMultiplier` is zero, and the same conversion is applied to the final result in the other case.

This is inconsistent with the documented expectation and may lead to unexpected reverts in scenarios where negative APRs are possible or expected.

### Recommended Mitigation Steps

Modify the function to handle negative APRs correctly, potentially by returning a signed integer (int256) instead of uint256. This would require changes in other parts of the code that interact with this function Or if negative APRs should not be allowed in the system, update the NatSpec comment to accurately reflect that the function reverts on negative input APRs, not just negative final results.















## [QA-05]  Fix the Calculation of `maxCashAmountIn` and `maxCredit` for Partial Purchases in `executeBuyCreditMarket` Function to Account for Other Cases

### Impact
Navigating to the current implementation of the `executeBuyCreditMarket` function, we can see that it does not correctly handle partial purchases of existing credit positions. This can lead to overpayment issues.

### Proof of Concept


Look at this part of the `executeBuyCreditMarket` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L160-L165

```solidity
maxCashAmountIn: params.creditPositionId == RESERVED_ID
    ? cashAmountIn
    : Math.mulDivUp(creditPosition.credit, PERCENT, PERCENT + ratePerTenor),
maxCredit: params.creditPositionId == RESERVED_ID
    ? Math.mulDivDown(cashAmountIn, PERCENT + ratePerTenor, PERCENT)
    : creditPosition.credit,
```
When `params.creditPositionId` is not `RESERVED_ID` (i.e., when buying an existing credit position), the function calculates `maxCashAmountIn` and `maxCredit` based on the full `creditPosition.credit`. This assumes the entire credit position is being purchased, which might not be the case if the user wants to buy only a portion of the credit position.

Consider a user who wants to buy only a portion of an existing credit position. The current logic will:

1. Calculate `maxCashAmountIn` based on the full `creditPosition.credit`, leading to overpayment.
2. Set `maxCredit` to the full `creditPosition.credit`, resulting in transferring more credit than requested.


### Recommended Mitigation Steps

When calculating `maxCashAmountIn` and `maxCredit` for existing credit positions, `params.amount` should be taken into account. Something like this(just an example):

```diff
if (params.exactAmountIn) {
    cashAmountIn = params.amount;
    (creditAmountOut, fees) = state.getCreditAmountOut({
        cashAmountIn: cashAmountIn,
        maxCashAmountIn: params.creditPositionId == RESERVED_ID
            ? cashAmountIn
-           : Math.mulDivUp(creditPosition.credit, PERCENT, PERCENT + ratePerTenor),
+           : Math.mulDivUp(params.amount, PERCENT, PERCENT + ratePerTenor),
        maxCredit: params.creditPositionId == RESERVED_ID
-           ? Math.mulDivDown(cashAmountIn, PERCENT + ratePerTenor, PERCENT)
-           : creditPosition.credit,
+           ? Math.mulDivDown(cashAmountIn, PERCENT + ratePerTenor, PERCENT)
+           : params.amount,
        ratePerTenor: ratePerTenor,
        tenor: tenor
    });
} else {
    creditAmountOut = params.amount;
    (cashAmountIn, fees) = state.getCashAmountIn({
        creditAmountOut: creditAmountOut,
-       maxCredit: params.creditPositionId == RESERVED_ID ? creditAmountOut : creditPosition.credit,
+       maxCredit: params.creditPositionId == RESERVED_ID ? creditAmountOut : params.amount,
        ratePerTenor: ratePerTenor,
        tenor: tenor
    });
}
```













## [QA-06] Error Handling in `getCreditAmountIn` Function Leading to Misleading `NOT_ENOUGH_CASH` Revert

### Impact
If we look at the current implementation of the `getCreditAmountIn` function in the `AccountingLibrary`, it reverts with a misleading `Errors.NOT_ENOUGH_CASH` error when `maxCashAmountOutFragmentation < cashAmountOut < maxCashAmountOut`. This can cause confusion and will make it harder to understand the real issue when this revert occurs.

### Proof of Concept
Take a look at this part of the `getCreditAmountIn` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L243-L262

```solidity
// slither-disable-next-line incorrect-equality
if (cashAmountOut == maxCashAmountOut) {
    // no credit fractionalization

    creditAmountIn = maxCredit;
    fees = Math.mulDivUp(cashAmountOut, swapFeePercent, PERCENT);
} else if (cashAmountOut < maxCashAmountOutFragmentation) {
    // credit fractionalization

    creditAmountIn = Math.mulDivUp(
        cashAmountOut + state.feeConfig.fragmentationFee, PERCENT + ratePerTenor, PERCENT - swapFeePercent
    );
    fees = Math.mulDivUp(cashAmountOut, swapFeePercent, PERCENT) + state.feeConfig.fragmentationFee;
} else {
    // for maxCashAmountOutFragmentation < amountOut < maxCashAmountOut we are in an inconsistent situation
    //   where charging the swap fee would require to sell a credit that exceeds the max possible credit

    revert Errors.NOT_ENOUGH_CASH(maxCashAmountOutFragmentation, cashAmountOut);
}
```

The issue is in the `else` block, where the function reverts with `Errors.NOT_ENOUGH_CASH` when `maxCashAmountOutFragmentation < cashAmountOut < maxCashAmountOut`. This condition does not necessarily mean there isn't enough cash. It's just an edge case where charging the swap fee would require selling more credit than the maximum possible credit.

### Recommended Mitigation Steps
Revert with a more specific error message indicating that the requested `cashAmountOut` is not possible due to the swap fee exceeding the maximum credit. For example, define a new error `SWAP_FEE_EXCEEDS_MAX_CREDIT` in the `Errors.sol` file and use it in the `else` block.










## [QA-07] Incorrect Parameter Order in `NOT_ENOUGH_CREDIT` Error Message in `getCreditAmountOut` Function

### Impact
The error message for `NOT_ENOUGH_CREDIT` in the `getCreditAmountOut` function provides misleading information by swapping the actual and required values. This will cause confusion when debugging or handling errors, as the error message will not accurately reflect the situation.

### Proof of Concept
In the [`getCreditAmountOut`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L299) function of the `AccountingLibrary.sol` contract, the `else` block for handling the error case is implemented this way: 
```solidity
else {
    revert Errors.NOT_ENOUGH_CREDIT(maxCashAmountIn, cashAmountIn);
}
```

However, the `NOT_ENOUGH_CREDIT` error is defined in the [`Errors.sol`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Errors.sol#L42) contract as:

```solidity
error NOT_ENOUGH_CREDIT(uint256 credit, uint256 required);
```

This definition expects the actual value (`credit`) as the first argument and the required value (`required`) as the second argument. The current implementation incorrectly swaps these values.


### Recommended Mitigation Steps
Update the `else` block in the `getCreditAmountOut` function to correctly order the arguments for the `NOT_ENOUGH_CREDIT` error. It should be:

```solidity
else {
    revert Errors.NOT_ENOUGH_CREDIT(cashAmountIn, maxCashAmountIn);
}
```













## [QA-08] Inconsistent Token Scaling in Withdrawal Function May Lead to Incorrect Token Burning

### Impact

This issue could result in users having an incorrect amount of tokens burned when withdrawing from the variable pool. Depending on the current exchange rate between scaled and non-scaled amounts, users might have too many tokens burned (losing value) or too few tokens burned (gaining undue value). This discrepancy could lead to accounting errors in the protocol and potential financial losses for users or the protocol itself.

>In Aave-like protocols, the `aToken` balance is often represented in scaled form to account for interest accrual. When withdrawing, you typically need to convert between scaled and non-scaled amounts to ensure consistency.

### Proof of Concept

Take a look at the `withdrawUnderlyingTokenFromVariablePool` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/DepositTokenLibrary.sol#L74-L90

```solidity
function withdrawUnderlyingTokenFromVariablePool(State storage state, address from, address to, uint256 amount)
    external
{
    IAToken aToken =
        IAToken(state.data.variablePool.getReserveData(address(state.data.underlyingBorrowToken)).aTokenAddress);

    uint256 scaledBalanceBefore = aToken.scaledBalanceOf(address(this));

    // slither-disable-next-line unused-return
    state.data.variablePool.withdraw(address(state.data.underlyingBorrowToken), amount, to);

    uint256 scaledAmount = scaledBalanceBefore - aToken.scaledBalanceOf(address(this));

    state.data.borrowAToken.burnScaled(from, scaledAmount);
}
```

The function accepts a non-scaled `amount` as input and uses this to withdraw tokens from the variable pool. However, it then calculates a `scaledAmount` based on the difference in scaled balances before and after the withdrawal. This `scaledAmount` is then used to burn tokens from the user's balance.

The issue is that this function withdraws a non-scaled `amount` of tokens from the variable pool, but then calculates and burns a `scaledAmount` of tokens from the user's balance. This mismatch between scaled and non-scaled amounts could lead to incorrect token burning.

### Recommended Mitigation Steps

The function should ensure consistency between the withdrawn amount and the burned amount. One way would be to convert the input amount to a scaled amount before withdrawal, ensuring that the same scaled amount is used for both withdrawal and burning.













## [QA-09] Potential Silent Failure in `getLoanStatus` Due to Unverified Debt Position Existence Leading to Incorrect Loan Status

### Impact

The `getLoanStatus` function has an issue that could lead to silent failures and incorrect loan status determinations. This arises from the function's assumption that a `DebtPosition` exists without proper verification. As a result, the function may return inaccurate loan statuses for non-existent debt positions.

### Proof of Concept

The issue lies in the `getLoanStatus` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/LoanLibrary.sol#L122-L140

```solidity
function getLoanStatus(State storage state, uint256 positionId) public view returns (LoanStatus) {
    // First, assumes `positionId` is a debt position id
    DebtPosition memory debtPosition = state.data.debtPositions[positionId];
    if (isCreditPositionId(state, positionId)) {
        // if `positionId` is in reality a credit position id, updates the memory variable
        debtPosition = getDebtPositionByCreditPositionId(state, positionId);
    } else if (!isDebtPositionId(state, positionId)) {
        // if `positionId` is neither a debt position id nor a credit position id, reverts
        revert Errors.INVALID_POSITION_ID(positionId);
    }

    if (debtPosition.futureValue == 0) {
        return LoanStatus.REPAID;
    } else if (block.timestamp > debtPosition.dueDate) {
        return LoanStatus.OVERDUE;
    } else {
        return LoanStatus.ACTIVE;
    }
}
```

The function immediately attempts to retrieve a `DebtPosition` without verifying its existence:
   ```solidity
   DebtPosition memory debtPosition = state.data.debtPositions[positionId];
   ```
   If `positionId` is a valid debt position ID but the position doesn't actually exist, this will create an empty `DebtPosition` struct in memory. Also, the function doesn't verify if the retrieved `DebtPosition` actually exists before using its properties to determine the loan status. If a non-existent `DebtPosition` is accessed, the function will not revert but instead continue execution with default values (0 for `futureValue` and `dueDate`), leading to incorrect loan status determination.

Let's consider these scenarios:

1. **Scenario 1: Non-existent Debt Position**
A `positionId` that passes the `isDebtPositionId` check but doesn't actually have a corresponding `DebtPosition` in `state.data.debtPositions`. The function will return `LoanStatus.REPAID` because `debtPosition.futureValue` will be 0 (default value).

2. **Scenario 2: Deleted Debt Position**
A `positionId` that once had a valid `DebtPosition`, but the position was deleted or invalidated without updating the `nextDebtPositionId`. The function might return an incorrect status based on residual or default values.


### Recommended Mitigation Steps
Explicitly checks for the existence of the `DebtPosition` before accessing its properties.












## [QA-10] Incorrect fee calculation in `getCashAmountOut()` results in overcharging users

### Impact

The `getCashAmountOut()` function calculates fees based on the maximum cash amount before subtracting those fees, resulting in users paying fees on money they don't actually receive. This leads to unfair fee calculations and potential loss of funds for users.

### Proof of Concept

Take a look at the `getCashAmountOut()` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L274-L300

```solidity
function getCashAmountOut(
    State storage state,
    uint256 creditAmountIn,
    uint256 maxCredit,
    uint256 ratePerTenor,
    uint256 tenor
) internal view returns (uint256 cashAmountOut, uint256 fees) {
    uint256 maxCashAmountOut = Math.mulDivDown(creditAmountIn, PERCENT, PERCENT + ratePerTenor);

    if (creditAmountIn == maxCredit) {
        // no credit fractionalization

        fees = getSwapFee(state, maxCashAmountOut, tenor);

        if (fees > maxCashAmountOut) {
            revert Errors.NOT_ENOUGH_CASH(maxCashAmountOut, fees);
        }

        cashAmountOut = maxCashAmountOut - fees;
    }
    // ... (rest of the function)
}
```

The function calculates `fees` based on `maxCashAmountOut`, but then subtracts these fees from `maxCashAmountOut` to get the final `cashAmountOut`. This means users are paying fees on the pre-fee amount rather than the actual amount they receive.

Lets consider a scenario. Suppose `maxCashAmountOut` is 1000 tokens and the swap fee is 5%.

In the current implementation, the contract calculates fees based on the maximum cash amount before subtracting those fees.

1. maxCashAmountOut = 1000 tokens
2. Swap fee = 5%
3. Fees = 5% of 1000 = 50 tokens
4. cashAmountOut = 1000 - 50 = 950 tokens

The user receives 950 tokens but pays 50 tokens in fees. To calculate the effective fee rate:
Effective fee rate = (Fees / cashAmountOut) * 100
= (50 / 950) * 100 ≈ 5.26%

This means the user is actually paying a 5.26% fee rate, which is higher than the intended 5%.

The fee should be calculated on the actual amount the user receives, ensuring they pay exactly 5% in fees.

To achieve this, let's solve the equation:
cashAmountOut + 5% of cashAmountOut = 1000

Let x be the cashAmountOut:
x + 0.05x = 1000
1.05x = 1000
x = 1000 / 1.05 ≈ 952.38

So:
1. cashAmountOut ≈ 952.38 tokens
2. Fees = 5% of 952.38 ≈ 47.62 tokens

Verifying:
952.38 + 47.62 = 1000 (which equals our original maxCashAmountOut)

Effective fee rate = (47.62 / 952.38) * 100 = 5%

This approach ensures that the user pays exactly 5% in fees on the amount they receive.

The Difference:
In the current implementation, the user receives 950 tokens and pays 50 in fees.
In the correct implementation, the user receives 952.38 tokens and pays 47.62 in fees.

While this difference might seem small in this example (2.38 tokens), it becomes more significant with larger amounts or higher fee percentages. For instance:

- With a tx of 1,000,000 tokens and a 5% fee, the difference would be 2,380 tokens.
- With a 10% fee on 1000 tokens, the current implementation would charge 100 tokens (resulting in a 11.11% effective rate), while the correct implementation would charge about 90.91 tokens.

This could lead to substantial overcharging in high-volume scenarios or with larger fee percentages resulting in significant losses for users over time.


### Recommended Mitigation Steps

Modify the `getCashAmountOut()` function to calculate fees based on the actual `cashAmountOut`























## [QA-11] Missing Tenor Validation for Existing Credit Positions in `validateBuyCreditMarket` Function Allows Out-of-Range Tenors

### Impact
The lack of tenor validation for existing credit positions (non-RESERVED_ID) could lead to situations where the tenor falls outside the allowed range. This might cause issues in other parts of the protocol that assume the tenor is always within the specified bounds.

### Proof of Concept
In the `validateBuyCreditMarket` function, the tenor is only validated when `params.creditPositionId == RESERVED_ID`. Take a look at this function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L60-L62

```solidity
// validate tenor
if (tenor < state.riskConfig.minTenor || tenor > state.riskConfig.maxTenor) {
    revert Errors.TENOR_OUT_OF_RANGE(tenor, state.riskConfig.minTenor, state.riskConfig.maxTenor);
}
```

However, when a non-RESERVED_ID is provided, the tenor is calculated but not validated:
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L80

```solidity
tenor = debtPosition.dueDate - block.timestamp;
```

There's no check to ensure this calculated tenor falls within the allowed range (between `minTenor` and `maxTenor`). This could potentially lead to situations where the tenor for existing credit positions falls outside the allowed range.

### Recommended Mitigation Steps
Add a tenor validation check for non-RESERVED_ID cases to ensure the tenor is always within the allowed range. Something like this:

```solidity
if (params.creditPositionId != RESERVED_ID) {
    tenor = debtPosition.dueDate - block.timestamp;
    if (tenor < state.riskConfig.minTenor || tenor > state.riskConfig.maxTenor) {
        revert Errors.TENOR_OUT_OF_RANGE(tenor, state.riskConfig.minTenor, state.riskConfig.maxTenor);
    }
}
```








## [QA-12] Excess Collateral Not Returned to Borrower in High-Collateral Liquidation Scenarios

### Impact
`executeLiquidate` function fails to properly handle excess collateral when the `collateralRemainder` exceeds the `collateralRemainderCap`. This results in the borrower not receiving the correct amount of collateral after the liquidation process, leading to loss for the borrower.

### Proof of Concept
Look at this part of the code: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Liquidate.sol#L104-L112

```solidity
// cap the collateral remainder to the liquidation collateral ratio
//   otherwise, the split for non-underwater overdue loans could be too much
uint256 collateralRemainderCap =
    Math.mulDivDown(debtInCollateralToken, state.riskConfig.crLiquidation, PERCENT);

collateralRemainder = Math.min(collateralRemainder, collateralRemainderCap);

protocolProfitCollateralToken = Math.mulDivDown(collateralRemainder, collateralProtocolPercent, PERCENT);
```

`collateralRemainderCap` is calculated based on the `debtInCollateralToken` and the `crLiquidation` (liquidation collateral ratio). This cap is then applied to the `collateralRemainder`, which is the difference between the `assignedCollateral` and the `liquidatorProfitCollateralToken`.

When the `collateralRemainder` is greater than the `collateralRemainderCap`, the `collateralRemainder` is capped to the `collateralRemainderCap`, and the excess collateral is not accounted for. Actually, this excess collateral should be returned to the borrower, but it is not handled in the current implementation.

Consider a scenario where:
- `assignedCollateral` = 1500
- `debtInCollateralToken` = 1000
- `liquidatorProfitCollateralToken` = 1100
- `crLiquidation` = 120%

The `collateralRemainder` would be 400 (1500 - 1100). The `collateralRemainderCap` would be 1200 (1000 * 120%). Since the `collateralRemainder` (400) is less than the `collateralRemainderCap` (1200), no excess collateral is calculated or returned to the borrower.

### Recommended Mitigation Steps
The excess collateral should be calculated and transferred back to the borrower.

```diff
        uint256 collateralRemainder = assignedCollateral - liquidatorProfitCollateralToken;
        uint256 collateralRemainderCap = Math.mulDivDown(debtInCollateralToken, state.riskConfig.crLiquidation, PERCENT);
        collateralRemainder = Math.min(collateralRemainder, collateralRemainderCap);

+       uint256 excessCollateral = collateralRemainder > collateralRemainderCap ? collateralRemainder - collateralRemainderCap : 0;
+       if (excessCollateral > 0) {
+           state.data.collateralToken.transfer(debtPosition.borrower, excessCollateral);
+       }

        protocolProfitCollateralToken = Math.mulDivDown(collateralRemainder, collateralProtocolPercent, PERCENT);
    } else {
        liquidatorProfitCollateralToken = assignedCollateral;
    }
```











## [QA-13] Chainlink price feed addresses should not be immutable to allow for updates

### Impact
The `PriceFeed.sol` contract sets the Chainlink `base` and `quote` price feed addresses as `immutable` in the constructor. This means that if the Chainlink price feeds are updated, become buggy, or are taken down, the contract will break and require redeployment. This can disrupt the protocol's functionality and incur significant costs thereby forcing redeployment.

### Proof of Concept
In `PriceFeed.sol`, the `base` and `quote` price feed addresses are set as `immutable` in the constructor, meaning they cannot be changed after deployment:
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L36-L53


```solidity
contract PriceFeed is IPriceFeed {
    AggregatorV3Interface public immutable base;
    AggregatorV3Interface public immutable quote;

    constructor(
        address _base,
        address _quote,
        address _sequencerUptimeFeed,
        uint256 _baseStalePriceInterval,
        uint256 _quoteStalePriceInterval
    ) {
        if (_base == address(0) || _quote == address(0)) {
            revert Errors.NULL_ADDRESS();
        }
        base = AggregatorV3Interface(_base);
        quote = AggregatorV3Interface(_quote);
    }
}
```


However, chainlink price feeds are not immutable as they can change, either by being taken down or being updated to a new address which is one of the reasons [Chainlink advertises](https://blog.chain.link/introducing-the-chainlink-feed-registry/)) the use of their registry contract.
When this occurs or if the price feed gets buggy, changing the pricefeed will be impossible, breaking the protocol's functionality and will force redeployment of the contract which doesn't come cheap.
The current implementation does not account for the possibility that Chainlink price feeds can change, either by being taken down or updated to a new address. If this occurs, the contract will break, forcing a costly redeployment.

### Recommended Mitigation Steps
Consider making the `base` and `quote` price feed addresses mutable and introducing admin-protected functions to update these addresses. This will allow the contract to adapt to changes in the Chainlink price feeds without requiring redeployment.











## [QA-14] PriceFeed.sol Lacks Check for Min and Max Price Thresholds

### Impact
Incorrect prices could be used which allows users to perform actions at an advantage. The `PriceFeed` contract relies on Chainlink aggregators to fetch the latest price data for the base and quote assets. However, Chainlink aggregators have a built-in circuit breaker mechanism that kicks in if the price of an asset goes outside a predetermined price band (defined by `minPrice` and `maxPrice`). In such cases, the aggregator will continue to return the `minPrice` or `maxPrice` instead of the actual price.

This can lead to issues if the asset experiences a significant price drop or spike. For example, during the LUNA crash, the price oracle continued to return the minPrice, allowing users to borrow the asset at an incorrect price. This is exactly what happened to [Venus on BSC when LUNA imploded](https://rekt.news/venus-blizz-rekt/). Albeit, the protocol misses to implement such a check.

The `PriceFeed` contract currently lacks a mechanism to validate the price returned by the aggregator against predefined `min` and `max` thresholds. This omission exposes the protocol to the risk of using and propagating incorrect prices in the event of extreme market conditions triggering the circuit breaker.

Refer to similar issues reported here:
- BakerFi audit: https://github.com/code-423n4/2024-05-bakerfi-findings/issues/16
- USSD audit: https://github.com/sherlock-audit/2023-05-USSD-judging/issues/598

### Proof of Concept
In the `_getPrice` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84-L94

```solidity
            function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
                // slither-disable-next-line unused-return
---->      (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
            
                if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
                if (block.timestamp - updatedAt > stalePriceInterval) {
                    revert Errors.STALE_PRICE(address(aggregator), updatedAt);
                }
            
                return SafeCast.toUint256(price);
}
```

The contract checks for non-positive prices and stale prices based on the `updatedAt` timestamp. However, it does not validate the price against `min` and `max` thresholds.

### Recommended Mitigation Steps:
Introduce a check in the `_getPrice` function to ensure the price is within an acceptable range defined by `minPrice` and `maxPrice`. This can be done by adding a require statement:

```solidity
require(price >= minPrice && price <= maxPrice, "Price outside of allowed range");
```

`min` and `max` prices can be gotten using [any of these ways](https://medium.com/cyfrin/chainlink-oracle-defi-attacks-93b6cb6541bf#99af:~:text=Developers%20%26%20Auditors%20can%20find%20Chainlink%E2%80%99s%20oracle%20feed).













## [QA-15] Rounding in `NonTransferrableScaledToken`'s `transferFrom` function could cause token loss

### Impact
The `transferFrom` function in the `NonTransferrableScaledToken` contract may report successful transfers for very small amounts without actually moving any tokens. This could lead to accounting discrepancies, misleading transaction histories, and potential loss of user funds.

### Proof of Concept
In the [`NonTransferrableScaledToken`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/token/NonTransferrableScaledToken.sol#L76-L85) contract:

```solidity
function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {
    uint256 scaledAmount = Math.mulDivDown(value, WadRayMath.RAY, liquidityIndex());

    _burn(from, scaledAmount);
    _mint(to, scaledAmount);

    emit TransferUnscaled(from, to, value);

    return true;
}
```

This function uses [`Math.mulDivDown`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Math.sol#L27-L28) from the `Math` library:

```solidity
function mulDivDown(uint256 x, uint256 y, uint256 z) internal pure returns (uint256) {
    return FixedPointMathLib.mulDiv(x, y, z);
}
```

The issue arises when `value` is very small compared to the `liquidityIndex`. In such cases, `scaledAmount` could be rounded down to 0. The function would still execute, emitting a `TransferUnscaled` event and returning `true`, despite no tokens actually being transferred.

Scenario:
1. Alice has 1 token (represented as 1e18 in the contract).
2. The `liquidityIndex` is very high, let's say 1e27.
3. Alice tries to transfer 0.1 tokens (1e17) to Bob.
4. The `scaledAmount` calculation: 1e17 * 1e27 / 1e27 = 0 (due to rounding down)
5. No tokens are actually transferred, but the contract reports a successful transfer.
6. This process could be repeated, gradually draining Alice's account without actually transferring tokens.

### Recommended Mitigation Steps
Add a check to ensure that `scaledAmount` is greater than 0 before proceeding with the transfer:

```diff
function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {
    uint256 scaledAmount = Math.mulDivDown(value, WadRayMath.RAY, liquidityIndex());

+   if (scaledAmount == 0) {
+       revert Errors.AMOUNT_TOO_SMALL();
+   }

    _burn(from, scaledAmount);
    _mint(to, scaledAmount);

    emit TransferUnscaled(from, to, value);

    return true;
}
```











## [QA-16] Unhandled Chainlink `latestRoundData` revert can lock price oracle access in `PriceFeed.sol`

### Impact
If the Chainlink multisig blocks access (which they can at will) to the price feed, causing the `latestRoundData()` function to revert, the `PriceFeed` contract will also revert. Therefore, to prevent DOS scenarios, it is recommended to query Chainlink price feeds using a defensive approach with Solidity’s try/catch structure. In this way, if the call to the price feed fails, the caller contract is still in control and can handle any errors safely and explicitly.

Refer to https://blog.openzeppelin.com/secure-smart-contract-guidelines-the-dangers-of-price-oracles/ for more information regarding potential risks to account for when relying on external price feed providers.

Also refer to this past issue for more insight:
- Obront's research: https://github.com/sherlock-audit/2023-02-blueberry-judging/issues/161

### Proof of Concept
The `_getPrice` function in `PriceFeed.sol` calls the `latestRoundData()` function of the Chainlink price feed aggregator without using a `try/catch` block to handle potential reverts: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84-L94

```solidity
                  function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
                      // slither-disable-next-line unused-return
 ----->          (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
                  
                      if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
                      if (block.timestamp - updatedAt > stalePriceInterval) {
                          revert Errors.STALE_PRICE(address(aggregator), updatedAt);
                      }
                  
                      return SafeCast.toUint256(price);
                  }
```

If the Chainlink multisig blocks access to the price feed, causing `latestRoundData()` to revert, the `_getPrice` function will also revert. This revert will propagate up to the `getPrice` function, which is the external function used by other contracts to retrieve the asset price.

### Recommended Mitigation Steps
Wrap the `latestRoundData()` call in a `try/catch` block instead of calling it directly and provide a graceful alternative/exit.. This way, if the call reverts, the `PriceFeed` contract can gracefully handle the error and provide a fallback value/alternative logic. 











## [QA-17] Unvalidated null offer storage in `BuyCreditLimit` lib allows insertion of invalid data

### Impact
The current implementation of the `BuyCreditLimit` library could allow the storage of invalid or malicious data within the contract's state. This issue affects the `Size.sol` contract (See Proof of Concept below)

### Proof of Concept

The issue stems from two key areas in the `BuyCreditLimit` library. 

In the [`validateBuyCreditLimit`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditLimit.sol#L29-L51) function, the contract checks if the loan offer is null (indicating an intent to clear the limit order). If the offer is null, no further validation is performed on the input parameters.

```solidity
function validateBuyCreditLimit(State storage state, BuyCreditLimitParams calldata params) external view {
    LoanOffer memory loanOffer =
        LoanOffer({maxDueDate: params.maxDueDate, curveRelativeTime: params.curveRelativeTime});

    // a null offer means clearing their limit order
    if (!loanOffer.isNull()) {
        // validate msg.sender
        // N/A

        // validate maxDueDate
        if (params.maxDueDate == 0) {
            revert Errors.NULL_MAX_DUE_DATE();
        }
        if (params.maxDueDate < block.timestamp + state.riskConfig.minTenor) {
            revert Errors.PAST_MAX_DUE_DATE(params.maxDueDate);
        }

        // validate curveRelativeTime
        YieldCurveLibrary.validateYieldCurve(
            params.curveRelativeTime, state.riskConfig.minTenor, state.riskConfig.maxTenor
        );
    }
}
```

Then the [`executeBuyCreditLimit`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditLimit.sol#L57-L65) function then proceeds to store these potentially unvalidated parameters directly into the contract's state.


```solidity
function executeBuyCreditLimit(State storage state, BuyCreditLimitParams calldata params) external {
    state.data.users[msg.sender].loanOffer =
        LoanOffer({maxDueDate: params.maxDueDate, curveRelativeTime: params.curveRelativeTime});
    emit Events.BuyCreditLimit(
        params.maxDueDate,
        params.curveRelativeTime.tenors,
        params.curveRelativeTime.aprs,
        params.curveRelativeTime.marketRateMultipliers
    );
}
```

Now, navigating to Size.sol, If a user calls the [`buyCreditLimit` function](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L166-L168) in `Size.sol` with a null offer, the parameters (`maxDueDate` and `curveRelativeTime`) are not validated. This could lead to the storage of invalid or malicious data in the `state.data.users[msg.sender].loanOffer` field.



### Recommended Mitigation Steps
Implement comprehensive input validation for all parameters in the `validateBuyCreditLimit` function, regardless of whether the offer is null or not. This ensures that only valid data can ever be passed to the `executeBuyCreditLimit` function. Also, consider adding extra validation checks in the `executeBuyCreditLimit` function to further safeguard against invalid data storage.












## [QA-18] Chainlink's `latestRoundData` might return stale or incorrect results in PriceFeed.sol

### Impact
In the `PriceFeed` contract, the protocol uses a Chainlink aggregator to fetch the latest price data via the `latestRoundData()` function. However, there is a potential issue where the return value might indicate stale or incorrect data. The current implementation checks if the price is greater than 0, but this alone is not sufficient to ensure the data's validity.


This discrepancy could have the protocol produce incorrect values for very important functions in different places across the system, such as [debtTokenAmountToCollateralTokenAmount](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L33), [collateralRatio](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L57), [validateInitializeOracleParams](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Initialize.sol#L138), etc


This issue could lead to stale prices according to the Chainlink documentation: 
- https://docs.chain.link/docs/historical-price-data/#historical-rounds
- https://docs.chain.link/docs/faq/#how-can-i-check-if-the-answer-to-a-round-is-being-carried-over-from-a-previous-round



>[Also in the Public known issues in the ReadMe,](https://github.com/code-423n4/2024-06-size#publicly-known-issues), it makes mentions of:
>
>- Price feeds must be redeployed and updated in case any Chainlink configuration changes (stale price timeouts, decimals, etc)


This issue in the Readme is about the need to redeploy and update price feeds in case of Chainlink configuration changes, which is unrelated to the timing mismatch in price updates as this report points out.


### Proof of Concept
The `getPrice()` function in the `PriceFeed` contract calls `_getPrice()` separately for both the base and quote assets. [Look at this part of the code:](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L79-L80)

```solidity
return Math.mulDivDown(
    _getPrice(base, baseStalePriceInterval), 
    10 ** decimals, 
    _getPrice(quote, quoteStalePriceInterval)
);
```

Each call to `_getPrice()` [fetches the latest round data independently](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L86): 

```solidity
(, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
```

Now there're several issues with this approach:
- **Independent Fetching**: The prices for the base and quote assets are fetched independently, which means they may not be from the same timestamp. This can lead to inaccuracies, especially during periods of high volatility.
- **Insufficient Checks**: The only check present is to ensure the price is greater than 0:

   ```solidity
   if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
   ```
   This does not account for the possibility of stale data.

And this can lead to: 
- **Inaccurate Pricing**: The timing mismatch between the base and quote price updates can result in a price that does not accurately reflect the true ratio between the assets at any single point in time.
- **Mispriced Operations**: This can lead to mispriced operations within the broader system, potentially causing incorrect liquidations, trades, or other financial operations.



### Recommended Mitigation Steps:
Consider adding additional checks for stale data. 
```diff
function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
-    // slither-disable-next-line unused-return
-    (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
+    (uint80 roundID, int256 price,, uint256 updatedAt, uint80 answeredInRound) = aggregator.latestRoundData();

     if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
     if (block.timestamp - updatedAt > stalePriceInterval) {
         revert Errors.STALE_PRICE(address(aggregator), updatedAt);
     }
+    if (answeredInRound < roundID) revert Errors.STALE_ROUND(address(aggregator), answeredInRound, roundID);
+    if (updatedAt == 0) revert Errors.INCOMPLETE_ROUND(address(aggregator));

     return SafeCast.toUint256(price);
 }
```








## [QA-19] Incomplete collateral ratio check in `validateSelfLiquidate` function allows edge cases

### Impact
The current implementation of the `validateSelfLiquidate` function does not handle edge cases where the collateral ratio is 0 or negative. This could potentially allow invalid self-liquidation attempts.

### Proof of Concept
The `validateSelfLiquidate` function currently checks if the collateral ratio is less than 100% (`PERCENT`), but it does not explicitly handle edge cases like 0 or negative values.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SelfLiquidate.sol#L46

```solidity

        // Check if collateral ratio is less than 100%
@>        if (collateralRatio >= PERCENT) {
            revert Errors.LIQUIDATION_NOT_AT_LOSS(params.creditPositionId, collateralRatio);
        }
    
        // validate msg.sender
        if (msg.sender != creditPosition.lender) {
            revert Errors.LIQUIDATOR_IS_NOT_LENDER(msg.sender, creditPosition.lender);
        }
    }
```

The function checks if the `collateralRatio` is less than 100% (`PERCENT`), but it does not check if the `collateralRatio` is 0 or negative. This can lead to situations where the function allows self-liquidation with an invalid collateral ratio or some edge cases issues.

### Recommended Mitigation Steps
Add additional checks in the `validateSelfLiquidate` function to ensure that the collateral ratio is greater than 0 and less than 100%. 









## [QA-20] Fix For a Case of Mismatch Between `msg.value` and `params.amount` in `executeDeposit` Function is Required

### Impact
Looking at the implementation of the `executeDeposit` function, it doesn't properly handle the case where `msg.value` is non-zero but does not match `params.amount`. This could affect deposits in the Size contract.

### Proof of Concept

[`Size.sol`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L153-L156) makes a call to `validateDeposit` and `executeDeposit`:

```solidity
function deposit(DepositParams calldata params) public payable override(ISize) whenNotPaused {
    state.validateDeposit(params);
    state.executeDeposit(params);
}
```

And if we look at the [Deposit.sol contract](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Deposit.sol#L64-L90):

```solidity
function validateDeposit(State storage state, DepositParams calldata params) external view {
    // validate msg.value
    if (msg.value != 0 && (msg.value != params.amount || params.token != address(state.data.weth))) {
        revert Errors.INVALID_MSG_VALUE(msg.value);
    }
}

function executeDeposit(State storage state, DepositParams calldata params) public {
    address from = msg.sender;
    uint256 amount = params.amount;
    if (msg.value > 0) {
        // do not trust msg.value (see `Multicall.sol`)
        amount = address(this).balance;
        state.data.weth.deposit{value: amount}();
        state.data.weth.forceApprove(address(this), amount);
        from = address(this);
    }

    if (params.token == address(state.data.underlyingBorrowToken)) {
        state.depositUnderlyingBorrowTokenToVariablePool(from, params.to, amount);
    } else {
        state.depositUnderlyingCollateralToken(from, params.to, amount);
    }

    emit Events.Deposit(params.token, params.to, amount);
}
```
The `validateDeposit` function checks if `msg.value` is non-zero and ensures it matches `params.amount` and that `params.token` is the WETH address. If these conditions are not met, it reverts with `Errors.INVALID_MSG_VALUE`.
The `executeDeposit` function, however, does not handle the case where `msg.value` is non-zero but does not match `params.amount` properly. It sets `amount` to the contract's balance and deposits it as WETH, potentially leading to inconsistencies.

### Recommended Mitigation Steps
Add a check in the `executeDeposit` function to ensure `msg.value` matches `params.amount` when `msg.value` is non-zero. 









## [QA-21] Lack of Slippage Protection in Liquidation and Withdrawal Functions

### Impact
The absence of slippage protection or minimum received amount checks in the liquidation and withdrawal functions could lead to potential exploitation most especially in volatile markets, resulting in users receiving less collateral than expected.

### Proof of Concept
##### Liquidate.sol
In the `executeLiquidate` function, there is no check to ensure the minimum amount of collateral received:
```solidity
state.data.collateralToken.transferFrom(debtPosition.borrower, msg.sender, liquidatorProfitCollateralToken);
```

##### LiquidateWithReplacement.sol
In the `executeLiquidateWithReplacement` function, the `executeLiquidate` function is called, which lacks slippage protection:
```solidity
liquidatorProfitCollateralToken = state.executeLiquidate(
    LiquidateParams({
        debtPositionId: params.debtPositionId,
        minimumCollateralProfit: params.minimumCollateralProfit
    })
);
```

##### Withdraw.sol
In the `executeWithdraw` function, there is no check to ensure the minimum amount of collateral received:
```solidity
state.withdrawUnderlyingCollateralToken(msg.sender, params.to, amount);
```

### Recommended Mitigation Steps
Add parameters for the minimum acceptable amounts to be received and include checks to ensure that the actual amounts transferred meet these minimums.








## [QA-22] Unchecked ERC-20 Return Value in Different Instances

### Impact
Across the protocol, there are many instances where contracts don't check the return value of the `transferFrom` call. This can lead to potential issues if the ERC-20 token does not throw an exception on failure but instead returns `false`, resulting in an undetected failed transfer.

### Proof of Concept
Use this ssearch markdown to see the various instances: https://github.com/search?q=repo%3Acode-423n4%2F2024-06-size%20transferfrom&type=code

The issue is that the return value of `transferFrom` is not checked. According to the [ERC-20 token standard](https://eips.ethereum.org/EIPS/eip-20), the `transferFrom` function returns a `bool` to signal the success or failure of the call. If the token returns `false` instead of throwing an exception, the failure will go unnoticed.

### Recommended Mitigation Steps
Consider using the `safeTransferFrom` function from the [OpenZeppelin `SafeERC20` library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol), which ensures that all calls revert on failure, regardless of whether the underlying token throws an exception or returns `false`.










## [QA-23] Incorrect Liquidation Ratio Validation Enables Unsafe Protocol States

### Impact
Allows setting liquidation thresholds above opening thresholds, risking immediate liquidations and protocol instability.

>In a typical lending protocol:
>- The liquidation collateral ratio should be lower than the opening collateral ratio.
>- When updating the liquidation collateral ratio, we should ensure it doesn't exceed the opening ratio.

### Proof of Concept
The `executeUpdateConfig` function incorrectly validates the `crLiquidation` parameter: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/UpdateConfig.sol#L90

```solidity
} else if (Strings.equal(params.key, "crLiquidation")) {
    if (params.value >= state.riskConfig.crLiquidation) {
        revert Errors.INVALID_COLLATERAL_RATIO(params.value);
    }
    state.riskConfig.crLiquidation = params.value;
}
```

Issues:
1. Prevents increasing liquidation ratio.
2. Doesn't ensure liquidation ratio stays below opening ratio.

Scenario:
With crOpening at 150% and crLiquidation at 120%, an admin could set crLiquidation to 160%. This would make all positions between 150% and 160% immediately liquidatable and prevent new borrows, potentially causing a liquidation cascade and significant losses.

### Recommended Mitigation Steps
```diff
} else if (Strings.equal(params.key, "crLiquidation")) {
-   if (params.value >= state.riskConfig.crLiquidation) {
+   if (params.value >= state.riskConfig.crOpening) {
        revert Errors.INVALID_COLLATERAL_RATIO(params.value);
    }
    state.riskConfig.crLiquidation = params.value;
}
```

This ensures the liquidation ratio remains below the opening ratio, maintaining protocol safety while allowing necessary adjustments.









## [QA-24] Incorrect Use of `transferFrom` Instead of `transfer` in `executeClaim` Function  Could Lead to Failed Claims

### Impact

The use of `transferFrom` instead of `transfer` in the `executeClaim` function can lead to transfer failures if the contract hasn't been approved to spend tokens on its own behalf. This could prevent users from successfully claiming their credit positions resulting in locked funds.

### Proof of Concept

Look at the `executeClaim` function: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Claim.sol#L48-L61

```solidity
function executeClaim(State storage state, ClaimParams calldata params) external {
    // ... (earlier code omitted for brevity)

    uint256 claimAmount = Math.mulDivDown(
        creditPosition.credit, state.data.borrowAToken.liquidityIndex(), debtPosition.liquidityIndexAtRepayment
    );
    state.reduceCredit(params.creditPositionId, creditPosition.credit);
    state.data.borrowAToken.transferFrom(address(this), creditPosition.lender, claimAmount);

    // ... (remaining code omitted for brevity)
}
```

The function calculates the `claimAmount` and then attempts to transfer this amount to the lender using `transferFrom`. However, `transferFrom` requires prior approval, which this contract likely doesn't have for its own address. 

>The `transferFrom` function is typically used when a contract wants to transfer tokens on behalf of another address that has approved the contract to do so.

In this case, the contract itself should be transferring the tokens directly to the lender. Using `transferFrom` can lead to the following problems:
- The transfer might fail if the contract hasn't been approved to spend tokens on its own behalf.
- Even if it doesn't fail, it's unnecessarily complex and gas-intensive compared to a simple `transfer`.


Scenario:
1. A lender's credit position becomes claimable after a loan is repaid.
2. The lender calls the claim function.
4. The function calculates the correct claim amount.
5. The transfer fails because the contract doesn't have approval to transfer tokens from itself.
6. The claim transaction reverts, and the lender is unable to receive their funds.

### Recommended Mitigation Steps
Replace the `transferFrom` call with a `transfer` call. The line should be changed to:

```solidity
state.data.borrowAToken.transfer(creditPosition.lender, claimAmount);
```
Even better if we use OZ's `SafeTransfer` library that handles failures and reverts more gracefully.
It'll also be worth adding a check to ensure the contract has sufficient balance before attempting the transfer. This could prevent issues if the contract's token balance is unexpectedly low.






## [QA-25] Potential Logic Issue in `executeLiquidateWithReplacement` Function in `LiquidateWithReplacement` Library

### Proof of Concept

The [`executeLiquidateWithReplacement`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/LiquidateWithReplacement.sol#L120-L164) function in `LiquidateWithReplacement` should improve the handling of the `DebtPosition` struct. Specifically, the issue lies in the way the `debtPosition` is updated and how the `futureValue` is handled.

1. **Updating `debtPosition` with `debtPositionCopy`**:
   - The `debtPosition` is a storage reference, while `debtPositionCopy` is a memory copy. The function updates `debtPosition` with values from `debtPositionCopy`, but `debtPositionCopy` is not modified after the liquidation. This means that any changes to `debtPosition` during the liquidation process are not reflected in `debtPositionCopy`.

2. **Handling of `futureValue`**:
   - The `futureValue` of `debtPosition` is set to `debtPositionCopy.futureValue`, which is the value before liquidation. If the liquidation process modifies the `futureValue`, this change is not captured, leading to potential inconsistencies.

### Recommendations

Ensure that any modifications to `debtPosition` during the liquidation process are correctly captured and updated. 

```diff
function executeLiquidateWithReplacement(State storage state, LiquidateWithReplacementParams calldata params)
    external
    returns (uint256 issuanceValue, uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
{
    emit Events.LiquidateWithReplacement(params.debtPositionId, params.borrower, params.minimumCollateralProfit);

-    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);
-    DebtPosition memory debtPositionCopy = debtPosition;
+    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);
    BorrowOffer storage borrowOffer = state.data.users[params.borrower].borrowOffer;
-    uint256 tenor = debtPositionCopy.dueDate - block.timestamp;
+    uint256 tenor = debtPosition.dueDate - block.timestamp;

    liquidatorProfitCollateralToken = state.executeLiquidate(
        LiquidateParams({
            debtPositionId: params.debtPositionId,
            minimumCollateralProfit: params.minimumCollateralProfit
        })
    );

    uint256 ratePerTenor = borrowOffer.getRatePerTenor(
        VariablePoolBorrowRateParams({
            variablePoolBorrowRate: state.oracle.variablePoolBorrowRate,
            variablePoolBorrowRateUpdatedAt: state.oracle.variablePoolBorrowRateUpdatedAt,
            variablePoolBorrowRateStaleRateInterval: state.oracle.variablePoolBorrowRateStaleRateInterval
        }),
        tenor
    );
-    issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
-    liquidatorProfitBorrowToken = debtPositionCopy.futureValue - issuanceValue;
+    issuanceValue = Math.mulDivDown(debtPosition.futureValue, PERCENT, PERCENT + ratePerTenor);
+    liquidatorProfitBorrowToken = debtPosition.futureValue - issuanceValue;

    debtPosition.borrower = params.borrower;
-    debtPosition.futureValue = debtPositionCopy.futureValue;
    debtPosition.liquidityIndexAtRepayment = 0;

    emit Events.UpdateDebtPosition(
        params.debtPositionId,
        debtPosition.borrower,
        debtPosition.futureValue,
        debtPosition.liquidityIndexAtRepayment
    );

    state.data.debtToken.mint(params.borrower, debtPosition.futureValue);
    state.data.borrowAToken.transferFrom(address(this), params.borrower, issuanceValue);
    state.data.borrowAToken.transferFrom(address(this), state.feeConfig.feeRecipient, liquidatorProfitBorrowToken);
}
```








## [QA-26] Incorrect Natspec Comment 

### Impact
The incorrect natspec comment could mislead developers or auditors about the function's behavior.

### Proof of Concept
The [natspec](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/CapsLibrary.sol#L13) states: 

```solidity
/// @dev Reverts if the debt increase is greater than the supply increase and the supply is above the cap
```

However, the actual implementation checks for the opposite condition: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/CapsLibrary.sol#L35-L38

```solidity
if (borrowATokenSupplyIncrease > debtATokenSupplyDecrease) {
    // revert
    revert Errors.BORROW_ATOKEN_INCREASE_EXCEEDS_DEBT_TOKEN_DECREASE(
        borrowATokenSupplyIncrease, debtATokenSupplyDecrease
    );
}
```

The function actually reverts if the borrow aToken supply increase is greater than the debt token supply decrease, not if the debt increase is greater than the supply increase as stated in the natspec.

### Recommended Mitigation Steps
The natspec comment should be corrected to accurately reflect the function's behavior. It should state that the function reverts if the borrow aToken supply increase exceeds the debt token supply decrease when the supply is above the cap. Additionally, consider adding a note that this check is only performed when the supply is above the cap to provide full clarity on the function's behavior.






## [QA-27] Inaccurate Logic in `getAdjustedAPR` Function Causes Unintended Reverts

### Impact
The `getAdjustedAPR` function will always revert if the `variablePoolBorrowRateStaleRateInterval` is set to 0. This can hinder the function from returning the adjusted APR when staleness checks are disabled.

### Proof of Concept
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/YieldCurveLibrary.sol#L94-L99

```solidity
} else if (
    params.variablePoolBorrowRateStaleRateInterval == 0
        || (
            block.timestamp - params.variablePoolBorrowRateUpdatedAt
                > params.variablePoolBorrowRateStaleRateInterval
        )
) {
    revert Errors.STALE_RATE(params.variablePoolBorrowRateUpdatedAt);
}
```

The protocol's intention seems to be to revert if the `variablePoolBorrowRate` is stale, i.e., if the time elapsed since it was last updated is greater than the `variablePoolBorrowRateStaleRateInterval`. However, the condition also includes `params.variablePoolBorrowRateStaleRateInterval == 0`, which means it will always revert if the stale rate interval is set to 0, regardless of whether the rate is actually stale or not.

### Recommended Mitigation Steps
The correct logic should be to revert only if:
1. The stale rate interval is not 0 (meaning staleness checks are enabled), and 
2. The time elapsed since the last update is greater than the stale rate interval.

```diff
- } else if (
-     params.variablePoolBorrowRateStaleRateInterval == 0
-         || (
-             block.timestamp - params.variablePoolBorrowRateUpdatedAt
-                 > params.variablePoolBorrowRateStaleRateInterval
-         )
- ) {
+ } else if (
+     params.variablePoolBorrowRateStaleRateInterval != 0 &&
+     block.timestamp - params.variablePoolBorrowRateUpdatedAt > params.variablePoolBorrowRateStaleRateInterval
+ ) {
    revert Errors.STALE_RATE(params.variablePoolBorrowRateUpdatedAt);
}
```











## [QA-28] Redundant Initialization of `creditPosition` Variable in `executeBuyCreditMarket` Function

### Impact
The unnecessary initialization of the `creditPosition` variable leads to inefficient use of memory and gas, which can be optimized.

### Proof of Concept
In the `executeBuyCreditMarket` function, the `creditPosition` local variable is initialized but not used effectively. Only the `lender` field is accessed, and the `credit` field is accessed directly from storage later in the code.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L130-L141

```solidity
CreditPosition memory creditPosition;
// ...
if (params.creditPositionId == RESERVED_ID) {
    // ...
} else {
    DebtPosition storage debtPosition = state.getDebtPositionByCreditPositionId(params.creditPositionId);
    creditPosition = state.getCreditPosition(params.creditPositionId);

    borrower = creditPosition.lender;
    tenor = debtPosition.dueDate - block.timestamp;
}

// Later in the code
maxCashAmountIn: params.creditPositionId == RESERVED_ID
    ? cashAmountIn
    : Math.mulDivUp(state.getCreditPosition(params.creditPositionId).credit, PERCENT, PERCENT + ratePerTenor),
maxCredit: params.creditPositionId == RESERVED_ID
    ? Math.mulDivDown(cashAmountIn, PERCENT + ratePerTenor, PERCENT)
    : state.getCreditPosition(params.creditPositionId).credit,
```
As seen above:
- The `creditPosition` variable is initialized but only its `lender` field is accessed.
- The `credit` field is accessed directly from storage via `state.getCreditPosition(params.creditPositionId).credit`.

### Recommended Mitigation Steps
Remove the `creditPosition` local variable and directly access the necessary fields from storage.







## [QA-29]  Fix Natspec
The parameter comment for the `maxCashAmountOut` parameter in the `getCreditAmountIn` function is incorrectly labeled as `maxCredit`, which can lead to confusion and potential misuse of the function.

### Impact
Causes confusion. It can lead to misinterpretation of the purpose and usage of the `maxCashAmountOut` parameter.

### Proof of Concept
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L223

```solidity
222:     /// @param maxCredit The maximum credit
223:     /// @param maxCredit The maximum cash amount out
```
In line 223, the parameter documentation incorrectly states `@param maxCredit The maximum cash amount out`, but it should be `@param maxCashAmountOut The maximum cash amount out`. This mismatch between the parameter name and its description can lead to confusion and potential misuse of the function.

### Recommended Mitigation Steps
Update the parameter documentation for the `maxCashAmountOut` parameter in the `getCreditAmountIn` function to accurately reflect its purpose and usage.



