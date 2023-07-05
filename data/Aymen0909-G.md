# Gas Optimizations

## Summary

|       | Issue        | Instances    |
| :---: |:-------------|:------------:|
| 1  | State variables should be packed to save storage slots | 6 |
| 2  | Variables inside struct should be packed to save gas | 1 |
| 3  | Emit events outside the for loops | 3 |
| 4  | Saving an array elements count in a state variable is redundant  | 6 |
| 5  | Use `unchecked` blocks to save gas | 9 |
| 6 | `memory` values should be emitted in events instead of `storage` ones |  6 |
| 7  | Duplicated input check statements should be refactored to a modifier  |  6 |


## Findings

### 1- State variables should be packed to save storage slots :

State variables inside contract should be packed if possible to save storage slots and thus will save gas cost.

There are 6 instances of this issue:

* Instance 1 :

```
File: governance/GovernorBravoInterfaces.sol

79      uint256 public votingDelay;
82      uint256 public votingPeriod;
85      uint256 public proposalThreshold;
88      uint256 public initialProposalId;
```

As the variables `votingDelay`, `votingPeriod` have maximal values defined in GovernorBravoDelegateMaia they can't overflow 2^32.
The variable `proposalThreshold` has a maximal value of 5e16, it can't overflow 2^64.
The variable `initialProposalId` represent the initial proposal id set by the owner so it can't realistically overflow 2^32.


So those variables should be packed together, this will **save 3 storage slots** and thus saves a lot of gas, the code should be modified as follow :

```
/// @notice The number of votes required in order for a voter to become a proposer
uint64 public proposalThreshold;

/// @notice The delay before voting on a proposal may take place, once proposed, in blocks
uint32 public votingDelay;

/// @notice The duration of voting on a proposal, in blocks
uint32 public votingPeriod;

/// @notice Initial proposal id set at become
uint32 public initialProposalId;
```

* Instance 2 :

```
File: minters/BaseV2Minter.sol

42      uint256 public override daoShare = 100;
43      uint256 public override tailEmission = 20;
```

As the variables `daoShare` and `tailEmission` have maximal values `max_dao_share = 300` and `max_tail_emission = 100`, they can't overflow 2^32, so those variables should be packed with the address variable `dao` for example (which only takes 20 bytes), this will **save 2 storage slots** and thus saves a lot of gas, the code should be modified as follow :

```
/// @inheritdoc IBaseV2Minter
address public override dao;

/// @inheritdoc IBaseV2Minter
uint32 public override daoShare = 100;
uint32 public override tailEmission = 20;  
```

* Instance 3 :

```
File: maia/vMaia.sol

34      uint256 private currentMonth;
35      uint256 private unstakePeriodEnd;
```

As the variable `currentMonth` represents months count from 1 to 12 it can't overflow 2^32 and as the variable `unstakePeriodEnd` represents a timestamp it can't realistically overflow 2^224, so those variables can be packed together, this will **save 1 storage slots** and saves a lot of gas, the code should be modified as follow :

```
uint224 private unstakePeriodEnd; 
uint32 private currentMonth;
```

* Instance 4 :

```
File: maia/vMaia.sol

34      uint128 public override liquidity;
```

The variable `liquidity` should be packed with the variables `tickLower`, `tickUpper` and `initialized`, this will **save 1 storage slots**, the code should be modified as follow :

```
/// @inheritdoc ITalosBaseStrategy
uint128 public override liquidity;

/// @notice Current tick lower of Optimizer pool position
/// @inheritdoc ITalosBaseStrategy
int24 public override tickLower;
/// @notice Current tick higher of Optimizer pool position
/// @inheritdoc ITalosBaseStrategy
int24 public override tickUpper;

/// @inheritdoc ITalosBaseStrategy
bool public initialized;
```

* Instance 5 :

```
File: factories/UlyssesFactory.sol

49      uint256 public poolId = 1;
52      uint256 public tokenId = 1;
```

The variables `poolId` and `tokenId` represent the id number of the added pools and tokens, those variables can't realistically overflow 2^128, so they should be packed together, this will **save 1 storage slots**, the code should be modified as follow :

```
///@notice next poolId
uint128 public poolId = 1;

///@notice next tokenId
uint128 public tokenId = 1;
```

* Instance 6 :

```
File: factories/UlyssesFactory.sol

48      uint256 public totalWeights;
67      uint256 public protocolFee = 1e14;
```

Both variables `totalWeights` and `protocolFee` have maximal values `MAX_TOTAL_WEIGHT = 256` and `MAX_PROTOCOL_FEE = 1e16`, so they should be packed together, this will **save 1 storage slots**, the code should be modified as follow :

```
uint64 public protocolFee = 1e14;

/// @notice Sum of all weights
uint32 public totalWeights;
```

### 2- Variables inside `struct` should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

There is 1 instance of this issue:

**File: GovernorBravoInterfaces.sol** [Line 105-136](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136)

```
struct Proposal {
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

As the variables `startBlock`, `endBlock` and `eta` represents the number of passed blocks they can't realistically overflow 2^64. And the variables `canceled` and `executed` are of boolean type (take only one bit), so all those variables should be packed together inside the struct this will **save 3 storage slots** and thus saves a lot of gas, the struct should be modified as follow :

```
struct Proposal {
    /// @notice Unique id for looking up a proposal
    uint256 id;
    /// @notice Creator of the proposal
    address proposer;
    /// @notice the ordered list of target addresses for calls to be made
    address[] targets;
    /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
    uint256[] values;
    /// @notice The ordered list of function signatures to be called
    string[] signatures;
    /// @notice The ordered list of calldata to be passed to each call
    bytes[] calldatas;
    /// @notice Current number of votes in favor of this proposal
    uint256 forVotes;
    /// @notice Current number of votes in opposition to this proposal
    uint256 againstVotes;
    /// @notice Current number of votes for abstaining for this proposal
    uint256 abstainVotes;
    /// @notice The block at which voting begins: holders must delegate their votes prior to this block
    uint64 startBlock;
    /// @notice The block at which voting ends: votes must be cast prior to this block
    uint64 endBlock;
    /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
    uint64 eta;
    /// @notice Flag marking whether the proposal has been canceled
    bool canceled;
    /// @notice Flag marking whether the proposal has been executed
    bool executed;
    /// @notice Receipts of ballots for the entire set of voters
    mapping(address => Receipt) receipts;
}
```


### 3- Emit events outside the for loops :

Putting an event inside a for loop means that the event will be emitted at each iteration which will cost a lot of gas in the end, you should consider emitting a global event outside the for loop when possible.

There are 3 instances of this issue :

**File: ERC20Boost.sol** [Line 242](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L242)
```
emit Detach(msg.sender, gauge);
```

The event in the code linked above is emitted at each iteration which can be avoided by emitting a final event after the for loop containing all the gauges :

```
emit Detach(msg.sender, gaugeList);
```

**File: ERC4626MultiToken.sol** [Line 57](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L57)
```
emit AssetAdded(_assets[i], _weights[i]);
```

The event in the code linked above is emitted at each iteration which can be avoided by emitting a final event after the for loop containing all the assets and the correspanding weights as follows :

```
emit AssetsAdded(_assets, _weights);
```

**File: UlyssesToken.sol** [Line 98-99](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L98-L99)
```
emit AssetRemoved(assets[i]);
emit AssetAdded(assets[i], _weights[i]);
```

Both events in the code linked above are emitted at each iteration which can be avoided by emitting them after the for loop with the list of all assets and weights as follows :

```
emit AssetsRemoved(assets);
emit AssetsAdded(assets, _weights);
```


### 4- Saving an array elements count in a state variable is redundant :

There is no need to save the count of elements inside an array in a state variables as it can be obtained by `array.length`, this will just cost more gas when incrementing the variable. The only case when it is necessary to save the count of elements inside an array is when you will in the future delete some elements from the array (leaving them equal to default value: 0, address(0),...).

All the state variables mentioned below should be removed, this will a lot save of gas. There are 6 instances of this issue :

**File: ERC20hTokenBranchFactory.sol** [Line 25](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L25)
```
uint256 public hTokensLenght;
```

**File: ERC20hTokenRootFactory.sol** [Line 26](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L26)
```
uint256 public hTokensLenght;
```

**File: BranchPort.sol** [Line 33](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L33)
```
uint256 public bridgeAgentsLenght;
```

**File: BranchPort.sol** [Line 46](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L46)
```
uint256 public bridgeAgentFactoriesLenght;
```

**File: BranchPort.sol** [Line 60](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L60)
```
uint256 public strategyTokensLenght;
```

**File: BranchPort.sol** [Line 77](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L77)
```
uint256 public portStrategiesLenght;
```


### 6- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 9 instances of this issue:

**File: ERC20Boost.sol** [Line 183](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L183)
```
gaugeState.userGaugeBoost -= boost.toUint128();
```

In the code linked above the operation cannot underflow because of the check in line [177](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L177), so the operation should be marked as `unchecked` to save gas.

**File: ERC20Boost.sol** [Line 218](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L218)
```
gaugeState.userGaugeBoost -= boost.toUint128();
```

In the code linked above the operation cannot underflow because of the check in line [212](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L212), so the operation should be marked as `unchecked` to save gas.


**File: TalosBaseStrategy.sol** [Line 167](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L167)
```
uint256 refund0 = amount0Desired - amount0;
```

In the code linked above the operation cannot underflow because of the check in line [166](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166), so the operation should be marked as `unchecked` to save gas.

**File: TalosBaseStrategy.sol** [Line 172](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L172)
```
uint256 refund1 = amount1Desired - amount1;
```

In the code linked above the operation cannot underflow because of the check in line [171](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L171), so the operation should be marked as `unchecked` to save gas.

**File: TalosBaseStrategy.sol** [Line 227](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L227)
```
uint256 refund0 = amount0Desired - amount0;
```

In the code linked above the operation cannot underflow because of the check in line [226](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L226), so the operation should be marked as `unchecked` to save gas.

**File: TalosBaseStrategy.sol** [Line 232](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L232)
```
uint256 refund1 = amount1Desired - amount1;
```

In the code linked above the operation cannot underflow because of the check in line [231](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L231), so the operation should be marked as `unchecked` to save gas.

**File: TalosBaseStrategy.sol** [Line 412-413](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L412-L413)
```
protocolFees0 = _protocolFees0 - amount0;
protocolFees1 = _protocolFees1 - amount1;
```

In the code linked above the operation cannot underflow because of the checks lines [398-403](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L398-L403), so the operation should be marked as `unchecked` to save gas.

**File: BoostAggregator.sol** [Line 122](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L122)
```
pendingRewards -= newProtocolRewards;
```

In the code linked above the operation cannot underflow because of the operation line [119](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L119), so the operation should be marked as `unchecked` to save gas.

**File: UlyssesPool.sol** [Line 139](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L139)
```
return balance - assets;
```

In the code linked above the operation cannot underflow because of the check line [138](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L138), so the operation should be marked as `unchecked` to save gas.


### 6- `memory` values should be emitted in events instead of `storage` ones :

The values emitted in events shouldn’t be read from storage but the existing memory values should be used instead, this will save **~101 GAS**.

There are 6 instances of this issue :

**File: GovernorBravoDelegateMaia.sol** [Line 406](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L406)
```
emit VotingDelaySet(oldVotingDelay, votingDelay);
```

The memory value `newVotingDelay` should be emitted as follow :

```
emit VotingDelaySet(oldVotingDelay, newVotingDelay);
```

**File: GovernorBravoDelegateMaia.sol** [Line 422](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L422)
```
emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
```

The memory value `newVotingPeriod` should be emitted as follow :

```
emit VotingPeriodSet(oldVotingPeriod, newVotingPeriod);
```

**File: GovernorBravoDelegateMaia.sol** [Line 439](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L439)
```
emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold);
```

The memory value `newProposalThreshold` should be emitted as follow :

```
emit ProposalThresholdSet(oldProposalThreshold, newProposalThreshold);
```

**File: GovernorBravoDelegateMaia.sol** [Line 466](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L466)
```
emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);
```

The memory value `account` should be emitted as follow :

```
emit WhitelistGuardianSet(oldGuardian, account);
```

**File: GovernorBravoDelegateMaia.sol** [Line 521-522](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L521-L522)
```
emit NewAdmin(oldAdmin, admin);
emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
```

The memory values `pendingAdmin` and `address(0)` should be emitted as follow :

```
emit NewAdmin(oldAdmin, pendingAdmin);
emit NewPendingAdmin(oldPendingAdmin, address(0));
```

**File: GovernorBravoDelegator.sol** [Line 50](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L50)
```
emit NewImplementation(oldImplementation, implementation);
```

The memory value `pendingAdmin` should be emitted as follow :

```
emit NewImplementation(oldImplementation, implementation_);
```


### 7- Duplicated input check statements should be refactored to a modifier :

Input check statements used multiple times inside a contract should be refactored to a modifier for better readability and also to save gas.

There are 6 instances of this issue :

```
File: GovernorBravoDelegateMaia.sol

398     require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");
414     require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");
431     require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");
462     require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");
475     require(msg.sender == admin, "GovernorBravo::_initiate: admin only");
489     require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");
```

Those checks should be replaced by an `onlyAdmin` modifier as follow :

```
modifier onlyAdmin(){
    require(msg.sender == admin, "GovernorBravo: admin only");
    _;
}
```