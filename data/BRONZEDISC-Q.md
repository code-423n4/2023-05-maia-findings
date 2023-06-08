## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

```solidity
// place this before the constructor
286:    error UnrecognizedBridgeAgent();
287:    error UnrecognizedBridgeAgentFactory();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

```solidity
// place this before the constructor
202:    error GasErrorOrRepeatedTx();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

```solidity
// place these after state variables
42:    error ParameterLengthError();
44:    error InvalidPoolId();
46:    error InvalidAsset();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol

```solidity
// events should come before constructor
165:    event CollectFees(uint256 feesFromPool0, uint256 feesFromPool1, uint256 usersFees0, uint256 usersFees1);
170:    event CompoundFees(uint256 amount0, uint256 amount1);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol

```solidity
// state variable and modifiers should come before constructor
138:    modifier requiresAgentExecutor() {
143:    uint256 internal _unlocked = 1;
147:    modifier lock() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol

```solidity
// modifier should come before constructor
190:    modifier onlyWhitelisted(address from) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol

```solidity
// modifier should come before constructor
155:    modifier onlyActiveGaugeFactory() {
160:    modifier onlyAdmin() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol

```solidity
// modifier and custom errors should come before constructor
69:    modifier checkWeight(uint256 amount) override {
77:    modifier checkBoost(uint256 amount) override {
85:    modifier checkGovernance(uint256 amount) override {
173:    error InsufficientUnderlying();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol

```solidity
// modifier should come before constructor
105:    modifier onlyGaugeFactory() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol

```solidity
// modifier and custom errors should come before constructor
67:    modifier checkWeight(uint256 amount) virtual override {
75:    modifier checkGovernance(uint256 amount) virtual override {
83:    modifier checkPartnerGovernance(uint256 amount) virtual override {

121:    error UnstakePeriodNotLive();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

```solidity
// modifier should come before constructor
75:    modifier requiresCoreRouter() {
81:    modifier requiresPort() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

```solidity
// modifier should come before constructor
74:    modifier requiresCoreRouter() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol

```solidity
// modifier should come before constructor
69:    modifier requiresApprovedCaller() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol

```solidity
// modifier should come before constructor
57:    modifier requiresPort() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol

```solidity
// modifier should come before constructor
72:    modifier onlyFlywheelRewards() override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol

```solidity
// modifier should come before constructor
73:    modifier onlyStrategy() override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/OptimizerFactory.sol

```solidity
// function is coming before constructor, should come after it
19:    function getOptimizers() external view returns (TalosOptimizer[] memory) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol

```solidity
// modifier should come before constructor
39:    modifier onlybHermes() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol

```solidity
// modifier should come before constructor
39:    modifier onlybHermes() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol

```solidity
// modifier should come before constructor
32:    modifier onlybHermes() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol

```solidity
// modifier should come before constructor
40:    modifier onlyFlywheelRewards() override {
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

```solidity
// external function coming before internal ones
229:    function anyExecuteNoSettlement(bytes calldata _data)

// fallback  should come before all the other functions
284:    fallback() external payable {}
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
// internal function coming before external one
169:    function _queueRewards(address[] memory gauges, uint32 currentCycle, uint32 lastCycle, uint256 totalQueuedForCycle)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

```solidity
// internal pure function should come after all the non-pure functions
134:    function _forceRevert() internal pure override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

```solidity
// private function coming before external one.
83:    function _createPool(ERC20 asset, address owner) private returns (uint256 _poolId) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

```solidity
// view external functions should come after non-view external ones
108:    function circulatingSupply() public view returns (uint256) {
113:    function weeklyEmission() public view returns (uint256) {
118:    function calculateGrowth(uint256 _minted) public view returns (uint256) {
// put this one before all the other public ones
123:    function updatePeriod() public returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol

```solidity
// view external functions should come after non-view external ones
51:    function getGaugeFactories() external view returns (BaseV2GaugeFactory[] memory) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

```solidity
// internal function coming before external function
75:    function _receiveAddBridgeAgent(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol

```solidity
// internal coming before public functions
128:    function _mint(address to, uint256 amount) internal virtual override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol

```solidity
// private functions coming before external ones
66:    function getRebalance(ITalosBaseStrategy position) private view returns (bool) {
78:    function getRerange(ITalosBaseStrategy position) private view returns (bool) {

// view external function is coming before non-view non-view external, should come after.
91:    function checkUpkeep(bytes calldata) external view override returns (bool upkeepNeeded, bytes memory performData) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol

```solidity
// external function coming after internal ones
98:    function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol

```solidity
// external view coming before external non-view 
63:    function getBribeFlywheels() external view returns (FlywheelCore[] memory) {

// public coming after external functions
79:    function createBribeFlywheel(address bribeToken) public {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol

```solidity
// place fallback right after constructor
71:    fallback() external payable {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol

```solidity
// private function coming before external ones
31:    function getUlyssesLP(uint256 id) private returns (UlyssesPool ulysses) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol

```solidity
// view external functions should come after non-view external ones
44:    function getPartners() external view returns (PartnerManager[] memory) {
49:    function getVaults() external view returns (IBaseVault[] memory) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol

```solidity
// internal function coming before external one
53:    function distribute(uint256 amount) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/BoostAggregatorFactory.sol

```solidity
// view/pure functions should come last at in their groups
42:    function getBoostAggregators() external view returns (BoostAggregator[] memory) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelBribeRewards.sol

```solidity
// internal function coming before external one.
32:    function getNextCycleRewards(ERC20 strategy) internal override returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol

```solidity
// external functions should come after public ones, not before.
23:    function mint(address account, uint256 amount) external override onlyOwner returns (bool) {
```

---

### natSpec missing [3]

Some functions are missing @params or @returns. Specification Format.” These are written with a triple slash (///) or a double asterisk block(/** ... */) directly above function declarations or statements to generate documentation in JSON format for developers and end-users. It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). These comments contain different types of tags:
- @title: A title that should describe the contract/interface @author: The name of the author (for contract, interface) 
- @notice: Explain to an end user what this does (for contract, interface, function, public state variable, event) 
- @dev: Explain to a developer any extra details (for contract, interface, function, state variable, event) 
- @param: Documents a parameter (just like in doxygen) and must be followed by parameter name (for function, event)
- @return: Documents the return variables of a contract’s function (function, public state variable)
- @inheritdoc: Copies all missing tags from the base function and must be followed by the contract name (for function, public state variable)
- @custom: Custom tag, semantics is application-defined (for everywhere).

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

```solidity
26:    constructor(address _hTokenFactoryAddress, address _localPortAddress) BaseBranchRouter() {
286:    error UnrecognizedBridgeAgent();
287:    error UnrecognizedBridgeAgentFactory();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
53:    constructor(address _rewardToken, address _owner, ERC20Gauges _gaugeToken, IBaseV2Minter _minter) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol

```solidity
4: contract GovernorBravoEvents {
6:    event ProposalCreated(
27:    event ProposalCanceled(uint256 id);
30:    event ProposalQueued(uint256 id, uint256 eta);
33:    event ProposalExecuted(uint256 id);
36:    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);
39:    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);
42:    event NewImplementation(address oldImplementation, address newImplementation);
45:    event ProposalThresholdSet(uint256 oldProposalThreshold, uint256 newProposalThreshold);
48:    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
51:    event NewAdmin(address oldAdmin, address newAdmin);
54:    event WhitelistAccountExpirationSet(address account, uint256 expiration);
57:    event WhitelistGuardianSet(address oldGuardian, address newGuardian);
60: contract GovernorBravoDelegatorStorage {
149:    enum ProposalState {
161: contract GovernorBravoDelegateStorageV2 is GovernorBravoDelegateStorageV1 {
169: interface TimelockInterface {
170:    function delay() external view returns (uint256);
171:    function GRACE_PERIOD() external view returns (uint256);
172:    function acceptAdmin() external;
173:    function queuedTransactions(bytes32 hash) external view returns (bool);
174:    function queueTransaction(
181:    function cancelTransaction(
188:    function executeTransaction(
197: interface GovTokenInterface {
198:    function getPriorVotes(address account, uint256 blockNumber) external view returns (uint96);
200:    function totalSupply() external view returns (uint256);
203: interface GovernorAlpha {

// return missing
205:    function proposalCount() external returns (uint256);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol

```solidity
19: library DeployStaked {
20:    function createTalosV3Strategy(
89:    function transfer(address _to, uint256 _amount) public override returns (bool) {
94:    function transferFrom(address _from, address _to, uint256 _amount) public override returns (bool) {

// @param missing
101:    function beforeRedeem(uint256 _tokenId, address _owner) internal override {
108:    function afterRedeem(uint256 _tokenId) internal override {
114:    function beforeDeposit(uint256 _tokenId, address _receiver) internal override {
121:    function afterDeposit(uint256 _tokenId) internal override {
127:    function beforeRerange(uint256 _tokenId) internal override {
134:    function afterRerange(uint256 _tokenId) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

```solidity
37: library DeployArbitrumBranchBridgeAgent {
38:    function deploy(
71:    constructor(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

```solidity
60:    constructor(address _owner) {
65:    function renounceOwnership() public payable override onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol

```solidity
22:    function createTalosV3Vanilla(

// @return missing
129:    function _compoundFees(uint256 _tokenId) internal returns (uint256 amount0, uint256 amount1) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol

```solidity
28:    constructor() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

```solidity
53:    constructor(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

```solidity
83:    function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

// @return missing
113:    function createBridgeAgent(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

```solidity
13:    function deploy() external returns (address) {
54:    constructor() {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

```solidity
21:    constructor(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

```solidity
38:    constructor(address _hTokenFactoryAddress, address _localPortAddress)

// @param missing
75:    function _receiveAddBridgeAgent(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol

```solidity
55:    constructor(ERC20 _hermes, address _owner, uint32 _gaugeCycleLength, uint32 _incrementFreezeWindow)
96:    function claimOutstanding() public virtual {

// @return missing
115:    function totalAssets() public view virtual override returns (uint256) {
140:    function transfer(address to, uint256 amount) public virtual override returns (bool) {
158:    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

```solidity
54:    function initialize(address _coreRootBridgeAgent) external override onlyOwner {

// @param missing
79:    function createBridgeAgent(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol

```solidity
// @param missing
76:    function newGauge(address strategy, bytes memory data) internal override returns (BaseV2Gauge) {
89:    function afterCreateGauge(address strategy, bytes memory) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol

```solidity
105:    modifier onlyGaugeFactory() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol

```solidity
8:     constructor(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

```solidity
27:    constructor(uint24 _localChainId, address _localPortAddress) {
35:    function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

// @return missing
60:    function createToken(string memory _name, string memory _symbol)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol

```solidity
// @params missing
62:    function createTalosV3Strategy(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

```solidity
40:    function initialize(address _coreRouter) external onlyOwner {

// @return missing
54:    function createToken(string memory _name, string memory _symbol)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol

```solidity
25:    constructor(address _userAddress, address _localPortAddress) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol

```solidity
// @param missing
53:    function distribute(uint256 amount) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/OptimizerFactory.sol

```solidity
// @params missing
36:    function createTalosOptimizer(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol

```solidity
// @params missing
18: contract TalosStrategyVanillaFactory is TalosBaseStrategyFactory {
33:    function createTalosV3Strategy(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol

```solidity
20:    constructor(address _owner) ERC20("bHermes Votes", "bHERMES-V", 18) {
39:    modifier onlybHermes() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol

```solidity
26:    constructor(address _owner, uint32 _rewardsCycleLength, uint32 _incrementFreezeWindow)
39:    modifier onlybHermes() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol

```solidity
32:    constructor(
39:    function _getAccruedRewards(ERC20 strategy) internal override returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol

```solidity
33:    constructor(
40:    function _getAccruedRewards(ERC20) internal override returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelInstantRewards.sol

```solidity
// params missing
17: contract FlywheelInstantRewards is BaseFlywheelRewards, IFlywheelInstantRewards {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol

```solidity
22:    constructor(address _owner) ERC20("bHermes Boost", "bHERMES-B", 18) {
32:    modifier onlybHermes() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol

```solidity
11: contract ERC20hTokenBranch is ERC20, Ownable, IERC20hTokenBranch {
12:    constructor(string memory _name, string memory _symbol, address _owner)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/Maia.sol

```solidity
41:    constructor(address _owner) ERC20("Maia", "MAIA", 18) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/HERMES.sol

```solidity
48:    constructor(address _owner) ERC20("Hermes", "HERMES", 18) {
```

---

### State variable and function names [4]

- variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
// private and internal `variables` should preppend with `underline`
45:    uint32 internal nextCycle;
48:    uint112 internal nextCycleQueuedRewards;
51:    uint32 internal paginationOffset;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol

```solidity
// private and internal `variables` should preppend with `underline`
52:    bool private stakeFlag = false;

// private and internal `functions` should preppend with `underline`
101:    function beforeRedeem(uint256 _tokenId, address _owner) internal override {
108:    function afterRedeem(uint256 _tokenId) internal override {
114:    function beforeDeposit(uint256 _tokenId, address _receiver) internal override {
121:    function afterDeposit(uint256 _tokenId) internal override {
127:    function beforeRerange(uint256 _tokenId) internal override {
134:    function afterRerange(uint256 _tokenId) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol

```solidity
// constant state variables should be UPPER_CASE
47:    uint24 private constant protocolFee = 2 * 1e5; //20%

// private and internal `functions` should preppend with `underline`
72:    function beforeRedeem(uint256 _tokenId, address) internal override {
79:    function afterRedeem(uint256 _tokenId) internal override {}
83:    function beforeDeposit(uint256 _tokenId, address) internal override {
90:    function afterDeposit(uint256 _tokenId) internal override {}
94:    function beforeRerange(uint256 _tokenId) internal override {
100:    function afterRerange(uint256 _tokenId) internal override {}
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

```solidity
// constant state variables should be UPPER_CASE
24:    uint256 internal constant week = 86400 * 7;
26:    uint256 internal constant base = 1000;
28:    uint256 internal constant max_tail_emission = 100;
29:    uint256 internal constant max_dao_share = 300;

// private and internal `variables` should preppend with `underline`
51:    address internal initializer;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

```solidity
// private and internal `functions` should preppend with `underline`
110:    function updateAssetBalances() internal {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol

```solidity
// private and internal `functions` should preppend with `underline`
66:    function getRebalance(ITalosBaseStrategy position) private view returns (bool) {
87:    function getRerange(ITalosBaseStrategy position) private view returns (bool) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol

```solidity
// private and internal `functions` should preppend with `underline`
76:    function newGauge(address strategy, bytes memory data) internal override returns (BaseV2Gauge) {
89:    function afterCreateGauge(address strategy, bytes memory) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol

```soldity
//  private and internal `variables` should preppend with `underline`
26:    FlywheelBoosterGaugeWeight private immutable flywheelGaugeWeightBooster;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol

```solidity
// private and internal `variables` should preppend with `underline`
34:    uint256 private currentMonth;
35:    uint256 private unstakePeriodEnd;

// private and internal `functions` should preppend with `underline`
102:    function beforeWithdraw(uint256, uint256) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol

```solidity
// underline prefixed are used on private or internal functions for a better pattern, consider removing it
41:    function _setImplementation(address implementation_) public {

// private and internal `functions` should preppend with `underline`
59:    function delegateTo(address callee, bytes memory data) internal {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol

```solidity
// private and internal `variables` should preppend with `underline`
15:    mapping(uint256 => UlyssesPool) private pools;

// private and internal `functions` should preppend with `underline`
31:    function getUlyssesLP(uint256 id) private returns (UlyssesPool ulysses) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol

```solidity
// private and internal `functions` should preppend with `underline`
62:    function createTalosV3Strategy(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol

```solidity
// private and internal `functions` should preppend with `underline`
53:    function distribute(uint256 amount) internal override {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol

 ```solidity
// private and internal `functions` should preppend with `underline`
33:    function createTalosV3Strategy( 
 ```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol

```solidity
// private and internal `variables` should preppend with `underline`
13:    address private immutable asset;
16:    address private immutable rewardsContract;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol

```solidity
// private and internal `variables` should preppend with `underline`
42:    bHermesGauges private immutable bhermes;
```

---

### Version [5]

 - Pragma versions should be standardized and avoid floating pragma ( ^ )

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol

```solidity
// remove the >= for a stable version, also version different from the rest of the repo.
2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol

```solidity
// remove the >= for a stable version, also version different from the rest of the repo.
3: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol

```solidity
// remove the >= for a stable version, also version different from the rest of the repo.
2: pragma solidity >=0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol

```soldity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol

```solidity
// remove the ^ for a stable version, also version different from the rest of the repo.
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol

```solidity
// remove the ^ for a stable version
3: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol

```solidity
// remove the ^ for a stable version
2: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/OptimizerFactory.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol

```solidity
// remove the ^ for a stable version
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol

```soldity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelInstantRewards.sol

```solidity
// remove the ^ for a stable version
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelBribeRewards.sol

```solidity
// remove the ^ for a stable version
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol

```solidity
// remove the ^ for a stable version
3:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/Maia.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/HERMES.sol

```solidity
// remove the ^ for a stable version
2:  pragma solidity ^0.8.0;
```


---

### Initialized variables [6]

- Variables are default initialized with 0 for `uint / int`, 0x0 for `address` and false for `bool`

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
176:        for (uint256 i = 0; i < size; i++) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol

```solidity
// initialized with the default value
52:    bool private stakeFlag = false;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

```solidity
101:        for (uint256 i = 0; i < _localAddresses.length;) {
136:        for (uint256 i = 0; i < _localAddresses.length;) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

```solidity
99:        for (uint256 i = 0; i < length;) {
107:        for (uint256 i = 0; i < length;) {
110:            for (uint256 j = 0; j < length;) {
123:        for (uint256 i = 0; i < length;) {
145:        for (uint256 i = 0; i < length;) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

```solidity
// initializing with default value
95:        for (uint256 i = 0; i < assets.length; i++) {
111:        for (uint256 i = 0; i < assets.length; i++) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol

```solidity
// public visibility is the default
8:    constructor(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol

```solidity
// initializing with default value
78:        for (uint256 i = 0; i < length;) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol

```solidity
// initializing with default value
48:        for (uint256 i = 0; i < calls.length; i++) {
```

---

### Observations [7]

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol

```solidity
// add an error message to this revert, a custom one is preferable
147:        require(_unlocked == 1);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

```solidity
// add a 2 step process to make sure you put the right oracles address before changing it 
86:    function setDao(address _dao) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol

```solidity
// add a 2 step process to make sure you put the right admin before transfering it 
145:    function changeAdmin(address newAdmin) external onlyAdmin {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

```solidity
// define the visibility explicitly
16:    address immutable localPortAddress;
```