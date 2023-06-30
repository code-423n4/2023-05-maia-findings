## [G-01] Array.length should not be calculate multiple of times.
To save gas usage, the length of the array should not be calculate multiple of times. 
The most gas-efficient effect is noticable in loops - those reported by automated bot: https://gist.github.com/itsmetechjay/09ae039958715d881a47209f74af1b7c#g-18-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop

However, code contains some adiditional places, where the length of arrays should not be calculated more than once:

```
File: governance/GovernorBravoDelegateMaia.sol

120:              targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,
123:          require(targets.length != 0, "GovernorBravo::propose: must provide actions");
124:          require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");
```

`target.length` can be calculated once, before line 120

```
File: ulysses-omnichain/BranchBridgeAgent.sol

332:          if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {
365:          } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {
373:                      uint8(getDeposit[_depositNonce].hTokens.length),
```

`uint8(getDeposit[_depositNonce].hTokens.length)` can be calculated once

```
108:          if (_data.length - PARAMS_GAS_OUT > 129) {
110:              (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);
[...]
142:              _data.length - PARAMS_GAS_OUT
150:                          _data.length - PARAMS_GAS_OUT
```

`_data.length - PARAMS_GAS_OUT` can be calculated once

```
File: ulysses-omnichain/RootBridgeAgent.sol

885:                  uint256(uint128(bytes16(data[data.length - PARAMS_GAS_IN:data.length - PARAMS_GAS_OUT]))), fromChainId
889:              _userFeeInfo.gasToBridgeOut = uint128(bytes16(data[data.length - PARAMS_GAS_OUT:data.length]));
895:              _userFeeInfo.depositedGas = uint128(bytes16(data[data.length - 32:data.length - 16]));
```

`data.length` can be calculated once

```
File: ulysses-omnichain/RootBridgeAgentExecutor.sol

135:          if (_data.length - PARAMS_GAS_IN > 112) {
138:                  _data[112], _data[113:_data.length - PARAMS_GAS_IN], dParams, _fromChainId
[...]
239:          if (_data.length - PARAMS_GAS_IN > 132) {
242:                  _data[132], _data[133:_data.length - PARAMS_GAS_IN], dParams, _account, _fromChainId
[...]
278:                 _data.length - PARAMS_GAS_IN
289:                              _data.length - PARAMS_GAS_IN
```

` _data.length - PARAMS_GAS_IN` can be calculated once

Additionaly, the bot missed the array's length calculation in VirtualAccont loop:

```
File: ulysses-omnichain/VirtualAccount.sol

47:        returnData = new bytes[](calls.length);
48:        for (uint256 i = 0; i < calls.length; i++) {
```


## [G-02] Variable assigments which are not used in calculation can be moved after revert.

```
File: ulysses-omnichain/VirtualAccount.sol

41      function call(Call[] calldata calls)
42          external
43          requiresApprovedCaller
44          returns (uint256 blockNumber, bytes[] memory returnData)
45      {
46          blockNumber = block.number;
47          returnData = new bytes[](calls.length);
48          for (uint256 i = 0; i < calls.length; i++) { 
49              (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);
50              if (!success) revert CallFailed();
51              returnData[i] = data;
```

Since `block.number` is only being returned in function `call`, and this function may revert in line `50`, it may save some gas by doing `blockNumber = block.number` assigment after potential revert (move line 46 after line 51). If function reverts, there won't be unnecessary gas cost for that instruction (`blockNumber = block.number`).


## [G-03] Use != 0 instead of > 0 for unsigned integer comparison


```
./erc-20/ERC20Gauges.sol:417:        if (weight > 0) {
./erc-20/ERC20Gauges.sol:441:        if (weight > 0) {
./erc-20/ERC20MultiVotes.sol:250:        if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {
./erc-4626/ERC4626MultiToken.sol:52:            require(_weights[i] > 0);
./maia/PartnerUtilityManager.sol:75:        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
./maia/PartnerUtilityManager.sol:85:        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
./maia/PartnerUtilityManager.sol:95:        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
./rewards/base/FlywheelCore.sol:127:        if (oldRewardBalance > 0) {
./rewards/base/FlywheelCore.sol:162:        if (strategyRewardsAccrued > 0) {
./rewards/rewards/FlywheelGaugeRewards.sol:234:        if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);
./talos/base/TalosBaseStrategy.sol:409:        if (amount0 > 0) _token0.transfer(msg.sender, amount0);
./talos/base/TalosBaseStrategy.sol:410:        if (amount1 > 0) _token1.transfer(msg.sender, amount1);
./talos/TalosStrategyVanilla.sol:139:        if (_liquidity > 0) {
./ulysses-amm/factories/UlyssesFactory.sol:111:                if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);
./ulysses-amm/UlyssesPool.sol:153:        if (claimed > 0) {
./ulysses-amm/UlyssesPool.sol:191:            if (oldBandwidth > 0) {
./ulysses-amm/UlyssesPool.sol:256:                    if (oldBandwidth > 0) {
./ulysses-amm/UlyssesPool.sol:272:            if (oldBandwidth > 0) {
./ulysses-amm/UlyssesPool.sol:282:                    } else if (leftOverBandwidth > 0) {
./ulysses-amm/UlyssesPool.sol:911:            if (updateAmount > 0) {
./ulysses-amm/UlyssesPool.sol:1048:            if (updateAmount > 0) {
./ulysses-amm/UlyssesToken.sol:47:        require(_weight > 0);
./ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:160:        if (gasRemaining > 0) {
./ulysses-omnichain/ArbitrumBranchPort.sol:118:        if (_deposit > 0) {
./ulysses-omnichain/ArbitrumBranchPort.sol:123:        if (_amount - _deposit > 0) {
./ulysses-omnichain/ArbitrumBranchPort.sol:137:            if (_deposits[i] > 0) {
./ulysses-omnichain/ArbitrumBranchPort.sol:144:            if (_amounts[i] - _deposits[i] > 0) {
./ulysses-omnichain/BranchBridgeAgent.sol:471:            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));
./ulysses-omnichain/BranchBridgeAgent.sol:474:        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
./ulysses-omnichain/BranchBridgeAgent.sol:496:            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));
./ulysses-omnichain/BranchBridgeAgent.sol:499:        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
./ulysses-omnichain/BranchBridgeAgent.sol:518:            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));
./ulysses-omnichain/BranchBridgeAgent.sol:521:        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
./ulysses-omnichain/BranchBridgeAgent.sol:540:            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));
./ulysses-omnichain/BranchBridgeAgent.sol:543:        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
./ulysses-omnichain/BranchBridgeAgent.sol:623:            if (_amounts[i] - _deposits[i] > 0) {
./ulysses-omnichain/BranchBridgeAgent.sol:627:            if (_deposits[i] > 0) {
./ulysses-omnichain/BranchBridgeAgent.sol:979:        if (_amount - _deposit > 0) {
./ulysses-omnichain/BranchBridgeAgent.sol:983:        if (_deposit > 0) {
./ulysses-omnichain/BranchBridgeAgent.sol:1328:        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
./ulysses-omnichain/BranchPort.sol:248:        if (_amount - _deposit > 0) {
./ulysses-omnichain/BranchPort.sol:252:        if (_deposit > 0) {
./ulysses-omnichain/BranchPort.sol:268:            if (_deposits[i] > 0) {
./ulysses-omnichain/BranchPort.sol:275:            if (_amounts[i] - _deposits[i] > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:57:                || (_dParams.amount > 0 && !IPort(_localPortAddress).isLocalToken(_dParams.hToken, _fromChain)) 
./ulysses-omnichain/RootBridgeAgent.sol:58:                || (_dParams.deposit > 0 && !IPort(_localPortAddress).isUnderlyingToken(_dParams.token, _fromChain)) 
./ulysses-omnichain/RootBridgeAgent.sol:301:        if (localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0)) {
./ulysses-omnichain/RootBridgeAgent.sol:347:            if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();
./ulysses-omnichain/RootBridgeAgent.sol:451:        if (_amount - _deposit > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:457:        if (_deposit > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:750:            if (_initialGas > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:757:        if (_initialGas > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:1161:            if (initialGas > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:1171:        if (initialGas > 0) {
./ulysses-omnichain/RootBridgeAgent.sol:1240:        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
./ulysses-omnichain/RootPort.sol:282:        if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);
./ulysses-omnichain/RootPort.sol:283:        if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);
./uni-v3-staker/libraries/RewardMath.sol:31:        if (boostTotalSupply > 0) {
./uni-v3-staker/UniswapV3Staker.sol:199:        if (incentive.numberOfStakes > 0) revert EndIncentiveWhileStakesArePresent();
./uni-v3-staker/UniswapV3Staker.sol:271:        if (reward > 0) hermes.safeTransfer(to, reward);
./uni-v3-staker/UniswapV3Staker.sol:281:        if (reward > 0) hermes.safeTransfer(to, reward);
```


## [G-04] Use == 0 instead of <= 0 for unsigned integer comparison

```
File: uni-v3-staker/UniswapV3Staker.sol

137: function createIncentiveFromGauge(uint256 reward) external {
138:        if (reward <= 0) revert IncentiveRewardMustBePositive();
[...]
157: function createIncentive(IncentiveKey memory key, uint256 reward) external {
158:        if (reward <= 0) revert IncentiveRewardMustBePositive();
``` 

Since `reward` is `uint256`, its value will never be lower than 0. This means, that below code miight be rewritten to: `if (reward == 0)`


## [G-05] Checking conditions in require should be the first possible instructions in function

```
File: ulysses-amm/UlyssesToken.sol

21      constructor(
22          uint256 _id,
23          address[] memory _assets,
24          uint256[] memory _weights,
25          string memory _name,
26          string memory _symbol,
27          address _owner
28      ) ERC4626MultiToken(_assets, _weights, _name, _symbol) {
29          _initializeOwner(_owner);
30          require(_id != 0);
31          id = _id;
``` 

Move `require(_id != 0);` before ` _initializeOwner(_owner);`. Otherwise, when constructor will be called with `id = 0`, the caller of `constructor` will waste gas on `_initializeOwner` before `constructor` will revert. If incorrect `_id` is being passed to the constructor, it should revert immediately - not after callin `_initializeOwner`.


## [G-06] ++var/--var saves more gas than var++/var--

```
./talos/libraries/PoolVariables.sol:98:        if (tick < 0 && tick % tickSpacing != 0) compressed--;
./uni-v3-staker/UniswapV3Staker.sol:428:        incentive.numberOfStakes--;

./erc-20/ERC20Boost.sol:55:                i++;
./erc-20/ERC20Boost.sol:101:                i++;
./erc-20/ERC20Boost.sol:166:                i++;
./erc-20/ERC20Boost.sol:224:                i++;
./erc-20/ERC20Boost.sol:245:                i++;
./erc-20/ERC20Gauges.sol:117:                i++;
./erc-20/ERC20Gauges.sol:158:                i++;
./erc-20/ERC20Gauges.sol:266:                i++;
./erc-20/ERC20Gauges.sol:346:                i++;
./erc-20/ERC20Gauges.sol:548:                    i++;
./erc-4626/ERC4626MultiToken.sol:60:                i++;
./erc-4626/ERC4626MultiToken.sol:72:                i++;
./erc-4626/ERC4626MultiToken.sol:83:                i++;
./erc-4626/ERC4626MultiToken.sol:174:                i++;
./erc-4626/ERC4626MultiToken.sol:205:                i++;
./erc-4626/ERC4626MultiToken.sol:219:                i++;
./erc-4626/ERC4626MultiToken.sol:238:                i++;
./erc-4626/ERC4626MultiToken.sol:254:                i++;
./gauges/BaseV2Gauge.sol:118:                i++;
./gauges/factories/BaseV2GaugeFactory.sol:81:                i++;
./gauges/factories/BaseV2GaugeFactory.sol:97:                i++;
./gauges/factories/BaseV2GaugeManager.sol:68:                i++;
./gauges/factories/BaseV2GaugeManager.sol:84:                i++;
./governance/GovernorBravoDelegateMaia.sol:142:        proposalCount++;
./ulysses-omnichain/BranchPort.sol:109:        bridgeAgentFactoriesLenght++;
./ulysses-omnichain/BranchPort.sol:280:                i++;
./ulysses-omnichain/BranchPort.sol:293:        bridgeAgentsLenght++;
./ulysses-omnichain/BranchPort.sol:311:        bridgeAgentFactoriesLenght++;
./ulysses-omnichain/BranchPort.sol:334:        strategyTokensLenght++;
./ulysses-omnichain/BranchPort.sol:355:        portStrategiesLenght++;
./ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:45:        hTokensLenght++;
./ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:67:        hTokensLenght++;
./ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:67:        hTokensLenght++;
./ulysses-omnichain/RootPort.sol:135:        bridgeAgentFactoriesLenght++;
./ulysses-omnichain/RootPort.sol:370:        bridgeAgentsLenght++;
./uni-v3-staker/UniswapV3Staker.sol:502:        incentives[incentiveId].numberOfStakes++;
```

## [G-07] Unused variable in ERC20hTokenBranchFactory and ERC20hTokenRootFactory

ERC20hTokenBranchFactory and ERC20hTokenRootFactory define public variable `hTokensLenght`, which is not being used across the code. 
This variable is unnecessary, since it defines the length of `ERC20hTokenRoot[] public hTokens;` (adding new token to `hTokens` increments the value of `hTokensLenght`).
Whenever it would be needed, it'll be more feasible to access `hTokens.length`. Current implementation does not use this variable at all (other than just increasing its value, however, the current length is not used anywhere across the source code).

```
./ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:25:    uint256 public hTokensLenght;
./ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:45:        hTokensLenght++; 
./ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:67:        hTokensLenght++;
./ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:26:    uint256 public hTokensLenght;
./ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:67:        hTokensLenght++;   
```

## [G-08] Incorrect ordering of calculating array's length

```
File: ulysses-amm/UlyssesToken.sol

49:        assetId[asset] = assets.length + 1;
50:        assets.push(asset);
```

The length of `assets` is being calculated, then it's being increased by one and then new element is being pushed into `assets`. Changing the orders of these operation will save some gas (adding one will not be needed, since after `push`, the `assets`'s length will be already increased by one):

```
assets.push(asset);
 assetId[asset] = assets.length;
```


## [G-09] Refactor getRebalance and getRerange functions in TalosManager.sol

Functions `getRerange` and `getRebalance` are called twice in `checkUpkeep` and `performUpkeep`:

```
File: talos/TalosManager.sol

91:      function checkUpkeep(bytes calldata) external view override returns (bool upkeepNeeded, bytes memory performData) {
[...]
101:          if (getRebalance(strategy)) {
102:              upkeepNeeded = true;
103:          } else if (getRerange(strategy)) {
104:              upkeepNeeded = true;
[...]
112:      function performUpkeep(bytes calldata) external override {
113:          if (getRebalance(strategy)) {
[..]
119:              strategy.rebalance();
120:          } else if (getRerange(strategy)) {
```

Since these functions are almost identical:

```
File: talos/TalosManager.sol

66      function getRebalance(ITalosBaseStrategy position) private view returns (bool) {
67          //Calculate base ticks.
68          (, int24 currentTick,,,,,) = position.pool().slot0();
69
70          return currentTick - position.tickLower() >= ticksFromLowerRebalance
71              || position.tickUpper() - currentTick >= ticksFromUpperRebalance;
72      }
[...]
78      function getRerange(ITalosBaseStrategy position) private view returns (bool) {
79          //Calculate base ticks.
80          (, int24 currentTick,,,,,) = position.pool().slot0();
81
82          return currentTick - position.tickLower() >= ticksFromLowerRerange
83              || position.tickUpper() - currentTick >= ticksFromUpperRerange;
84      }
```

there can be refactored into one function, to lower the number of calculations:

```
function getRebalanceAndRerange(ITalosBaseStrategy position) private view returns (bool rebalance, bool rerange) {

 (, int24 currentTick,,,,,) = position.pool().slot0();
 int24 tickLower = currentTick - position.tickLower();
 int24 tickUpper = position.tickUpper() - currentTick;

 rebalance = (tickLower >= ticksFromLowerRebalance || tickUpper >= ticksFromUpperRebalance);
 rerange = (tickLower >= ticksFromLowerRerange || tickUpper >= ticksFromUpperRerange);
}
```


## [G-10] Redundant comparison in BaseV2Minter

```
File: hermes/minters/BaseV2Minter.sol

126: if (block.timestamp >= _period + week && initializer == address(0))
```

Since function `initialize` already sets `initializer` to `address(0)`:

```
File: hermes/minters/BaseV2Minter.sol

78:    function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {
79:        if (initializer != msg.sender) revert NotInitializer();
80:        flywheelGaugeRewards = _flywheelGaugeRewards;
81:        initializer = address(0);
    }
```

the check: `initializer == address(0)` will be always true.
Please notice, that this issue assumes,  that function `initialize` will be called by the owner before `updatePeriod`. Otherwise, `initializer` is being set to `msg.sender` in `constructor`. However, if `initialize` won't be executed first, the first comparison `block.timestamp >= _period + week` will always be `false`.
Thus, in both cases `initialized == address(0)` check is redundant.


## [G-11] Substraction results can be cached

`_amount - deposit` is being calculated in multiple of places, instead of being calculated once and its results assigned to variable.

```
File: ulysses-omnichain/ArbitrumBranchPort.sol

123:   if (_amount - _deposit > 0) {
124:            IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(_depositor, _localAddress, _amount - _deposit);
```

```
File: ulysses-omnichain/BranchBridgeAgent.sol

979: if (_amount - _deposit > 0) {
980:            IPort(localPortAddress).bridgeIn(_recipient, _hToken, _amount - _deposit);
```

```
File: ulysses-omnichain/BranchPort.sol

248:  if (_amount - _deposit > 0) {
249:            _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);
250:            ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);
```

```
File: ulysses-omnichain/RootBridgeAgent.sol

451: if (_amount - _deposit > 0) {
[...]
454:            _globalAddress.safeTransferFrom(_sender, localPortAddress, _amount - _deposit);
```

```
File: ulysses-omnichain/RootPort.sol

282: if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);
```

## [G-11] Redundant else if construction

```
File: governance/GovernorBravoDelegateMaia.sol

361: function castVoteInternal(address voter, uint256 proposalId, uint8 support) internal returns (uint96) {
362:        require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");
363:        require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");
[...]
369:        if (support == 0) {
370:            proposal.againstVotes = add256(proposal.againstVotes, votes);
371:        } else if (support == 1) {
372:            proposal.forVotes = add256(proposal.forVotes, votes);
373:        } else if (support == 2) {
374:            proposal.abstainVotes = add256(proposal.abstainVotes, votes);
375:        }
```

Line 363 checks if `support <= 2`. Since `support` is `uint8`, its value can only be: 0, 1, 2 (otherwise, function will revert).
This means, that the last `else if (support == 2)` is redundant. Because if support was not 0 (the first comparison), and not 1 (the 2nd comparison) - it has to be 2 (otherwise, function would revert on line 363).

Line `373:        } else if (support == 2) {` should be changed to `373:        } else  {`.


