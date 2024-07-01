# QA Report

## Low Risk 
| Id | Title |
|:--:|:-------|
| [L-01] | YieldCurveLibrary.sol#getAdjustedAPR does not check if variablePoolBorrowRate is non-zero. |
| [L-02] | LiquidateWithReplacement should round up issuanceValue instead of rounding down. |
| [L-03] | NonTransferrableScaledToken is not ERC20 compliant - it does not correctly fire events. |
| [L-04] | Initialization of configs does not check `tenor * swapFeeAPR / YEAR < PERCENT`. |
| [L-05] | No minPrice/maxPrice circuit breaker in Pricefeed. |
| [L-06] | Math.sol#aprToRatePerTenor should round up when called by SellCreditMarket. |
| [L-07] | Protocol does not check whether feeRecipient can collect borrowAToken in liquidateWithReplacement. |
| [L-08] | Some protocol invariants are untrue. |
| [L-09] | USDC blacklisted users cannot repay debt. |

## [L-01] YieldCurveLibrary.sol#getAdjustedAPR does not check if variablePoolBorrowRate is non-zero.

### Bug Description

There are three parameters related to variable pool borrow rate when calculating the APR.

1. `variablePoolBorrowRate`
2. `variablePoolBorrowRateUpdatedAt`
3. `variablePoolBorrowRateStaleRateInterval`

1 and 2 are updated periodically by the BORROW_RATE_UPDATER_ROLE. 3 is updated by the protocol admin. The issue is that if the protocol admin updates `variablePoolBorrowRateStaleRateInterval` to a very large number (larger than current timestamp), and the borrow rate is never updated (defaults to zero), when calculating APR, it does not check whether borrow rate is non-zero and simply assumes borrow rate to be zero, which is obviously incorrect.

This issue only occurs if admin updates `variablePoolBorrowRateStaleRateInterval` to a really large number. However, this may be likely, if the admin simply wants to disable the borrow rate update interval check.

```solidity
    function getAdjustedAPR(int256 apr, uint256 marketRateMultiplier, VariablePoolBorrowRateParams memory params)
        internal
        view
        returns (uint256)
    {
        if (marketRateMultiplier == 0) {
            return SafeCast.toUint256(apr);
        } else if (
>           params.variablePoolBorrowRateStaleRateInterval == 0
                || (
                    block.timestamp - params.variablePoolBorrowRateUpdatedAt
>                       > params.variablePoolBorrowRateStaleRateInterval
                )
        ) {
            revert Errors.STALE_RATE(params.variablePoolBorrowRateUpdatedAt);
        } else {
            return SafeCast.toUint256(
                apr + SafeCast.toInt256(Math.mulDivDown(params.variablePoolBorrowRate, marketRateMultiplier, PERCENT))
            );
        }
    }
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/YieldCurveLibrary.sol#L87-L107

### Recommendation

```diff
        } else if (
            params.variablePoolBorrowRateStaleRateInterval == 0
                || (
                    block.timestamp - params.variablePoolBorrowRateUpdatedAt
                        > params.variablePoolBorrowRateStaleRateInterval
                )
        ) {
            revert Errors.STALE_RATE(params.variablePoolBorrowRateUpdatedAt);
+       } else if (params.variablePoolBorrowRate == 0) {
+       	revert Errors.INVALID_BORROW_RATE();
        } else {
```

## [L-02] LiquidateWithReplacement should round up issuanceValue instead of rounding down.

### Bug Description

According to the contest readme, the rounding should always favor the maker.

> Whenever a taker-maker operation occurs, all rounding tries to favor the maker, who is the passive party.

However, during the LiquidateWithReplacement action, the maker is the user that places the borrow offer, and the admin is the taker. When calculating the issuanceValue (the number of borrowATokens given to the borrower), it should round up to favor the borrower. However, it is currently rounding down.

```solidity
    function executeLiquidateWithReplacement(State storage state, LiquidateWithReplacementParams calldata params)
        external
        returns (uint256 issuanceValue, uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
    {
        ...

>       issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);

        ...

>       state.data.borrowAToken.transferFrom(address(this), params.borrower, issuanceValue);

        ...
    }
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/LiquidateWithReplacement.sol#L146

### Recommendation

```diff
-		issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
+		issuanceValue = Math.mulDivUp(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
```


## [L-03] NonTransferrableScaledToken is not ERC20 compliant - it does not correctly fire events.

### Bug Description

According to the [EIP-20](https://eips.ethereum.org/EIPS/eip-20#transfer-1), ERC20 compatible tokens **must** trigger a `Transfer` event when tokens are being transferred.

> Transfer

> MUST trigger when tokens are transferred, including zero value transfers.

> `event Transfer(address indexed _from, address indexed _to, uint256 _value)`

The NonTransferrableScaledToken natspec clearly states that NonTransferrableScaledToken is an ERC20 token, however it does not correctly fire the Transfer event in `transferFrom()` function.

```solidity
>	/// @notice An ERC-20 that is not transferrable from outside of the protocol
	/// @dev The contract owner (i.e. the Size contract) can still mint, burn, and transfer tokens
	///      Enables the owner to mint and burn scaled amounts. Emits the TransferUnscaled event representing the actual unscaled amount
	contract NonTransferrableScaledToken is NonTransferrableToken {
		...
>    function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {
	        uint256 scaledAmount = Math.mulDivDown(value, WadRayMath.RAY, liquidityIndex());

	        _burn(from, scaledAmount);
	        _mint(to, scaledAmount);

	        emit TransferUnscaled(from, to, value);

	        return true;
	    }
	}
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/token/NonTransferrableScaledToken.sol#L75-L86c

### Recommendation

```diff
    function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {
        uint256 scaledAmount = Math.mulDivDown(value, WadRayMath.RAY, liquidityIndex());

        _burn(from, scaledAmount);
        _mint(to, scaledAmount);

        emit TransferUnscaled(from, to, value);
+       emit Transfer(from, to, value);

        return true;
    }
```

## [L-04] Initialization of configs does not check `tenor * swapFeeAPR / YEAR < PERCENT`.

### Bug Description

When updating the config of `minTenor`, `maxTenor`, `swapFeeAPR`, there is an additional check that makes sure the swap fee for a tenor is not larger than 100%. Specifically, `tenor * swapFeeAPR / YEAR < PERCENT`.

However, such check is not present during the config initialization phase. In order to be consistent, it is better to also add the check in initialization.

UpdateConfig.sol
```solidity
		else if (Strings.equal(params.key, "minTenor")) {
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
        } 
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/UpdateConfig.sol#L99-L124
- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Initialize.sol

### Recommendation

Add such check during initialization phase.

## [L-05] No minPrice/maxPrice circuit breaker in Pricefeed.

### Bug Description

The `PriceFeed.sol` implementation uses Chainlink Aggregator to calculate the value for the quoteToken. However, it does not check for `minAnswer/maxAnswer` price. This means if the circuit breaker mechanism is implemented for this price feed, the return value may be incorrect.

A simple introduction to the circuit breaker mechanism in Chainlink: For some data feed aggregators, there is a `minAnswer/maxAnswer` check, that if the oracle price feed falls out of range, it will simply return the `minAnswer/maxAnswer`. For example, if the price falls below `minAnswer`, the oracle will simply return `minAnswer`. See https://docs.chain.link/data-feeds#check-the-latest-answer-against-reasonable-limits for more details.

From the code, we know the Size protocol supports USDC/USD on the Ethereum mainnet. We can [lookup](https://docs.chain.link/data-feeds/price-feeds/addresses?network=ethereum&page=1&search=usdc) the data feed for USDC/USD https://etherscan.io/address/0x8fFfFfd4AfB6115b954Bd326cbe7B4BA576818f6, and fetch its aggregator address https://etherscan.io/address/0x789190466E21a8b78b8027866CBBDc151542A26C. We can check that the minAnswer == 1e6, maxAnswer == 1e11, with a decimals == 8. This means the USDC/USD price is bounded in the range [0.01, 1000].

In history, the circuit breaker caused this issue https://rekt.news/venus-blizz-rekt/ during the LUNA crash.

```solidity
	/// @dev The price is calculated as `base / quote`. Example configuration:
	///      _base: ETH/USD feed
>	///      _quote: USDC/USD feed
	///      _sequencerUptimeFeed: the sequencer uptime feed for supported L2s (https://docs.chain.link/data-feeds/l2-sequencer-feeds)
	///      _baseStalePriceInterval: 3600 seconds (https://data.chain.link/ethereum/mainnet/crypto-usd/eth-usd)
	///      _quoteStalePriceInterval: 86400 seconds (https://data.chain.link/ethereum/mainnet/stablecoins/usdc-usd)
	///      answer: ETH/USDC in 1e18
	///      Note: _base and _quote must have the same number of decimals
	///      Note: _base and _quote must have the same intermediate asset (in this example, USD)
	contract PriceFeed is IPriceFeed {
		...
>	    function _getPrice(AggregatorV3Interface aggregator, uint256 stalePriceInterval) internal view returns (uint256) {
	        // slither-disable-next-line unused-return
	        (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();

	        if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
	        if (block.timestamp - updatedAt > stalePriceInterval) {
	            revert Errors.STALE_PRICE(address(aggregator), updatedAt);
	        }

	        return SafeCast.toUint256(price);
	    }
	}
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L84-L94

### Recommendation

Add a minPrice/maxPrice check, and revert if is out of bounds.

## [L-06] Math.sol#aprToRatePerTenor should round up when called by SellCreditMarket.

### Bug Description

When a user is trying to sell credit, the rounding should benefit the lender (maker), which means the APR should round up and the user should pay more credit.

> Whenever a taker-maker operation occurs, all rounding tries to favor the maker, who is the passive party.

However, in `SellCreditMarket.sol`, when calculating `ratePerTenor`, it is round down, which is unexpected.

SellCreditMarket.sol
```solidity
        uint256 ratePerTenor = state.data.users[params.lender].loanOffer.getRatePerTenor(
            VariablePoolBorrowRateParams({
                variablePoolBorrowRate: state.oracle.variablePoolBorrowRate,
                variablePoolBorrowRateUpdatedAt: state.oracle.variablePoolBorrowRateUpdatedAt,
                variablePoolBorrowRateStaleRateInterval: state.oracle.variablePoolBorrowRateStaleRateInterval
            }),
            tenor
        );
```

Math.sol
```solidity
    function aprToRatePerTenor(uint256 apr, uint256 tenor) internal pure returns (uint256) {
        return mulDivDown(apr, tenor, YEAR);
    }
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SellCreditMarket.sol#L147-L154
- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/Math.sol#L40

### Recommendation

Add a round direction parameter in Math.sol#aprToRatePerTenor, and round up during `SellCreditMarket.sol`.

## [L-07] Protocol does not check whether feeRecipient can collect borrowAToken in liquidateWithReplacement.

### Bug Description

When the admin calls `liquidateWithReplacement`, the repaid debt borrowerATokens are split to 2 parts:

1. The borrower receives `issuanceValue` amount of tokens.
2. The feeRecipient receives `debtPositionCopy.futureValue - issuanceValue` amount of tokens.

However, when validating the variable pool liquidity, only part 1 is checked, and does not check part 2. This may cause either the borrower or feeRecipient to not be able to withdraw their USDC due to not enough liquidity.

```solidity
    function executeLiquidateWithReplacement(State storage state, LiquidateWithReplacementParams calldata params)
        external
        returns (uint256 issuanceValue, uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken)
    {
        ...
        issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
        liquidatorProfitBorrowToken = debtPositionCopy.futureValue - issuanceValue;

        ...
        state.data.borrowAToken.transferFrom(address(this), params.borrower, issuanceValue);
        state.data.borrowAToken.transferFrom(address(this), state.feeConfig.feeRecipient, liquidatorProfitBorrowToken);
    }
```

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
>       state.validateVariablePoolHasEnoughLiquidity(amount);
    }
```

### Code Snippet

- https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/LiquidateWithReplacement.sol#L162

### Recommendation

```diff
-       state.validateVariablePoolHasEnoughLiquidity(amount);
+       state.validateVariablePoolHasEnoughLiquidity(amount + liquidatorProfitBorrowToken);
```

## [L-08] Some protocol invariants are untrue.

### Bug Description

There are two protocol invariants that are untrue.

> BORROW_01: Borrow increases the borrower's cash

> SOLVENCY_02: SUM(credit) <= SUM(debt)

For `BORROW_01`, if a user borrows from himself, then the borrower's cash will actually decrease, due to swap fees.

For `SOLVENCY_02`, the total credit may be larger than debt, if the debt is repaid or liquidated, but the credit is not claimed yet.

### Code Snippet

N/A

### Recommendation

1. To make `BORROW_01` invariant hold true, users should not be able to take a loan from themselves. 
2. The `SOLVENCY_02` invariant does not make sense, it should be removed.

## [L-09] USDC blacklisted users cannot repay debt.

### Bug Description

If users have an open debt position, but he is then blacklisted by USDC token, currently there is no way for him to pay off debt.

### Code Snippet

N/A

### Recommendation

Add in documentation of this use case, so users are aware of this issue.
