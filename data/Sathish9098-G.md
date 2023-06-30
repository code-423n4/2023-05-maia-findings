# GAS OPTIMIZATIONS

### Table Of Contents


##

## [G-1] Pack structs by putting variables that can fit together next to each other

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

## [G-2] State variables can be packed to use fewer storage slots

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

### ``vMaia.sol``: currentMonth,unstakePeriodEnd state variables can be packed together: ``Gas saved: 1 * 2k = 2k , 1 SLOT``

``currentMonth`` only stores month values ``(0-12)`` and ``unstakePeriodEnd`` this stores ``timestamp``. For ``currentMonth,unstakePeriodEnd`` uint128 is more than enough. So we can save ``1 SLOT`` and ``2000 GAS``

``uint128`` alone stores more than ``10000`` years timestamp 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/vMaia.sol#L34-L35

```diff
FILE: 2023-05-maia/src/maia/vMaia.sol

- 34: uint256 private currentMonth;
- 35:    uint256 private unstakePeriodEnd;
+ 34: uint128 private currentMonth;
+ 35:    uint128 private unstakePeriodEnd;

```

##

##

## [G-3] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each ``Gwarmaccess (100 gas)`` with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

### ``BaseV2Minter.sol``: ``dao `` should be cached with local ``address`` stack variable : Saves ``100 GAS``,``1 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L145

```diff
FILE: 2023-05-maia/src/hermes/minters/BaseV2Minter.sol

+ address _dao=dao ;
- 145: if (dao != address(0)) underlying.safeTransfer(dao, share);
+ 145: if (_dao != address(0)) underlying.safeTransfer(_dao, share);

```

### ``ERC20Boost.sol``: ``gaugeState.userGaugeBoost `` should be cached with local ``uint256`` stack variable : Saves ``400 GAS``,``4 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L177-L185

```diff
FILE: Breadcrumbs2023-05-maia/src/erc-20/ERC20Boost.sol

+ uint256 userGaugeBoost_=gaugeState.userGaugeBoost;
- 177: if (boost >= gaugeState.userGaugeBoost) {
+ 177: if (boost >= userGaugeBoost_) {
178:            _userGauges[msg.sender].remove(gauge);
179:            delete getUserGaugeBoost[msg.sender][gauge];
180:
181:            emit Detach(msg.sender, gauge);
182:        } else {
183:            gaugeState.userGaugeBoost -= boost.toUint128();
184:
- 185:            emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
+ 185:            emit DecrementUserGaugeBoost(msg.sender, gauge, userGaugeBoost_);
186:        }




210:           GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
211:
+ uint256 userGaugeBoost_=gaugeState.userGaugeBoost;
- 212: if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
+ 212: if (_deprecatedGauges.contains(gauge) || boost >= userGaugeBoost_) {
213:                require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
214:                delete getUserGaugeBoost[msg.sender][gauge];
215:
216:                emit Detach(msg.sender, gauge);
217:            } else {
218:                gaugeState.userGaugeBoost -= boost.toUint128();
219:
- 220:                emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
+ 220:                emit DecrementUserGaugeBoost(msg.sender, gauge, userGaugeBoost_);
221:            }

```

### ``RootBridgeAgent.sol``: ``settlement.hTokens[i] ``,``settlement.toChain`` should be cached with local ``address`` and ``uint24`` stack variable : Saves ``200 GAS``,``2 SLOD`` per iterations 

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L591-L612

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol

596:for (uint256 i = 0; i < settlement.hTokens.length;) {
597:            //Check if asset
+        address hTokens_ = settlement.hTokens[i] ;
+        uint24  toChain_= settlement.toChain;
- 598:            if (settlement.hTokens[i] != address(0)) { //@audit settlement.hTokens[i] first SLOD
+ 598:            if (hTokens_ != address(0)) {
599:                //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
600:                IPort(localPortAddress).bridgeToRoot(
601:                    msg.sender,
- 602:                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain), //@audit settlement.hTokens[i] second SLOD , //@audit settlement.toChain first SLOD
+ 602:                    IPort(localPortAddress).getGlobalTokenFromLocal(hTokens_ , toChain_),
603:                    settlement.amounts[i],
604:                    settlement.deposits[i],
- 605:                    settlement.toChain  //@audit settlement.toChain second SLOD
+ 605:                    toChain_
606:                );
607:            }

```

### ``RootBridgeAgent.sol``: ``userFeeInfo.gasToBridgeOut `` should be cached with local uint128 stack variable : Saves ``200 GAS``,``2 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L746-L759

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol

746: uint256 _initialGas = initialGas;
+ uint128 _gasToBridgeOut = userFeeInfo.gasToBridgeOut ;
747:
748:        if (_toChain == localChainId) {
749:            //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
750:            if (_initialGas > 0) {
- 751:                address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], userFeeInfo.gasToBridgeOut);
+ 751:                address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId],_gasToBridgeOut );
752:            }
753:
- 754:            return uint128(userFeeInfo.gasToBridgeOut);
+ 754:            return uint128(_gasToBridgeOut);
755:        }
756:
757:        if (_initialGas > 0) {
- 758:            if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
+ 758:            if (_gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
- 759:            (amountOut, gasToken) = _gasSwapOut(userFeeInfo.gasToBridgeOut, _toChain);
+ 759:            (amountOut, gasToken) = _gasSwapOut(_gasToBridgeOut, _toChain);

```

### ``UlyssesPool.sol``: `` newTotalWeights `` should be used instead of ``totalWeights``   : Saves ``100 GAS``,``1 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L241-L243

```diff
FILE: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol

241:  totalWeights = newTotalWeights;
242:
- 243:        if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights) {
+ 243:        if (newTotalWeights> MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights) {
```

### ``GovernorBravoDelegateMaia.sol``: `` proposal.proposer `` should be cached with local ``address`` variable   : Saves ``200 GAS``,``2 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L234C9-L247C14

```diff
FILE: 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol

+ address _proposer= proposal.proposer;
- if (msg.sender != proposal.proposer && msg.sender != admin) {
+ if (msg.sender != _proposer && msg.sender != admin) {
            // Whitelisted proposers can't be canceled for falling below proposal threshold
            if (isWhitelisted(proposal.proposer)) {
                require(
-                     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < 
+                     (govToken.getPriorVotes(_proposer, sub256(block.number, 1)) < 
                   getProposalThresholdAmount())
                        && msg.sender == whitelistGuardian,
                    "GovernorBravo::cancel: whitelisted proposer"
                );
            } else {
                require(
-                     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < 
+                     (govToken.getPriorVotes(_proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
                    "GovernorBravo::cancel: proposer above threshold"
                );
            }

```
### ``GovernorBravoDelegateMaia.sol``: `` pendingAdmin `` should be cached with local ``address`` variable   : Saves ``400 GAS``,``4 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L508

```diff
FILE: 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol

+ address oldPendingAdmin = pendingAdmin;
507: require(
- 508:            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending
+ 508:            msg.sender == oldPendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending  admin only"
509:        );
510:
511:        // Save current values for inclusion in log
512:        address oldAdmin = admin;
- 513:        address oldPendingAdmin = pendingAdmin;
514:
515:        // Store admin with value pendingAdmin
- 516:        admin = pendingAdmin;
+ 516:        admin = oldPendingAdmin ;
517:
518:        // Clear the pending value
519:        pendingAdmin = address(0);
520:
- 521:        emit NewAdmin(oldAdmin, admin);
+ 521:        emit NewAdmin(oldAdmin, oldPendingAdmin );
- 522:        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
+ 522:        emit NewPendingAdmin(oldPendingAdmin, address(0));

```
### ``ERC20Boost.sol``: `` gaugeState.userGaugeBoost`` should be cached with local ``uint128`` variable   : Saves ``100 GAS``,``1 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L176-L185

```diff
FILE: Breadcrumbs2023-05-maia/src/erc-20/ERC20Boost.sol

176: GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
+   uint128 _userGaugeBoost = gaugeState.userGaugeBoost;
- 177:        if (boost >= gaugeState.userGaugeBoost) {
+ 177:        if (boost >= _userGaugeBoost ) {
178:            _userGauges[msg.sender].remove(gauge);
179:            delete getUserGaugeBoost[msg.sender][gauge];
180:
181:            emit Detach(msg.sender, gauge);
182:        } else {
- 183:            gaugeState.userGaugeBoost -= boost.toUint128();
+ 183:            gaugeState.userGaugeBoost = _userGaugeBoost - boost.toUint128();
184:
185:            emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);


```
### ``TalosBaseStrategy.sol``: `` liquidity`` should be cached with local ``uint128`` variable   : Saves ``200 GAS``,``2 SLOD``

```diff
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

214: uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.
+  uint128 _liquidity = liquidity;
- 215:        shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;
+ 215:        shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / _liquidity;
- 216:        liquidity += liquidityDifference;
+ 216:        liquidity =_liquidity + liquidityDifference;
```

```diff

INonfungiblePositionManager _nonfungiblePositionManager = nonfungiblePositionManager; // Saves an extra SLOAD
        {
+         uint _liquidity= liquidity ;
-            uint128 liquidityToDecrease = uint128((_liquidity* shares) / totalSupply);

            (amount0, amount1) = _nonfungiblePositionManager.decreaseLiquidity(
                INonfungiblePositionManager.DecreaseLiquidityParams({
                    tokenId: _tokenId,
                    liquidity: liquidityToDecrease,
                    amount0Min: amount0Min,
                    amount1Min: amount1Min,
                    deadline: block.timestamp
                })
            );

            if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

            _burn(_owner, shares);

-             liquidity -= liquidityToDecrease;
+             liquidity =_liquidity - liquidityToDecrease;
        }

```
##

## [G-4] Avoid emit state variable when stack variable available

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

###  ``TalosBaseStrategy.sol``: Emit stack variable ``_tokenId`` instead of ``tokenId `` state variable  : Saves ``100 GAS``, ``1 SLOD``


```diff
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

155:   tokenId = _tokenId;
156:
157:        _mint(receiver, shares);
158:        if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
159:
- 160:`        emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares);
+ 160:`        emit Initialize(_tokenId, msg.sender, receiver, amount0, amount1, shares);
```

##

## [G-5]  ``require() or revert()`` statements that check input arguments should be at the ``top`` of the ``function`` (Also restructured some if’s)

``Fail early and cheaply``

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

### ``GovernorBravoDelegateMaia.sol``: Cheaper to check the function parameters,constants before checking ``govToken.getPriorVotes(msg.sender, sub256(block.number, 1))`` external calls. Saves  ``2100 gas`` 

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

### ``UlyssesPool.sol``: Cheaper to check the function parameters, constants before checking ``factory.owner()`` external calls. Saves  ``2100 gas`` 

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
### ``RootPort.sol``: Cheaper to check the ``_bridgeAgentFactory,_coreRootRouter`` function parameters before checking  ``_setup``  state variable. ``Saves around 60-75 gas ``. 

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


- 145: require(_setup, "Setup ended.");
- 146:        require(isBridgeAgent[_coreRootBridgeAgent], "Core Bridge Agent doesn't exist.");
147:        require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");
148:        require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");
149:        require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");
+ 145: require(_setup, "Setup ended.");
+ 146:        require(isBridgeAgent[_coreRootBridgeAgent], "Core Bridge Agent doesn't exist.");

```

##

## [G-6] Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
Help the Optimizer by saving a storage variable’s reference instead of repeatedly fetching it

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.

As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it

### ``MultiRewardsDepot.sol``:  _assets[rewardsContract] mapping should be cached : Saves ``100 GAS``, ``1 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/depots/MultiRewardsDepot.sol#L60-L62

```diff
FILE: 2023-05-maia/src/rewards/depots/MultiRewardsDepot.sol

+ address rewardsContract_=_assets[rewardsContract];
- 60: emit AssetRemoved(rewardsContract, _assets[rewardsContract]);
+ 60: emit AssetRemoved(rewardsContract, rewardsContract_);
61:
- 62: delete _isAsset[_assets[rewardsContract]];
+ 62: delete _isAsset[rewardsContract_];
```

### ``MultiRewardsDepot.sol``:  _assets[rewardsContract] mapping should be cached : Saves ``100 GAS``, ``1 SLOD``

```diff
FILE: 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol

+ uint256 _partnerManager = partners[partnerIds[partnerManager]];
- 81: if (partners[partnerIds[partnerManager]] != partnerManager) revert InvalidPartnerManager();
+ 81: if (_partnerManager  != partnerManager) revert InvalidPartnerManager();
- 82:  delete partners[partnerIds[partnerManager]];
+ 82:  delete partners[_partnerManager];
```

### ``BranchBridgeAgent.sol``:  ``uint8(getDeposit[_depositNonce].hTokens.length)``,``getDeposit[_depositNonce].tokens[0]``,``getDeposit[_depositNonce].hTokens[0]``,``getDeposit[_depositNonce].amounts[0]``,``getDeposit[_depositNonce].deposits[0]``,``getDeposit[nonce].hTokens``,``getDeposit[nonce].tokens``, ``getDeposit[nonce].amounts``, ``getDeposit[nonce].deposits``,``getDeposit[_depositNonce].depositedGas``  mappings should be cached : Saves ``1500 GAS``, ``15 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L332



```diff
FILE: 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol

+ uint8 _hTokensLength = uint8(getDeposit[_depositNonce].hTokens.length);

- 332: if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) { //@audit 1 SLOD
+ 332: if (_hTokensLength  == 1) {

- 365: } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) { //@audit 2 SLOD
+ 365: } else if (_hTokensLength > 1) {

372: msg.sender,
- 373: uint8(getDeposit[_depositNonce].hTokens.length), //@audit 3 SLOD
+ 373: _hTokensLength,
374: nonce,

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L332-L364

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol

if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {
+     address tokens_= getDeposit[_depositNonce].tokens[0];
+     address hTokens_=getDeposit[_depositNonce].hTokens[0];
+     uint256 amounts_=getDeposit[_depositNonce].amounts[0];
+    uint256  deposits_= getDeposit[_depositNonce].deposits[0];
            if (_isSigned) {
                packedData = abi.encodePacked( //@audit GAS is this possible to cache getDeposit[_depositNonce]
                    bytes1(0x05),
                    msg.sender,
                    _depositNonce,
-                    getDeposit[_depositNonce].hTokens[0], //@audit 1 SLOD
+                    hTokens_,
-                     getDeposit[_depositNonce].tokens[0], //@audit 1 SLOD
+                     tokens_,
-                    getDeposit[_depositNonce].amounts[0], //@audit 1 SLOD
+                    amounts_,
                    _normalizeDecimals(
                        
-                 getDeposit[_depositNonce].deposits[0],ERC20(getDeposit[_depositNonce].tokens[0]).decimals()  //@audit 2 SLOD
+                 deposits_,ERC20(tokens_).decimals()
                    ),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                );
            } else {
                packedData = abi.encodePacked(
                    bytes1(0x02),
                    _depositNonce,
-                     getDeposit[_depositNonce].hTokens[0], //@audit 2 SLOD
+                      hTokens_,
-                     getDeposit[_depositNonce].tokens[0], //@audit 3 SLOD
+                     tokens_,
-                    getDeposit[_depositNonce].amounts[0],   //@audit 2 SLOD
+                    amounts_,
                    _normalizeDecimals(
                        
-            getDeposit[_depositNonce].deposits[0],ERC20(getDeposit[_depositNonce].tokens[0]).decimals() //@audit 4 SLOD
+            deposits_,ERC20(tokens_).decimals()

```


```diff
FILE: 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol

 } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {
            //Nonce
         uint32 nonce = _depositNonce; 

+    address[]  hTokens_=getDeposit[nonce].hTokens; 
+    address[]  tokens_=getDeposit[nonce].tokens;
+    uint256[]  amounts_=getDeposit[nonce].amounts; 
+    uint256[]  deposits_=getDeposit[nonce].deposits; 

            if (_isSigned) {
                packedData = abi.encodePacked(
                    bytes1(0x06),
                    msg.sender,
                    uint8(getDeposit[_depositNonce].hTokens.length),
                    nonce,
-                    getDeposit[nonce].hTokens, //@audit 1 SLOD
+                    hTokens_,
-                    getDeposit[nonce].tokens, //@audit 1 SLOD
+                    tokens_,
-                    getDeposit[nonce].amounts, //@audit 1 SLOD
+                    amounts_,
-                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens), //@audit 2 SLOD
+                    _normalizeDecimalsMultiple(deposits_, tokens_),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                );
            } else {
                packedData = abi.encodePacked(
                    bytes1(0x03),
                    uint8(getDeposit[nonce].hTokens.length),
                    _depositNonce,
-                    getDeposit[nonce].hTokens,  //@audit 2 SLOD
+                    hTokens_,
-                    getDeposit[nonce].tokens,   //@audit 3 SLOD
+                    tokens_,
-                    getDeposit[nonce].amounts,  //@audit 2 SLOD
+                    amounts_,
-                     _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens), //@audit 4 SLOD
+                     _normalizeDecimalsMultiple(deposits_, tokens_),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                );
            }

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L1068-L1075

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol

+   uint256 depositedGas_= getDeposit[_depositNonce].depositedGas ;
- 1069: if (minExecCost > getDeposit[_depositNonce].depositedGas) {
+ 1069: if (minExecCost > gdepositedGas_) {
1070:            _forceRevert();
1071:            return;
1072:        }
1073:
1074:        //Update user deposit reverts if not enough gas => user must boost deposit with gas
- 1075:        getDeposit[_depositNonce].depositedGas -= minExecCost.toUint128();
+ 1075:        getDeposit[_depositNonce].depositedGas = depositedGas_ - minExecCost.toUint128();
```

### ``RootBridgeAgent.sol``:  ``getSettlement[_settlementNonce].gasToBridgeOut`` mapping should be cached : Saves ``100 GAS``, ``1 SLOD``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L839-L845

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol

838: //Check if sufficient balance
+ uint128 _gasToBridgeOut = getSettlement[_settlementNonce].gasToBridgeOut ;
- 839:        if (minExecCost > getSettlement[_settlementNonce].gasToBridgeOut) {
+ 839:        if (minExecCost > _gasToBridgeOut) {
840:            _forceRevert();
841:            return;
842:        }
843:
844:        //Update user deposit reverts if not enough gas
- 845:        getSettlement[_settlementNonce].gasToBridgeOut -= minExecCost.toUint128();
+ 845:        getSettlement[_settlementNonce].gasToBridgeOut =_gasToBridgeOut - minExecCost.toUint128();
846:    }


```

##

## [G-7] Store external call values in immutable variables to improve performance and reduce gas costs 

You can save a Gcoldsload (2100 gas) in the address provider, plus the 100 gas overhead of the external call, for every _replenishGas(), by creating an immutable CONFIG variable which will store the ``IAnycallProxy(localAnyCallAddress).config()`` address. ``localAnyCallAddress`` is immutable variable so not changed any where in the contract. When ever we call ``IAnycallProxy(localAnyCallAddress).config()`` always return the same address. So its ``waste of external call `` instead we can store the ``IAnycallProxy(localAnyCallAddress).config()`` address with ``CONFIG`` immutable variable during constructor initialization time.

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L851

```diff
FILE: 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol

+ address public immutable CONFIG;

Inside the constructor

+ CONFIG = IAnycallProxy(localAnyCallAddress).config();

- 851: IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));
+ 851: IAnycallConfig(CONFIG).deposit{value: _executionGasSpent}(address(this));

- 1236: IAnycallConfig anycallConfig = IAnycallConfig(IAnycallProxy(localAnyCallAddress).config());
+ 1236: IAnycallConfig anycallConfig = IAnycallConfig(CONFIG);
```
###


```diff
FILE: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol

+ address public immutable OWNER;

Inside the constructor

+ OWNER= factory.owner()

- 154: asset.safeTransfer(factory.owner(), claimed);
+ 154: asset.safeTransfer(OWNER, claimed);
- 324: if (msg.sender != factory.owner()) revert Unauthorized();
+ 324: if (msg.sender != OWNER) revert Unauthorized();

```

##

## [G-8] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }

Saves: `` 100-130 GAS ``

### ``bHermes.sol``: The ``subtractions`` should be ``unchecked ``. `` /// @dev Never overflows since balandeOf >= userClaimed`` As per comment the values not going to overflow : Saves ``390 GAS``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/bHermes.sol#L98-L101

```diff
FILE: Breadcrumbs2023-05-maia/src/hermes/bHermes.sol

98:   /// @dev Never overflows since balandeOf >= userClaimed.
99:        claimWeight(balance - userClaimedWeight[msg.sender]);
100:        claimBoost(balance - userClaimedBoost[msg.sender]);
101:        claimGovernance(balance - userClaimedGovernance[msg.sender]);

```

### ``UlyssesPool.sol``: ``balance - assets`` should be ``unchecked`` since not possible to overflow because of this condition check ``if (balance > assets)`` : Saves ``130 GAS``

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-amm/UlyssesPool.sol

138: if (balance > assets) {
- 139:  return balance - assets;
+ 139:  return unchecked{balance - assets};
140: } else {

```
### ``UlyssesPool.sol``: `` newRebalancingFee - oldRebalancingFee`` should be ``unchecked`` since not possible to overflow because of this condition check ``if (oldRebalancingFee < newRebalancingFee)`` : Saves ``260 GAS``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L217-L219

```diff
FILE: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol

217:     if (oldRebalancingFee < newRebalancingFee) {
- 218:            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);
+ unchecked {
+        asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);
+    }
219:     }

302: if (oldRebalancingFee < newRebalancingFee) {
- 303:            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);
+ unchecked {
+        asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);
+    }
304:        }

```

### ``BranchPort.sol``: `` currBalance - minReserves``,`` minReserves - currBalance`` should be ``unchecked`` since not possible to overflow because of this condition check ``currBalance > minReserves``,``currBalance < minReserves `` : Saves ``260 GAS``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L129

```diff
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/BranchPort.sol

- 129: return currBalance > minReserves ? currBalance - minReserves : 0;
+ 129: unchecked {return currBalance > minReserves ? currBalance - minReserves : 0;}

- 141: return currBalance < minReserves ? minReserves - currBalance : 0;
+ 129: unchecked {return currBalance < minReserves ? minReserves - currBalance : 0;}

```





##

## [G-9] Unused internal state variable and lock modifier can be removed to save overall contract deployment cost 

``Unused internal state variable'' take up space in the contract's storage, which can increase the gas cost of deploying and calling the contract. If a state variable is never used, then it can be safely removed from the contract.
``Lock modifier`` can also increase the gas cost of calling a contract. Lock modifiers are used to prevent a contract from being called by an unauthorized address. However, if the contract is only ever called by a trusted address, then the lock modifier can be safely removed.


### ``CoreRootRouter.sol``: After Removed unused code the deployment cost saved : ``25,487 GAS``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/CoreRootRouter.sol#L435-L443

```Solidity
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol

435: uint256 internal _unlocked = 1;
436:
437:    /// @notice Modifier for a simple re-entrancy check.
438:    modifier lock() {
439:        require(_unlocked == 1);
440:        _unlocked = 2;
441:        _;
442:        _unlocked = 1;
443:    }

```

##

## [G-10] Use constants instead of type(uintx).max

Using constants instead of type(uint256).max can save gas. This is because the compiler can embed constants directly into the bytecode of your contract, which saves on gas costs.

``type(uint256).max `` this code will use 3 gas to get the maximum value of a uint256.

But constant will use 0 gas to get the maximum value of a uint256.

### ``BranchBridgeAgent.sol``: Use ``constants`` instead of ``type(uintx).max ``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L70C8-L70C45

```solidity
FILE: 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol

70: if (liquidity == type(uint96).max) {

385: amount0Max: type(uint128).max,

386: amount1Max: type(uint128).max

456: if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

506: if (liquidity >= type(uint96).max) {

509: liquidityNoOverflow: type(uint96).max,

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L133C12-L133C67

```solidity
FILE: 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol

133: require(newRewards <= type(uint112).max); // safe cast

187: require(nextRewards <= type(uint112).max); // safe cast

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/tokens/ERC4626PartnerManager.sol#L200-L203

```solidity
FILE: 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol

200:  address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);

201:  address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);

202:  address(governance).safeApprove(newPartnerVault, type(uint256).max);

203:  address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626MultiToken.sol#L143

```solidity
FILE: Breadcrumbs2023-05-maia/src/erc-4626/ERC4626MultiToken.sol

143: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

165: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

200: shares = type(uint256).max;

270:  return type(uint256).max;

275:  return type(uint256).max;

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L130

```solidity
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

130: address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

131: address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

249: if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares;

285: amount0Max: type(uint128).max,

286: amount1Max: type(uint128).max

367: amount0Max: type(uint128).max,

368: amount1Max: type(uint128).max

```

##

## [G-11] Upgrade Solidity’s optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

```
FILE: hardhat.config.js

 overrides: {
      'src/talos/TalosStrategyVanilla.sol': {
        version: '0.8.18',
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 200,
          },
          metadata: {
            bytecodeHash: 'none',
          },
        },
      },
      'src/talos/TalosStrategyStaked.sol': {
        version: '0.8.18',
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 200,
          },
          metadata: {
            bytecodeHash: 'none',
          },
        },
      },
      'src/ulysses-omnichain/RootBridgeAgent.sol': {
        version: '0.8.18',
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 200,
          },
          metadata: {
            bytecodeHash: 'none',
          },
        },
      },
      'src/ulysses-omnichain/BranchBridgeAgent.sol': {
        version: '0.8.18',
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 200,
          },
          metadata: {
            bytecodeHash: 'none',
          },
        },
      },
      'src/ulysses-omnichain/BranchBridgeAgentFactory.sol': {
        version: '0.8.18',
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 200,
          },
          metadata: {
            bytecodeHash: 'none',
          },
        },
      },
    },
  },

```

## [G-] Unnecessary memory operations with an immutable variable

[G-13] External function calls should be avoided inside the loops 

[G-10]	State variables only set in the constructor should be declared immutable	18

## [G-] Use memory instead of calldata inside the loops if the values not changed 

Use assembly to perform efficient back-to-back calls

If a similar external call is performed back-to-back, we can use assembly to reuse any function signatures and function parameters that stay the same. In addition, we can also reuse the same memory space for each function call (scratch space + free memory pointer + zero slot), which can potentially allow us to avoid memory expansion costs.

File: contracts/Pool/PoolRegistry.sol
239:        comptrollerProxy.setCloseFactor(closeFactor);
240:        comptrollerProxy.setLiquidationIncentive(liquidationIncentive);
241:        comptrollerProxy.setMinLiquidatableCollateral(minLiquidatableCollateral);
242:        comptrollerProxy.setPriceOracle(PriceOracle(priceOracle));
243:
244:        // Start transferring ownership to msg.sender
245:        comptrollerProxy.transferOwnership(msg.sender);








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