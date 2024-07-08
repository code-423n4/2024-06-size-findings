## L1: No partial liquidations are allowed
The size protocol doesn't allow partial liquidations, which could allow huge positions to accrue bad debt.
We understand that the protocol in the first iteration relies on eth and USDC, which are very liquid, and there are enough protocols that offer flashloans.
However, if the protocol in the future will allow other tokens to be used as collateral, that might not be as liquid as ETH and USDC, the protocol could be at risk of accumulating bad debt for large positions, as there is not enough liquidity to fully liquidate the position in one go. (like the case that happend with the CRV token)

## L2: Protocol doesn't differentiate between borrowOffer, and creditSellingOffer, which could force credit sellers into unwanted outcomes
The protocol doesn't differentiate in borrowOffer, between the case where the user wants to sell his credit and when he wants to borrow. both functionalities are handled by the borrow Offer this could lead to cases where the user wants to sell his credit but instead he get'ss forced into a DebtPositionh

Please, Note that some might argue that lenders could use the `openingLimitBorrowCR` to block debts from being create, this is however hard to track as the price of the collateral is not stable could variate  
### Recomendation 

The problem could be completly mitigated by adding a user config, 
```diff
struct User {
    // The user's loan offer
    LoanOffer loanOffer;
    // The user's borrow offer
    BorrowOffer borrowOffer;
    // The user-defined opening limit CR. If not set, the protocol's crOpening is used.
    uint256 openingLimitBorrowCR;
    // Whether the user has disabled all credit positions for sale
    bool allCreditPositionsForSaleDisabled;
+    bool BorrowingDisabled;
}
```


## L3: no caps upwords for apys allows for unrealistic and exagerated rates
The protocol doesn't enforce any upper limit on the apr set in the `yieldCurve` defined in the limit orders set by users in `buyCreditLimit()` and `sellCreditLimit()`.    
This would potentially allow the users to set the apr to unrealistic rates such as >1000%.

## L4: assigned collateral in liquidations is undervalued, because it rounds down instead of up
To caluclate the amount of collateral to seize `assignedCollateral`, in the `executeLiquidate()` function the protocol calls the function `getDebtPositionAssignedCollateral()` which allways rounds down. This could be the correct way to round when calculating the collteralisation ratio of the user, but when calculating the amount of collateral to seize the protocol should round up in the favour of the liquidator and the protocol
This is the line in `

```solidity
        uint256 assignedCollateral = state.getDebtPositionAssignedCollateral(debtPosition);
```
and this is how the function is implemented
```solidity
    function getDebtPositionAssignedCollateral(State storage state, DebtPosition memory debtPosition)
        public
        view
        returns (uint256)
    {
        uint256 debt = state.data.debtToken.balanceOf(debtPosition.borrower);
        uint256 collateral = state.data.collateralToken.balanceOf(debtPosition.borrower);

        if (debt != 0) {
           return Math.mulDivDown(collateral, debtPosition.futureValue, debt);
        } else {
            return 0;
        }
    }
```
=> we recommend to adjust the rounding so it will use Math.mulDivUp() instead when calculating the amount to seize in `executeLiquidate()`

## L5: withdrawing non collateral token will fail if user CR < CR_Liquidation
This could block operation such as leveraging on the protocol, e.g a user wants to borrow more usdc in order to put in more collateral, (for the sake of leveraging the shorting of usdc in the face of eth). The user will want to withdraw the borrowed usdc (max) swap them for eth and then deposit the eth as more collatera.
Because withdrawing ausdc doesn't affect the CR of the user, (the debt in usdc is already measured seperately)

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L159-L163
```solidity

    function withdraw(WithdrawParams calldata params) external payable override(ISize) whenNotPaused {
        state.validateWithdraw(params);
        state.executeWithdraw(params);
        state.validateUserIsNotBelowOpeningLimitBorrowCR(msg.sender);
    }
```



```solidity
   function validateUserIsNotBelowOpeningLimitBorrowCR(State storage state, address account) external view {
        // NOTE check with minCr or the from user set in SetUserConfiguration (max to stay safer if necessary)
        uint256 openingLimitBorrowCR = Math.max(
            state.riskConfig.crOpening,
            state.data.users[account].openingLimitBorrowCR // 0 by default, or user-defined if SetUserConfiguration has been used
        );
        //@audit this will revert if the user is below the minCR even if user is withdawing non collateral token
        if (collateralRatio(state, account) < openingLimitBorrowCR) {
            revert Errors.CR_BELOW_OPENING_LIMIT_BORROW_CR(
                account, collateralRatio(state, account), openingLimitBorrowCR
            );
        }
    }
```

To mitigate this issue we can add a check for the token to withdraw, and only validate the CR if the token to withdraw is the collateral token.
```diff

    function withdraw(WithdrawParams calldata params) external payable override(ISize) whenNotPaused {
        state.validateWithdraw(params);
        state.executeWithdraw(params);
-        state.validateUserIsNotBelowOpeningLimitBorrowCR(msg.sender);
+       if (params.token == address(state.data.collateralToken)) {
+           state.validateUserIsNotBelowOpeningLimitBorrowCR(msg.sender);
+       }
    }
```
