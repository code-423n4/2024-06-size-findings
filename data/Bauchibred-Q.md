# QA Report for **Size**

## Table of Contents

| Issue ID                                                                                                                                  | Description                                                                                                                |
| ----------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| [QA-01](#qa-01-grace-period-duration-seems-to-be-excessive-after-sequencer-recovery)                                                      | Grace Period duration seems to be excessive after sequencer recovery                                                       |
| [QA-02](#qa-02-variable-rate-can-be-set-even-when-protocol-is-not-paused)                                                                 | Variable rate can be set even when protocol is not paused                                                                  |
| [QA-03](#qa-03-a-position-thats-liquidatable/underwater-might-be-pronounced-as-non-liquidatable-due-to-the-circuit-breakers-on-chainlink) | A position that's liquidatable/underwater might be pronounced as non-liquidatable due to the circuit breakers on Chainlink |
| [QA-04](#qa-04-crliquidation-is-allowed-to-be-set-as-high-as-100%)                                                                        | `crLiquidation` is allowed to be set as high as 100%                                                                       |
| [QA-05](#qa-05-borrow_rate_updater_role-can-easily-affect-all-borrowed-positions-and-make-new-borrows-for-~-0)                            | `BORROW_RATE_UPDATER_ROLE` can easily affect all borrowed positions and make new borrows for ~ 0                           |
| [QA-06](#qa-06-if-an-oracle-goes-down-liquidations-are-outrightly-bricked/dosd)                                                           | If an oracle goes down, liquidations are outrightly bricked/DOS'd                                                          |
| [QA-07](#qa-07-setters-should-always-have-equality-checkers)                                                                              | Setters should always have equality checkers                                                                               |
| [QA-08](#qa-08-fix-typos)                                                                                                                 | Fix typos                                                                                                                  |
| [QA-09](#qa-09-a-position-thats-liquidatable/underwater-might-never-get-liquidated-due-to-unhandled-chainlink-reverts)                    | A position that's liquidatable/underwater might never get liquidated due to Unhandled Chainlink reverts                    |
| [QA-10](#qa-10-fix-stale-docs-pointing-to-deprecated-contracts)                                                                           | Fix stale docs pointing to deprecated contracts                                                                            |
| [QA-11](#qa-11-usdc-getting-paused-by-circle-would-brick-size)                                                                            | USDC getting paused by Circle would brick Size                                                                             |
| [QA-12](#qa-12-size-could-be-dosd-when-some-new-tokens-are-integrated)                                                                    | Size could be DOS'd when some new tokens are integrated                                                                    |
| [QA-13](#qa-13-documentation-should-talk-about-the-right-and-valid-assets)                                                                | Documentation should talk about the right and valid assets                                                                 |

## QA-01 Grace Period duration seems to be excessive after sequencer recovery

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L63-L82):

```solidity
function getPrice() external view returns (uint256) {
    if (address(sequencerUptimeFeed) != address(0)) {
        // slither-disable-next-line unused-return
        (, int256 answer, uint256 startedAt,,) = sequencerUptimeFeed.latestRoundData();

        if (answer == 1) {
            // sequencer is down
            revert Errors.SEQUENCER_DOWN();
        }

        if (block.timestamp - startedAt <= GRACE_PERIOD_TIME) {
            // time since up
            revert Errors.GRACE_PERIOD_NOT_OVER();
        }
    }

    return Math.mulDivDown(
        _getPrice(base, baseStalePriceInterval), 10 ** decimals, _getPrice(quote, quoteStalePriceInterval)
    );
}
```

The protocol incorporates a grace period checker for when the sequencer comes back up. However, this grace period duration is set too high:

```solidity
uint256 private constant GRACE_PERIOD_TIME = 3600;
```

Given that the `getPrice` function is used for liquidations and other core functionalities, blocking it for an hour can lead to the accumulation of bad debts and potentially cause a denial of service for essential protocol operations.

### Impact

The protocol risks entering an undesirable state due to the prolonged grace period, allowing bad debt positions to accumulate even after the sequencer is back online.

> Note: Even if transactions are passed via the delayed inbox, the sequencer includes the message from the inbox only after a delay of approximately 10 minutes when it comes back up. This ensures that the transaction/message’s arrival is not affected by a reorganization of the base layer chain. Therefore, the current implementation of a 1-hour grace period introduces significant risk to the protocol, whereas a duration of 10-15 minutes would suffice.

### Recommended Mitigation Steps

Consider shortening the grace period duration to 10-15 minutes to ensure that reorgs and related issues are resolved without exposing the protocol to excessive risk.

### Additional Note

After noticing that this could lead to an accumulation of bad debt for many users allowing the protocol to be in a bad position, a more detailed report has been submitted as `2-risk (med)`.

## QA-02 Variable rate can be set even when protocol is not paused

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L120-L129

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

This function is used to set the variable rate, and is directly used in other core functions across Size, issue however is that this function lacks the `whenNotPaused` modifier, which then means that in the case where protocol is paused this function could still be used to update the variable rate, now looking at the user available functionalities in Size.sol here: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L140-L263, we would notice that all these functionalities are only available in the case where the protocol is unpaused, i.e they all use the `whenNotPaused` modifier that the `setVariablePoolBorrowRate` lacks, this then leads us to a situation where:

- An emergency occurs.
- Protocol gets paused.
- While being paused, variable rates get updated.
- Apr calculations are now completely changed depending on the new value of the variable rates.
- User would like to leave protocol but they can't. due to the `whenNotPaused` modifier.

### Impact

Slightly QA due to this being admin backed, however this is an expected series of execution which would be unfair to the user that would rather leave than keep on integrating with Size.

### Recommended Mitigation Steps

Consider applying these changes:

```diff
    function setVariablePoolBorrowRate(uint128 borrowRate)
        external
        override(ISizeAdmin)
        onlyRole(BORROW_RATE_UPDATER_ROLE)
+        whenNotPaused
    {
        uint128 oldBorrowRate = state.oracle.variablePoolBorrowRate;
        state.oracle.variablePoolBorrowRate = borrowRate;
        state.oracle.variablePoolBorrowRateUpdatedAt = uint64(block.timestamp);
        emit Events.VariablePoolBorrowRateUpdated(oldBorrowRate, borrowRate);
    }
```

This way even if the rates gets changed to what a user doesn't like they can just immediately curb their integration.

## QA-03 A position that's liquidatable/underwater might be pronounced as non-liquidatable due to the circuit breakers on Chainlink

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84-L94

```solidity
    function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
        // slither-disable-next-line unused-return
        (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();//@audit check for chainlink issues like frontrunning and the rest

        if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
        if (block.timestamp - updatedAt > stalePriceInterval) {
            revert Errors.STALE_PRICE(address(aggregator), updatedAt);
        }

        return SafeCast.toUint256(price);
    }
```

This function is called, via the external getPrice(), that gets queried when [checking if a debt position is liquidiatable](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L111), i.e from the collateral ratio check here https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L121-L123

```solidity
    function isUserUnderwater(State storage state, address account) public view returns (bool) {
        return collateralRatio(state, account) < state.riskConfig.crLiquidation;
    }
```

i.e https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L52-L63

```solidity
    function collateralRatio(State storage state, address account) public view returns (uint256) {
        uint256 collateral = state.data.collateralToken.balanceOf(account);
        uint256 debt = state.data.debtToken.balanceOf(account);
        uint256 debtWad = Math.amountToWad(debt, state.data.underlyingBorrowToken.decimals());
        //@audit
        uint256 price = state.oracle.priceFeed.getPrice();

        if (debt != 0) {
            return Math.mulDivDown(collateral, price, debtWad);
        } else {
            return type(uint256).max;
        }
```

Evidently, Chainlink's `latestRoundData` is being queried to get the price, but no min/max checks are applied, leading to contracts working with flawed pricing.

### Impact

If the prices ever go over the min/max boundary then protocol is going to ingest heavily inflated/deflated pricing data as the prices returned would be wrong.

Considering protocol plans to support a lot of tokens this would to be a problem as multiple tokens would have their source feed's aggregators with this min/max circuit breakers and as such it should be checked for an asset that has it, since this has quite a high impact with a low likelihood, but would be key to note that this has happened before with `LUNA`.

### Recommended Mitigation Steps

Consider integrating min/max checkers and ensure that the prices returned are not on these boundaries, or implement a fallback oracle that gets queried when the prices returned are at these boundaries

## QA-04 `crLiquidation` is allowed to be set as high as 100%

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/UpdateConfig.sol#L86-L148

```solidity
    function executeUpdateConfig(State storage state, UpdateConfigParams calldata params) external {
        if (Strings.equal(params.key, "crOpening")) {
            state.riskConfig.crOpening = params.value;
        } else if (Strings.equal(params.key, "crLiquidation")) {
            if (params.value >= state.riskConfig.crLiquidation) {
                revert Errors.INVALID_COLLATERAL_RATIO(params.value);
            }
            state.riskConfig.crLiquidation = params.value;
        } else if (Strings.equal(params.key, "minimumCreditBorrowAToken")) {
            state.riskConfig.minimumCreditBorrowAToken = params.value;
        } else if (Strings.equal(params.key, "borrowATokenCap")) {
            state.riskConfig.borrowATokenCap = params.value;
        } else if (Strings.equal(params.key, "minTenor")) {
            if (
                state.feeConfig.swapFeeAPR != 0
                    && params.value >= Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)
            ) {
                revert Errors.VALUE_GREATER_THAN_MAX(
                    params.value, Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)
                );
            }
            state.riskConfig.minTenor = params.value;
        } else if (Strings.equal(params.key, "maxTenor")) {
            if (
                state.feeConfig.swapFeeAPR != 0
                    && params.value >= Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)
            ) {
                revert Errors.VALUE_GREATER_THAN_MAX(
                    params.value, Math.mulDivDown(YEAR, PERCENT, state.feeConfig.swapFeeAPR)
                );
            }
            state.riskConfig.maxTenor = params.value;
        } else if (Strings.equal(params.key, "swapFeeAPR")) {
            if (params.value >= Math.mulDivDown(PERCENT, YEAR, state.riskConfig.maxTenor)) {
                revert Errors.VALUE_GREATER_THAN_MAX(
                    params.value, Math.mulDivDown(PERCENT, YEAR, state.riskConfig.maxTenor)
                );
            }
            state.feeConfig.swapFeeAPR = params.value;
        } else if (Strings.equal(params.key, "fragmentationFee")) {
            state.feeConfig.fragmentationFee = params.value;
        } else if (Strings.equal(params.key, "liquidationRewardPercent")) {
            state.feeConfig.liquidationRewardPercent = params.value;
        } else if (Strings.equal(params.key, "overdueCollateralProtocolPercent")) {
            state.feeConfig.overdueCollateralProtocolPercent = params.value;
        } else if (Strings.equal(params.key, "collateralProtocolPercent")) {
            state.feeConfig.collateralProtocolPercent = params.value;
        } else if (Strings.equal(params.key, "feeRecipient")) {
            state.feeConfig.feeRecipient = address(uint160(params.value));
        } else if (Strings.equal(params.key, "priceFeed")) {
            state.oracle.priceFeed = IPriceFeed(address(uint160(params.value)));
        } else if (Strings.equal(params.key, "variablePoolBorrowRateStaleRateInterval")) {
            state.oracle.variablePoolBorrowRateStaleRateInterval = uint64(params.value);
        } else {
            revert Errors.INVALID_KEY(params.key);
        }

        Initialize.validateInitializeFeeConfigParams(feeConfigParams(state));
        Initialize.validateInitializeRiskConfigParams(riskConfigParams(state));
        Initialize.validateInitializeOracleParams(oracleParams(state));

        emit Events.UpdateConfig(params.key, params.value);
    }
```

This function is used to apply all the updates for the configuration of the protocol, however it doesn't have any checkers for a max value for `crLiquidation`.

In the case this value gets set to `100%` this means that all current positions can be liquidated and so is a heavy position that would be opened as they could be a front/back run of opening the positions by the owners to liquidate said position.

### Impact

QA... Heavily depends on the admin being a _tad malicious_, however following Code4rena's update to QA rules, centralization risks should be flagged in an audit and tagged in the QA report.

However this could lead to a situation where every single position is immediately liquidatable.

### Recommended Mitigation Steps

Consider having a max value, or a waiting duration before the change kicks in.

## QA-05 `BORROW_RATE_UPDATER_ROLE` can easily affect all borrowed positions and make new borrows for ~ 0

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L119-L129

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

This function is used to set the variable rate, which directly translates to how the loan would be positioned on the yield curve.

Considering this function is admin backed, this then allows for a _malicious_ admin to set the value to what they want backrun the tx by placing a loan/ liquidating any position and then set it back to the normal value

### Impact

QA, centralisation risk, considering new Code4rena QA rules, this should be flagged in an audit since the `BORROW_RATE_UPDATER_ROLE` can easily affect all borrowed positions and make new borrows for ~ 0

### Recommended Mitigation Steps

N/A

## QA-06 If an oracle goes down, liquidations are outrightly bricked/DOS'd

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84-L94

```solidity
    function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
        // slither-disable-next-line unused-return
        (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();//@audit check for chainlink issues like frontrunning and the rest

        if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
        if (block.timestamp - updatedAt > stalePriceInterval) {
            revert Errors.STALE_PRICE(address(aggregator), updatedAt);
        }

        return SafeCast.toUint256(price);
    }
```

Evidently, Chainlink's `latestRoundData` is being queried to get the price, issue here however is that there is no fallback oracle mechanism, that's to say if the collateral's oracle goes down, which would not be a new thing with Chainlink then access to liquidations would always fail, because before liquidating there is a need to see [if the debt positions is liquidiatable](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L111), i.e from the collateral ratio check here https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L121-L123

```solidity
    function isUserUnderwater(State storage state, address account) public view returns (bool) {
        return collateralRatio(state, account) < state.riskConfig.crLiquidation;
    }
```

Which would fail in the query to `collateralRatio` since the attempt to get the price reverts in the @audit hinted instance below https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L52-L63

```solidity
    function collateralRatio(State storage state, address account) public view returns (uint256) {
        uint256 collateral = state.data.collateralToken.balanceOf(account);
        uint256 debt = state.data.debtToken.balanceOf(account);
        uint256 debtWad = Math.amountToWad(debt, state.data.underlyingBorrowToken.decimals());
        //@audit
        uint256 price = state.oracle.priceFeed.getPrice();

        if (debt != 0) {
            return Math.mulDivDown(collateral, price, debtWad);
        } else {
            return type(uint256).max;
        }
```

### Impact

Protocol would be forced to hold on to bad debt since they can't be liquidated.

### Recommended Mitigation Steps

Consider integrating a trusted fallback oracle for all collaterals, this could be an oracle provider like Pyth for eg.

## QA-07 Setters should always have equality checkers

### Proof of Concept

Take a look atMultiple instances in protocol, one of which would be https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/Size.sol#L119-L129

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

This function is used to set the variable rate, however there are no checks to ensure that the current value of `oldBorrowRate` != new `borrowRate`, which would then lead to an unnecessary update in the case where `oldBorrowRate` = new `borrowRate`

### Impact

QA

### Recommended Mitigation Steps

Consider applying equality checkers.

## QA-08 Fix typos

### Proof of Concept

Multiple instances in protocol, one of which would be https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/interfaces/ISizeAdmin.sol#L18-L24

```solidity

    /// @notice Sets the variable borrow rate
    ///         Only callabe by the BORROW_RATE_UPDATER_ROLE
    /// @dev The variable pool borrow rate cannot be used if the variablePoolBorrowRateStaleRateInterval is set to zero
    /// @param borrowRate The new borrow rate
    function setVariablePoolBorrowRate(uint128 borrowRate) external;

```

Evidently, `    ///         Only callable by the BORROW_RATE_UPDATER_ROLE` meant to read `    ///         Only CALLABLE by the BORROW_RATE_UPDATER_ROLE` _a missing l_

### Impact

Bad code documentation.

### Recommended Mitigation Steps

Consider fixing all the typos in code, multiple other instances should be taken care of too.

## QA-09 A position that's liquidatable/underwater might never get liquidated due to Unhandled Chainlink reverts

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L84-L94

```solidity
    function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
        // slither-disable-next-line unused-return
        (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();//@audit check for chainlink issues like frontrunning and the rest

        if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
        if (block.timestamp - updatedAt > stalePriceInterval) {
            revert Errors.STALE_PRICE(address(aggregator), updatedAt);
        }

        return SafeCast.toUint256(price);
    }
```

This function is called, via the external getPrice(), that gets queried when [checking if a debt position is liquidiatable](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L111), i.e from the collateral ratio check here https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L121-L123

```solidity
    function isUserUnderwater(State storage state, address account) public view returns (bool) {
        return collateralRatio(state, account) < state.riskConfig.crLiquidation;
    }
```

i.e https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/RiskLibrary.sol#L52-L63

Evidently, Chainlink's `latestRoundData` is being queried to get the price, issue however is that protocol does this without implementing any fallback mechanism and in the case where the query to Chainlink for this asset fails, the whole attempt at querying the price would revert and so is any functionality that uses this, in our case here we are considering it being implemented fir liquidations.

### Impact

Pricing functionalities would be unavailable and in the case where a position is underwater then the even liquidations would be unaccessible, leaving protocol to hold on to bad debt.

### Recommended Mitigation Steps

Consider integrating a fallback oracle and have a try/catch mechanism implemented that queries the fallback oracle in the case the current one fails.

## QA-10 Fix stale docs pointing to deprecated contracts

### Proof of Concept

Take a look at https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/README-sponsor.md#L77-L79

```markdown
#### Oracles

##### Price Feed

A contract that provides the price of ETH in terms of USDC in 18 decimals. For example, a price of 3327.39 ETH/USDC is represented as 3327390000000000000000.

##### Variable Pool Borrow Rate Feed

In order to set the current market average value of USDC variable borrow rates, we perform an off-chain calculation on Aave's rate, convert it to 18 decimals, and store it in the Size contract. For example, a rate of 2.49% on Aave v3 is represented as 24900000000000000. The admin can disable this feature by setting the stale interval to zero. If the oracle information is stale, orders relying on the variable rate feed cannot be matched.
```

However going to the scope we can see that only one contract exists under the _Oracles_, i.e `PriceFeed.sol`, leaving the docs to be stale, in retrospect the contract that dealt with the Variable Pool Borrow Rate Feed was present during the Solidified audit, see #5 issue from the solidified audit here: https://github.com/code-423n4/2024-06-size/blob/main/audits/2024-03-26-Solidified.pdf

### Impact

Bad code documentation, confusing codes for all integrators, users/devs auditors.

### Recommended Mitigation Steps

Considering this contract has been deprecated and it's implementation is now a function on Size.sol. Reroute the documentation to point to this instead, i.e `Size#setVariablePoolBorrowRate()`

## QA-11 USDC getting paused by Circle would brick Size

### Proof of Concept

From the documentation we can see that USDC is expected to be the backbone of the protocol, issue however is that USDC is a centralized stablecoin that is controlled by Circle operators outside of the Size
protocol.

That's to say for example, Circle can just pause transfers of USDC, thereby inadvertently freezing the Size protocol integration with it's USDC liquidity.

### Impact

QA, external integration risks.

### Recommended Mitigation Steps

N/A for the risk by USDC, however using DAI could curb this.

## QA-12 Size could be DOS'd when some new tokens are integrated

### Proof Of Concept

First notice all the instances where `balanceOf` gets used.

```solidity

src/SizeView.sol:
  100              account: user,
  101:             collateralTokenBalance: state.data.collateralToken.balanceOf(user),
  102:             borrowATokenBalance: state.data.borrowAToken.balanceOf(user),
  103:             debtBalance: state.data.debtToken.balanceOf(user)
  104          });

src/libraries/CapsLibrary.sol:
  67      function validateVariablePoolHasEnoughLiquidity(State storage state, uint256 amount) public view {
  68:         uint256 liquidity = state.data.underlyingBorrowToken.balanceOf(address(state.data.variablePool));
  69          if (liquidity < amount) {

src/libraries/LoanLibrary.sol:
  152      {
  153:         uint256 debt = state.data.debtToken.balanceOf(debtPosition.borrower);
  154:         uint256 collateral = state.data.collateralToken.balanceOf(debtPosition.borrower);
  155

src/libraries/Multicall.sol:
  28
  29:         uint256 borrowATokenSupplyBefore = state.data.borrowAToken.balanceOf(address(this));
  30          uint256 debtTokenSupplyBefore = state.data.debtToken.totalSupply();

  36
  37:         uint256 borrowATokenSupplyAfter = state.data.borrowAToken.balanceOf(address(this));
  38          uint256 debtTokenSupplyAfter = state.data.debtToken.totalSupply();

src/libraries/RiskLibrary.sol:
  53      function collateralRatio(State storage state, address account) public view returns (uint256) {
  54:         uint256 collateral = state.data.collateralToken.balanceOf(account);
  55:         uint256 debt = state.data.debtToken.balanceOf(account);
  56          uint256 debtWad = Math.amountToWad(debt, state.data.underlyingBorrowToken.decimals());

src/libraries/actions/Compensate.sol:
  149                  state.debtTokenAmountToCollateralTokenAmount(state.feeConfig.fragmentationFee),
  150:                 state.data.collateralToken.balanceOf(msg.sender)
  151              );

src/libraries/actions/Withdraw.sol:
  54          if (params.token == address(state.data.underlyingBorrowToken)) {
  55:             amount = Math.min(params.amount, state.data.borrowAToken.balanceOf(msg.sender));
  56              if (amount > 0) {

  59          } else {
  60:             amount = Math.min(params.amount, state.data.collateralToken.balanceOf(msg.sender));
  61              if (amount > 0) {

src/token/NonTransferrableScaledToken.sol:
  90      function scaledBalanceOf(address account) public view returns (uint256) {
  91:         return super.balanceOf(account);
  92      }

```

Now since all these functionalities query the `balanceOf()` method on these tokens which for some tokens like the AURA stash token, this attempt at checking the balance would always revert causing the attempt to integrate with these tokens and use them to also revert.

### Impact

See _Proof of Concept_.

### Recommended Mitigation Steps

Consider querying `balanceOf()` on a low level.

## QA-13 Documentation should talk about the right and valid assets

### Proof of Concept

Take a look at https://docs.size.credit/technical-docs/contracts/2.1-deposit-tokens-and-global-trackers#id-2.1-deposit-tokens

```markdown
Underlying borrow and collateral tokens (USDC and WETH) are converted 1:1 into ERC-20 deposit tokens via deposit, which mints `szaUSDC` and `szWETH`, and received back via withdraw, which burns deposit tokens 1:1 in exchange for the underlying tokens.
```

Evidently, the instance of `szaUSDC` should be `aszUSDC` instead of `szaUSDC`.

### Impact

Bad code documentation.

### Recommended Mitigation Steps

Consider fixing all the typos in documentation, multiple other instances should be taken care of too.
