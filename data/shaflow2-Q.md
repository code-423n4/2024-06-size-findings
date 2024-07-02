## [QA-1] The rounding processing of NonTransferableScaledToken and AToken scaling Round calculations is inconsistent, and there may be a small amount of funds remain

## Impact
The scaling and rounding method of NonTransferableScaledToken is different from the aToken of AAVE v3.
This may result in a small number of aTokens in the protocol not being retrieved.

## Proof of Concept
  
NonTransferableScaledToken rounds down when scaling balance
  
github:[https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/token/NonTransferrableScaledToken.sol#L99C9-L99C80](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/token/NonTransferrableScaledToken.sol#L99C9-L99C80)
```solidity
      return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
```
  
AToken rounding calculation when scaling.
  
github:[https://github.com/aave/aave-v3-core/blob/b74526a7bc67a3a117a1963fc871b3eb8cea8435/contracts/protocol/tokenization/AToken.sol#L131](https://github.com/aave/aave-v3-core/blob/b74526a7bc67a3a117a1963fc871b3eb8cea8435/contracts/protocol/tokenization/AToken.sol#L131)
```solidity
      return super.balanceOf(user).rayMul(POOL.getReserveNormalizedIncome(_underlyingAsset));
```
github:[https://github.com/aave/aave-v3-core/blob/b74526a7bc67a3a117a1963fc871b3eb8cea8435/contracts/protocol/libraries/math/WadRayMath.sol#L59](https://github.com/aave/aave-v3-core/blob/b74526a7bc67a3a117a1963fc871b3eb8cea8435/contracts/protocol/libraries/math/WadRayMath.sol#L59)
```solidity
  /**
   * @notice Multiplies two ray, rounding half up to the nearest ray
   * @dev assembly optimized for improved gas savings, see https://twitter.com/transmissions11/status/1451131036377571328
   * @param a Ray
   * @param b Ray
   * @return c = a raymul b
   */
  function rayMul(uint256 a, uint256 b) internal pure returns (uint256 c) {
    // to avoid overflow, a <= (type(uint256).max - HALF_RAY) / b
    assembly {
      if iszero(or(iszero(b), iszero(gt(a, div(sub(not(0), HALF_RAY), b))))) {
        revert(0, 0)
      }

      c := div(add(mul(a, b), HALF_RAY), RAY)
    }
  }
```
POC:
To run the following test function, add it to Withdraw.t.sol and supplement imports
```solidity
    function test_Withdraw_withdraw() public {
        // init LiquidityIndex
        PoolMock(address(variablePool)).setLiquidityIndex(
            address(usdc),
            1075598490984895245242726277
        );
        _deposit(alice, usdc, 12e6);
        UserView memory aliceUser = size.getUserView(alice);
        console2.log("=====After Deposit========");
        console2.log(
            "aliceUser.borrowATokenBalance:  %s",
            aliceUser.borrowATokenBalance
        );
        console2.log(
            "aliceUser.borrowATokenScaledBalance:  %s",
            NonTransferrableScaledToken((size.data().borrowAToken))
                .scaledBalanceOf(alice)
        );
        console2.log(
            "contract ATokenBalance: %s",
            IERC20Metadata(
                variablePool.getReserveData(address(usdc)).aTokenAddress
            ).balanceOf(address(size))
        );

        //LiquidityIndex Increase
        deal(address(usdc), address(variablePool), uint256(12000035));
        PoolMock(address(variablePool)).setLiquidityIndex(
            address(usdc),
            1075601635412505774748769305
        );
        aliceUser = size.getUserView(alice);

        console2.log("=====After LiquidityIndex Increase========");
        console2.log(
            "aliceUser.borrowATokenBalance:  %s",
            aliceUser.borrowATokenBalance
        );
        console2.log(
            "aliceUser.borrowATokenScaledBalance:  %s",
            NonTransferrableScaledToken((size.data().borrowAToken))
                .scaledBalanceOf(alice)
        );

        console2.log(
            "contract ATokenBalance: %s",
            IERC20Metadata(
                variablePool.getReserveData(address(usdc)).aTokenAddress
            ).balanceOf(address(size))
        );
        _withdraw(alice, usdc, type(uint256).max);
        aliceUser = size.getUserView(alice);
        console2.log("=====After LiquidityIndex Increase========");
        console2.log(
            "contract ATokenBalance: %s",
            IERC20Metadata(
                variablePool.getReserveData(address(usdc)).aTokenAddress
            ).balanceOf(address(size))
        );
    }
```
Log:  
```solidity
Running 1 test for test/local/actions/Withdraw.t.sol:WithdrawTest
[PASS] test_Withdraw_withdraw() (gas: 672457)
Logs:
  =====After Deposit========
  aliceUser.borrowATokenBalance:  11999999
  aliceUser.borrowATokenScaledBalance:  11156579
  contract ATokenBalance: 12000000
  =====After LiquidityIndex Increase========
  aliceUser.borrowATokenBalance:  12000034
  aliceUser.borrowATokenScaledBalance:  11156579
  contract ATokenBalance: 12000035
  =====After LiquidityIndex Increase========
  contract ATokenBalance: 1
```

## Tools Used
foundry, Manual audit  
## Recommended Mitigation Steps
Scale rounding operation is consistent with AAVE V3 AToken, rounding


## [QA-2] Comment Error
## impact
This error occurs in the Non Transferable ScaledToken contract.
github:[https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/token/NonTransferrableScaledToken.sol#L97](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/token/NonTransferrableScaledToken.sol#L97)
```solidity
    /// @notice Unscales a scaled amount
    /// @param scaledAmount The scaled amount to unscale
    /// @return The unscaled amount
    /// @dev The unscaled amount is the scaled amount divided by the current liquidity index
    function _unscale(uint256 scaledAmount) internal view returns (uint256) {
        return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
    }
```

_The @dev section of the annotation for the _unscale function incorrectly writes the calculation of "divided"
Actually, it should be multiplication.
## Recommended Mitigation Steps
```solidity
    /// @notice Unscales a scaled amount
    /// @param scaledAmount The scaled amount to unscale
    /// @return The unscaled amount
-    /// @dev The unscaled amount is the scaled amount divided by the current liquidity index
+    /// @dev The unscaled amount is the scaled amount multiplied by the current liquidity index
    function _unscale(uint256 scaledAmount) internal view returns (uint256) {
        return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
    }
```

## [QA-3] Unless Check
## impact
The validateWithdraw function will check if the withdrawal quantity is 0. If it is 0, the withdrawal will be blocked
github:[https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Withdraw.sol#L42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Withdraw.sol#L42)
```solidity
        if (params.amount == 0) {
            revert Errors.NULL_AMOUNT();
        }
```
This check is unnecessary because the function cannot prevent a caller with a balance of 0 from calling the withdraw function  
The reasons are as follows:
github:[https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Withdraw.sol#L55](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Withdraw.sol#L55)
```solidity
    function executeWithdraw(State storage state, WithdrawParams calldata params) public {
        uint256 amount;
        if (params.token == address(state.data.underlyingBorrowToken)) {
            amount = Math.min(params.amount, state.data.borrowAToken.balanceOf(msg.sender));
            if (amount > 0) {
                state.withdrawUnderlyingTokenFromVariablePool(msg.sender, params.to, amount);
            }
        } else {
            amount = Math.min(params.amount, state.data.collateralToken.balanceOf(msg.sender));
            if (amount > 0) {
                state.withdrawUnderlyingCollateralToken(msg.sender, params.to, amount);
            }
        }

        emit Events.Withdraw(params.token, params.to, amount);
    }
```
Users can input any value of amount expect 0.  
After entering the executeWithdraw function, the smaller value between amount and balance will be automatically taken as the amount  
In this way, users with a balance of 0 can also call the withdraw function and trigger the withdraw event, so this check is unnecessary  

POC:
To run the following test function, add it to Withdraw.t.sol
```solidity
    function test_Withdraw_withdraw() public {
        _withdraw(alice, usdc, type(uint256).max);
    }
```
log:
```solidity
[PASS] test_Withdraw_withdraw() (gas: 79198)
Traces:
  [79198] WithdrawTest::test_Withdraw_withdraw()
    ├─ [0] VM::prank(alice: [0x0000000000000000000000000000000000010000])
    │   └─ ← ()
    ├─ [66656] ERC1967Proxy::withdraw((0x704873C9E3ab58125E1166db7f56103b37C730A9, 115792089237316195423570985008687907853269984665640564039457584007913129639935 [1.157e77], 0x0000000000000000000000000000000000010000))
    │   ├─ [61757] SizeMock::withdraw((0x704873C9E3ab58125E1166db7f56103b37C730A9, 115792089237316195423570985008687907853269984665640564039457584007913129639935 [1.157e77], 0x0000000000000000000000000000000000010000)) [delegatecall]
    │   │   ├─ [5062] Withdraw::0433026c(0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000704873c9e3ab58125e1166db7f56103b37c730a9ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff0000000000000000000000000000000000000000000000000000000000010000) [delegatecall]
    │   │   │   └─ ← ()
    │   │   ├─ [16202] Withdraw::b8359e97(0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000704873c9e3ab58125e1166db7f56103b37c730a9ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff0000000000000000000000000000000000000000000000000000000000010000) [delegatecall]
    │   │   │   ├─ [8379] NonTransferrableScaledToken::balanceOf(alice: [0x0000000000000000000000000000000000010000]) [staticcall]
    │   │   │   │   ├─ [2565] PoolMock::getReserveNormalizedIncome(USDC: [0x704873C9E3ab58125E1166db7f56103b37C730A9]) [staticcall]
    │   │   │   │   │   └─ ← 1000000000000000000000000000 [1e27]
    │   │   │   │   └─ ← 0
    │   │   │   ├─ emit Withdraw(token: USDC: [0x704873C9E3ab58125E1166db7f56103b37C730A9], to: alice: [0x0000000000000000000000000000000000010000], amount: 0)
    │   │   │   └─ ← ()
    │   │   ├─ [31234] RiskLibrary::2d77afb6(00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000010000) [delegatecall]
    │   │   │   ├─ [2630] NonTransferrableToken::balanceOf(alice: [0x0000000000000000000000000000000000010000]) [staticcall]
    │   │   │   │   └─ ← 0
    │   │   │   ├─ [2630] NonTransferrableToken::balanceOf(alice: [0x0000000000000000000000000000000000010000]) [staticcall]
    │   │   │   │   └─ ← 0
    │   │   │   ├─ [222] USDC::decimals() [staticcall]
    │   │   │   │   └─ ← 6
    │   │   │   ├─ [2303] PriceFeedMock::getPrice() [staticcall]
    │   │   │   │   └─ ← 1337000000000000000000 [1.337e21]
    │   │   │   └─ ← ()
    │   │   └─ ← ()
    │   └─ ← ()
    └─ ← ()
```
## Recommended Mitigation Steps
Remove unnecessary inspections
```solidity
-        if (params.amount == 0) {
-            revert Errors.NULL_AMOUNT();
-        }
```

## [QA-4] LoanOffer is a lack of means to control the proportion of their own borrowAToken asset loans
## impact
If Alice is a lender and holds a quantity of 100e6 borrowATokens, she creates a LoanOffer order to wait for someone else to take out a loan, but she cannot control how much borrowAToken someone else is borrowing.
github:[https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/OfferLibrary.sol#L8](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/OfferLibrary.sol#L8)
```solidity
struct LoanOffer {
    // The maximum due date of the loan offer
    // Since the yield curve is defined in relative terms, lenders can protect themselves by
    //   setting a maximum timestamp for a loan to be matched
    uint256 maxDueDate;
    // The yield curve in relative terms
    YieldCurve curveRelativeTime;
}
```
  
As long as the order exists and the lender balance is sufficient, the order can be fulfilled infinitely until the lender's borrowAToken is exhausted
  
## Recommended Mitigation Steps
Add a parameter maxLoan to the order to record the maximum loan for each order, allowing the lender to better manage their assets.
```solidity
struct LoanOffer {
    // The maximum due date of the loan offer
    // Since the yield curve is defined in relative terms, lenders can protect themselves by
    //   setting a maximum timestamp for a loan to be matched
    uint256 maxDueDate;

+   uint256 maxLoan;

    // The yield curve in relative terms
    YieldCurve curveRelativeTime;
}
```
## [QA-5] When creating credit positions, they are all available for sale by default

## impact
Whenever a new credit position is created, it can be sold by default, and the default value should be set to false to avoid being sold when the original position is not wanted to be sold.
github:[https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L62)
```solidity
    function createDebtAndCreditPositions(
        State storage state,
        address lender,
        address borrower,
        uint256 futureValue,
        uint256 dueDate
    ) external returns (CreditPosition memory creditPosition) {
        DebtPosition memory debtPosition =
            DebtPosition({borrower: borrower, futureValue: futureValue, dueDate: dueDate, liquidityIndexAtRepayment: 0});

        uint256 debtPositionId = state.data.nextDebtPositionId++;
        state.data.debtPositions[debtPositionId] = debtPosition;

        emit Events.CreateDebtPosition(debtPositionId, borrower, lender, futureValue, dueDate);

        creditPosition = CreditPosition({
            lender: lender,
            credit: debtPosition.futureValue,
            debtPositionId: debtPositionId,
            forSale: true
        });

        uint256 creditPositionId = state.data.nextCreditPositionId++;
        state.data.creditPositions[creditPositionId] = creditPosition;
        state.validateMinimumCreditOpening(creditPosition.credit);
        state.validateTenor(dueDate - block.timestamp);

        emit Events.CreateCreditPosition(creditPositionId, lender, debtPositionId, RESERVED_ID, creditPosition.credit);

        state.data.debtToken.mint(borrower, futureValue);
    }
```
## Recommended Mitigation Steps
```solidity
    function createDebtAndCreditPositions(
        State storage state,
        address lender,
        address borrower,
        uint256 futureValue,
        uint256 dueDate
    ) external returns (CreditPosition memory creditPosition) {
        DebtPosition memory debtPosition =
            DebtPosition({borrower: borrower, futureValue: futureValue, dueDate: dueDate, liquidityIndexAtRepayment: 0});

        uint256 debtPositionId = state.data.nextDebtPositionId++;
        state.data.debtPositions[debtPositionId] = debtPosition;

        emit Events.CreateDebtPosition(debtPositionId, borrower, lender, futureValue, dueDate);

        creditPosition = CreditPosition({
            lender: lender,
            credit: debtPosition.futureValue,
            debtPositionId: debtPositionId,
-            forSale: true
+            forSale: false
        });

        uint256 creditPositionId = state.data.nextCreditPositionId++;
        state.data.creditPositions[creditPositionId] = creditPosition;
        state.validateMinimumCreditOpening(creditPosition.credit);
        state.validateTenor(dueDate - block.timestamp);

        emit Events.CreateCreditPosition(creditPositionId, lender, debtPositionId, RESERVED_ID, creditPosition.credit);

        state.data.debtToken.mint(borrower, futureValue);
    }
```