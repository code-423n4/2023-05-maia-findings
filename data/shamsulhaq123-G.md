No | Issue |Instances|
|-|:-|:-:|:-:|
|G-1|If statements that use && can be refactored into nested if statements|6
|G-3|>= costs less gas than >|33
|G-4|Can Make The Variable Outside The Loop To Save Gas |4
|G-5|A modifier used only once and not being inherited should be inlined to save gas|7
|G-6|Structs can be packed into fewer storage slots|7
|G-7|Using > 0 costs more gas than != 0 when used on a uint in a require() statement|2
|G-8|se hardcode address instead address(this)|56
|G-9|String literals passed to abi.encode()/abi.encodePacked() should not be split by commas|3
|G-10|Use constants instead of type(uintx).max|37
|G-11|internal functions not called by the contract should be removed to save deployment gas|1
|G-12|public functions not called by the contract should be declared external instead|32
|G-13|Do not calculate constants|2
|G-14| Using delete statement can save gas|8
|G-15|Variable end can be of type uint128|2
|G-16|State variables with values known at compile time should be constants|1
|G-17|using private rather then public for constants,save gas|4

## [G-01] If statements that use && can be refactored into nested if statements

```solidity

file: src/hermes/minters/BaseV2Minter.sol

126  if (block.timestamp >= _period + week && initializer == address(0)) 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L126

```solidity

file: src/ulysses-amm/factories/UlyssesFactory.sol

111    if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111

```solidity

file : src/rewards/rewards/FlywheelGaugeRewards.sol

210     if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {
            return 0;
        }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L210

```solidity

file: src/uni-v3-staker/UniswapV3Staker.sol

264    if (amountRequested != 0 && amountRequested < reward)
374    if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();
402    if (hermesGaugeBoost.isUserGauge(owner, address(gauge)) && _userAttachements[owner][key.pool] == tokenId) 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L264
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L374
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L402

## [G‑2] >= costs less gas than >
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

```solidity

file: src/ulysses-amm/UlyssesToken.sol

45   if (assetId[asset] != 0) revert AssetAlreadyAdded();
        require(ERC20(asset).decimals() == 18);
47        require(_weight > 0);

115   if (assetBalance > newAssetBalance)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L45-L47
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L115

```solidity
file: ulysses-omnichain/BranchBridgeAgentExecutor.sol

108 if (_data.length - PARAMS_GAS_OUT > 129)

141  if (
            _data.length - PARAMS_GAS_OUT
                > PARAMS_START_SIGNED + PARAMS_TKN_START
144                    + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L108
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L141-L144

```solidity
file: src/gauges/factories/BaseV2GaugeManager.sol

78   if (end > length) end = length;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L78

```solidity

file: src/hermes/minters/BaseV2Minter.sol

93   if (_daoShare > max_dao_share) revert DaoShareTooHigh();

99   if (_tail_emission > max_tail_emission) revert TailEmissionTooHigh();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L93
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L99

```solidity
file: src/talos/boost-aggregator/BoostAggregator.sol

118   if (pendingRewards > DIVISIONER)

154   if (_protocolFee > DIVISIONER) revert FeeTooHigh();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L118
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L154

```solidity

file: src/talos/TalosStrategyVanilla.sol

139   if (_liquidity > 0)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139

```solidity

file: ulysses-amm/factories/UlyssesFactory.sol

111   if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111

```solidity

file: src/ulysses-omnichain/ArbitrumBranchPort.sol

118  if (_deposit > 0) 

123   if (_amount - _deposit > 0) 

137  if (_deposits[i] > 0)

144   if (_amounts[i] - _deposits[i] > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L123
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L137
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L144

```solidity
file: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

160    if (gasRemaining > 0) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160

```solidity

file: src/rewards/rewards/FlywheelGaugeRewards.sol

120  if (currentCycle > nextCycle) 

207     bool incompleteCycle = queuedRewards.storedCycle > cycle;

234    if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L120
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L207
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234

```solidity

file: src/ulysses-omnichain/BranchPort.sol

129    return currBalance > minReserves ? currBalance - minReserves : 0;

159     if (_amount > _excessReserves(_token)) revert InsufficientReserves();

247     ) external virtual requiresBridgeAgent {
        if (_amount - _deposit > 0) {
            _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);
            ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);
        }
        if (_deposit > 0) {
            _underlyingAddress.safeTransferFrom(
                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
            );
        }
257    }

268   if (_deposits[i] > 0) 

275  if (_amounts[i] - _deposits[i] > 0) 


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L129
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L159
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L247-L257
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L268
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L275

```solidity

file: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

135      if (_data.length - PARAMS_GAS_IN > 112)

172        if (
            length - PARAMS_GAS_IN
174               > PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE

239      if (_data.length - PARAMS_GAS_IN > 132) 

277    if (
                _data.length - PARAMS_GAS_IN
                    > PARAMS_END_SIGNED_OFFSET
                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
280            )

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L135
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L172-L174
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L239
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L277-L280

```solidity

file: src/ulysses-omnichain/RootPort.sol

282 
        if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);
283        if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282-L283

```solidity

file: src/uni-v3-staker/UniswapV3Staker.sol

165  if (startTime - block.timestamp > maxIncentiveStartLeadTime) 

199  if (incentive.numberOfStakes > 0) revert EndIncentiveWhileStakesArePresent();

271       if (reward > 0) hermes.safeTransfer(to, reward);
281    if (reward > 0) hermes.safeTransfer(to, reward);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L165
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L199
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L271
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L281
 
 ## [G-03] Can Make The Variable Outside The Loop To Save Gas 
When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

```
contract MyContract {
    function sum(uint256[] memory values) public pure returns (uint256) {
        uint256 total = 0;
        
        for (uint256 i = 0; i < values.length; i++) {
            total += values[i];
        }
        
        return total;
    }
}
```
```solidity
File: /src/LlamaCore.sol
637    bytes32 salt = bytes32(keccak256(strategyConfigs[i]));

657    bytes32 salt = bytes32(keccak256(accountConfigs[i]));
```
https://github.com/code-423n4/2023-06-llama/blob/main/src/LlamaCore.sol#L637


```solidity
File: /src/llama-scripts/LlamaGovernanceScript.sol
72    bool addressIsCore = targets[i] == address(core);

73    bool addressIsPolicy = targets[i] == address(policy);
```
https://github.com/code-423n4/2023-06-llama/blob/main/src/llama-scripts/LlamaGovernanceScript.sol#L72

```solidity
178   uint8 role = strategyConfig.forceApprovalRoles[i];

186   uint8 role = strategyConfig.forceDisapprovalRoles[i];
```
https://github.com/code-423n4/2023-06-llama/blob/main/src/strategies/LlamaAbsoluteStrategyBase.sol#L178

```solidity
178    uint8 role = strategyConfig.forceApprovalRoles[i];

186    uint8 role = strategyConfig.forceDisapprovalRoles[i];
```
https://github.com/code-423n4/2023-06-llama/blob/main/src/strategies/LlamaRelativeQuorum.sol#L178


## [G-04] A modifier used only once and not being inherited should be inlined to save gas


When a modifier is used only once and is not inherited by any other contracts, inlining it can reduce gas costs. Inlining means that the modifier's code is directly inserted into the function where it is applied, rather than creating a separate function call for the modifier.
By inlining the modifier, you avoid the overhead of the additional function call, which results in lower gas consumption. This optimization is especially useful when the modifier's code is short or simple.

```solidity
file:    src/talos/boost-aggregator/BoostAggregator.sol

190      modifier onlyWhitelisted(address from) {
        if (!whitelistedAddresses[from]) revert Unauthorized();
        _;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L190-L193


```solidity
file:    src/ulysses-omnichain/BranchPort.sol

423      modifier lock() {
        require(_unlocked == 1);
        _unlocked = 2;
        _;
        _unlocked = 1;
    }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L423-L429

```solidity
file:      src/gauges/factories/BribesFactory.sol 

105         modifier onlyGaugeFactory() {
        if (!gaugeManager.activeGaugeFactories(BaseV2GaugeFactory(msg.sender))) {
            revert Unauthorized();
        }
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L105-L110

```solidity
file:   src/hermes/tokens/bHermesBoost.sol

32      modifier onlybHermes() {
        if (msg.sender != bHermes) revert NotbHermes();
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32-L35

```solidity
file:    src/hermes/tokens/bHermesGauges.sol

39       modifier onlybHermes() {
        if (msg.sender != bHermes) revert NotbHermes();
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39-L42

```solidity
file: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

75     modifier requiresCoreRouter() {
        if (msg.sender != localCoreRouterAddress) revert UnrecognizedCoreRouter();
        _;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L75-L78

```solidity
file:   src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

74      modifier requiresCoreRouter() {
        if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress) {
            revert UnrecognizedCoreRouter();
        }
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L74-L79


## [G-05] Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings



```solidity
file:   src/governance/GovernorBravoInterfaces.sol


105    struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
        uint256 eta;
        /// @notice the ordered list of target addresses for calls to be made
        address[] targets;
        /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
        uint256[] values;
        /// @notice The ordered list of function signatures to be called
        string[] signatures;
        /// @notice The ordered list of calldata to be passed to each call
        bytes[] calldatas;
        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
        uint256 startBlock;
        /// @notice The block at which voting ends: votes must be cast prior to this block
        uint256 endBlock;
        /// @notice Current number of votes in favor of this proposal
        uint256 forVotes;
        /// @notice Current number of votes in opposition to this proposal
        uint256 againstVotes;
        /// @notice Current number of votes for abstaining for this proposal
        uint256 abstainVotes;
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#105-L136


```solidity
file:

26    struct DepositInput {
    //Deposit Info
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
44   struct DepositParams {
    //Deposit Info
    uint32 depositNonce; //Deposit nonce.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
    uint128 depositedGas; //BRanch chain gas token amount sent with request.
}

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L44-L53


```solidity
file:

31    struct Settlement {
    uint24 toChain; //Destination chain for interaction.
    uint128 gasToBridgeOut; //Gas owed to user
    address owner; //Owner of the settlement
    address recipient; //Recipient of the settlement.
    SettlementStatus status; //Status of the settlement
    address[] hTokens; //Input Local hTokens Addresses.
    address[] tokens; //Input Native / underlying Token Addresses.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    bytes callData; //Call data for settlement
}
63    struct DepositParams {
    //Deposit Info
    uint32 depositNonce; //Deposit nonce.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
73  struct DepositMultipleParams {
    //Deposit Info
    uint8 numberOfAssets; //Number of assets to deposit.
    uint32 depositNonce; //Deposit nonce.
    address[] hTokens; //Input Local hTokens Address.
    address[] tokens; //Input Native / underlying Token Address.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L31-L42
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L73-L82


## [G-06]   Using > 0 costs more gas than != 0 when used on a uint in a require() statement
This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

```solidity

file:  src/erc-4626/ERC4626MultiToken.sol

52    require(_weights[i] > 0);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52


```solidity

file:    src/ulysses-amm/UlyssesToken.sol

47       require(_weight > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47



## [G-07] Use hardcode address instead address(this)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.

```solidity
file:  src/erc-4626/ERC4626.sol

37     address(asset).safeTransferFrom(msg.sender, address(this), assets);

51    address(asset).safeTransferFrom(msg.sender, address(this), assets);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L37

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L51

```solidity
file:   src/erc-4626/ERC4626MultiToken.sol
 
69     assets[i].safeTransferFrom(msg.sender, address(this), assetsAmounts[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L69

```solidity
file:  src/erc-4626/UlyssesERC4626.sol

36     asset.safeTransferFrom(msg.sender, address(this), assets);

52     asset.safeTransferFrom(msg.sender, address(this), assets);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L36

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L52

```solidity
file:   src/gauges/BaseV2Gauge.sol

70      multiRewardsDepot = new MultiRewardsDepot(address(this));

115     if (isActive[_bribeFlywheels[i]]) _bribeFlywheels[i].accrue(ERC20(address(this)), user);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L70

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L115

```solidity
file:   src/hermes/minters/BaseV2Minter.sol

138     uint256 _balanceOf = underlying.balanceOf(address(this));

140     HERMES(underlying).mint(address(this), _required - _balanceOf);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L138

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140 

```solidity
file:   src/hermes/bHermes.sol

116     return address(asset).balanceOf(address(this));

129     gaugeWeight.mint(address(this), amount);
        gaugeBoost.mint(address(this), amount);
        governance.mint(address(this), amount);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L116

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L129-L131

```solidity
file:    src/hermes/UtilityManager.sol

72       address(gaugeWeight).safeTransferFrom(msg.sender, address(this), amount);

81       address(gaugeBoost).safeTransferFrom(msg.sender, address(this), amount);

90       address(governance).safeTransferFrom(msg.sender, address(this), amount);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L72

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L81

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L90 

```solidity
file:   src/maia/tokens/ERC4626PartnerManager.sol

162      return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

168      return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

219      if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) 

226      address(this), totalSupply * newRate - address(partnerGovernance).balanceOf(address(this))

244      ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L162

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L168

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L219

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L226

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L244

```solidity
file:  src/maia/PartnerUtilityManager.sol

75     if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0)

85     if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) 

95     if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) 

104    address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);

128    uint256 weightAvailable = address(gaugeWeight).balanceOf(address(this));

139    uint256 boostAvailable = address(gaugeBoost).balanceOf(address(this));

148    uint256 governanceAvailable = address(governance).balanceOf(address(this));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L104

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L128

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L139

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L148


```solidity
file:   src/talos/base/TalosBaseStrategy.sol

125     address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);
        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);

146     recipient: address(this),

196     address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);
        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);

366     recipient: address(this),

406     uint256 balance0 = _token0.balanceOf(address(this));
        uint256 balance1 = _token1.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L125-L126

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L146

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L196-L197

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L366

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L406-L407

```solidity
file:   src/talos/TalosStrategyStaked.sol

90      flywheel.accrue(ERC20(address(this)), msg.sender, _to);

95      flywheel.accrue(ERC20(address(this)), _from, _to);

150     recipient: address(this),

177     try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L90

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L95

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#150

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L177

```solidity
file:   src/ulysses-amm/UlyssesPool.sol

103     return asset.balanceOf(address(this)) - getProtocolFees();

108     return balanceOf[owner].min(asset.balanceOf(address(this)));

127     uint256 balance = asset.balanceOf(address(this));

218     asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

303     asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

1109    asset.safeTransferFrom(msg.sender, address(this), assets);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L103

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L108

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L127

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L218

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L303

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1109

```solidity
file:    src/ulysses-amm/UlyssesToken.sol

84        asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));

112       uint256 assetBalance = assets[i].balanceOf(address(this));

118       assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L84

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L118

```solidity
file:     src/ulysses-omnichain/BranchBridgeAgent.sol

1020      uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));

1078     IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);

1093      IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));

1103      IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);

1325     uint256 executionBudget = anycallConfig.executionBudget(address(this));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1020

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1078

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1093

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1103

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1325

```solidity
file:    src/ulysses-omnichain/BranchPort.sol

127     uint256 currBalance = ERC20(_token).balanceOf(address(this));

138     uint256 currBalance = ERC20(_token).balanceOf(address(this));

180     IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

249     _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);

254     _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

271      address(this),

276      _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L138

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L180

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L249

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L254

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L271

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L276


## [G-8] String literals passed to abi.encode()/abi.encodePacked() should not be split by commas  

String literals can be split into multiple parts and still be considered as a single string literal. Adding commas between each chunk makes it no longer a single string, and instead multiple strings. EACH new comma costs 21 gas due to stack operations and separate MSTOREs.

```solidity
file:     src/governance/GovernorBravoDelegateMaia.sol

346       keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346


```solidity
file:   src/talos/libraries/PoolActions.sol

51      abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L51

```solidity
file:    src/ulysses-omnichain/RootBridgeAgent.sol

689      abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689


## [G-9] Use constants instead of type(uintx).max

When working with maximum values in Solidity, it is more gas-efficient to use constants instead of using the expression type(uintX).max, where X represents the desired bit size. Using constants allows the compiler to optimize the code and save gas during execution.

Here's an example to illustrate this:

```solidity
contract ExampleContract {
    uint256 constant MAX_UINT256 = 2**256 - 1;

    function doSomething() external {
        uint256 maxValue = type(uint256).max;

        // Perform some operations

        if (value > maxValue) {
            // Do something
        }
    }
}

```
In this example, we have defined a constant MAX_UINT256 with the maximum value of a uint256 variable, which is equivalent to 2^256 - 1. Instead of using type(uint256).max directly, we use the constant MAX_UINT256 throughout the contract.


```solidity
file:   src/erc-4626/ERC4626.sol

67      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

84      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

149     return type(uint256).max;

154     return type(uint256).max;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L67

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L84

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L149

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L154

```solidity
file:  src/erc-4626/ERC4626DepositOnly.sol

99   return type(uint256).max;

104  return type(uint256).max;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L99

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L104

```solidity
file:    src/erc-4626/ERC4626MultiToken.sol

143      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

165      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

200      shares = type(uint256).max;

270      return type(uint256).max;

275      return type(uint256).max;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L143

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L165

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L200

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L270

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L275



```solidity
file:    src/erc-4626/UlyssesERC4626.sol
 
68       if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

113      return type(uint256).max;

117      return type(uint256).max;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L68

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L113

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L117 


```solidity
file:   src/gauges/UniswapV3Gauge.sol

45      rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L45 

```solidity
file:   src/maia/tokens/ERC4626PartnerManager.sol

200     address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
        address(governance).safeApprove(newPartnerVault, type(uint256).max);
        address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L200-L203

```solidity
file:  src/rewards/base/BaseFlywheelRewards.sol

36     _rewardToken.safeApprove(address(_flywheel), type(uint256).max);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L36

```solidity
file:   src/rewards/rewards/FlywheelGaugeRewards.sol
 
133     require(newRewards <= type(uint112).max); 

187     require(nextRewards <= type(uint112).max); 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L133

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L187 

```solidity
file:      src/talos/base/TalosBaseStrategy.sol

130        address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

131        address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

249        if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares

285        amount0Max: type(uint128).max,

286        amount1Max: type(uint128).max

367        amount0Max: type(uint128).max,

368        amount1Max: type(uint128).max

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L130

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L131

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L249

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L285

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L286

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L367

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L368

```solidity
file:    src/talos/TalosStrategyStaked.sol

151      amount0Max: type(uint128).max,

152      amount1Max: type(uint128).max

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L151

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L152


```solidity
file:       src/talos/TalosStrategyVanilla.sol

111          amount0Max: type(uint128).max,

112          amount1Max: type(uint128).max

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L111

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L112

```solidity
file:   src/ulysses-amm/UlyssesRouter.sol

40      address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40 

```solidity

file:  src/uni-v3-staker/UniswapV3Staker.sol

70     if (liquidity == type(uint96).max)

385    amount0Max: type(uint128).max,

386    amount1Max: type(uint128).max

456    if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

506    if (liquidity >= type(uint96).max)

509    liquidityNoOverflow: type(uint96).max,

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L70

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L385

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L386

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L456

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L506

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L509


## [G-10] internal functions not called by the contract should be removed to save deployment gas

When deploying a smart contract, it is essential to optimize gas usage to minimize transaction costs and improve overall efficiency. One aspect to consider is removing unused internal functions that are not called by the contract. By eliminating these unused functions, you can significantly reduce the deployment gas cost.

Internal functions in Solidity are only intended to be invoked within the contract or by other internal functions. If an internal function is not called anywhere within the contract, it serves no purpose and contributes unnecessary overhead during deployment. Removing such functions can lead to substantial gas savings.

```solidity

file:   src/rewards/depots/RewardsDepot.sol

19     function transferRewards(address _asset, address _rewardsContract) internal returns (uint256 balance)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L19


## [G-11] Amounts should be checked for 0 before calling a transfer

 that it is a good practice to check if the transfer amount is zero before making a transfer of tokens. This optimization can help save gas by avoiding unnecessary token transfer operations when the transfer amount is zero.

 Here's an example to illustrate this optimization:

 ```solidity

 function transferTokens(address recipient, uint256 amount) public {
    require(amount > 0, "Amount must be greater than zero");

    // Check if the contract has sufficient balance before transferring
    require(balanceOf(msg.sender) >= amount, "Insufficient balance");

    // Perform the token transfer
    _transfer(msg.sender, recipient, amount);
}
 
 ```
In this example, before making the token transfer, we first check if the amount is greater than zero. If the amount is zero or less, the transfer operation is skipped, and an error is thrown. By performing this check, we avoid unnecessary token transfers and the associated gas costs when the transfer amount is zero.

Additionally, it's important to include a check for sufficient balance before making the transfer to ensure that the sender has enough tokens to fulfill the transfer.



```solidity
file:  src/erc-20/ERC20Gauges.sol

510    return super.transferFrom(from, to, amount);


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L510



```solidity
file:   src/erc-20/ERC20MultiVotes.sol

293     return super.transfer(to, amount);

305     return super.transferFrom(from, to, amount);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L293

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L305

```solidity
file:  src/talos/TalosStrategyStaked.sol

91     return super.transfer(_to, _amount);

96     return super.transferFrom(_from, _to, _amount);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L91

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L96


## [G-12] public functions not called by the contract should be declared external instead


```solidity

file:  src/erc-20/ERC20MultiVotes.sol

32     function checkpoints(address account, uint32 pos) public view virtual returns (Checkpoint memory)

37     function numCheckpoints(address account) public view virtual returns (uint32) {

42     function freeVotes(address account) public view virtual returns (uint256)

58     function getPriorVotes(address account, uint256 blockNumber) public view virtual returns (uint256)

126    function delegatesVotesCount(address delegator, address delegatee) public view virtual returns (uint256) 

141   function incrementDelegation(address delegatee, uint256 amount) public virtual

146   function undelegate(address delegatee, uint256 amount) public virtual
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L32

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L37

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L42

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L58

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L126

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L141

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L146


```solidity

file:  src/erc-4626/ERC4626.sol

47   function mint(uint256 shares, address receiver) public virtual returns (uint256 assets)

80   function redeem(uint256 shares, address receiver, address owner) public virtual returns (uint256 assets)

148  function maxDeposit(address) public view virtual returns (uint256)

153  function maxMint(address) public view virtual returns (uint256)

158  function maxWithdraw(address owner) public view virtual returns (uint256)

163  function maxRedeem(address owner) public view virtual returns (uint256)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L47

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L80

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L148

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L153

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L158

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L163

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L153

```solidity

file:

47     function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) 

75     function convertToAssets(uint256 shares) public view virtual returns (uint256) 

98     function maxDeposit(address) public view virtual returns (uint256)

103    function maxMint(address) public view virtual returns (uint256) 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L47

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L75

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L98

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L103




```solidity
file:

113         function mint(uint256 shares, address receiver)
        public
        virtual
        nonReentrant
        returns (uint256[] memory assetsAmounts)

156     function redeem(uint256 shares, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256[] memory assetsAmounts)
269     function maxDeposit(address) public view virtual returns (uint256)

274     function maxMint(address) public view virtual returns (uint256) 

279     function maxWithdraw(address owner) public view virtual returns (uint256[] memory) 

284     function maxRedeem(address owner) public view virtual returns (uint256)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L113-L117

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L156-L160

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L169

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L274

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L279

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L284 



```solidity
file:

47     function mint(uint256 shares, address receiver) public virtual nonReentrant returns (uint256 assets)

59      function redeem(uint256 shares, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256 assets)

86         function totalAssets() public view virtual returns (uint256);

    function convertToShares(uint256 assets) public view virtual returns (uint256) {
        return assets;
    }

    function convertToAssets(uint256 shares) public view virtual returns (uint256) {
        return shares;
    }

    function previewDeposit(uint256 assets) public view virtual returns (uint256) {
        return assets;
    }

    function previewMint(uint256 shares) public view virtual returns (uint256) {
        return shares;
    }

    function previewRedeem(uint256 shares) public view virtual returns (uint256) {
        return shares;
    }

    /*//////////////////////////////////////////////////////////////
                     DEPOSIT/WITHDRAWAL LIMIT LOGIC
    //////////////////////////////////////////////////////////////*/

    function maxDeposit(address) public view virtual returns (uint256) {
        return type(uint256).max;
    }

    function maxMint(address) public view virtual returns (uint256) {
        return type(uint256).max;
    }

    function maxRedeem(address owner) public view virtual returns (uint256) {
        return balanceOf[owner];
    }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L47

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L59-L63

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L86-L122

```solidity

file: 

55    function forfeitMultiple(uint256 amount) public virtual

62    function forfeitMultipleAmounts(uint256 weight, uint256 boost, uint256 _governance) public virtual

96    function claimMultiple(uint256 amount) public virtual

103   function claimMultipleAmounts(uint256 weight, uint256 boost, uint256 _governance) public virtual 
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L55

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L62

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L96

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L103

```solidity
file:

85      function updateUnderlyingBalance() public virtual

90      function claimOutstanding() public virtual 

113     function convertToShares(uint256 assets) public view virtual override returns (uint256)

121     function convertToAssets(uint256 shares) public view virtual override returns (uint256)

129     function previewDeposit(uint256 assets) public view virtual override returns (uint256)

136     function previewMint(uint256 shares) public view virtual override returns (uint256) 

143     function previewWithdraw(uint256 assets) public view virtual override returns (uint256) 

151     function previewRedeem(uint256 shares) public view virtual override returns (uint256) 
```


https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L85-L151


##  [G-13] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
file:   src/hermes/minters/BaseV2Minter.sol

24      uint256 internal constant week = 86400 * 7;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L24

```solidity
file:   src/talos/TalosStrategyVanilla.sol  

47      uint24 private constant protocolFee = 2 * 1e5; //20%

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47


## [G-14]  Using delete statement can save gas


```solidity
file:  src/erc-20/ERC20Boost.sol

249    getUserBoost[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L249 

```solidity
file:     src/erc-20/ERC20MultiVotes.sol

340      _delegatesVotesCount[user][delegatee] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L340

```solidity
file:  src/hermes/minters/BaseV2Minter.sol

165    weekly = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L165

```solidity
file:    src/rewards/base/FlywheelCore.sol

98       rewardsAccrued[user] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L98

```solidity
file:  src/rewards/rewards/FlywheelAcummulatedRewards.sol

55     amount = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L55

```solidity
file:    src/rewards/rewards/FlywheelGaugeRewards.sol

225      cycleRewardsNext = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L225

```solidity
file:   src/talos/libraries/PoolVariables.sol

211     secondsAgo[1] = 0;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L211

```solidity
file:    src/ulysses-amm/UlyssesToken.sol

78       assetId[asset] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L78

## [G-15] Variable end can be of type uint128
Variable end can be of type uint128 to take less place and save one storage slot.
A lot of protocols even use uint64 for dates to reduce gas usage.

```solidity
File:     src/gauges/factories/BaseV2GaugeFactory.sol

87        function newEpoch(uint256 start, uint256 end) external 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L87

```solidity
File:    src/gauges/factories/BaseV2GaugeManager.sol

74       function newEpoch(uint256 start, uint256 end) external

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L74

## [G-16]  State variables with values known at compile time should be constants
Variables with values known at compile time and that do not change at runtime should be declared as constant.

```solidity
File:    src/hermes/minters/BaseV2Minter.sol

47      uint256 public override weekly;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L47


## [G-17]  using private rather then public for constants,save gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

```solidity

File:     src/erc-20/ERC20MultiVotes.sol

360         bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity

  
File:    src/governance/GovernorBravoDelegateMaia.sol 

9          string public constant name = "vMaia Governor Bravo";

    /// @notice The minimum setable proposal threshold
    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

    /// @notice The maximum setable proposal threshold
    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

    /// @notice The minimum setable voting period
    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

    /// @notice The max setable voting period
    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

    /// @notice The min setable voting delay
    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

    /// @notice The max setable voting delay
    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

    /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed
    uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

    /// @notice The maximum number of actions that can be included in a proposal
    uint256 public constant proposalMaxOperations = 10; // 10 actions

    /// @notice The divisor value used in percentage calculations
    uint256 public constant DIVISIONER = 1 ether;

42        bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");



```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9-L36

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-L46

```solidity

File:   src/ulysses-omnichain/lib/AnycallFlags.sol

8           uint256 public constant FLAG_NONE = 0x0;
    uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;
    uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;
    uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;
    uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;

    // exec flags used internally
    uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;
    uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L8-L16




