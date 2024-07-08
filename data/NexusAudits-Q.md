# QA Report: Size


## Summary:

| Severity | Title | Link |
|----------|-------|------|
| Low | L-01: Lack of Repayment Amount in `Events.Repay` Event Emission | [L-01](#l-01-lack-of-repayment-amount-in-eventsrepay-event-emission) |
| Low | L-02: Lack of Emergency Withdraw Mechanism | [L-02](#l-02-lack-of-emergecy-withdraw-mechanism) |
| Low | L-03: Lack of Existence and Validity Checks for `debtPositionId` | [L-03](#l-03-lack-of-existence-and-validity-checks-for-debtpositionid) |
| Low | L-04: Unrestricted Access to Repayment Function Due to lack of Repayment Delegation Mechanism | [L-04](#l-04-unrestricted-access-to-repayment-function-due-to-lack-of-repayment-delegation-mechanism) |
| Low | L-05: Surround latestRoundData with try/catch blocks to prevent DoS | [L-05](#l-05-surround-latestrounddata-with-trycatch-blocks-to-prevent-dos) |
| Low | L-06: Missing consistency checks to ensure that the base and quote feeds are using the same intermediate asset | [L-06](#l-06-missing-consistency-checks-to-ensure-that-the-base-and-quote-feeds-are-using-the-same-intermediate-asset) |
| Low | L-07: Use ownable2step instead of ownable | [L-07](#l-07-use-ownable2step-instead-of-ownable) |
| Low | L-08: Initialize function can be frontrun | [L-08](#l-08-initialize-function-can-be-frontrun) |
| Low | L-09: Improper Initialization of Base Contracts | [L-09](#l-09-improper-initialization-of-base-contracts) |
| Low | L-10: Bypass of validateBorrowATokenCap during multicall operations could potentially be exploited to exceed intended limits | [L-10](#l-10-bypass-of-validateborrowatokencap-during-multicall-operations-could-potentially-be-exploited-to-exceed-intended-limits) |
| Low | L-11: Year constant will bring issues in Leap years | [L-11](#l-11-year-constant-will-bring-issues-in-leap-years) |
| Low | L-12: Malicious actors can frontrun chainlink price updates | [L-12](#l-12-malicious-actors-can-frontrun-chainlink-price-updates) |
| Low | L-13: No checks to ensure the user has sufficient balance for the withdrawal | [L-13](#l-13-no-checks-to-ensure-the-user-has-sufficient-balance-for-the-withdrawal) |
| Low | L-14: Potential for griefing in user configuration | [L-14](#l-14-potential-for-griefing-in-user-configuration) |
| Low | L-15: No check to ensure new borrow rate is greater than zero | [L-15](#l-15-no-check-to-ensure-new-borrow-rate-is-greater-than-zero) |
| Low | L-16: Add a check to ensure the new borrow rate doesn't exceed a maximum allowed value | [L-16](#l-16-add-a-check-to-ensure-the-new-borrow-rate-doesnt-exceed-a-maximum-allowed-value) |
| Low | L-17: Add a check to ensure the new rate is different from the current rate | [L-17](#l-17-add-a-check-to-ensure-the-new-rate-is-different-from-the-current-rate) |
| Low | L-18: Lack of granular error handling in multicall function | [L-18](#l-18-lack-of-granular-error-handling-in-multicall-function) |
| Low | L-19: Malicious Keeper Bots can exploit the liquidateWithReplacement functionality | [L-19](#l-19-malicious-keeper-bots-can-exploit-the-liquidatewithreplacement-functionality) |
| Low | L-20: Missing Dedicated Batch functions | [L-20](#l-20-missing-dedicated-batch-functions) |
| Low | L-21: Add Timelocks to Sensitive Functions like `setVariablePoolBorrowRate` function | [L-21](#l-21-add-timelocks-to-sensitive-functions-like-setvariablepoolborrowrate-function) |
| Low | L-22: Lack of incentive for early repayment | [L-22](#l-22-lack-of-incentive-for-early-repayment) |
| Low | L-23: validateUserIsNotBelowOpeningLimitBorrowCR check after certain operations could potentially be gamed in multi-step transactions | [L-23](#l-23-validateuserisnotbelowopeninglimitborrowcr-check-after-certain-operations-could-potentially-be-gamed-in-multi-step-transactions) |
| Low | L-24: Heavy reliance on block.timestamp | [L-24](#l-24-heavy-reliance-on-blocktimestamp) |
| Informational | I-01: Naming Clarity | [I-01](#i-01-naming-clarity) |
| Informational | I-02: Incorrect Function Signature | [I-02](#i-02-incorrect-function-signature) |
| Informational | I-03: Add explicit timestamp overflow checks | [I-03](#i-03-add-explicit-timestamp-overflow-checks) |

## Low:


## L-01: Lack of Repayment Amount in `Events.Repay` Event Emission

### Issue:

The problem lies in the last line where the `Events.Repay` event is emitted. Currently, it only includes the `debtPositionId` as an argument:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Repay.sol#L53

```solidity
emit Events.Repay(params.debtPositionId);
```

However, the event emission lacks crucial information about the repayment amount. This omission can make it difficult for external observers or systems to track the exact amount repaid for each debt position.


### Mitigation
To resolve this issue, the `Events.Repay` event should include the repayment amount. The affected line should be modified to include this information. For example:

```solidity
emit Events.Repay(params.debtPositionId, debtPosition.futureValue);
```


## L-02: Lack of Emergecy Withdraw Mechanism

### Issue:

There doesn't appear to be a dedicated emergency withdrawal function in the protocol that would allow users to quickly retrieve their funds in case of a critical vulnerability.

This means that if a severe bug or exploit is discovered, there's no quick way for users to secure their assets before the vulnerability can be exploited. 

In the `Size.sol` contract, we see pause and unpause functions:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L132

```solidity
function pause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) {
    _pause();
}

function unpause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) {
    _unpause();
}
```

These functions allow the protocol to be paused, which can prevent further interactions, but they don't provide a mechanism for users to withdraw their funds.

The `Withdraw.sol` library contains the standard withdrawal function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Withdraw.sol#L52

```solidity
function executeWithdraw(State storage state, WithdrawParams calldata params) external {
    // ... (code omitted for brevity)
    state.data.collateralToken.transferFrom(msg.sender, address(this), params.amount);
    state.data.underlyingCollateralToken.transfer(params.to, params.amount);
    // ... (remaining code omitted)
}
```

However, this function is likely to be subject to the same checks and balances as regular operations, which may not be suitable in an emergency situation.

If a critical vulnerability is discovered, the lack of an emergency withdrawal function means that users' funds could remain at risk for an extended period while a fix is developed and implemented.

Without a quick way to withdraw funds, more users' assets could be affected if an exploit is actively being used.

The inability to quickly secure funds in an emergency could lead to a loss of confidence in the protocol.

### Mitigation:

To address this issue, the protocol should consider implementing an emergency withdrawal function. This function should:

1. Be callable by users directly, not just admins.
2. Bypass most or all of the regular checks and balances.
3. Only be activatable in genuine emergency situations.

Here's an example of how such a function could be implemented:

```solidity
// In Size.sol

bool public emergencyWithdrawalActive;

function activateEmergencyWithdrawal() external onlyRole(DEFAULT_ADMIN_ROLE) {
    emergencyWithdrawalActive = true;
    emit EmergencyWithdrawalActivated();
}

function emergencyWithdraw() external {
    require(emergencyWithdrawalActive, "Emergency withdrawal not active");
    
    uint256 collateralBalance = state.data.collateralToken.balanceOf(msg.sender);
    uint256 borrowBalance = state.data.borrowAToken.balanceOf(msg.sender);
    
    if (collateralBalance > 0) {
        state.data.collateralToken.transferFrom(msg.sender, address(this), collateralBalance);
        state.data.underlyingCollateralToken.transfer(msg.sender, collateralBalance);
    }
    
    if (borrowBalance > 0) {
        state.data.borrowAToken.transferFrom(msg.sender, address(this), borrowBalance);
        state.data.underlyingBorrowToken.transfer(msg.sender, borrowBalance);
    }
    
    emit EmergencyWithdrawal(msg.sender, collateralBalance, borrowBalance);
}
```

This implementation allows the protocol admin to activate emergency withdrawals in case of a critical vulnerability. Once activated, users can call `emergencyWithdraw()` to quickly retrieve their funds, bypassing normal checks.


## L-03: Lack of Existence and Validity Checks for `debtPositionId`

### Issue:

The `validateRepay` function in the Repay library lacks a crucial check for the existence of the `debtPositionId` before proceeding with other validations. This omission can lead to unexpected behavior or errors if a non-existent debt position ID is provided.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Repay.sol#L33

```solidity
function validateRepay(State storage state, RepayParams calldata params) external view {
    // validate debtPositionId
    if (state.getLoanStatus(params.debtPositionId) == LoanStatus.REPAID) {
        revert Errors.LOAN_ALREADY_REPAID(params.debtPositionId);
    }
    // validate msg.sender
    // N/A
}
```


Without an existence check, the function may attempt to validate the status of a non-existent debt position. This could result in undefined behavior, potential security vulnerabilities, or incorrect contract state management.

### Recommendation:
Add an existence check for the `debtPositionId` at the beginning of the `validateRepay` function. This check should precede any other validations to ensure that only valid debt positions are processed.


## L-04: Unrestricted Access to Repayment Function Due to lack of Repayment Delegation Mechanism

### Issue:

Any user can call the `repay` function to repay any debt position, regardless of whether they are the borrower or have been authorized to repay on behalf of the borrower.

In the Size contract:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L198

```solidity
function repay(RepayParams calldata params) external payable override(ISize) whenNotPaused {
    state.validateRepay(params);
    state.executeRepay(params);
}
```

In the Repay library:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Repay.sol#L33

```solidity
function validateRepay(State storage state, RepayParams calldata params) external view {
    // validate debtPositionId
    if (state.getLoanStatus(params.debtPositionId) == LoanStatus.REPAID) {
        revert Errors.LOAN_ALREADY_REPAID(params.debtPositionId);
    }

    // validate msg.sender
    // N/A
}

function executeRepay(State storage state, RepayParams calldata params) external {
    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);

    state.data.borrowAToken.transferFrom(msg.sender, address(this), debtPosition.futureValue);
    debtPosition.liquidityIndexAtRepayment = state.data.borrowAToken.liquidityIndex();
    state.repayDebt(params.debtPositionId, debtPosition.futureValue);

    emit Events.Repay(params.debtPositionId);
}
```


This lack of access control can lead to several problems:

1. Unauthorized Repayments: Anyone can repay someone else's debt without their permission, which could interfere with the borrower's financial strategies or plans.

2. Front-running Attacks: Malicious actors could monitor the mempool for repayment transactions and front-run them, potentially causing the original repayment transaction to fail or be less efficient.

3. Griefing Attacks: An attacker could repeatedly repay small amounts of a victim's loan, causing inconvenience and potentially increased gas costs for the victim.

4. Privacy Issues: The ability for anyone to repay a loan reveals information about when loans are due or when users intend to repay, which could be exploited in various ways.

5. Disruption of Financial Strategies: Borrowers might have specific reasons for timing their repayments (e.g., tax purposes, waiting for better exchange rates). Unrestricted repayments can disrupt these strategies.

### Mitigation:

To mitigate this issue, a repayment delegation mechanism should be implemented. This could involve:

1. Adding an `authorized` field to the `DebtPosition` struct to specify who can repay the loan.
2. Implementing a function for borrowers to delegate repayment rights.
3. Modifying the `validateRepay` function to check for repayment authorization:

```solidity
function validateRepay(State storage state, RepayParams calldata params) external view {
    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);
    if (state.getLoanStatus(params.debtPositionId) == LoanStatus.REPAID) {
        revert Errors.LOAN_ALREADY_REPAID(params.debtPositionId);
    }
    if (msg.sender != debtPosition.borrower && msg.sender != debtPosition.authorized) {
        revert Errors.UNAUTHORIZED_REPAYMENT();
    }
}
```



## L-05: Surround latestRoundData with try/catch blocks to prevent DoS

### Issue:

The `getPrice()` function calls `latestRoundData()` on the sequencer uptime feed and price feeds without using try/catch blocks. This can lead to a Denial of Service vulnerability.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84

```solidity
function getPrice() external view returns (uint256) {
    if (address(sequencerUptimeFeed) != address(0)) {
        // slither-disable-next-line unused-return
        (, int256 answer, uint256 startedAt,,) = sequencerUptimeFeed.latestRoundData();
        // ... checks ...
    }
    // ...
}

function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
    // slither-disable-next-line unused-return
    (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
    // ... checks ...
}
```

**Why it's problematic:**

1. If the `latestRoundData()` call reverts due to network issues, contract errors, etc, it will cause the entire `getPrice()` function to revert.
2. This can lead to a DoS situation where users can't get prices, potentially breaking critical functionality in dependent contracts.

**How it can be exploited:**

1. An attacker could deliberately cause the Chainlink aggregator contracts to revert (e.g., by manipulating gas prices or exploiting vulnerabilities in the aggregator contracts).
2. Network congestion or temporary outages could cause `latestRoundData()` to fail, making the price feed unusable.
3. If the price feed is critical for other contract operations (e.g., liquidations, collateral calculations), those operations would also fail, potentially leading to significant financial losses or system instability.

## Mitigation:

To fix this, we should use try/catch blocks around the `latestRoundData()` calls. Here's how the code could be modified:

```solidity
function getPrice() external view returns (uint256) {
    if (address(sequencerUptimeFeed) != address(0)) {
        try sequencerUptimeFeed.latestRoundData() returns (
            uint80,
            int256 answer,
            uint256 startedAt,
            uint256,
            uint80
        ) {
            if (answer == 1) {
                revert Errors.SEQUENCER_DOWN();
            }
            if (block.timestamp - startedAt <= GRACE_PERIOD_TIME) {
                revert Errors.GRACE_PERIOD_NOT_OVER();
            }
        } catch {
            // Handle error, e.g., revert with a custom error or return a fallback value
            revert Errors.SEQUENCER_DATA_FEED_ERROR();
        }
    }
    
    return Math.mulDivDown(
        _getPrice(base, baseStalePriceInterval),
        10 ** decimals,
        _getPrice(quote, quoteStalePriceInterval)
    );
}

function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
    try aggregator.latestRoundData() returns (
        uint80,
        int256 price,
        uint256,
        uint256 updatedAt,
        uint80
    ) {
        if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
        if (block.timestamp - updatedAt > stalePriceInterval) {
            revert Errors.STALE_PRICE(address(aggregator), updatedAt);
        }
        return SafeCast.toUint256(price);
    } catch {
        // Handle error, e.g., revert with a custom error or return a fallback value
        revert Errors.PRICE_FEED_ERROR(address(aggregator));
    }
}
```

## L-06: Missing consistency checks to ensure that the base and quote feeds are using the same intermediate asset

### Issue:

In the current implementation, there's no explicit check to ensure that both the base and quote price feeds are using the same intermediate asset (both using USD as the quote currency).

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L23

### Impact:

1. Incorrect Price Calculation: If the base and quote feeds use different intermediate assets, the resulting price will be meaningless. For example, if we have:
- Base: ETH/USD
- Quote: USDC/EUR

The calculated "ETH/USDC" price would be incorrect because we're dividing a USD-denominated price by a EUR-denominated price.

2. Silently Wrong Results: The contract would continue to function and return a result, but this result would be fundamentally flawed. Users relying on this price feed could make incorrect financial decisions based on this data.


To mitigate this issue, you could:

1. Add a check in the constructor to verify the description of both feeds (if available through the Chainlink interface) to ensure they use the same quote asset.

2. Implement a whitelist of allowed base/quote feed combinations that are known to be correct.

3. Add a new parameter to the constructor that explicitly states the expected intermediate asset, and use this in error messages to make any mismatch more apparent.


## L-07: Use ownable2step instead of ownable


### Issue:

The problem with using Ownable is that it allows for immediate transfer of ownership, which can be risky in certain scenarios.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/token/NonTransferrableToken.sol#L4

```solidity
import {Ownable} from "@openzeppelin/contracts/access/Ownable.sol";
```


### Impact:

1. Accidental transfer: If the owner accidentally calls `transferOwnership()` with an incorrect address, ownership is immediately lost.
2. Single-step vulnerability: In a multi-signature wallet scenario, if one key is compromised, the attacker can immediately transfer ownership.
3. No grace period: There's no time buffer for the team to react if an unauthorized ownership transfer is initiated.

### Mitigation:
Use Ownable2Step instead of Ownable:

```solidity
import {Ownable2Step} from "@openzeppelin/contracts/access/Ownable2Step.sol";
```

Then, update your contract to inherit from Ownable2Step

## L-08: Initialize function can be frontrun

### Issue:

Initialize function can be vulnerable to front-running attacks. The `initialize` function is external and can be called by anyone who deploys the contract. This leaves it open to front-running attacks, where an attacker could monitor the mempool for the legitimate initialization transaction and submit their own with a higher gas price, effectively taking control of the contract.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L87

```solidity
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

### exploit scenario:

1. The legitimate owner deploys the contract.
2. Before the owner can call `initialize`, the attacker sees the deployment transaction in the mempool.
3. The attacker quickly submits their own `initialize` transaction with a higher gas price.
4. The attacker's transaction gets mined first, allowing them to set themselves as the owner and gain control of all privileged roles.

```solidity
// Attacker's malicious initialization
contract Attacker {
    function attack(address sizeContract) external {
        Size size = Size(sizeContract);
        
        InitializeFeeConfigParams memory f = /* ... */;
        InitializeRiskConfigParams memory r = /* ... */;
        InitializeOracleParams memory o = /* ... */;
        InitializeDataParams memory d = /* ... */;
        
        size.initialize(address(this), f, r, o, d);
        // Now the attacker is the owner and has all privileged roles
    }
}
```

### Mitigation:

Use a factory pattern where the contract is deployed and initialized in a single transaction.


## L-09: Improper Initialization of Base Contracts

### Issue:

There is an issue with how the contract is using the initializer pattern. The issue is that while the contract is inheriting from `Initializable` and other upgradeable contracts, it's not properly using the initializer functions for all the base contracts. Specifically, it's missing the initializer for the `Initializable` base contract.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L96

```solidity
contract Size is ISize, SizeView, Initializable, AccessControlUpgradeable, PausableUpgradeable, UUPSUpgradeable {
    // ... other code ...

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

    // ... other code ...
}
```


This can lead to potential issues:

1. The `initializer` modifier from `Initializable` might not work as expected, potentially allowing multiple initializations.
2. The contract might not be fully initialized, leading to unexpected behavior.


### Mitigation:

To fix this, you should add the `__Initializable_init()` call in the `initialize` function. Here's how you can modify the `initialize` function to properly initialize all base contracts:

```solidity
function initialize(
    address owner,
    InitializeFeeConfigParams calldata f,
    InitializeRiskConfigParams calldata r,
    InitializeOracleParams calldata o,
    InitializeDataParams calldata d
) external initializer {
    state.validateInitialize(owner, f, r, o, d);

    __Initializable_init();
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



This prevents the implementation contract from being initialized, which adds an extra layer of security for upgradeable contracts.

## L-10: Bypass of validateBorrowATokenCap during multicall operations could potentially be exploited to exceed intended limits.

### Issue:

The issue lies in the `Deposit` library, specifically in the `executeDeposit` function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Deposit.sol#L64

```solidity
function executeDeposit(State storage state, DepositParams calldata params) public {
    // ... (code omitted for brevity)

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

    // ... (code omitted for brevity)
}
```

The problematic part is the conditional check that skips the `validateBorrowATokenCap()` call during multicall operations:

```solidity
if (!state.data.isMulticall) {
    state.validateBorrowATokenCap();
}
```

This check is intended to allow users to deposit more tokens to repay debt during multicall operations. However, it creates a vulnerability that could be exploited to bypass the borrow aToken cap.

### Impact:

1. An attacker could create a multicall transaction that includes multiple deposit operations.
2. Since the `validateBorrowATokenCap()` check is skipped during multicall, the attacker could potentially deposit more tokens than the cap allows.
3. This could lead to the total supply of borrow aTokens exceeding the intended cap.

Consider the following exploit:

```solidity
function exploit(State storage state) external {
    bytes[] memory calls = new bytes[](10); // Assuming we want to make 10 deposits

    for (uint256 i = 0; i < 10; i++) {
        DepositParams memory params = DepositParams({
            token: address(state.data.underlyingBorrowToken),
            amount: 1000000e18, // Large amount, potentially exceeding the cap
            to: msg.sender
        });
        
        calls[i] = abi.encodeWithSelector(
            Deposit.executeDeposit.selector,
            params
        );
    }

    // Execute multiple deposits in a single multicall
    Multicall.multicall(state, calls);
}
```

In this exploit, the attacker creates multiple deposit calls, each potentially depositing a large amount. When executed as a multicall, the `validateBorrowATokenCap()` check is bypassed for each individual deposit.

The `Multicall` library does perform a check at the end of the multicall operation:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L40

```solidity
state.validateBorrowATokenIncreaseLteDebtTokenDecrease(
    borrowATokenSupplyBefore, debtTokenSupplyBefore, borrowATokenSupplyAfter, debtTokenSupplyAfter
);
```

However, this check only ensures that the increase in borrow aToken supply is less than or equal to the decrease in debt token supply. It does not enforce the absolute cap on borrow aTokens.


### Mitigation:

To fix this issue, the `validateBorrowATokenCap()` check should be performed after each deposit, regardless of whether it's part of a multicall or not. Alternatively, a more comprehensive check that considers both the cap and the debt token decrease could be implemented at the end of the multicall operation.



## L-11: Year constant will bring issues in Leap years

### Issue:

The protocol uses a constant YEAR that does not accurately represent a year, especially in leap years. This leads to systematic errors in various calculations throughout the contract, potentially resulting in financial discrepancies.


YEAR constant definition:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Math.sol#L9

```solidity
// 1 year in seconds
uint256 constant YEAR = 365 days;
```

Usage in APR calculations:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Math.sol#L40

```solidity
function aprToRatePerTenor(uint256 apr, uint256 tenor) internal pure returns (uint256) {
    return mulDivDown(apr, tenor, YEAR);
}
```

Usage in fee calculations:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L164

```solidity
function getSwapFeePercent(State storage state, uint256 tenor) internal view returns (uint256) {
    return Math.mulDivUp(state.feeConfig.swapFeeAPR, tenor, YEAR);
}
```

### Impact:
1. Inaccurate APR calculations: The `aprToRatePerTenor` function will consistently underestimate rates in leap years.
2. Incorrect fee calculations: Swap fees will be slightly lower than intended in leap years.
3. Cumulative errors: Over multiple years, these errors will compound, leading to potentially significant financial discrepancies.
4. Predictable exploitation: Malicious actors could potentially exploit this predictable inaccuracy in long-term contracts or high-value transactions.

### Exploitation scenario:
A malicious actor could create long-term contracts that mature just after leap years, knowing that the protocol will slightly undervalue the time period. For large sums, this could result in noticeable financial gains at the expense of the protocol or other users.

### Recommended Fix:

Consider using a time oracle for extremely precise time-based calculations in long-term contracts.

## L-12: No check to ensure new borrow rate is greater than zero

### Issue:

The function allows setting the borrow rate to any value, including zero or negative values (though negative values are not possible with uint128).

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L120

```solidity
function setVariablePoolBorrowRate(uint128 borrowRate)
    external
    override(ISizeAdmin)
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    state.oracle.variablePoolBorrowRate = borrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
}
```

### Impact:
1. Setting a zero borrow rate could break the economics of the lending protocol, potentially allowing free borrowing.
2. An extremely low (but non-zero) borrow rate could also disrupt the protocol's economics.
3. It might lead to division-by-zero errors in other parts of the contract that use this rate for calculations.

How it can be exploited:
An attacker with the BORROW_RATE_UPDATER_ROLE could set the borrow rate to zero, potentially allowing themselves or others to borrow funds without accruing interest.

### Mitigation
Add a check to ensure the new borrow rate is greater than zero:

```solidity
function setVariablePoolBorrowRate(uint128 borrowRate)
    external
    override(ISizeAdmin)
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    require(borrowRate > 0, "Borrow rate must be greater than zero");

    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    state.oracle.variablePoolBorrowRate = borrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
}
```


## L-13: Add a check to ensure the new borrow rate doesn't exceed a maximum allowed value

### Issue:
The function allows setting the borrow rate to any value within the range of uint128, which could include extremely high rates.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L120

```solidity
function setVariablePoolBorrowRate(uint128 borrowRate)
    external
    override(ISizeAdmin)
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    state.oracle.variablePoolBorrowRate = borrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
}
```


### Impact:
1. An excessively high borrow rate could make borrowing prohibitively expensive, effectively freezing the lending functionality of the protocol.
2. It could lead to rapid, unexpected increases in user debt, potentially causing mass liquidations.
3. Extremely high rates might cause overflow errors in interest calculations elsewhere in the contract.

### Exploit:
An attacker with the BORROW_RATE_UPDATER_ROLE could set an absurdly high borrow rate, potentially forcing borrowers into default or breaking the protocol's economic model.

### Mitigation
To address this, we should introduce a maximum allowed borrow rate:

```solidity
// At the contract level
uint128 public constant MAX_BORROW_RATE = 1e6; // Example: 100% APR, assuming 8 decimal places

function setVariablePoolBorrowRate(uint128 borrowRate)
    external
    override(ISizeAdmin)
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{

    require(borrowRate <= MAX_BORROW_RATE, "Borrow rate exceeds maximum allowed");

    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    state.oracle.variablePoolBorrowRate = borrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
}
```


## L-14: Add a check to ensure the new rate is different from the current rate

### Issue:
The function doesn't check if the new `borrowRate` is different from the current rate. This allows unnecessary state updates and event emissions, potentially wasting gas and cluttering the event log with redundant entries.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L120

### Impact:
1. Gas waste: An attacker or malfunctioning system could repeatedly call this function with the same rate, causing unnecessary gas consumption for state updates and event emissions.
2. Event spam: The event log could be filled with redundant events, making it harder to track actual rate changes.
3. Misleading data: Frequent calls with the same rate could give the false impression of active management when no real changes are occurring.

### Mitigation:

Here's how we can modify the function to address this issue:

```solidity
function setVariablePoolBorrowRate(uint128 borrowRate)
    external
    override(ISizeAdmin)
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    
    // Check if the new rate is different from the current rate
    require(borrowRate != oldBorrowRate, "New rate must be different from current rate");

    state.oracle.variablePoolBorrowRate = borrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
}
```


## L-15: Malicious actors can frontrun chainlink price updates

### Issue:

The PriceFeed contract is vulnerable to front-running attacks on Chainlink price updates, which could potentially be exploited by malicious actors.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84

```solidity
function getPrice() external view returns (uint256) {
    if (address(sequencerUptimeFeed) != address(0)) {
        // sequencer checks...
    }
    return Math.mulDivDown(
        _getPrice(base, baseStalePriceInterval), 
        10 ** decimals, 
        _getPrice(quote, quoteStalePriceInterval)
    );
}

function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
    (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
    if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
    if (block.timestamp - updatedAt > stalePriceInterval) {
        revert Errors.STALE_PRICE(address(aggregator), updatedAt);
    }
    return SafeCast.toUint256(price);
}
```

### Impact:
This could lead to unfair liquidations or exploited lending/borrowing positions.

### Mitigation

Use a Time-Weighted Average Price (TWAP) over a short period instead of the latest price.


## L-16: No checks to ensure the user has sufficient balance for the withdrawal

### Issue:

There's an issue in the `executeWithdraw` function of the `Withdraw` library. The function does not include explicit checks to ensure the user has sufficient balance for the withdrawal.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Withdraw.sol#L52

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

The issue here is that while the function does use `Math.min` to cap the withdrawal amount to the user's balance, it doesn't explicitly check if the user has sufficient balance before proceeding with the withdrawal. This could potentially lead to problems in the following scenarios:

1. If the `withdrawUnderlyingTokenFromVariablePool` or `withdrawUnderlyingCollateralToken` functions don't have their own balance checks, they might attempt to withdraw more tokens than available.

2. If there's any discrepancy between the balance reported by `balanceOf` and the actual withdrawable balance (e.g., due to pending transactions or internal accounting), it could lead to unexpected behavior.

3. In a multi-threaded or multi-contract environment, there could be race conditions where the balance changes between the check and the actual withdrawal.


### Mitigation:

To mitigate these risks, it would be advisable to add explicit balance checks before proceeding with the withdrawal. Here's an example of how the function could be improved:

```solidity
function executeWithdraw(State storage state, WithdrawParams calldata params) public {
    uint256 userBalance;
    uint256 amount;
    
    if (params.token == address(state.data.underlyingBorrowToken)) {
        userBalance = state.data.borrowAToken.balanceOf(msg.sender);
        require(userBalance >= params.amount, "Insufficient balance");
        amount = params.amount;
        if (amount > 0) {
            state.withdrawUnderlyingTokenFromVariablePool(msg.sender, params.to, amount);
        }
    } else {
        userBalance = state.data.collateralToken.balanceOf(msg.sender);
        require(userBalance >= params.amount, "Insufficient balance");
        amount = params.amount;
        if (amount > 0) {
            state.withdrawUnderlyingCollateralToken(msg.sender, params.to, amount);
        }
    }

    emit Events.Withdraw(params.token, params.to, amount);
}
```

## L-17: Potential for griefing in user configuration

### Issue:

The setUserConfiguration function in the Size contract allows users to change their configuration settings, including the openingLimitBorrowCR, allCreditPositionsForSaleDisabled flag, and the for sale status of specific credit positions.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L254

```solidity
function setUserConfiguration(SetUserConfigurationParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
{
    state.validateSetUserConfiguration(params);
    state.executeSetUserConfiguration(params);
}
```

This function calls the executeSetUserConfiguration function in the SetUserConfiguration library, which updates the user's settings:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SetUserConfiguration.sol#L63

```solidity
function executeSetUserConfiguration(State storage state, SetUserConfigurationParams calldata params) external {
    User storage user = state.data.users[msg.sender];

    user.openingLimitBorrowCR = params.openingLimitBorrowCR;
    user.allCreditPositionsForSaleDisabled = params.allCreditPositionsForSaleDisabled;

    for (uint256 i = 0; i < params.creditPositionIds.length; i++) {
        CreditPosition storage creditPosition = state.getCreditPosition(params.creditPositionIds[i]);
        creditPosition.forSale = params.creditPositionIdsForSale;
        emit Events.UpdateCreditPosition(
            params.creditPositionIds[i], creditPosition.lender, creditPosition.credit, creditPosition.forSale
        );
    }

    emit Events.SetUserConfiguration(
        params.openingLimitBorrowCR,
        params.allCreditPositionsForSaleDisabled,
        params.creditPositionIdsForSale,
        params.creditPositionIds
    );
}
```

The potential for griefing arises from the fact that there are no restrictions on how frequently a user can call this function to change their settings. This could be exploited in the following ways:

1. Frequent updates: A malicious user could constantly update their configuration, emitting many events and potentially causing issues for off-chain systems that monitor these events.

2. Gas costs for other parties: If other parts of the system need to read or react to these configuration changes, frequent updates could cause increased gas costs for other users or the protocol itself.

3. Market manipulation: By frequently changing the forSale status of credit positions, a user could create confusion in the market or manipulate the perceived availability of credit positions.


### Mitigation:

To mitigate this issue, you could consider implementing one or more of the following solutions:

1. Add a cooldown period between configuration changes:

```solidity
mapping(address => uint256) private lastConfigUpdateTime;
uint256 private constant CONFIG_UPDATE_COOLDOWN = 1 hours;

function setUserConfiguration(SetUserConfigurationParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
{
    require(block.timestamp >= lastConfigUpdateTime[msg.sender] + CONFIG_UPDATE_COOLDOWN, "Cooldown period not elapsed");
    state.validateSetUserConfiguration(params);
    state.executeSetUserConfiguration(params);
    lastConfigUpdateTime[msg.sender] = block.timestamp;
}
```

2. Implement a fee for configuration changes:

```solidity
uint256 private constant CONFIG_UPDATE_FEE = 0.01 ether;

function setUserConfiguration(SetUserConfigurationParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
{
    require(msg.value >= CONFIG_UPDATE_FEE, "Insufficient fee");
    state.validateSetUserConfiguration(params);
    state.executeSetUserConfiguration(params);
    // Handle the fee (e.g., transfer to a treasury)
}
```

3. Limit the number of changes per time period:

```solidity
mapping(address => uint256) private configUpdateCount;
mapping(address => uint256) private lastConfigUpdatePeriod;
uint256 private constant CONFIG_UPDATE_LIMIT = 5;
uint256 private constant CONFIG_UPDATE_PERIOD = 1 days;

function setUserConfiguration(SetUserConfigurationParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
{
    uint256 currentPeriod = block.timestamp / CONFIG_UPDATE_PERIOD;
    if (currentPeriod > lastConfigUpdatePeriod[msg.sender]) {
        configUpdateCount[msg.sender] = 0;
        lastConfigUpdatePeriod[msg.sender] = currentPeriod;
    }
    require(configUpdateCount[msg.sender] < CONFIG_UPDATE_LIMIT, "Update limit reached");
    
    state.validateSetUserConfiguration(params);
    state.executeSetUserConfiguration(params);
    
    configUpdateCount[msg.sender]++;
}
```


L-18: Lack of granular error handling in multicall function

### Issue:

The issue is in the Multicall library, specifically in the multicall function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/Multicall.sol#L26

```solidity
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

The problem lies in how errors are handled during the execution of individual calls within the multicall function. Specifically:

1. The function uses a low-level `functionDelegateCall` to execute each call in the batch.
2. If any individual call in the batch reverts, the entire multicall transaction will revert.
3. There's no mechanism to capture or return information about which specific call(s) failed or why.

This lack of granular error handling can cause several problems:

1. If a multicall transaction fails, users won't know which specific operation caused the failure without additional off-chain analysis.

2. A single failing call will cause all other calls in the batch to be reverted, even if they would have succeeded individually.


### Mitigation:

To mitigate this issue, the multicall function could be modified to handle errors for each individual call, perhaps by using a try/catch mechanism or by returning success/failure flags for each call along with any error messages. This would allow for more granular error reporting and potentially partial execution of multicall batches.


L-19: Malicious Keeper Bots can exploit the liquidateWithReplacement functionality

### Issue:

The is potential for malicious or self-interested keeper bots to exploit the `liquidateWithReplacement` function for their own benefit, potentially at the expense of the protocol or other users. 

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L229

```solidity
function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
    onlyRole(KEEPER_ROLE)
    returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
{
    state.validateLiquidateWithReplacement(params);
    uint256 amount;
    (amount, liquidatorProfitCollateralToken, liquidatorProfitBorrowToken) =
        state.executeLiquidateWithReplacement(params);
    // ... additional validations
}
```

The key vulnerability lies in the keeper's ability to control the `params` of the liquidation, particularly:

1. The choice of the replacement borrower (`params.borrower`)
2. The `minimumCollateralProfit`
3. The `minAPR` for the new loan

A malicious keeper bot could exploit this in several ways:

1. **Self-dealing**: The bot could set itself (or an associated address) as the replacement borrower, potentially getting favorable loan terms.

2. **Profit maximization at protocol's expense**: By manipulating the `minimumCollateralProfit`, the bot could extract maximum value for itself, potentially leaving less for the protocol.

3. **Market manipulation**: If the keeper controls a significant portion of liquidations, it could potentially influence market rates by consistently setting specific `minAPR` values.

Here's how the profit is calculated in `LiquidateWithReplacement.sol`:

```solidity
function executeLiquidateWithReplacement(State storage state, LiquidateWithReplacementParams calldata params)
    external
    returns (uint256 issuanceValue, uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
{
    // ...
    liquidatorProfitCollateralToken = state.executeLiquidate(
        LiquidateParams({
            debtPositionId: params.debtPositionId,
            minimumCollateralProfit: params.minimumCollateralProfit
        })
    );

    // ...
    issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
    liquidatorProfitBorrowToken = debtPositionCopy.futureValue - issuanceValue;
    // ...
}
```

A malicious bot could optimize these parameters to maximize its profit, potentially at the expense of the protocol or borrowers.

### Mitigation:

To mitigate these risks:

1. Implement stricter validation on the replacement borrower, perhaps requiring them to be pre-approved or randomly selected from a pool of eligible borrowers.

2. Set tighter bounds on `minimumCollateralProfit` and `minAPR` to prevent extreme values.

3. Implement a profit-sharing mechanism where a significant portion of the liquidation profit goes back to the protocol or is distributed among stakeholders.

4. Rotate keeper roles or implement a multi-keeper system to prevent a single bot from dominating liquidations.

5. Implement monitoring and alerting systems to detect unusual patterns in liquidations that might indicate exploitation.


## L-20: Missing Dedicated Batch functions

### Issue:
In the Size.sol contract, we see individual functions for various operations, but there are no corresponding batch functions for performing multiple operations in a single transaction. This omission can lead to inefficiencies and higher gas costs, especially for users who need to perform multiple actions.

Here are some examples of functions that could benefit from batch versions:

1. Deposit:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L153

```solidity
function deposit(DepositParams calldata params) public payable override(ISize) whenNotPaused {
    state.validateDeposit(params);
    state.executeDeposit(params);
}
```

2. Withdraw:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L159

```solidity
function withdraw(WithdrawParams calldata params) external payable override(ISize) whenNotPaused {
    state.validateWithdraw(params);
    state.executeWithdraw(params);
    state.validateUserIsNotBelowOpeningLimitBorrowCR(msg.sender);
}
```

3. BuyCreditLimit:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L166

```solidity
function buyCreditLimit(BuyCreditLimitParams calldata params) external payable override(ISize) whenNotPaused {
    state.validateBuyCreditLimit(params);
    state.executeBuyCreditLimit(params);
}
```

### Impact:

1. Higher gas costs: Users need to send separate transactions for each operation, paying gas fees for each transaction.
2. Increased network congestion: Multiple separate transactions contribute more to network congestion than a single batched transaction.
3. Potential for inconsistent state: If a user needs to perform multiple related actions, doing them in separate transactions could lead to an inconsistent state if one transaction fails or is front-run.

### Mitigation
To address this issue, batch functions should be implemented. For example, a batch deposit function could look like this:

```solidity
function batchDeposit(DepositParams[] calldata paramsArray) external payable whenNotPaused {
    for (uint i = 0; i < paramsArray.length; i++) {
        state.validateDeposit(paramsArray[i]);
        state.executeDeposit(paramsArray[i]);
    }
}
```

Similar batch functions could be implemented for withdraw, buyCreditLimit, and other relevant operations.

The contract does include a `multicall` function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L142

```solidity
function multicall(bytes[] calldata _data)
    public
    payable
    override(IMulticall)
    whenNotPaused
    returns (bytes[] memory results)
{
    results = state.multicall(_data);
}
```

While this allows for some batching of operations, it's not as gas-efficient or user-friendly as dedicated batch functions would be. The `multicall` function requires encoding function calls into bytes, which can be complex for users and doesn't allow for easy optimization of batch operations.


## L-21: Add Timelocks to Sensitive Functions like `setVariablePoolBorrowRate` function

### Issue:

The `setVariablePoolBorrowRate` function in the Size.sol contract lacks a timelock mechanism, which could potentially be exploited or cause problems.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L120

```solidity
/// @inheritdoc ISizeAdmin
function setVariablePoolBorrowRate(uint128 borrowRate)
    external
    override(ISizeAdmin)
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    state.oracle.variablePoolBorrowRate = borrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
}
```

The issue with this implementation is that it allows immediate changes to the variable pool borrow rate without any delay or warning to users. 

### Impact:

1. Sudden market impact: An immediate change in the borrow rate could cause abrupt shifts in market dynamics, potentially leading to unfair advantages for those who can react quickly.

2. Potential for abuse: If the BORROW_RATE_UPDATER_ROLE is compromised, an attacker could make drastic changes to the borrow rate, disrupting the market and potentially profiting from the chaos.

3. Lack of user preparation: Users don't have time to adjust their positions or strategies in response to rate changes, which could lead to unexpected liquidations or losses.

### Mitigation:
To mitigate these risks, a timelock mechanism should be implemented. Here's an example of how the function could be modified to include a timelock:

```solidity
uint256 public constant BORROW_RATE_TIMELOCK = 1 days;
uint128 public pendingBorrowRate;
uint256 public borrowRateUpdateTime;

function proposeVariablePoolBorrowRate(uint128 newBorrowRate)
    external
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    pendingBorrowRate = newBorrowRate;
    borrowRateUpdateTime = block.timestamp + BORROW_RATE_TIMELOCK;
    emit VariablePoolBorrowRateProposed(newBorrowRate, borrowRateUpdateTime);
}

function executeVariablePoolBorrowRate()
    external
    onlyRole(BORROW_RATE_UPDATER_ROLE)
{
    require(block.timestamp >= borrowRateUpdateTime, "Timelock not expired");
    require(pendingBorrowRate != 0, "No pending borrow rate");

    uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
    state.oracle.variablePoolBorrowRate = pendingBorrowRate;
    state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);

    emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, pendingBorrowRate);

    // Reset pending values
    pendingBorrowRate = 0;
    borrowRateUpdateTime = 0;
}
```

With this implementation:

1. A new borrow rate is proposed and cannot be executed immediately.
2. There's a mandatory waiting period (in this case, 1 day) before the new rate can be applied.
3. Users have time to prepare for the rate change and adjust their positions if necessary.
4. If a malicious rate is proposed, there's time for the community or administrators to respond before it takes effect.

This timelock mechanism significantly reduces the potential for abuse and provides users with more stability and predictability in the protocol's operations. It's a crucial safety feature for such a sensitive parameter that can have wide-reaching effects on the protocol's economy.

## L-22: Lack of incentive for early repayment

### Issue:

There appears to be no mechanism to incentivize early repayment of loans. This could potentially lead to problems in the system.

In the Repay library, we can see the repayment function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Repay.sol#L46

```solidity
function executeRepay(State storage state, RepayParams calldata params) external {
    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);

    state.data.borrowAToken.transferFrom(msg.sender, address(this), debtPosition.futureValue);
    debtPosition.liquidityIndexAtRepayment = state.data.borrowAToken.liquidityIndex();
    state.repayDebt(params.debtPositionId, debtPosition.futureValue);

    emit Events.Repay(params.debtPositionId);
}
```

This function allows for repayment, but it doesn't provide any incentive for early repayment. The borrower pays the full `futureValue` regardless of when they repay.

### Impact:

1. Concentration of risk: As loans approach their due dates, there may be a concentration of outstanding loans, increasing the systemic risk if many borrowers default simultaneously.

2. Reduced capital efficiency: Without incentives for early repayment, capital remains locked in loans for longer periods, potentially reducing overall capital efficiency in the system.

3. Lack of flexibility for borrowers: Borrowers who are able to repay early don't benefit from doing so, which might discourage them from improving their financial position.

4. Potential for last-minute defaults: Borrowers might wait until the last moment to repay, increasing the risk of default due to unforeseen circumstances.

### Mitigation:

The system could implement an early repayment incentive mechanism. Here's an example of how this could be implemented:

```solidity
function executeRepay(State storage state, RepayParams calldata params) external {
    DebtPosition storage debtPosition = state.getDebtPosition(params.debtPositionId);

    uint256 repaymentAmount = calculateEarlyRepaymentAmount(debtPosition);
    state.data.borrowAToken.transferFrom(msg.sender, address(this), repaymentAmount);
    debtPosition.liquidityIndexAtRepayment = state.data.borrowAToken.liquidityIndex();
    state.repayDebt(params.debtPositionId, debtPosition.futureValue);

    emit Events.Repay(params.debtPositionId, repaymentAmount);
}

function calculateEarlyRepaymentAmount(DebtPosition storage debtPosition) internal view returns (uint256) {
    uint256 timeRemaining = debtPosition.dueDate - block.timestamp;
    uint256 fullTerm = debtPosition.dueDate - debtPosition.creationTimestamp;
    uint256 discount = (debtPosition.futureValue - debtPosition.principalAmount) * timeRemaining / fullTerm;
    return debtPosition.futureValue - discount;
}
```

In this example, `calculateEarlyRepaymentAmount` provides a pro-rata discount based on how early the repayment is made. This would incentivize borrowers to repay early when they can, addressing the issues mentioned above.

Implementing such a mechanism would require careful consideration of the economic implications and might need adjustments to other parts of the system, such as how interest is calculated and how credit positions are valued. However, it would provide a more balanced and efficient lending system that encourages responsible borrowing and timely repayment.

## L-23: validateUserIsNotBelowOpeningLimitBorrowCR check after certain operations could potentially be gamed in multi-step transactions

### Issue:
The issue lies in the placement of the `validateUserIsNotBelowOpeningLimitBorrowCR` check in certain functions. This check is performed after the main operation, which could allow users to temporarily enter an invalid state during multi-step transactions. Here are the relevant code snippets:

In the `buyCreditMarket` function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L178

```solidity
function buyCreditMarket(BuyCreditMarketParams calldata params) external payable override(ISize) whenNotPaused {
    state.validateBuyCreditMarket(params);
    uint256 amount = state.executeBuyCreditMarket(params);
    if (params.creditPositionId == RESERVED_ID) {
        state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
    }
    state.validateVariablePoolHasEnoughLiquidity(amount);
}
```

In the `liquidateWithReplacement` function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L229

```solidity
function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
    onlyRole(KEEPER_ROLE)
    returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
{
    state.validateLiquidateWithReplacement(params);
    uint256 amount;
    (amount, liquidatorProfitCollateralToken, liquidatorProfitBorrowToken) =
        state.executeLiquidateWithReplacement(params);
    state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
    state.validateMinimumCollateralProfit(params, liquidatorProfitCollateralToken);
    state.validateVariablePoolHasEnoughLiquidity(amount);
}
```

The potential exploit:

1. A user could execute a `buyCreditMarket` or `liquidateWithReplacement` operation that temporarily puts them below the opening limit borrow CR.
2. Before the transaction is completed, they could perform another action (like depositing collateral) within the same transaction to bring their position back above the required CR.
3. The `validateUserIsNotBelowOpeningLimitBorrowCR` check would then pass, even though the user momentarily entered an invalid state.

This could be exploited in various ways:

1. Users could potentially access larger loans than they should be allowed.
2. It could lead to inconsistent state management, where users can bypass intended risk controls.
3. In complex multi-step transactions, it might allow users to manipulate their positions in ways that weren't intended by the protocol design.

### Mitigation:

To fix this issue, the validation should be performed before the main operation, not after. For example, in `buyCreditMarket`:

```solidity
function buyCreditMarket(BuyCreditMarketParams calldata params) external payable override(ISize) whenNotPaused {
    state.validateBuyCreditMarket(params);
    if (params.creditPositionId == RESERVED_ID) {
        state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
    }
    uint256 amount = state.executeBuyCreditMarket(params);
    state.validateVariablePoolHasEnoughLiquidity(amount);
}
```

Similarly, in `liquidateWithReplacement`, the check should be moved before the execution:

```solidity
function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
    external
    payable
    override(ISize)
    whenNotPaused
    onlyRole(KEEPER_ROLE)
    returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
{
    state.validateLiquidateWithReplacement(params);
    state.validateUserIsNotBelowOpeningLimitBorrowCR(params.borrower);
    uint256 amount;
    (amount, liquidatorProfitCollateralToken, liquidatorProfitBorrowToken) =
        state.executeLiquidateWithReplacement(params);
    state.validateMinimumCollateralProfit(params, liquidatorProfitCollateralToken);
    state.validateVariablePoolHasEnoughLiquidity(amount);
}
```

By moving these checks before the main operations, the contract ensures that users cannot temporarily enter invalid states during multi-step transactions, thus closing this potential loophole.

## L-24: Heavy reliance on block.timestamp

### Issue:
The code relies on block timestamps in a way that could potentially be manipulated by miners, leading to potential vulnerabilities.

In the `validateLiquidateWithReplacement` function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/LiquidateWithReplacement.sol#L47

```solidity
uint256 tenor = debtPosition.dueDate - block.timestamp;
if (tenor < state.riskConfig.minTenor || tenor > state.riskConfig.maxTenor) {
    revert Errors.TENOR_OUT_OF_RANGE(tenor, state.riskConfig.minTenor, state.riskConfig.maxTenor);
}

// ...

if (params.deadline < block.timestamp) {
    revert Errors.PAST_DEADLINE(params.deadline);
}
```

In the `executeLiquidateWithReplacement` function:

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/LiquidateWithReplacement.sol#L129

```solidity
uint256 tenor = debtPositionCopy.dueDate - block.timestamp;
```

The issue here is that these functions rely on `block.timestamp` for critical calculations and validations. Miners have some ability to manipulate the timestamp of a block they're mining (typically by a few seconds). This manipulation could potentially be exploited in the following ways:

1. Tenor Manipulation: An attacker could attempt to manipulate the `block.timestamp` to artificially increase or decrease the calculated `tenor`. This could potentially allow them to bypass the tenor range check or affect the rate calculations.

2. Deadline Bypass: By manipulating the timestamp, an attacker might be able to execute a transaction that should have expired, bypassing the deadline check.

3. Rate Calculation Impact: The `tenor` calculated using `block.timestamp` is used in rate calculations. Manipulating this could lead to more favorable rates for an attacker.

### Mitigation:

To mitigate these risks, consider the following approaches:

1. Use block numbers instead of timestamps for time-sensitive operations. Block numbers are strictly increasing and can't be manipulated by miners.

2. Implement a "grace period" for deadline checks. Instead of checking if the deadline is strictly in the past, check if it's more than a few minutes in the past.

3. For tenor calculations, consider using a trusted oracle or a time source that's less susceptible to manipulation.

Here's an example of how you might modify the code to use block numbers instead of timestamps:

```solidity
// Store the block number at which the debt position was created
uint256 public creationBlockNumber;

// In the constructor or initialization function
creationBlockNumber = block.number;

// In validateLiquidateWithReplacement
uint256 blocksPassed = block.number - creationBlockNumber;
uint256 estimatedTenor = blocksPassed * AVERAGE_BLOCK_TIME; // AVERAGE_BLOCK_TIME should be carefully chosen
if (estimatedTenor < state.riskConfig.minTenor || estimatedTenor > state.riskConfig.maxTenor) {
    revert Errors.TENOR_OUT_OF_RANGE(estimatedTenor, state.riskConfig.minTenor, state.riskConfig.maxTenor);
}

// For deadline checks
if (params.deadlineBlock < block.number) {
    revert Errors.PAST_DEADLINE(params.deadlineBlock);
}
```


## Informational:


## I-01: Naming Clarity

Rename BORROW_RATE_UPDATER_ROLE to BORROW_RATE_MANAGER_ROLE for a clearer understanding of the authority and responsibility associated with this role.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L56

## I-02: Incorrect Function Signature

In `sellCreditMarket`, the `params` argument is passed by memory but should be `calldata` for consistency and gas optimization.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L188

```solidity
   function sellCreditMarket(SellCreditMarketParams calldata params) external payable override(ISize) whenNotPaused {
       state.validateSellCreditMarket(params);
       uint256 amount = state.executeSellCreditMarket(params);
       if (params.creditPositionId == RESERVED_ID) {
           state.validateUserIsNotBelowOpeningLimitBorrowCR(msg.sender);
       }
       state.validateVariablePoolHasEnoughLiquidity(amount);
   }
```

## I-03: Add explicit timestamp overflow checks

In `setVariablePoolBorrowRate`, updating the `variablePoolBorrowRateUpdatedAt` should ensure the timestamp fits within the `uint64` range. Add checks or assertions to ensure no overflows occur.

https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L127

```solidity
   state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
   assert(state.oracle.variablePoolBorrowRateUpdatedAt == block.timestamp);
```
