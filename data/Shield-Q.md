## L-01 `selfLiquidate` can be executed after a delay since no deadline check

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SelfLiquidate.sol#L34

### Description
The time sensitive operations such as the `BuyCreditMarket`, `SellCreditMarket`, `LiquidateWithReplacement` functions perform a deadline check as shown below:

 
        // validate deadline
        if (params.deadline < block.timestamp) {
            revert Errors.PAST_DEADLINE(params.deadline);
        }


The `SelfLiquidate` is a function which helps the lender to secure his credit position by allowing to liquidate insolvent positions for a smaller loss.

But the `validateSelfLiquidate` does not perform a deadline check as it is with the other operations explained earlier.

As a result if the lender's `SelfLiquidate` function is executed at a time where collateral value is extremely less the lender will have to bear a heavy loss more than he anticipated. Hence recommended to add the deadline functionality here as well.

Because this functionality to mitigate the risk to the lender might not be effective as described above.

### Recommended Mitigation Steps
May be `selfLiquidation` can be executed by THE `KEEPER` bots as well to ensure smaller loss to the lender or the size treasury of can cover atleast part of the loss to the lender if above scenario occurs.

## L-02  it should be mentioned in the natspec docs that `liquidateWithReplacement` is conditional

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/LiquidateWithReplacement.sol#L63

### Description
`liquidateWithReplacement` works only when the loan is active and doesn't work when a loan is overdue which seems like intended behaviour but there is no mention of that in the natspec docs 

### Recommended Mitigation Steps
Add in natspec docs that `liquidateWithReplacement` does not work for overdue loans

## L-03 Error in natspec comments in the `AccountingLibrary.getCreditAmountIn`

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/AccountingLibrary.sol#L228

### Description
In the A`ccountingLibrary.getCreditAmountIn` function, the transaction reverts if the following condition occurs:

`maxCashAmountOutFragmentation < amountOut < maxCashAmountOut`


The natspec comment fo the above scenario is given as follows:

            // for maxCashAmountOutFragmentation < amountOut < maxCashAmountOut we are in an inconsistent situation
            //   where charging the swap fee would require to sell a credit that exceeds the max possible credit


But the credit exceed happens not because fo the swap fee calculation but because of the fragmentation fee calculation, since the swap fee has already been accounted for in the `maxCashAmountOut` value.

### Recommended Mitigation Steps
`//   where charging the fragmentation fee would require to sell a credit that exceeds the max possible credit`

## L-04 Discrepancy between logic implementation and Natspec

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/token/NonTransferrableScaledToken.sol#L98

### Description
The `NonTransferrableScaledToken._unscale` is used to unscale the scaled amount of aTokens in to its correct balance after accounting for the accrued interest.

This is done by multiplying the `scaledAmount` by the current liquidity index of the pool. But the natspec comment says the two values should be divided as shown below. 
```
/// @dev The unscaled amount is the scaled amount divided by the current liquidity index
function _unscale(uint256 scaledAmount) internal view returns (uint256) {

return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);

}
```

### Recommended Mitigation Steps
`/// @dev The unscaled amount is the scaled amount multiplied by the current liquidity index`


## L-05 lender should not be allowed to sell their credit position if it is not marked for sale

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SellCreditMarket.sol#L77

### Description
There is no check that the lender can sell their own credit position that is the reason the forSale param exists we see that the check is made in [`BuyCreditMarket.sol`](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/BuyCreditMarket.sol#L75) a similar check should exist in `SellCreditMarket.sol` too

### Recommended Mitigation Steps
add a check whether the credit position is for sale in `SellCreditMarket.sol`

## L-06 Discrepancy between logic implementation and Natspec

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol#L85

### Description
The comment states that
`if the loan is both underwater and overdue, the protocol fee related to underwater liquidations takes precedence`


whereas when determining `collateralProtocolPercent`  only `isUserUnderwater` checked, whereas acc to the comment so the overdue check should be present too as at the point the loan can be underwater or/and overdue

```
        uint256 collateralProtocolPercent = state.isUserUnderwater(debtPosition.borrower)
            ? state.feeConfig.collateralProtocolPercent
            : state.feeConfig.overdueCollateralProtocolPercent;
```

### Recommended Mitigation Steps
The check should be updated to something like

```
        uint256 collateralProtocolPercent = state.isUserUnderwater(debtPosition.borrower) && loanStatus.OVERDUE
            ? state.feeConfig.collateralProtocolPercent
            : state.isUserUnderwater(debtPosition.borrower) ?state.feeConfig.collateralProtocolPercent : state.feeConfig.overdueCollateralProtocolPercent;
```

## L-07 The `msg.value != params.amount` check is redundant

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Deposit.sol#L36

### Description
The `Deposit.validateDeposit` function validates the msg.value as shown below:

        if (msg.value != 0 && (msg.value != params.amount || params.token != address(state.data.weth))) {
            revert Errors.INVALID_MSG_VALUE(msg.value);
        }
 

Here if the `msg.value != 0 && msg.value != params.amount` the transaction will revert.

But in the `Deposit.executeDeposit` function if the `msg.value > 0` then the contract balance is considered as the amount as shown below:

        if (msg.value > 0) { //@audit-info - if the amount is passed in via eth transfer
            // do not trust msg.value (see `Multicall.sol`)
            amount = address(this).balance;

Hence if the user deposits eth into the contract prior calling the deposit function then the msg.value != params.amount becomes a redundant check.

This eth balance is converted into weth and deposited into the Size contract. 

```
   function test_Deposit_deposit_eth_leftovers() public {
        vm.deal(alice, 1 ether);
        vm.deal(address(size), 42 wei);

        assertEq(address(alice).balance, 1 ether);
        assertEq(_state().alice.collateralTokenBalance, 0);

        vm.prank(alice);
        size.deposit{value: 1 ether}(DepositParams({token: address(weth), amount: 1 ether, to: alice}));

        assertEq(address(alice).balance, 0);
        assertEq(_state().alice.collateralTokenBalance, 1 ether + 42 wei);
    }
```

Above test in the `Deposit.t.sol` itself shows irrespective of the params.amount passed in is not considered and the address(this).balance is what is deposited into the Size protocol as collateral.

### Recommended Mitigation Steps
The `msg.value != params.amount` check seems redundant and should be removed

## L-08 Discrepancy between code implementation and Natspec

### Lines of Code
https://github.com/code-423n4/2024-06-size/blob/cba8dc6f9e0cfb4245c111282fc2ee0bd476adf6/src/libraries/AccountingLibrary.sol#L223

### Description
The natspec comment order for the `getCreditAmountIn` method is in the worng order
```
    /// @param cashAmountOut The cash amount out
-    /// @param maxCredit The maximum credit
+    /// @param maxCashAmountOut The maximum cash amount out
-    /// @param maxCredit The maximum cash amount out
+    /// @param maxCredit The maximum credit
    /// @param ratePerTenor The rate per tenor
    /// @param tenor The tenor
    /// @return creditAmountIn The credit amount in
    /// @return fees The fees
    function getCreditAmountIn(
        State storage state,
        uint256 cashAmountOut,
        uint256 maxCashAmountOut,
        uint256 maxCredit,
```

### Recommended Mitigation Steps
update the comments and make sure it is in the right order