### [L-0x] Improper BorrowAToken Cap Validation in Multicall

#### Line References
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L29
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L37

#### Impact
The `validateBorrowATokenCap()` in the deposit borrow token flow can be skipped by using the `multicall()` function.

#### Description
Normally, the deposit amount of the `underlyingBorrowToken` is capped by the `state.riskConfig.borrowATokenCap` as shown in the `executeDeposit()` and the `validateBorrowATokenCap()` below:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Deposit.sol#L64-L88
```solidity=64
function executeDeposit(State storage state, DepositParams calldata params) public {
    address from = msg.sender;
    uint256 amount = params.amount;
    if (msg.value > 0) {
        // do not trust msg.value (see `Multicall.sol`)
        amount = address(this).balance;
        // slither-disable-next-line arbitrary-send-eth
        state.data.weth.deposit{value: amount}();
        state.data.weth.forceApprove(address(this), amount);
        from = address(this);
    }

    if (params.token == address(state.data.underlyingBorrowToken)) {
        state.depositUnderlyingBorrowTokenToVariablePool(from, params.to, amount);
        // borrow aToken cap is not validated in multicall,
        //   since users must be able to deposit more tokens to repay debt
        if (!state.data.isMulticall) {
            state.validateBorrowATokenCap();
        }
    } else {
        state.depositUnderlyingCollateralToken(from, params.to, amount);
    }

    emit Events.Deposit(params.token, params.to, amount);
}
```

The `borrowAToken.totalSupply()` is used to compare with the `state.riskConfig.borrowATokenCap` as shown in line 53.
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/CapsLibrary.sol#L53

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/CapsLibrary.sol#L52-L58
```solidity=52
function validateBorrowATokenCap(State storage state) external view {
    if (state.data.borrowAToken.totalSupply() > state.riskConfig.borrowATokenCap) {
        revert Errors.BORROW_ATOKEN_CAP_EXCEEDED(
            state.riskConfig.borrowATokenCap, state.data.borrowAToken.totalSupply()
        );
    }
}
```

However, if the user deposits borrow token with the `multicall()` function, the validation in the `executeDeposit()` function would be skipped, as shown in line 81.
https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Deposit.sol#L81


Furthermore, there is an additional validation called `validateBorrowATokenIncreaseLteDebtTokenDecrease()` at the end of the `multicall()` function call.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L26-L45
```solidity=26
function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
    state.data.isMulticall = true;

    uint256 borrowATokenSupplyBefore = state.data.borrowAToken.balanceOf(address(this));
    uint256 debtTokenSupplyBefore = state.data.debtToken.totalSupply();

    results = new bytes[](data.length);
    for (uint256 i = 0; i < data.length; i++) {
        results[i] = Address.functionDelegateCall(address(this), data[i]);
    }

    uint256 borrowATokenSupplyAfter = state.data.borrowAToken.balanceOf(address(this));
    uint256 debtTokenSupplyAfter = state.data.debtToken.totalSupply();

    state.validateBorrowATokenIncreaseLteDebtTokenDecrease(
        borrowATokenSupplyBefore, debtTokenSupplyBefore, borrowATokenSupplyAfter, debtTokenSupplyAfter
    );

    state.data.isMulticall = false;
}
```

However, the `borrowATokenSupplyBefore`, and the `borrowATokenSupplyAfter` are the `balanceOf(address(this))` not the `totalSupply()` of the `borrowAToken`.

This results in the check in the `validateBorrowATokenIncreaseLteDebtTokenDecrease()` function at line 27 being always `false` and the logic inside this if-else block being skipped. Due to the `borrowATokenSupplyAfter` is the current `borrowAToken` balance of the Size contract, which is 0. 


```solidity=19
function validateBorrowATokenIncreaseLteDebtTokenDecrease(
    State storage state,
    uint256 borrowATokenSupplyBefore,
    uint256 debtTokenSupplyBefore,
    uint256 borrowATokenSupplyAfter,
    uint256 debtTokenSupplyAfter
) external view {
    // If the supply is above the cap
    if (borrowATokenSupplyAfter > state.riskConfig.borrowATokenCap) {
        uint256 borrowATokenSupplyIncrease = borrowATokenSupplyAfter > borrowATokenSupplyBefore
            ? borrowATokenSupplyAfter - borrowATokenSupplyBefore
            : 0;
        uint256 debtATokenSupplyDecrease =
            debtTokenSupplyBefore > debtTokenSupplyAfter ? debtTokenSupplyBefore - debtTokenSupplyAfter : 0;

        // and the supply increase is greater than the debt reduction
        if (borrowATokenSupplyIncrease > debtATokenSupplyDecrease) {
            // revert
            revert Errors.BORROW_ATOKEN_INCREASE_EXCEEDS_DEBT_TOKEN_DECREASE(
                borrowATokenSupplyIncrease, debtATokenSupplyDecrease
            );
        }
        // otherwise, it means the debt reduction was greater than the inflow of cash: do not revert
    }
    // otherwise, the supply is below the cap: do not revert
}
```

#### Recommended Mitigation Steps
Use the `borrowAToken.totalSupply()` instead of `borrowAToken.balanceOf(address(this))` in order to properly validate the `borrowAToken` cap.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L26-L45
```diff=26
function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
    state.data.isMulticall = true;

-   uint256 borrowATokenSupplyBefore = state.data.borrowAToken.balanceOf(address(this));
+   uint256 borrowATokenSupplyBefore = state.data.borrowAToken.totalSupply();
    uint256 debtTokenSupplyBefore = state.data.debtToken.totalSupply();

    results = new bytes[](data.length);
    for (uint256 i = 0; i < data.length; i++) {
        results[i] = Address.functionDelegateCall(address(this), data[i]);
    }

-   uint256 borrowATokenSupplyAfter = state.data.borrowAToken.balanceOf(address(this));
+   uint256 borrowATokenSupplyAfter = state.data.borrowAToken.totalSupply();
    uint256 debtTokenSupplyAfter = state.data.debtToken.totalSupply();

    state.validateBorrowATokenIncreaseLteDebtTokenDecrease(
        borrowATokenSupplyBefore, debtTokenSupplyBefore, borrowATokenSupplyAfter, debtTokenSupplyAfter
    );

    state.data.isMulticall = false;
}
```