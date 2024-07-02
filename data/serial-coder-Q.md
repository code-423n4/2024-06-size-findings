# [L-01] `getPrice()` does not validate an invalid update round status of the sequencer uptime feed

## Impact

The Size protocol supports the Base chain, which is an L2 network. That's why the `PriceFeed::getPrice()` has checked for the sequencer uptime. However, I noticed that the function does not check the edge case when the Chainlink feed update round is invalid. 

Once the update round is invalid, the `getPrice()` cannot detect it, and the caller may consume stale or incorrect price data.

## Proof of Concept

As per the [Chainlink docs](https://docs.chain.link/data-feeds/l2-sequencer-feeds#example-code):
> *`startedAt`: This timestamp indicates when the sequencer changed status. This timestamp returns `0` if a round is invalid.*

The `startedAt` returned from Chainlink's `latestRoundData()` will be 0 if an update round is invalid/incomplete. As you can see in the [snippet below](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L73), the `getPrice()` does not check if the `startedAt` is 0.

If the round is invalid, the `getPrice()` cannot detect that the sequencer has just come back up after an outage and is still in the grace period. Thus, a caller may consume stale/incorrect price data.

```solidity
    function getPrice() external view returns (uint256) {
        if (address(sequencerUptimeFeed) != address(0)) {
            // slither-disable-next-line unused-return
            (, int256 answer, uint256 startedAt,,) = sequencerUptimeFeed.latestRoundData();

            if (answer == 1) {
                // sequencer is down
                revert Errors.SEQUENCER_DOWN();
            }

            //@audit -- The startedAt will be 0 if a round is invalid/incomplete. If so, we cannot guarantee that the sequencer
            //          hasn't just come back up after an outage and is still in the grace period. Then, a caller may
            //          consume stale/incorrect price data.
@>          if (block.timestamp - startedAt <= GRACE_PERIOD_TIME) {
                // time since up
                revert Errors.GRACE_PERIOD_NOT_OVER();
            }
        }

        return Math.mulDivDown(
            _getPrice(base, baseStalePriceInterval), 10 ** decimals, _getPrice(quote, quoteStalePriceInterval)
        );
    }
```

- `The startedAt will be 0 if a round is invalid/incomplete. If so, we cannot guarantee that the sequencer hasn't just come back up after an outage and is still in the grace period. Then, a caller may consume stale/incorrect price data.`: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/oracle/PriceFeed.sol#L73

## Tools Used

Manual Review

## Recommended Mitigation Steps

Add a check to validate the update round status of the sequencer uptime feed, like in the snippet below.

```diff
    function getPrice() external view returns (uint256) {
        if (address(sequencerUptimeFeed) != address(0)) {
            // slither-disable-next-line unused-return
            (, int256 answer, uint256 startedAt,,) = sequencerUptimeFeed.latestRoundData();

            if (answer == 1) {
                // sequencer is down
                revert Errors.SEQUENCER_DOWN();
            }

+           if (startedAt == 0) {
+               revert Errors.ROUND_IS_INVALID();
+           }

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

---

# [L-02] Liquidators will receive rewards less than expected

## Impact

The `liquidatorReward` variable represents the liquidator's rewards in WETH (collateral token).

However, while calculating the `liquidatorReward` variable in the `Liquidate::executeLiquidate()`, the function uses the `debtPosition.futureValue`, which is a debt value in USDC, instead of the `debtInCollateralToken`, which is a debt value in WETH. 

Subsequently, a liquidator will receive less of the liquidator's rewards (`liquidatorReward`) (in WETH) than expected.

## Proof of Concept

As you can see in the snippet below, the `executeLiquidate()` calculates the `liquidatorReward` variable using the [`debtPosition.futureValue`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Liquidate.sol#L98) (in USDC) instead of the `debtInCollateralToken` (in WETH).

Since the `liquidatorReward` variable represents the liquidator's rewards in WETH (collateral token), the use of `debtPosition.futureValue`, which represents a debt value in USDC (borrow token), is inconsistent.

Furthermore, suppose the price of 1 WETH == 3500 USDC.

> ***Actually, we should not compare the values indicated by the `debtPosition.futureValue` (USDC) and `debtInCollateralToken` (WETH) since they represent different asset types and underlying values, but I intend to point out that the use of the `debtPosition.futureValue` is incorrect***. 

While the WETH token is represented using 18 decimals, the USDC token uses only 6 decimals. In other words, the `debtPosition.futureValue` (USDC) will be 3500 * 10^6 (in wei), whereas the `debtInCollateralToken` (WETH) will be 10^18 (in wei). Hence, the face value of the `debtPosition.futureValue` used to calculate the `liquidatorReward` variable will be less than the value of the `debtInCollateralToken`.

```solidity
    function executeLiquidate(State storage state, LiquidateParams calldata params)
        external
        returns (uint256 liquidatorProfitCollateralToken)
    {
        ...

        // profitable liquidation
        if (assignedCollateral > debtInCollateralToken) {
            uint256 liquidatorReward = Math.min(
                assignedCollateral - debtInCollateralToken,

                //@audit -- The debtPosition.futureValue (in USDC) is used instead of the debtInCollateralToken (in WETH).
                //          Thus, a liquidator will get the liquidator's rewards (liquidatorReward in WETH) less than expected.
@>              Math.mulDivUp(debtPosition.futureValue, state.feeConfig.liquidationRewardPercent, PERCENT)
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

        ...
    }
```

- `The debtPosition.futureValue (in USDC) is used instead of the debtInCollateralToken (in WETH). Thus, a liquidator will get the liquidator's rewards (liquidatorReward in WETH) less than expected.`: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Liquidate.sol#L98

## Tools Used

Manual Review

## Recommended Mitigation Steps

Use the `debtInCollateralToken` variable instead of the `debtPosition.futureValue` when calculating the `liquidatorReward` variable.

```diff
    function executeLiquidate(State storage state, LiquidateParams calldata params)
        external
        returns (uint256 liquidatorProfitCollateralToken)
    {
        ...

        // profitable liquidation
        if (assignedCollateral > debtInCollateralToken) {
            uint256 liquidatorReward = Math.min(
                assignedCollateral - debtInCollateralToken,
+               Math.mulDivUp(debtInCollateralToken, state.feeConfig.liquidationRewardPercent, PERCENT)
-               Math.mulDivUp(debtPosition.futureValue, state.feeConfig.liquidationRewardPercent, PERCENT)
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

        ...
    }
```

---

# [L-03] Lenders can be grieved by claiming gas fees if keeper bots are out of service

## Impact

The protocol allows borrowers to partially repay their loans via `Size::compensate()`. Suppose the repayment process splits the credit position. In that case, the protocol will charge the borrower a fragmentation fee, which will be used for running keeper bots to claim and aggregate the lending liquidity on behalf of lenders.

With the current implementation, lenders cannot disable the partial repayment feature on their credit positions. Suppose the keeper bots are somehow out of service. In that case, lenders must independently claim and aggregate their lending liquidity, and they will be grieved by a significant amount of claiming gas fees.

## Proof of Concept

As you can see, the `executeCompensate()` will charge a borrower [a fragmentation fee](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Compensate.sol#L148-L154) if their repayment process causes the credit split, which is a proper design.

However, if the protocol's keeper bots are somehow out of service, a lender must claim and aggregate the lending liquidity on his own, and he will be grieved by a significant amount of claiming gas fees.

```solidity
    function executeCompensate(State storage state, CompensateParams calldata params) external {
        ...

        uint256 exiterCreditRemaining = creditPositionToCompensate.credit - amountToCompensate;

        // credit emission
        state.createCreditPosition({
            exitCreditPositionId: params.creditPositionToCompensateId == RESERVED_ID
                ? state.data.nextCreditPositionId - 1
                : params.creditPositionToCompensateId,
            lender: creditPositionWithDebtToRepay.lender,
            credit: amountToCompensate
        });
        if (exiterCreditRemaining > 0) {
            //@audit -- Even if the protocol would charge the fragmentation fee for running keeper bots to 
            //          claim and aggregate lending liquidity on behalf of a lender in case a borrower 
            //          partially repays their loan and the repayment splits the credit position.
            //
            //          However, if the keeper bots are somehow out of service, a lender must claim and
            //          aggregate the lending liquidity on his own, and he will be grieved by a significant 
            //          amount of claiming gas fees.
            //
            // charge the fragmentation fee in collateral tokens, capped by the user balance
@>          uint256 fragmentationFeeInCollateral = Math.min(
@>              state.debtTokenAmountToCollateralTokenAmount(state.feeConfig.fragmentationFee),
@>              state.data.collateralToken.balanceOf(msg.sender)
@>          );
@>          state.data.collateralToken.transferFrom(
@>              msg.sender, state.feeConfig.feeRecipient, fragmentationFeeInCollateral
@>          );
        }
    }
```

- `Even if the protocol would charge the fragmentation fee for running keeper bots to claim and aggregate lending liquidity on behalf of a lender in case a borrower partially repays their loan and the repayment splits the credit position. However, if the keeper bots are somehow out of service, a lender must claim and aggregate the lending liquidity on his own, and he will be grieved by a significant amount of claiming gas fees.`: https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Compensate.sol#L148-L154

## Tools Used

Manual Review

## Recommended Mitigation Steps

Ensure that keeper bots will operate adequately.

If possible, lenders should be able to opt in or out of the partial repayment of their credit positions.