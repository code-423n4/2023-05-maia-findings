# [L-01] Empty payable fallback() function does not authenticate requests
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue unused Ether.        
       
There are 3 instances of this issue:
````solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1419:    fallback() external payable {}
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419)
````solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

284:    fallback() external payable {}
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L284](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L284)
````solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1334:    fallback() external payable {}
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1334](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1334)
# [L-02] Solmate safeTransfer and safeTransferFrom does not check the codesize of the token address, which may lead to fund loss
## Lines of code
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L127](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L127)
## Vulnerability details
### Impact
Solmate safeTransfer and safeTransferFrom does not check the codesize of the token address, which may lead to fund loss
### Proof of concept
Solmate safetransferFrom doesn’t check the existence of code at the token address, all contract below that use SafeTransferLib and and all safeTransfer and safeTransferFrom has the issue below . The impacted function and LOC is linked above.
````solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol
17    contract FlywheelInstantRewards is BaseFlywheelRewards, IFlywheelInstantRewards {
18        using SafeTransferLib for ERC20;
19:       using SafeCastLib for uint256;

File: src/ulysses-amm/factories/UlyssesFactory.sol
38    contract UlyssesFactory is Ownable, IUlyssesFactory {
39        using SafeTransferLib for ERC20;
40:       using FixedPointMathLib for uint256;

File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
67    contract ArbitrumBranchBridgeAgent is BranchBridgeAgent {
68        using SafeTransferLib for address;
69:       using SafeTransferLib for ERC20;
````
This is a known issue while using solmate’s libraries.
Hence this may lead to miscalculation of funds and may lead to loss of funds , because if safetransfer() and safetransferfrom() are called on a token address that doesn’t have contract in it, it will always return success, bypassing the return value check.        
      
If a underlying token is self-destructed, the code may consider the transfer because the lack of contract existence contract.      
      
Due to this protocol will think that funds has been transferred and successful , and records will be accordingly calculated, but in reality funds were never transferred.      
        
So this will lead to miscalculation and possibly loss of funds.  
## Tools Used
Manual Review
## Recommended mitigation steps
Use openzeppelin’s safeERC20 or implement a code existence check.
# [N-01] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING
Consider changing the variable to be an unnamed one.
- ERC20Boost gauges()
- ERC20Boost userGauges() 
- ERC20Gauges gauges()
- ERC20Gauges userGauges()
- ERC20Gauges _incrementUserAndGlobalWeights()
- ERC20Gauges _decrementUserWeights()
- ERC20Gauges _addGauge()
- ERC4626 deposit()
- ERC4626 mint()
- ERC4626 withdraw()
- ERC4626 redeem()
- ERC4626DepositOnly deposit()
- ERC4626DepositOnly mint()
- ERC4626MultiToken deposit()
- ERC4626MultiToken mint()
- ERC4626MultiToken withdraw()
- ERC4626MultiToken redeem()
- ERC4626MultiToken convertToShares()
- ERC4626MultiToken convertToAssets()
- ERC4626MultiToken previewMint()
- ERC4626MultiToken previewWithdraw()
- UlyssesERC4626 deposit()
- UlyssesERC4626 mint()
- UlyssesERC4626 redeem()
- UlyssesERC4626 beforeDeposit()
- UlyssesERC4626 beforeMint()
- UlyssesERC4626 afterRedeem()
- BaseV2GaugeFactory newGauge()
- BaseV2Minter getRewards()
- DateTimeLib getMonth()
- DateTimeLib isTuesday()
- FlywheelCore setBooster()
- RewardsDepot transferRewards()
- FlywheelAccumulatedRewards getAccruedRewards()
- FlywheelGaugeRewards queueRewardsForCycle()
- FlywheelGaugeRewards getAccruedRewards()
- TalosManager checkUpkeep()
- TalosStrategyVanilla _compoundFees()
- TalosBaseStrategy init()
- TalosBaseStrategy deposit()
- TalosBaseStrategy redeem()
- TalosBaseStrategy doRerange()
- TalosBaseStrategy doRebalance()
- TalosBaseStrategyFactory createTalosV3Strategy()
- TalosStartegyStakedFactory createTalosV3Strategy()
- PoolActions rerange()
- PoolActions getThisPositionTicks()
- PoolVariables getPositionTicks()
- PoolVariables amountsForTicks()
- PoolVariables baseTicks()
- PoolVariables amountsDirection()
- PoolVariables getSwapToEqualAmountsParams()
- TalosStrategySimple doRerange()
- TalosStrategySimple doRebalance()
- UlyssesPool claimProtocolFees()
- UlyssesPool addNewBandwidth()
- UlyssesPool getBandwidthUpdateAmounts()
- UlyssesPool updateBandwidth()
- UlyssesPool _calculateRebalancingFee()
- UlyssesPool calcFee()
- UlyssesPool ulyssesSwap()
- UlyssesPool ulyssesAddLP()
- UlyssesPool ulyssesRemoveLP()
- UlyssesPool swapIn()
- UlyssesPool swapFromPool()
- UlyssesPool beforeDeposit()
- UlyssesPool beforeMint()
- UlyssesPool afterRedeem()
- UlyssesRouter getUlyssesLP()
- UlyssesFactory _createPool()
- UlyssesFactory createPools()
- UlyssesFactory createToken()
- ArbitrumBranchBridgeAgent _gasSwapIn()
- BranchBridgeAgent _gasSwapIn()
- BranchBridgeAgent _getContext()
- BranchBridgeAgent _normalizeDecimalsMultiple()
- BranchBridgeAgentExecutor executeNoSettlement()
- BranchBridgeAgentExecutor executeWithSettlement()
- BranchBridgeAgentExecutor executeWithSettlementMultiple()
- MulticallRootRouter _multicall()
- RootBridgeAgent _getContext()
- RootBridgeAgentExecutor executeSystemRequest()
- RootBridgeAgentExecutor executeNoDeposit()
- RootBridgeAgentExecutor executeWithDeposit()
- RootBridgeAgentExecutor executeWithDepositMultiple()
- RootBridgeAgentExecutor executeSignedNoDeposit()
- RootBridgeAgentExecutor executeSignedWithDeposit()
- RootBridgeAgentExecutor executeSignedWithDepositMultiple()
- RootBridgeAgentExecutor executeRetrySettlement()
- RootBridgeAgentExecutor _bridgeInMultiple()
- RootPort fetchVirtualAccount()
- RootPort addVirtualAccount()
- VirtualAccount call()
- ArbitrumBranchBridgeAgentFactory createBridgeAgent()
- BranchBridgeAgentFactory createBridgeAgent()
- ERC20hTokenBranchFactory createToken()
- ERC20hTokenRootFactory createToken()
- RootBridgeAgentFactory createBridgeAgent()
- UniswapV3Staker stakes()
- UniswapV3Staker endIncentive()
- UniswapV3Staker claimReward()
- UniswapV3Staker claimAllRewards()
- UniswapV3Staker getRewardInfo()
- IncentiveTime getEnd()
- IncentiveTime getEndAndDuration()
- NFTPositionInfo getPositionInfo()
- RewardMath computeBoostedSecondsInsideX128()
# [N-02] Immutables should be in uppercase
````solidity
File: src/erc-20/ERC20Gauges.sol

39:    uint32 public immutable override gaugeCycleLength;

42:    uint32 public immutable override incrementFreezeWindow;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L39](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L39)
````solidity
File: src/erc-4626/ERC4626.sol

21:    ERC20 public immutable asset;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L21](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L21)
````solidity
File: src/erc-4626/ERC4626DepositOnly.sol

21:    ERC20 public immutable asset;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L21](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L21)
````solidity
File: src/erc-4626/UlyssesERC4626.sol

22:    address public immutable asset;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L22](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L22)
````solidity
File: src/gauges/BaseV2Gauge.sol
26:    address public immutable override rewardToken;

29:    bHermesBoost public immutable hermesGaugeBoost;

32:    FlywheelGaugeRewards public immutable override flywheelGaugeRewards;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L26](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L26)
````solidity
File: src/gauges/UniswapV3Gauge.sol

20:    address public immutable override uniswapV3Staker;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L20](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L20)
````solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

24:    BaseV2GaugeManager public immutable override gaugeManager;

27:    bHermesBoost public immutable override bHermesBoostToken;

30:    BribesFactory public immutable override bribesFactory;
````
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L24](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L24)