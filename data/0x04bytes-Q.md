# Quality Assurance Report

Report about low level severity risk issues.

## Low Severity Issues

### [L-01] The `params.amount` validation could be wrong if the value of `params.amount` is `amountCashOut` in `Size.sellCreditMarket()` and `amountCashIn` in `Size.buyCreditMarket()` potentially leads to opportunity loss

#### Summary

The protocol validates the minimum amount of credit created in `BuyCreditMarket.validateBuyCreditMarket()` and `SellCreditMarket.validateSellCreditMarket()`.

```solidity
.....
// validate amount
if (params.amount < state.riskConfig.minimumCreditBorrowAToken) {
    revert Errors.CREDIT_LOWER_THAN_MINIMUM_CREDIT(params.amount, state.riskConfig.minimumCreditBorrowAToken);
}
.....
```

The problem is, the `params.amount` can mean amount of `cashIn` in `BuyCreditMarket.validateBuyCreditMarket()` if `params.exactAmountIn = true` and can mean amount of `cashOut` in `SellCreditMarket.validateSellCreditMarket()` if the `params.exactAmountIn = false`. If one of those condition happen, the protocol can wrongly assume the amount of cash as the amount of credit.

Let's review the relationship between the amount of credit and cash.

$$credit = cash * (1+interestRate) $$ (1)

In the view of credit seller, selling credit in credit market will require them to pay swap fee so that the amount they will receive can be written as:

$$cashOut = cash * (1-swapFeeRate) $$

can be rewritten as:

$$cash = \frac{cashOut}{(1-swapFeeRate)} $$ (2)

Now substitute $cash$ in eq (1) with eq (2) so the new equation formed.

$$credit = \frac{cashOut}{(1-swapFeeRate)} * (1+interestRate) $$ (3)

Because the value of `riskConfig.minimumCreditBorrowAToken` in production is `$50`. We will try to calculate the amount of $cashOut$ if the `credit = 51` to show that it must be valid because it is greater than `riskConfig.minimumCreditBorrowAToken`.

Let's assume the `swapFeeRate = 0.5%` and `interestRate = 10%`

$$\$51 = \frac{cashOut}{(1-0.005)} * (1+0.1)$$

$$cashOut = \frac{\$51 * (1-0.005)}{(1+0.1)}$$

$$cashOut = \$46.132$$

It must be valid if the `params.amount = $46.132` and `params.exactAmountIn = false` in `sellCreditMarket()` without credit fractionalization, but because the validation is wrong, the operation will revert. If we take credit fractionalization into account, the value of `params.amount` could possibly be smaller because the seller needs to take additional `$5` loan to cover the `fragmentationFee`.

For the `buyCreditMarket()` almost similar, the different is in buy credit market there is no swap fee for the credit buyer because it will be paid by the credit seller and also the credit buyer need to provide additional `$5` to cover the `fragmentationFee`.

#### Impact
Potential opportunity loss. In the example above the opportunity loss would be around `$4.86`.

#### Tool used
Manual Review

#### Recommendation

Remove the validation lines below from `validateSellCreditMarket()` and `validateBuyCreditMarket()`

```solidity
.....
// validate amount
if (params.amount < state.riskConfig.minimumCreditBorrowAToken) {
    revert Errors.CREDIT_LOWER_THAN_MINIMUM_CREDIT(params.amount, state.riskConfig.minimumCreditBorrowAToken);
}
.....
```

It is error prone and redundant, because the validation is already done in `createDebtAndCreditPositions()` and `createCreditPosition()`. Those functions call `state.validateMinimumCreditOpening(creditPosition.credit)` where the input is actual amount of credit.

### [L-02] The credits sellers who are willing to sell their credits through `Size.sellCreditLimit()` can mistakenly taking new debt if they have deposited enough collateral token when the lenders buy through `buyCreditMarket()` with `params.creditPositionId = RESERVED_ID`

#### Summary

The protocol allows any users to deposit collateral token and at the same time, the protocol also allows the users to sell their existing credits through `Size.sellMarketLimit()`. The problem is, there is no machanism to explicitly indicate that the users only want to sell existing credits and don't want to take a new debt. If the users forget to withdraw their collateral token, they will end up taking a new debt instead of selling existing credits if the credit buyer call `buyCreditMarket()` with `params.creditPositionId = RESERVED_ID`. Also to note here, it is not efficient in gas usage if the users need to withdraw every time they want to sell existing credits in market limit.

#### Impact

If the users who want to sell existing credits end up taking a new debt, they are risking their collateral because they are now suffering from possibility of liquidation if their collateral is under water.

#### Tool used
Manual Review

#### Recommendation

Introduce a new field in `SellCreditLimitParams` and `BorrowOffer` to indice whether we only sell existing credit or also accept new debt.

```diff
struct SellCreditLimitParams {
    // The yield curve of the borrow offer
    YieldCurve curveRelativeTime;
+   bool sellExistingCreditOnly;
}
```

```diff
struct BorrowOffer {
    // The yield curve in relative terms
    // Borrowers can protect themselves by setting an opening limit CR for a loan to be matched
    YieldCurve curveRelativeTime;
+   bool sellExistingCreditOnly;
}
```

And add validation in: `src/libraries/actions/BuyCreditMarket.sol`

```diff
function validateBuyCreditMarket(State storage state, BuyCreditMarketParams calldata params) external view {
.....
    BorrowOffer memory borrowOffer = state.data.users[borrower].borrowOffer;

+   if (params.creditPositionId == RESERVED_ID && borowOffer.sellExistingCreditOnly == true) {
+       revert("only sell existing credit");
+   }
.....
}
```