### [Low-01] As Referral Program Of Aave Currently Inactive, So In `variablePool.supply(_, _, _, 0). Here 0 Passed But This Refferal Program Could Activated In Future, In that Case SIZE Will Not Able To Take Advantage Of This.

Aave Developer documentation have following 
`Referral supply is currently inactive, you can pass 0 as referralCode. This program may be activated in the future through an Aave governance proposal`

So This can active in future, in that case SIZE will not able to Use this feature due to hardcoded value.

```solidity 
  state.data.underlyingBorrowToken.forceApprove(address(state.data.variablePool), amount);
        state.data.variablePool.supply(address(state.data.underlyingBorrowToken), amount, address(this), 0);
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L60

#### Mitigation
May be Use some state variable for `referral code` and change it when ever its get changed(activated) in aave. 




### [Low-02] AnyOne Can Create A New Loan Offer / New Borrow Offer, Irrespective they Have Deposited Collateral Or Not.
There is no validation that User creating loan offer has collateral. Although they will not receive loan as in `buyCreditMarket()` function `validateVariablePoolHasEnoughLiquidity()` will failed.

But Point is Many Users could create those type of Offers and populate whole system.

```solidity
    function validateSellCreditLimit(State storage state, SellCreditLimitParams calldata params) external view {
        BorrowOffer memory borrowOffer = BorrowOffer({curveRelativeTime: params.curveRelativeTime});

        // a null offer mean clearing their limit order
        if (!borrowOffer.isNull()) {
            // validate msg.sender
            // N/A

            // validate curveRelativeTime
            YieldCurveLibrary.validateYieldCurve(
                params.curveRelativeTime, state.riskConfig.minTenor, state.riskConfig.maxTenor
            );
        }
    }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SellCreditLimit.sol#L25-L38

#### Mitigation
Simple mitigation is to check for collateral before User create loan offer 




### [Low-03] `params.minAPR` checks in `validateBuyCreditMarket()` is not true with actual senario.

```solidity
        // validate minAPR
        uint256 apr = borrowOffer.getAPRByTenor(
            VariablePoolBorrowRateParams({
                variablePoolBorrowRate: state.oracle.variablePoolBorrowRate,
                variablePoolBorrowRateUpdatedAt: state.oracle.variablePoolBorrowRateUpdatedAt,
                variablePoolBorrowRateStaleRateInterval: state.oracle.variablePoolBorrowRateStaleRateInterval
            }),
            tenor
        );
        if (apr < params.minAPR) {
            revert Errors.APR_LOWER_THAN_MIN_APR(apr, params.minAPR);
        }
```

For let say (taking an example from official document)
A Borrow has Borrow Offer of 5% APR for 30 Days
when lender wants to lend he calls `buyCreditmarket()` with params.minApr = 5%

This `params.minApr` get checked with `borrowOffer.getAPRByTenor()` which return 5% in above simple example. (ignore market adjustments)

Then in function pointer moves to execute part

In `executeBuyCreditMarket()` `ratePerTenor` get via `Math.mulDivDown(apr, tenor, Year)`
In this calculation 
apr : 0.05*10**18
tenor : 2,592,000 seconds
YEAR : 31,536,000 seconds

which give ratePerTenor : 0.41% per Month

In solidity 1 YEAR = 12 Months

so Acuatual APR Lender receiving is = 4.92%

where Lender intension to receive minAPR of 5%

So this invariant `(apr > params.minAPR)` is not actually hold in practical senario

#### Mitigation
Should reconsider this calculation 





### [Low-04] Recidual Amount Of Token Remain In Conract Due To `Rounding`
SIZE implementing different calculation approch than AaveV3.

I tested above things in HardHat By forking mainnet

So summery Of testing
Deposit to aave pool 100USDC
Then checked my account's
- scaledBalance (`aToken.scaledBalanceOf(deployer.address)`) : 92955238
- balanceOf (`aToken.balanceOf(deployer.address)`) : 100000000
- liquidityIndexAt (`lendingPool.getReserveNormalizedIncome(USDC_ADDRESS)`) : 1075786603020953661152106365

Then i go for withdraw
After withdraw my account's balances now
- USDC balance : 100000000
- scaledBalance (`aToken.scaledBalanceOf(deployer.address)`) : 0
- balanceOf (`aToken.balanceOf(deployer.address)`) : 0

Same things i done but via size
`NOTE : here i use hardhat for aave, and size's Math.sol on Remix to get exact answer for calculation`

Deposit 100USDC to size (Considering first Deposit to SIZE and followed by first withdraw from SIZE)
- scaledBalanceBefore : 0
- SIZE's scaledBalanceAfter : 92955238
- SIZE's balanceOf : 100000000
- liquidityIndexAt (`lendingPool.getReserveNormalizedIncome(USDC_ADDRESS)`) : 1075786603020953661152106365
- scaledAmount : scaledBalanceAfter - scaledBalanceBefore =  92955238

Now minting of borrowAToken happens for USER
```solidity
state.data.borrowAToken.mintScaled(to, scaledAmount);
```

- USER's borrowATokens.scaledBalanceOf() : 92955238
- USER's borrowATokens.balanceOf() : 99999999
which clearly less than above aave example

This difference due to how `_unscaled` value calculated differently in SIZE and AAVE

SIZE use a Rounding Down Approch
```solidity
    function _unscale(uint256 scaledAmount) internal view returns (uint256) {
        return Math.mulDivDown(scaledAmount, liquidityIndex(), WadRayMath.RAY);
    }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/token/NonTransferrableScaledToken.sol#L98-L100

https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/Math.sol#L27-L29
Due to this User Balane is 1 wei less

And when User go for withdraw, withdraw() has following code segment
```solidity
            amount = Math.min(params.amount, state.data.borrowAToken.balanceOf(msg.sender));
            if (amount > 0) {
                state.withdrawUnderlyingTokenFromVariablePool(msg.sender, params.to, amount);
            }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Withdraw.sol#L55-L58

which call further

```solidity
    function withdrawUnderlyingTokenFromVariablePool(State storage state, address from, address to, uint256 amount)
        external
    {
        IAToken aToken =
            IAToken(state.data.variablePool.getReserveData(address(state.data.underlyingBorrowToken)).aTokenAddress);

        uint256 scaledBalanceBefore = aToken.scaledBalanceOf(address(this));

        // slither-disable-next-line unused-return
        state.data.variablePool.withdraw(address(state.data.underlyingBorrowToken), amount, to);

        uint256 scaledAmount = scaledBalanceBefore - aToken.scaledBalanceOf(address(this));

        state.data.borrowAToken.burnScaled(from, scaledAmount);
    }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L74-L88

AS SIZE here try to withdraw `99999999` from aave

scaledAmountRemain = 92955238 - 92955237 = 1
balanceRemain = 99999999 - 99999998 = 1

That some Dust remain in User account, which in longer run could be become significant amount.

#### Test Code
```solidity
const { ethers } = require("hardhat");

// Aave V3 Lending Pool address and USDC token address on Ethereum mainnet
// const LENDING_POOL_ADDRESS = "0x7B2c0A2F4E4627bF6A7BdA8C83B29170356bF9b3"; 
const lendingPoolAddress = "0x87870Bca3F3fD6335C3F4ce8392D69350B4fA4E2";
const USDC_ADDRESS = "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"; 
const AAVE_PROTOCOL_DATA_PROVIDER = "0x7B4EB56E7CD4b454BA8ff71E4518426369a138a3"; 

// Simplified ERC20 ABI
const ERC20_ABI = [
  "function approve(address spender, uint256 amount) external returns (bool)",
  "function balanceOf(address account) external view returns (uint256)",
  "function transfer(address recipient, uint256 amount) returns (bool)",
  "function transferFrom(address sender, address recipient, uint256 amount) public returns (bool)",
  "function allowance(address owner, address spender) external view returns (uint256)"
];

// Simplified Aave Lending Pool ABI
const LENDING_POOL_ABI = [
  "function deposit(address asset, uint256 amount, address onBehalfOf, uint16 referralCode) external",
  "function getReserveNormalizedIncome(address asset) external view virtual override returns (uint256)",
  "function withdraw(address asset, uint256 amount, address to) public virtual override returns (uint256)"
];

// Simplified Aave Protocol Data Provider ABI
const DATA_PROVIDER_ABI = [
  "function getReserveTokensAddresses(address asset) external view returns (address aTokenAddress, address stableDebtTokenAddress, address variableDebtTokenAddress)"
  
];

// Simplified aToken ABI
const ATOKEN_ABI = [
  "function scaledBalanceOf(address user) external view returns (uint256)",
  "function balanceOf(address account) external view returns (uint256)"
];

async function main() {
    const [deployer] = await ethers.getSigners();

    // Address with a large USDC balance (you can find such addresses on Etherscan)
    const richUSDCAccount = "0x4B16c5dE96EB2117bBE5fd171E4d203624B014aa";

    // Impersonate the rich USDC account
    await hre.network.provider.request({
      method: "hardhat_impersonateAccount",
      params: [richUSDCAccount],
    });
  
    const impersonatedSigner = await ethers.getSigner(richUSDCAccount);
  
    // Get USDC contract instance
     let usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, impersonatedSigner);
  
    // Transfer USDC to deployer address
    const transferAmount = ethers.utils.parseUnits("100", 6); // 1000 USDC
    await usdc.transfer(deployer.address, transferAmount);
  
    // Check the USDC balance of the deployer address
    const deployerBalance = await usdc.balanceOf(deployer.address);
    console.log(`Deployer USDC Balance: ${ethers.utils.formatUnits(deployerBalance, 6)} USDC`);
  
    // Stop impersonating the account
    await hre.network.provider.request({
      method: "hardhat_stopImpersonatingAccount",
      params: [richUSDCAccount],
    });

  // Get USDC contract instance
  usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, deployer);

  // Get Aave Protocol Data Provider contract instance
  const dataProvider = new ethers.Contract(AAVE_PROTOCOL_DATA_PROVIDER, DATA_PROVIDER_ABI, deployer);

  // Get Lending Pool contract instance
  const lendingPool = new ethers.Contract(lendingPoolAddress, LENDING_POOL_ABI, deployer);

  const amount = ethers.utils.parseUnits("100", 6); // 100 USDC with 6 decimals

  // Check USDC balance
  const balance = await usdc.balanceOf(deployer.address);
  console.log(`Balance: ${ethers.utils.formatUnits(balance, 6)} USDC`);

  if (balance.lt(amount)) {
    throw new Error("Insufficient USDC balance");
  }

  // if (allowance.lt(amount)) {
  //   await usdc.approve(lendingPoolAddress, amount);
  // }

   await usdc.approve(lendingPoolAddress, amount);

  // Check allowance
  const allowance = await usdc.allowance(deployer.address, lendingPoolAddress);
  console.log(`Allowance To Pool: ${ethers.utils.formatUnits(allowance, 6)} USDC`);

  // Deposit USDC into Aave
  await lendingPool.deposit(USDC_ADDRESS, amount, deployer.address, 0);

  const liquidityIdex = await lendingPool.getReserveNormalizedIncome(USDC_ADDRESS);
  console.log(`Liquidity Index`, liquidityIdex);

  // Fetch aToken address for USDC
  const { aTokenAddress } = await dataProvider.getReserveTokensAddresses(USDC_ADDRESS);
  const aToken = new ethers.Contract(aTokenAddress, ATOKEN_ABI, deployer);

  // Fetch scaledBalanceOf and balanceOf
  const scaledBalance = await aToken.scaledBalanceOf(deployer.address);
  const balanceOf = await aToken.balanceOf(deployer.address);

  console.log(`Scaled Balance before: ${scaledBalance.toString()}`);
  console.log(`Balance before: ${balanceOf.toString()}`);


  console.log(`///////////////////////// withdraw //////////////////////`);
  console.log(`///////////////////////// withdraw //////////////////////`);
  console.log();

  const testBalance = 99999999 // get from Size's Math.mulDivDown() by fitting above value.

  await lendingPool.withdraw(USDC_ADDRESS, testBalance, deployer.address);

    // Fetch scaledBalanceOf and balanceOf
    const scaledBalanceA = await aToken.scaledBalanceOf(deployer.address);
    const balanceOfA = await aToken.balanceOf(deployer.address);
  
    console.log(`Scaled Balance after: ${scaledBalanceA.toString()}`);
    console.log(`Balance after: ${balanceOfA.toString()}`);

    const balanceA = await usdc.balanceOf(deployer.address);
    console.log(`Balance After: ${ethers.utils.formatUnits(balanceA, 6)} USDC`);

}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });

```

#### Test Result
```solidity
sathya@DESKTOP-I3RU8Q3:~/aave-deposit$ npx hardhat run scripts/test2.js --network hardhat

Deployer USDC Balance: 100.0 USDC
Balance: 100.0 USDC
Allowance To Pool: 100.0 USDC
Liquidity Index BigNumber { value: "1075786603020953661152106365" }
Scaled Balance before: 92955238
Balance before: 100000000
///////////////////////// withdraw //////////////////////
///////////////////////// withdraw //////////////////////

Scaled Balance after: 1
Balance after: 1
Balance After: 99.999999 USDC
```

#### Mitigation 
Try to AAVE's calculation methods which are more precise

### [Low-05] Due to Rounding Down Protocol may loss some reward fee

In `executeLiquidate()` there is below code segment
```solidity
            // cap the collateral remainder to the liquidation collateral ratio
            //   otherwise, the split for non-underwater overdue loans could be too much
            uint256 collateralRemainderCap =
                Math.mulDivDown(debtInCollateralToken, state.riskConfig.crLiquidation, PERCENT);

            collateralRemainder = Math.min(collateralRemainder, collateralRemainderCap);

            protocolProfitCollateralToken = Math.mulDivDown(collateralRemainder, collateralProtocolPercent, PERCENT);
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol#L107-L112

where `protocolProfitCollateralToken` calculated on Profitable liquidation where `assignCollateral > debtInCollateral` and `protocolProfitCollateralToken` amount of token taken by protocol fee receipient.

Here we can see Double Rounding Down occurs in calculation.

first Rounding Down happens in calculation of Cap variable `collateralRemainderCap`

then let say `collateralRemainderCap` is minimum b/w `collateralRemainder & collateralRemainderCap` so collateralRemainder = collateralRemainderCap

And then second rounding down happens in `protocolProfitCollateralToken` calculation

#### Mitigation
its a standard that fee taken by protocol should always in favour of Protocol and always Rounding Up.



### [Low-06] `weth.forceApprove()` in `executeDeposit()` is not relevant

```solidity
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
....
....
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Deposit.sol#L67-L73

If we carefully observe here is that caller to weth i.e `address(this)` forceApproving same `address(this)` with amount. then further from overrided with address this

when actual token trasfer happening 
```solidity
    function depositUnderlyingCollateralToken(State storage state, address from, address to, uint256 amount) external {
        IERC20Metadata underlyingCollateralToken = IERC20Metadata(state.data.underlyingCollateralToken);
        underlyingCollateralToken.safeTransferFrom(from, address(this), amount);
        state.data.collateralToken.mint(to, amount);
    }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol#L24-L26

here when underlyingCollateralToken's transfer happening through safeTransferFrom
from = address(this)
to = address(this)

so basically self token transfering happen here. I don't see any use case here.

#### Mitigation
Re-configure function logic.



### [Low-07] `executeLiquidateWithReplacement()`'s Natspec comment and Code contradicting each other

```solidity
....
....
    /// @return liquidatorProfitCollateralToken The amount of collateral tokens liquidator received from the liquidation
    /// @return liquidatorProfitBorrowToken The amount of borrow tokens liquidator received from the liquidation
    function liquidateWithReplacement(LiquidateWithReplacementParams calldata params)
        external
        payable
        returns (uint256 liquidatorProfitCollateralToken, uint256 liquidatorProfitBorrowToken);
```
https://github.com/code-423n4/2024-06-size/blob/main/src/interfaces/ISize.sol#L149-L153

`ISizePool.liquidateWithReplacement()` says that `liquidatorProfitBorrowToken` amount will send to `liquidator` who is in this case `KEEPER_ROLE`

But in actual code `liquidatorProfitBorrowToken` send to feeRecipient (`state.feeConfig.feeRecipient`)

```solidity
        state.data.borrowAToken.transferFrom(address(this), state.feeConfig.feeRecipient, liquidatorProfitBorrowToken);
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/LiquidateWithReplacement.sol#L161-L162



### [Low-08] By Default A Newly Created CreditPosition Is Set To `For Sale = True` Which Could Problematic

If user's didn't configured eairlier, then may be his Buyed credit could be taken by other lenders without his intention, as by default CreditPosition's forSale parameter set as TRUE.

OR

A senario, a User has Multiple Credit Positions and he want to sell Some only but not others(which are more profitable for him), in that case he has to

As for below code segment from `BuyCreditMarket.sol`'s `validateBuyCreditMarket()` if no configuration happen for User then `user.allCreditPositionsForSaleDisabled = false` so in that case he has to set `allCreditPositionsForSaleDisabled = false`, if any good offfer he could be front-runned before he goes and made modification in `SetUserConfiguration.sol` contract for his newly created position.

I know multicall available to deal this but, when User does normal Tx their could be front-run chance.

```solidity
            if (user.allCreditPositionsForSaleDisabled || !creditPosition.forSale) {
                revert Errors.CREDIT_NOT_FOR_SALE(params.creditPositionId);
            }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/BuyCreditMarket.sol#L75-L77

#### Miigation
Its make compulsory for User to set configuration, or newly created credit position should created with `forSale = false` & protocol have feature that could change its later. 

### [Low-09] Chainlink Price Feed Have some missing checks for Validation
```diff
-       (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
+       (uint80 roundId, int256 price,, uint256 updatedAt, uint80 answeredInRound) = aggregator.latestRoundData();

        if (price <= 0) revert Errors.INVALID_PRICE(address(aggregator), price);
        if (block.timestamp - updatedAt > stalePriceInterval) {
            revert Errors.STALE_PRICE(address(aggregator), updatedAt);
        }
+       require(answeredInRound >= roundId, "price is stale");
+       require(updatedAt > 0, "round is incomplete");
```


### [Low-10] PriceFeed Will Use The Wrong Price If The Chainlink Registry Returns Price Outside min/max Range 

Chainlink aggregators have a built in circuit breaker if the price of an asset goes outside of a predetermined price band. The result is that if an asset experiences a huge drop in value (i.e. LUNA crash) the price of the oracle will continue to return the minPrice instead of the actual price of the asset. This would allow user to continue borrowing with the asset but at the wrong price. This is exactly what happened to [Venus on BSC when LUNA imploded](https://rekt.news/venus-blizz-rekt/).

aggregator#latestRoundData pulls the associated aggregator and requests round data from it. ChainlinkAggregators have minPrice and maxPrice circuit breakers built into them. This means that if the price of the asset drops below the minPrice, the protocol will continue to value the token at minPrice instead of it's actual value. 

Example: TokenA has a minPrice of $1. The price of TokenA drops to $0.10. The aggregator still returns $1 

```diff
    (, int256 price,, uint256 updatedAt,) = aggregator.latestRoundData();
    
+   if (price >= maxPrice or price <= minPrice) revert();
```
https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol#L86

#### Mitigation
Mentioned in above code segment


### [Low-11] Function May Be Trying To transfer 0 amount

Below we can see that `protocolProfitCollateralToken` could be 0, if the liquidation is not profitable

so in that case `state.feeConfig.feeRecipient` transfered with 0 amount.
```solidity
        uint256 assignedCollateral = state.getDebtPositionAssignedCollateral(debtPosition);
        uint256 debtInCollateralToken = state.debtTokenAmountToCollateralTokenAmount(debtPosition.futureValue);
        uint256 protocolProfitCollateralToken = 0;
        // profitable liquidation
        if (assignedCollateral > debtInCollateralToken) {
 ...
...
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
+       if (protocolProfitCollateralToken !=0){
        state.data.collateralToken.transferFrom(
            debtPosition.borrower, state.feeConfig.feeRecipient, protocolProfitCollateralToken
        )};
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol#L92-L122

#### Mitigation
Only transfer token when transfered amount is non-zero as showed above 




### [Low-12] Many NatSpec Comments Are Wrong
#### Input Params and params mentioned in NatSpec mismatched in `Compensate()`, create huge confusion.

ISizePool.compensate() has following Natspec
```solidity
    ///     - uint256 debtPositionToRepayId: The id of the debt position to repay
    ///     - uint256 creditPositionToCompensateId: The id of the credit position to compensate
    ///     - uint256 amount: The amount of tokens to compensate (in decimals, e.g. 1_000e6 for 1000 aUSDC)
```
https://github.com/code-423n4/2024-06-size/blob/main/src/interfaces/ISize.sol#L159-L161

while the struct `CompensateParams` that passed as parameter to `compensate()` has following namings
```solidity
    struct CompensateParams {
     
        uint256 creditPositionWithDebtToRepayId;
     
        uint256 creditPositionToCompensateId;
     
        uint256 amount;
    }
```
https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Compensate.sol#L16-L25

Here we clearly shows that 1st parameter is mismatched in both which create huge confusion due to their naming.


### [Low-13] Repay Should Allowed In Paused Event Period or User Should Allowed Some Grace Period After Unpause To Supply Collateral And Escape From UnWanted Liquidation. 

`ISizeAdmin.sol` have some admin functions like 
```solidity
    /// @notice Pauses the protocol
    ///         Only callabe by the DEFAULT_ADMIN_ROLE
    function pause() external;

    /// @notice Unpauses the protocol
    ///         Only callabe by the DEFAULT_ADMIN_ROLE
    function unpause() external;
}
```
https://github.com/code-423n4/2024-06-size/blob/main/src/interfaces/ISizeAdmin.sol#L25-L32

So basically whole system goes into pause/unpause mode when this functions called including some crucial functions like `repay` `deposit` etc

if in this duration, some major market correction happens / market dip appears then Collateral ratios of Users could fall below liquidation and they subject to liquidation, (and User can do nothing about it, nither they increase their CR via depositing more underlayingCollateral token, no settle their loans)

On very next moment profitable liqudit position get liqudated by liquidators or Bots.

#### Mitigation
To Deal with this type of situation Protocol should allow some functions to operate even Pause mode Or should give some grace period to Borrowers to take decision to To rise their CRs above thresold or wish to getliquidated.