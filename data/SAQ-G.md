## Summary

### Gas Optimization

no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] | Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if statement | 19 | - |
| [G-02] | Using PRIVATE rather than PUBLIC for Constans, Saves Gas | 12 | - |
| [G-03] | Can make the variable outside the loop to save gas | 12 | - |
| [G-04] | Using fixed bytes is cheaper than using string | 2 | - |
| [G-05] | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 2 | - |
| [G-06] | State variables should be cached in stack variables rather than re-reading them from storage | 9 | - |
| [G-07] | Require() or Revert() statement should be used sorted from cheapest to most expensive | 2 | - |
| [G-08] | State Variable can be packed into fewer storage slots | 1 | - |
| [G-09] | Require() or revert() statements that check input arguments should be at the top of the function | 1 | - |
| [G-10] | The result of function calls should be cached rather than re-calling the function | 2 | - |
| [G-11] | Duplicated require()/if() checks should be refactored to a modifier or function | 8 | - |
| [G-12] | Use constants instead of type(uintx).max | 13 | - |
| [G-13] | Multiple accesses of a mapping/array should use a local variable cache | 5 | - |
| [G-14] | Caching global variables is more expensive than using the actual variable (use msg.sender instead of caching it) | 5 | - |
| [G-15] | abi.encode() is less efficient than  abi.encodepacked() | 11 | - |
| [G-16] | Do not calculate  constant | 2 | - |
| [G-17] | Using delete statement can save gas | 15 | - |
| [G-18] | >= costs less gas than > | 9 | - |
| [G-19] | Using ERC721A instead of ERC721 for more gas-efficient | 1 | - |
| [G-20] | Use hardcode address instead of address(this) | 34 | - |
| [G-21] | Division by two should use bit shifting | 3 | - |
| [G-22] | Use assembly for math (add, sub, mul, div) | 22 | - |
| [G-23] | Use != 0 instead of > 0 for unsigned integer comparison | 3 | - |
| [G-24] | Refactor event to avoid emitting empty data | 2 | - |
| [G-25] | Sort Solidity operations using short-circuit mode | 10 | - |


## Gas Optimizations  

## [G-1] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if statement  

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
This will stop the check for overflow and underflow so it will save gas

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

90        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);

132       require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L90

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol
///@audit a-b      should unchecked.
532        require(b <= a, "subtraction underflow");
533        return a - b;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L532-L533

```solidity
file: /src/ulysses-amm/UlyssesPool.sol

139            return balance - assets;

195             newBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;

218            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

260            leftOverBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;

275            leftOverBandwidth += oldBandwidth - poolState.bandwidth;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L139


```solidity
file: /src/ulysses-omnichain/RootPort.sol

282         if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282

```solidity
File: /src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

///@audit _data.length - PARAMS_GAS_OUT   should unchecked.

147     (success, result) = IRouter(_router).anyExecuteSettlementMultiple(
148                _data[
149                   PARAMS_END_SIGNED_OFFSET + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * PARAMS_TKN_SET_SIZE):
150                        _data.length - PARAMS_GAS_OUT

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L14147-L150


```solidity
file: /src/hermes/minters/BaseV2Minter.sol

140      HERMES(underlying).mint(address(this), _required - _balanceOf);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140


```solidity
file: /src/ulysses-omnichain/ArbitrumBranchPort.sol

146        _depositor, _localAddresses[i], _amounts[i] - _deposits[i]

124         IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(_depositor, _localAddress, _amount - _deposit);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L146

```solidity
file: /src/ulysses-omnichain/BranchPort.sol

249            _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);
250             ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);


276            localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);
277            ERC20hTokenBranch(_localAddresses[i]).burn(_amounts[i] - _deposits[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L249-L250


```solidity
file: /src/ulysses-omnichain/RootBridgeAgentExecutor.sol

138          _data[112], _data[113:_data.length - PARAMS_GAS_IN], dParams, _fromChainId

242          _data[132], _data[133:_data.length - PARAMS_GAS_IN], dParams, _account, _fromChainId

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L138

```solidity
file: /src/ulysses-omnichain/RootBridgeAgent.sol

823        accumulatedFees += availableGas - minExecCost;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L823


```solidity
file: /src/ulysses-omnichain/BranchBridgeAgent.sol

624            IPort(localPortAddress).bridgeIn(_recipient, _hTokens[i], _amounts[i] - _deposits[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L624


## [G-2] Using PRIVATE rather than PUBLIC FOR Constans, Saves Gas        

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

9    string public constant name = "vMaia Governor Bravo";
10
11   /// @notice The minimum setable proposal threshold
12    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken
13
14    /// @notice The maximum setable proposal threshold
15    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken
16
17    /// @notice The minimum setable voting period
18    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks
19
20    /// @notice The max setable voting period
21    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks
22
23    /// @notice The min setable voting delay
24    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks
25
26    /// @notice The max setable voting delay
27    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks
28
29    /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed
30    uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken
31
32    /// @notice The maximum number of actions that can be included in a proposal
33    uint256 public constant proposalMaxOperations = 10; // 10 actions
34
35    /// @notice The divisor value used in percentage calculations
36    uint256 public constant DIVISIONER = 1 ether;


42    bytes32 public constant DOMAIN_TYPEHASH =

46    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9-L36


## [G-3]   Can make the variable outside the loop to save gas

Consider making the stack variables before the loop which gonna save gas

```solidity
file: /src/ulysses-amm/UlyssesToken.sol

112            uint256 assetBalance = assets[i].balanceOf(address(this));
113            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112-L113


```solidity
file: /src/ulysses-amm/UlyssesPool.sol

131            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

176            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

190            uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

212            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

233            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

255            uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

297            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

908            uint256 updateAmount = bandwidthUpdateAmounts[i];

963            uint256 updateAmount = bandwidthUpdateAmounts[i];

1045           uint256 updateAmount = bandwidthUpdateAmounts[i];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L131


## [G-4] Using fixed bytes is cheaper than using string


As a rule of thumb, use bytes for arbitrary-length raw byte data and string for arbitrary-length string (UTF-8) data.
If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

```solidity
file: /src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

50        string memory symbol = ERC20(_underlyingAddress).symbol();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L50


```solidity
file: /src/ulysses-amm/UlyssesToken.sol

26        string memory _symbol,

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L26



## [G-5]  Expressions for constant values such as a call to keccak256(), should use immutable rather than constant


```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

42      bytes32 public constant DOMAIN_TYPEHASH =
43        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");


46     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42



## [G-6] State variables should be cached in stack variables rather than re-reading them from storage


The instances below point to the second+ access of a state variable within a function.
Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read.
Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.
Most of the times this if statement will be true and we will save 100 gas at a small possibility of 3 gas loss ,


```solidity
file: /src/talos/boost-aggregator/BoostAggregator.sol

/// @audit 'uniswapV3Staker'   is state variable, its access multiple time in one function on lines: 114,116,127,130 

109    function unstakeAndWithdraw(uint256 tokenId) external {
110        address user = tokenIdToUser[tokenId];
111        if (user != msg.sender) revert NotTokenIdOwner();
112
113        // unstake NFT from Uniswap V3 Staker
114        uniswapV3Staker.unstakeToken(tokenId);
115
116        uint256 pendingRewards = uniswapV3Staker.tokenIdRewards(tokenId) - tokenIdRewards[tokenId];
117
118        if (pendingRewards > DIVISIONER) {
119            uint256 newProtocolRewards = (pendingRewards * protocolFee) / DIVISIONER;
120            /// @dev protocol rewards stay in stake contract
121            protocolRewards += newProtocolRewards;
122            pendingRewards -= newProtocolRewards;
123
124            address rewardsDepot = userToRewardsDepot[user];
125            if (rewardsDepot != address(0)) {
126                // claim rewards to user's rewardsDepot
127                uniswapV3Staker.claimReward(rewardsDepot, pendingRewards);
128            } else {
129                // claim rewards to user
130                uniswapV3Staker.claimReward(user, pendingRewards);
131           }
132        }
133
134        // withdraw rewards from Uniswap V3 Staker
135        uniswapV3Staker.withdrawToken(tokenId, user, "");
136    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L109-L136


```solidity
file: /src/ulysses-amm/UlyssesPool.sol
///@audit "totalWeights"  is state variable, second+ accessed within one function.

233        uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
238        uint256 oldTotalWeights = totalWeights;
241        totalWeights = newTotalWeights;
291        uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L233


```solidity
file: /src/ulysses-omnichain/CoreRootRouter.sol
///@audit "bridgeAgentExecutorAddress"  is state variable, second accessed within one function.

65        bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L65


```solidity
file: /src/ulysses-omnichain/ArbitrumBranchPort.sol

///@audit "localChainId"   is state variable, second accessed within one function.

62        if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {
            revert UnknownToken();
        }

66        address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);


///@audit "rootPortAddress"   is state variable, second accessed within one function.

66        address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);
70        IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L62-L66

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

///@audit "gaugeToken"   is state variable, second accessed within one function.

139        uint256 remaining = gaugeToken.numGauges() - offset;
153        address[] memory gauges = gaugeToken.gauges(offset, numRewards);

///@audit "rewardToken"   is state variable, second accessed within one function.

88        uint256 balanceBefore = rewardToken.balanceOf(address(this));
90        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);

///@audit "gaugeCycle"   is state variable, second accessed within one function.

80        uint32 lastCycle = gaugeCycle;
85        gaugeCycle = currentCycle;


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L139

```solidity
file: /src/ulysses-omnichain/BranchPort.sol
///@audit "coreBranchRouterAddress"  is state variable, second accessed within one function.

100        require(coreBranchRouterAddress == address(0), "Contract already initialized");
106        coreBranchRouterAddress = _coreBranchRouter;


302        require(coreBranchRouterAddress != address(0), "CoreRouter address is zero");
304        coreBranchRouterAddress = _newCoreRouter;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L100


## [G-7]  Require() or Revert() statement should be used sorted from cheapest to most expensive 

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

```solidity
file: /src/ulysses-omnichain/RootPort.sol

130        require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");
131        require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L130-L131

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

362        require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");
363        require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L362-L363


## [G-8]  State Variable can be packed into fewer storage slots    				

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas).Reads of the variables are also cheaper

```solidity
file: /src/talos/TalosOptimizer.sol
17   /// @inheritdoc ITalosOptimizer
18    uint32 public override twapDuration;
19    /// @inheritdoc ITalosOptimizer
20    int24 public override maxTwapDeviation;
21    /// @inheritdoc ITalosOptimizer
22    int24 public override tickRangeMultiplier;
23    /// @inheritdoc ITalosOptimizer
24    uint24 public override priceImpactPercentage;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L17-L24

### Recommended code

```solidity
    17    /// @inheritdoc ITalosOptimizer
    18    uint32 public override twapDuration;
    23    /// @inheritdoc ITalosOptimizer
+   24    uint24 public override priceImpactPercentage;
    19    /// @inheritdoc ITalosOptimizer
    20    int24 public override maxTwapDeviation;
    21    /// @inheritdoc ITalosOptimizer
    22    int24 public override tickRangeMultiplier;

```

## [G-9] Require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.


```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

90        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L90


## [G-10] The result of function calls should be cached rather than re-calling the function

The instances below point to the second+ call of the function within a single function


```solidity
file: /src/talos/boost-aggregator/BoostAggregator.sol

///@audit "uniswapV3Staker"    is a function ,its second+ accessd in one functions

109      function unstakeAndWithdraw(uint256 tokenId) external {

114        uniswapV3Staker.unstakeToken(tokenId);

116        uint256 pendingRewards = uniswapV3Staker.tokenIdRewards(tokenId) - tokenIdRewards[tokenId];

127                uniswapV3Staker.claimReward(rewardsDepot, pendingRewards);

130                uniswapV3Staker.claimReward(user, pendingRewards);

135        uniswapV3Staker.withdrawToken(tokenId, user, "");    

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L109-L136

```solidity
file: /src/talos/boost-aggregator/BoostAggregator.sol
///@audit "hermesGaugeBoost"    is a function ,its second+ accessd in one functions


165    function withdrawAllGaugeBoost(address to) external onlyOwner {
166        /// @dev May run out of gas.
167        hermesGaugeBoost.decrementAllGaugesAllBoost();
168        address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));
169    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L165


## [G-11] Duplicated require()/if() checks should be refactored to a modifier or function   

   to reduce code duplication and improve readability.
•  Modifiers can be used to perform additional checks on the function inputs or state before it is executed. By defining a modifier to perform a specific check, we can reuse it across multiple functions that require the same check.
• A function can also be used to perform a specific check and return a boolean value indicating whether the check has passed or failed. This can be useful when the check is more complex and cannot be performed easily in a modifier.


```solidity
file: /src/talos/TalosManager.sol

101        if (getRebalance(strategy)) {
113        if (getRebalance(strategy)) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L101


```solidity
file: /src/gauges/factories/BaseV2GaugeManager.sol

65            if (activeGaugeFactories[_gaugeFactories[i]]) _gaugeFactories[i].newEpoch();
81            if (activeGaugeFactories[_gaugeFactories[i]]) _gaugeFactories[i].newEpoch();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L65


```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

83        if (currentCycle <= lastCycle) revert CycleError();
118       if (currentCycle <= lastCycle) revert CycleError();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L83


```solidity
file: /src/ulysses-omnichain/BranchPort.sol

173        if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();]
353        if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L173

```solidity
file: /src/ulysses-omnichain/MulticallRootRouter.sol

194        if (funcId == 0x01) {
277        if (funcId == 0x01) {
353        if (funcId == 0x01) {
429        if (funcId == 0x01) {    


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L194


```solidity
file: /src/ulysses-omnichain/RootPort.sol

280        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L280

```solidity
file: /src/ulysses-amm/UlyssesPool.sol

217        if (oldRebalancingFee < newRebalancingFee) {
302        if (oldRebalancingFee < newRebalancingFee) {    

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L217


```solidity
file: /src/talos/TalosOptimizer.sol

42        if (_maxTwapDeviation < 20) revert MaxTwapDeviationTooLow();
75

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L42


## [G-12] Use constants instead of type(uintx).max

type(uint120).max or type(uint128).max, type(uint112).max ,etc. it uses more gas in the distribution process and also for each transaction than constant usage.

```solidity
file: /src/talos/TalosStrategyVanilla.sol

111                amount0Max: type(uint128).max,

112                amount1Max: type(uint128).max

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L111-L112

```solidity
file: /src/talos/TalosStrategyStaked.sol

151                    amount0Max: type(uint128).max,

152                    amount1Max: type(uint128).max

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L151-L152

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

133            require(newRewards <= type(uint112).max); // safe cast

187            require(nextRewards <= type(uint112).max); // safe cast

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L133


```solidity
file: /src/uni-v3-staker/UniswapV3Staker.sol

385             amount0Max: type(uint128).max,

386             amount1Max: type(uint128).max

456             if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

506             if (liquidity >= type(uint96).max) {

509             liquidityNoOverflow: type(uint96).max,

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L385-L386


```solidity
file: /src/gauges/UniswapV3Gauge.sol

45        rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L45


```solidity
file: /src/ulysses-amm/UlyssesRouter.sol

40            address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40


## [G-13] Multiple accesses of a mapping/array should use a local variable cache

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory/calldata.

```solidity
file: /src/ulysses-amm/UlyssesToken.sol
///@audit assetId[asset]
62        uint256 assetIndex = assetId[asset] - 1;

78        assetId[asset] = 0;

///@audit weights[assetIndex]

68        totalWeights -= weights[assetIndex];

74        weights[assetIndex] = weights[newAssetsLength];

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L62

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

///@audit gaugeQueuedRewards[gauge]

179            QueuedRewards memory queuedRewards = gaugeQueuedRewards[gauge];

189            gaugeQueuedRewards[gauge] = QueuedRewards({

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L179

```solidity
file: /src/ulysses-omnichain/RootBridgeAgentExecutor.sol
///@audit _data[PARAMS_START]

164         PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE

169         uint8 numOfAssets = uint8(bytes1(_data[PARAMS_START]));

174         > PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE

///@audit _data[PARAMS_START_SIGNED]

271        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * 

280        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE

285        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE

288        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L164


## [G-14] Caching global variables is more expensive than using the actual variable (use msg.sender instead of caching it)


```solidity
file: /src/governance/GovernorBravoDelegator.sol

18        admin = msg.sender;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L18

```solidity
file: /src/rewards/depots/SingleRewardsDepot.sol

24        rewardsContract = msg.sender;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L24

```solidity
file: /src/hermes/tokens/bHermesBoost.sol

24        bHermes = msg.sender;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L24

```solidity
file: /src/hermes/tokens/bHermesGauges.sol

31        bHermes = msg.sender;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L31


```solidity
file: /src/hermes/minters/BaseV2Minter.sol

59:         initializer = msg.sender;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L59


## [G-15] abi.encode() is less efficient than  abi.encodepacked()

In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.

Reference: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison

```solidity
file: /src/ulysses-omnichain/CoreBranchRouter.sol

48        bytes memory data = abi.encode(msg.sender, _globalAddress, _toChain, _rootExecutionGas);

72        bytes memory data = abi.encode(_underlyingAddress, newToken, name, symbol);

105       bytes memory data = abi.encode(_globalAddress, newToken);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L48

```solidity
file: /src/ulysses-omnichain/RootBridgeAgent.sol

689            abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

733            abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

198        !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

347        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L198

```solidity
file: /src/ulysses-omnichain/CoreRootRouter.sol

282        bytes memory data = abi.encode(_underlyingToken, _minimumReservesRatio);

309        bytes memory data = abi.encode(_portStrategy, _underlyingToken, _dailyManagementLimit, _isUpdateDailyLimit);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L282


```solidity
file: /src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

53        bytes memory data = abi.encode(_underlyingAddress, address(0), name, symbol);

98        bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L53


## [G-16] Do not calculate  constant

When you define a constant in Solidity, the compiler can calculate its value at compile-time and replace all references to the constant with its computed value. This can be helpful for readability and reducing the size of the compiled code, but it can also increase gas usage at runtime.

```solidity
file: /src/hermes/minters/BaseV2Minter.sol

24    uint256 internal constant week = 86400 * 7;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L24

```solidity
file: /src/talos/TalosStrategyVanilla.sol

47    uint24 private constant protocolFee = 2 * 1e5; //20%

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47


## [G-17] Using delete statement can save gas

```solidity
file: /src/ulysses-amm/UlyssesToken.sol

78        assetId[asset] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L78

```solidity
file: /src/hermes/minters/BaseV2Minter.sol

165        weekly = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L165

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

100            nextCycleQueuedRewards = 0;

101            paginationOffset = 0;

122            paginationOffset = 0;

145            nextCycleQueuedRewards = 0;

146            paginationOffset = 0;

225            cycleRewardsNext = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L100

```solidity
file: /src/uni-v3-staker/UniswapV3Staker.sol

202            incentive.totalRewardUnclaimed = 0;

268            rewards[msg.sender] = 0;

279            rewards[msg.sender] = 0;

426            deposit.stakedTimestamp = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L202

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

156        newProposal.forVotes = 0;

157        newProposal.againstVotes = 0;

158        newProposal.abstainVotes = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L156


## [G-18] >= costs less gas than >
 
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas 
Reference: https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde


```solidity
file: /src/gauges/factories/BaseV2GaugeManager.sol

78        if (end > length) end = length;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L78


```slidity
file: /src/ulysses-omnichain/BranchBridgeAgent.sol

1328        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328

```solidity
file: /src/ulysses-amm/UlyssesToken.sol

47        require(_weight > 0);

115       if (assetBalance > newAssetBalance) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47

```solidity
file: /src/talos/TalosStrategyVanilla.sol

139        if (_liquidity > 0) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139

```solidity
file: /src/ulysses-amm/factories/UlyssesFactory.sol

111        if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111

```solidity
file:/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

160        if (gasRemaining > 0) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

234        if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234

```solidity
file: /src/ulysses-omnichain/BranchPort.sol

268            if (_deposits[i] > 0) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L268

```solidity
file: /src/uni-v3-staker/UniswapV3Staker.sol

271        if (reward > 0) hermes.safeTransfer(to, reward);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L271


## [G-19] Using ERC721A instead of ERC721 for more gas-efficient 

ERC721A is a proposed extension to the ERC721 standard that aims to improve gas efficiency and reduce the cost of deploying and using non-fungible tokens (NFTs) on the Ethereum blockchain. 

Example:   

```solidity
function batchTransfer(address[] memory recipients, uint256[] memory tokenIds) public {
    require(recipients.length == tokenIds.length, "Arrays must have the same length");
    for (uint i = 0; i < recipients.length; i++) {
        safeTransferFrom(msg.sender, recipients[i], tokenIds[i]);
    }
}
```
Reference : https://nextrope.com/erc721-vs-erc721a-2/


```solidity
file: /src/ulysses-omnichain/VirtualAccount.sol

37        ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L37


## [G-20] Use hardcode address instead of address(this)
 if the contract's address is needed in the code, it's more gas-efficient to hardcode the address as a constant rather than using the address(this) expression. This is because using address(this) requires additional gas consumption to compute the contract's address at runtime.

Here's an example :
```solidity
contract MyContract {
    address constant public CONTRACT_ADDRESS = 0x1234567890123456789012345678901234567890;
    
    function getContractAddress() public view returns (address) {
        return CONTRACT_ADDRESS;
    }
}
```
References: https://book.getfoundry.sh/reference/forge-std/compute-create-address

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

346            keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346


```solidity
file: /src/ulysses-amm/UlyssesToken.sol

84          asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));

112         uint256 assetBalance = assets[i].balanceOf(address(this));

118         assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L84

```solidity
file: /src/hermes/minters/BaseV2Minter.sol

138            uint256 _balanceOf = underlying.balanceOf(address(this));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L138

```solidity
file: /src/ulysses-omnichain/VirtualAccount.sol

37        ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L37

```solidity
file: /src/hermes/bHermes.sol

116        return address(asset).balanceOf(address(this));

129        gaugeWeight.mint(address(this), amount);

130        gaugeBoost.mint(address(this), amount);

131        governance.mint(address(this), amount);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L116

```solidity
file: /src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

104        IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, 0, 0);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L104


```solidity
file: /src/talos/boost-aggregator/BoostAggregator.sol

90        nonfungiblePositionManager.safeTransferFrom(address(this), address(uniswapV3Staker), tokenId);

168        address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L90

```solidity
file: /src/talos/TalosStrategyVanilla.sol

130        uint256 balance0 = token0.balanceOf(address(this)) - protocolFees0;

131        uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L130

```solidity
file: /src/ulysses-omnichain/ArbitrumBranchPort.sol

52         _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);

120        _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L52

```solidity
file: /src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

158         uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L158

```solidity
file: /src/ulysses-amm/UlyssesRouter.sol

74        address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L74


```solidity
file: /src/talos/TalosStrategyStaked.sol

90        flywheel.accrue(ERC20(address(this)), msg.sender, _to);

95        flywheel.accrue(ERC20(address(this)), _from, _to);

177       try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L90

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

88             uint256 balanceBefore = rewardToken.balanceOf(address(this));

90             require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);

130            uint256 balanceBefore = rewardToken.balanceOf(address(this));

132            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L88

```solidity
file: /src/ulysses-omnichain/BranchPort.sol

138        uint256 currBalance = ERC20(_token).balanceOf(address(this));

180        IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

276        _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L138

```solidity
file: /src/ulysses-omnichain/RootPort.sol

311        _hToken.safeTransferFrom(_from, address(this), _amount);

350        newAccount = new VirtualAccount(_user, address(this));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L311

```solidity
file: /src/uni-v3-staker/UniswapV3Staker.sol

177        hermes.safeTransferFrom(msg.sender, address(this), reward);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L177

```solidity
file: /src/ulysses-omnichain/RootBridgeAgent.sol

688        IPort(localPortAddress).bridgeToRoot(address(this), gasTokenGlobalAddress, _amount, _amount, _fromChain);

766        IPort(localPortAddress).burn(address(this), gasToken, amountOut, _toChain);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L668


## [G-21]  Division by two should use bit shifting

<x> / 2 is the same as <x> >> 1. 

While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 gas due  to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by two.


```solidity
file: /src/ulysses-amm/UlyssesPool.sol

312        if (_fees.lambda1 + _fees.lambda2 != DIVISIONER / 2) revert InvalidFee();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L312

```solidity
file: /src/ulysses-omnichain/RootBridgeAgent.sol

677        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;

720        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L677


## [G-22] Use assembly for math (add, sub, mul, div)

Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.

Example: Subtraction
```solidity
//subtraction in Solidity
function subTest(uint256 a, uint256 b) public pure {
uint256 c = a - b;
}
```
Gas: 300

```solidity
//subtraction in assembly
function subAssemblyTest(uint256 a, uint256 b) public pure {
assembly {
let c := sub(a, b)
if gt(c, a) {
mstore(0x00, "underflow")
revert(0x00, 0x20)
     }
  }
}
```
Gas: 263

Instances:

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

526        uint256 c = a + b;

533        return a - b;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L526


```solidity
file: /src/ulysses-amm/UlyssesToken.sol

62        uint256 assetIndex = assetId[asset] - 1;

64        uint256 newAssetsLength = assets.length - 1;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L62


## [G-23] Use != 0 instead of > 0 for unsigned integer comparison

it's generally more gas-efficient to use != 0 instead of > 0 when
comparing unsigned integer types.
This is because the Solidity compiler can optimize the != 0 comparison to a simple bitwise operation,
 while the > 0 comparison requires an additional subtraction operation.
  As a result, using != 0 can be more gas-efficient and can help to reduce the overall cost of your contract.


Here's an example of how you can use != 0 instead of > 0:
```solidity
contract MyContract {
    uint256 public myUnsignedInteger;
    
    function myFunction() public view returns (bool) {
        // Use != 0 instead of > 0
        return myUnsignedInteger != 0;
    }
}
```
Instances:
```solidity
file: /src/ulysses-omnichain/RootBridgeAgent.sol
///@audit uint256(amount0 > 0 ? amount0 : amount1)  >> amount0 > 0

651        address(data.tokenIn).safeTransfer(msg.sender, uint256(amount0 > 0 ? amount0 : amount1));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L651

```solidity
file: /src/ulysses-omnichain/BranchBridgeAgent.sol

499          if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

1328         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499


## [G-24] Refactor event to avoid emitting empty data

```solidity
file: /src/governance/GovernorBravoDelegateMaia.sol

///@audit ""     ,  no data emitted 

327       emit VoteCast(msg.sender, proposalId, support, castVoteInternal(msg.sender, proposalId, support), "");

351       emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), "");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L327


## [G-25] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 
//Sort operations with different gas costs as follows
 f(x) || g(y) 
 f(x) && g(y)

```solidity
file: /src/gauges/factories/BaseV2GaugeManager.sol

122        if (!activeGaugeFactories[gaugeFactory] || gaugeFactories[gaugeFactoryIds[gaugeFactory]] != gaugeFactory) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L122

```solidity
file: /src/rewards/rewards/FlywheelGaugeRewards.sol

210        if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L210

```solidity
file: /src/ulysses-omnichain/CoreRootRouter.sol

188                || IPort(rootPortAddress).isLocalToken(_underlyingAddress, _fromChain)
189                || IPort(rootPortAddress).isUnderlyingToken(_underlyingAddress, _fromChain)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L188

```solidity
file: /src/ulysses-omnichain/RootPort.sol

495               || getLocalTokenFromUnder[_ecoTokenGlobalAddress][localChainId] != address(0)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L495

```solidity
file: /src/uni-v3-staker/UniswapV3Staker.sol

374        if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L374

```solidity
file: /src/ulysses-amm/UlyssesPool.sol

165        if (destinationIds[address(destination)] != 0 || destinationId == id) revert InvalidPool();

243        if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L165

```solidity
file: /src/ulysses-omnichain/RootBridgeAgent.sol

347            if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347

```solidity
file: /src/ulysses-omnichain/BranchBridgeAgent.sol

1243         if ((flag == 0x00) || (flag == 0x01) || (flag == 0x02)) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1243