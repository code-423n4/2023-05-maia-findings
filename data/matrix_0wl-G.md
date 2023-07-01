## Gas Optimizations

|        | Issue                                                                                                                                               |
| ------ | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| GAS-1  | `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`                                                                                          |
| GAS-2  | USE FUNCTION INSTEAD OF MODIFIERS                                                                                                                   |
| GAS-3  | INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT |
| GAS-4  | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT                                                            |
| GAS-5  | require() strings longer than 32 bytes cost extra gas                                                                                               |
| GAS-6  | USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS                                                                                           |
| GAS-7  | Optimize names to save gas                                                                                                                          |
| GAS-8  | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) = for-loop and while-loops                              |
| GAS-9  | Using `private` rather than `public` for constants, saves gas                                                                                       |
| GAS-10 | USING SOLIDITY VERSION 0.8.20 WILL PROVIDE AN OVERALL GAS OPTIMIZATION                                                                              |
| GAS-11 | Use shift Right/Left instead of division/multiplication if possible                                                                                 |
| GAS-12 | Use != 0 instead of > 0 for unsigned integer comparison                                                                                             |
| GAS-13 | Use bitmaps to save gas                                                                                                                             |
| GAS-14 | USE BYTES32 INSTEAD OF STRING                                                                                                                       |

### [GAS-1] `ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`

#### Description:

Use `abi.encodePacked()` where possible to save gas

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

368:                     "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

198:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

346:             keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

347:         bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

```

```solidity
File: src/talos/libraries/PoolActions.sol

51:             abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))

```

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

53:         bytes memory data = abi.encode(_underlyingAddress, address(0), name, symbol);

98:         bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);

```

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

48:         bytes memory data = abi.encode(msg.sender, _globalAddress, _toChain, _rootExecutionGas);

72:         bytes memory data = abi.encode(_underlyingAddress, newToken, name, symbol);

105:         bytes memory data = abi.encode(_globalAddress, newToken);

148:         bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

238:         bytes memory data = abi.encode(_branchBridgeAgentFactory);

259:         bytes memory data = abi.encode(_branchBridgeAgent);

282:         bytes memory data = abi.encode(_underlyingToken, _minimumReservesRatio);

309:         bytes memory data = abi.encode(_portStrategy, _underlyingToken, _dailyManagementLimit, _isUpdateDailyLimit);

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

689:             abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

733:             abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))

```

```solidity
File: src/uni-v3-staker/libraries/IncentiveId.sol

17:         return keccak256(abi.encode(key));

```

### [GAS-2] USE FUNCTION INSTEAD OF MODIFIERS

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Boost.sol

341:     modifier notAttached(address user, uint256 amount) {

```

```solidity
File: src/gauges/BaseV2Gauge.sol

155:     modifier onlyStrategy() virtual {

```

```solidity
File: src/gauges/UniswapV3Gauge.sol

73:     modifier onlyStrategy() override {

```

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

160:     modifier onlyOwnerOrBribesFactoryOwner() {

```

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

155:     modifier onlyActiveGaugeFactory() {

160:     modifier onlyAdmin() {

```

```solidity
File: src/gauges/factories/BribesFactory.sol

105:     modifier onlyGaugeFactory() {

```

```solidity
File: src/hermes/UtilityManager.sol

141:     modifier checkWeight(uint256 amount) virtual;

144:     modifier checkBoost(uint256 amount) virtual;

147:     modifier checkGovernance(uint256 amount) virtual;

```

```solidity
File: src/hermes/bHermes.sol

69:     modifier checkWeight(uint256 amount) override {

77:     modifier checkBoost(uint256 amount) override {

85:     modifier checkGovernance(uint256 amount) override {

```

```solidity
File: src/hermes/tokens/bHermesBoost.sol

32:     modifier onlybHermes() {

```

```solidity
File: src/hermes/tokens/bHermesGauges.sol

39:     modifier onlybHermes() {

```

```solidity
File: src/hermes/tokens/bHermesVotes.sol

39:     modifier onlybHermes() {

```

```solidity
File: src/maia/PartnerUtilityManager.sol

168:     modifier checkPartnerGovernance(uint256 amount) virtual;

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

294:     modifier checkWeight(uint256 amount) virtual override {

302:     modifier checkBoost(uint256 amount) virtual override {

310:     modifier checkGovernance(uint256 amount) virtual override {

318:     modifier checkPartnerGovernance(uint256 amount) virtual override {

325:     modifier checkTransfer(address from, uint256 amount) virtual {

```

```solidity
File: src/maia/vMaia.sol

67:     modifier checkWeight(uint256 amount) virtual override {

75:     modifier checkGovernance(uint256 amount) virtual override {

83:     modifier checkPartnerGovernance(uint256 amount) virtual override {

```

```solidity
File: src/rewards/base/BaseFlywheelRewards.sol

43:     modifier onlyFlywheel() {

```

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

72:     modifier onlyFlywheelRewards() override {

```

```solidity
File: src/rewards/depots/RewardsDepot.sol

24:     modifier onlyFlywheelRewards() virtual;

```

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

40:     modifier onlyFlywheelRewards() override {

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

423:     modifier checkDeviation() {

430:     modifier onlyStrategyManager() {

```

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

190:     modifier onlyWhitelisted(address from) {

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

138:     modifier requiresAgentExecutor() {

146:     modifier lock() {

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1366:     modifier lock() {

1374:     modifier requiresAgentExecutor() {

1380:     modifier requiresExecutor() {

1393:     modifier requiresRouter() {

1404:     modifier requiresFallbackGas() {

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

397:     modifier requiresCoreRouter() {

403:     modifier requiresBridgeAgent() {

409:     modifier requiresBridgeAgentFactory() {

415:     modifier requiresPortStrategy(address _token) {

423:     modifier lock() {

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

438:     modifier lock() {

446:     modifier requiresExecutor() {

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

494:     modifier lock() {

502:     modifier requiresExecutor() {

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1273:     modifier lock() {

1281:     modifier requiresExecutor() {

1296:     modifier requiresRouter() {

1307:     modifier requiresAgentExecutor() {

1313:     modifier requiresLocalBranchBridgeAgent() {

1321:     modifier requiresPort() {

1327:     modifier requiresManager() {

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

510:     modifier requiresBridgeAgentFactory() {

516:     modifier requiresBridgeAgent() {

522:     modifier requiresCoreRootRouter() {

528:     modifier requiresLocalBranchPort() {

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

69:     modifier requiresApprovedCaller() {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

75:     modifier requiresCoreRouter() {

81:     modifier requiresPort() {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

74:     modifier requiresCoreRouter() {

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

57:     modifier requiresPort() {

```

### [GAS-3] INSTEAD OF CALCULATING A STATEVAR WITH KECCAK256() EVERY TIME THE CONTRACT IS MADE PRE CALCULATE THEM BEFORE AND ONLY GIVE THE RESULT TO A CONSTANT

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

347:         bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

348:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: src/uni-v3-staker/libraries/IncentiveId.sol

17:         return keccak256(abi.encode(key));

```

### [GAS-4] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

46:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

### [GAS-5] require() strings longer than 32 bytes cost extra gas

#### Description:

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

364:         require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

63:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

64:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

65:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

66:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

112:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

123:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

124:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

146:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

228:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

350:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

362:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

363:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

366:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

398:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

414:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

431:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

462:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

475:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

476:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

489:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

```

```solidity
File: src/governance/GovernorBravoDelegator.sol

42:         require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

151:         require(_rootBridgeAgentAddress != address(0), "Root Bridge Agent Address cannot be the zero address.");

152:         require(_localAnyCallAddress != address(0), "AnyCall Address cannot be the zero address.");

153:         require(_localAnyCallExecutorAddress != address(0), "AnyCall Executor Address cannot be the zero address.");

154:         require(_localRouterAddress != address(0), "Local Router Address cannot be the zero address.");

155:         require(_localPortAddress != address(0), "Local Port Address cannot be the zero address.");

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

104:         require(_bridgeAgentFactory != address(0), "BridgeAgentFactory is zero address");

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

210:         require(address(_wrappedNativeToken) != address(0), "Wrapped native token cannot be zero address");

212:         require(_localAnyCallAddress != address(0), "Anycall Address cannot be zero address");

213:         require(_localAnyCallExecutorAddress != address(0), "Anycall Executor Address cannot be zero address");

214:         require(_localPortAddress != address(0), "Port Address cannot be zero address");

215:         require(_localRouterAddress != address(0), "Router Address cannot be zero address");

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

115:         require(_wrappedNativeToken != address(0), "Invalid wrapped native token address.");

130:         require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");

131:         require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");

147:         require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");

148:         require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");

149:         require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

64:         require(_rootBridgeAgentFactoryAddress != address(0), "Root Bridge Agent Factory Address cannot be 0");

68:         require(_localCoreBranchRouterAddress != address(0), "Core Branch Router Address cannot be 0");

84:         require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0");

```

### [GAS-6] USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

#### Description:

When constants are marked public, extra getter functions are created, increasing the deployment cost. Marking these functions private will decrease gas cost. One can still read these variables through the source code. If they need to be accessed by an external contract, a separate single getter function can be used to return all constants as a tuple. There are four instances of public constants.

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

360:     bytes32 public constant DELEGATION_TYPEHASH =

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

9:     string public constant name = "vMaia Governor Bravo";

12:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

15:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

18:     uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

21:     uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

24:     uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

27:     uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

30:     uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

33:     uint256 public constant proposalMaxOperations = 10; // 10 actions

36:     uint256 public constant DIVISIONER = 1 ether;

42:     bytes32 public constant DOMAIN_TYPEHASH =

46:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: src/ulysses-omnichain/lib/AnycallFlags.sol

8:     uint256 public constant FLAG_NONE = 0x0;

9:     uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;

10:     uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;

11:     uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;

12:     uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;

15:     uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;

16:     uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```

### [GAS-7] Optimize names to save gas

#### Description:

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. In this report are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).

[Solidity optimize name github](https://github.com/enzosv/solidity-optimize-name)

[Source](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

### [GAS-8] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) = for-loop and while-loops

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Boost.sol

55:                 i++;

101:                 i++;

166:                 i++;

224:                 i++;

245:                 i++;

```

```solidity
File: src/erc-20/ERC20Gauges.sol

117:                 i++;

158:                 i++;

266:                 i++;

346:                 i++;

548:                     i++;

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

328:         for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

60:                 i++;

72:                 i++;

83:                 i++;

174:                 i++;

205:                 i++;

219:                 i++;

238:                 i++;

254:                 i++;

```

```solidity
File: src/gauges/BaseV2Gauge.sol

118:                 i++;

```

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

81:                 i++;

97:                 i++;

```

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

68:                 i++;

84:                 i++;

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

142:         proposalCount++;

181:         for (uint256 i = 0; i < proposal.targets.length; i++) {

215:         for (uint256 i = 0; i < proposal.targets.length; i++) {

251:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

176:         for (uint256 i = 0; i < size; i++) {

```

```solidity
File: src/talos/libraries/PoolVariables.sol

98:         if (tick < 0 && tick % tickSpacing != 0) compressed--;

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

130:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

175:         for (uint256 i = 1; i < index; i++) {

211:         for (uint256 i = 1; i <= index; i++) {

232:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

296:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

95:         for (uint256 i = 0; i < assets.length; i++) {

111:         for (uint256 i = 0; i < assets.length; i++) {

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

283:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

426:             bytes1(0x07), depositNonce++, _settlementNonce, msg.value.toUint128(), _gasToBoostSettlement

719:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

938:         return depositNonce++;

1354:         for (uint256 i = 0; i < _deposits.length; i++) {

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

109:         bridgeAgentFactoriesLenght++;

280:                 i++;

293:         bridgeAgentsLenght++;

311:         bridgeAgentFactoriesLenght++;

334:         strategyTokensLenght++;

355:         portStrategiesLenght++;

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

278:             abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));

633:         return settlementNonce++;

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

135:         bridgeAgentFactoriesLenght++;

370:         bridgeAgentsLenght++;

407:         bridgeAgentFactories[bridgeAgentsLenght++] = _bridgeAgentFactory;

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

48:         for (uint256 i = 0; i < calls.length; i++) {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

45:         hTokensLenght++;

67:         hTokensLenght++;

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

67:         hTokensLenght++;

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

428:         incentive.numberOfStakes--;

502:         incentives[incentiveId].numberOfStakes++;

```

### [GAS-9] Using `private` rather than `public` for constants, saves gas

#### Description:

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

360:     bytes32 public constant DELEGATION_TYPEHASH =

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

9:     string public constant name = "vMaia Governor Bravo";

12:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

15:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

18:     uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

21:     uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

24:     uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

27:     uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

30:     uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

33:     uint256 public constant proposalMaxOperations = 10; // 10 actions

36:     uint256 public constant DIVISIONER = 1 ether;

42:     bytes32 public constant DOMAIN_TYPEHASH =

46:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: src/ulysses-omnichain/lib/AnycallFlags.sol

8:     uint256 public constant FLAG_NONE = 0x0;

9:     uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;

10:     uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;

11:     uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;

12:     uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;

15:     uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;

16:     uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```

### [GAS-10] USING SOLIDITY VERSION 0.8.20 WILL PROVIDE AN OVERALL GAS OPTIMIZATION

#### Description:

Using at least `0.8.10` will save gas due to skipped extcodesize check if there is a return value.

#### **Proof Of Concept**

```solidity
File: lib/v3-periphery/contracts/interfaces/external/IWETH9.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/ERC20Boost.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/ERC20Gauges.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

4: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/interfaces/Errors.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/interfaces/IERC20Boost.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/interfaces/IERC20Gauges.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-20/interfaces/IERC20MultiVotes.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-4626/ERC4626.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/erc-4626/UlyssesERC4626.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/erc-4626/interfaces/IERC4626.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/erc-4626/interfaces/IERC4626DepositOnly.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-4626/interfaces/IERC4626MultiToken.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/erc-4626/interfaces/IUlyssesERC4626.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/BaseV2Gauge.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/UniswapV3Gauge.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/factories/BribesFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/interfaces/IBaseV2Gauge.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/interfaces/IBaseV2GaugeFactory.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/interfaces/IBaseV2GaugeManager.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/interfaces/IBribesFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/interfaces/IUniswapV3Gauge.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/gauges/interfaces/IUniswapV3GaugeFactory.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

2: pragma solidity ^0.8.10;

```

```solidity
File: src/governance/GovernorBravoDelegator.sol

2: pragma solidity ^0.8.10;

```

```solidity
File: src/governance/GovernorBravoInterfaces.sol

2: pragma solidity ^0.8.10;

```

```solidity
File: src/hermes/UtilityManager.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/bHermes.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/interfaces/IUtilityManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/interfaces/IbHermesUnderlying.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/tokens/HERMES.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/tokens/bHermesBoost.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/tokens/bHermesGauges.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/hermes/tokens/bHermesVotes.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/PartnerUtilityManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/interfaces/IBaseVault.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/interfaces/IERC4626PartnerManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/interfaces/IPartnerManagerFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/interfaces/IPartnerUtilityManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/libraries/DateTimeLib.sol

2: pragma solidity ^0.8.4;

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/tokens/Maia.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/maia/vMaia.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/FlywheelCoreInstant.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/FlywheelCoreStrategy.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/base/BaseFlywheelRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/base/FlywheelCore.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/booster/FlywheelBoosterGaugeWeight.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/depots/RewardsDepot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelAcummulatedRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelBooster.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelBribeRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelCore.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelGaugeRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelInstantRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IFlywheelRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IMultiRewardsDepot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/interfaces/IRewardsDepot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/TalosManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/TalosOptimizer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/TalosStrategyStaked.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/talos/TalosStrategyVanilla.sol

3: pragma solidity >=0.8.0;

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

3: pragma solidity >=0.8.0;

```

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/talos/factories/BoostAggregatorFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/factories/OptimizerFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/factories/TalosStrategyVanillaFactory.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/AutomationCompatibleInterface.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/talos/interfaces/IBoostAggregatorFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/IOptimizerFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/ITalosBaseStrategy.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/ITalosBaseStrategyFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/ITalosManager.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/ITalosOptimizer.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/ITalosStrategyStaked.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/interfaces/ITalosStrategyStakedFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/libraries/PoolActions.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/libraries/PoolVariables.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/interfaces/IUlyssesFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/interfaces/IUlyssesPool.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/interfaces/IUlyssesRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-amm/interfaces/IUlyssesToken.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallConfig.sol

3: pragma solidity ^0.8.10;

```

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallExecutor.sol

3: pragma solidity ^0.8.10;

```

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallProxy.sol

3: pragma solidity ^0.8.10;

```

```solidity
File: src/ulysses-omnichain/interfaces/IApp.sol

3: pragma solidity ^0.8.10;

```

```solidity
File: src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IBranchPort.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IBranchRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IMulticall2.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IPortStrategy.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootPort.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootRouter.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IVirtualAccount.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/interfaces/IWETH9.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/lib/AnycallFlags.sol

3: pragma solidity ^0.8.10;

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/uni-v3-staker/interfaces/IUniswapV3Staker.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/uni-v3-staker/libraries/IncentiveId.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/uni-v3-staker/libraries/IncentiveTime.sol

2: pragma solidity ^0.8.0;

```

```solidity
File: src/uni-v3-staker/libraries/NFTPositionInfo.sol

3: pragma solidity ^0.8.0;

```

```solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

2: pragma solidity ^0.8.0;

```

### [GAS-11] Use shift Right/Left instead of division/multiplication if possible

#### **Proof Of Concept**

```solidity
File: src/maia/libraries/DateTimeLib.sol

40:         uint256 epochDay = timestamp / 86400;

57:             uint256 day = timestamp / 86400;

```

```solidity
File: src/talos/libraries/PoolVariables.sol

247:             ? int256((cache.amount0Desired - cache.amount0) / 2)

248:             : int256((cache.amount1Desired - cache.amount1) / 2); // always positive. "overflow" safe convertion cuz we are dividing by 2

```

### [GAS-12] Use != 0 instead of > 0 for unsigned integer comparison

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Gauges.sol

417:         if (weight > 0) {

441:         if (weight > 0) {

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

250:         if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

2: pragma solidity >=0.8.0;

52:             require(_weights[i] > 0);

```

```solidity
File: src/maia/PartnerUtilityManager.sol

75:         if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {

85:         if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {

95:         if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {

```

```solidity
File: src/rewards/base/FlywheelCore.sol

127:         if (oldRewardBalance > 0) {

162:         if (strategyRewardsAccrued > 0) {

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

234:         if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);

```

```solidity
File: src/talos/TalosStrategyVanilla.sol

139:         if (_liquidity > 0) {

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

166:         if (amount0 < amount0Desired) {

226:         if (amount0 < amount0Desired) {

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

153:         if (claimed > 0) {

191:             if (oldBandwidth > 0) {

256:                     if (oldBandwidth > 0) {

272:             if (oldBandwidth > 0) {

282:                     } else if (leftOverBandwidth > 0) {

911:             if (updateAmount > 0) {

1048:             if (updateAmount > 0) {

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

47:         require(_weight > 0);

```

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

111:                 if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

160:         if (gasRemaining > 0) {

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

118:         if (_deposit > 0) {

123:         if (_amount - _deposit > 0) {

137:             if (_deposits[i] > 0) {

144:             if (_amounts[i] - _deposits[i] > 0) {

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

471:             (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));

474:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

496:             (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));

499:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

518:             (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));

521:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

540:             (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));

543:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

623:             if (_amounts[i] - _deposits[i] > 0) {

627:             if (_deposits[i] > 0) {

979:         if (_amount - _deposit > 0) {

983:         if (_deposit > 0) {

1328:         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

248:         if (_amount - _deposit > 0) {

252:         if (_deposit > 0) {

268:             if (_deposits[i] > 0) {

275:             if (_amounts[i] - _deposits[i] > 0) {

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

57:                 || (_dParams.amount > 0 && !IPort(_localPortAddress).isLocalToken(_dParams.hToken, _fromChain)) //Check local exists.

58:                 || (_dParams.deposit > 0 && !IPort(_localPortAddress).isUnderlyingToken(_dParams.token, _fromChain)) //Check underlying exists.

301:         if (localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0)) {

347:             if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();

451:         if (_amount - _deposit > 0) {

457:         if (_deposit > 0) {

651:         address(data.tokenIn).safeTransfer(msg.sender, uint256(amount0 > 0 ? amount0 : amount1));

750:             if (_initialGas > 0) {

757:         if (_initialGas > 0) {

1161:             if (initialGas > 0) {

1171:         if (initialGas > 0) {

1240:         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

282:         if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);

283:         if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

199:         if (incentive.numberOfStakes > 0) revert EndIncentiveWhileStakesArePresent();

271:         if (reward > 0) hermes.safeTransfer(to, reward);

281:         if (reward > 0) hermes.safeTransfer(to, reward);

```

```solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

31:         if (boostTotalSupply > 0) {

```

### [GAS-13] Use bitmaps to save gas

#### Description:

Use mapping(uint256 => uint256) instead of mapping(uint256 => bool)

#### **Proof Of Concept**

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

157:     mapping(uint256 => bool) public isBranchBridgeAgentAllowed;

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

58:     mapping(uint256 => bool) public isChainId;

```

#### Recommended Mitigation Steps:

Use mapping(uint256 => uint256) instead of mapping(uint256 => bool)

### [GAS-14] USE BYTES32 INSTEAD OF STRING

#### Description:

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

#### **Proof Of Concept**

```solidity
File: src/erc-4626/ERC4626.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

39:     constructor(address[] memory _assets, uint256[] memory _weights, string memory _name, string memory _symbol)

```

```solidity
File: src/erc-4626/UlyssesERC4626.sol

24:     constructor(address _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, 18) {

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

9:     string public constant name = "vMaia Governor Bravo";

43:         keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

107:         string[] memory signatures,

109:         string memory description

193:         string memory signature,

274:             string[] memory signatures,

336:     function castVoteWithReason(uint256 proposalId, uint8 support, string calldata reason) external {

```

```solidity
File: src/governance/GovernorBravoInterfaces.sol

11:         string[] signatures,

15:         string description

24:     event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

117:         string[] signatures;

177:         string calldata signature,

184:         string calldata signature,

191:         string calldata signature,

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

54:         string memory _name,

55:         string memory _symbol,

69:             string.concat(_name, " - Burned Hermes: Aggregated Gov + Yield + Boost"),

70:             string.concat(_symbol, "-bHermes")

```

```solidity
File: src/maia/vMaia.sol

52:         string memory _name,

53:         string memory _symbol,

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

83:         string memory _name,

84:         string memory _symbol,

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

25:         string memory _name,

26:         string memory _symbol,

```

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

28:         string calldata name,

29:         string calldata symbol,

```

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

49:         string memory name = ERC20(_underlyingAddress).name();

50:         string memory symbol = ERC20(_underlyingAddress).symbol();

```

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

65:         string memory name = ERC20(_underlyingAddress).name();

66:         string memory symbol = ERC20(_underlyingAddress).symbol();

97:         string memory _name,

98:         string memory _symbol,

238:             (address globalAddress, string memory name, string memory symbol, uint128 gasToBridgeOut) =

239:                 abi.decode(_data[1:], (address, string, string, uint128));

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

181:         string memory _name,

182:         string memory _symbol,

366:             (address underlyingAddress, address localAddress, string memory name, string memory symbol) =

367:                 abi.decode(_encodedData, (address, address, string, string));

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

425:         string memory _wrappedGasTokenName,

426:         string memory _wrappedGasTokenSymbol,

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

60:     function createToken(string memory _name, string memory _symbol)

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

54:     function createToken(string memory _name, string memory _symbol)

```

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallProxy.sol

16:         string calldata _to,

```

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol

22:     function createToken(string memory _name, string memory _symbol) external returns (ERC20hTokenBranch newToken);

```

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol

22:     function createToken(string memory _name, string memory _symbol) external returns (ERC20hTokenRoot newToken);

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootPort.sol

285:         string memory _wrappedGasTokenName,

286:         string memory _wrappedGasTokenSymbol,

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol

12:     constructor(string memory _name, string memory _symbol, address _owner)

13:         ERC20(string(string.concat("Hermes - ", _name)), string(string.concat("h-", _symbol)), 18)

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

42:         string memory _name,

43:         string memory _symbol

44:     ) ERC20(string(string.concat("Hermes ", _name)), string(string.concat("h-", _symbol)), 18) {

```
