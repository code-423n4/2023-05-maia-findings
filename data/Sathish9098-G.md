# GAS OPTIMIZATIONS

##

## [G-] Pack structs by putting variables that can fit together next to each other

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

### ``IBranchBridgeAgent.sol``: hToken and toChain can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

26: struct DepositInput {
27:    //Deposit Info
28:    address hToken; //Input Local hTokens Address.
+ 32:    uint24 toChain; //Destination chain for interaction.
29:    address token; //Input Native / underlying Token Address.
30:    uint256 amount; //Amount of Local hTokens deposited for interaction.
31:    uint256 deposit; //Amount of native tokens deposited for interaction.
- 32:    uint24 toChain; //Destination chain for interaction.
33: }

```

### ``IBranchBridgeAgent.sol``: numberOfAssets,depositNonce,toChain,depositedGas can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L55-L65

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

55: struct DepositMultipleParams {
56:    //Deposit Info
- 57:    uint8 numberOfAssets; //Number of assets to deposit.
- 58:    uint32 depositNonce; //Deposit nonce.
59:    address[] hTokens; //Input Local hTokens Address.
60:    address[] tokens; //Input Native / underlying Token Address.
61:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
62:    uint256[] deposits; //Amount of native tokens deposited for interaction.
63:    uint24 toChain; //Destination chain for interaction.
64:    uint128 depositedGas; //BRanch chain gas token amount sent with request.
+ 57:    uint8 numberOfAssets; //Number of assets to deposit.
+ 58:    uint32 depositNonce; //Deposit nonce.
65: }

```
### ``IRootBridgeAgent.sol``: depositNonce,hToken,toChain can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol

63: struct DepositParams {
64:    //Deposit Info
65:    uint32 depositNonce; //Deposit nonce.
+ 70:    uint24 toChain; //Destination chain for interaction.
66:    address hToken; //Input Local hTokens Address.
67:    address token; //Input Native / underlying Token Address.
68:    uint256 amount; //Amount of Local hTokens deposited for interaction.
69:    uint256 deposit; //Amount of native tokens deposited for interaction.
- 70:    uint24 toChain; //Destination chain for interaction.
71: }

```
### ``IRootBridgeAgent.sol``: numberOfAssets,depositNonce,toChain can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L73-L82

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol

73: struct DepositMultipleParams {
74:    //Deposit Info
- 75:    uint8 numberOfAssets; //Number of assets to deposit.
- 76:    uint32 depositNonce; //Deposit nonce.
77:    address[] hTokens; //Input Local hTokens Address.
78:    address[] tokens; //Input Native / underlying Token Address.
79:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
80:    uint256[] deposits; //Amount of native tokens deposited for interaction.
+ 75:    uint8 numberOfAssets; //Number of assets to deposit.
+ 76:    uint32 depositNonce; //Deposit nonce.
81:    uint24 toChain; //Destination chain for interaction.
82: }
```
### ``GovernorBravoInterfaces.sol#L105-L136``: proposer,canceled,executed can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L105-L136

```diff
FILE: Breadcrumbs2023-05-maia/src/governance/GovernorBravoInterfaces.sol

105: struct Proposal {
106:        /// @notice Unique id for looking up a proposal
107:        uint256 id;
108:        /// @notice Creator of the proposal
109:        address proposer;
+ 130:        /// @notice Flag marking whether the proposal has been canceled
+ 131:        bool canceled;
+ 132:        /// @notice Flag marking whether the proposal has been executed
+ 133:        bool executed;
110:        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
111:        uint256 eta;
112:        /// @notice the ordered list of target addresses for calls to be made
113:        address[] targets;
114:        /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
115:        uint256[] values;
116:        /// @notice The ordered list of function signatures to be called
117:        string[] signatures;
118:        /// @notice The ordered list of calldata to be passed to each call
119:        bytes[] calldatas;
120:        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
121:        uint256 startBlock;
122:        /// @notice The block at which voting ends: votes must be cast prior to this block
123:        uint256 endBlock;
124:        /// @notice Current number of votes in favor of this proposal
125:        uint256 forVotes;
126:        /// @notice Current number of votes in opposition to this proposal
127:        uint256 againstVotes;
128:        /// @notice Current number of votes for abstaining for this proposal
129:        uint256 abstainVotes;
- 130:        /// @notice Flag marking whether the proposal has been canceled
- 131:        bool canceled;
- 132:        /// @notice Flag marking whether the proposal has been executed
- 133:        bool executed;
134:        /// @notice Receipts of ballots for the entire set of voters
135:        mapping(address => Receipt) receipts;
136:    }


```
##

## [G-] State variables can be packed to use fewer storage slots

The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to each other in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ``~2000 gas`` with every subsequent SLOAD for that storage slot. This is due to us incurring a ``Gwarmaccess (100 gas)`` versus a ``Gcoldsload (2100 gas)``

### ``RootBridgeAgent.sol``: settlementNonce,accumulatedFees state variables can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``. 

``accumulatedFees`` mostly saves the value ``1`` the other values also not exceeds the ``20000``. So uint128 is more than enough.

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L164-L190

- NOTE: The line numbers are skipped unwanted areas

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol

- 160:    uint32 public settlementNonce;

    /// @notice Mapping from Settlement nonce to Deposit Struct.
    mapping(uint32 => Settlement) public getSettlement;

    /*///////////////////////////////////////////////////////////////
                            EXECUTOR STATE
    //////////////////////////////////////////////////////////////*/

    /// @notice If true, bridge agent has already served a request with this nonce from  a given chain. Chain -> Nonce -> Bool
    mapping(uint256 => mapping(uint32 => bool)) public executionHistory;

    /*///////////////////////////////////////////////////////////////
                        GAS MANAGEMENT STATE
    //////////////////////////////////////////////////////////////*/

    uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; // 100_000 for anycall + 55_000 for fallback
    uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution

    uint256 public initialGas;
    UserFeeInfo public userFeeInfo;

    /*///////////////////////////////////////////////////////////////
                        DAO STATE
    //////////////////////////////////////////////////////////////*/
+ 160:    uint32 public settlementNonce;
- 190:    uint256 public accumulatedFees;
+ 190:    uint128 public accumulatedFees;
```

### ``RootBridgeAgent.sol``: coreRootBridgeAgentAddress,bridgeAgentFactoriesLenght,_setup state variables can be packed together: ``Gas saved: 2 * 2k = 4k , 2 SLOT``

``bridgeAgentFactoriesLenght`` the type can be changed to uint64 instead of uint256. ``bridgeAgentFactoriesLenght`` this value is incremented only once in initialize() function. the value is not changed any where inside the contract 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootPort.sol#L83

- NOTE: The line numbers are skipped unwanted areas

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/RootPort.sol

- 40: address public coreRootBridgeAgentAddress;



+ 40: address public coreRootBridgeAgentAddress;
- 83: uint256 public bridgeAgentFactoriesLenght;
+ 126: bool internal _setup;
+ 83: uint64 public bridgeAgentFactoriesLenght;
- 126: bool internal _setup;

```

### ``BaseV2Minter.sol``: daoShare,dao and tailEmission,initializer  state variables can be packed together: ``Gas saved: 2 * 2k = 4k , 2 SLOT``

``daoShare,tailEmission`` can be uint96 instead of uint256. Because ``_daoShare > max_dao_share,tail_emission > max_tail_emission`` checks. ``max_dao_share,max_tail_emission`` are constants and stores the value of ``max_dao_share=300,max_tail_emission=100``. So ``daoShare,tailEmission`` can't store more than constants values.

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L42-L43

```diff
FILE: 2023-05-maia/src/hermes/minters/BaseV2Minter.sol

39: address public override dao;
+ 42:    uint96 public override daoShare = 100;
40:
41:    /// @inheritdoc IBaseV2Minter
- 42:    uint256 public override daoShare = 100;
- 43:    uint256 public override tailEmission = 20;
44:    /// @inheritdoc IBaseV2Minter
45:
46:    /// @inheritdoc IBaseV2Minter
47:    uint256 public override weekly;
48:    /// @inheritdoc IBaseV2Minter
49:    uint256 public override activePeriod;
50:
+ 43:    uint96 public override tailEmission = 20;
51:    address internal initializer;

```

### ``ERC20hTokenRoot.sol``: localChainId,rootPortAddress  state variables can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

The ``uint96`` type is a 96-bit unsigned integer, which is more than enough for the ``vast majority of chain IDs``. Also other contracts only using ``uint24`` for chainId .  it is perfectly safe to change the ``uint256 public localChainId;`` declaration to ``uint96 public localChainId;``. This will save some gas, and it will make the contract more efficient.

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L16C33-L28

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol

15:  /// @inheritdoc IERC20hTokenRoot
- 16:    uint256 public localChainId;
+ 16:    uint96 public localChainId;
17:
18:    /// @inheritdoc IERC20hTokenRoot
19:    address public rootPortAddress;
20:
21:    /// @inheritdoc IERC20hTokenRoot
22:    address public localBranchPortAddress;
23:
24:    /// @inheritdoc IERC20hTokenRoot
25:    address public factoryAddress;
26:
27:    /// @inheritdoc IERC20hTokenRoot
28:    mapping(uint256 => uint256) public getTokenBalance;

```

### ``ERC20hTokenRootFactory.sol``: coreRootRouterAddress,hTokensLenght state variables can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

``hTokensLenght`` uint96 is more than enough to ``htokens`` length. Because all new tokens pushed to ``hTokens`` array.The array exceeds 1024 elements EVM reverts. So maximum ``hTokensLenght`` may be 1024 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L22-L26

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

22:    address public coreRootRouterAddress;
+ 26:    uint96 public hTokensLenght;
23:
24:    ERC20hTokenRoot[] public hTokens;
25:
- 26:    uint256 public hTokensLenght;

```

## [G-] State variables should be cached in stack variables rather than re-reading them from storage



## [G-]  ``require() or revert()`` statements that check input arguments should be at the ``top`` of the ``function`` (Also restructured some if’s)

``Fail early and cheaply``

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

### Cheaper to check the function parameters,constants before checking ``govToken.getPriorVotes(msg.sender, sub256(block.number, 1))`` external calls. Saves  ``2100 gas`` 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L112-L124

```diff
FILE: Breadcrumbs2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol

111: // Reject proposals before initiating as Governor
112:        require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");
+ 123:        require(targets.length != 0, "GovernorBravo::propose: must provide actions");
+ 124:        require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");
113:        // Allow addresses above proposal threshold and whitelisted addresses to propose
+ 119:        require(
+ 120:            targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,
+ 121:            "GovernorBravo::propose: proposal function information arity mismatch"
+ 122:        );
114:        require(
115:            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
116:                || isWhitelisted(msg.sender),
117:            "GovernorBravo::propose: proposer votes below proposal threshold"
118:        );
- 119:        require(
- 120:            targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,
- 121:            "GovernorBravo::propose: proposal function information arity mismatch"
- 122:        );
- 123:        require(targets.length != 0, "GovernorBravo::propose: must provide actions");
- 124:        require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

```

### Cheaper to check the function parameters, constants before checking ``factory.owner()`` external calls. Saves  ``2100 gas`` 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L324-L327


```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-amm/UlyssesPool.sol

323: function setProtocolFee(uint256 _protocolFee) external nonReentrant {
- 324:        if (msg.sender != factory.owner()) revert Unauthorized();
325:
326:        // Revert if the protocol fee is larger than 1%
327:        if (_protocolFee > MAX_PROTOCOL_FEE) revert InvalidFee();
+ 324:        if (msg.sender != factory.owner()) revert Unauthorized();
328:
329:        protocolFee = _protocolFee;
330:    }

```
### Cheaper to check the ``_bridgeAgentFactory,_coreRootRouter`` function parameters before checking  ``_setup``  state variable. ``Saves around 60-75 gas ``. 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootPort.sol#L129-L131

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/RootPort.sol

128: function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {
- 129:        require(_setup, "Setup ended.");
130:        require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");
131:        require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");
+ 129:        require(_setup, "Setup ended.");
132:
133:        isBridgeAgentFactory[_bridgeAgentFactory] = true;

```



## [G-] Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
Help the Optimizer by saving a storage variable’s reference instead of repeatedly fetching it

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.

As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it

###






## [G-] Save gas by checking against default WETH address

[G-] Avoid emitting constants
A log topic (declared with indexed) has a gas cost of Glogtopic (375 gas)

[G-] Combine constants into single 

## [G-] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement



## [G-] Avoid emit state variable when stack variable available

The gas cost for emitting a state variable is ``100 gas``, while the gas cost for emitting a stack variable is 8 gas. This means that emitting a stack variable instead of a state variable can save ``92 gas``

### ``GovernorBravoDelegateMaia.sol``: Emit and return stack variable ``newProposalID`` instead of state variable ``newProposal.id`` : Saves ``200 GAS``, ``2 SLOD`` 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L164-L167

```diff
FILE: Breadcrumbs2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol

164: emit ProposalCreated(
- 165: newProposal.id, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock,description
+ 165: newProposalID, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock,description
166:        );
- 167:      return newProposal.id;
+ 167:      return newProposalID;

```

### ``GovernorBravoDelegateMaia.sol``: Emit stack variables ``newVotingDelay,newVotingPeriod,newProposalThreshold,account`` instead of ``votingDelay,votingPeriod,proposalThreshold , whitelistGuardian`` state variables  : Saves ``500 GAS``, ``5 SLOD`` 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L406

```diff
FILE: Breadcrumbs2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol

404: votingDelay = newVotingDelay;
405:
- 406:    emit VotingDelaySet(oldVotingDelay, votingDelay);
+ 406:    emit VotingDelaySet(oldVotingDelay, newVotingDelay);


420: votingPeriod = newVotingPeriod;
421:
- 422: emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
+ 422: emit VotingPeriodSet(oldVotingPeriod, newVotingPeriod);

437:  proposalThreshold = newProposalThreshold;
438:
- 439:  emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold);
+ 439:  emit ProposalThresholdSet(oldProposalThreshold, newProposalThreshold);

464: whitelistGuardian = account;
465:
- 466: emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);
+ 466: emit WhitelistGuardianSet(oldGuardian, account);

```
### ``GovernorBravoDelegator.sol``: Emit stack variable ``implementation_`` instead of ``implementation `` state variable  : Saves ``100 GAS``, ``1 SLOD`` 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegator.sol#L48-L50

```diff
FILE: 2023-05-maia/src/governance/GovernorBravoDelegator.sol

48:  implementation = implementation_;
49:
- 50:    emit NewImplementation(oldImplementation, implementation);
+ 50:    emit NewImplementation(oldImplementation, implementation_);

```



## [G-] Unnecessary memory operations with an immutable variable

## [G-] Superfluous event fields

For event we don't want declare the variable to BLOCK.NUMBER AND BLOCK.TIMESTAP. 
block.timestamp and block.number are added to event information by default so adding them manually wastes gas


## [G-] Avoid contract existence check using low level calls 

##

## [G-] Cache the state variables outside the loop

Caching ``state variables`` outside the loop can be a good way to ``optimize`` your Solidity code. This is because it can prevent the ``contract`` from having to ``load the state variables from storage multiple times``. 

### ``UlyssesPool.sol``: Cache the ``totalWeights`` outside the loop. ``Instances 5`` Saves ``500 GAS`` per ``iteration``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L175-L179

```diff
FILE: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol

+    uint256 totalWeights_= totalWeights;
130: for (uint256 i = 1; i < bandwidthStateList.length; i++) {
- 131:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
- 131:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights_);


173: uint256 oldRebalancingFee;
174:
+    uint256 totalWeights_= totalWeights;
175: for (uint256 i = 1; i < index; i++) {
- 176:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
+ 176:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights_);
177:
178:            oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
179:        }


209: uint256 newRebalancingFee;
210:
+    uint256 totalWeights_= totalWeights;
211:        for (uint256 i = 1; i <= index; i++) {
- 212:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
+ 212:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights_);
213:
214:            newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
        }


230: uint256 oldRebalancingFee;
231:
+    uint256 totalWeights_= totalWeights;
232:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
- 233:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
233:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights_);
234:
235:            oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
236:        }



294: uint256 newRebalancingFee;
295:
+    uint256 totalWeights_= totalWeights;
296:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
- 297:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
- 297:            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights_);
298:
299:            newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
300        }

```





[G-1]	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements	60
[G-2]	Use assembly to check for address(0)	31
[G-3]	Use custom errors instead of require	108
[G-4]	Empty blocks should be removed or emit something	3
[G-5]	Non efficient zero initialization	56
[G-6]	Use assembly to write address storage values	209
[G-7]	Duplicated require/if checks should be refactored to a modifier or function	2
[G-8]	Functions guaranteed to revert when called by normal users can be marked payable	92
[G-9]	State variable read in a loop	68
[G-10]	State variables only set in the constructor should be declared immutable	18
[G-11]	Don’t compare boolean expressions to boolean literals	1
[G-12]	Using storage instead of memory for structs/arrays saves gas	8
[G-13]	Use calldata instead of memory for function parameters	22
[G-14]	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements1	2
[G-15]	Function calls should be cached	32
[G-16]	Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead	21
[G-17]	Change public to external for functions that are not called internally	89
[G-18]	<array>.length should not be looked up in every loop of a for-loop	29
[G-19]	Multiplication by two should use bit shifting	3
[G-20]	Reduce the size of error messages	65
[G-21]	Internal functions only called once can be inlined to save gas	38
[G-22]	Using bools for storage incurs overhead	71
[G-23]	Multiple mappings can be replaced with a single struct mapping	56
[G-24]	Setting the constructor to payable	57
[G-25]	Use unchecked keyword for loop counter	16
[G-26]	Do not reduce approval on transferFrom if current allowance is type(uint256).max	5
[G-27]	Bytes constants are more efficient than string constants	1
[G-28]	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements1	2
[G-29]	<x> += <y> costs more gas than <x> = <x> + <y> for state variables	6
[G-30]	Use solidity version 0.8.20 to gain gas boost	142
[G-31]	Variable names that consist of all capital letters should be reserved for constant/immutable variables	1
[G-32]	Division by two should use bit shifting	9