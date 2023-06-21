
## Optimal Struct Variable Order
- Severity: Gas Optimization
- Confidence: High

### Description
Detect optimal variable order in struct definitions to decrease the number of slots used.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- Optimization opportunity in struct File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 105          struct Proposal {

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

- original variable order (count: 14 slots)
    - uint256 id
    - address proposer
    - uint256 eta
    - address[] targets
    - uint256[] values
    - string[] signatures
    - bytes[] calldatas
    - uint256 startBlock
    - uint256 endBlock
    - uint256 forVotes
    - uint256 againstVotes
    - uint256 abstainVotes
    - bool canceled
    - bool executed
    - mapping(address => GovernorBravoDelegateStorageV1.Receipt) receipts


 - optimized variable order (count: 13 slots)
    - address proposer
    - bool canceled
    - bool executed
    - uint256 id
    - uint256 eta
    - address[] targets
    - uint256[] values
    - string[] signatures
    - bytes[] calldatas
    - uint256 startBlock
    - uint256 endBlock
    - uint256 forVotes
    - uint256 againstVotes
    - uint256 abstainVotes
    - mapping(address => GovernorBravoDelegateStorageV1.Receipt) receipts



Recomended optimizations will use 1 less slots.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136)


- Optimization opportunity in struct File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
```
 
Line: 26          struct DepositInput {

    //Deposit Info

    address hToken; //Input Local hTokens Address.

    address token; //Input Native / underlying Token Address.

    uint256 amount; //Amount of Local hTokens deposited for interaction.

    uint256 deposit; //Amount of native tokens deposited for interaction.

    uint24 toChain; //Destination chain for interaction.

}
```

- original variable order (count: 5 slots)
    - address hToken
    - address token
    - uint256 amount
    - uint256 deposit
    - uint24 toChain


 - optimized variable order (count: 4 slots)
    - address hToken
    - uint24 toChain
    - address token
    - uint256 amount
    - uint256 deposit


[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33)


- Optimization opportunity in struct File: src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
```
 
Line: 73          struct DepositMultipleParams {

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

- original variable order (count: 6 slots)
    - uint8 numberOfAssets
    - uint32 depositNonce
    - address[] hTokens
    - address[] tokens
    - uint256[] amounts
    - uint256[] deposits
    - uint24 toChain


 - optimized variable order (count: 5 slots)
    - uint32 depositNonce
    - uint24 toChain
    - uint8 numberOfAssets
    - address[] hTokens
    - address[] tokens
    - uint256[] amounts
    - uint256[] deposits


[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L73-L82](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L73-L82)


- Optimization opportunity in struct File: src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
```
 
Line: 63          struct DepositParams {

    //Deposit Info

    uint32 depositNonce; //Deposit nonce.

    address hToken; //Input Local hTokens Address.

    address token; //Input Native / underlying Token Address.

    uint256 amount; //Amount of Local hTokens deposited for interaction.

    uint256 deposit; //Amount of native tokens deposited for interaction.

    uint24 toChain; //Destination chain for interaction.

}
```

- original variable order (count: 5 slots)
    - uint32 depositNonce
    - address hToken
    - address token
    - uint256 amount
    - uint256 deposit
    - uint24 toChain


 - optimized variable order (count: 4 slots)
    - address hToken
    - uint32 depositNonce
    - uint24 toChain
    - address token
    - uint256 amount
    - uint256 deposit



Recomended optimizations will use 4 less slots.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71)



- Optimization opportunity in struct File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
```
 
Line: 55          struct DepositMultipleParams {

    //Deposit Info

    uint8 numberOfAssets; //Number of assets to deposit.

    uint32 depositNonce; //Deposit nonce.

    address[] hTokens; //Input Local hTokens Address.

    address[] tokens; //Input Native / underlying Token Address.

    uint256[] amounts; //Amount of Local hTokens deposited for interaction.

    uint256[] deposits; //Amount of native tokens deposited for interaction.

    uint24 toChain; //Destination chain for interaction.

    uint128 depositedGas; //BRanch chain gas token amount sent with request.

}
```

- original variable order (count: 6 slots)
    - uint8 numberOfAssets
    - uint32 depositNonce
    - address[] hTokens
    - address[] tokens
    - uint256[] amounts
    - uint256[] deposits
    - uint24 toChain
    - uint128 depositedGas


 - optimized variable order (count: 5 slots)
    - uint128 depositedGas
    - uint32 depositNonce
    - uint24 toChain
    - uint8 numberOfAssets
    - address[] hTokens
    - address[] tokens
    - uint256[] amounts
    - uint256[] deposits



Recomended optimizations will use 2 less slots.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L55-L65](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L55-L65)


</details>

# 

## Optimal State Variable Order
- Severity: Gas Optimization
- Confidence: High

### Description
Detect optimal variable order in contract storage layouts to decrease the number of slots used.

<details>

<summary>
There are 3 instances of this issue:

</summary>

###
- Optimization opportunity in storage variable layout of contract File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 58          contract BranchBridgeAgent is IBranchBridgeAgent 
```

- original variable order (count: 17 slots)
    - uint8 PARAMS_START
    - uint8 PARAMS_START_SIGNED
    - uint8 PARAMS_ENTRY_SIZE
    - uint8 PARAMS_GAS_OUT
    - uint8 PARAMS_TKN_START
    - uint8 PARAMS_AMT_OFFSET
    - uint8 PARAMS_DEPOSIT_OFFSET
    - uint256 rootChainId
    - uint256 localChainId
    - WETH9 wrappedNativeToken
    - address rootBridgeAgentAddress
    - address localAnyCallAddress
    - address localAnyCallExecutorAddress
    - address localRouterAddress
    - address localPortAddress
    - address bridgeAgentExecutorAddress
    - uint32 depositNonce
    - mapping(uint32 => Deposit) getDeposit
    - mapping(uint32 => bool) executionHistory
    - uint256 remoteCallDepositedGas
    - uint256 MIN_FALLBACK_RESERVE
    - uint256 MIN_EXECUTION_OVERHEAD
    - uint256 TRANSFER_OVERHEAD
    - uint256 _unlocked


 - optimized variable order (count: 16 slots)
    - WETH9 wrappedNativeToken
    - uint32 depositNonce
    - uint8 PARAMS_START
    - uint8 PARAMS_START_SIGNED
    - uint8 PARAMS_ENTRY_SIZE
    - uint8 PARAMS_GAS_OUT
    - uint8 PARAMS_TKN_START
    - uint8 PARAMS_AMT_OFFSET
    - uint8 PARAMS_DEPOSIT_OFFSET
    - address rootBridgeAgentAddress
    - address localAnyCallAddress
    - address localAnyCallExecutorAddress
    - address localRouterAddress
    - address localPortAddress
    - address bridgeAgentExecutorAddress
    - uint256 rootChainId
    - uint256 localChainId
    - mapping(uint32 => Deposit) getDeposit
    - mapping(uint32 => bool) executionHistory
    - uint256 remoteCallDepositedGas
    - uint256 MIN_FALLBACK_RESERVE
    - uint256 MIN_EXECUTION_OVERHEAD
    - uint256 TRANSFER_OVERHEAD
    - uint256 _unlocked



Recomended optimizations will use 1 less slots.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L58-L1420](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L58-L1420)



- Optimization opportunity in storage variable layout of contract File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 90          contract RootBridgeAgent is IRootBridgeAgent 
```

- original variable order (count: 21 slots)
    - uint8 PARAMS_START
    - uint8 PARAMS_START_SIGNED
    - uint8 PARAMS_ADDRESS_SIZE
    - uint8 PARAMS_GAS_IN
    - uint8 PARAMS_GAS_OUT
    - uint8 PARAMS_TKN_START
    - uint8 PARAMS_AMT_OFFSET
    - uint8 PARAMS_DEPOSIT_OFFSET
    - uint24 localChainId
    - WETH9 wrappedNativeToken
    - address factoryAddress
    - address daoAddress
    - address localRouterAddress
    - address localPortAddress
    - address localAnyCallAddress
    - address localAnyCallExecutorAddress
    - address bridgeAgentExecutorAddress
    - mapping(uint256 => address) getBranchBridgeAgent
    - mapping(uint256 => bool) isBranchBridgeAgentAllowed
    - uint32 settlementNonce
    - mapping(uint32 => Settlement) getSettlement
    - mapping(uint256 => mapping(uint32 => bool)) executionHistory
    - uint256 MIN_FALLBACK_RESERVE
    - uint256 MIN_EXECUTION_OVERHEAD
    - uint256 initialGas
    - UserFeeInfo userFeeInfo
    - uint256 accumulatedFees
    - uint24 GLOBAL_DIVISIONER
    - mapping(address => bool) approvedGasPool
    - uint256 _unlocked


 - optimized variable order (count: 19 slots)
    - WETH9 wrappedNativeToken
    - uint32 settlementNonce
    - uint24 localChainId
    - uint24 GLOBAL_DIVISIONER
    - uint8 PARAMS_START
    - uint8 PARAMS_START_SIGNED
    - address factoryAddress
    - uint8 PARAMS_ADDRESS_SIZE
    - uint8 PARAMS_GAS_IN
    - uint8 PARAMS_GAS_OUT
    - uint8 PARAMS_TKN_START
    - uint8 PARAMS_AMT_OFFSET
    - uint8 PARAMS_DEPOSIT_OFFSET
    - address daoAddress
    - address localRouterAddress
    - address localPortAddress
    - address localAnyCallAddress
    - address localAnyCallExecutorAddress
    - address bridgeAgentExecutorAddress
    - mapping(uint256 => address) getBranchBridgeAgent
    - mapping(uint256 => bool) isBranchBridgeAgentAllowed
    - mapping(uint32 => Settlement) getSettlement
    - mapping(uint256 => mapping(uint32 => bool)) executionHistory
    - uint256 MIN_FALLBACK_RESERVE
    - uint256 MIN_EXECUTION_OVERHEAD
    - uint256 initialGas
    - UserFeeInfo userFeeInfo
    - uint256 accumulatedFees
    - mapping(address => bool) approvedGasPool
    - uint256 _unlocked



Recomended optimizations will use 2 less slots.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L90-L1335](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L90-L1335)



- Optimization opportunity in storage variable layout of contract File: src/ulysses-omnichain/RootPort.sol
```
 
Line: 20          contract RootPort is Ownable, IRootPort 
```

- original variable order (count: 22 slots)
    - uint24 localChainId
    - address wrappedNativeTokenAddress
    - address localBranchPortAddress
    - address coreRootRouterAddress
    - address coreRootBridgeAgentAddress
    - mapping(address => VirtualAccount) getUserAccount
    - mapping(VirtualAccount => mapping(address => bool)) isRouterApproved
    - mapping(uint256 => bool) isChainId
    - mapping(address => bool) isBridgeAgent
    - address[] bridgeAgents
    - uint256 bridgeAgentsLenght
    - mapping(address => address) getBridgeAgentManager
    - mapping(address => bool) isBridgeAgentFactory
    - address[] bridgeAgentFactories
    - uint256 bridgeAgentFactoriesLenght
    - mapping(address => bool) isGlobalAddress
    - mapping(address => mapping(uint256 => address)) getGlobalTokenFromLocal
    - mapping(address => mapping(uint256 => address)) getLocalTokenFromGlobal
    - mapping(address => mapping(uint256 => address)) getLocalTokenFromUnder
    - mapping(address => mapping(uint256 => address)) getUnderlyingTokenFromLocal
    - mapping(uint256 => address) getWrappedNativeToken
    - mapping(uint256 => GasPoolInfo) getGasPoolInfo
    - bool _setup


 - optimized variable order (count: 21 slots)
    - address wrappedNativeTokenAddress
    - uint24 localChainId
    - bool _setup
    - address localBranchPortAddress
    - address coreRootRouterAddress
    - address coreRootBridgeAgentAddress
    - mapping(address => VirtualAccount) getUserAccount
    - mapping(VirtualAccount => mapping(address => bool)) isRouterApproved
    - mapping(uint256 => bool) isChainId
    - mapping(address => bool) isBridgeAgent
    - address[] bridgeAgents
    - uint256 bridgeAgentsLenght
    - mapping(address => address) getBridgeAgentManager
    - mapping(address => bool) isBridgeAgentFactory
    - address[] bridgeAgentFactories
    - uint256 bridgeAgentFactoriesLenght
    - mapping(address => bool) isGlobalAddress
    - mapping(address => mapping(uint256 => address)) getGlobalTokenFromLocal
    - mapping(address => mapping(uint256 => address)) getLocalTokenFromGlobal
    - mapping(address => mapping(uint256 => address)) getLocalTokenFromUnder
    - mapping(address => mapping(uint256 => address)) getUnderlyingTokenFromLocal
    - mapping(uint256 => address) getWrappedNativeToken
    - mapping(uint256 => GasPoolInfo) getGasPoolInfo



Recomended optimizations will use 1 less slots.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L20-L532](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L20-L532)


</details>

# 



## Unnecessary Casting of Variables
- Severity: Gas Optimization
- Confidence: High

### Description
Where a variable is casted to its own type. This is unnecessary and can be safely removed to improve code readability.

<details>

<summary>
There are 25 instances of this issue:

</summary>

###

- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 100          rewardToken.safeTransferFrom(address(flywheelRewards), user, accrued)
```
Unnecessary cast: `address(flywheelRewards)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L100](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L100)


- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 126          uint256 oldRewardBalance = rewardToken.balanceOf(address(flywheelRewards))
```
Unnecessary cast: `address(flywheelRewards)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L126](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L126)


- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 128          rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance)
```
Unnecessary cast: `address(flywheelRewards)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L128](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L128)


- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 128          rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance)
```
Unnecessary cast: `address(newFlywheelRewards)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L128](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L128)


- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 133          emit FlywheelRewardsUpdate(address(newFlywheelRewards))
```
Unnecessary cast: `address(newFlywheelRewards)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L133](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L133)
 

- File: src/gauges/BaseV2Gauge.sol
```
 
Line: 132          address flyWheelRewards = address(bribeFlywheel.flywheelRewards())
```
Unnecessary cast: `address(bribeFlywheel.flywheelRewards())` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L132](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L132)

 
- File: src/maia/tokens/ERC4626PartnerManager.sol
```
 
Line: 244          ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate)
```
Unnecessary cast: `ERC20MultiVotes(partnerGovernance)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L244](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L244)
 

- File: src/rewards/depots/SingleRewardsDepot.sol
```
 
Line: 41          msg.sender != address(rewardsContract)
```
Unnecessary cast: `address(rewardsContract)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L41](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L41)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 261          uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply)
```
Unnecessary cast: `uint128((liquidity * shares) / totalSupply)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L261](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L261)


- File: src/ulysses-amm/UlyssesRouter.sol
```
 
Line: 40          address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max)
```
Unnecessary cast: `address(ulysses.asset())` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40)


- File: src/ulysses-amm/UlyssesRouter.sol
```
 
Line: 74          address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount)
```
Unnecessary cast: `address(getUlyssesLP(routes[0].from).asset())` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L74](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L74)


- File: src/ulysses-amm/UlyssesRouter.sol
```
 
Line: 92          address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount)
```
Unnecessary cast: `address(getUlyssesLP(routes[length].to).asset())` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L92](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L92)


- File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol
```
 
Line: 13          ERC20(string(string.concat("Hermes - ", _name)), string(string.concat("h-", _symbol)), 18)
```
Unnecessary cast: `string(string.concat("h-", _symbol))` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L13](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L13)


- File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol
```
 
Line: 13          ERC20(string(string.concat("Hermes - ", _name)), string(string.concat("h-", _symbol)), 18)
```
Unnecessary cast: `string(string.concat("Hermes - ", _name))` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L13](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L13)


- File: src/ulysses-omnichain/CoreRootRouter.sol
```
 
Line: 64          bridgeAgentAddress = payable(_bridgeAgentAddress)
```
Unnecessary cast: `payable(_bridgeAgentAddress)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L64](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L64)

 

- File: src/ulysses-omnichain/MulticallRootRouter.sol
```
 
Line: 77          bridgeAgentAddress = payable(_bridgeAgentAddress)
```
Unnecessary cast: `payable(_bridgeAgentAddress)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L77](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L77)


 

- File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol
```
 
Line: 44          ERC20(string(string.concat("Hermes ", _name)), string(string.concat("h-", _symbol)), 18)
```
Unnecessary cast: `string(string.concat("h-", _symbol))` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L44](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L44)


 

 

- File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol
```
 
Line: 364          i < uint256(uint8(numOfAssets))
```
Unnecessary cast: `uint8(numOfAssets)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L364](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L364)


 

- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 651          address(data.tokenIn).safeTransfer(msg.sender, uint256(amount0 > 0 ? amount0 : amount1))
```
Unnecessary cast: `address(data.tokenIn)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L651](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L651)


 


- File: src/ulysses-omnichain/RootPort.sol
```
 
Line: 160          _setOwner(address(_owner))
```
Unnecessary cast: `address(_owner)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L160](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L160)


- File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol
```
 
Line: 44          ERC20(string(string.concat("Hermes ", _name)), string(string.concat("h-", _symbol)), 18)
```
Unnecessary cast: `string(string.concat("Hermes ", _name))` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L44](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L44)

- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 315          secondsInsideX128 = RewardMath.computeBoostedSecondsInsideX128(

                stakedDuration,

                liquidity,

                uint128(boostAmount),

                uint128(boostTotalSupply),

                secondsPerLiquidityInsideInitialX128,

                secondsPerLiquidityInsideX128

            )
```
Unnecessary cast: `uint128(boostAmount)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L315-L322](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L315-L322)


- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 315          secondsInsideX128 = RewardMath.computeBoostedSecondsInsideX128(

                stakedDuration,

                liquidity,

                uint128(boostAmount),

                uint128(boostTotalSupply),

                secondsPerLiquidityInsideInitialX128,

                secondsPerLiquidityInsideX128

            )
```
Unnecessary cast: `uint128(boostTotalSupply)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L315-L322](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L315-L322)


- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 416          secondsInsideX128 = RewardMath.computeBoostedSecondsInsideX128(

                stakedDuration,

                liquidity,

                uint128(boostAmount),

                uint128(boostTotalSupply),

                secondsPerLiquidityInsideInitialX128,

                secondsPerLiquidityInsideX128

            )
```
Unnecessary cast: `uint128(boostAmount)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L416-L423](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L416-L423)


- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 416          secondsInsideX128 = RewardMath.computeBoostedSecondsInsideX128(

                stakedDuration,

                liquidity,

                uint128(boostAmount),

                uint128(boostTotalSupply),

                secondsPerLiquidityInsideInitialX128,

                secondsPerLiquidityInsideX128

            )
```
Unnecessary cast: `uint128(boostTotalSupply)` it cast to the same type.<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L416-L423](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L416-L423)


</details>

# 
## Splitting Require Assert Statements
- Severity: Gas Optimization
- Confidence: High

### Description
Instead of using operator && in a a single require clause split into multiple clauses to save gas.

<details>

<summary>
There are 11 instances of this issue:

</summary>

###
- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 67          require(

            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,

            "GovernorBravo::initialize: invalid voting period"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L67-L70](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L67-L70)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 71          require(

            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,

            "GovernorBravo::initialize: invalid voting delay"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L71-L74](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L71-L74)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 75          require(

            proposalThreshold_ >= MIN_PROPOSAL_THRESHOLD && proposalThreshold_ <= MAX_PROPOSAL_THRESHOLD,

            "GovernorBravo::initialize: invalid proposal threshold"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L75-L78](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L75-L78)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 119          require(

            targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,

            "GovernorBravo::propose: proposal function information arity mismatch"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L119-L122](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L119-L122)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 237          require(

                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())

                        && msg.sender == whitelistGuardian,

                    "GovernorBravo::cancel: whitelisted proposer"

                )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L237-L241](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L237-L241)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 298          require(

            proposalCount >= proposalId && proposalId > initialProposalId, "GovernorBravo::state: invalid proposal id"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L298-L300](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L298-L300)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 399          require(

            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,

            "GovernorBravo::_setVotingDelay: invalid voting delay"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L399-L402](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L399-L402)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 415          require(

            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,

            "GovernorBravo::_setVotingPeriod: invalid voting period"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L415-L418](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L415-L418)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 432          require(

            newProposalThreshold >= MIN_PROPOSAL_THRESHOLD && newProposalThreshold <= MAX_PROPOSAL_THRESHOLD,

            "GovernorBravo::_setProposalThreshold: invalid proposal threshold"

        )
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L432-L435](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L432-L435)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 507          require(

            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"

        );

```
 split the condition:

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L507-L509](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L507-L509)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 408          require(balance0 >= amount0 && balance1 >= amount1)
```
 split the condition

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L408](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L408)


</details>

# 



## Use != 0 Instead of > 0 to Save Gas
- Severity: Gas Optimization
- Confidence: High

### Description
This detector checks for instances where a non-negative number is compared to 0 using the ">/<" comparison. It suggests replacing this with the "!="/"==" comparison as it could potentially save a small amount of gas.

<details>

<summary>
There are 68 instances of this issue:

</summary>

###

- File: src/maia/PartnerUtilityManager.sol
```
 
Line: 85          partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0
```
Replace `address(gaugeBoost).balanceOf(address(this)) > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85)


- File: src/maia/PartnerUtilityManager.sol
```
 
Line: 95          partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0
```
Replace `address(governance).balanceOf(address(this)) > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95)


- File: src/maia/PartnerUtilityManager.sol
```
 
Line: 75          partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0
```
Replace `address(gaugeWeight).balanceOf(address(this)) > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75)
 


- File: src/uni-v3-staker/UniswapV3Staker.sol
```
v 
Line: 158          reward <= 0
```
Replace `reward <= 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L158](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L158)


 


- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 271          reward > 0
```
Replace `reward > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L271](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L271)

- File: src/talos/TalosStrategyVanilla.sol
```
 
Line: 139          _liquidity > 0
```
Replace `_liquidity > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 409          amount0 > 0
```
Replace `amount0 > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 410          amount1 > 0
```
Replace `amount1 > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410)

 

- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 153          claimed > 0
```
Replace `claimed > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L153](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L153)


 


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 282          leftOverBandwidth > 0
```
Replace `leftOverBandwidth > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L282](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L282)


 


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 926          if gt(positiveFee, 0) { output := add(output, positiveFee) }
```
Replace `positiveFee > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L926](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L926)


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 968          if gt(updateAmount, 0) { output := add(output, updateAmount) }
```
Replace `updateAmount > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L968](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L968)


 


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 991          gt(_positiveFee, 0)
```
Replace `_positiveFee > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L991](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L991)


- File: src/ulysses-amm/factories/UlyssesFactory.sol
```
 
Line: 111          j != i && weights[i][j] > 0
```
Replace `weights[i_scope_0][j] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111)
 

 

- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 191          oldBandwidth > 0
```
Replace `oldBandwidth > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L191](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L191)



- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 272          oldBandwidth > 0
```
Replace `oldBandwidth_scope_1 > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L272](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L272)


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 256          oldBandwidth > 0
```
Replace `oldBandwidth > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L256](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L256)


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 911          updateAmount > 0
```
Replace `updateAmount > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L911](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L911)



 

- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 981          gt(_positiveFee, 0)
```
Replace `_positiveFee > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L981](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L981)


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 1048          updateAmount > 0
```
Replace `updateAmount > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1048](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1048)


- File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 47          require(_weight > 0)
```
Replace `_weight > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47)

 

- File: src/erc-4626/ERC4626MultiToken.sol
```
 
Line: 52          require(_weights[i] > 0)
```
Replace `_weights[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52)



 


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 979          _amount - _deposit > 0
```
Replace `_amount - _deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L979](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L979)
 
 
- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 517          (uint128 gasToBridgeOut, bool isRemote) =

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false))
```
Replace `remoteCallDepositedGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L517-L518](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L517-L518)

 

- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 623          _amounts[i] - _deposits[i] > 0
```
Replace `_amounts[i] - _deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L623](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L623)


 
 


- File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
```
 
Line: 160          gasRemaining > 0
```
Replace `gasRemaining > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 123          _amount - _deposit > 0
```
Replace `_amount - _deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L123](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L123)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 118          _deposit > 0
```
Replace `_deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 144          _amounts[i] - _deposits[i] > 0
```
Replace `_amounts[i] - _deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L144](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L144)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 137          _deposits[i] > 0
```
Replace `_deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L137](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L137)
 
 

- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 495          (uint128 gasToBridgeOut, bool isRemote) =

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false))
```
Replace `remoteCallDepositedGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L495-L496](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L495-L496)


 


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 521          !isRemote && gasToBridgeOut > 0
```
Replace `gasToBridgeOut > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L521](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L521)



- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 627          _deposits[i] > 0
```
Replace `_deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627)



- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 248          _amount - _deposit > 0
```
Replace `_amount - _deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L248](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L248)


 

 


- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 268          _deposits[i] > 0
```
Replace `_deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L268](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L268)



- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 474          !isRemote && gasToBridgeOut > 0
```
Replace `gasToBridgeOut > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 499          !isRemote && gasToBridgeOut > 0
```
Replace `gasToBridgeOut > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499)



 

 


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 539          (uint128 gasToBridgeOut, bool isRemote) =

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false))
```
Replace `remoteCallDepositedGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L539-L540](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L539-L540)
 
 

- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 983          _deposit > 0
```
Replace `_deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L983](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L983)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 470          (uint128 gasToBridgeOut, bool isRemote) =

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false))
```
Replace `remoteCallDepositedGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L470-L471](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L470-L471)


 
 


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 543          !isRemote && gasToBridgeOut > 0
```
Replace `gasToBridgeOut > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L543](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L543)
 

- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 1328          executionBudget > 0
```
Replace `executionBudget > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328)


 


- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 252          _deposit > 0
```
Replace `_deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L252](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L252)


- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 275          _amounts[i] - _deposits[i] > 0
```
Replace `_amounts[i] - _deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L275](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L275)



 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 56          (_dParams.amount < _dParams.deposit) //Deposit can't be greater than amount.

                || (_dParams.amount > 0 && !IPort(_localPortAddress).isLocalToken(_dParams.hToken, _fromChain)) //Check local exists.

                || (_dParams.deposit > 0 && !IPort(_localPortAddress).isUnderlyingToken(_dParams.token, _fromChain))
```
Replace `_dParams.amount > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L56-L58](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L56-L58)


 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 750          _initialGas > 0
```
Replace `_initialGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L750](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L750)


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 457          _deposit > 0
```
Replace `_deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L457](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L457)


 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 451          _amount - _deposit > 0
```
Replace `_amount - _deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L451](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L451)


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 347          hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)
```
Replace `_deposits[i] > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347)


 

- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 1161          initialGas > 0
```
Replace `initialGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1161](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1161)


 

- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 56          (_dParams.amount < _dParams.deposit) //Deposit can't be greater than amount.

                || (_dParams.amount > 0 && !IPort(_localPortAddress).isLocalToken(_dParams.hToken, _fromChain)) //Check local exists.

                || (_dParams.deposit > 0 && !IPort(_localPortAddress).isUnderlyingToken(_dParams.token, _fromChain))
```
Replace `_dParams.deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L56-L58](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L56-L58)


 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 750          _initialGas > 0
```
Replace `_initialGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L750](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L750)


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 757          _initialGas > 0
```
Replace `_initialGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L757](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L757)


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 451          _amount - _deposit > 0
```
Replace `_amount - _deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L451](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L451)


 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 301          localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0)
```
Replace `_deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L301](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L301)


 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 1240          executionBudget > 0
```
Replace `executionBudget > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1240](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1240)


 


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 1171          initialGas > 0
```
Replace `initialGas > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1171](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1171)


- File: src/ulysses-omnichain/RootPort.sol
```
 
Line: 282          _amount - _deposit > 0
```
Replace `_amount - _deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282)


- File: src/ulysses-omnichain/RootPort.sol
```
 
Line: 283          _deposit > 0
```
Replace `_deposit > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L283](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L283)


- File: src/erc-20/ERC20Gauges.sol
```
 
Line: 417          weight > 0
```
Replace `weight > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417)


- File: src/erc-20/ERC20Gauges.sol
```
 
Line: 441          weight > 0
```
Replace `weight > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L441](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L441)


- File: src/erc-20/ERC20MultiVotes.sol
```
 
Line: 250          pos > 0 && ckpts[pos - 1].fromBlock == block.number
```
Replace `pos > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250)


- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 162          strategyRewardsAccrued > 0
```
Replace `strategyRewardsAccrued > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L162](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L162)


- File: src/rewards/base/FlywheelCore.sol
```
 
Line: 127          oldRewardBalance > 0
```
Replace `oldRewardBalance > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127)


- File: src/rewards/rewards/FlywheelGaugeRewards.sol
```
 
Line: 234          accruedRewards > 0
```
Replace `accruedRewards > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234)


- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 138          reward <= 0
```
Replace `reward <= 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L138](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L138)



- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 199          incentive.numberOfStakes > 0
```
Replace `incentive.numberOfStakes > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L199](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L199)



- File: src/uni-v3-staker/UniswapV3Staker.sol
```
 
Line: 281          reward > 0
```
Replace `reward > 0` with `==`/`!=`
[https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L281](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L281)


</details>

#


## Multiplication and Division by 2 Should use in Bit Shifting
- Severity: Gas Optimization
- Confidence: High


### Description
The expressions 'x * 2' and 'x / 2' can be optimized for gas efficiency by utilizing bitwise operations. In Solidity, you can achieve the same results by using bitwise left shift (x << 1) for multiplication and bitwise right shift (x >> 1) for division.

Using bitwise shift operations (SHL and SHR) instead of multiplication (MUL) and division (DIV) opcodes can lead to significant gas savings. The MUL and DIV opcodes cost 5 gas, while the SHL and SHR opcodes incur a lower cost of only 3 gas.

By leveraging these more efficient bitwise operations, you can reduce the gas consumption of your smart contracts and enhance their overall performance.

## Note:
 I reported issues that were overlooked by the winning bot. Specifically, the bot missed opportunities for optimization in the cases of multiplication and division operations performed inside assembly blocks. These operations could potentially be replaced by more efficient bitwise shift operations (`shl` and `shr`). I reported only on instances that were missed by the winning bot. 
 
 It's important to mention that I've merged the issues of multiplication and division into one, considering they are conceptually similar and can be addressed using the same optimization approach. Hence, please take this consolidation into account when evaluating the reported issues


<details>

<summary>
There are 7 instances of this issue:

</summary>

###
  
- File: src/talos/TalosStrategyVanilla.sol
```
 
Line: 47          uint24 private constant protocolFee = 2 * 1e5
```
 instead `2` use bit shifting `1` 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47)


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 396          let slot := sload(add(bandwidthStateListStart, mul(i, 2)))
```
 instead `2` use bit shifting `1` 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L396](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L396)

 

- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 490          let weight := shr(248, sload(add(bandwidthStateListStart, mul(i, 2))))
```
 instead `2` use bit shifting `1` like this shl(1, i)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L490](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L490)

 

- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 424          mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L424](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L424)


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 522          mstore(add(bandwidthUpdateAmounts, add(mul(i, 0x20), 0x20)), bandwidthUpdate)
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L522](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L522)



- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 464          let diff := mload(add(diffs, add(mul(i, 0x20), 0x20)))
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L464](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L464)
 

- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 436          mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)
```
 instead `32` use bit shifting `5` 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L436](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L436)



</details>

# 


## Empty Block
- Severity: Gas Optimization
- Confidence: High

### Description
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified.

## Note: 
I reported issues that were overlooked by the winning bot. I reported only on instances that were missed.

It's important to highlight that I didn't report on cases of empty functions overriding others, as these are often intentional design choices in contract inheritance and do not constitute inefficiencies or errors.

<details>

<summary>
There are 6 instances of this issue:

</summary>

###
 

- File: src/talos/TalosManager.sol
```
 
Line: 96          try strategy.pool().checkDeviation(optimizer. maxTwapDeviation(), optimizer.twapDuration()) {}
        catch {
            ...
        }
```
  contains an empty block.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L96-L99](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L96-L99)



- File: src/talos/TalosStrategyStaked.sol
```
 
Line: 177          try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId) {
            ...
        } catch {}
```
  contains an empty block.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L177-L179](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L177-L179)


- File: src/rewards/rewards/FlywheelGaugeRewards.sol
```
 
Line: 221          if (incompleteCycle) {

            // If current cycle queue incomplete, do nothing to current cycle rewards or accrued

        } else {

            ...
        }
```
  contains an empty block.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L221-L226](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L221-L226)




- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 1328          if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
```
  contains an empty block.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 1328          try anycallConfig.withdraw(executionBudget) {} catch {}
```
  contains an empty block.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328)


 

- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 1240          try anycallConfig.withdraw(executionBudget) {} catch {}
```
  contains an empty block.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1240](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1240)

 
</details>

# 


## Constants Variable Should Be Private for Gas Optimization
- Severity: Gas Optimization
- Confidence: High

### Description
Using `private` for constant variables is cheaper in terms of gas usage. If the value of the constant variable is needed, it can be accessed via a getter function. In case of multiple constant variables, a single getter function could be used to return a tuple of the values of all currently-private constants.

<details>

<summary>
There are 13 instances of this issue:

</summary>

###
 
- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 9          string public constant name = "vMaia Governor Bravo"
```
 `name` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 12          uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether
```
 `MIN_PROPOSAL_THRESHOLD` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L12](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L12)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 15          uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether
```
 `MAX_PROPOSAL_THRESHOLD` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L15](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L15)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 18          uint256 public constant MIN_VOTING_PERIOD = 80640
```
 `MIN_VOTING_PERIOD` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L18](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L18)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 21          uint256 public constant MAX_VOTING_PERIOD = 161280
```
 `MAX_VOTING_PERIOD` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L21](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L21)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 24          uint256 public constant MIN_VOTING_DELAY = 40320
```
 `MIN_VOTING_DELAY` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L24](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L24)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 27          uint256 public constant MAX_VOTING_DELAY = 80640
```
 `MAX_VOTING_DELAY` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L27](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L27)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 30          uint256 public constant quorumVotes = 0.35 ether
```
 `quorumVotes` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L30](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L30)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 33          uint256 public constant proposalMaxOperations = 10
```
 `proposalMaxOperations` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L33](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L33)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 36          uint256 public constant DIVISIONER = 1 ether
```
 `DIVISIONER` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L36](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L36)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 42          bytes32 public constant DOMAIN_TYPEHASH =

        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)")
```
 `DOMAIN_TYPEHASH` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-L43](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-L43)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 46          bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)")
```
 `BALLOT_TYPEHASH` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46)


- File: src/erc-20/ERC20MultiVotes.sol
```
 
Line: 360          bytes32 public constant DELEGATION_TYPEHASH =

        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)")
```
 `DELEGATION_TYPEHASH` is a public constant variable. Consider making it private to save gas.

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361)


</details>

# 
## Unused Named Return Variables
- Severity: Gas Optimization
- Confidence: High

### Description
Named return variables allow for clear and explicit naming of values to be returned from a function. However, when these variables are unused, it can lead to confusion and make the code less maintainable.

<details>

<summary>
There are 16 instances of this issue:

</summary>

###
- File: src/erc-20/ERC20Gauges.sol
```
 
Line: 188          function incrementGauge(address gauge, uint112 weight) external nonReentrant returns (uint112 newUserWeight) 
```
there is not use of this variables:
@ **newUserWeight**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L188-L192](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L188-L192)


- File: src/erc-20/ERC20Gauges.sol
```
 
Line: 245          function incrementGauges(address[] calldata gaugeList, uint112[] calldata weights)

        external

        nonReentrant

        returns (uint256 newUserWeight)

    
```
there is not use of this variables:
@ **newUserWeight**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L245-L270](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L245-L270)


- File: src/erc-20/ERC20Gauges.sol
```
 
Line: 273          function decrementGauge(address gauge, uint112 weight) external nonReentrant returns (uint112 newUserWeight) 
```
there is not use of this variables:
@ **newUserWeight**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L273-L282](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L273-L282)


- File: src/erc-20/ERC20Gauges.sol
```
 
Line: 322          function decrementGauges(address[] calldata gaugeList, uint112[] calldata weights)

        external

        nonReentrant

        returns (uint112 newUserWeight)

    
```
there is not use of this variables:
@ **newUserWeight**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L322-L352](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L322-L352)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 268          function getActions(uint256 proposalId)

        external

        view

        returns (

            address[] memory targets,

            uint256[] memory values,

            string[] memory signatures,

            bytes[] memory calldatas

        )

    
```
there is not use of this variables:
@ **targets**
@ **values**
@ **signatures**
@ **calldatas**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L268-L280](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L268-L280)

 
- File: src/rewards/depots/SingleRewardsDepot.sol
```
 
Line: 32          function getRewards() external override onlyFlywheelRewards returns (uint256 balance) 
```
there is not use of this variables:
@ **balance**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L32-L34](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L32-L34)



- File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 39          function totalAssets() public view override returns (uint256 _totalAssets) 
```
there is not use of this variables:
@ **_totalAssets**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L39-L41](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L39-L41)
 

- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 1227          function anyFallback(bytes calldata data)

        external

        virtual

        requiresExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1227-L1307](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1227-L1307)



- File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
```
 
Line: 112          function anyExecuteNoSettlement(bytes calldata _data)

        external

        override

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L112-L157](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L112-L157)


- File: src/ulysses-omnichain/BaseBranchRouter.sol
```
 
Line: 101          function anyExecuteNoSettlement(bytes calldata)

        external

        virtual

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L101-L109](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L101-L109)


- File: src/ulysses-omnichain/BaseBranchRouter.sol
```
 
Line: 112          function anyExecuteSettlement(bytes calldata, SettlementParams memory)

        external

        virtual

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L112-L120](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L112-L120)


- File: src/ulysses-omnichain/BaseBranchRouter.sol
```
 
Line: 123          function anyExecuteSettlementMultiple(bytes calldata, SettlementMultipleParams memory)

        external

        virtual

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L123-L131](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L123-L131)


- File: src/ulysses-omnichain/CoreBranchRouter.sol
```
 
Line: 229          function anyExecuteNoSettlement(bytes calldata _data)

        external

        virtual

        override

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L229-L282](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L229-L282)


- File: src/ulysses-omnichain/MulticallRootRouter.sol
```
 
Line: 345          function anyExecuteSignedDepositSingle(

        bytes1 funcId,

        bytes calldata encodedData,

        DepositParams calldata,

        address userAccount,

        uint24

    ) external payable override requiresExecutor lock returns (bool success, bytes memory result) 
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L345-L409](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L345-L409)


- File: src/ulysses-omnichain/MulticallRootRouter.sol
```
 
Line: 421          function anyExecuteSignedDepositMultiple(

        bytes1 funcId,

        bytes memory encodedData,

        DepositMultipleParams calldata,

        address userAccount,

        uint24

    ) external payable requiresExecutor lock returns (bool success, bytes memory result) 
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L421-L485](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L421-L485)


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 1177          function anyFallback(bytes calldata data)

        external

        virtual

        requiresExecutor

        returns (bool success, bytes memory result)

    
```
there is not use of this variables:
@ **success**
@ **result**

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1177-L1216](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1177-L1216)


</details>

# 

## State variables that could be declared immutable
- Severity: Gas Optimization
- Confidence: High

### Description
State variables that are not updated following deployment should be declared immutable to save gas.

<details>

<summary>
There are 27 instances of this issue:

</summary>

###
- File: src/erc-4626/ERC4626MultiToken.sol
```
 
Line: 32          uint256 public totalWeights
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L32](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L32)


- File: src/gauges/BaseV2Gauge.sol
```
 
Line: 44          MultiRewardsDepot public override multiRewardsDepot
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L44](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L44)


- File: src/gauges/BaseV2Gauge.sol
```
 
Line: 41          address public override strategy
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L41](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L41)



- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 62          address public admin
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L62](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L62)


- File: src/maia/PartnerUtilityManager.sol
```
 
Line: 20          address public partnerVault
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L20](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L20)



- File: src/ulysses-amm/UlyssesRouter.sol
```
 
Line: 18          UlyssesFactory public ulyssesFactory
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L18](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L18)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 112          address public bridgeAgentExecutorAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L112](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L112)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 22          uint24 public localChainId
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L22](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L22)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 25          address public rootPortAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L25](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L25)


 


- File: src/ulysses-omnichain/VirtualAccount.sol
```
 
Line: 23          address public localPortAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L23](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L23)


- File: src/ulysses-omnichain/CoreBranchRouter.sol
```
 
Line: 21          address public hTokenFactoryAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L21](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L21)


- File: src/ulysses-omnichain/CoreBranchRouter.sol
```
 
Line: 24          address public localPortAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L24](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L24)
 

- File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol
```
 
Line: 25          address public factoryAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L25](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L25)


- File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol
```
 
Line: 16          uint256 public localChainId
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L16](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L16)


- File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol
```
 
Line: 19          address public rootPortAddress
```
 should be immutable 

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L19](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L19)

 
 
- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 97          GovTokenInterface public govToken
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L97](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L97)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 88          uint256 public initialProposalId
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L88](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L88)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 91          uint256 public proposalCount
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L91](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L91)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 85          uint256 public proposalThreshold
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L85](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L85)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 94          TimelockInterface public timelock
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L94](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L94)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 79          uint256 public votingDelay
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L79](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L79)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 82          uint256 public votingPeriod
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L82](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L82)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 166          address public whitelistGuardian
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L166](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L166)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 62          address public admin
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L62](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L62)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 68          address public implementation
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L68](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L68)


- File: src/governance/GovernorBravoInterfaces.sol
```
 
Line: 65          address public pendingAdmin
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L65](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L65)


- File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol
```
 
Line: 22          address public localBranchPortAddress
```
 should be immutable
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L22](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L22)


</details>

# 

## Inefficient assignments to state variables
- Severity: Gas Optimization
- Confidence: High

### Description
Assignment operations that involve calculations, like add-assigment (`+=`), for state variables, should be replaced with the appropriate calculation operation, like add (`+`), followed by an assignment operation (`=`), to save gas. Avoids a `Gwarmaccess` (100 gas).

## Note: 
I reported issues that were overlooked by the winning bot. I reported only on instances that were missed.

<details>

<summary>
There are 7 instances of this issue:

</summary>

###


- File: src/talos/TalosStrategyStaked.sol
```
 
Line: 157          protocolFees0 += collect0
```
 should use the appropriate calculation operation (`+`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L157](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L157)


- File: src/talos/TalosStrategyStaked.sol
```
 
Line: 158          protocolFees1 += collect1
```
 should use the appropriate calculation operation (`+`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L158](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L158)



- File: src/talos/TalosStrategyVanilla.sol
```
 
Line: 122          protocolFees0 += earnedProtocolFees0
```
 should use the appropriate calculation operation (`+`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L122](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L122)


- File: src/talos/TalosStrategyVanilla.sol
```
 
Line: 123          protocolFees1 += earnedProtocolFees1
```
 should use the appropriate calculation operation (`+`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L123](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L123)


- File: src/talos/TalosStrategyVanilla.sol
```
 
Line: 151          liquidity += liquidityDifference
```
 should use the appropriate calculation operation (`+`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L151](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L151)


- File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 52          totalWeights += _weight
```
 should use the appropriate calculation operation (`+`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L52](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L52)


- File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 68          totalWeights -= weights[assetIndex]
```
 should use the appropriate calculation operation (`-`), followed by an assignment operation (`=`)

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L68](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L68)



</details>

# 


## Avoid Unnecessary Computation Inside Loops
- Severity: Gas Optimization
- Confidence: High

### Description
This detector identifies instances of computations being performed inside loops that could be performed outside the loop to save gas.

Unnecessary computation inside loops:

Any computation performed inside a loop that doesn't change with each iteration can be moved outside the loop to save gas.This detector identifies instances where the following unnecessary computations are performed inside loops:

- 1 Local variables are read inside loops but never modified within the same loop, indicating they could be cached outside the loop.

- 2 Computations involving constant expressions (literals) which result in the same output in every loop iteration.

By addressing these issues, gas usage can be optimized.

<details>

<summary>
There are 1 instances of this issue:

</summary>

###


- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 282          leftOverBandwidth > 0
```
The following computations can be cached outside of loops for gas optimization `leftOverBandwidth > 0`<br>.
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L282](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L282)


</details>

# 
## Redundant Contract Existence Check in Consecutive External Calls
- Severity: Gas Optimization
- Confidence: Medium

### Description
This detector identifies instances where there are consecutive external calls to the same contract, where the subsequent calls could use a low-level call to save gas.

Note: This detector only triggers if the function call does not return any value. 

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 13 instances of this issue:

</summary>

###
- File: src/maia/tokens/ERC4626PartnerManager.sol
```
 
Line: 196          address(gaugeBoost).safeApprove(oldPartnerVault, 0)
```
This call could be replaced with a low-level call because the contract `SafeTransferLib` has already been checked in <br>`Line: 195    address(gaugeWeight).safeApprove(oldPartnerVault, 0)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L196](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L196)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 126          address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired)
```
This call could be replaced with a low-level call because the contract `SafeTransferLib` has already been checked in <br>`Line: 125    address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L126](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L126)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 197          address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired)
```
This call could be replaced with a low-level call because the contract `SafeTransferLib` has already been checked in <br>`Line: 196    address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L197](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L197)


- File: src/talos/base/TalosBaseStrategy.sol
```
 
Line: 407          uint256 balance1 = _token1.balanceOf(address(this))
```
This call could be replaced with a low-level call because the contract `ERC20` has already been checked in <br>`Line: 406    uint256 balance0 = _token0.balanceOf(address(this))`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L407](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L407)


- File: src/talos/boost-aggregator/BoostAggregator.sol
```
 
Line: 135          uniswapV3Staker.withdrawToken(tokenId, user, "")
```
This call could be replaced with a low-level call because the contract `UniswapV3Staker` has already been checked in <br>`Line: 114    uniswapV3Staker.unstakeToken(tokenId)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L135](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L135)


- File: src/talos/boost-aggregator/BoostAggregator.sol
```
 
Line: 168          address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)))
```
This call could be replaced with a low-level call because the contract `bHermesBoost` has already been checked in <br>`Line: 167    hermesGaugeBoost.decrementAllGaugesAllBoost()`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L168](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L168)


- File: src/talos/boost-aggregator/BoostAggregator.sol
```
 
Line: 175          hermesGaugeBoost.updateUserBoost(address(this))
```
This call could be replaced with a low-level call because the contract `bHermesBoost` has already been checked in <br>`Line: 174    hermesGaugeBoost.decrementAllGaugesBoost(amount)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L175](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L175)


- File: src/talos/TalosStrategyVanilla.sol
```
 
Line: 131          uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1
```
This call could be replaced with a low-level call because the contract `ERC20` has already been checked in <br>`Line: 130    uint256 balance0 = token0.balanceOf(address(this)) - protocolFees0`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L131](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L131)


- File: src/ulysses-amm/UlyssesRouter.sol
```
 
Line: 92          address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount)
```
This call could be replaced with a low-level call because the contract `UlyssesPool` has already been checked in <br>`Line: 74    address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L92](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L92)


- File: src/ulysses-amm/UlyssesRouter.sol
```
 
Line: 92          address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount)
```
This call could be replaced with a low-level call because the contract `SafeTransferLib` has already been checked in <br>`Line: 74    address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L92](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L92)


- File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
```
 
Line: 50          string memory symbol = ERC20(_underlyingAddress).symbol()
```
This call could be replaced with a low-level call because the contract `ERC20` has already been checked in <br>`Line: 49    string memory name = ERC20(_underlyingAddress).name()`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L50](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L50)


- File: src/ulysses-omnichain/CoreBranchRouter.sol
```
 
Line: 66          string memory symbol = ERC20(_underlyingAddress).symbol()
```
This call could be replaced with a low-level call because the contract `ERC20` has already been checked in <br>`Line: 65    string memory name = ERC20(_underlyingAddress).name()`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L66](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L66)



- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 1042          IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress)
```
This call could be replaced with a low-level call because the contract `IRootPort` has already been checked in <br>`Line: 1029    IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress)`<br>
[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1042](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1042)




</details>

# 


## Duplicated require()/revert() checks should be refactored to a modifier or function
- Severity: Gas Optimization
- Confidence: High

### Description
This detector checks for instances where the same require() or revert() condition is repeated in multiple places within the same contract. Such duplicate checks can often be refactored into a modifier or function, reducing code redundancy and increasing maintainability.

## Note: 
I reported issues that were overlooked by the winning bot. I reported only on instances that were missed.

<details>

<summary>
There are 5 instances of this issue:

</summary>

###
- File: src/erc-20/ERC20Boost.sol
```
 
Line: 191          require(_userGauges[msg.sender].remove(gauge))
```
Has duplicate:<br>File: src/erc-20/ERC20Boost.sol
```
 
Line: 213          require(_userGauges[msg.sender].remove(gauge))
```
File: src/erc-20/ERC20Boost.sol
```
 
Line: 239          require(_userGauges[msg.sender].remove(gauge))
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L191](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L191)


- File: src/erc-4626/ERC4626.sol
```
 
Line: 34          require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES")
```
Has duplicate:<br>File: src/erc-4626/ERC4626DepositOnly.sol
```
 
Line: 34          require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES")
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L34](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L34)



- File: src/ulysses-amm/UlyssesPool.sol
```
 
Line: 91          require(_id != 0)
```
Has duplicate:<br>File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 30          require(_id != 0)
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L91](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L91)





- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 1367          require(_unlocked == 1)
```
Has duplicate:<br>File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 424          require(_unlocked == 1)
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1367](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1367)


- File: src/ulysses-omnichain/RootPort.sol
```
 
Line: 129          require(_setup, "Setup ended.")
```
Has duplicate:<br>File: src/ulysses-omnichain/RootPort.sol
```
 
Line: 145          require(_setup, "Setup ended.")
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L129](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L129)


</details>

# 


## Cache External Calls in Loops
- Severity: Gas Optimization
- Confidence: High

### Description
This detector identifies instances of repeated external calls within loops. By moving the first external call outside of the loop and using low-level calls inside the loop, it is possible to save gas by avoiding repeated EXTCODESIZE opcodes, as there is no need to check again if the contract exists. 

Note: This detector only triggers if the function call does not return any value.

Prior to 0.8.10, the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent Solidity versions the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low-level calls never check for contract existence. 

<details>

<summary>
There are 26 instances of this issue:

</summary>

###
- File: src/erc-4626/ERC4626MultiToken.sol
```
 
Line: 51          require(ERC20(_assets[i]).decimals() == 18)
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L51](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L51)


- File: src/erc-4626/ERC4626MultiToken.sol
```
 
Line: 69          assets[i].safeTransferFrom(msg.sender, address(this), assetsAmounts[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L69](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L69)


- File: src/erc-4626/ERC4626MultiToken.sol
```
 
Line: 80          assets[i].safeTransfer(receiver, assetsAmounts[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L80](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L80)


- File: src/gauges/factories/BaseV2GaugeFactory.sol
```
 
Line: 78          _gauges[i].newEpoch()
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L78](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L78)


- File: src/gauges/factories/BaseV2GaugeFactory.sol
```
 
Line: 94          _gauges[i].newEpoch()
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L94](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L94)


- File: src/gauges/factories/BaseV2GaugeManager.sol
```
 
Line: 65          _gaugeFactories[i].newEpoch()
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L65](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L65)


- File: src/gauges/factories/BaseV2GaugeManager.sol
```
 
Line: 81          _gaugeFactories[i].newEpoch()
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L81](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L81)


- File: src/governance/GovernorBravoDelegateMaia.sol
```
 
Line: 252          timelock.cancelTransaction(

                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta

            )
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L252-L254](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L252-L254)



- File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 116          assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance)
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L116](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L116)


- File: src/ulysses-amm/UlyssesToken.sol
```
 
Line: 118          assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance)
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L118](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L118)


- File: src/ulysses-amm/factories/UlyssesFactory.sol
```
 
Line: 124          pools[poolIds[i]].transferOwnership(owner)
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L124](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L124)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 102          IRootPort(rootPortAddress).bridgeToLocalBranchFromRoot(_recipient, _localAddresses[i], _amounts[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L102](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L102)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 138          _underlyingAddresses[i].safeTransferFrom(

                    _depositor,

                    address(this),

                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

                )
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L138-L142](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L138-L142)


- File: src/ulysses-omnichain/ArbitrumBranchPort.sol
```
 
Line: 145          IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(

                    _depositor, _localAddresses[i], _amounts[i] - _deposits[i]

                )
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L145-L147](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L145-L147)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 284          _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals())
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L284](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L284)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 624          IPort(localPortAddress).bridgeIn(_recipient, _hTokens[i], _amounts[i] - _deposits[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L624](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L624)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 628          IPort(localPortAddress).withdraw(_recipient, _tokens[i], _deposits[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L628](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L628)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 720          deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals())
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L720](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L720)


- File: src/ulysses-omnichain/BranchBridgeAgent.sol
```
 
Line: 1355          deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals())
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1355](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1355)


- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 269          _underlyingAddresses[i].safeTransferFrom(

                    _depositor,

                    address(this),

                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

                )
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L269-L273](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L269-L273)


- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 276          _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L276](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L276)


- File: src/ulysses-omnichain/BranchPort.sol
```
 
Line: 277          ERC20hTokenBranch(_localAddresses[i]).burn(_amounts[i] - _deposits[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L277](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L277)


- File: src/ulysses-omnichain/MulticallRootRouter.sol
```
 
Line: 312          IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L312](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L312)


- File: src/ulysses-omnichain/MulticallRootRouter.sol
```
 
Line: 388          IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L388](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L388)


- File: src/ulysses-omnichain/MulticallRootRouter.sol
```
 
Line: 464          IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i])
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L464](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L464)


- File: src/ulysses-omnichain/RootBridgeAgent.sol
```
 
Line: 600          IPort(localPortAddress).bridgeToRoot(

                    msg.sender,

                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain),

                    settlement.amounts[i],

                    settlement.deposits[i],

                    settlement.toChain

                )
```

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L600-L606](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L600-L606)



</details>

# 