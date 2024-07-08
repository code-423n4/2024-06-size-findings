# L-01 Several Events are emitted incorrectly:

1. In `executeCompensate` the following event is emitted:

```solidity
emit Events.Compensate(params.creditPositionWithDebtToRepayId, params.creditPositionToCompensateId, params.amount);
```
However, `params.amount` is incorrect as this variable could change during the code execution due to the execution state of the credit positions involved in the operation. Take for example the following code from the same function:

```solidity
uint256 amountToCompensate = Math.min(params.amount, creditPositionWithDebtToRepay.credit);
```

2. At the beginning of the function `executeSellCreditMarket` the event `SellCreditMarket` is emitted:

```solidity
emit Events.SellCreditMarket(params.lender, params.creditPositionId, params.tenor, params.amount, params.tenor, params.exactAmountIn);
```

However, two issues exists in this event:

- The 3rd field which is passed as `params.tenor` is not necessarily the `tenor` at which the order is executed this comes from the fact that in case an existing `creditPosition` is being sold, then `tenor` is recomputed as `debtPosition.dueDate - block.timestamp`.

- The 5th field which is passed as `params.tenor` should be `dueDate` as per the definition in the `Events.sol` file. 

3. At the beginning of the `executeBuyCreditMarket` function the code emits the following event:

```solidity
emit Events.BuyCreditMarket(params.borrower, params.creditPositionId, params.tenor, params.amount, params.exactAmountIn);
```

However, there is no guarantee that `params.borrower` is the underlying borrower for `params.creditPositionId`. This comes from the fact that when `creditPositionId != RESERVED_ID` then the borrower is derived from the current state instead of using `params.borrower`.

# L-02 Incorrect assumption of the stale price interval in L2 networks.

The `PriceFeed.sol` contract assumes that the ETH/USD feed has a heartbeat of 3600 seconds. The issue is that this is only true in Ethereum mainnet. However, the Size project is intended to also be deployed in L2s chains such as Arbitrum, where the ETH/USD has a heartbeat of 86400 seconds.

Reference: https://data.chain.link/feeds/arbitrum/mainnet/eth-usd

# L-03 Do not allow to call multicall during a multicall

At the beginning of the `multicall` function, the code sets the state variable `state.data.isMulticall` to `true`. Then, the code executes all the user actions specified in the data array. At the end, the code sets `state.data.isMulticall` to false again:

```solidity
function multicall(State storage state, bytes[] calldata data) internal returns (bytes[] memory results) {
      state.data.isMulticall = true;

      // ......     

      state.data.isMulticall = false;
}
```

In this way, all the calls executed in between will know that they are in a `multicall` and react accordingly. However, it is possible to change the `isMulticall` variable to false while still being in a `multicall`.

This can happen by invoking the multicall function inside a batch of a previous multicall. The inner multicall will change the flag to false when finishing its execution, causing subsequent calls of the outer multicall to see the `isMulticall` flag as false.

While this inconsistency cannot be exploited, in favor of the user, in the current codebase, a future upgrade mechanism that relies on `state.isMulticall` could become exploitable.

# L-04 Is it possible to create a credit position with `tenor < minTenor`

In the functions `validateSellCreditMarket` and `validateBuyCreditMarket` when `creditPositionID != RESERVED_ID` it means that a specific credit position was passed as argument. In this case, the `tenor` is computed as the `dueDate - block.timestamp`:

```solidity
tenor = debtPosition.dueDate - block.timestamp;
```

However, this `tenor` is not validated to not bet smaller than the `minTenor` or greater than the `maxTenor`. If we check for example, the `validateLiquidateWithReplacement` function we can find that the validation exists:

```solidity
uint256 tenor = debtPosition.dueDate - block.timestamp;
if (tenor < state.riskConfig.minTenor || tenor > state.riskConfig.maxTenor) {
      revert Errors.TENOR_OUT_OF_RANGE(tenor, state.riskConfig.minTenor, state.riskConfig.maxTenor);
}
```

# L-05 Cash receiver is not paying swap fee in `LiquidateWithReplacement`

The new borrower in `LiquidateWithReplacement` is being transferred the `issuanceValue` computed as follows:

```solidity
issuanceValue = Math.mulDivDown(debtPositionCopy.futureValue, PERCENT, PERCENT + ratePerTenor);
```

It is being computed using the `futureValue` which represents the debt that the new borrower is accruing. However, this new borrower is not paying for the swap fee even though the borrower is receiving cash.


# L-06 A malicious user can fragment a credit position without the paying fragmentation fee via compensate

With the `compensate` mechanism a user can go from the debt position having only one credit position to the debt position having several credit positions as the minimum credit configuration allows. This is achieved by using the `RESERVED_ID` as the `creditPositionToCompensateId` parameter.

Therefore, the Size's bots will need to call `claim` for each of the created credit positions without receiving a fragmentation fee. 


