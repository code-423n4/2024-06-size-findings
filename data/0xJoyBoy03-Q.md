
# [L-1] The natspec and the code do not match in the `NonTransferrableScaledToken::_unscaled`

## Description 
the natspec says that `The unscaled amount is the scaled amount divided by the current liquidity index`
however, this does not match the code
```js
    /// @dev The unscaled amount is the scaled amount divided by the current liquidity index
    function _unscale(uint256 scaledAmount) internal view returns (uint256) {
        // @audit-low the code is correct the natspec is not
        return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
    }
```

## Recommended Mitigation Steps
remove or modify it
```git
-    /// @dev The unscaled amount is the scaled amount divided by the current liquidity index
    function _unscale(uint256 scaledAmount) internal view returns (uint256) {
        // @audit-low the code is correct the natspec is not
        return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
    }
```


# [L-2] The `PriceFeed` contract does not check the equality of intermediate assets

## Description
the constructor of the `PriceFeed` contract checks the equality of decimals between base and quote.
however, it does not check the equality of intermediate assets between the base and the quote
```js
    constructor(
        address _base,
        address _quote,
        address _sequencerUptimeFeed,
        uint256 _baseStalePriceInterval,
        uint256 _quoteStalePriceInterval
    ) {
       // ...

        //here, it does impl the equality of the decimals but not the intermediate asset
        if (base.decimals() != quote.decimals()) {
            revert Errors.INVALID_DECIMALS(quote.decimals());
        }
    }
```

## Recommended Mitigation Steps


```git

    constructor(
        address _base,
        address _quote,
        address _sequencerUptimeFeed,
        uint256 _baseStalePriceInterval,
        uint256 _quoteStalePriceInterval
    ) {
       // ...

       // Additional check to ensure the equality of intermediate assets
+       address baseIntermediateAsset = getIntermediateAsset(_base);
+       address quoteIntermediateAsset = getIntermediateAsset(_quote);

+       if (baseIntermediateAsset != quoteIntermediateAsset) {
           revert Errors.INVALID_INTERMEDIATE_ASSET(baseIntermediateAsset, quoteIntermediateAsset);
       }
    }

    
+    function getIntermediateAsset(address asset) internal view returns (address) {
+        AggregatorV3Interface priceFeed = AggregatorV3Interface(asset);
+        // Logic to determine the intermediate asset used by the price feed
+        // This may involve reading the contract's storage or calling specific methods
+        // For simplicity, let's assume the intermediate asset address is retrievable
+        return priceFeed.getIntermediateAsset();
+    }
```



# [L-3] wrong emission of the event in the `BuyCreditMarket::executeBuyCreditMarket`

## Description 
the data provided for emission is wrong when the `creditPositionId` is not the `RESERVED_ID`
```js
function executeBuyCreditMarket(State storage state, BuyCreditMarketParams memory params)
        external
        returns (uint256 cashAmountIn)
    {
        // @audit-low: wrong tenor and borrower data for emission when buying an existing creditPosition id
        emit Events.BuyCreditMarket(
@>            params.borrower, params.creditPositionId, params.tenor, params.amount, params.exactAmountIn
        );
```
according to the natspec, the tenor and the borrower data is ignored when the creditPositionId is equal to RESERVED_ID so the provided tenor and borrower could be anything wrong

**@note**:  Similarly, the `SellCreditMarket::executeSellCreditMarket`  emission is wrong under the same conditions for the tenor param

## Recommended Mitigation Steps
 use the correct tenor and borrower of the existing creditPositionId for emission



# [C-1] Owner of the contract can transfer any debt token of any account

## Description
in the `NonTransferrableToken::transferFrom` function:
```js
// @audit-centralization-risk: owner can transfer the debt tokens of any account
    function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {
        _transfer(from, to, value);
        return true;
    }
```
according to the doc "[Debt tokens are non-transferable](https://docs.size.credit/technical-docs/contracts/2.1-deposit-tokens-and-global-trackers#id-2.2-global-trackers)" but the owner of the contract can transfer any debt token of any account in any amount which is significant and is potential risk for users involved with the protocol

## Recommended Mitigation Steps
do a check for `szDebt` token to revert:
```git
    function transferFrom(address from, address to, uint256 value) public virtual override onlyOwner returns (bool) {
+        if (name() == "szDebt")     revert;
        _transfer(from, to, value);
        return true;
    }
```

