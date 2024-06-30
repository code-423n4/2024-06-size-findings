# L

- [L](#l)
  - [Validation issue: APRs array do not check if it was sorted. Allowing yield decrease with time and suddenly jump to a higher value.](#validation-issue-aprs-array-do-not-check-if-it-was-sorted-allowing-yield-decrease-with-time-and-suddenly-jump-to-a-higher-value)
  - [Admin can set RiskConfig Collateral Fee percent can be higher than 100%](#admin-can-set-riskconfig-collateral-fee-percent-can-be-higher-than-100)
  - [ForceApprove WETH when user deposit with msg.value is unnecessary](#forceapprove-weth-when-user-deposit-with-msgvalue-is-unnecessary)
  - [It is not possible for lender to lend all 100% fund when matching order with borrower through `BuyCreditMarket.executeBuyCreditMarket()`](#it-is-not-possible-for-lender-to-lend-all-100-fund-when-matching-order-with-borrower-through-buycreditmarketexecutebuycreditmarket)

## Validation issue: APRs array do not check if it was sorted. Allowing yield decrease with time and suddenly jump to a higher value.

There is no reason for lender and borrower to accept lower yield with longer time.
It should be sorted in ascending order. Same as tenors array.
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/YieldCurveLibrary.sol#L50-L78

```solidity
        // validate aprs //@not sorted?
        // N/A
        //@note aprs in tenors array is APRs yearly. it is sorted in ascending order.
        // validate tenors
        uint256 lastTenor = type(uint256).max;
        for (uint256 i = self.tenors.length; i != 0; i--) {
            if (self.tenors[i - 1] >= lastTenor) {//@note tenors[] cannot be duplicated in array
                revert Errors.TENORS_NOT_STRICTLY_INCREASING();
            }
            lastTenor = self.tenors[i - 1];
        }
```

if aprs array is not sorted. User is allowed to set 3 months tenor with 5% rate, 6 months tenor with 1% rate, then 12 months tenor with 100% rate.
Allowing unpredictable behavior and potential exploit. Mostly against bot or smartcontract account.

## Admin can set RiskConfig Collateral Fee percent can be higher than 100%

Fee higher than 100% is permitted and not reverted during liquidation.
If this happen, fee will be taken from other users fund. Causing lost to user.

## ForceApprove WETH when user deposit with msg.value is unnecessary

In `Deposit.executeDeposit()`, WETH.transferFrom already ignore approval if from address same as caller. So approve is unnecessary.

```solidity
        if (msg.value > 0) {
            // do not trust msg.value (see `Multicall.sol`)
            amount = address(this).balance;
            // slither-disable-next-line arbitrary-send-eth
            state.data.weth.deposit{value: amount}();
            state.data.weth.forceApprove(address(this), amount); // @audit this can safely be removed
            from = address(this);
        }
```

## It is not possible for lender to lend all 100% fund when matching order with borrower through `BuyCreditMarket.executeBuyCreditMarket()`

Some of the USDC will be leftover as aave accrued token already shifted value during transaction pending execution.
So when lender try to lend all available USDC to borrower here:
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L195

There will be some tiny USDC left over in lender account.
