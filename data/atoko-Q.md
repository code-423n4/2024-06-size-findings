## Low Risk Findings

| Number | issue                                                                                                   |
| ------ | ------------------------------------------------------------------------------------------------------- |
| [L-01] | Unhandled chainlink revert would lock all price oracle access                                           |
| [L-02] | Pausable Contracts are Non-Compliant with OpenZeppelin                                                  |
| [L-03] | User could deposit and get 0 tokens in return                                                           |
| [L-04] | Chainlink's latestRoundData return stale or incorrect result                                            |
| [L-05] | User cant repay when contract is paused                                                                 |
| [L-06] | Deposit missing slippage protection                                                                     |
| [L-07] | Introduce `LastUpdatedAt` Logic to Handle Oracle Failures by Using the Latest Available Chainlink Price |
| [L-08] | liquidator could end up getting liquidated                                                              |
| [L-09] | Aave aTokens cannot be withdrawn                                                                        |
| [L-10] | Activate the Optimizer                                                                                  |
| [L-11] | PriceFeed does not consider that different price feeds have different staleness thresholds              |
| [L-12] | Liquidator takes collateral with insufficient repayment                                                 |
| [L-13] | No grace period applied which would then allow positions to be liquidated after sequencer goes down     |
| [L-14] | Missing fee collection in withdraw                                                                      |
| [L-15] | Off by one error                                                                                        |
| [L-16] | Critical functions should have a timelock                                                               |
| [L-17] | New Borrow rate should not affect existing loan                                                         |
| [L-18] | No sweep function                                                                                       |
| [L-19] | updates done in a single-step manner are dangerous                                                      |
| [L-20] | Dont allow users to liquidate themselves until they are liquidatable by public                          |
| [L-21] | Small loans will be unprofitable to liquidate                                                           |
| [L-22] | State variables should not be updated more than once in a function                                      |
| [L-23] | Add min deposit to prevent dust deposits                                                                |
| [L-24] | Borrower can front-run a liquidation and pay a small amount                                             |
| [L-25] | Add a donate function incase of bad debt                                                                |
| [L-26] | Sending tokens to `msg.sender` directly will revert incase of blacklists                                |
| [L-27] | Address from parameter can cause issues                                                                 |
| [L-28] | User will loose their yeild from aave after liquidation                                                 |
| [L-29] | key functions with transfer logic could suffer reentrancy                                               |
| [L-30] | Consider a uptime feed on L2 deployments to prevent issues caused by downtime                           |
| [L-31] | Using zero as a parameter                                                                               |
| [L-32] | Initialize missing access control                                                                       |
| [L-33] | payable modifier on a function that shouldn't receive native tokens                                     |
| [L-34] | Missing slippage protection in function claim()                                                         |
| [L-35] | Incorrect Prices Returned During Flash Crashes                                                          |

## [L-01] Unhandled chainlink revert would lock all price oracle access

Feeds cannot be changed after they are configured, Call to `latestRoundData` could potentially revert and make it impossible to query any prices.

Chainlink's multisigs can immediately block access to price feeds at will. Therefore, to prevent denial of service scenarios, it is recommended to query Chainlink price feeds using a defensive approach with Solidity’s try/catch structure. In this way, if the call to the price feed fails, the caller contract is still in control and can handle any errors safely and explicitly.

in openZeppelin blog on dangers to price oracles below

https://blog.openzeppelin.com/secure-smart-contract-guidelines-the-dangers-of-price-oracles

it is stated that

While currently there’s no whitelisting mechanism to allow or disallow contracts from reading prices, powerful multisigs can tighten these access controls. In other words, the multisigs can immediately block access to price feeds at will. Therefore, to prevent denial of service scenarios, it is recommended to query ChainLink price feeds using a defensive approach with Solidity’s try/catch structure. In this way, if the call to the price feed fails, the caller contract is still in control and can handle any errors safely and explicitly.

Below is a snippet of code where the price feed’s `latestRoundData` function is queried. Instead of calling it directly, we surround it with try/catch. In a scenario where the call reverts, the catch block can be used to explicitly revert, call a fallback oracle, or handle the error in any way suitable for the contract’s logic.

```javascript
function getPrice(address priceFeedAddress) external view returns (int256) {
        try AggregatorV3Interface(priceFeedAddress).latestRoundData() returns (
            uint80,         // roundID
            int256 price,   // price
            uint256,        // startedAt
            uint256,        // timestamp
            uint80          // answeredInRound
        ) {
            return price;
        } catch Error(string memory) {
            // handle failure here:
            // revert, call propietary fallback oracle, fetch from another 3rd-party oracle, etc.
        }
    }
```

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L66

https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L86

Recommendation
Surround the call to latestRoundData() with try/catch instead of calling it directly. In a scenario where the call reverts, the catch block can be used to call a fallback oracle or handle the error in any other suitable way.

## [L-02] Pausable Contracts are Non-Compliant with OpenZeppelin

The pausable functionality in `Size` protocol current implementation lacks proper checks in the pause and unPause functions, potentially leading to security vulnerabilities and

If the contract allows pausing even when it is already paused, it may result in unintended consequences such as redundant state changes or disruptions to normal contract operations. This can confuse users and affect the expected behavior of the contract.

Allowing the contract to be unpaused when it is not already paused can introduce security risks by potentially enabling unauthorized actions or changes to the contract state. This could lead to exploitation by malicious actors, resulting in financial losses or other detrimental outcomes.

Failure to enforce proper pausing and unpausing conditions undermines the contract owner's ability to effectively manage and control contract operations. This loss of control may lead to scenarios where critical functions are executed inadvertently or inappropriately.

```javascript


    function pause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) { // @audit missing whenNotPaused
        _pause();
    }


    function unpause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) { // @audit missing whenPaused
        _unpause();
    }
```

As it is the functions lack modifiers to prevent the functions from being called when the contract is paused or unpaused

POC

The OpenZeppelin implementation of pausable contracts includes specific requirements in the comments for the pause and unpause functions:

Pause Function:
According to the comments, the contract must not be paused when calling \_pause.

```javascript
 /**
     * @dev Triggers stopped state.
     *
     * Requirements:
     *
@--->  * - The contract must not be paused.
     */
    function _pause() internal virtual whenNotPaused {
        _paused = true;
        emit Paused(_msgSender());
    }


```

Unpause Function:
As per the comments, the contract must be paused when calling \_unpause.

```javascript
 /**
     * @dev Returns to normal state.
     *
     * Requirements:
     *
     * - The contract must be paused.
     */
    function _unpause() internal virtual whenPaused {
        _paused = false;
        emit Unpaused(_msgSender());
    }
```

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L132

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L137

## Recommendation

the contract should be modified to include proper checks in the pause and unPause functions:

Add Modifier to pause: Incorporate the `whenNotPaused` modifier to ensure that the contract can only be paused when it is not already in the paused state.

```javascript
 function pause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) whenNotPaused {
        _pause();
    }
```

Add Modifier to unPause: Utilize the `whenPaused` modifier to guarantee that the contract can only be unpaused when it is in the paused state.

```javascript
 function unpause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) whenPaused {
        _unpause();
    }
```

## [L-03] User could deposit and get 0 tokens in return

When a user deposits tokens into the protocol, the user could receive 0 tokens in return as there is no check to prevent this from happening, This issue can occur due to the calculations involved in determining the scaled amount of tokens to be minted.

Once a user deposits some amount in the protocol it should always be checked that they user dont get minted 0 tokens in return

```javascript
function depositUnderlyingBorrowTokenToVariablePool(State storage state, address from, address to, uint256 amount)
        external
    {
        state.data.underlyingBorrowToken.safeTransferFrom(from, address(this), amount);

        IAToken aToken =
            IAToken(state.data.variablePool.getReserveData(address(state.data.underlyingBorrowToken)).aTokenAddress);

        uint256 scaledBalanceBefore = aToken.scaledBalanceOf(address(this));

        state.data.underlyingBorrowToken.forceApprove(address(state.data.variablePool), amount);
        state.data.variablePool.supply(address(state.data.underlyingBorrowToken), amount, address(this), 0);

        uint256 scaledAmount = aToken.scaledBalanceOf(address(this)) - scaledBalanceBefore;

        state.data.borrowAToken.mintScaled(to, scaledAmount);
    }
```

Proof of Concept(POC)

The user initiates a deposit transaction with a significant amount of `underlyingBorrowToken` or underlyingCollateralToken.
Due to some state manipulation or calculation errors, the function depositUnderlyingBorrowTokenToVariablePool or depositUnderlyingCollateralToken calculates the scaled amount to mint as 0.
Result:

The user receives 0 borrowAToken or collateral tokens in return.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L49

## Recommendation

To prevent users from depositing tokens and receiving 0 tokens in return, we should implement a check in the `executeDeposit` function. This check will ensure that the scaled amount of tokens to be minted is non-zero before proceeding with the deposit. If the scaled amount is 0, the transaction should revert with an appropriate error message.

```javascript
// Revert if the scaled amount is zero
    if (scaledAmount == 0) {
        revert Errors.ZERO_TOKENS_MINTED();
    }
```

## [L-04] Chainlink's latestRoundData return stale or incorrect result

latestRoundData is used, However, there are no checks on roundID nor timeStamp, resulting in stale prices. According to the chainlink docs

- You must know a valid roundId before consuming historical data.
  @dev A timestamp with zero value means the round is not complete and should not be used.

```javascript
(, int256 answer, uint256 startedAt,,) = sequencerUptimeFeed.latestRoundData();
```

```javascript
(, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
```

This could lead to staleness according to chainlink docs below

https://docs.chain.link/data-feeds/historical-data

Related Finding https://solodit.xyz/issues/m-12-chainlinks-latestrounddata-return-stale-or-incorrect-result-sherlock-blueberry-blueberry-git

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L66

https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L86

## Recommendation

Add checks on the return data with proper revert messages if the price is stale or the round is uncomplete,

## [L-05] User cant repay when contract is paused

The `repay` function currently includes a `whenNotPaused` modifier, which restricts its usage when the protocol is in a paused state. This restriction is intended to ensure contract stability and security by preventing certain operations during critical periods. However, this approach introduces a critical issue regarding debt management and liquidation processes within the protocol.

The problem is that the usage of this function should not be prevented because if users are unable to repay their debts, their accounts can fall into liquidation status while the contract is paused, they will not be able to repay and save their collateral in this case as they are already limited. Once the protocol is unPaused the liquidations will start right away given that users were not able to repay in the paused state they will just be liquidated.

Proof of concept

The current implemetation

```javascript
function repay(RepayParams calldata params) external payable override(ISize) whenNotPaused {
        state.validateRepay(params);
        state.executeRepay(params);
    }
```

The protocol is set to a paused state due to maintenance or emergency conditions.
During this period, users owing debts may not have the opportunity to repay them through the `repay` function.

The contract is unpaused, and liquidations are now enabled to address overdue debts and maintain protocol stability.
Accounts that fell into liquidation status during the paused phase are now liquidatable users get liquidated helplessly.

**\*Instances**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L198

Recommendation

don't interrupt the repayments. Remove the `whenNotPaused` modifier in `repay` function

## [L-06] Deposit missing slippage protection

The current implementation of the deposit functions does not account for slippage, which can result in users receiving fewer tokens than expected. Slippage occurs when there is a difference between the expected price of a token and the actual price at the time the transaction is executed.

Here is the deposit function

```javascript
function deposit(DepositParams calldata params) public payable override(ISize) whenNotPaused {
        state.validateDeposit(params);
        state.executeDeposit(params);
    }
```

it later calls validateDeposit to check everything is as expected before proceeding

```javascript
 function validateDeposit(State storage state, DepositParams calldata params) external view {
        // validate msg.sender
        // N/A

        // validate msg.value
        if (msg.value != 0 && (msg.value != params.amount || params.token != address(state.data.weth))) {
            revert Errors.INVALID_MSG_VALUE(msg.value);
        }

        // validate token
        if (
            params.token != address(state.data.underlyingCollateralToken)
                && params.token != address(state.data.underlyingBorrowToken)
        ) {
            revert Errors.INVALID_TOKEN(params.token);
        }

        // validate amount
        if (params.amount == 0) {
            revert Errors.NULL_AMOUNT();
        }

        // validate to
        if (params.to == address(0)) {
            revert Errors.NULL_ADDRESS();
        }
    }
```

After that executeDeposit is called where it will ensure that user gets `minted` some amount of tokens based on there deposit amount

```javascript
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

at this time if user is not protected against slippage they might end up being minted less tokens than expected.

Proof of Concept (PoC)
A user intends to deposit 100 tokens into the protocol.
The user expects to receive 100 equivalent tokens in return.

Due to market volatility, the price of the underlyingBorrowToken changes between the time the user initiates the transaction and the time it is executed.
As a result, the user receives only 95 tokens instead of the expected 100 tokens.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L153

## Recommendation

Implement slippage protection to prevent users from getting less tokens from what is desired

## [L-07] Introduce LastUpdatedAt Logic to Handle Oracle Failures by Using the Latest Available Chainlink Price

The current implementation of the `liquidate` function calls `collateralRatio` function relies on the latest price provided by the Chainlink oracle. This dependency can lead to transaction reverts if the oracle fails to provide a price. To enhance the robustness of the system, it is recommended to introduce a `lastUpdatedAt` logic. This logic should utilize the latest available price from Chainlink in case the current price fetch fails.

Current Implementation:

```javascript
function collateralRatio(State storage state, address account) public view returns (uint256) {
    uint256 collateral = state.data.collateralToken.balanceOf(account);
    uint256 debt = state.data.debtToken.balanceOf(account);
    uint256 debtWad = Math.amountToWad(debt, state.data.underlyingBorrowToken.decimals());
    uint256 price = state.oracle.priceFeed.getPrice();

    if (debt != 0) {
        return Math.mulDivDown(collateral, price, debtWad);
    } else {
        return type(uint256).max;
    }
}
```

Problem:
If the `priceFeed.getPrice()` call fails due to oracle issues, the function reverts, causing disruptions in the system. This behavior can be problematic, especially during critical operations like liquidations.

## Recommendation

Introduce a `lastUpdatedAt` logic that keeps track of the latest successful price fetch from Chainlink. If the current price fetch fails, the function should fallback to the last recorded price instead of reverting.

## [L-08] liquidator could end up getting liquidated

During the liquidation process in the protocol, there exists a potential vulnerability where a liquidator who initiates a liquidation may unknowingly trigger their own liquidation in the same transaction. This scenario arises when a borrower, acting as a liquidator, especially a lender that has a borrow position attempts to liquidate another borrower's position but fails to ensure their own position remains solvent after the transaction completes.

Borrowers are able to act as liquidators to liquidate other borrower's positions within the Size protocol.
if market conditions shift unfavorably during the transaction or if they end up paying alot in the process, their own collateral ratio may fall below the required threshold, in cases where a borrower attempts to liquidate another borrower but inadvertently causes their own collateral ratio to drop below the liquidation threshold, the Size protocol may trigger the liquidation of their position as well.

during the liquidating process in the `executeLiquidate` there is no check that ensures liquidator does not become liquidatable from the process of liquidation

```javascript

    function executeLiquidate(State storage state, LiquidateParams calldata params)
        external
        returns (uint256 liquidatorProfitCollateralToken)
    {
        DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);
        LoanStatus loanStatus = state.getLoanStatus(params.debtPositionId);
        uint256 collateralRatio = state.collateralRatio(debtPosition.borrower);

        emit Events.Liquidate(params.debtPositionId, params.minimumCollateralProfit, collateralRatio, loanStatus);

        // if the loan is both underwater and overdue, the protocol fee related to underwater liquidations takes precedence
        uint256 collateralProtocolPercent = state.isUserUnderwater(debtPosition.borrower)
            ? state.feeConfig.collateralProtocolPercent
            : state.feeConfig.overdueCollateralProtocolPercent;

        uint256 assignedCollateral = state.getDebtPositionAssignedCollateral(debtPosition);
        uint256 debtInCollateralToken = state.debtTokenAmountToCollateralTokenAmount(debtPosition.futureValue);
        uint256 protocolProfitCollateralToken = 0;

        // profitable liquidation
        if (assignedCollateral > debtInCollateralToken) {
            uint256 liquidatorReward = Math.min(
                assignedCollateral - debtInCollateralToken,
                Math.mulDivUp(debtPosition.futureValue, state.feeConfig.liquidationRewardPercent, PERCENT)
            );
            liquidatorProfitCollateralToken = debtInCollateralToken + liquidatorReward;

            // split the remaining collateral between the protocol and the borrower, capped by the crLiquidation
            uint256 collateralRemainder = assignedCollateral - liquidatorProfitCollateralToken;

            // cap the collateral remainder to the liquidation collateral ratio
            //   otherwise, the split for non-underwater overdue loans could be too much
            uint256 collateralRemainderCap =
                Math.mulDivDown(debtInCollateralToken, state.riskConfig.crLiquidation, PERCENT);

            collateralRemainder = Math.min(collateralRemainder, collateralRemainderCap);

            protocolProfitCollateralToken = Math.mulDivDown(collateralRemainder, collateralProtocolPercent, PERCENT);
        } else {
            // unprofitable liquidation
            liquidatorProfitCollateralToken = assignedCollateral;
        }

        state.data.borrowAToken.transferFrom(msg.sender, address(this), debtPosition.futureValue);
        state.data.collateralToken.transferFrom(debtPosition.borrower, msg.sender, liquidatorProfitCollateralToken);
        state.data.collateralToken.transferFrom(
            debtPosition.borrower, state.feeConfig.feeRecipient, protocolProfitCollateralToken
        );

        debtPosition.liquidityIndexAtRepayment = state.data.borrowAToken.liquidityIndex();
        state.repayDebt(params.debtPositionId, debtPosition.futureValue);
    }

```

## Recommendation

Add checks within the liquidation process to verify the solvency of the borrower acting as a liquidator after the transaction to make sure they are not liquidatable after liquidating another user

## [L-09] Aave aTokens cannot be withdrawn

Currently, the implementation of the withdrawal logic does not distinguish between the initial deposit and the yield earned on that deposit. When users withdraw their funds, they must withdraw their entire balance together with that of aTokens, which includes both the initial deposit and any accrued yield. This limitation prevents users from being able to withdraw only the yield without affecting their initial deposit.

The `executeWithdraw` function currently calculates the amount to be withdrawn based on the user's balance of the specified token, without distinguishing between the initial deposit and the yield.

Users cannot withdraw just the yield they have earned from their deposits on AAVE. They must withdraw their entire balance, including their initial deposit.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Withdraw.sol#L52

## Recommendation

Introduce a mechanism to track the initial deposit separately from the yield earned. This would allow users to withdraw only the yield without touching their principal deposit.

## [L-10] Activate the Optimizer

Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

module.exports = {
solidity: {
version: "0.8.24",
settings: {
optimizer: {
enabled: true,
runs: 1000,
},
},
},
};
Please visit this site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past.

A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## [L-11] PriceFeed does not consider that different price feeds have different staleness thresholds

In the current implementation, the `stalePriceInterval` is passed as a parameter to the `_getPrice` function. While this allows some flexibility, it can lead to unnecessary reverts if the interval is not precisely tuned for each price feed's specific heartbeat. it can lead to inconsistencies and mismatches across different chains.

Different price feeds have different update frequencies (heartbeats), and using a generalized interval can cause valid price updates to be considered stale or, conversely, stale prices to be accepted if the interval is too lenient.

## Recommendation

implement a mapping for `stalePriceInterval` values specific to each price feed, the protocol can ensure more consistent and reliable price validation across different chains.

## [L-12] Liquidator takes collateral with insufficient repayment

if the borrower has multiple debt positions, the liquidator
can take the whole collateral by paying off only the lowest value
debt position, since the calculation is based from the one
position being liquidated, not from the total debt which can be
spread out across multiple positions.

The current implementation allows a liquidator to potentially exploit this by paying off a lower-value debt position and claiming an excessive amount of collateral. This could result in disproportionate collateral allocation, leaving other debt positions under-collateralized and jeopardizing the integrity of the collateral management system.

## Proof of Concept (PoC):

Consider a borrower with two debt positions, both secured by a shared collateral pool of 1000 collateral tokens. The details of the debt positions are as follows:

Debt Position 1: 200 debt tokens
Debt Position 2: 800 debt tokens
Scenario:

The liquidator targets and liquidates Debt Position 1, which has a lower value.
The liquidation logic calculates the liquidator's profit and collateral distribution based solely on Debt Position 1, without considering the total debt of the borrower.
The liquidator pays off 200 debt tokens and, due to the current implementation, could claim an excessive amount of collateral, potentially close to or exceeding the entire collateral pool.
Impact:

The liquidator claims more than the fair share of collateral.
Remaining debt positions are left severely under-collateralized.
Potential systemic risk due to multiple under-collateralized positions.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol#L75

## Recommendation:

To mitigate this vulnerability, the liquidation process must account for the total collateral and total debt across all debt positions held by the borrower. The following changes are recommended:

Calculate Total Debt and Collateral:
Implement functions to calculate the total debt and total collateral of the borrower:

```javascript
// Implement logic to sum up all debt positions of the borrower
function getTotalDebt(address borrower) internal view returns (uint256) {

}

// Implement logic to sum up all collateral securing the borrower's debt positions
function getTotalCollateral(address borrower) internal view returns (uint256) {

}
```

Proportional Collateral Calculation:
Modify the executeLiquidate function to calculate the collateral proportionally to the total debt:

```javascript
function executeLiquidate(State storage state, LiquidateParams calldata params)
    external
    returns (uint256 liquidatorProfitCollateralToken)
{
    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);
    uint256 totalDebt = state.getTotalDebt(debtPosition.borrower);
    uint256 totalCollateral = state.getTotalCollateral(debtPosition.borrower);

    uint256 proportionalCollateral = (totalCollateral * debtPosition.futureValue) / totalDebt;

    // Existing logic to calculate liquidation rewards and collateral distribution
    // using proportionalCollateral instead of assignedCollateral

```

## [L-13] No grace period applied which would then allow positions to be liquidated after sequencer goes down

The active sequencer is well accounted for and checked but there is no grace period implied in the process which means that When the sequencer is down users will not have enough time to repay debt or deposit funds in the protocol. if the sequencer ever goes down and comes back up users wouldn't have enough time to get their positions back afloat since all price updates would be immediately consumed after the sequencer comes back up (note that while the sequencer is down users can't deposit in more collateral), this now causes their positions to be immediately unfairly liquidatable.

## Recommendation

provide a grace period for users if the sequencer ever goes down to keep their positions afloat.

## [L-14] Missing fee collection in withdraw

The current implementation of the withdraw function does not account for the collection of fees. This omission can lead to a loss of revenue for the protocol, as fees are a crucial part of maintaining the protocol's sustainability and incentivizing its operations.

Here is how the executeWithdraw looks at the moment

```javascript
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

## Proof of Concept (PoC)

A user decides to withdraw 100 underlyingBorrowToken from their account.
The protocol expects to collect a fee on the withdrawal.

The executeWithdraw function is called.
The function calculates the amount to withdraw without deducting any fees.
The full 100 tokens are transferred to the user's address.

There is no where the protocol is collecting any fee in the process even thought the state struct has a fee in it

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Withdraw.sol#L52

## Recommendation

The executeWithdraw function should be updated to calculate and deduct the appropriate fee before transferring the remaining amount to the user.

## [L-15] Off by one error

Using the <= operator for time comparisons against block.timestamp can introduce off-by-one errors due to the nature of how block.timestamp is updated only once per block. This can lead to unexpected behavior if the condition is met at the exact second when block.timestamp changes. This issue is especially critical in scenarios where time-sensitive operations are performed, potentially causing operations to revert unexpectedly or execute when they shouldn't.

Proof of Concept (PoC)
Consider the following code snippet:

```javascript
  if (block.timestamp - startedAt <= GRACE_PERIOD_TIME) {
                // time since up
                revert Errors.GRACE_PERIOD_NOT_OVER();
            }
```

In this code, the condition checks if the current block.timestamp is less than or equal to lastUpdateTimestamp. If the condition is true, it returns 0

In Solidity, using >= or <= to compare against block.timestamp (alias now) may introduce off-by-one errors due to the fact that block.timestamp is only updated once per block and its value remains constant throughout the block's execution. If an operation happens at the exact second when block.timestamp changes, it could result in unexpected behavior. To avoid this, it's safer to use strict inequality operators (> or <). For instance, if a condition should only be met after a certain time, use block.timestamp > time rather than block.timestamp >= time. This way, potential off-by-one errors due to the exact timing of block mining are mitigated, leading to safer, more predictable contract behavior.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L73

Recommendation
To avoid potential off-by-one errors, use strict inequality operators (> or <) instead of non-strict ones (>= or <=). This ensures that the condition only triggers after the specified time has completely passed.

```javascript
  if (block.timestamp - startedAt < GRACE_PERIOD_TIME) {
                // time since up
                revert Errors.GRACE_PERIOD_NOT_OVER();
            }
```

## [L-16] Critical functions should have a timelock

Critical functions, such as configuration updates in our protocol, should include a time lock to enhance security and prevent unauthorized or erroneous changes. A time lock introduces a delay period before execution, allowing for review and the opportunity to cancel potentially harmful updates. This mechanism ensures the integrity of the protocol by providing a safeguard against immediate and potentially disruptive changes.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L110

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L120

## [L-17] New Borrow rate should not affect existing loan

In the protocol, lenders have to pay a small borrow rate. The contract `BORROW_RATE_UPDATER_ROLE` can change this poolBorrowRate at any time using the function `setVariablePoolBorrowRate()`.

```javascript
/// @inheritdoc ISizeAdmin
    function setVariablePoolBorrowRate(uint128 borrowRate)
        external
        override(ISizeAdmin)
        onlyRole(BORROW_RATE_UPDATER_ROLE)
    {
        uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate; //@audit--info what if the oracle fails
        state.oracle.variablePoolBorrowRate = borrowRate;
        state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
        emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
    }
```

However, when the `BORROW_RATE_UPDATER_ROLE` changes the rate, the new borrow rate will also be applied to active loans, which is not the agreed-upon term between the lenders and borrowers.

## Recommended Mitigation Steps

Consider storing the `setVariablePoolBorrowRate` in the loan struct. where each borrow rate will be related to some rate, The loan struct is not kept in storage, so the gas cost will not increase significantly.

Alternatively, consider adding a timelock mechanism to prevent the admin from changing the `setVariablePoolBorrowRate`.

## [L-18] No sweep function

the contract has payable functions but no way to sweep. sweep function is necessary to transfer Ether out of the contract to a specific address, typically the owner's or a designated recipient. Without this, the contract lacks flexibility in managing its funds, potentially leading to lost or inaccessible Ether.
A sweep function in a protocol provides an essential mechanism to manage tokens that are mistakenly sent or unexpectedly deposited into contract addresses. This feature allows tokens to be safely retrieved and returned to their rightful owners, thereby preventing potential losses and maintaining the cleanliness of the contract's state. By incorporating a sweep function, protocols can enhance security, improve user experience, and ensure efficient management of token interactions within the system. This proactive approach not only safeguards against unintended token accumulations but also promotes transparency and accountability in token handling processes.

## [L-19] updates done in a single-step manner are dangerous

The update process for configuring protocol addresses or parameters should involve separating the initiation and execution phases of updates. this could be done in many ways ie seperating the logic in two categories In the first step, administrators propose changes to configuration settings, recording these proposals for review. This could involve submitting the proposed changes to a queue or a pending state. In the second step, after a specified period or upon confirmation from designated authorities or conditions, the proposed updates are executed. This approach enhances security and oversight, ensuring that critical configurations are only updated after deliberate review and approval, reducing the risk of unintended or unauthorized changes.

Implementing a two-step procedure for updating protocol addresses adds an extra layer of security. In such a system, the first step initiates the change, and the second step, after a predefined delay, confirms and finalizes it. This delay allows stakeholders or monitoring tools to observe and react to unintended or malicious changes. If an unauthorized change is detected, corrective actions can be taken before the change is finalized. To achieve this, introduce a "proposed address" state variable and a "delay period". Upon an update request, set the "proposed address". After the delay, if not contested, the main protocol address can be updated.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L110

## [L-20] Dont allow users to liquidate themselves until they are liquidatable by public

Borrowers can initiate their own liquidation before their loans are publicly liquidatable. This allows borrowers to become their own liquidators, potentially gaining an unfair advantage by reclaiming collateral or receiving liquidation rewards meant for lenders. To prevent this, it is recommended to enforce stricter controls in the selfLiquidate() function or the liquidation process. By disallowing borrowers from initiating their own liquidation prematurely, the protocol can ensure fairness and prevent borrowers from exploiting the liquidation mechanism for personal gain at the expense of lenders.

**_INstances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L223

## Recommendation

Don't allow users to liquidate their own loans until they are liquidatable by the public.

## [L-21] Small loans will be unprofitable to liquidate

small loans might become unprofitable to liquidate due to the fee structure and calculation methods used. The liquidation process prioritizes profitable scenarios where the assigned collateral significantly exceeds the debt owed, allowing the liquidator to earn a reward from the excess collateral. However, for loans where the debt approaches or exceeds the collateral value, the potential profit for the liquidator diminishes, particularly after accounting for the protocol's liquidation fee and other expenses. This could lead to scenarios where small loans, despite being overdue or underwater, remain unliquidated.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L210

## [L-22] State variables should not be updated more than once in a function

Updating a state variable multiple times within a function can lead to inefficiencies and unintended behaviors. Every state change in Ethereum consumes gas, increasing the transaction cost. Moreover, frequent state changes can introduce vulnerabilities if interim values can be externally observed or acted upon. To address this, one should consolidate updates to occur only once, at the end of the function. This minimizes gas usage and ensures consistent state.

```javascript
function executeDeposit(State storage state, DepositParams calldata params) public {
      >  address from = msg.sender; //here
        uint256 amount = params.amount;
        if (msg.value > 0) {
            // do not trust msg.value (see `Multicall.sol`)
            amount = address(this).balance;
            // slither-disable-next-line arbitrary-send-eth
            state.data.weth.deposit{value: amount}();
            state.data.weth.forceApprove(address(this), amount);
       >     from = address(this); //here
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

## [L-23] Add min deposit to prevent dust deposits

Adding a minimum deposit requirement is crucial to prevent dust deposits that can clutter and complicate the accounting and management of assets within the protocol. Dust deposits, which are extremely small amounts of tokens, can accumulate unnecessarily and add to blockchain bloat, increasing storage costs and potentially causing inefficiencies in transaction processing. By enforcing a minimum deposit amount, protocols ensure that deposited assets meet a practical threshold of economic significance, reducing the overhead associated with managing and accounting for numerous small transactions. This approach also helps maintain clarity and efficiency in user interactions with the protocol, aligning with best practices for blockchain resource management and user experience.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L153

## [L-24] Borrower can front-run a liquidation and pay a small amount

In the current liquidation process, borrowers may attempt to exploit timing advantages by frontrunning transactions with small payments. This practice can potentially undermine the fairness and effectiveness of the liquidation mechanism. To address this concern, it is advisable to incorporate recalculations during the liquidation execution phase. These recalculations ensure that critical parameters such as collateral ratios and debt balances are re-evaluated at the point of liquidation. This approach aims to accurately reflect the borrower's financial position in real-time, reducing the opportunity for borrowers to manipulate transactions to their advantage. This dynamic approach will also ensure that liquidation amounts are adjusted in real-time based on the latest actions; thereby, maintaining operational continuity and fairness among transactions, even in the face of potential frontrunning or high transaction volumes.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L210

## [L-25] Add a donate function incase of bad debt

In our protocol, the current liquidation process allows liquidators to seize a borrower's assets when their borrow credit exceeds their collateral credit, potentially leaving unpaid debt as bad debt permanently. The issue arises because the liquidation function only takes the minimum available for bad debt repayment, which may not cover the entire debt amount if collateral values drop significantly. This can inflate the lending pool's total assets while leaving unresolved bad debt that impacts the protocol's borrow cap checks and risks loss for lenders. To address this, it is crucial to enhance the liquidation mechanism by introducing methods to handle and explicitly manage unpaid bad debt after assets like lending pool shares or wrapped LP tokens are fully seized. Implementing these measures can mitigate risks, ensure fair handling of borrower debts, and maintain the protocol's financial stability.

## Recommendation

Add a way to handle not fully repaid bad debt after liquidation

Add a function to donate to the lending pool to let user supply asset or add a function to socialize the bad debt as loss explicilty.

## [L-26] Sending tokens to `msg.sender` directly will revert incase of blacklists

The liquidation function currently sends tokens directly to the `msg.sender` who is the liquidator incase of blacklisting like USDC that will cause some reverts to happen in the process.

Directly sending tokens to a liquidator during the liquidation process introduces a significant risk of transaction reverts, especially if the recipient address is blacklisted or otherwise restricted. This approach lacks robustness in handling potential security issues and operational risks. Instead allow the liquidator to specify a destination `address _to` for token transfers and not sending the tokens directly to the `msg.sender`, we can mitigate these concerns effectively. This change ensures that tokens are transferred securely to authorized addresses only, aligning with our protocol's security standards and regulatory compliance measures. It also enhances transparency and accountability in the token transfer process, reducing the likelihood of unintended transfers or transaction failures during critical operations like liquidations.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol#L119

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SelfLiquidate.sol#L70

## [L-27] Address from parameter can cause issues

In Solidity, accepting the 'from' address as a public/external function parameter in a non-view/pure function can potentially be misused by an attacker to perform operations on behalf of another user. If not properly checked, an attacker could call the function, pass another user's address as the 'from' parameter, and perform actions like transferring tokens on their behalf. To mitigate this, it's preferable to use 'msg.sender' which refers to the address of the caller of the current function, ensuring that actions are performed by the actual owner of an account or contract. This significantly reduces the risk of unauthorized actions.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L23

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L49

## [L-28] User will loose their yeild from aave after liquidation

In the current liquidate function, there is no mechanism to refund users their rewards accrued from Aave `aTokens`, which poses significant risks, potentially causing losses for affected users. This oversight means that users lose the yield they have accrued in Aave protocols when their positions get liquidated. This issue not only undermines user expectations but also raises fairness concerns, as users' accrued rewards are lost upon liquidation. To mitigate these issues and align with user expectations, it is imperative to introduce a mechanism in the liquidation process that ensures users receive their accrued rewards from Aave before any liquidation proceeds are distributed. This adjustment would enhance user trust, align operational practices with industry standards, and uphold fairness in our protocol's operations.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol#L75

## [L-29] protocol addresses lack adequate protection while updating key functions with transfer logic could suffer reentrancy

The absence of a reentrancy guard in functions, especially where transfer hooks might be present, can expose the protocol users to risks of read-only reentrancies. Such reentrancy vulnerabilities can be exploited to execute malicious actions even without altering the contract state. Without a reentrancy guard, the only potential mitigation would be to blocklist the entire protocol - an extreme and disruptive measure. Therefore, incorporating a reentrancy guard into these functions is vital to bolster security, as it helps protect against both traditional reentrancy attacks and read-only reentrancies, ensuring robust and safe protocol operations.

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol#L204

## [L-30] Consider a uptime feed on L2 deployments to prevent issues caused by downtime

In L2 deployments, incorporating an uptime feed is crucial to mitigate issues arising from sequencer downtime. Downtime can disrupt services, leading to transaction failures or incorrect data readings, affecting overall system reliability. By integrating an uptime feed, you gain insight into the operational status of the L2 network, enabling proactive measures like halting sensitive operations or alerting users. This approach ensures that your contract behaves predictably and securely during network outages, enhancing the robustness and reliability of your decentralized application, which is especially important in mission-critical or high-stakes environments.

## [L-31] Using zero as a parameter

Taking 0 as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because '0' can be interpreted as an uninitialized address, leading to transfers to the '0x0' address, effectively burning tokens. Moreover, 0 as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like 0 appropriately. Use require() statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.

```javascript
state.data.variablePool.supply(
  address(state.data.underlyingBorrowToken),
  amount,
  address(this),
  0
);
```

**_Instances_**

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L60

## [L-32] Initialize missing access control

The initialize function below is missing access control

```javascript
 function initialize(
        address owner,
        InitializeFeeConfigParams calldata f,
        InitializeRiskConfigParams calldata r,
        InitializeOracleParams calldata o,
        InitializeDataParams calldata d
    ) external initializer {
        state.validateInitialize(owner, f, r, o, d);

        __AccessControl_init();
        __Pausable_init();
        __UUPSUpgradeable_init();

        state.executeInitialize(f, r, o, d);
        _grantRole(DEFAULT_ADMIN_ROLE, owner);
        _grantRole(PAUSER_ROLE, owner);
        _grantRole(KEEPER_ROLE, owner);
        _grantRole(BORROW_RATE_UPDATER_ROLE, owner);
    }
```

## [L-33] payable modifier on a function that shouldn't receive native tokens

some functions have a payable modifier despite it doesn't use msg.value, and the contract shouldn't receive native tokens. This may lead to user errors and lost ETH, as it can't be recovered.

```javascript
function claim(ClaimParams calldata params) external payable override(ISize) whenNotPaused {
        state.validateClaim(params);
        state.executeClaim(params);
    }
```

## Recommendation

remove the payable in functions that never user `msg.value`

## [L-34] Missing slippage protection in function claim()

Adding protection would allow users/investors to claim tokens and receive minimumAmount of tokens required by them.

```javascript
struct ClaimParams {
    // The credit position ID to claim
    uint256 creditPositionId;
}
```

```javascript
function claim(ClaimParams calldata params) external payable override(ISize) whenNotPaused {
        state.validateClaim(params);
        state.executeClaim(params);
    }
```

## [L-35] Incorrect Prices Returned During Flash Crashes

Chainlink price feeds have in-built minimum & maximum prices they will return; if during a flash crash, bridge compromise, or depegging event, an asset’s value falls below the price feed’s minimum price, the oracle price feed will continue to report the (now incorrect) minimum price.

The current implementation of the `_getPrice` function lacks checks for minimum and maximum price values returned during flash crashes. Without these checks, the function may return erroneous values or be vulnerable to manipulation during extreme market conditions.

Proof of Concept (PoC):
Consider the following scenario:

A flash crash occurs, causing extreme price volatility in the asset.
The Chainlink price feed returns an unexpected inbuilt value due to the crash, potentially resulting in erroneous asset valuations.
Without checks for minimum and maximum answer values, the function may accept and use this erroneous data, leading to incorrect calculations or vulnerability to manipulation.
Recommendation:
To enhance the robustness of the function and protect against flash crashes, we should implement checks for minimum and maximum answer values returned by the Chainlink price feed. Here's how we can do it:

```javascript


    // Check for minimum and maximum answer values
    if (price < MIN_ANSWER || price > MAX_ANSWER) {
        revert NoyaChainlinkOracle_INVALID_ANSWER();
    }
```
