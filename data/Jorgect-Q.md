# LOW REPORT

## [L-01] Credit positions are by default forSale true.

A credit position can be sell early, so lender can get his money before the maturity, but lender also may want whole his position until the end because he is getting more money.

## Impact

The problem is that lenders that want hold their position until the final can be front ran before they can call `setUserConfiguration` and set forSale to false.

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
...
}

```
[[Link]](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/SetUserConfiguration.sol#L71)

## Recommendation

Consider set to false ForSale by default when a credit position is open, and let the user decide is the position forSale will be set to true.

## [L-02] inability to specify an max amount in the offer can let lender/borrower.

User can make offer but they can not expecify a max amount see:

```solidity

    function executeBuyCreditLimit(State storage state, BuyCreditLimitParams calldata params) external {
        state.data.users[msg.sender].loanOffer =
            LoanOffer({maxDueDate: params.maxDueDate, curveRelativeTime: params.curveRelativeTime});

```

### Impact

The problem is that lender may want let his positions in several borrower to divide his risk, and not in just one borrower who can take off his borrow tokens.

### Recommendation

Allow lender/borrowers to specify a max A amount tha lender/borrowe can take.

## [L-03] just the msg.sender should claim

The current implementation is allowing any person to claim on behalf of a lender.

```solidity

 function validateClaim(State storage state, ClaimParams calldata params) external view {
        CreditPosition storage creditPosition = state.getCreditPosition(params.creditPositionId);
        // validate msg.sender   <--------
        // N/A

        // validate creditPositionId
        if (state.getLoanStatus(params.creditPositionId) != LoanStatus.REPAID) {
            revert Errors.LOAN_NOT_REPAID(params.creditPositionId);
        }
        if (creditPosition.credit == 0) {
            revert Errors.CREDIT_POSITION_ALREADY_CLAIMED(params.creditPositionId);
        }
    }



```
[[Link]](https://github.com/code-423n4/2024-06-size/blob/8850e25fb088898e9cf86f9be1c401ad155bea86/src/libraries/actions/Claim.sol#L31C4-L44C1)

### Impact 
The problem is that a lender may want let his money in the protocol because he want accrued more yield. see the next scenario lender get repay, he can claim but he prefer let the usdc generating yield, he decide claim, 1 month after he discovery that his position was already claim and they don't generate any yield

### Recommendation
Consider just the msg.sender claim his own position.

