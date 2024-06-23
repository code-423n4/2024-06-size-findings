[L1]Incorrect validate amount in function buyCreditMarket()
In function buyCreditMarket(), we will execute to buy credit. The return value is  the `cashAmountIn`. After that, we will validate whether aave pool has enough liquidity for this amount. Because the borrower may want to withdraw to get USDC. And the withdraw amount should be `cashAmountIn - fees`, not `cashAmountIn`. So it's not correct to check `cashAmountIn`.
Considering one edge case, aave pool owns `cashAmountIn - fees` USDC. It's enough for current borrower to withdraw. However, this operation will be reverted.

```javascript
    function buyCreditMarket(BuyCreditMarketParams calldata params) external payable override(ISize) whenNotPaused {
        state.validateBuyCreditMarket(params);
        uint256 amount = state.executeBuyCreditMarket(params);
        if (params.creditPositionId == RESERVED_ID) {
            state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
        }
        state.validateVariablePoolHasEnoughLiquidity(amount);
    }
    function executeBuyCreditMarket(State storage state, BuyCreditMarketParams memory params)
        external
        returns (uint256 cashAmountIn)
    {
        ......
        state.data.borrowAToken.transferFrom(msg.sender, borrower, cashAmountIn - fees);
        state.data.borrowAToken.transferFrom(msg.sender, state.feeConfig.feeRecipient, fees);
    }
```