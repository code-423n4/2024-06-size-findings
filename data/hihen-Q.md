# QA Report

## Summary

### Low Issues

Total **133 instances** over **18 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[L-01]](#l-01-missing-storage-gap-for-upgradeable-contracts) | Missing storage gap for upgradeable contracts | 1 |
| [[L-02]](#l-02-a-year-is-not-always-365-days) | A year is not always 365 days | 1 |
| [[L-03]](#l-03-initializers-can-be-front-run) | Initializers can be front-run | 1 |
| [[L-04]](#l-04-library-function-isnt-internal-or-private) | Library function isn't `internal` or `private` | 62 |
| [[L-05]](#l-05-downcasting-other-types-to-an-address-can-cause-collisions) | Downcasting other types to an address can cause collisions | 2 |
| [[L-06]](#l-06-upgradeable-contract-not-initialized) | Upgradeable contract not initialized | 1 |
| [[L-07]](#l-07-use-ownable2step-instead-of-ownable) | Use Ownable2Step instead of Ownable | 2 |
| [[L-08]](#l-08-constructor--initialization-function-lacks-parameter-validation) | Constructor / initialization function lacks parameter validation | 8 |
| [[L-09]](#l-09-missing-checks-for-address0-when-setting-address-state-variables) | Missing checks for `address(0)` when setting address state variables | 1 |
| [[L-10]](#l-10-owner-can-renounce-ownership) | Owner can renounce Ownership | 2 |
| [[L-11]](#l-11-functions-calling-contractsaddresses-with-transfer-hooks-should-be-protected-by-reentrancy-guard) | Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard | 16 |
| [[L-12]](#l-12-critical-functions-should-be-controlled-by-time-locks) | Critical functions should be controlled by time locks | 1 |
| [[L-13]](#l-13-external-function-calls-within-loops) | External function calls within loops | 1 |
| [[L-14]](#l-14-tokens-may-be-minted-to-the-zero-address) | Tokens may be minted to the zero address | 8 |
| [[L-15]](#l-15-use-safecast-to-downcast-safely) | Use `SafeCast` to downcast safely | 1 |
| [[L-16]](#l-16-loss-of-precision-in-divisions) | Loss of precision in divisions | 21 |
| [[L-17]](#l-17-function-name-is-not-a-part-of-the-erc-20-standard) | Function `name()` is not a part of the ERC-20 standard | 3 |
| [[L-18]](#l-18-code-does-not-follow-the-best-practice-of-check-effects-interaction) | Code does not follow the best practice of check-effects-interaction | 1 |

### Non Critical Issues

Total **388 instances** over **51 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[N-01]](#n-01-custom-errors-has-no-error-details) | Custom errors has no error details | 13 |
| [[N-02]](#n-02-there-is-no-need-to-initialize-variables-with-0) | There is no need to initialize variables with 0 | 6 |
| [[N-03]](#n-03-names-of-constants-should-use-the-upper_case_with_underscores-style) | Names of constants should use the UPPER_CASE_WITH_UNDERSCORES style | 1 |
| [[N-04]](#n-04-names-of-privateinternal-functions-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` functions should be prefixed with an underscore | 37 |
| [[N-05]](#n-05-names-of-privateinternal-state-variables-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` state variables should be prefixed with an underscore | 5 |
| [[N-06]](#n-06-redundant-inheritance-specifier) | Redundant inheritance specifier | 1 |
| [[N-07]](#n-07-use-of-override-is-unnecessary) | Use of `override` is unnecessary | 28 |
| [[N-08]](#n-08-unused-errors) | Unused errors | 2 |
| [[N-09]](#n-09-large-numeric-literals-should-use-underscores-for-readability) | Large numeric literals should use underscores for readability | 1 |
| [[N-10]](#n-10-add-inline-comments-for-unnamed-parameters) | Add inline comments for unnamed parameters | 7 |
| [[N-11]](#n-11-consider-splitting-complex-checks-into-multiple-steps) | Consider splitting complex checks into multiple steps | 2 |
| [[N-12]](#n-12-consider-moving-msgsender-checks-to-modifiers) | Consider moving `msg.sender` checks to `modifier`s | 4 |
| [[N-13]](#n-13-complex-math-should-be-split-into-multiple-steps) | Complex math should be split into multiple steps | 3 |
| [[N-14]](#n-14-consider-adding-a-blockdeny-list) | Consider adding a block/deny-list | 1 |
| [[N-15]](#n-15-do-not-use-literal-numbers-for-array-indexes) | Do not use literal numbers for array indexes | 4 |
| [[N-16]](#n-16-consider-emitting-an-event-at-the-end-of-the-constructor) | Consider emitting an event at the end of the constructor | 4 |
| [[N-17]](#n-17-empty-function-body-without-comments) | Empty function body without comments | 1 |
| [[N-18]](#n-18-events-are-emitted-without-the-sender-information) | Events are emitted without the sender information | 23 |
| [[N-19]](#n-19-event-is-missing-indexed-fields) | Event is missing `indexed` fields | 2 |
| [[N-20]](#n-20-function-state-mutability-can-be-restricted-to-pure) | Function state mutability can be restricted to pure | 1 |
| [[N-21]](#n-21-imports-could-be-organized-more-systematically) | Imports could be organized more systematically | 8 |
| [[N-22]](#n-22-lib-solady-should-be-upgraded-to-a-newer-version) | Lib solady should be upgraded to a newer version | 1 |
| [[N-23]](#n-23-lines-are-too-long) | Lines are too long | 22 |
| [[N-24]](#n-24-magic-numbers-should-be-replaced-with-constants) | Magic numbers should be replaced with constants | 7 |
| [[N-25]](#n-25-expressions-for-constant-values-should-use-immutable-rather-than-constant) | Expressions for constant values should use `immutable` rather than `constant` | 4 |
| [[N-26]](#n-26-functions-not-used-internally-could-be-marked-external) | Functions not used internally could be marked external | 12 |
| [[N-27]](#n-27-functions-should-be-named-in-mixedcase-style) | Functions should be named in mixedCase style | 9 |
| [[N-28]](#n-28-variable-names-for-immutables-should-use-upper_case_with_underscores) | Variable names for `immutable`s should use UPPER_CASE_WITH_UNDERSCORES | 8 |
| [[N-29]](#n-29-constants-should-be-put-on-the-left-side-of-comparisons) | Constants should be put on the left side of comparisons | 62 |
| [[N-30]](#n-30-else-block-not-required) | `else`-block not required | 12 |
| [[N-31]](#n-31-high-cyclomatic-complexity) | High cyclomatic complexity | 1 |
| [[N-32]](#n-32-typos) | Typos | 2 |
| [[N-33]](#n-33-unnecessary-casting) | Unnecessary casting | 11 |
| [[N-34]](#n-34-unused-function-parameters) | Unused function parameters | 1 |
| [[N-35]](#n-35-use-delete-instead-of-assigning-values-to-false) | Use delete instead of assigning values to `false` | 1 |
| [[N-36]](#n-36-consider-using-delete-rather-than-assigning-zero-to-clear-values) | Consider using `delete` rather than assigning zero to clear values | 2 |
| [[N-37]](#n-37-use-the-latest-solidity-version) | Use the latest Solidity version | 34 |
| [[N-38]](#n-38-using-underscore-at-the-end-of-variable-name) | Using underscore at the end of variable name | 10 |
| [[N-39]](#n-39-use-a-struct-to-encapsulate-multiple-function-parameters) | Use a struct to encapsulate multiple function parameters | 3 |
| [[N-40]](#n-40-returning-a-struct-instead-of-a-bunch-of-variables-is-better) | Returning a struct instead of a bunch of variables is better | 2 |
| [[N-41]](#n-41-events-that-mark-critical-parameter-changes-should-contain-both-the-old-and-the-new-value) | Events that mark critical parameter changes should contain both the old and the new value | 1 |
| [[N-42]](#n-42-contract-variables-should-have-comments) | Contract variables should have comments | 5 |
| [[N-43]](#n-43-dont-define-functions-with-the-same-name-in-a-contract) | Don't define functions with the same name in a contract | 3 |
| [[N-44]](#n-44-interface-files-should-not-use-fixed-compiler-versions) | Interface files should not use fixed compiler versions | 2 |
| [[N-45]](#n-45-too-long-functions-should-be-refactored) | Too long functions should be refactored | 2 |
| [[N-46]](#n-46-missing-event-for-critical-changes) | Missing event for critical changes | 2 |
| [[N-47]](#n-47-consider-adding-emergency-stop-functionality) | Consider adding emergency-stop functionality | 3 |
| [[N-48]](#n-48-use-the-modern-upgradeable-contract-paradigm) | Use the Modern Upgradeable Contract Paradigm | 3 |
| [[N-49]](#n-49-large-or-complicated-code-bases-should-implement-invariant-tests) | Large or complicated code bases should implement invariant tests | 1 |
| [[N-50]](#n-50-the-default-value-is-manually-set-when-it-is-declared) | The default value is manually set when it is declared | 5 |
| [[N-51]](#n-51-contracts-should-have-all-publicexternal-functions-exposed-by-interfaces) | Contracts should have all `public`/`external` functions exposed by `interface`s | 3 |

## Low Issues

### [L-01] Missing storage gap for upgradeable contracts

Each upgradeable contract (even for most derived) should include a state variable of type fixed-size array (usually named [`__gap`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/f4b309ae37dc650efa7523dd1230d230e3f452d7/contracts/token/ERC20/ERC20Upgradeable.sol#L383) and placed after all other variables) to provide reserved space in storage.
It allows the team to freely add new state variables in the future upgrades without compromising the storage compatibility with existing deployments.

There is 1 instance:

- *Size.sol* ( [62-62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L62-L62) ):

```solidity
62: contract Size is ISize, SizeView, Initializable, AccessControlUpgradeable, PausableUpgradeable, UUPSUpgradeable {
```

### [L-02] A year is not always 365 days

A year is not always 365 days or any fixed days or seconds. On leap years, the number of days is 366, so calculations during those years will return the wrong value.

There is 1 instance:

- *Math.sol* ( [9-9](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L9-L9) ):

```solidity
9: uint256 constant YEAR = 365 days;
```

### [L-03] Initializers can be front-run

Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment.

There is 1 instance:

- *Size.sol* ( [87](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L87) ):

```solidity
87:     function initialize(
```

### [L-04] Library function isn't `internal` or `private`

In a library, using an external or public visibility means that we won't be going through the library with a DELEGATECALL but with a CALL. This changes the context and should be done carefully.

<details>
<summary>There are 62 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L42), [62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L62), [103](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L103), [137](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L137) ):

```solidity
42:     function repayDebt(State storage state, uint256 debtPositionId, uint256 repayAmount) public {

62:     function createDebtAndCreditPositions(

103:     function createCreditPosition(State storage state, uint256 exitCreditPositionId, address lender, uint256 credit)

137:     function reduceCredit(State storage state, uint256 creditPositionId, uint256 amount) public {
```

- *CapsLibrary.sol* ( [19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L19), [52](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L52), [67](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L67) ):

```solidity
19:     function validateBorrowATokenIncreaseLteDebtTokenDecrease(

52:     function validateBorrowATokenCap(State storage state) external view {

67:     function validateVariablePoolHasEnoughLiquidity(State storage state, uint256 amount) public view {
```

- *DepositTokenLibrary.sol* ( [23](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L23), [34](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L34), [49](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L49), [74](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L74) ):

```solidity
23:     function depositUnderlyingCollateralToken(State storage state, address from, address to, uint256 amount) external {

34:     function withdrawUnderlyingCollateralToken(State storage state, address from, address to, uint256 amount)

49:     function depositUnderlyingBorrowTokenToVariablePool(State storage state, address from, address to, uint256 amount)

74:     function withdrawUnderlyingTokenFromVariablePool(State storage state, address from, address to, uint256 amount)
```

- *LoanLibrary.sol* ( [80](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L80), [93](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L93), [109](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L109), [122](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L122), [148](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L148), [170](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L170) ):

```solidity
80:     function getDebtPosition(State storage state, uint256 debtPositionId) public view returns (DebtPosition storage) {

93:     function getCreditPosition(State storage state, uint256 creditPositionId)

109:     function getDebtPositionByCreditPositionId(State storage state, uint256 creditPositionId)

122:     function getLoanStatus(State storage state, uint256 positionId) public view returns (LoanStatus) {

148:     function getDebtPositionAssignedCollateral(State storage state, DebtPosition memory debtPosition)

170:     function getCreditPositionProRataAssignedCollateral(State storage state, CreditPosition memory creditPosition)
```

- *RiskLibrary.sol* ( [21](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L21), [31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L31), [41](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L41), [53](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L53), [71](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L71), [104](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L104), [121](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L121), [129](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L129), [140](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L140) ):

```solidity
21:     function validateMinimumCredit(State storage state, uint256 credit) public view {

31:     function validateMinimumCreditOpening(State storage state, uint256 credit) public view {

41:     function validateTenor(State storage state, uint256 tenor) public view {

53:     function collateralRatio(State storage state, address account) public view returns (uint256) {

71:     function isCreditPositionSelfLiquidatable(State storage state, uint256 creditPositionId)

104:     function isDebtPositionLiquidatable(State storage state, uint256 debtPositionId) public view returns (bool) {

121:     function isUserUnderwater(State storage state, address account) public view returns (bool) {

129:     function validateUserIsNotUnderwater(State storage state, address account) external view {

140:     function validateUserIsNotBelowOpeningLimitBorrowCR(State storage state, address account) external view {
```

- *YieldCurveLibrary.sol* ( [115](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L115) ):

```solidity
115:     function getAPR(YieldCurve memory curveRelativeTime, VariablePoolBorrowRateParams memory params, uint256 tenor)
```

- *BuyCreditLimit.sol* ( [29](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditLimit.sol#L29), [57](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditLimit.sol#L57) ):

```solidity
29:     function validateBuyCreditLimit(State storage state, BuyCreditLimitParams calldata params) external view {

57:     function executeBuyCreditLimit(State storage state, BuyCreditLimitParams calldata params) external {
```

- *BuyCreditMarket.sol* ( [51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L51), [121](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L121) ):

```solidity
51:     function validateBuyCreditMarket(State storage state, BuyCreditMarketParams calldata params) external view {

121:     function executeBuyCreditMarket(State storage state, BuyCreditMarketParams memory params)
```

- *Claim.sol* ( [31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L31), [48](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L48) ):

```solidity
31:     function validateClaim(State storage state, ClaimParams calldata params) external view {

48:     function executeClaim(State storage state, ClaimParams calldata params) external {
```

- *Compensate.sol* ( [42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L42), [106](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L106) ):

```solidity
42:     function validateCompensate(State storage state, CompensateParams calldata params) external view {

106:     function executeCompensate(State storage state, CompensateParams calldata params) external {
```

- *Deposit.sol* ( [36](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L36), [64](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L64) ):

```solidity
36:     function validateDeposit(State storage state, DepositParams calldata params) external view {

64:     function executeDeposit(State storage state, DepositParams calldata params) public {
```

- *Initialize.sol* ( [175](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L175), [267](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L267) ):

```solidity
175:     function validateInitialize(

267:     function executeInitialize(
```

- *Liquidate.sol* ( [37](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L37), [59](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L59), [75](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L75) ):

```solidity
37:     function validateLiquidate(State storage state, LiquidateParams calldata params) external view {

59:     function validateMinimumCollateralProfit(

75:     function executeLiquidate(State storage state, LiquidateParams calldata params)
```

- *LiquidateWithReplacement.sol* ( [47](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L47), [99](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L99), [120](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L120) ):

```solidity
47:     function validateLiquidateWithReplacement(State storage state, LiquidateWithReplacementParams calldata params)

99:     function validateMinimumCollateralProfit(

120:     function executeLiquidateWithReplacement(State storage state, LiquidateWithReplacementParams calldata params)
```

- *Repay.sol* ( [33](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Repay.sol#L33), [46](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Repay.sol#L46) ):

```solidity
33:     function validateRepay(State storage state, RepayParams calldata params) external view {

46:     function executeRepay(State storage state, RepayParams calldata params) external {
```

- *SelfLiquidate.sol* ( [34](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L34), [59](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L59) ):

```solidity
34:     function validateSelfLiquidate(State storage state, SelfLiquidateParams calldata params) external view {

59:     function executeSelfLiquidate(State storage state, SelfLiquidateParams calldata params) external {
```

- *SellCreditLimit.sol* ( [25](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditLimit.sol#L25), [44](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditLimit.sol#L44) ):

```solidity
25:     function validateSellCreditLimit(State storage state, SellCreditLimitParams calldata params) external view {

44:     function executeSellCreditLimit(State storage state, SellCreditLimitParams calldata params) external {
```

- *SellCreditMarket.sol* ( [51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L51), [127](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L127) ):

```solidity
51:     function validateSellCreditMarket(State storage state, SellCreditMarketParams calldata params) external view {

127:     function executeSellCreditMarket(State storage state, SellCreditMarketParams calldata params)
```

- *SetUserConfiguration.sol* ( [31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L31), [63](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L63) ):

```solidity
31:     function validateSetUserConfiguration(State storage state, SetUserConfigurationParams calldata params)

63:     function executeSetUserConfiguration(State storage state, SetUserConfigurationParams calldata params) external {
```

- *UpdateConfig.sol* ( [42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L42), [56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L56), [70](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L70), [79](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L79), [86](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L86) ):

```solidity
42:     function feeConfigParams(State storage state) public view returns (InitializeFeeConfigParams memory) {

56:     function riskConfigParams(State storage state) public view returns (InitializeRiskConfigParams memory) {

70:     function oracleParams(State storage state) public view returns (InitializeOracleParams memory) {

79:     function validateUpdateConfig(State storage, UpdateConfigParams calldata) external pure {

86:     function executeUpdateConfig(State storage state, UpdateConfigParams calldata params) external {
```

- *Withdraw.sol* ( [29](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L29), [52](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L52) ):

```solidity
29:     function validateWithdraw(State storage state, WithdrawParams calldata params) external view {

52:     function executeWithdraw(State storage state, WithdrawParams calldata params) public {
```

</details>

### [L-05] Downcasting other types to an address can cause collisions

Downcasting other types to an address will truncates the upper bytes, which means that multiple values can be mapped to an address, i.e. address collisions can occur.

There are 2 instances:

- *UpdateConfig.sol* ( [134-134](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L134-L134), [136-136](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L136-L136) ):

```solidity
134:             state.feeConfig.feeRecipient = address(uint160(params.value));

136:             state.oracle.priceFeed = IPriceFeed(address(uint160(params.value)));
```

### [L-06] Upgradeable contract not initialized

Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user.

There is 1 instance:

- *Size.sol* ( [62-62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L62-L62) ):

```solidity
/// @audit __UUPS_init()
62: contract Size is ISize, SizeView, Initializable, AccessControlUpgradeable, PausableUpgradeable, UUPSUpgradeable {
```

### [L-07] Use Ownable2Step instead of Ownable

[`Ownable2Step`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3d7a93876a2e5e1d7fe29b5a0e96e222afdc4cfa/contracts/access/Ownable2Step.sol#L31-L56) and [`Ownable2StepUpgradeable`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/access/Ownable2StepUpgradeable.sol#L47-L63) prevent the contract ownership from mistakenly being transferred to an address that cannot handle it (e.g. due to a typo in the address), by requiring that the recipient of the owner permissions actively accept via a contract call of its own.

There are 2 instances:

- *NonTransferrableScaledToken.sol* ( [19-19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L19-L19) ):

```solidity
19: contract NonTransferrableScaledToken is NonTransferrableToken {
```

- *NonTransferrableToken.sol* ( [14-14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L14-L14) ):

```solidity
14: contract NonTransferrableToken is Ownable, ERC20 {
```

### [L-08] Constructor / initialization function lacks parameter validation

Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

<details>
<summary>There are 8 instances (click to show):</summary>

- *PriceFeed.sol* ( [36-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L36-L42) ):

```solidity
/// @audit `_sequencerUptimeFeed`
36:     constructor(
37:         address _base,
38:         address _quote,
39:         address _sequencerUptimeFeed,
40:         uint256 _baseStalePriceInterval,
41:         uint256 _quoteStalePriceInterval
42:     ) {
```

- *NonTransferrableScaledToken.sol* ( [25-32](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L25-L32) ):

```solidity
/// @audit `owner_`
/// @audit `name_`
/// @audit `symbol_`
/// @audit `decimals_`
25:     constructor(
26:         IPool variablePool_,
27:         IERC20Metadata underlyingToken_,
28:         address owner_,
29:         string memory name_,
30:         string memory symbol_,
31:         uint8 decimals_
32:     ) NonTransferrableToken(owner_, name_, symbol_, decimals_) {
```

- *NonTransferrableToken.sol* ( [18-21](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L18-L21) ):

```solidity
/// @audit `owner_`
/// @audit `name_`
/// @audit `symbol_`
18:     constructor(address owner_, string memory name_, string memory symbol_, uint8 decimals_)
19:         Ownable(owner_)
20:         ERC20(name_, symbol_)
21:     {
```

</details>

### [L-09] Missing checks for `address(0)` when setting address state variables

Consider adding a zero address check when setting address state variables.

There is 1 instance:

- *PriceFeed.sol* ( [54-54](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L54-L54) ):

```solidity
54:         sequencerUptimeFeed = AggregatorV3Interface(_sequencerUptimeFeed);
```

### [L-10] Owner can renounce Ownership

Each of the following contracts implements or inherits the `renounceOwnership()` function. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

There are 2 instances:

- *NonTransferrableScaledToken.sol* ( [19-19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L19-L19) ):

```solidity
19: contract NonTransferrableScaledToken is NonTransferrableToken {
```

- *NonTransferrableToken.sol* ( [14-14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L14-L14) ):

```solidity
14: contract NonTransferrableToken is Ownable, ERC20 {
```

### [L-11] Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks opens the users of this protocol up to [read-only reentrancy vulnerability](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect them except by block-listing the entire protocol.

<details>
<summary>There are 16 instances (click to show):</summary>

- *DepositTokenLibrary.sol* ( [25-25](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L25-L25), [39-39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L39-L39), [52-52](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L52-L52) ):

```solidity
25:         underlyingCollateralToken.safeTransferFrom(from, address(this), amount);

39:         underlyingCollateralToken.safeTransfer(to, amount);

52:         state.data.underlyingBorrowToken.safeTransferFrom(from, address(this), amount);
```

- *BuyCreditMarket.sol* ( [195-195](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L195-L195), [196-196](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L196-L196) ):

```solidity
195:         state.data.borrowAToken.transferFrom(msg.sender, borrower, cashAmountIn - fees);

196:         state.data.borrowAToken.transferFrom(msg.sender, state.feeConfig.feeRecipient, fees);
```

- *Claim.sol* ( [56-56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L56-L56) ):

```solidity
56:         state.data.borrowAToken.transferFrom(address(this), creditPosition.lender, claimAmount);
```

- *Compensate.sol* ( [152-154](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L152-L154) ):

```solidity
152:             state.data.collateralToken.transferFrom(
153:                 msg.sender, state.feeConfig.feeRecipient, fragmentationFeeInCollateral
154:             );
```

- *Liquidate.sol* ( [118-118](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L118-L118), [119-119](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L119-L119), [120-122](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L120-L122) ):

```solidity
118:         state.data.borrowAToken.transferFrom(msg.sender, address(this), debtPosition.futureValue);

119:         state.data.collateralToken.transferFrom(debtPosition.borrower, msg.sender, liquidatorProfitCollateralToken);

120:         state.data.collateralToken.transferFrom(
121:             debtPosition.borrower, state.feeConfig.feeRecipient, protocolProfitCollateralToken
122:         );
```

- *LiquidateWithReplacement.sol* ( [161-161](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L161-L161), [162-162](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L162-L162) ):

```solidity
161:         state.data.borrowAToken.transferFrom(address(this), params.borrower, issuanceValue);

162:         state.data.borrowAToken.transferFrom(address(this), state.feeConfig.feeRecipient, liquidatorProfitBorrowToken);
```

- *Repay.sol* ( [49-49](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Repay.sol#L49-L49) ):

```solidity
49:         state.data.borrowAToken.transferFrom(msg.sender, address(this), debtPosition.futureValue);
```

- *SelfLiquidate.sol* ( [70-70](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L70-L70) ):

```solidity
70:         state.data.collateralToken.transferFrom(debtPosition.borrower, msg.sender, assignedCollateral);
```

- *SellCreditMarket.sol* ( [201-201](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L201-L201), [202-202](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L202-L202) ):

```solidity
201:         state.data.borrowAToken.transferFrom(params.lender, msg.sender, cashAmountOut);

202:         state.data.borrowAToken.transferFrom(params.lender, state.feeConfig.feeRecipient, fees);
```

</details>

### [L-12] Critical functions should be controlled by time locks

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

There is 1 instance:

- *Size.sol* ( [107-107](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L107-L107) ):

```solidity
107:     function _authorizeUpgrade(address newImplementation) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}
```

### [L-13] External function calls within loops

Calling external functions within loops can easily result in insufficient gas. This greatly increases the likelihood of transaction failures, DOS attacks, and other unexpected actions. It is recommended to limit the number of loops within loops that call external functions, and to limit the gas line for each external call.

There is 1 instance:

- *Multicall.sol* ( [34-34](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L34-L34) ):

```solidity
/// @audit Calling `functionDelegateCall()` within `for` loop
34:             results[i] = Address.functionDelegateCall(address(this), data[i]);
```

### [L-14] Tokens may be minted to the zero address

Neither the listed functions, nor `_mint()` prevent minting to `address(0x0)`

<details>
<summary>There are 8 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [91-91](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L91-L91) ):

```solidity
91:         state.data.debtToken.mint(borrower, futureValue);
```

- *DepositTokenLibrary.sol* ( [26-26](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L26-L26), [64-64](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L64-L64) ):

```solidity
26:         state.data.collateralToken.mint(to, amount);

64:         state.data.borrowAToken.mintScaled(to, scaledAmount);
```

- *LiquidateWithReplacement.sol* ( [160-160](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L160-L160) ):

```solidity
160:         state.data.debtToken.mint(params.borrower, debtPosition.futureValue);
```

- *NonTransferrableScaledToken.sol* ( [42-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L42-L42), [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L50-L50), [80-80](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L80-L80) ):

```solidity
42:     function mint(address, uint256) external view override onlyOwner {

50:     function mintScaled(address to, uint256 scaledAmount) external onlyOwner {

80:         _mint(to, scaledAmount);
```

- *NonTransferrableToken.sol* ( [29-29](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L29-L29) ):

```solidity
29:     function mint(address to, uint256 value) external virtual onlyOwner {
```

</details>

### [L-15] Use `SafeCast` to downcast safely

When a type is downcast to a smaller type, the higher order bits are truncated, effectively applying a modulo to the original value. The loss of data may cause incorrect calculations, unexpected state changes, or other unexpected behavior.
It is recommended to use the [SafeCast library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeCast.sol).

There is 1 instance:

- *Size.sol* ( [127-127](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L127-L127) ):

```solidity
/// @audit uint256 -> uint64
127:         state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
```

### [L-16] Loss of precision in divisions

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.

<details>
<summary>There are 21 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [32-34](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L32-L34), [192-192](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L192-L192), [253-255](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L253-L255), [321-321](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L321-L321), [326-326](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L326-L326) ):

```solidity
32:         collateralTokenAmount = Math.mulDivUp(
33:             debtTokenAmountWad, 10 ** state.oracle.priceFeed.decimals(), state.oracle.priceFeed.getPrice()
34:         );

192:         uint256 maxCashAmountOut = Math.mulDivDown(creditAmountIn, PERCENT, PERCENT + ratePerTenor);

253:             creditAmountIn = Math.mulDivUp(
254:                 cashAmountOut + state.feeConfig.fragmentationFee, PERCENT + ratePerTenor, PERCENT - swapFeePercent
255:             );

321:             cashAmountIn = Math.mulDivUp(maxCredit, PERCENT, PERCENT + ratePerTenor);

326:             uint256 netCashAmountIn = Math.mulDivUp(creditAmountOut, PERCENT, PERCENT + ratePerTenor);
```

- *YieldCurveLibrary.sol* ( [135-135](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L135-L135), [137-137](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L137-L137) ):

```solidity
135:                     return y0 + Math.mulDivDown(y1 - y0, tenor - x0, x1 - x0);

137:                     return y0 - Math.mulDivDown(y0 - y1, tenor - x0, x1 - x0);
```

- *BuyCreditMarket.sol* ( [162-162](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L162-L162) ):

```solidity
162:                     : Math.mulDivUp(creditPosition.credit, PERCENT, PERCENT + ratePerTenor),
```

- *Claim.sol* ( [52-54](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L52-L54) ):

```solidity
52:         uint256 claimAmount = Math.mulDivDown(
53:             creditPosition.credit, state.data.borrowAToken.liquidityIndex(), debtPosition.liquidityIndexAtRepayment
54:         );
```

- *LiquidateWithReplacement.sol* ( [146-146](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L146-L146) ):

```solidity
146:         issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
```

- *SellCreditMarket.sol* ( [175-175](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L175-L175), [177-177](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L177-L177) ):

```solidity
175:                     : Math.mulDivDown(creditPosition.credit, PERCENT - state.getSwapFeePercent(tenor), PERCENT + ratePerTenor),

177:                     ? Math.mulDivUp(cashAmountOut, PERCENT + ratePerTenor, PERCENT - state.getSwapFeePercent(tenor))
```

- *UpdateConfig.sol* ( [101-101](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L101-L101), [104-104](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L104-L104), [111-111](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L111-L111), [114-114](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L114-L114), [119-119](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L119-L119), [121-121](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L121-L121) ):

```solidity
101:                     && params.value >= Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)

104:                     params.value, Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)

111:                     && params.value >= Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)

114:                     params.value, Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)

119:             if (params.value >= Math.mulDivDown(PERCENT, YEAR, state.riskConfig.maxTenor)) {

121:                     params.value, Math.mulDivDown(PERCENT, YEAR, state.riskConfig.maxTenor)
```

- *PriceFeed.sol* ( [79-81](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L79-L81) ):

```solidity
79:         return Math.mulDivDown(
80:             _getPrice(base, baseStalePriceInterval), 10 ** decimals, _getPrice(quote, quoteStalePriceInterval)
81:         );
```

- *NonTransferrableScaledToken.sol* ( [77-77](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L77-L77), [99-99](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L99-L99) ):

```solidity
77:         uint256 scaledAmount = Math.mulDivDown(value, WadRayMath.RAY, liquidityIndex());

99:         return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
```

</details>

### [L-17] Function `name()` is not a part of the ERC-20 standard

The `name()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

There are 3 instances:

- *Initialize.sol* ( [241-241](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L241-L241), [249-249](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L249-L249), [255-255](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L255-L255) ):

```solidity
241:             string.concat("Size ", IERC20Metadata(state.data.underlyingCollateralToken).name()),

249:             string.concat("Size Scaled ", IERC20Metadata(state.data.underlyingBorrowToken).name()),

255:             string.concat("Size Debt ", IERC20Metadata(state.data.underlyingBorrowToken).name()),
```

### [L-18] Code does not follow the best practice of check-effects-interaction

Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

There is 1 instance:

- *Size.sol* ( [218-218](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L218-L218) ):

```solidity
/// @audit `validateLiquidate()` is called on line 217, triggering an external call - `balanceOf()`
218:         liquidatorProfitCollateralToken = state.executeLiquidate(params);
```

## Non Critical Issues

### [N-01] Custom errors has no error details

Consider adding parameters to the error to indicate which user or values caused the failure.

There are 13 instances:

- *Errors.sol* ( [11](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L11), [12](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L12), [13](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L13), [14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L14), [15](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L15), [16](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L16), [18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L18), [19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L19), [73](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L73), [74](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L74), [80](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L80), [82](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L82), [83](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L83) ):

```solidity
11:     error NULL_ADDRESS();

12:     error NULL_AMOUNT();

13:     error NULL_TENOR();

14:     error NULL_MAX_DUE_DATE();

15:     error NULL_ARRAY();

16:     error NULL_OFFER();

18:     error TENORS_NOT_STRICTLY_INCREASING();

19:     error ARRAY_LENGTHS_MISMATCH();

73:     error NULL_STALE_PRICE();

74:     error NULL_STALE_RATE();

80:     error NOT_SUPPORTED();

82:     error SEQUENCER_DOWN();

83:     error GRACE_PERIOD_NOT_OVER();
```

### [N-02] There is no need to initialize variables with 0

Since the variables are automatically set to 0 when created, it is redundant to initialize it with 0 again.

<details>
<summary>There are 6 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [238-238](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L238-L238) ):

```solidity
238:         uint256 maxCashAmountOutFragmentation = 0;
```

- *LoanLibrary.sol* ( [10-10](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L10-L10) ):

```solidity
10: uint256 constant DEBT_POSITION_ID_START = 0;
```

- *Multicall.sol* ( [33-33](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L33-L33) ):

```solidity
33:         for (uint256 i = 0; i < data.length; i++) {
```

- *Liquidate.sol* ( [92-92](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L92-L92) ):

```solidity
92:         uint256 protocolProfitCollateralToken = 0;
```

- *SetUserConfiguration.sol* ( [48-48](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L48-L48), [69-69](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L69-L69) ):

```solidity
48:         for (uint256 i = 0; i < params.creditPositionIds.length; i++) {

69:         for (uint256 i = 0; i < params.creditPositionIds.length; i++) {
```

</details>

### [N-03] Names of constants should use the UPPER_CASE_WITH_UNDERSCORES style

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html)

There is 1 instance:

- *PriceFeed.sol* ( [28-28](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L28-L28) ):

```solidity
28:     uint256 public constant decimals = 18;
```

### [N-04] Names of `private`/`internal` functions should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

<details>
<summary>There are 37 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [26-29](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L26-L29), [153-155](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L153-L155), [164-164](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L164-L164), [173-173](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L173-L173), [185-191](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L185-L191), [228-235](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L228-L235), [274-281](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L274-L281), [311-317](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L311-L317) ):

```solidity
26:     function debtTokenAmountToCollateralTokenAmount(State storage state, uint256 debtTokenAmount)
27:         internal
28:         view
29:         returns (uint256 collateralTokenAmount)

153:     function reduceDebtAndCredit(State storage state, uint256 debtPositionId, uint256 creditPositionId, uint256 amount)
154:         internal
155:     {

164:     function getSwapFeePercent(State storage state, uint256 tenor) internal view returns (uint256) {

173:     function getSwapFee(State storage state, uint256 cash, uint256 tenor) internal view returns (uint256) {

185:     function getCashAmountOut(
186:         State storage state,
187:         uint256 creditAmountIn,
188:         uint256 maxCredit,
189:         uint256 ratePerTenor,
190:         uint256 tenor
191:     ) internal view returns (uint256 cashAmountOut, uint256 fees) {

228:     function getCreditAmountIn(
229:         State storage state,
230:         uint256 cashAmountOut,
231:         uint256 maxCashAmountOut,
232:         uint256 maxCredit,
233:         uint256 ratePerTenor,
234:         uint256 tenor
235:     ) internal view returns (uint256 creditAmountIn, uint256 fees) {

274:     function getCreditAmountOut(
275:         State storage state,
276:         uint256 cashAmountIn,
277:         uint256 maxCashAmountIn,
278:         uint256 maxCredit,
279:         uint256 ratePerTenor,
280:         uint256 tenor
281:     ) internal view returns (uint256 creditAmountOut, uint256 fees) {

311:     function getCashAmountIn(
312:         State storage state,
313:         uint256 creditAmountOut,
314:         uint256 maxCredit,
315:         uint256 ratePerTenor,
316:         uint256 tenor
317:     ) internal view returns (uint256 cashAmountIn, uint256 fees) {
```

- *LoanLibrary.sol* ( [63-63](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L63-L63), [71-71](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L71-L71) ):

```solidity
63:     function isDebtPositionId(State storage state, uint256 positionId) internal view returns (bool) {

71:     function isCreditPositionId(State storage state, uint256 positionId) internal view returns (bool) {
```

- *Math.sol* ( [15-15](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L15-L15), [19-19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L19-L19), [23-23](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L23-L23), [27-27](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L27-L27), [31-31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L31-L31), [40-40](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L40-L40), [51-51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L51-L51) ):

```solidity
15:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

19:     function max(uint256 a, uint256 b) internal pure returns (uint256) {

23:     function mulDivUp(uint256 x, uint256 y, uint256 z) internal pure returns (uint256) {

27:     function mulDivDown(uint256 x, uint256 y, uint256 z) internal pure returns (uint256) {

31:     function amountToWad(uint256 amount, uint8 decimals) internal pure returns (uint256) {

40:     function aprToRatePerTenor(uint256 apr, uint256 tenor) internal pure returns (uint256) {

51:     function binarySearch(uint256[] memory array, uint256 value) internal pure returns (uint256 low, uint256 high) {
```

- *Multicall.sol* ( [26-26](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L26-L26) ):

```solidity
26:     function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
```

- *OfferLibrary.sol* ( [32-32](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L32-L32), [39-39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L39-L39), [48-51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L48-L51), [62-65](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L62-L65), [76-79](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L76-L79), [90-93](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L90-L93) ):

```solidity
32:     function isNull(LoanOffer memory self) internal pure returns (bool) {

39:     function isNull(BorrowOffer memory self) internal pure returns (bool) {

48:     function getAPRByTenor(LoanOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)
49:         internal
50:         view
51:         returns (uint256)

62:     function getRatePerTenor(LoanOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)
63:         internal
64:         view
65:         returns (uint256)

76:     function getAPRByTenor(BorrowOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)
77:         internal
78:         view
79:         returns (uint256)

90:     function getRatePerTenor(BorrowOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)
91:         internal
92:         view
93:         returns (uint256)
```

- *RiskLibrary.sol* ( [89-92](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L89-L92) ):

```solidity
89:     function isCreditPositionTransferrable(State storage state, uint256 creditPositionId)
90:         internal
91:         view
92:         returns (bool)
```

- *YieldCurveLibrary.sol* ( [38-38](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L38-L38), [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L50-L50), [87-90](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L87-L90) ):

```solidity
38:     function isNull(YieldCurve memory self) internal pure returns (bool) {

50:     function validateYieldCurve(YieldCurve memory self, uint256 minTenor, uint256 maxTenor) internal pure {

87:     function getAdjustedAPR(int256 apr, uint256 marketRateMultiplier, VariablePoolBorrowRateParams memory params)
88:         internal
89:         view
90:         returns (uint256)
```

- *Initialize.sol* ( [62-62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L62-L62), [70-70](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L70-L70), [98-98](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L98-L98), [132-132](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L132-L132), [146-146](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L146-L146), [193-193](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L193-L193), [207-207](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L207-L207), [222-222](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L222-L222), [230-230](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L230-L230) ):

```solidity
62:     function validateOwner(address owner) internal pure {

70:     function validateInitializeFeeConfigParams(InitializeFeeConfigParams memory f) internal pure {

98:     function validateInitializeRiskConfigParams(InitializeRiskConfigParams memory r) internal pure {

132:     function validateInitializeOracleParams(InitializeOracleParams memory o) internal view {

146:     function validateInitializeDataParams(InitializeDataParams memory d) internal view {

193:     function executeInitializeFeeConfig(State storage state, InitializeFeeConfigParams memory f) internal {

207:     function executeInitializeRiskConfig(State storage state, InitializeRiskConfigParams memory r) internal {

222:     function executeInitializeOracle(State storage state, InitializeOracleParams memory o) internal {

230:     function executeInitializeData(State storage state, InitializeDataParams memory d) internal {
```

</details>

### [N-05] Names of `private`/`internal` state variables should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

There are 5 instances:

- *SizeStorage.sol* ( [114-114](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeStorage.sol#L114-L114) ):

```solidity
114:     State internal state;
```

- *PriceFeed.sol* ( [25-25](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L25-L25), [31-31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L31-L31) ):

```solidity
25:     uint256 private constant GRACE_PERIOD_TIME = 3600;

31:     AggregatorV3Interface internal immutable sequencerUptimeFeed;
```

- *NonTransferrableScaledToken.sol* ( [20-20](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L20-L20), [21-21](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L21-L21) ):

```solidity
20:     IPool private immutable variablePool;

21:     IERC20Metadata private immutable underlyingToken;
```

### [N-06] Redundant inheritance specifier

The contracts below already extend the specified contract, so there is no need to list it in the inheritance list again.

There is 1 instance:

- *Size.sol* ( [62-62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L62-L62) ):

```solidity
/// @audit AccessControlUpgradeable already extends Initializable
62: contract Size is ISize, SizeView, Initializable, AccessControlUpgradeable, PausableUpgradeable, UUPSUpgradeable {
```

### [N-07] Use of `override` is unnecessary

[Starting from Solidity 0.8.8](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding), the override keyword is not required when overriding an interface function, except for the case where the function is defined in multiple bases.

<details>
<summary>There are 28 instances (click to show):</summary>

- *Size.sol* ( [107-107](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L107-L107), [110-114](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L110-L114), [120-124](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L120-L124), [132-132](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L132-L132), [137-137](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L137-L137), [142-147](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L142-L147), [153-153](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L153-L153), [159-159](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L159-L159), [166-166](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L166-L166), [172-172](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L172-L172), [178-178](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L178-L178), [188-188](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L188-L188), [198-198](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L198-L198), [204-204](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L204-L204), [210-215](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L210-L215), [223-223](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L223-L223), [229-235](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L229-L235), [247-247](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L247-L247), [254-259](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L254-L259) ):

```solidity
107:     function _authorizeUpgrade(address newImplementation) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}

110:     function updateConfig(UpdateConfigParams calldata params)
111:         external
112:         override(ISizeAdmin)
113:         onlyRole(DEFAULT_ADMIN_ROLE)
114:     {

120:     function setVariablePoolBorrowRate(uint128 borrowRate)
121:         external
122:         override(ISizeAdmin)
123:         onlyRole(BORROW_RATE_UPDATER_ROLE)
124:     {

132:     function pause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) {

137:     function unpause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) {

142:     function multicall(bytes[] calldata _data)
143:         public
144:         payable
145:         override(IMulticall)
146:         whenNotPaused
147:         returns (bytes[] memory results)

153:     function deposit(DepositParams calldata params) public payable override(ISize) whenNotPaused {

159:     function withdraw(WithdrawParams calldata params) external payable override(ISize) whenNotPaused {

166:     function buyCreditLimit(BuyCreditLimitParams calldata params) external payable override(ISize) whenNotPaused {

172:     function sellCreditLimit(SellCreditLimitParams calldata params) external payable override(ISize) whenNotPaused {

178:     function buyCreditMarket(BuyCreditMarketParams calldata params) external payable override(ISize) whenNotPaused {

188:     function sellCreditMarket(SellCreditMarketParams memory params) external payable override(ISize) whenNotPaused {

198:     function repay(RepayParams calldata params) external payable override(ISize) whenNotPaused {

204:     function claim(ClaimParams calldata params) external payable override(ISize) whenNotPaused {

210:     function liquidate(LiquidateParams calldata params)
211:         external
212:         payable
213:         override(ISize)
214:         whenNotPaused
215:         returns (uint256 liquidatorProfitCollateralToken)

223:     function selfLiquidate(SelfLiquidateParams calldata params) external payable override(ISize) whenNotPaused {

229:     function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
230:         external
231:         payable
232:         override(ISize)
233:         whenNotPaused
234:         onlyRole(KEEPER_ROLE)
235:         returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)

247:     function compensate(CompensateParams calldata params) external payable override(ISize) whenNotPaused {

254:     function setUserConfiguration(SetUserConfigurationParams calldata params)
255:         external
256:         payable
257:         override(ISize)
258:         whenNotPaused
259:     {
```

- *NonTransferrableScaledToken.sol* ( [42-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L42-L42), [56-56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L56-L56), [105-105](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L105-L105), [117-117](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L117-L117) ):

```solidity
42:     function mint(address, uint256) external view override onlyOwner {

56:     function burn(address, uint256) external view override onlyOwner {

105:     function balanceOf(address account) public view override returns (uint256) {

117:     function totalSupply() public view override returns (uint256) {
```

- *NonTransferrableToken.sol* ( [37-37](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L37-L37), [42-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L42-L42), [46-46](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L46-L46), [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L50-L50), [54-54](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L54-L54) ):

```solidity
37:     function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {

42:     function transfer(address to, uint256 value) public virtual override onlyOwner returns (bool) {

46:     function allowance(address, address spender) public view virtual override returns (uint256) {

50:     function approve(address, uint256) public virtual override returns (bool) {

54:     function decimals() public view virtual override returns (uint8) {
```

</details>

### [N-08] Unused errors

The following `error`s are defined but not used. It is recommended to check the code for logical omissions that cause them not to be used. If it's determined that they are not needed anywhere, it's best to remove them from the codebase to improve code clarity and minimize confusion.
Note that there may be cases where an error appears to be used because it has multiple definitions in different files. In such cases, the definitions should be moved to a separate file.

There are 2 instances:

- *Errors.sol* ( [49-49](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L49-L49), [74-74](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L74-L74) ):

```solidity
49:     error NOT_ENOUGH_BORROW_ATOKEN_BALANCE(address account, uint256 balance, uint256 required);

74:     error NULL_STALE_RATE();
```

### [N-09] Large numeric literals should use underscores for readability

Large hardcoded numbers in code can be difficult to read. Consider using underscores for number literals to improve readability (e.g `1234567` -> `1_234_567`). Consider using an underscore for every third digit from the right.

There is 1 instance:

- *PriceFeed.sol* ( [25-25](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L25-L25) ):

```solidity
25:     uint256 private constant GRACE_PERIOD_TIME = 3600;
```

### [N-10] Add inline comments for unnamed parameters

`function func(address a, address)` -> `function func(address a, address /* b */)`

<details>
<summary>There are 7 instances (click to show):</summary>

- *Initialize.sol* ( [175-182](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L175-L182) ):

```solidity
175:     function validateInitialize(
176:         State storage,
177:         address owner,
178:         InitializeFeeConfigParams memory f,
179:         InitializeRiskConfigParams memory r,
180:         InitializeOracleParams memory o,
181:         InitializeDataParams memory d
182:     ) external view {
```

- *Liquidate.sol* ( [59-63](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L59-L63) ):

```solidity
59:     function validateMinimumCollateralProfit(
60:         State storage,
61:         LiquidateParams calldata params,
62:         uint256 liquidatorProfitCollateralToken
63:     ) external pure {
```

- *UpdateConfig.sol* ( [79-79](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L79-L79) ):

```solidity
79:     function validateUpdateConfig(State storage, UpdateConfigParams calldata) external pure {
```

- *NonTransferrableScaledToken.sol* ( [42-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L42-L42), [56-56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L56-L56) ):

```solidity
42:     function mint(address, uint256) external view override onlyOwner {

56:     function burn(address, uint256) external view override onlyOwner {
```

- *NonTransferrableToken.sol* ( [46-46](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L46-L46), [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L50-L50) ):

```solidity
46:     function allowance(address, address spender) public view virtual override returns (uint256) {

50:     function approve(address, uint256) public virtual override returns (bool) {
```

</details>

### [N-11] Consider splitting complex checks into multiple steps

Assign the expression's parts to intermediate local variables, and check against those instead.

There are 2 instances:

- *YieldCurveLibrary.sol* ( [51-51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L51-L51) ):

```solidity
51:         if (self.tenors.length == 0 || self.aprs.length == 0 || self.marketRateMultipliers.length == 0) {
```

- *Deposit.sol* ( [41-41](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L41-L41) ):

```solidity
41:         if (msg.value != 0 && (msg.value != params.amount || params.token != address(state.data.weth))) {
```

### [N-12] Consider moving `msg.sender` checks to `modifier`s

If some functions are only allowed to be called by some specific users, consider using a modifier instead of checking with a require statement, especially if this check is done in multiple functions.

<details>
<summary>There are 4 instances (click to show):</summary>

- *Compensate.sol* ( [93-95](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L93-L95) ):

```solidity
93:         if (msg.sender != debtPositionToRepay.borrower) {
94:             revert Errors.COMPENSATOR_IS_NOT_BORROWER(msg.sender, debtPositionToRepay.borrower);
95:         }
```

- *SelfLiquidate.sol* ( [51-53](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L51-L53) ):

```solidity
51:         if (msg.sender != creditPosition.lender) {
52:             revert Errors.LIQUIDATOR_IS_NOT_LENDER(msg.sender, creditPosition.lender);
53:         }
```

- *SellCreditMarket.sol* ( [74-76](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L74-L76) ):

```solidity
74:             if (msg.sender != creditPosition.lender) {
75:                 revert Errors.BORROWER_IS_NOT_LENDER(msg.sender, creditPosition.lender);
76:             }
```

- *SetUserConfiguration.sol* ( [50-52](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L50-L52) ):

```solidity
50:             if (creditPosition.lender != msg.sender) {
51:                 revert Errors.INVALID_CREDIT_POSITION_ID(params.creditPositionIds[i]);
52:             }
```

</details>

### [N-13] Complex math should be split into multiple steps

Consider splitting long arithmetic calculations into multiple steps to improve the code readability.

<details>
<summary>There are 3 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [253-255](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L253-L255) ):

```solidity
253:             creditAmountIn = Math.mulDivUp(
254:                 cashAmountOut + state.feeConfig.fragmentationFee, PERCENT + ratePerTenor, PERCENT - swapFeePercent
255:             );
```

- *BuyCreditMarket.sol* ( [158-168](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L158-L168) ):

```solidity
158:             (creditAmountOut, fees) = state.getCreditAmountOut({
159:                 cashAmountIn: cashAmountIn,
160:                 maxCashAmountIn: params.creditPositionId == RESERVED_ID
161:                     ? cashAmountIn
162:                     : Math.mulDivUp(creditPosition.credit, PERCENT, PERCENT + ratePerTenor),
163:                 maxCredit: params.creditPositionId == RESERVED_ID
164:                     ? Math.mulDivDown(cashAmountIn, PERCENT + ratePerTenor, PERCENT)
165:                     : creditPosition.credit,
166:                 ratePerTenor: ratePerTenor,
167:                 tenor: tenor
168:             });
```

- *SellCreditMarket.sol* ( [171-181](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L171-L181) ):

```solidity
171:             (creditAmountIn, fees) = state.getCreditAmountIn({
172:                 cashAmountOut: cashAmountOut,
173:                 maxCashAmountOut: params.creditPositionId == RESERVED_ID
174:                     ? cashAmountOut
175:                     : Math.mulDivDown(creditPosition.credit, PERCENT - state.getSwapFeePercent(tenor), PERCENT + ratePerTenor),
176:                 maxCredit: params.creditPositionId == RESERVED_ID
177:                     ? Math.mulDivUp(cashAmountOut, PERCENT + ratePerTenor, PERCENT - state.getSwapFeePercent(tenor))
178:                     : creditPosition.credit,
179:                 ratePerTenor: ratePerTenor,
180:                 tenor: tenor
181:             });
```

</details>

### [N-14] Consider adding a block/deny-list

Doing so will significantly increase centralization, but will help to prevent hackers from using stolen tokens

There is 1 instance:

- *NonTransferrableToken.sol* ( [14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L14) ):

```solidity
14: contract NonTransferrableToken is Ownable, ERC20 {
```

### [N-15] Do not use literal numbers for array indexes

Indexing arrays using enums or meaningfully named constants instead of literals can make code easier to read and maintain.

There are 4 instances:

- *YieldCurveLibrary.sol* ( [69-69](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L69-L69), [70-70](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L70-L70), [121-121](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L121-L121), [122-122](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L122-L122) ):

```solidity
69:         if (self.tenors[0] < minTenor) {

70:             revert Errors.TENOR_OUT_OF_RANGE(self.tenors[0], minTenor, maxTenor);

121:         if (tenor < curveRelativeTime.tenors[0] || tenor > curveRelativeTime.tenors[length - 1]) {

122:             revert Errors.TENOR_OUT_OF_RANGE(tenor, curveRelativeTime.tenors[0], curveRelativeTime.tenors[length - 1]);
```

### [N-16] Consider emitting an event at the end of the constructor

This will allow users to easily exactly pinpoint when and by whom a contract was constructed.

<details>
<summary>There are 4 instances (click to show):</summary>

- *Size.sol* ( [83-83](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L83-L83) ):

```solidity
83:     constructor() {
```

- *PriceFeed.sol* ( [36-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L36-L42) ):

```solidity
36:     constructor(
37:         address _base,
38:         address _quote,
39:         address _sequencerUptimeFeed,
40:         uint256 _baseStalePriceInterval,
41:         uint256 _quoteStalePriceInterval
42:     ) {
```

- *NonTransferrableScaledToken.sol* ( [25-32](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L25-L32) ):

```solidity
25:     constructor(
26:         IPool variablePool_,
27:         IERC20Metadata underlyingToken_,
28:         address owner_,
29:         string memory name_,
30:         string memory symbol_,
31:         uint8 decimals_
32:     ) NonTransferrableToken(owner_, name_, symbol_, decimals_) {
```

- *NonTransferrableToken.sol* ( [18-21](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L18-L21) ):

```solidity
18:     constructor(address owner_, string memory name_, string memory symbol_, uint8 decimals_)
19:         Ownable(owner_)
20:         ERC20(name_, symbol_)
21:     {
```

</details>

### [N-17] Empty function body without comments

Empty function body in solidity is not recommended, consider adding some comments to the body.

There is 1 instance:

- *Size.sol* ( [107-107](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L107-L107) ):

```solidity
107:     function _authorizeUpgrade(address newImplementation) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}
```

### [N-18] Events are emitted without the sender information

When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the `msg.sender` the events of these types of action will make events much more useful to end users, especially when `msg.sender` is not `tx.origin`.

<details>
<summary>There are 23 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [48-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L48-L50), [75-75](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L75-L75), [89-89](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L89-L89), [110-112](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L110-L112), [125-125](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L125-L125), [142-144](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L142-L144) ):

```solidity
48:         emit Events.UpdateDebtPosition(
49:             debtPositionId, debtPosition.borrower, debtPosition.futureValue, debtPosition.liquidityIndexAtRepayment
50:         );

75:         emit Events.CreateDebtPosition(debtPositionId, borrower, lender, futureValue, dueDate);

89:         emit Events.CreateCreditPosition(creditPositionId, lender, debtPositionId, RESERVED_ID, creditPosition.credit);

110:             emit Events.UpdateCreditPosition(
111:                 exitCreditPositionId, lender, exitCreditPosition.credit, exitCreditPosition.forSale
112:             );

125:             emit Events.CreateCreditPosition(creditPositionId, lender, debtPositionId, exitCreditPositionId, credit);

142:         emit Events.UpdateCreditPosition(
143:             creditPositionId, creditPosition.lender, creditPosition.credit, creditPosition.forSale
144:         );
```

- *BuyCreditLimit.sol* ( [60-65](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditLimit.sol#L60-L65) ):

```solidity
60:         emit Events.BuyCreditLimit(
61:             params.maxDueDate,
62:             params.curveRelativeTime.tenors,
63:             params.curveRelativeTime.aprs,
64:             params.curveRelativeTime.marketRateMultipliers
65:         );
```

- *BuyCreditMarket.sol* ( [125-127](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L125-L127) ):

```solidity
125:         emit Events.BuyCreditMarket(
126:             params.borrower, params.creditPositionId, params.tenor, params.amount, params.exactAmountIn
127:         );
```

- *Claim.sol* ( [58-58](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L58-L58) ):

```solidity
58:         emit Events.Claim(params.creditPositionId, creditPosition.debtPositionId);
```

- *Compensate.sol* ( [107-109](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L107-L109) ):

```solidity
107:         emit Events.Compensate(
108:             params.creditPositionWithDebtToRepayId, params.creditPositionToCompensateId, params.amount
109:         );
```

- *Deposit.sol* ( [87-87](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L87-L87) ):

```solidity
87:         emit Events.Deposit(params.token, params.to, amount);
```

- *Initialize.sol* ( [278-278](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L278-L278) ):

```solidity
278:         emit Events.Initialize(f, r, o, d);
```

- *Liquidate.sol* ( [83-83](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L83-L83) ):

```solidity
83:         emit Events.Liquidate(params.debtPositionId, params.minimumCollateralProfit, collateralRatio, loanStatus);
```

- *LiquidateWithReplacement.sol* ( [124-124](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L124-L124), [153-158](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L153-L158) ):

```solidity
124:         emit Events.LiquidateWithReplacement(params.debtPositionId, params.borrower, params.minimumCollateralProfit);

153:         emit Events.UpdateDebtPosition(
154:             params.debtPositionId,
155:             debtPosition.borrower,
156:             debtPosition.futureValue,
157:             debtPosition.liquidityIndexAtRepayment
158:         );
```

- *Repay.sol* ( [53-53](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Repay.sol#L53-L53) ):

```solidity
53:         emit Events.Repay(params.debtPositionId);
```

- *SelfLiquidate.sol* ( [60-60](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L60-L60) ):

```solidity
60:         emit Events.SelfLiquidate(params.creditPositionId);
```

- *SellCreditLimit.sol* ( [46-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditLimit.sol#L46-L50) ):

```solidity
46:         emit Events.SellCreditLimit(
47:             params.curveRelativeTime.tenors,
48:             params.curveRelativeTime.aprs,
49:             params.curveRelativeTime.marketRateMultipliers
50:         );
```

- *SellCreditMarket.sol* ( [131-133](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L131-L133) ):

```solidity
131:         emit Events.SellCreditMarket(
132:             params.lender, params.creditPositionId, params.tenor, params.amount, params.tenor, params.exactAmountIn
133:         );
```

- *SetUserConfiguration.sol* ( [72-74](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L72-L74), [77-82](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L77-L82) ):

```solidity
72:             emit Events.UpdateCreditPosition(
73:                 params.creditPositionIds[i], creditPosition.lender, creditPosition.credit, creditPosition.forSale
74:             );

77:         emit Events.SetUserConfiguration(
78:             params.openingLimitBorrowCR,
79:             params.allCreditPositionsForSaleDisabled,
80:             params.creditPositionIdsForSale,
81:             params.creditPositionIds
82:         );
```

- *UpdateConfig.sol* ( [147-147](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L147-L147) ):

```solidity
147:         emit Events.UpdateConfig(params.key, params.value);
```

- *Withdraw.sol* ( [66-66](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L66-L66) ):

```solidity
66:         emit Events.Withdraw(params.token, params.to, amount);
```

</details>

### [N-19] Event is missing `indexed` fields

Index event fields makes the field more quickly accessible to [off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each indexed field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

There are 2 instances:

- *Events.sol* ( [53-55](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Events.sol#L53-L55), [92-92](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Events.sol#L92-L92) ):

```solidity
53:     event Liquidate(
54:         uint256 indexed debtPositionId, uint256 minimumCollateralProfit, uint256 collateralRatio, LoanStatus loanStatus
55:     );

92:     event UpdateCreditPosition(uint256 indexed creditPositionId, address indexed lender, uint256 credit, bool forSale);
```

### [N-20] Function state mutability can be restricted to pure

Function state mutability can be restricted to pure

There is 1 instance:

- *NonTransferrableToken.sol* ( [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L50-L50) ):

```solidity
50:     function approve(address, uint256) public virtual override returns (bool) {
```

### [N-21] Imports could be organized more systematically

The contract's interface should be imported first, followed by each of the interfaces it uses, followed by all other files. The examples below do not follow this layout.

<details>
<summary>There are 8 instances (click to show):</summary>

- *Size.sol* ( [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L50-L50) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
50: import {IMulticall} from "@src/interfaces/IMulticall.sol";
```

- *SizeStorage.sol* ( [11-11](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeStorage.sol#L11-L11) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
11: import {IPriceFeed} from "@src/oracle/IPriceFeed.sol";
```

- *SizeView.sol* ( [23-23](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L23-L23) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
23: import {ISizeView} from "@src/interfaces/ISizeView.sol";
```

- *Deposit.sol* ( [6-6](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L6-L6) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
6: import {IWETH} from "@src/interfaces/IWETH.sol";
```

- *Initialize.sol* ( [13-13](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L13-L13) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
13: import {IPriceFeed} from "@src/oracle/IPriceFeed.sol";
```

- *UpdateConfig.sol* ( [12-12](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L12-L12) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
12: import {IPriceFeed} from "@src/oracle/IPriceFeed.sol";
```

- *PriceFeed.sol* ( [8-8](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L8-L8) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
8: import {IPriceFeed} from "./IPriceFeed.sol";
```

- *NonTransferrableScaledToken.sol* ( [6-6](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L6-L6) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
6: import {IERC20Metadata} from "@openzeppelin/contracts/interfaces/IERC20Metadata.sol";
```

</details>

### [N-22] Lib solady should be upgraded to a newer version

These contracts import contracts from lib solady, but they are not using [the latest version](https://github.com/Vectorized/solady/tags). Using the latest version ensures contract security with fixes for known vulnerabilities, access to the latest feature updates, and enhanced community support and engagement.

The imported version is `0.0.156`.

There is 1 instance:

- Global finding

### [N-23] Lines are too long

The [solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum line length of 120 characters. Lines of code that are longer than 120 should be wrapped.

<details>
<summary>There are 22 instances (click to show):</summary>

- *AccountingLibrary.sol* ( [70](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L70), [96](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L96), [134](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L134) ):

```solidity
70:             DebtPosition({borrower: borrower, futureValue: futureValue, dueDate: dueDate, liquidityIndexAtRepayment: 0});

96:     ///      If the credit amount is different, the existing credit position is reduced and a new credit position is created.

134:     ///        If the loan is in ACTIVE status, a debt reduction must be performed together with a credit reduction (See reduceDebtAndCredit).
```

- *CapsLibrary.sol* ( [12](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L12), [50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L50), [62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L62), [63](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L63) ):

```solidity
12:     /// @notice Validate that the increase in borrow aToken supply is less than or equal to the decrease in debt token supply

50:     ///      Due to rounding, the borrow aToken supply may be slightly less than the actual AToken supply, which is acceptable.

62:     ///      This safety mechanism prevents takers from matching orders that could not be withdrawn from the Variable Pool.

63:     ///        Nevertheless, the Variable Pool may still fail to withdraw the cash due to other factors (such as a pause, etc),
```

- *Math.sol* ( [45](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L45) ):

```solidity
45:     /// @dev If `value` is below the lowest value in `array` or above the highest value in `array`, the function returns (type(uint256).max, type(uint256).max)
```

- *Multicall.sol* ( [13](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L13) ):

```solidity
13: /// @author OpenZeppelin (https://raw.githubusercontent.com/OpenZeppelin/openzeppelin-contracts/v5.0.2/contracts/utils/Multicall.sol), Size
```

- *RiskLibrary.sol* ( [67](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L67), [99](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L99), [143](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L143) ):

```solidity
67:     /// @dev A credit position is self-liquidatable if the user is underwater and the loan is not REPAID (ie, ACTIVE or OVERDUE)

99:     /// @dev A debt position is liquidatable if the user is underwater and the loan is not REPAID (ie, ACTIVE or OVERDUE) or

143:             state.data.users[account].openingLimitBorrowCR // 0 by default, or user-defined if SetUserConfiguration has been used
```

- *BuyCreditMarket.sol* ( [80](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L80) ):

```solidity
80:             tenor = debtPosition.dueDate - block.timestamp; // positive since the credit position is transferrable, so the loan must be ACTIVE
```

- *Initialize.sol* ( [58](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L58) ):

```solidity
58: /// @dev The collateralToken (e.g. szETH), borrowAToken (e.g. szaUSDC), and debtToken (e.g. szDebt) are created in the `executeInitialize` function
```

- *Liquidate.sol* ( [85](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L85) ):

```solidity
85:         // if the loan is both underwater and overdue, the protocol fee related to underwater liquidations takes precedence
```

- *SelfLiquidate.sol* ( [47](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L47) ):

```solidity
47:             revert Errors.LIQUIDATION_NOT_AT_LOSS(params.creditPositionId, state.collateralRatio(debtPosition.borrower));
```

- *SellCreditMarket.sol* ( [84](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L84), [175](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L175) ):

```solidity
84:             tenor = debtPosition.dueDate - block.timestamp; // positive since the credit position is transferrable, so the loan must be ACTIVE

175:                     : Math.mulDivDown(creditPosition.credit, PERCENT - state.getSwapFeePercent(tenor), PERCENT + ratePerTenor),
```

- *UpdateConfig.sol* ( [34](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L34) ):

```solidity
34: ///      A `key` string is used to identify the configuration parameter to update and a `value` uint256 is used to set the new value
```

- *PriceFeed.sol* ( [14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L14), [18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L18) ):

```solidity
14: /// @notice A contract that provides the price of a `base` asset in terms of a `quote` asset, using an intermediate asset, scaled to 18 decimals

18: ///      _sequencerUptimeFeed: the sequencer uptime feed for supported L2s (https://docs.chain.link/data-feeds/l2-sequencer-feeds)
```

- *NonTransferrableScaledToken.sol* ( [18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L18) ):

```solidity
18: ///      Enables the owner to mint and burn scaled amounts. Emits the TransferUnscaled event representing the actual unscaled amount
```

</details>

### [N-24] Magic numbers should be replaced with constants

Magic numbers are hard-coded values in code that can make it difficult for developers and maintainers to understand the code, and can also cause confusion or errors. To improve the readability and maintainability of code, it is recommended to replace magic numbers with constants that have good readability.

There are 7 instances:

- *AccountingLibrary.sol* ( [33-33](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L33-L33) ):

```solidity
33:             debtTokenAmountWad, 10 ** state.oracle.priceFeed.decimals(), state.oracle.priceFeed.getPrice()
```

- *Math.sol* ( [32-32](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L32-L32), [58-58](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L58-L58) ):

```solidity
32:         return amount * 10 ** (18 - decimals);

58:             uint256 mid = (low + high) / 2;
```

- *Initialize.sol* ( [151-151](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L151-L151), [159-159](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L159-L159) ):

```solidity
151:         if (IERC20Metadata(d.underlyingCollateralToken).decimals() > 18) {

159:         if (IERC20Metadata(d.underlyingBorrowToken).decimals() > 18) {
```

- *PriceFeed.sol* ( [80-80](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L80-L80) ):

```solidity
80:             _getPrice(base, baseStalePriceInterval), 10 ** decimals, _getPrice(quote, quoteStalePriceInterval)
```

### [N-25] Expressions for constant values should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

There are 4 instances:

- *Size.sol* ( [54-54](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L54-L54), [55-55](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L55-L55), [56-56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L56-L56) ):

```solidity
54: bytes32 constant KEEPER_ROLE = keccak256("KEEPER_ROLE");

55: bytes32 constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

56: bytes32 constant BORROW_RATE_UPDATER_ROLE = keccak256("BORROW_RATE_UPDATER_ROLE");
```

- *LoanLibrary.sol* ( [11-11](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L11-L11) ):

```solidity
11: uint256 constant CREDIT_POSITION_ID_START = type(uint256).max / 2;
```

### [N-26] Functions not used internally could be marked external

If desired, sub contracts can change the visibility from `external` to `public` by [overriding](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding) the functions of their parents.

<details>
<summary>There are 12 instances (click to show):</summary>

- *Size.sol* ( [132-132](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L132-L132), [137-137](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L137-L137), [142-147](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L142-L147), [153-153](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L153-L153) ):

```solidity
132:     function pause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) {

137:     function unpause() public override(ISizeAdmin) onlyRole(PAUSER_ROLE) {

142:     function multicall(bytes[] calldata _data)
143:         public
144:         payable
145:         override(IMulticall)
146:         whenNotPaused
147:         returns (bytes[] memory results)

153:     function deposit(DepositParams calldata params) public payable override(ISize) whenNotPaused {
```

- *SizeView.sol* ( [179-179](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L179-L179) ):

```solidity
179:     function getSwapFee(uint256 cash, uint256 tenor) public view returns (uint256) {
```

- *NonTransferrableScaledToken.sol* ( [76-76](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L76-L76), [105-105](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L105-L105), [117-117](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L117-L117) ):

```solidity
76:     function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {

105:     function balanceOf(address account) public view override returns (uint256) {

117:     function totalSupply() public view override returns (uint256) {
```

- *NonTransferrableToken.sol* ( [42-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L42-L42), [46-46](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L46-L46), [50-50](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L50-L50), [54-54](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L54-L54) ):

```solidity
42:     function transfer(address to, uint256 value) public virtual override onlyOwner returns (bool) {

46:     function allowance(address, address spender) public view virtual override returns (uint256) {

50:     function approve(address, uint256) public virtual override returns (bool) {

54:     function decimals() public view virtual override returns (uint8) {
```

</details>

### [N-27] Functions should be named in mixedCase style

As the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.21/style-guide.html#function-names) suggests: functions should be named in mixedCase style.

<details>
<summary>There are 9 instances (click to show):</summary>

- *SizeView.sol* ( [141](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L141), [157](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L157) ):

```solidity
141:     function getBorrowOfferAPR(address borrower, uint256 tenor) external view returns (uint256) {

157:     function getLoanOfferAPR(address lender, uint256 tenor) external view returns (uint256) {
```

- *CapsLibrary.sol* ( [19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L19), [52](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L52) ):

```solidity
19:     function validateBorrowATokenIncreaseLteDebtTokenDecrease(

52:     function validateBorrowATokenCap(State storage state) external view {
```

- *OfferLibrary.sol* ( [48](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L48), [76](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L76) ):

```solidity
48:     function getAPRByTenor(LoanOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)

76:     function getAPRByTenor(BorrowOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)
```

- *RiskLibrary.sol* ( [140](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L140) ):

```solidity
140:     function validateUserIsNotBelowOpeningLimitBorrowCR(State storage state, address account) external view {
```

- *YieldCurveLibrary.sol* ( [87](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L87), [115](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L115) ):

```solidity
87:     function getAdjustedAPR(int256 apr, uint256 marketRateMultiplier, VariablePoolBorrowRateParams memory params)

115:     function getAPR(YieldCurve memory curveRelativeTime, VariablePoolBorrowRateParams memory params, uint256 tenor)
```

</details>

### [N-28] Variable names for `immutable`s should use UPPER_CASE_WITH_UNDERSCORES

For `immutable` variable names, each word should use all capital letters, with underscores separating each word (UPPER_CASE_WITH_UNDERSCORES)

There are 8 instances:

- *PriceFeed.sol* ( [29-29](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L29-L29), [30-30](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L30-L30), [31-31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L31-L31), [32-32](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L32-L32), [33-33](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L33-L33) ):

```solidity
29:     AggregatorV3Interface public immutable base;

30:     AggregatorV3Interface public immutable quote;

31:     AggregatorV3Interface internal immutable sequencerUptimeFeed;

32:     uint256 public immutable baseStalePriceInterval;

33:     uint256 public immutable quoteStalePriceInterval;
```

- *NonTransferrableScaledToken.sol* ( [20-20](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L20-L20), [21-21](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L21-L21) ):

```solidity
20:     IPool private immutable variablePool;

21:     IERC20Metadata private immutable underlyingToken;
```

- *NonTransferrableToken.sol* ( [15-15](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L15-L15) ):

```solidity
15:     uint8 internal immutable _decimals;
```

### [N-29] Constants should be put on the left side of comparisons

Putting constants on the left side of comparison statements is a best practice known as [Yoda conditions](https://en.wikipedia.org/wiki/Yoda_conditions).
Although solidity's static typing system prevents accidental assignments within conditionals, adopting this practice can improve code readability and consistency, especially when working across multiple languages.

<details>
<summary>There are 62 instances (click to show):</summary>

- *Size.sol* ( [181](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L181), [191](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L191) ):

```solidity
/// @audit put `RESERVED_ID` on the left
181:         if (params.creditPositionId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
191:         if (params.creditPositionId == RESERVED_ID) {
```

- *SizeView.sol* ( [180](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L180) ):

```solidity
/// @audit put `0` on the left
180:         if (tenor == 0) {
```

- *LoanLibrary.sol* ( [64](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L64), [72](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L72), [133](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L133), [156](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L156), [181](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L181) ):

```solidity
/// @audit put `DEBT_POSITION_ID_START` on the left
64:         return positionId >= DEBT_POSITION_ID_START && positionId < state.data.nextDebtPositionId;

/// @audit put `CREDIT_POSITION_ID_START` on the left
72:         return positionId >= CREDIT_POSITION_ID_START && positionId < state.data.nextCreditPositionId;

/// @audit put `0` on the left
133:         if (debtPosition.futureValue == 0) {

/// @audit put `0` on the left
156:         if (debt != 0) {

/// @audit put `0` on the left
181:         if (debtPositionFutureValue != 0) {
```

- *OfferLibrary.sol* ( [33](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L33), [53](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L53), [81](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L81) ):

```solidity
/// @audit put `0` on the left
33:         return self.maxDueDate == 0 && self.curveRelativeTime.isNull();

/// @audit put `0` on the left
53:         if (tenor == 0) revert Errors.NULL_TENOR();

/// @audit put `0` on the left
81:         if (tenor == 0) revert Errors.NULL_TENOR();
```

- *RiskLibrary.sol* ( [59](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L59) ):

```solidity
/// @audit put `0` on the left
59:         if (debt != 0) {
```

- *YieldCurveLibrary.sol* ( [39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L39), [39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L39), [39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L39), [51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L51), [51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L51), [51](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L51), [63](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L63), [92](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L92), [95](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L95) ):

```solidity
/// @audit put `0` on the left
39:         return self.tenors.length == 0 && self.aprs.length == 0 && self.marketRateMultipliers.length == 0;

/// @audit put `0` on the left
39:         return self.tenors.length == 0 && self.aprs.length == 0 && self.marketRateMultipliers.length == 0;

/// @audit put `0` on the left
39:         return self.tenors.length == 0 && self.aprs.length == 0 && self.marketRateMultipliers.length == 0;

/// @audit put `0` on the left
51:         if (self.tenors.length == 0 || self.aprs.length == 0 || self.marketRateMultipliers.length == 0) {

/// @audit put `0` on the left
51:         if (self.tenors.length == 0 || self.aprs.length == 0 || self.marketRateMultipliers.length == 0) {

/// @audit put `0` on the left
51:         if (self.tenors.length == 0 || self.aprs.length == 0 || self.marketRateMultipliers.length == 0) {

/// @audit put `0` on the left
63:         for (uint256 i = self.tenors.length; i != 0; i--) {

/// @audit put `0` on the left
92:         if (marketRateMultiplier == 0) {

/// @audit put `0` on the left
95:             params.variablePoolBorrowRateStaleRateInterval == 0
```

- *BuyCreditLimit.sol* ( [39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditLimit.sol#L39) ):

```solidity
/// @audit put `0` on the left
39:             if (params.maxDueDate == 0) {
```

- *BuyCreditMarket.sol* ( [56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L56), [133](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L133), [160](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L160), [163](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L163), [173](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L173), [179](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L179) ):

```solidity
/// @audit put `RESERVED_ID` on the left
56:         if (params.creditPositionId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
133:         if (params.creditPositionId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
160:                 maxCashAmountIn: params.creditPositionId == RESERVED_ID

/// @audit put `RESERVED_ID` on the left
163:                 maxCredit: params.creditPositionId == RESERVED_ID

/// @audit put `RESERVED_ID` on the left
173:                 maxCredit: params.creditPositionId == RESERVED_ID ? creditAmountOut : creditPosition.credit,

/// @audit put `RESERVED_ID` on the left
179:         if (params.creditPositionId == RESERVED_ID) {
```

- *Claim.sol* ( [40](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L40) ):

```solidity
/// @audit put `0` on the left
40:         if (creditPosition.credit == 0) {
```

- *Compensate.sol* ( [56](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L56), [98](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L98), [119](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L119), [140](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L140) ):

```solidity
/// @audit put `RESERVED_ID` on the left
56:         if (params.creditPositionToCompensateId == RESERVED_ID) {

/// @audit put `0` on the left
98:         if (amountToCompensate == 0) {

/// @audit put `RESERVED_ID` on the left
119:         if (params.creditPositionToCompensateId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
140:             exitCreditPositionId: params.creditPositionToCompensateId == RESERVED_ID
```

- *Deposit.sol* ( [41](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L41), [54](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L54), [59](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L59) ):

```solidity
/// @audit put `0` on the left
41:         if (msg.value != 0 && (msg.value != params.amount || params.token != address(state.data.weth))) {

/// @audit put `0` on the left
54:         if (params.amount == 0) {

/// @audit put `address(0)` on the left
59:         if (params.to == address(0)) {
```

- *Initialize.sol* ( [63](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L63), [91](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L91), [113](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L113), [121](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L121), [134](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L134), [148](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L148), [156](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L156), [164](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L164) ):

```solidity
/// @audit put `address(0)` on the left
63:         if (owner == address(0)) {

/// @audit put `address(0)` on the left
91:         if (f.feeRecipient == address(0)) {

/// @audit put `0` on the left
113:         if (r.minimumCreditBorrowAToken == 0) {

/// @audit put `0` on the left
121:         if (r.minTenor == 0) {

/// @audit put `address(0)` on the left
134:         if (o.priceFeed == address(0)) {

/// @audit put `address(0)` on the left
148:         if (d.underlyingCollateralToken == address(0)) {

/// @audit put `address(0)` on the left
156:         if (d.underlyingBorrowToken == address(0)) {

/// @audit put `address(0)` on the left
164:         if (d.variablePool == address(0)) {
```

- *SellCreditMarket.sol* ( [64](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L64), [138](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L138), [164](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L164), [173](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L173), [176](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L176), [184](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L184), [195](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L195) ):

```solidity
/// @audit put `RESERVED_ID` on the left
64:         if (params.creditPositionId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
138:         if (params.creditPositionId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
164:                 maxCredit: params.creditPositionId == RESERVED_ID ? creditAmountIn : creditPosition.credit,

/// @audit put `RESERVED_ID` on the left
173:                 maxCashAmountOut: params.creditPositionId == RESERVED_ID

/// @audit put `RESERVED_ID` on the left
176:                 maxCredit: params.creditPositionId == RESERVED_ID

/// @audit put `RESERVED_ID` on the left
184:         if (params.creditPositionId == RESERVED_ID) {

/// @audit put `RESERVED_ID` on the left
195:             exitCreditPositionId: params.creditPositionId == RESERVED_ID
```

- *UpdateConfig.sol* ( [100](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L100), [110](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L110) ):

```solidity
/// @audit put `0` on the left
100:                 state.feeConfig.swapFeeAPR != 0

/// @audit put `0` on the left
110:                 state.feeConfig.swapFeeAPR != 0
```

- *Withdraw.sol* ( [42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L42), [47](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L47) ):

```solidity
/// @audit put `0` on the left
42:         if (params.amount == 0) {

/// @audit put `address(0)` on the left
47:         if (params.to == address(0)) {
```

- *PriceFeed.sol* ( [43](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L43), [43](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L43), [48](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L48), [48](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L48), [68](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L68), [88](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L88) ):

```solidity
/// @audit put `address(0)` on the left
43:         if (_base == address(0) || _quote == address(0)) {

/// @audit put `address(0)` on the left
43:         if (_base == address(0) || _quote == address(0)) {

/// @audit put `0` on the left
48:         if (_baseStalePriceInterval == 0 || _quoteStalePriceInterval == 0) {

/// @audit put `0` on the left
48:         if (_baseStalePriceInterval == 0 || _quoteStalePriceInterval == 0) {

/// @audit put `1` on the left
68:             if (answer == 1) {

/// @audit put `0` on the left
88:         if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
```

- *NonTransferrableToken.sol* ( [22](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L22) ):

```solidity
/// @audit put `0` on the left
22:         if (decimals_ == 0) {
```

</details>

### [N-30] `else`-block not required

One level of nesting can be removed by not having an `else` block when the `if`-block always jumps at the end. For example:
```solidity
if (condition) {
    body1...
    return x;
} else {
    body2...
}
```
can be changed to:
```solidity
if (condition) {
    body1...
    return x;
}
body2...
```

<details>
<summary>There are 12 instances (click to show):</summary>

- *LoanLibrary.sol* ( [81-83](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L81-L83), [98-100](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L98-L100), [133-135](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L133-L135), [135-137](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L135-L137), [156-158](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L156-L158), [181-183](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L181-L183) ):

```solidity
81:         if (isDebtPositionId(state, debtPositionId)) {
82:             return state.data.debtPositions[debtPositionId];
83:         } else {

98:         if (isCreditPositionId(state, creditPositionId)) {
99:             return state.data.creditPositions[creditPositionId];
100:         } else {

133:         if (debtPosition.futureValue == 0) {
134:             return LoanStatus.REPAID;
135:         } else if (block.timestamp > debtPosition.dueDate) {

135:         } else if (block.timestamp > debtPosition.dueDate) {
136:             return LoanStatus.OVERDUE;
137:         } else {

156:         if (debt != 0) {
157:             return Math.mulDivDown(collateral, debtPosition.futureValue, debt);
158:         } else {

181:         if (debtPositionFutureValue != 0) {
182:             return Math.mulDivDown(debtPositionCollateral, creditPositionCredit, debtPositionFutureValue);
183:         } else {
```

- *Math.sol* ( [59-61](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L59-L61) ):

```solidity
59:             if (array[mid] == value) {
60:                 return (mid, mid);
61:             } else if (array[mid] < value) {
```

- *RiskLibrary.sol* ( [59-61](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L59-L61) ):

```solidity
59:         if (debt != 0) {
60:             return Math.mulDivDown(collateral, price, debtWad);
61:         } else {
```

- *YieldCurveLibrary.sol* ( [92-94](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L92-L94), [94-102](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L94-L102), [121-123](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L121-L123), [134-136](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L134-L136) ):

```solidity
92:         if (marketRateMultiplier == 0) {
93:             return SafeCast.toUint256(apr);
94:         } else if (

94:         } else if (
95:             params.variablePoolBorrowRateStaleRateInterval == 0
96:                 || (
97:                     block.timestamp - params.variablePoolBorrowRateUpdatedAt
98:                         > params.variablePoolBorrowRateStaleRateInterval
99:                 )
100:         ) {
101:             revert Errors.STALE_RATE(params.variablePoolBorrowRateUpdatedAt);
102:         } else {

121:         if (tenor < curveRelativeTime.tenors[0] || tenor > curveRelativeTime.tenors[length - 1]) {
122:             revert Errors.TENOR_OUT_OF_RANGE(tenor, curveRelativeTime.tenors[0], curveRelativeTime.tenors[length - 1]);
123:         } else {

134:                 if (y1 >= y0) {
135:                     return y0 + Math.mulDivDown(y1 - y0, tenor - x0, x1 - x0);
136:                 } else {
```

</details>

### [N-31] High cyclomatic complexity

Consider breaking down these blocks into more manageable units, by splitting things into utility functions, by reducing nesting, and by using early returns.

<details>
<summary>There is 1 instance (click to show):</summary>

- *BuyCreditMarket.sol* ( [51-115](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L51-L115) ):

```solidity
51:     function validateBuyCreditMarket(State storage state, BuyCreditMarketParams calldata params) external view {
52:         address borrower;
53:         uint256 tenor;
54: 
55:         // validate creditPositionId
56:         if (params.creditPositionId == RESERVED_ID) {
57:             borrower = params.borrower;
58:             tenor = params.tenor;
59: 
60:             // validate tenor
61:             if (tenor < state.riskConfig.minTenor || tenor > state.riskConfig.maxTenor) {
62:                 revert Errors.TENOR_OUT_OF_RANGE(tenor, state.riskConfig.minTenor, state.riskConfig.maxTenor);
63:             }
64:         } else {
65:             CreditPosition storage creditPosition = state.getCreditPosition(params.creditPositionId);
66:             DebtPosition storage debtPosition = state.getDebtPositionByCreditPositionId(params.creditPositionId);
67:             if (!state.isCreditPositionTransferrable(params.creditPositionId)) {
68:                 revert Errors.CREDIT_POSITION_NOT_TRANSFERRABLE(
69:                     params.creditPositionId,
70:                     state.getLoanStatus(params.creditPositionId),
71:                     state.collateralRatio(debtPosition.borrower)
72:                 );
73:             }
74:             User storage user = state.data.users[creditPosition.lender];
75:             if (user.allCreditPositionsForSaleDisabled || !creditPosition.forSale) {
...... OMITTED ......
91:         if (params.amount < state.riskConfig.minimumCreditBorrowAToken) {
92:             revert Errors.CREDIT_LOWER_THAN_MINIMUM_CREDIT(params.amount, state.riskConfig.minimumCreditBorrowAToken);
93:         }
94: 
95:         // validate deadline
96:         if (params.deadline < block.timestamp) {
97:             revert Errors.PAST_DEADLINE(params.deadline);
98:         }
99: 
100:         // validate minAPR
101:         uint256 apr = borrowOffer.getAPRByTenor(
102:             VariablePoolBorrowRateParams({
103:                 variablePoolBorrowRate: state.oracle.variablePoolBorrowRate,
104:                 variablePoolBorrowRateUpdatedAt: state.oracle.variablePoolBorrowRateUpdatedAt,
105:                 variablePoolBorrowRateStaleRateInterval: state.oracle.variablePoolBorrowRateStaleRateInterval
106:             }),
107:             tenor
108:         );
109:         if (apr < params.minAPR) {
110:             revert Errors.APR_LOWER_THAN_MIN_APR(apr, params.minAPR);
111:         }
112: 
113:         // validate exactAmountIn
114:         // N/A
115:     }
```

</details>

### [N-32] Typos

All typos should be corrected.

There are 2 instances:

- *UpdateConfig.sol* ( [78](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L78) ):

```solidity
/// @audit purposefuly
78:     ///      We purposefuly leave this function empty for documentation purposes
```

- *Withdraw.sol* ( [30](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L30) ):

```solidity
/// @audit validte
30:         // validte msg.sender
```

### [N-33] Unnecessary casting

Unnecessary castings can be removed.

<details>
<summary>There are 11 instances (click to show):</summary>

- *DepositTokenLibrary.sol* ( [24-24](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L24-L24), [37-37](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L37-L37) ):

```solidity
/// @audit IERC20Metadata(state.data.underlyingCollateralToken)
24:         IERC20Metadata underlyingCollateralToken = IERC20Metadata(state.data.underlyingCollateralToken);

/// @audit IERC20Metadata(state.data.underlyingCollateralToken)
37:         IERC20Metadata underlyingCollateralToken = IERC20Metadata(state.data.underlyingCollateralToken);
```

- *Initialize.sol* ( [241-241](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L241-L241), [242-242](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L242-L242), [243-243](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L243-L243), [249-249](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L249-L249), [250-250](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L250-L250), [251-251](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L251-L251), [255-255](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L255-L255), [256-256](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L256-L256), [257-257](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L257-L257) ):

```solidity
/// @audit IERC20Metadata(state.data.underlyingCollateralToken)
241:             string.concat("Size ", IERC20Metadata(state.data.underlyingCollateralToken).name()),

/// @audit IERC20Metadata(state.data.underlyingCollateralToken)
242:             string.concat("sz", IERC20Metadata(state.data.underlyingCollateralToken).symbol()),

/// @audit IERC20Metadata(state.data.underlyingCollateralToken)
243:             IERC20Metadata(state.data.underlyingCollateralToken).decimals()

/// @audit IERC20Metadata(state.data.underlyingBorrowToken)
249:             string.concat("Size Scaled ", IERC20Metadata(state.data.underlyingBorrowToken).name()),

/// @audit IERC20Metadata(state.data.underlyingBorrowToken)
250:             string.concat("sza", IERC20Metadata(state.data.underlyingBorrowToken).symbol()),

/// @audit IERC20Metadata(state.data.underlyingBorrowToken)
251:             IERC20Metadata(state.data.underlyingBorrowToken).decimals()

/// @audit IERC20Metadata(state.data.underlyingBorrowToken)
255:             string.concat("Size Debt ", IERC20Metadata(state.data.underlyingBorrowToken).name()),

/// @audit IERC20Metadata(state.data.underlyingBorrowToken)
256:             string.concat("szDebt", IERC20Metadata(state.data.underlyingBorrowToken).symbol()),

/// @audit IERC20Metadata(state.data.underlyingBorrowToken)
257:             IERC20Metadata(state.data.underlyingBorrowToken).decimals()
```

</details>

### [N-34] Unused function parameters

Function parameters that are defined but not used may be logical errors and need to be checked to see if any logic is missing.
If the parameter is not really needed, it should be removed, otherwise it will repeatedly cause confusion and make code maintenance difficult.
If the parameter cannot be removed directly (for example, if it is an override function), its name should be removed and some comment can be appended.

There is 1 instance:

- *Size.sol* ( [107-107](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L107-L107) ):

```solidity
/// @audit newImplementation
107:     function _authorizeUpgrade(address newImplementation) internal override onlyRole(DEFAULT_ADMIN_ROLE) {}
```

### [N-35] Use delete instead of assigning values to `false`

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

There is 1 instance:

- *Multicall.sol* ( [44-44](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L44-L44) ):

```solidity
44:         state.data.isMulticall = false;
```

### [N-36] Consider using `delete` rather than assigning zero to clear values

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

There are 2 instances:

- *Math.sol* ( [52-52](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L52-L52) ):

```solidity
52:         low = 0;
```

- *LiquidateWithReplacement.sol* ( [151-151](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L151-L151) ):

```solidity
151:         debtPosition.liquidityIndexAtRepayment = 0;
```

### [N-37] Use the latest Solidity version

Upgrading to the [latest solidity version](https://github.com/ethereum/solc-js/tags) (0.8.19 for L2s) can optimize gas usage, take advantage of new features and improve overall contract efficiency. Where possible, based on compatibility requirements, it is recommended to use newer/latest solidity version to take advantage of the latest optimizations and features.

<details>
<summary>There are 34 instances (click to show):</summary>

- *Size.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SizeStorage.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeStorage.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SizeView.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SizeViewData.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeViewData.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *AccountingLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *CapsLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/CapsLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *DepositTokenLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/DepositTokenLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Errors.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Errors.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Events.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Events.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *LoanLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/LoanLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Math.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Math.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Multicall.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *OfferLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *RiskLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/RiskLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *YieldCurveLibrary.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/YieldCurveLibrary.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *BuyCreditLimit.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditLimit.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *BuyCreditMarket.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Claim.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Claim.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Compensate.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Compensate.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Deposit.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Deposit.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Initialize.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Liquidate.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *LiquidateWithReplacement.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/LiquidateWithReplacement.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Repay.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Repay.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SelfLiquidate.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SelfLiquidate.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SellCreditLimit.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditLimit.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SellCreditMarket.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *SetUserConfiguration.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *UpdateConfig.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *Withdraw.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Withdraw.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *IPriceFeed.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/IPriceFeed.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *PriceFeed.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *NonTransferrableScaledToken.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *NonTransferrableToken.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

</details>

### [N-38] Using underscore at the end of variable name

The use of the underscore at the end of the variable name is unusual, consider refactoring it.

<details>
<summary>There are 10 instances (click to show):</summary>

- *NonTransferrableScaledToken.sol* ( [26-26](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L26-L26), [27-27](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L27-L27), [28-28](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L28-L28), [29-29](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L29-L29), [30-30](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L30-L30), [31-31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L31-L31) ):

```solidity
/// @audit variablePool_
26:         IPool variablePool_,

/// @audit underlyingToken_
27:         IERC20Metadata underlyingToken_,

/// @audit owner_
28:         address owner_,

/// @audit name_
29:         string memory name_,

/// @audit symbol_
30:         string memory symbol_,

/// @audit decimals_
31:         uint8 decimals_
```

- *NonTransferrableToken.sol* ( [18-18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L18-L18), [18-18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L18-L18), [18-18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L18-L18), [18-18](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L18-L18) ):

```solidity
/// @audit owner_
18:     constructor(address owner_, string memory name_, string memory symbol_, uint8 decimals_)

/// @audit name_
18:     constructor(address owner_, string memory name_, string memory symbol_, uint8 decimals_)

/// @audit symbol_
18:     constructor(address owner_, string memory name_, string memory symbol_, uint8 decimals_)

/// @audit decimals_
18:     constructor(address owner_, string memory name_, string memory symbol_, uint8 decimals_)
```

</details>

### [N-39] Use a struct to encapsulate multiple function parameters

If a function has too many parameters, replacing them with a struct can improve code readability and maintainability, increase reusability, and reduce the likelihood of errors when passing the parameters.

<details>
<summary>There are 3 instances (click to show):</summary>

- *Size.sol* ( [87-93](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L87-L93) ):

```solidity
87:     function initialize(
88:         address owner,
89:         InitializeFeeConfigParams calldata f,
90:         InitializeRiskConfigParams calldata r,
91:         InitializeOracleParams calldata o,
92:         InitializeDataParams calldata d
93:     ) external initializer {
```

- *PriceFeed.sol* ( [36-42](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L36-L42) ):

```solidity
36:     constructor(
37:         address _base,
38:         address _quote,
39:         address _sequencerUptimeFeed,
40:         uint256 _baseStalePriceInterval,
41:         uint256 _quoteStalePriceInterval
42:     ) {
```

- *NonTransferrableScaledToken.sol* ( [25-32](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L25-L32) ):

```solidity
25:     constructor(
26:         IPool variablePool_,
27:         IERC20Metadata underlyingToken_,
28:         address owner_,
29:         string memory name_,
30:         string memory symbol_,
31:         uint8 decimals_
32:     ) NonTransferrableToken(owner_, name_, symbol_, decimals_) {
```

</details>

### [N-40] Returning a struct instead of a bunch of variables is better

If a function returns [too many variables](https://docs.soliditylang.org/en/v0.8.21/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

There are 2 instances:

- *Size.sol* ( [229-235](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L229-L235) ):

```solidity
229:     function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
230:         external
231:         payable
232:         override(ISize)
233:         whenNotPaused
234:         onlyRole(KEEPER_ROLE)
235:         returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
```

- *SizeView.sol* ( [133-133](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeView.sol#L133-L133) ):

```solidity
133:     function getPositionsCount() external view returns (uint256, uint256) {
```

### [N-41] Events that mark critical parameter changes should contain both the old and the new value

This should especially be done if the new value is not required to be different from the old value.

There is 1 instance:

- *UpdateConfig.sol* ( [147-147](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/UpdateConfig.sol#L147-L147) ):

```solidity
147:         emit Events.UpdateConfig(params.key, params.value);
```

### [N-42] Contract variables should have comments

Consider adding some comments on non-public contract variables to explain what they are supposed to do. This will help for future code reviews.

There are 5 instances:

- *SizeStorage.sol* ( [114-114](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeStorage.sol#L114-L114) ):

```solidity
114:     State internal state;
```

- *PriceFeed.sol* ( [31-31](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L31-L31) ):

```solidity
31:     AggregatorV3Interface internal immutable sequencerUptimeFeed;
```

- *NonTransferrableScaledToken.sol* ( [20-20](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L20-L20), [21-21](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L21-L21) ):

```solidity
20:     IPool private immutable variablePool;

21:     IERC20Metadata private immutable underlyingToken;
```

- *NonTransferrableToken.sol* ( [15-15](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L15-L15) ):

```solidity
15:     uint8 internal immutable _decimals;
```

### [N-43] Don't define functions with the same name in a contract

In Solidity, while function overriding allows for functions with the same name to coexist, it is advisable to avoid this practice to enhance code readability and maintainability. Having multiple functions with the same name, even with different parameters or in inherited contracts, can cause confusion and increase the likelihood of errors during development, testing, and debugging. Using distinct and descriptive function names not only clarifies the purpose and behavior of each function, but also helps prevent unintended function calls or incorrect overriding. By adopting a clear and consistent naming convention, developers can create more comprehensible and maintainable smart contracts.

There are 3 instances:

- *OfferLibrary.sol* ( [39](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L39), [76](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L76), [90](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/OfferLibrary.sol#L90) ):

```solidity
/// @audit Different function with same name found on line 32
39:     function isNull(BorrowOffer memory self) internal pure returns (bool) {

/// @audit Different function with same name found on line 48
76:     function getAPRByTenor(BorrowOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)

/// @audit Different function with same name found on line 62
90:     function getRatePerTenor(BorrowOffer memory self, VariablePoolBorrowRateParams memory params, uint256 tenor)
```

### [N-44] Interface files should not use fixed compiler versions

Interfaces should not use fixed compiler versions, since they may be used by projects using a different version.

There are 2 instances:

- *SizeViewData.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/SizeViewData.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

- *IPriceFeed.sol* ( [2](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/IPriceFeed.sol#L2) ):

```solidity
2: pragma solidity 0.8.23;
```

### [N-45] Too long functions should be refactored

Functions with too many lines are difficult to understand. It is recommended to refactor complex functions into multiple shorter and easier to understand functions.

There are 2 instances:

- *BuyCreditMarket.sol* ( [121](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/BuyCreditMarket.sol#L121) ):

```solidity
/// @audit 77 lines
121:     function executeBuyCreditMarket(State storage state, BuyCreditMarketParams memory params)
```

- *SellCreditMarket.sol* ( [127](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SellCreditMarket.sol#L127) ):

```solidity
/// @audit 77 lines
127:     function executeSellCreditMarket(State storage state, SellCreditMarketParams calldata params)
```

### [N-46] Missing event for critical changes

Events should be emitted when critical changes are made to the contracts.

There are 2 instances:

- *Initialize.sol* ( [193-202](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L193-L202), [207-217](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Initialize.sol#L207-L217) ):

```solidity
193:     function executeInitializeFeeConfig(State storage state, InitializeFeeConfigParams memory f) internal {
194:         state.feeConfig.swapFeeAPR = f.swapFeeAPR;
195:         state.feeConfig.fragmentationFee = f.fragmentationFee;
196: 
197:         state.feeConfig.liquidationRewardPercent = f.liquidationRewardPercent;
198:         state.feeConfig.overdueCollateralProtocolPercent = f.overdueCollateralProtocolPercent;
199:         state.feeConfig.collateralProtocolPercent = f.collateralProtocolPercent;
200: 
201:         state.feeConfig.feeRecipient = f.feeRecipient;
202:     }

207:     function executeInitializeRiskConfig(State storage state, InitializeRiskConfigParams memory r) internal {
208:         state.riskConfig.crOpening = r.crOpening;
209:         state.riskConfig.crLiquidation = r.crLiquidation;
210: 
211:         state.riskConfig.minimumCreditBorrowAToken = r.minimumCreditBorrowAToken;
212: 
213:         state.riskConfig.borrowATokenCap = r.borrowATokenCap;
214: 
215:         state.riskConfig.minTenor = r.minTenor;
216:         state.riskConfig.maxTenor = r.maxTenor;
217:     }
```

### [N-47] Consider adding emergency-stop functionality

Adding a way to quickly halt protocol functionality in an emergency, rather than having to pause individual contracts one-by-one, will make in-progress hack mitigation faster and much less stressful.

There are 3 instances:

- *Size.sol* ( [62-62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L62-L62) ):

```solidity
62: contract Size is ISize, SizeView, Initializable, AccessControlUpgradeable, PausableUpgradeable, UUPSUpgradeable {
```

- *NonTransferrableScaledToken.sol* ( [19-19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L19-L19) ):

```solidity
19: contract NonTransferrableScaledToken is NonTransferrableToken {
```

- *NonTransferrableToken.sol* ( [14-14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L14-L14) ):

```solidity
14: contract NonTransferrableToken is Ownable, ERC20 {
```

### [N-48] Use the Modern Upgradeable Contract Paradigm

Modern smart contract development often employs upgradeable contract structures, utilizing proxy patterns like OpenZeppelin’s Upgradeable Contracts. This paradigm separates logic and state, allowing developers to amend and enhance the contract's functionality without altering its state or the deployed contract address. Transitioning to this approach enhances long-term maintainability.
Resolution: Adopt a well-established proxy pattern for upgradeability, ensuring proper initialization and employing transparent proxies to mitigate potential risks. Embrace comprehensive testing and audit practices, particularly when updating contract logic, to ensure state consistency and security are preserved across upgrades. This ensures your contract remains robust and adaptable to future requirements.

There are 3 instances:

- *PriceFeed.sol* ( [24](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/oracle/PriceFeed.sol#L24) ):

```solidity
24: contract PriceFeed is IPriceFeed {
```

- *NonTransferrableScaledToken.sol* ( [19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L19) ):

```solidity
19: contract NonTransferrableScaledToken is NonTransferrableToken {
```

- *NonTransferrableToken.sol* ( [14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L14) ):

```solidity
14: contract NonTransferrableToken is Ownable, ERC20 {
```

### [N-49] Large or complicated code bases should implement invariant tests

This includes: large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts.
Invariant fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold.
Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and invariant fuzzers may help significantly.

There is 1 instance:

- Global finding

### [N-50] The default value is manually set when it is declared

In instances where a new variable is defined, there is no need to set it to it's default value.

There are 5 instances:

- *AccountingLibrary.sol* ( [238-238](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/AccountingLibrary.sol#L238-L238) ):

```solidity
238:         uint256 maxCashAmountOutFragmentation = 0;
```

- *Multicall.sol* ( [33-33](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/Multicall.sol#L33-L33) ):

```solidity
33:         for (uint256 i = 0; i < data.length; i++) {
```

- *Liquidate.sol* ( [92-92](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/Liquidate.sol#L92-L92) ):

```solidity
92:         uint256 protocolProfitCollateralToken = 0;
```

- *SetUserConfiguration.sol* ( [48-48](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L48-L48), [69-69](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/libraries/actions/SetUserConfiguration.sol#L69-L69) ):

```solidity
48:         for (uint256 i = 0; i < params.creditPositionIds.length; i++) {

69:         for (uint256 i = 0; i < params.creditPositionIds.length; i++) {
```

### [N-51] Contracts should have all `public`/`external` functions exposed by `interface`s

All `external`/`public` functions should extend an `interface`. This is useful to ensure that the whole API is extracted and can be more easily integrated by other projects.

There are 3 instances:

- *Size.sol* ( [62](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/Size.sol#L62) ):

```solidity
62: contract Size is ISize, SizeView, Initializable, AccessControlUpgradeable, PausableUpgradeable, UUPSUpgradeable {
```

- *NonTransferrableScaledToken.sol* ( [19](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableScaledToken.sol#L19) ):

```solidity
19: contract NonTransferrableScaledToken is NonTransferrableToken {
```

- *NonTransferrableToken.sol* ( [14](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/./src/token/NonTransferrableToken.sol#L14) ):

```solidity
14: contract NonTransferrableToken is Ownable, ERC20 {
```
