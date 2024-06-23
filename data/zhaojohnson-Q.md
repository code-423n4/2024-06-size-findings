[L1]Incorrect validate amount in function buyCreditMarket()
In function buyCreditMarket(), 
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