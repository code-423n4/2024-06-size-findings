[L-1] There should be a `grace period` for `repayment`
[L-2] Users cannot `borrow` `USDC` from a `lender` who has less than the `minimumCreditBorrowAToken` amount of `borrowA` tokens
[L-3] The `buyCreditMarket` transaction can be reverted due to an `amount check`
[L-4] The `validateVariablePoolHasEnoughLiquidity check` in the `buyCreditMarket` function is incorrect
[L-5] When the protocol is paused, `debt` positions can become `overdue` because `repayments` are also paused
[L-6] `Lenders` can potentially lose funds in the `buyCreditMarket` function
[L-7] Users who have a `loan offer` should have some `USDC` to create a `loan`
[L-8] The `isMulticall` flag is not correctly reset to `false` in the `multicall` function

# [L-1] There should be a `grace period` for `repayment`.

`Debt` positions have a `due date` and can be `liquidated` after this date. 
Many users will try to `repay` their `debts` before the `due date`. 
For example, some users may start `repaying` one day or one hour in advance, depending on their preference. 
However, there is no `100%` guarantee that these `repayments` will be executed within this period. 
If the repayment transaction is not included in the block, the `debt` position might be accidentally `liquidated`.

At this point, anyone can easily `profit` by `liquidating` this `debt` position.
Therefore, it is sensible to set a `grace period` during which `liquidation` is not allowed. 
This can help prevent accidental `liquidation` and protect users' funds.

This issue can be upgraded as it results in users' `funds loss`.

# [L-2] Users cannot `borrow` `USDC` from a `lender` who has less than the `minimumCreditBorrowAToken` amount of `borrowA` tokens.

Suppose the `minimumCreditBorrowAToken` is `1000 USDC`. 
A `lender` has a `loan offer` with an `APR` of `5%` and `960 USDC` available. 
A `borrower` wants to `borrow` all `960 USDC`, so he call the `sellCreditMarket` function with an `amount` parameter of `960` and `exactAmountIn` set to `false`.
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L188-L195
```
function sellCreditMarket(SellCreditMarketParams memory params) external payable override(ISize) whenNotPaused {
    state.validateSellCreditMarket(params);
    uint256 amount = state.executeSellCreditMarket(params);
    if (params.creditPositionId == RESERVED_ID) {
        state.validateUserIsNotBelowOpeningLimitBorrowCR(msg.sender);
    }
    state.validateVariablePoolHasEnoughLiquidity(amount);
}
```
Even though everything seems correct, this transaction will be reverted due to the following check. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L93-L95
```
function validateSellCreditMarket(State storage state, SellCreditMarketParams calldata params) external view {
    // validate amount
    if (params.amount < state.riskConfig.minimumCreditBorrowAToken) {  // @audit, here
        revert Errors.CREDIT_LOWER_THAN_MINIMUM_CREDIT(params.amount, state.riskConfig.minimumCreditBorrowAToken);
    }
}
```
The future `credit` value is `960 * (1 + 0.05)`, which is larger than the `minimumCreditBorrowAToken`. 
Therefore, it should be possible to create this `loan/debt`, but the transaction is still reverted.

The `borrower` can change `exactAmountIn` to `true` and calculate the future `credit` value needed to receive `960 USDC` now. 
However, this is not an ideal solution.

This issue can be upgraded as it can lead to a `DoS`.

# [L-3] The `buyCreditMarket` transaction can be reverted due to an `amount check`.

In the `validateBuyCreditMarket` function, the `amount check` does not account for the fact that the future `credit` value will be larger than the `input cash amount` due to `interest` when `exactAmountIn` is `true`. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L91-L93
```
function validateBuyCreditMarket(State storage state, BuyCreditMarketParams calldata params) external view {
    // validate amount
    if (params.amount < state.riskConfig.minimumCreditBorrowAToken) {  // @audit, here
        revert Errors.CREDIT_LOWER_THAN_MINIMUM_CREDIT(params.amount, state.riskConfig.minimumCreditBorrowAToken);
    }
}
```
Consequently, a valid transaction may be reverted if the `amount` is less than `minimumCreditBorrowAToken`, even though the future `credit` will indeed exceed `minimumCreditBorrowAToken`.

# [L-4] The `validateVariablePoolHasEnoughLiquidity check` in the `buyCreditMarket` function is incorrect.

Whenever users `borrow` `USDC` from `lenders`, most of them will try to `withdraw` those `USDC` from the `Aave pool` immediately. 
If the `Aave pool` does not have enough liquidity, they cannot `withdraw` `USDC`, which is unfair because `interest` has already started to accumulate.

For this purpose, the `validateVariablePoolHasEnoughLiquidity check` exists in the `buyCreditMarket` and `sellCreditMarket` functions. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L184
```
function buyCreditMarket(BuyCreditMarketParams calldata params) external payable override(ISize) whenNotPaused {
    state.validateBuyCreditMarket(params);
    uint256 amount = state.executeBuyCreditMarket(params);
    if (params.creditPositionId == RESERVED_ID) {
        state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
    }
    state.validateVariablePoolHasEnoughLiquidity(amount);  // @audit, here
}
```
However, this `check` does not consider the `fee` in the `buyCreditMarket` function. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L195-L196
```
function executeBuyCreditMarket(State storage state, BuyCreditMarketParams memory params)
    external
    returns (uint256 cashAmountIn)  // @audit, here
{
    state.data.borrowAToken.transferFrom(msg.sender, borrower, cashAmountIn - fees);  // @audit, here
    state.data.borrowAToken.transferFrom(msg.sender, state.feeConfig.feeRecipient, fees);
}
```
Specifically, there is no need to include the `fee` in this check because `borrowers` only intend to `withdraw` the `borrowed` amount, not the `fees`.

This `check` is correct in the `sellCreditMarket` function.

This issue can be upgraded as it can lead to a `DoS`.

# [L-5] When the protocol is paused, `debt` positions can become `overdue` because `repayments` are also paused.

When the protocol is paused, `repayments` are not allowed, which can result in `debt` positions becoming `overdue` and potentially `liquidatable`. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L198
```
function repay(RepayParams calldata params) external payable override(ISize) whenNotPaused {  // @audit, here
    state.validateRepay(params);
    state.executeRepay(params);
}
```
In the `publicly known issues`, there is mention of `collateral price fluctuation`, but this issue is not related to changes in `collateral prices`.
```
In case the protocol is paused, the price of the collateral may change during the unpause event. This may cause unforeseen liquidations, among other issues
```

One possible solution could be to allow `repayments` even when the protocol is paused.

# [L-6] `Lenders` can potentially lose funds in the `buyCreditMarket` function.

In the `buyCreditMarket` function, let's denote some values:
`A` is the future `credit` value that the `lender` will receive.
`V` is the `cash` amount that the `lender` should pay to the `borrower`.
`k` is the `swap fee`.
`T` is the `tenor`.
`r` is the rate.
According to the formula described in the documentation, when the `fragmentation fee` is `0`, the relationship between these values is as follows:
```
A=V×(1+r)
fee=V×k×T
```
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L285-L286
```
function getCreditAmountOut(
    State storage state,
    uint256 cashAmountIn,
    uint256 maxCashAmountIn,
    uint256 maxCredit,
    uint256 ratePerTenor,
    uint256 tenor
) internal view returns (uint256 creditAmountOut, uint256 fees) {
    if (cashAmountIn == maxCashAmountIn) {
        creditAmountOut = maxCredit;
        fees = getSwapFee(state, cashAmountIn, tenor);
    } else if (cashAmountIn < maxCashAmountIn) {
        ...
    } else {
        revert Errors.NOT_ENOUGH_CREDIT(maxCashAmountIn, cashAmountIn);
    }
}
```

There is no guarantee that `A` is always larger than the sum of `V+fee` due to the absence of a strict relation between `k` and `r`. 
Therefore, `lenders` can potentially pay more funds than what they will receive in the future.
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L195-L196
```
function executeBuyCreditMarket(State storage state, BuyCreditMarketParams memory params)
    external
    returns (uint256 cashAmountIn)
{
    state.data.borrowAToken.transferFrom(msg.sender, borrower, cashAmountIn - fees);
    state.data.borrowAToken.transferFrom(msg.sender, state.feeConfig.feeRecipient, fees);
}
```

The `loss` can increase when there is a `fragmentation fee`.
This issue can be upgraded as it can lead to a user's funds loss.

# [L-7] Users who have a `loan offer` should have some `USDC` to create a `loan`.

In the `sellCreditMarket` function, the transaction will be reverted if the `lender` does not have enough `USDC`. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L201-L202
```
function executeSellCreditMarket(State storage state, SellCreditMarketParams calldata params)
    external
    returns (uint256 cashAmountOut)
{
    state.data.borrowAToken.transferFrom(params.lender, msg.sender, cashAmountOut);
    state.data.borrowAToken.transferFrom(params.lender, state.feeConfig.feeRecipient, fees);
}
```
To prevent this, we can add a check for the `lender`'s `balance` in the `pre-check` stage.

# [L-8] The `isMulticall` flag is not correctly reset to `false` in the `multicall` function.

In the `multicall` function, we set `isMulticall` to `true` at the beginning and reset it to `false` at the end. 
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L44
```
function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
    state.data.isMulticall = true;

    ...

    state.data.isMulticall = false;
}
```
However, if another `multicall` function is called within the initial `multicall` function, the `isMulticall` flag will be reset to `false` when the `inner multicall` function execution ends. 
If a subsequent `deposit` function follows, the `cap check` will be performed, which violates the `multicall` function design.

To prevent this, we should not allow an inner `multicall` function within another `multicall` function.
```
function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
+    if (state.data.isMulticall == true) revert();
    state.data.isMulticall = true;

    ...

    state.data.isMulticall = false;
}
```