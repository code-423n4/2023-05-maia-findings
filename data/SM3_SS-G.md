

# Summary  

## Gas Optimization    
no | Issue |Instances||
|-|:-|:-:|:-:|
| [G-01] |  A modifier used only once and not being inherited should be inlined to save gas | 7 |
| [G-02] |  Structs can be packed into fewer storage slots | 6 |
| [G-03] |  >= costs less gas than > | 59 |
| [G-04] |  Using > 0 costs more gas than != 0 when used on a uint in a require() statement | 2 |
| [G-05] |  Using private rather than public for constants, saves gas | 4 |
| [G-06] |  Avoid contract existence checks by using low level calls| 107 |
| [G-07] |  Do not calculate constants | 2 |
| [G-08] |  Using delete statement can save gas | 5 |
| [G-09] |  Use hardcode address instead address(this) | 57 |
| [G-10] |  String literals passed to abi.encode()/abi.encodePacked() should not be split by commas  | 3 |
| [G-11] |  Use constants instead of type(uintx).max | 37 |
| [G-12] |  internal functions not called by the contract should be removed to save deployment gas | 1 |
| [G-13] |  Amounts should be checked for 0 before calling a transfer  | 5 |
| [G-14] |  Uncheck arithmetics operations that can’t underflow/overflow | 1 |
| [G-15] |  Can Make The Variable Outside The Loop To Save Gas  | 27 |
| [G-16] |  Make 3 event parameters indexed when possible | 74 |
| [G-17] |  Assigning keccak operations to constant variables results in extra gas costs | 3 |
| [G-18] |  Using private rather than public for constants, saves gas | 10 |
| [G-19] |  State variables should be cached in stack variables rather than re-reading them from storage | 7 |
| [G-20] |  abi.encode() is less efficient than abi.encodePacked()  | 4 |
| [G-21] |  Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement | 6 |
| [G-22] |  Avoid emitting storage values | 7 |
| [G-23] |  Use assembly to perform efficient back-to-back calls |   |
| [G-24] |  Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or  | 11 |
| [G-25] |  Use assembly for math (add, sub, mul, div)  | 3  | 
| [G-26] |  Use Mappings Instead of Arrays | 5  | 
| [G-27] |  Don’t apply the same value to state variable  | 7  | 
| [G-28] |  Use do while loops instead of for loops  | 4  | 
| [G-29] |  Inverting the condition of an if-else-statement wastes gas | 13  | 


## [G-01] A modifier used only once and not being inherited should be inlined to save gas


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


## [G-02] Structs can be packed into fewer storage slots
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

## [G-03] >= costs less gas than >
The compiler uses opcodes for solidity code that uses >, but only requires for >=, which saves 3 gas



```solidity

file:  src/erc-4626/ERC4626MultiToken.sol

52     require(_weights[i] > 0);

252    if (share > shares) shares = share;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L252



```solidity

file:  src/erc-20/ERC20Gauges.sol

211 if (added && _userGauges[user].length() > maxGauges && !canContractExceedMaxGauges[user]) 
236 if (newUserWeight > getVotes(user)) revert OverWeightError();
417 if (weight > 0)
441 if (weight > 0) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L211

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L236

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L441




```solidity
file:  src/erc-20/ERC20MultiVotes.sol   

70     if (ckpts[mid].fromBlock > blockNumber)

165    if (count > 1) revert DelegationError();

194    if (newDelegate && delegateCount(delegator) > maxDelegates && !canContractExceedMaxDelegates[delegator])

250    if (pos > 0 && ckpts[pos - 1].fromBlock == block.number)

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L70

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L165

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L194

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250


```solidity

file:  src/gauges/factories/BaseV2GaugeFactory.sol

91     if (end > length) end = length;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L91


```solidity

file:      src/governance/GovernorBravoDelegateMaia.sol

114         require(
            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
298         require(
            proposalCount >= proposalId && proposalId > initialProposalId, "GovernorBravo::state: invalid proposal id"
        );    

390         return (whitelistAccountExpirations[account] > block.timestamp);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L114

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L298

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L390

```solidity

file:     src/talos/boost-aggregator/BoostAggregator.sol

93       if (_daoShare > max_dao_share) revert DaoShareTooHigh();

99       if (_tail_emission > max_tail_emission) revert TailEmissionTooHigh();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L93

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L99


```solidity

file:   src/maia/tokens/ERC4626PartnerManager.sol

219     if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply))

241     if (amount > maxMint(to)) revert ExceedsMaxDeposit();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L219

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L241


```solidity

file:  src/maia/PartnerUtilityManager.sol

75     if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0)

85     if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0)

95     if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0)

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95

```solidity
file:  src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

127    if (oldRewardBalance > 0) 

162    if (strategyRewardsAccrued > 0)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L162


```solidity

file:    src/rewards/rewards/FlywheelGaugeRewards.sol

120     if (currentCycle > nextCycle)

234     if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L120

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234

```solidity

file:

158    if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();

219    if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();

398    if (amount0 > _protocolFees0)

401    if (amount1 > _protocolFees1)

409    if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410    if (amount1 > 0) _token1.transfer(msg.sender, amount1);


```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L158

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L219

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L398

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L401

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410


```solidity

file:   src/talos/factories/TalosBaseStrategyFactory.sol

138     if (balance > assets)

153     if (claimed > 0)

171     if (index > MAX_DESTINATIONS) revert TooManyDestinations();

185     if (newTotalWeights > MAX_TOTAL_WEIGHT) revert InvalidWeight();

191     if (oldBandwidth > 0)

243     if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights)

252     if (oldTotalWeights > newTotalWeights)

256     if (oldBandwidth > 0)

272     if (oldBandwidth > 0)

310     if (_fees.lambda1 > MAX_LAMBDA1) revert InvalidFee();

315     if (_fees.sigma1 > DIVISIONER) revert InvalidFee();

327     if (_protocolFee > MAX_PROTOCOL_FEE) revert InvalidFee();

911     if (updateAmount > 0)

1048    if (updateAmount > 0) 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L138

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L153

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L171

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L185

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L191

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L243

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L252

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L256

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L272

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L310

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L315

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L327

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L911

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L1048


```solidity

file: src/talos/TalosOptimizer.sol

47    require(_weight > 0);

115   if (assetBalance > newAssetBalance)

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L47

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L115


```solidity

file:

474     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

499     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

521     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

543     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}(); 

623     if (_amounts[i] - _deposits[i] > 0)

627     if (_deposits[i] > 0)

979     if (_amount - _deposit > 0)

983     if (_deposit > 0) 

1035    if (minExecCost > gasRemaining) 

1050    if (gasLeft - gasAfterTransfer > TRANSFER_OVERHEAD) 

1069    if (minExecCost > getDeposit[_depositNonce].depositedGas)

1328    if (executionBudget > 0) try anycallConfig.withdraw(executionBudget)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L521
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L543
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L623
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L979
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L983
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1035
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1050
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1069
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328


## [G-04] Using > 0 costs more gas than != 0 when used on a uint in a require() statement
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

## [G-05] Using private rather than public for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

```solidity

file:     src/erc-20/ERC20MultiVotes.sol

360         bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity

  
file:    src/governance/GovernorBravoDelegateMaia.sol 

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

file:   src/ulysses-omnichain/lib/AnycallFlags.sol

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

## [G‑06] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

```solidity
File: /src/rewards/FlywheelCoreInstant.sol
41    return IFlywheelInstantRewards(flywheelRewards).getAccruedRewards();
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreInstant.sol#L41


```solidity
File: /src/rewards/FlywheelCoreStrategy.sol
40   return IFlywheelAcummulatedRewards(flywheelRewards).getAccruedRewards(strategy);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreStrategy.sol#L40


```solidity
File: /src/talos/TalosStrategyStaked.sol
82    _boostAggregator.setOwnRewardsDepot(address(FlywheelInstantRewards(_flywheel.flywheelRewards()).rewardsDepot()));
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L82

```solidity
File: /src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol
104   IPort(localPortAddress).addBridgeAgent(newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L104

```solidity
File: /src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
99    IPort(localPortAddress).addBridgeAgent(newCoreBridgeAgent);

139   IPort(localPortAddress).addBridgeAgent(newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L99

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L139


```solidity
File: /src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
62    localAnyCallExecutorAddress = IAnycallProxy(localAnyCallAddress).executor();

88    IRootPort(rootPortAddress).addBridgeAgent(msg.sender, newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L62

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L88



```solidity
File: /src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
103   IArbPort(localPortAddress).depositToPort(
            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
        );

115   IArbPort(localPortAddress).withdrawFromPort(msg.sender, msg.sender, localAddress, amount);

143   IRootBridgeAgent(rootBridgeAgentAddress).anyExecute(_callData);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L103

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L143

```solidity
File: /src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
104   BranchBridgeAgent(payable(msg.sender)).clearToken(
            sParams.recipient, sParams.hToken, sParams.token, sParams.amount, sParams.deposit
        );

110    (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);

147   (success, result) = IRouter(_router).anyExecuteSettlementMultiple(    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L104

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L110

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L147

```solidity
File: /src/ulysses-omnichain/BranchPort.sol
127   uint256 currBalance = ERC20(_token).balanceOf(address(this));

138   uint256 currBalance = ERC20(_token).balanceOf(address(this));

180   IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

212    _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

222   ERC20hTokenBranch(_localAddress).mint(_recipient, _amount);

232    ERC20hTokenBranch(_localAddresses[i]).mint(_recipient, _amounts[i]);

250   ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);

254  _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

272  _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L138

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L180

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L212

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L222

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L232

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L250

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L254

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L272



```solidity
File: /src/ulysses-omnichain/CoreBranchRouter.sol
54   IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(
            msg.sender, packedData, 0, _remoteExecutionGas
        );

65    string memory name = ERC20(_underlyingAddress).name();

66    string memory symbol = ERC20(_underlyingAddress).symbol();   

69    ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(name, symbol);

78     IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(msg.sender, packedData, 0, 0);

102    ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(_name, _symbol);

111    IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _rootExecutionGas, 0);

133    if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {

138     address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(
            _newBranchRouter, _rootBridgeAgent, _rootBridgeAgentFactory
        );

143     if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {
            revert UnrecognizedBridgeAgent();
        }

154     IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _remoteExecutionGas, 0);

164   if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {

165   IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);

167   IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);

178     if (!IPort(localPortAddress).isBridgeAgent(_branchBridgeAgent)) revert UnrecognizedBridgeAgent();

179   IPort(localPortAddress).toggleBridgeAgent(_branchBridgeAgent);

190   if (!IPort(localPortAddress).isStrategyToken(_underlyingToken)) {

191   IPort(localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio);

193   IPort(localPortAddress).toggleStrategyToken(_underlyingToken);

212   if (!IPort(localPortAddress).isPortStrategy(_portStrategy, _underlyingToken)) {

214   IPort(localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

217    IPort(localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

220   IPort(localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L54

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L65

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L66

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L69

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L78

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L102

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L111

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L133

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L143

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L154

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L164

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#167

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L178

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L179

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L190

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L191

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L193

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L214

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L217

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L220

```solidity
File: /src/ulysses-omnichain/CoreRootRouter.sol
65    bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

90    if (msg.sender != IPort(rootPortAddress).getBridgeAgentManager(_rootBridgeAgent)) {
            revert UnauthorizedCallerNotManager();
        }

95    if (!IPort(rootPortAddress).isChainId(_toChain)) revert InvalidChainId();

98     if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_toChain) != address(0)) revert InvalidChainId();

101   if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_toChain)) revert UnauthorizedChainId();

104   address rootBridgeAgentFactory = IBridgeAgent(_rootBridgeAgent).factoryAddress();

115   IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

128     IPort(rootPortAddress).syncBranchBridgeAgentWithRoot(_newBranchBridgeAgent, _rootBridgeAgent, _fromChain);
    }

148   if (!IPort(rootPortAddress).isGlobalAddress(_globalAddress)) {

153   if (IPort(rootPortAddress).isGlobalToken(_globalAddress, _toChain)) {

159    _globalAddress, ERC20(_globalAddress).name(), ERC20(_globalAddress).symbol(), _remoteExecutionGas

166   IBridgeAgent(bridgeAgentAddress).callOut(_gasReceiver, packedData, _toChain);

187    IPort(rootPortAddress).isGlobalAddress(_underlyingAddress)

188      || IPort(rootPortAddress).isLocalToken(_underlyingAddress, _fromChain)

189      || IPort(rootPortAddress).isUnderlyingToken(_underlyingAddress, _fromChain)

193    address newToken = address(IFactory(hTokenFactoryAddress).createToken(_name, _symbol));

196    IPort(rootPortAddress).setAddresses(
            newToken, (_fromChain == rootChainId) ? newToken : _localAddress, _underlyingAddress, _fromChain
        );

210    if (IPort(rootPortAddress).isLocalToken(_localAddress, _toChain)) revert TokenAlreadyAdded();

213    IPort(rootPortAddress).setLocalAddress(_globalAddress, _localAddress, _toChain);

233    if (!IPort(rootPortAddress).isBridgeAgentFactory(_rootBridgeAgentFactory)) {

244    IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

265      IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

288     IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);


315     IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L65

```solidity
File: /src/ulysses-omnichain/MulticallRootRouter.sol
78     bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

96    (blockNumber, returnData) = IMulticall(multicallAddress).aggregate(calls);

120   ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

123   IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(
            owner, recipient, "", outputToken, amountOut, depositOut, toChain
        );

155   IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(
            owner, recipient, "", outputTokens, amountsOut, depositsOut, toChain
        );   
        
281   IVirtualAccount(userAccount).call(calls);

289   IVirtualAccount(userAccount).call(calls);

292    IVirtualAccount(userAccount).withdrawERC20(outputParams.outputToken, outputParams.amountOut);

295   IVirtualAccount(userAccount).userAddress(),

309   IVirtualAccount(userAccount).call(calls);

312     IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

320   IVirtualAccount(userAccount).userAddress(),

388   IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

396   IVirtualAccount(userAccount).userAddress(),

444   IVirtualAccount(userAccount).withdrawERC20(outputParams.outputToken, outputParams.amountOut);

464    IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L78

```solidity
File: /src/ulysses-omnichain/RootBridgeAgentExecutor.sol
84   (success, result) = IRouter(_router).anyExecuteResponse(
            bytes1(_data[PARAMS_TKN_START]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId
        );
105    IRouter(_router).anyExecute(bytes1(_data[5]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId);

137   (success, result) = IRouter(_router).anyExecuteDepositSingle(
                _data[112], _data[113:_data.length - PARAMS_GAS_IN], dParams, _fromChainId
            );

177   (success, result) = IRouter(_router).anyExecuteDepositMultiple(
                bytes1(_data[PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE]),
                _data[
                    PARAMS_START + PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE:
                        length - PARAMS_GAS_IN
                ],
                dParams,
                _fromChainId
            );
208        IRouter(_router).anyExecuteSigned(_data[25], _data[26:_data.length - PARAMS_GAS_IN], _account, _fromChainId);

241   (success, result) = IRouter(_router).anyExecuteSignedDepositSingle(
                _data[132], _data[133:_data.length - PARAMS_GAS_IN], dParams, _account, _fromChainId
            );

283   (success, result) = IRouter(_router).anyExecuteSignedDepositMultiple(
                    _data[PARAMS_END_SIGNED_OFFSET
                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE],
                    _data[
                        PARAMS_START + PARAMS_END_SIGNED_OFFSET
                            + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE:
                            _data.length - PARAMS_GAS_IN
                    ],
                    dParams,
                    _account,
                    _fromChainId
                );

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L84


```solidity
File: /src/ulysses-omnichain/RootPort.sol
153   IBridgeAgent(_coreRootBridgeAgent).syncBranchBridgeAgent(_coreLocalBranchBridgeAgent, localChainId);

295   ERC20hTokenRoot(_hToken).mint(_to, _amount, _fromChain);

301   ERC20hTokenRoot(_hToken).burn(_from, _amount, _fromChain);

332   ERC20hTokenRoot(_hToken).burn(_from, _amount, localChainId);

383   if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_branchChainId) != address(0)) {

386   if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_branchChainId)) {

389    IBridgeAgent(_rootBridgeAgent).syncBranchBridgeAgent(_newBranchBridgeAgent, _branchChainId);

435    IERC20hTokenRootFactory(ICoreRootRouter(coreRootRouterAddress).hTokenFactoryAddress()).createToken(
                _wrappedGasTokenName, _wrappedGasTokenSymbol
            )
440   ERC20hTokenRoot(newGlobalToken).mint(_pledger, _pledgedInitialAmount, _chainId);

442   IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(
            _coreBranchBridgeAgentAddress, _chainId
        );

465    newGasPoolAddress = INonfungiblePositionManager(_nonFungiblePositionManagerAddress)
                .createAndInitializePoolIfNecessary(newGlobalToken, wrappedNativeTokenAddress, _fee, _sqrtPriceX96);
469    newGasPoolAddress = INonfungiblePositionManager(_nonFungiblePositionManagerAddress)
                .createAndInitializePoolIfNecessary(wrappedNativeTokenAddress, newGlobalToken, _fee, _sqrtPriceX96);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L153


```solidity
File: /src/ulysses-omnichain/VirtualAccount.sol
37   ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);

70   if ((!IRootPort(localPortAddress).isRouterApproved(this, msg.sender)) && (msg.sender != userAddress)) {
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L37

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L70

## [G-07] Do not calculate constants

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


## [G-08] Using delete statement can save gas


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
file:    src/rewards/base/FlywheelCore.sol

98       rewardsAccrued[user] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L98

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


## [G-09] Use hardcode address instead address(this)

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


## [G-10] String literals passed to abi.encode()/abi.encodePacked() should not be split by commas  

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


## [G-11] Use constants instead of type(uintx).max

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


## [G-12] internal functions not called by the contract should be removed to save deployment gas

When deploying a smart contract, it is essential to optimize gas usage to minimize transaction costs and improve overall efficiency. One aspect to consider is removing unused internal functions that are not called by the contract. By eliminating these unused functions, you can significantly reduce the deployment gas cost.

Internal functions in Solidity are only intended to be invoked within the contract or by other internal functions. If an internal function is not called anywhere within the contract, it serves no purpose and contributes unnecessary overhead during deployment. Removing such functions can lead to substantial gas savings.

```solidity

file:   src/rewards/depots/RewardsDepot.sol

19     function transferRewards(address _asset, address _rewardsContract) internal returns (uint256 balance)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L19


## [G-13] Amounts should be checked for 0 before calling a transfer

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



## [G-14] Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block


The average function you provided calculates the average of two uint256 values, a and b. However, the expression (a + b) / 2 in the comment suggests that you are concerned about the possibility of overflow.

In this case, you can apply the gas-saving technique using the unchecked block. Since the compiler cannot automatically determine that overflow is not possible in this expression, using unchecked can help optimize gas consumption while ensuring the validity of the operation.

```solidity
file:

80   function average(uint256 a, uint256 b) internal pure returns (uint256) {
        // (a + b) / 2 can overflow.
        return (a & b) + (a ^ b) / 2;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L80-L83


Here's an recommanded code of the average function with the unchecked block:

```solidity
 
 function average(uint256 a, uint256 b) internal pure returns (uint256) {
    unchecked {
        return (a & b) + (a ^ b) / 2;
    }
}

```

By wrapping the expression (a & b) + (a ^ b) / 2 with the unchecked block, you inform the compiler to skip the overflow check. However, make sure to thoroughly validate and test your code to ensure that the operation won't result in overflow in all possible scenarios.


## [G-15] Can Make The Variable Outside The Loop To Save Gas 
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
File:  src/erc-20/ERC20Boost.sol
157   address gauge = gaugeList[i];

208   address gauge = gaugeList[offset + i];

237   address gauge = gaugeList[i];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L157

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L208

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L237


```solidity
File: src/erc-20/ERC20Gauges.sol
260  address gauge = gaugeList[i];

261  uint112 weight = weights[i];

339  address gauge = gaugeList[i];

340  uint112 weight = weights[i];

537  address gauge = gaugeList[i];

538  uint112 userGaugeWeight = getUserGaugeWeight[user][gauge];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L260

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L261

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L339

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L340

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L537

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L538


```solidity
File:  src/erc-20/ERC20MultiVotes.sol
329   address delegatee = delegateList[i];

330   uint256 delegateVotes = _delegatesVotesCount[user][delegatee];
           
332   uint256 votesToFree = FixedPointMathLib.min(delegateVotes, userUnusedVotes(delegatee));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L329

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L330

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L332

```solidity
File:  src/erc-4626/ERC4626MultiToken.sol
202   uint256 share = assetsAmounts[i].mulDiv(_totalWeights, weights[i]);

251   uint256 share = assetsAmounts[i].mulDivUp(_totalWeights, weights[i]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L202

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L251

```solidity
File:  src/ulysses-amm/UlyssesPool.sol
131   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

176   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

190   uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

212   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

233   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

255   uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

297   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

908   uint256 updateAmount = bandwidthUpdateAmounts[i];

963   uint256 updateAmount = bandwidthUpdateAmounts[i];

1045   uint256 updateAmount = bandwidthUpdateAmounts[i];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L131

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L176

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L190

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L212

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L233

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L255

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L297

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L908

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L963

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1045


```solidity
File:  src/ulysses-amm/UlyssesToken.sol
112   uint256 assetBalance = assets[i].balanceOf(address(this));

113   uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L113

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol
146    address destination = address(pools[poolIds[i]]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L146



## [G-16] Make 3 event parameters indexed when possible
It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

Reffrecnce past c4 report  :   [Reffrecnce](https://github.com/code-423n4/2023-01-drips/blob/main/src/DripsHub.sol#L93)

```solidity
File:  src/erc-20/interfaces/IERC20Boost.sol

222   event UpdateUserBoost(address indexed user, uint256 updatedBoost);

225   event DecrementUserGaugeBoost(address indexed user, address indexed gauge, uint256 UpdatedBoost);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Boost.sol#L222

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Boost.sol#L225

```solidity
File:  src/erc-20/interfaces/IERC20Gauges.sol

246    event IncrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);

249    event DecrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);

258    event MaxGaugesUpdate(uint256 oldMaxGauges, uint256 newMaxGauges);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L246

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L249

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L258

```solidity
File: src/erc-20/interfaces/IERC20MultiVotes.sol
147    event MaxDelegatesUpdate(uint256 oldMaxDelegates, uint256 newMaxDelegates);

153    event Delegation(address indexed delegator, address indexed delegate, uint256 amount);

159    event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L147

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L153

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L159

```solidity
File:  src/erc-4626/interfaces/IERC4626.sol
93    event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626.sol#L93

```solidity
File:  src/erc-4626/interfaces/IERC4626DepositOnly.sol
64    event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L64

```solidity
File:  src/erc-4626/interfaces/IERC4626MultiToken.sol
159    event Deposit(address indexed caller, address indexed owner, uint256[] assets, uint256 shares);

178    event AssetAdded(address asset, uint256 weight);

184    event AssetRemoved(address asset);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L159

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L178

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L184

```solidity
File:  src/erc-4626/interfaces/IUlyssesERC4626.sol

86   event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L86

```solidity
File:  src/gauges/interfaces/IBaseV2GaugeManager.sol
111    event AddedGaugeFactory(address gaugeFactory);

114    event RemovedGaugeFactory(address gaugeFactory);

117    event ChangedbHermesGaugeOwner(address newOwner);

120    event ChangedAdmin(address newAdmin);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L111

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L114

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L117

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L120

```solidity
File: src/governance/GovernorBravoInterfaces.sol
6     event ProposalCreated(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        string description
    );
24    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

27    event ProposalCanceled(uint256 id);

30    event ProposalQueued(uint256 id, uint256 eta);

33    event ProposalExecuted(uint256 id);

36    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);

39    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);

42    event NewImplementation(address oldImplementation, address newImplementation);

45    event ProposalThresholdSet(uint256 oldProposalThreshold, uint256 newProposalThreshold);

48    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);

51    event NewAdmin(address oldAdmin, address newAdmin);

54    event WhitelistAccountExpirationSet(address account, uint256 expiration);

57    event WhitelistGuardianSet(address oldGuardian, address newGuardian);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L6

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L24

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L27

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L30

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L33

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L36

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L39

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L42

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L45

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L48

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L51

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L54

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L57

```solidity
File:  src/hermes/interfaces/IBaseV2Minter.sol
109    event Mint(address indexed sender, uint256 weekly, uint256 circulatingSupply, uint256 growth, uint256 dao_share);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IBaseV2Minter.sol#L109

```solidity
File:  src/hermes/interfaces/IUtilityManager.sol
82    event ForfeitWeight(address indexed user, uint256 amount);

85    event ForfeitBoost(address indexed user, uint256 amount);

88    event ForfeitGovernance(address indexed user, uint256 amount);

91    event ClaimWeight(address indexed user, uint256 amount);

94    event ClaimBoost(address indexed user, uint256 amount);

97    event ClaimGovernance(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L82

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L85

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L88

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L91

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L94

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L97

```solidity
File:  src/maia/interfaces/IERC4626PartnerManager.sol
78    event AccrueRewards(address indexed user, uint256 rewardsDelta, uint256 rewardsIndex);

85    event ClaimRewards(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/interfaces/IERC4626PartnerManager.sol#L78

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/interfaces/IERC4626PartnerManager.sol#L85

```solidity
File:  src/rewards/interfaces/IFlywheelAcummulatedRewards.sol
42    event NewRewardsCycle(uint32 indexed start, uint256 indexed end, uint256 reward);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L42

```solidity
File:  src/rewards/interfaces/IFlywheelCore.sol
121    event AccrueRewards(ERC20 indexed strategy, address indexed user, uint256 rewardsDelta, uint256 rewardsIndex);

128    event ClaimRewards(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelCore.sol#L121

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelCore.sol#L128

```solidity
File:  src/rewards/interfaces/IFlywheelGaugeRewards.sol
93    event CycleStart(uint32 indexed cycleStart, uint256 rewardAmount);

96    event QueueRewards(address indexed gauge, uint32 indexed cycleStart, uint256 rewardAmount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L93

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L96

```solidity
File:  src/talos/TalosStrategyVanilla.sol

165    event CollectFees(uint256 feesFromPool0, uint256 feesFromPool1, uint256 usersFees0, uint256 usersFees1);

170    event CompoundFees(uint256 amount0, uint256 amount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L165

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L170

```solidity
File:  src/talos/interfaces/ITalosBaseStrategy.sol
187    event RewardPaid(address indexed sender, uint256 fees0, uint256 fees1);

235    event Rerange(uint256 indexed tokenId, int24 tickLower, int24 tickUpper, uint256 amount0, uint256 amount1);

240    event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L187

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L235

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L240


```solidity
File:  src/talos/libraries/PoolActions.sol

23     event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L23

```solidity
File:  src/talos/libraries/PoolVariables.sol

28    event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L28

```solidity
File:  src/ulysses-amm/interfaces/IUlyssesPool.sol
237    event Swap(address indexed caller, uint256 indexed poolId, uint256 assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L237

```solidity
File:  src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
281    event LogCallin(bytes1 selector, bytes data, uint256 fromChainId);

282    event LogCallout(bytes1 selector, bytes data, uint256, uint256 toChainId);

293    event LogCalloutFail(bytes1 selector, bytes data, uint256 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L281

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L282

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L293

```solidity
File:  src/ulysses-omnichain/interfaces/IBranchPort.sol
198    event DebtCreated(address indexed _strategy, address indexed _token, uint256 _amount);

199    event DebtRepaid(address indexed _strategy, address indexed _token, uint256 _amount);

201    event StrategyTokenAdded(address indexed _token, uint256 _minimumReservesRatio);

202    event StrategyTokenToggled(address indexed _token);


204    event PortStrategyAdded(address indexed _portStrategy, address indexed _token, uint256 _dailyManagementLimit);

205    event PortStrategyToggled(address indexed _portStrategy, address indexed _token);

206    event PortStrategyUpdated(address indexed _portStrategy, address indexed _token, uint256 _dailyManagementLimit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L198

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L199

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L201

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L202

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L204

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L205

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L206

```solidity
File:  src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
378    event LogCallin(bytes1 selector, bytes data, uint24 fromChainId);

379    event LogCallout(bytes1 selector, bytes data, uint256, uint24 toChainId);

380    event LogCalloutFail(bytes1 selector, bytes data, uint24 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L378

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L379

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L380


```solidity
File:  src/ulysses-omnichain/interfaces/IRootPort.sol
315    event BridgeAgentAdded(address indexed bridgeAgent, address manager);

322    event VirtualAccountCreated(address indexed user, address account);

324    event LocalTokenAdded(
        address indexed underlyingAddress, address localAddress, address globalAddress, uint24 chainId
    );
327    event GlobalTokenAdded(address indexed localAddress, address indexed globalAddress, uint24 chainId);    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L315

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L322

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L324

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L327

```solidity
File:  src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
264    event IncentiveCreated(IUniswapV3Pool indexed pool, uint256 startTime, uint256 reward);

269    event IncentiveEnded(bytes32 indexed incentiveId, uint256 refund);

281    event TokenStaked(uint256 indexed tokenId, bytes32 indexed incentiveId, uint128 liquidity);

291    event RewardClaimed(address indexed to, uint256 reward);

296    event BribeDepotUpdated(IUniswapV3Pool indexed uniswapV3Pool, address bribeDepot);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L264

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L269

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L281

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L291

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L296


## [G-17] Assigning keccak operations to constant variables results in extra gas costs

"constants" expressions are expressions. As such, keccak assigned to a constant variable are re-computed at each use of the variable, which will consume gas unnecessarily. To save gas, Changing the variable from constant to immutable will make the computation run only once and therefore save gas.

```solidity
file:   src/erc-20/ERC20MultiVotes.sol

360     bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity
file:

42    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

46    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-L43

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46

### Recommended Mitigation Steps

Change the variable from constant to immutable



## [G-18] Using private rather than public for constants, saves gas
When you declare a constant variable as public, Solidity generates a getter function that allows anyone to read the value of the constant. This getter function can consume gas, especially if the constant is read frequently or the contract is called by multiple users.

By using private instead of public for constants, you can prevent the generation of the getter function and reduce the overall gas cost of your contract.


```solidity  
File:  src/governance/GovernorBravoDelegateMaia.sol 
9    string public constant name = "vMaia Governor Bravo";

12   uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

15   uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

18   uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

21   uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

24   uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

27   uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

30   uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

33   uint256 public constant proposalMaxOperations = 10; // 10 actions

36   uint256 public constant DIVISIONER = 1 ether;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L12

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L15

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L18

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L21

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L24

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L27

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L30

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L33

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L36



## [G-19] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.
```solidity
File:  src/governance/GovernorBravoDelegateMaia.sol
234    if (msg.sender != proposal.proposer && msg.sender != admin) {

236    if (isWhitelisted(proposal.proposer)) {

238    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())

244     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L234-L244



```solidity
File:   src/ulysses-omnichain/ArbitrumBranchPort.sol
62    if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {

66    address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);

70    IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L62-L70

```solidity
File:    src/ulysses-omnichain/CoreBranchRouter.sol
        if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {
            IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);
        } else {
            IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);
        }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L164-L168


## [G-20] abi.encode() is less efficient than abi.encodePacked()
In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.

```solidity
File:   src/governance/GovernorBravoDelegateMaia.sol
346   keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
File:  src/talos/libraries/PoolActions.sol
51   abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L51

```solidity
File:   src/ulysses-omnichain/RootBridgeAgent.sol
689   abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

733   abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L733

## [G‑21] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }.

```solidity
File:   src/governance/GovernorBravoDelegateMaia.sol
532    require(b <= a, "subtraction underflow");
        return a - b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L532-L533

```solidity
File:   src/hermes/minters/BaseV2Minter.sol
140   HERMES(underlying).mint(address(this), _required - _balanceOf);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140

```solidity
File:   src/talos/base/TalosBaseStrategy.sol
166   if (amount0 < amount0Desired) {
            uint256 refund0 = amount0Desired - amount0;
            address(_token0).safeTransfer(msg.sender, refund0);
        }

171    if (amount1 < amount1Desired) {
            uint256 refund1 = amount1Desired - amount1;
            address(_token1).safeTransfer(msg.sender, refund1);
        }
226    if (amount0 < amount0Desired) {
            uint256 refund0 = amount0Desired - amount0;
            address(_token0).safeTransfer(msg.sender, refund0);
        }

231   if (amount1 < amount1Desired) {
            uint256 refund1 = amount1Desired - amount1;
            address(_token1).safeTransfer(msg.sender, refund1);
        }  
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166-L169

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L171-L174

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L226-L229

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L231-L234


## [G-22] Avoid emitting storage values

Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. We can avoid unecessary SLOADs by caching storage values that were previously accessed and emitting those cached values.


```solidity
file:     src/erc-20/ERC20Boost.sol

185       emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);

220       emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L185

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L220

```solidity
file:     src/governance/GovernorBravoDelegateMaia.sol

164       emit ProposalCreated(
            newProposal.id, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock, description
        );
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L164-L167

```solidity
file:  src/rewards/depots/MultiRewardsDepot.sol

60     emit AssetRemoved(rewardsContract, _assets[rewardsContract]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L60

```solidity
file:   src/talos/base/TalosBaseStrategy.sol

160     emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares);

305     emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

318     emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#160

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#305

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#318

## [G-23] Use assembly to perform efficient back-to-back calls

If a similar external call is performed back-to-back, we can use assembly to reuse any function signatures and function parameters that stay the same. In addition, we can also reuse the same memory space for each function call (scratch space + free memory pointer + zero slot), which can potentially allow us to avoid memory expansion costs.

Note: In order to do this optimization safely we will cache the free memory pointer value and restore it once we are done with our function calls. We will also set the zero slot back to 0 if neccessary.

Total Instances: 4

the queuedTransactions function used twise

```solidity
file:   src/governance/GovernorBravoDelegateMaia.sol

190     function queueOrRevertInternal(
        address target,
        uint256 value,
        string memory signature,
        bytes memory data,
        uint256 eta
    ) internal {
        require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
        );
        timelock.queueTransaction(target, value, signature, data, eta);
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L190-L202


the balanceOf

```solidity
file:    src/talos/libraries/PoolActions.sol

90          function getThisPositionTicks(
        IUniswapV3Pool pool,
        ERC20 token0,
        ERC20 token1,
        int24 baseThreshold,
        int24 tickSpacing
    ) private view returns (uint256 balance0, uint256 balance1, int24 tickLower, int24 tickUpper) {
        // Emit snapshot to record balances
        balance0 = token0.balanceOf(address(this));
        balance1 = token1.balanceOf(address(this));

        //Get exact ticks depending on Optimizer's balances
        (tickLower, tickUpper) = pool.getPositionTicks(balance0, balance1, baseThreshold, tickSpacing);
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L90-L103

```solidity
file:   src/talos/libraries/PoolVariables.sol

217     function getSwapToEqualAmountsParams(
        IUniswapV3Pool _pool,
        ITalosOptimizer _strategy,
        int24 _tickSpacing,
        int24 baseThreshold,
        ERC20 _token0,
        ERC20 _token1
    ) internal returns (bool zeroForOne, int256 amountSpecified, uint160 sqrtPriceLimitX96) {
        PoolVariables.Info memory cache;

        cache.amount0Desired = _token0.balanceOf(address(this));
        cache.amount1Desired = _token1.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L217-L228
```solidity
file:   src/talos/TalosStrategyVanilla.sol

129     function _compoundFees(uint256 _tokenId) internal returns (uint256 amount0, uint256 amount1) {
        uint256 balance0 = token0.balanceOf(address(this)) - protocolFees0;
        uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L129-L131



## [G‑24] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or 


```solidity
File:   src/erc-4626/ERC4626.sol
67    if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

84    if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L67

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L84

```solidity
File:   src/erc-4626/ERC4626MultiToken.sol
143    if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

165    if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L143

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L165

```solidity
File:   src/erc-4626/UlyssesERC4626.sol
68    if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L68



```solidity
File:   src/hermes/minters/BaseV2Minter.sol
140   HERMES(underlying).mint(address(this), _required - _balanceOf);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140

```solidity
File:   src/maia/PartnerUtilityManager.sol
131   IBaseVault(partnerVault).clearWeight(amount - weightAvailable);

141   if (boostAvailable < amount) IBaseVault(partnerVault).clearBoost(amount - boostAvailable);

151   IBaseVault(partnerVault).clearGovernance(amount - governanceAvailable);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L131

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L141

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L151

```solidity
File:  src/talos/base/TalosBaseStrategy.sol
412     protocolFees0 = _protocolFees0 - amount0;
413     protocolFees1 = _protocolFees1 - amount1;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L412

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L413

## [G-025] Use assembly for math (add, sub, mul, div)
Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety.

Addition:
```
//addition in Solidity
function addTest(uint256 a, uint256 b) public pure {
    uint256 c = a + b;
}
```
Gas: 303
```
//addition in assembly
function addAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := add(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263
```
Subtraction
```
//subtraction in Solidity
function subTest(uint256 a, uint256 b) public pure {
  uint256 c = a - b;
}
```
Gas: 300
```
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
```
Multiplication
```
//multiplication in Solidity
function mulTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325
```
//multiplication in assembly
function mulAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := mul(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265

Division
```
//division in Solidity
function divTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325

```
//division in assembly
function divAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := div(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265

```solidity
file:  src/erc-20/ERC20MultiVotes.sol

73     low = mid + 1;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L73

```solidity
file:    src/governance/GovernorBravoDelegateMaia.sol

526     uint256 c = a + b;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L526

```solidity
file:       src/maia/libraries/DateTimeLib.sol

57           uint256 day = timestamp / 86400;
            startOfDay = day * 86400;
            result = ((day + 3) % 7) + 1 == 2;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/libraries/DateTimeLib.sol#L57-L59


## [G-26]  Use Mappings Instead of Arrays

There are two data types to describe lists of data in Solidity, arrays and maps, and their syntax and structure are quite different, allowing each to serve a distinct purpose. While arrays are packable and iterable, mappings are less expensive.

```solidity
file:      src/ulysses-omnichain/MulticallRootRouter.sol

25         uint256[] amountsOut; // Amounts of output hTokens to send.
            uint256[] depositsOut;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L25-L26


```solidity
file:   src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

20      address[] hTokens;
        address[] tokens;
        uint256[] amounts;
        uint256[] deposits;

37      address[] hTokens; //Input Local hTokens Address.
        address[] tokens; //Input Native / underlying Token Address.
        uint256[] amounts; //Amount of Local hTokens deposited for interaction.
        uint256[] deposits;

61      uint256[] amounts; //Amount of Local hTokens deposited for interaction.
        uint256[] deposits;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L20-L23

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L37-L40

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L61-L62

```solidity
file:   src/ulysses-omnichain/interfaces/IBranchPort.sol

122     address[] memory _localAddresses,
        address[] memory _underlyingAddresses,
        uint256[] memory _amounts,
        uint256[] memory _deposits

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L122-L225



## [G‑27] Don’t apply the same value to state variable

If MAX_VOTING_DELAY is already 80640, it’ll save 2100 gas to not set it to that value again

```solidity

file:    src/governance/GovernorBravoDelegateMaia.sol

18        uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

      /// @notice The max setable voting period
       uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

      /// @notice The min setable voting delay
       uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

     /// @notice The max setable voting delay
       uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#18-L27


### If daoShare is already 100, it’ll save 2100 gas to not set it to that value again


```solidity

file:   src/hermes/minters/BaseV2Minter.sol

28        uint256 internal constant max_tail_emission = 100;
    uint256 internal constant max_dao_share = 300;

    /// @inheritdoc IBaseV2Minter
    address public immutable override underlying;
    /// @inheritdoc IBaseV2Minter
    ERC4626 public immutable override vault;

    /// @inheritdoc IBaseV2Minter
    FlywheelGaugeRewards public override flywheelGaugeRewards;
    /// @inheritdoc IBaseV2Minter
    address public override dao;

    /// @inheritdoc IBaseV2Minter
    uint256 public override daoShare = 100;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L28-L42


```solidity

file:    src/ulysses-amm/factories/UlyssesFactory.sol

49        uint256 public poolId = 1;

         ///@notice next tokenId
          uint256 public tokenId = 1;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L49-L52



### If MIN_SIGMA2 is already  1e16, it’ll save 2100 gas to not set it to that value again

```solidity

file:  src/ulysses-amm/UlyssesPool.sol

52    uint256 private constant MAX_PROTOCOL_FEE = 1e16;

    /// @notice The maximum lambda1 that can be set (10%)
    uint256 private constant MAX_LAMBDA1 = 1e17;

    /// @notice The minimum sigma2 that can be set (1%)
    uint256 private constant MIN_SIGMA2 = 1e16;


```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L52-L58

```solidity
file:

15        uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;
16          uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L15-L16


### If MIN_EXECUTION_OVERHEAD is already  155_000, it’ll save 2100 gas to not set it to that value again

```solidity

file:

180       uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; 
181       uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; 

226       settlementNonce = 1;
227       accumulatedFees = 1;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L180-L181

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L226-L227



## [G-28]   Use do while loops instead of for loops

A do while loop will cost less gas since the condition is not being checked for the first iteration.


In the provided code snippet, using a while loop instead of a for loop can potentially save gas. By making this change, you avoid the redundant evaluation of the condition i < num && i < length in each iteration of the loop.

```solidity
file:    src/erc-20/ERC20Boost.sol

207        for (uint256 i = 0; i < num && i < length;) {
            address gauge = gaugeList[offset + i];

            GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];

            if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
                require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
                delete getUserGaugeBoost[msg.sender][gauge];

                emit Detach(msg.sender, gauge);
            } else {
                gaugeState.userGaugeBoost -= boost.toUint128();

                emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
            }

            unchecked {
                i++;
            }
        }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L207

### Here's the refactored code using a while loop: 

```solidity

uint256 i = 0;
while (i < num && i < length) {
    address gauge = gaugeList[offset + i];

    GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];

    if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
        require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
        delete getUserGaugeBoost[msg.sender][gauge];

        emit Detach(msg.sender, gauge);
    } else {
        gaugeState.userGaugeBoost -= boost.toUint128();

        emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
    }

    unchecked {
        i++;
    }
}

```
In this refactored code, the for loop has been replaced with a while loop. The initialization of the counter i is moved outside the loop, and the loop condition i < num && i < length is checked only once at the beginning.

In the provided code snippet, using a do while loop instead of a for loop can potentially save gas. By making this change, you avoid the redundant evaluation of the condition (userFreeWeight + totalFreed) < weight in each iteration of the loop.

```solidity
file:    src/erc-20/ERC20Gauges.sol

535        uint256 size = gaugeList.length;
        for (uint256 i = 0; i < size && (userFreeWeight + totalFreed) < weight;) {
            address gauge = gaugeList[i];
            uint112 userGaugeWeight = getUserGaugeWeight[user][gauge];
            if (userGaugeWeight != 0) {
                // If the gauge is live (not deprecated), include its weight in the total to remove
                if (!_deprecatedGauges.contains(gauge)) {
                    totalFreed += userGaugeWeight;
                }
                userFreed += userGaugeWeight;
                _decrementGaugeWeight(user, gauge, userGaugeWeight, currentCycle);

                unchecked {
                    i++;
                }
            }
        }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L535
### Here's the refactored code using a do while loop:

```solidity
uint256 size = gaugeList.length;
uint256 i = 0;
do {
    address gauge = gaugeList[i];
    uint112 userGaugeWeight = getUserGaugeWeight[user][gauge];
    if (userGaugeWeight != 0) {
        // If the gauge is live (not deprecated), include its weight in the total to remove
        if (!_deprecatedGauges.contains(gauge)) {
            totalFreed += userGaugeWeight;
        }
        userFreed += userGaugeWeight;
        _decrementGaugeWeight(user, gauge, userGaugeWeight, currentCycle);

        unchecked {
            i++;
        }
    }
} while (i < size && (userFreeWeight + totalFreed) < weight);


```
In this refactored code, the for loop has been replaced with a do while loop. The initialization of the counter i and the assignment of size are moved outside the loop. The loop condition i < size && (userFreeWeight + totalFreed) < weight is checked only once at the end of each iteration.


```solidity
file:    src/erc-20/ERC20MultiVotes.sol


328        for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {
            address delegatee = delegateList[i];
            uint256 delegateVotes = _delegatesVotesCount[user][delegatee];
            // Minimum of votes delegated to delegatee and unused votes of delegatee
            uint256 votesToFree = FixedPointMathLib.min(delegateVotes, userUnusedVotes(delegatee));
            // Skip if votesToFree is zero
            if (votesToFree != 0) {
                totalFreed += votesToFree;

                if (delegateVotes == votesToFree) {
                    // If all votes are freed, remove delegatee from list
                    require(_delegates[user].remove(delegatee)); // Remove from set. Should never fail.
                    _delegatesVotesCount[user][delegatee] = 0;
                } else {
                    // If not all votes are freed, update the votes count
                    _delegatesVotesCount[user][delegatee] -= votesToFree;
                }

                _writeCheckpoint(delegatee, _subtract, votesToFree);
                emit Undelegation(user, delegatee, votesToFree);
            }
        }


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L328


In the provided code snippet, using a do while loop instead of a for loop can potentially save gas. By making this change, you avoid the redundant evaluation of the condition _localAddresses.length in each iteration of the loop.

```solidity
file:      src/ulysses-omnichain/ArbitrumBranchPort.sol
  
136        for (uint256 i = 0; i < _localAddresses.length;) {
            if (_deposits[i] > 0) {
                _underlyingAddresses[i].safeTransferFrom(
                    _depositor,
                    address(this),
                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
                );
            }
            if (_amounts[i] - _deposits[i] > 0) {
                IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(
                    _depositor, _localAddresses[i], _amounts[i] - _deposits[i]
                );
            }

            unchecked {
                ++i;
            }
        }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L136 

### Here's the refactored code using a do while loop:


```solidity

uint256 i = 0;
do {
    if (_deposits[i] > 0) {
        _underlyingAddresses[i].safeTransferFrom(
            _depositor,
            address(this),
            _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
        );
    }
    if (_amounts[i] - _deposits[i] > 0) {
        IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(
            _depositor, _localAddresses[i], _amounts[i] - _deposits[i]
        );
    }

    unchecked {
        ++i;
    }
} while (i < _localAddresses.length);


```
In this refactored code, the for loop has been replaced with a do while loop. The initialization of the counter i is moved outside the loop, and the loop condition i < _localAddresses.length is checked only once at the end of each iteration.


## [G-29]  Inverting the condition of an if-else-statement wastes gas

Flipping the true and false blocks instead saves 3 gas.


```solidity
file:    src/rewards/rewards/FlywheelGaugeRewards.sol

185       uint112 completedRewards = queuedRewards.storedCycle == lastCycle ? queuedRewards.cycleRewards : 0;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L185

To convert the given code snippet into an if-else statement, you can do the following:

```solidity
uint112 completedRewards;
if (queuedRewards.storedCycle == lastCycle) {
    completedRewards = queuedRewards.cycleRewards;
} else {
    completedRewards = 0;
}

```
n this converted code, an if-else statement is used to assign the value to the completedRewards variable based on the condition. If queuedRewards.storedCycle is equal to lastCycle, the code block inside the if statement is executed, and completedRewards is assigned the value of queuedRewards.cycleRewards. On the other hand, if the condition is false, the code block inside the else statement is executed, and completedRewards is assigned the value of 0.


```solidity
file:    src/talos/base/TalosBaseStrategy.sol

215     shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L215

```solidity
file:    src/talos/libraries/PoolVariables.sol

252      sqrtPriceLimitX96 = zeroForOne ? sqrtPriceX96 - exactSqrtPriceImpact : sqrtPriceX96 + exactSqrtPriceImpact;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L252 

```solidity
file:   src/uni-v3-staker/libraries/IncentiveTime.sol

44      uint256 earliest = timestamp < end ? timestamp : end;


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L44

```solidity
file:      src/ulysses-omnichain/RootBridgeAgent.sol

680         uint160 sqrtPriceLimitX96 =
            zeroForOneOnInflow ? sqrtPriceX96 - exactSqrtPriceImpact : sqrtPriceX96 + exactSqrtPriceImpact;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L680

```solidity
file:      src/erc-4626/ERC4626.sol

109        return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());

116        return supply == 0 ? shares : shares.mulDiv(totalAssets(), supply);

128        return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);

135        return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets());
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L109

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L116

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L128

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L135


we only convert the first snippet into an if-else statement in order to potentially save gas, you can apply the concept of flipping the true and false blocks. Here's the modified code:

```solidity
if (supply != 0) {
    return assets.mulDiv(supply, totalAssets());
} else {
    return assets;
}

```
In this converted code, the condition supply == 0 is inverted to supply != 0. If supply is not equal to zero, the if block is executed, and the result of assets.mulDiv(supply, totalAssets()) is returned. On the other hand, if supply is equal to zero, the else block is executed, and assets is returned directly.

By flipping the true and false blocks, the execution avoids performing the division operation when it's not necessary (i.e., when supply is zero), potentially reducing gas consumption. This optimization assumes that the division operation is more expensive than returning the assets directly.

```solidity

file:  src/erc-4626/ERC4626DepositOnly.sol

71     return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());

78     return supply == 0 ? shares : shares.mulDiv(totalAssets(), supply);

90     return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#71

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L78

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L90

```solidity

file:    src/ulysses-omnichain/BranchPort.sol

389      return _decimals == 18 ? _amount : _amount * 1 ether / (10 ** _decimals);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L389


