# Low
## Credit offerers cannot specify the amount to borrow
Although the protocol allows borrowers to specify a minimum opening collateral ratio, it doesn't allow them to specify the amount to borrow. 

Given that ETH is used as collateral, any variation in its price will lead to a different credit offer.

For instance, assuming Alice wants to take a loan of 1000 USDC while having a collateral of 1 ETH at a price of 4000 USDC, she will need to specify an opening collateral ratio of 400%, however if the price goes up to 5000 USDC here borrow offer will be 1250 USDC. She is enforced to update her minimum opening collateral ratio to 500% to keep the same amount of credit.

A better approach would be to also allow borrowers to specify the amount to borrow, and then cap it debt to by the min amount between max debt to take and the amount corresponding to the min opening collateral ratio.

## `PriceFeed._getPrice(AggregatorV3Interface,uint256)` lack of min/max price validation
Only check if price is lower/equal to 0, but not check if price is higher than min price, opening vector attack similar to Terra case with venus

## `RiskLibrary.collateralRatio(State storage, address)` assumes `collateralToken` always has 18 decimals
Although currently for size there is no issue give that ETH is used as collateral, this library is dangerous if used with other tokens in the future give that it assumes that collateral always have 18 decimals.

## `UpdateConfig.executeUpdateConfig(State,UpdateConfigParams)`: If opening collateral ratio is updated, offers based on previous default opening collateral ratio will be affected
This become a problem is the value is lower than previous one

##  `SetUserConfiguration.validateSetUserConfiguration(State,SetUserConfigurationParams)` does not validate that `openingLimitBorrowCR` is greater `crOpening`
Current codebase allow users to set an `openingLimitBorrowCR` below `crOpening`. The impact is nos high given that `RiskLibrary:validateUserIsNotBelowOpeningLimitBorrowCR` always check against max between this two values. Still, it would be better to validate this in the function, leaving check in `RiskLibrary:validateUserIsNotBelowOpeningLimitBorrowCR` in case a decrease of `crOpening` happens.


# Informational
## `DepositTokenLibrary.depositUnderlyingCollateralToken(State, address, uint256)` does not deposit underlying collateral in Aave
Current implementation does not deposit underlying collateral token in Aave, therefore users will not earn interest on their collateral while they are not using it. A better approach would be depositing in Aave and accrue interest, which would improve user collateral ratio and reduce the risk of liquidation.

## `Size.updateConfig(UpdateConfigParams)` can comment valdation to save gas
It's just a dummy call, although it follows a desired patter it would be better to simply leave it commented, saving gas

## Stuck ETH can be claimed by anyone
If for some reason the contract is stuck with ETH, anyone can claim it through function `deposit` ataching 1 wei given that amount to deposit consider the balance of the contract instead of the amount sent.

Although a minor issue, a better approach would be to use `msg.value` checking if the contract has some balance, and if this balance is equal to `msg.value` execute deposit by this value, if balance is greater than `msg.value` do a deposit for this value, and sent the rest to feeRecipent address.


## Lenders cannot specify max amounts to lend for different terms
Protocol assumes that lender are ok with lending any amount for any term, this enforce users to use multiple addresses in order to limit the amount to lend for each tenor, leading to a worse UX. A better approach would be adding a `maxAmount` when buying credit limit

## `DepositTokenLibrary.depositUnderlyingBorrowTokenToVariablePool(State,address,address,uint256)` reentrancy risk for ERC-777 tokens
Current implementation can be affected by reentrancy for tokens with hooks like ERC-777 due to [this call](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/DepositTokenLibrary.sol#L60). In case of an update, consider adding nonReentrant modifier to avoid this issue.

## New credit are automatically put on sale
Whenever a loan is created (by [buying](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/BuyCreditMarket.sol#L181-L186) or [selling](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SellCreditMarket.sol#L186-L191) credit on market) or [compensated](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Compensate.sol#L120-L125), the [new credit position is automatically put on sale](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L81).

[While this is not an issue if user has disallowed all their sales](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/SizeStorage.sol#L24), if they are currently selling just some of their credit positions but no all, this will enforce them to update the new credit states.

A better way to approach for credit sales would be adding an extra parameter when they are created to specify if the new credit position should be put on sale or not.

## Partial repayments are not allowed if `deadline - block.timestamp < state.riskConfig.minTenor`
According to docs partial repayments are meant to be triggered though multicall by calling: deposit, compensate, repay.

However, if `deadline - block.timestamp < state.riskConfig.minTenor` compensate will revert given it calls [`createDebtAndCreditPositions`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Compensate.sol#L67), which validates that [`debtPositionToRepay.dueDate - block.timestamp < state.riskConfig.minTenor`](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/AccountingLibrary.sol#L87), DOSing repayments if this criteria is met.

If last quoted line is deleted then loans with `debtPositionToRepay.dueDate < state.riskConfig.minTenor` would be able to be created, breaking one of the main invariants of the protocol. 

The only way to solve this issue is creating another function which handle partial repayments.