# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Using bools for storage incurs overhead | 26 |
| [GAS-2](#GAS-2) | Cache array length outside of loop | 44 |
| [GAS-3](#GAS-3) | Use Custom Errors | 199 |
| [GAS-4](#GAS-4) | Don't initialize variables with default value | 71 |
| [GAS-5](#GAS-5) | Long revert strings | 143 |
| [GAS-6](#GAS-6) | Functions guaranteed to revert when called by normal users can be marked `payable` | 89 |
| [GAS-7](#GAS-7) | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 78 |
| [GAS-8](#GAS-8) | Using `private` rather than `public` for constants, saves gas | 80 |
| [GAS-9](#GAS-9) | Use shift Right/Left instead of division/multiplication if possible | 4 |
| [GAS-10](#GAS-10) | Splitting require() statements that use && saves gas | 1 |
| [GAS-11](#GAS-11) | Use != 0 instead of > 0 for unsigned integer comparison | 75 |
### <a name="GAS-1"></a>[GAS-1] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (26)*:
```solidity
File: erc-20/ERC20Gauges.sol

395:     mapping(address => bool) public override canContractExceedMaxGauges;

```

```solidity
File: erc-20/ERC20MultiVotes.sol

93:     mapping(address => bool) public override canContractExceedMaxDelegates;

```

```solidity
File: gauges/BaseV2Gauge.sol

35:     mapping(FlywheelCore => bool) public override isActive;

38:     mapping(FlywheelCore => bool) public override added;

```

```solidity
File: gauges/factories/BaseV2GaugeFactory.sol

39:     mapping(BaseV2Gauge => bool) public override activeGauges;

```

```solidity
File: gauges/factories/BaseV2GaugeManager.sol

35:     mapping(BaseV2GaugeFactory => bool) public activeGaugeFactories;

```

```solidity
File: gauges/factories/BribesFactory.sol

35:     mapping(FlywheelCore => bool) public activeBribeFlywheels;

```

```solidity
File: rewards/depots/MultiRewardsDepot.sol

20:     mapping(address => bool) private _isRewardsContract;

23:     mapping(address => bool) private _isAsset;

```

```solidity
File: talos/TalosStrategyStaked.sol

52:     bool private stakeFlag = false;

```

```solidity
File: talos/base/TalosBaseStrategy.sol

54:     bool public initialized;

```

```solidity
File: talos/boost-aggregator/BoostAggregator.sol

48:     mapping(address => bool) public whitelistedAddresses;

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

129:     mapping(uint32 => bool) public executionHistory;

```

```solidity
File: ulysses-omnichain/BranchPort.sol

27:     mapping(address => bool) public isBridgeAgent;

40:     mapping(address => bool) public isBridgeAgentFactory;

54:     mapping(address => bool) public isStrategyToken;

71:     mapping(address => mapping(address => bool)) public isPortStrategy;

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

157:     mapping(uint256 => bool) public isBranchBridgeAgentAllowed;

174:     mapping(uint256 => mapping(uint32 => bool)) public executionHistory;

643:     mapping(address => bool) private approvedGasPool;

```

```solidity
File: ulysses-omnichain/RootPort.sol

51:     mapping(VirtualAccount => mapping(address => bool)) public isRouterApproved;

58:     mapping(uint256 => bool) public isChainId;

61:     mapping(address => bool) public isBridgeAgent;

77:     mapping(address => bool) public isBridgeAgentFactory;

90:     mapping(address => bool) public isGlobalAddress;

126:     bool internal _setup;

```

### <a name="GAS-2"></a>[GAS-2] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (44)*:
```solidity
File: governance/GovernorBravoDelegateMaia.sol

181:         for (uint256 i = 0; i < proposal.targets.length; i++) {

215:         for (uint256 i = 0; i < proposal.targets.length; i++) {

251:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: ulysses-amm/UlyssesPool.sol

130:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

232:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

253:             for (uint256 i = 1; i < bandwidthStateList.length;) {

278:             for (uint256 i = 1; i < bandwidthStateList.length;) {

296:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

```

```solidity
File: ulysses-amm/UlyssesToken.sol

95:         for (uint256 i = 0; i < assets.length; i++) {

111:         for (uint256 i = 0; i < assets.length; i++) {

```

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

101:         for (uint256 i = 0; i < _localAddresses.length;) {

136:         for (uint256 i = 0; i < _localAddresses.length;) {

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

283:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

719:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

951:         for (uint256 i = 0; i < deposit.hTokens.length;) {

1354:         for (uint256 i = 0; i < _deposits.length; i++) {

```

```solidity
File: ulysses-omnichain/BranchPort.sol

231:         for (uint256 i = 0; i < _localAddresses.length;) {

267:         for (uint256 i = 0; i < _localAddresses.length;) {

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

146:         for (uint256 i = 0; i < outputTokens.length;) {

311:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

387:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

463:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

342:         for (uint256 i = 0; i < _globalAddresses.length;) {

408:         for (uint256 i = 0; i < _dParams.hTokens.length;) {

561:         for (uint256 i = 0; i < newGas.length;) {

596:         for (uint256 i = 0; i < settlement.hTokens.length;) {

889:             _userFeeInfo.gasToBridgeOut = uint128(bytes16(data[data.length - PARAMS_GAS_OUT:data.length]));

895:             _userFeeInfo.depositedGas = uint128(bytes16(data[data.length - 32:data.length - 16]));

```

```solidity
File: ulysses-omnichain/VirtualAccount.sol

48:         for (uint256 i = 0; i < calls.length; i++) {

```

### <a name="GAS-3"></a>[GAS-3] Use Custom Errors
[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (199)*:
```solidity
File: erc-20/ERC20MultiVotes.sol

364:         require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");

375:         require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");

```

```solidity
File: erc-4626/ERC4626.sol

34:         require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

88:         require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");

```

```solidity
File: erc-4626/ERC4626DepositOnly.sol

34:         require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

100:         require((shares = previewDeposit(assetsAmounts)) != 0, "ZERO_SHARES");

```

```solidity
File: erc-4626/UlyssesERC4626.sol

40:         require(shares != 0, "ZERO_SHARES");

50:         require(assets != 0, "ZERO_ASSETS");

75:         require(assets != 0, "ZERO_ASSETS");

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

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

527:         require(c >= a, "addition overflow");

532:         require(b <= a, "subtraction underflow");

```

```solidity
File: governance/GovernorBravoDelegator.sol

42:         require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

529:         require(c >= a, "addition overflow");

534:         require(b <= a, "subtraction underflow");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

529:         require(c >= a, "addition overflow");

534:         require(b <= a, "subtraction underflow");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

529:         require(c >= a, "addition overflow");

534:         require(b <= a, "subtraction underflow");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

529:         require(c >= a, "addition overflow");

534:         require(b <= a, "subtraction underflow");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

529:         require(c >= a, "addition overflow");

534:         require(b <= a, "subtraction underflow");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegator.sol

41:         require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");

```

```solidity
File: rewards/base/FlywheelCore.sol

116:         require(strategyIndex[strategy] == 0, "strategy");

```

```solidity
File: ulysses-amm/factories/UlyssesFactory.sol

61:         require(_owner != address(0), "Owner cannot be 0");

66:         revert("Cannot renounce ownership");

```

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

34:         require(_rootPortAddress != address(0), "Root Port Address cannot be 0");

```

```solidity
File: ulysses-omnichain/BaseBranchRouter.sol

38:         require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

151:         require(_rootBridgeAgentAddress != address(0), "Root Bridge Agent Address cannot be the zero address.");

152:         require(_localAnyCallAddress != address(0), "AnyCall Address cannot be the zero address.");

153:         require(_localAnyCallExecutorAddress != address(0), "AnyCall Executor Address cannot be the zero address.");

154:         require(_localRouterAddress != address(0), "Local Router Address cannot be the zero address.");

155:         require(_localPortAddress != address(0), "Local Port Address cannot be the zero address.");

```

```solidity
File: ulysses-omnichain/BranchPort.sol

95:         require(_owner != address(0), "Owner is zero address");

100:         require(coreBranchRouterAddress == address(0), "Contract already initialized");

101:         require(!isBridgeAgentFactory[_bridgeAgentFactory], "Contract already initialized");

103:         require(_coreBranchRouter != address(0), "CoreBranchRouter is zero address");

104:         require(_bridgeAgentFactory != address(0), "BridgeAgentFactory is zero address");

114:         revert("Cannot renounce ownership");

302:         require(coreBranchRouterAddress != address(0), "CoreRouter address is zero");

303:         require(_newCoreRouter != address(0), "New CoreRouter address is zero");

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

65:         require(_localPortAddress != address(0), "Local Port Address cannot be 0");

66:         require(_multicallAddress != address(0), "Multicall Address cannot be 0");

75:         require(_bridgeAgentAddress != address(0), "Bridge Agent Address cannot be 0");

509:         require(msg.sender == bridgeAgentExecutorAddress, "Unauthorized Caller");

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

210:         require(address(_wrappedNativeToken) != address(0), "Wrapped native token cannot be zero address");

211:         require(_daoAddress != address(0), "DAO cannot be zero address");

212:         require(_localAnyCallAddress != address(0), "Anycall Address cannot be zero address");

213:         require(_localAnyCallExecutorAddress != address(0), "Anycall Executor Address cannot be zero address");

214:         require(_localPortAddress != address(0), "Port Address cannot be zero address");

215:         require(_localRouterAddress != address(0), "Router Address cannot be zero address");

```

```solidity
File: ulysses-omnichain/RootPort.sol

115:         require(_wrappedNativeToken != address(0), "Invalid wrapped native token address.");

129:         require(_setup, "Setup ended.");

130:         require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");

131:         require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");

145:         require(_setup, "Setup ended.");

146:         require(isBridgeAgent[_coreRootBridgeAgent], "Core Bridge Agent doesn't exist.");

147:         require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");

148:         require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");

149:         require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");

159:         require(_owner != address(0), "Owner cannot be 0 address.");

166:         revert("Cannot renounce ownership");

```

```solidity
File: ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

64:         require(_rootBridgeAgentFactoryAddress != address(0), "Root Bridge Agent Factory Address cannot be 0");

65:         require(address(_wrappedNativeToken) != address(0), "Wrapped Native Token cannot be 0");

66:         require(_localAnyCallAddress != address(0), "Anycall Address cannot be 0");

67:         require(_localAnyCallExecutorAddress != address(0), "Anyexec Address cannot be 0");

68:         require(_localCoreBranchRouterAddress != address(0), "Core Branch Router Address cannot be 0");

69:         require(_localPortAddress != address(0), "Port Address cannot be 0");

70:         require(_owner != address(0), "Owner cannot be 0");

84:         require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0");

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

28:         require(_localPortAddress != address(0), "Port address cannot be 0");

36:         require(_coreRouter != address(0), "CoreRouter address cannot be 0");

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

34:         require(_rootPortAddress != address(0), "Root Port Address cannot be 0");

41:         require(_coreRouter != address(0), "CoreRouter address cannot be 0");

```

```solidity
File: ulysses-omnichain/factories/RootBridgeAgentFactory.sol

55:         require(address(_wrappedNativeToken) != address(0), "Wrapped Native Token cannot be 0");

56:         require(_rootPortAddress != address(0), "Root Port Address cannot be 0");

57:         require(_daoAddress != address(0), "DAO Address cannot be 0");

```

```solidity
File: ulysses-omnichain/token/ERC20hTokenRoot.sol

45:         require(_rootPortAddress != address(0), "Root Port Address cannot be 0");

46:         require(_factoryAddress != address(0), "Factory Address cannot be 0");

```

### <a name="GAS-4"></a>[GAS-4] Don't initialize variables with default value

*Instances (71)*:
```solidity
File: erc-20/ERC20Boost.sol

52:         for (uint256 i = 0; i < num;) {

98:         for (uint256 i = 0; i < num;) {

151:         uint256 userBoost = 0;

156:         for (uint256 i = 0; i < length;) {

207:         for (uint256 i = 0; i < num && i < length;) {

236:         for (uint256 i = 0; i < size;) {

```

```solidity
File: erc-20/ERC20Gauges.sol

114:         for (uint256 i = 0; i < num;) {

155:         for (uint256 i = 0; i < num;) {

259:         for (uint256 i = 0; i < size;) {

338:         for (uint256 i = 0; i < size;) {

536:         for (uint256 i = 0; i < size && (userFreeWeight + totalFreed) < weight;) {

```

```solidity
File: erc-20/ERC20MultiVotes.sol

67:         uint256 low = 0;

328:         for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

50:         for (uint256 i = 0; i < length;) {

68:         for (uint256 i = 0; i < length;) {

79:         for (uint256 i = 0; i < length;) {

170:         for (uint256 i = 0; i < length;) {

201:         for (uint256 i = 0; i < length;) {

216:         for (uint256 i = 0; i < length;) {

235:         for (uint256 i = 0; i < length;) {

250:         for (uint256 i = 0; i < length;) {

```

```solidity
File: gauges/BaseV2Gauge.sol

114:         for (uint256 i = 0; i < length;) {

```

```solidity
File: gauges/factories/BaseV2GaugeFactory.sol

77:         for (uint256 i = 0; i < length;) {

```

```solidity
File: gauges/factories/BaseV2GaugeManager.sol

64:         for (uint256 i = 0; i < length;) {

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

181:         for (uint256 i = 0; i < proposal.targets.length; i++) {

215:         for (uint256 i = 0; i < proposal.targets.length; i++) {

251:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

176:         for (uint256 i = 0; i < size; i++) {

```

```solidity
File: ulysses-amm/UlyssesRouter.sol

78:         for (uint256 i = 0; i < length;) {

```

```solidity
File: ulysses-amm/UlyssesToken.sol

95:         for (uint256 i = 0; i < assets.length; i++) {

111:         for (uint256 i = 0; i < assets.length; i++) {

```

```solidity
File: ulysses-amm/factories/UlyssesFactory.sol

99:         for (uint256 i = 0; i < length;) {

107:         for (uint256 i = 0; i < length;) {

110:             for (uint256 j = 0; j < length;) {

123:         for (uint256 i = 0; i < length;) {

145:         for (uint256 i = 0; i < length;) {

```

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

101:         for (uint256 i = 0; i < _localAddresses.length;) {

136:         for (uint256 i = 0; i < _localAddresses.length;) {

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

283:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

580:         for (uint256 i = 0; i < numOfAssets;) {

719:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

951:         for (uint256 i = 0; i < deposit.hTokens.length;) {

1354:         for (uint256 i = 0; i < _deposits.length; i++) {

```

```solidity
File: ulysses-omnichain/BranchPort.sol

231:         for (uint256 i = 0; i < _localAddresses.length;) {

267:         for (uint256 i = 0; i < _localAddresses.length;) {

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

146:         for (uint256 i = 0; i < outputTokens.length;) {

311:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

387:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

463:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

342:         for (uint256 i = 0; i < _globalAddresses.length;) {

408:         for (uint256 i = 0; i < _dParams.hTokens.length;) {

561:         for (uint256 i = 0; i < newGas.length;) {

596:         for (uint256 i = 0; i < settlement.hTokens.length;) {

```

```solidity
File: ulysses-omnichain/RootBridgeAgentExecutor.sol

364:         for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {

```

```solidity
File: ulysses-omnichain/VirtualAccount.sol

48:         for (uint256 i = 0; i < calls.length; i++) {

```

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

456:         if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

```

### <a name="GAS-5"></a>[GAS-5] Long revert strings

*Instances (143)*:
```solidity
File: erc-20/ERC20MultiVotes.sol

364:         require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

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
File: governance/GovernorBravoDelegator.sol

42:         require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

65:         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");

66:         require(msg.sender == admin, "GovernorBravo::initialize: admin only");

67:         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");

68:         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");

114:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");

125:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");

126:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

230:         require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

352:         require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");

364:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

365:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

368:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

400:         require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");

416:         require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");

433:         require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");

464:         require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");

477:         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

491:         require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegator.sol

41:         require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

151:         require(_rootBridgeAgentAddress != address(0), "Root Bridge Agent Address cannot be the zero address.");

152:         require(_localAnyCallAddress != address(0), "AnyCall Address cannot be the zero address.");

153:         require(_localAnyCallExecutorAddress != address(0), "AnyCall Executor Address cannot be the zero address.");

154:         require(_localRouterAddress != address(0), "Local Router Address cannot be the zero address.");

155:         require(_localPortAddress != address(0), "Local Port Address cannot be the zero address.");

```

```solidity
File: ulysses-omnichain/BranchPort.sol

104:         require(_bridgeAgentFactory != address(0), "BridgeAgentFactory is zero address");

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

210:         require(address(_wrappedNativeToken) != address(0), "Wrapped native token cannot be zero address");

212:         require(_localAnyCallAddress != address(0), "Anycall Address cannot be zero address");

213:         require(_localAnyCallExecutorAddress != address(0), "Anycall Executor Address cannot be zero address");

214:         require(_localPortAddress != address(0), "Port Address cannot be zero address");

215:         require(_localRouterAddress != address(0), "Router Address cannot be zero address");

```

```solidity
File: ulysses-omnichain/RootPort.sol

115:         require(_wrappedNativeToken != address(0), "Invalid wrapped native token address.");

130:         require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");

131:         require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");

147:         require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");

148:         require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");

149:         require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");

```

```solidity
File: ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

64:         require(_rootBridgeAgentFactoryAddress != address(0), "Root Bridge Agent Factory Address cannot be 0");

68:         require(_localCoreBranchRouterAddress != address(0), "Core Branch Router Address cannot be 0");

84:         require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0");

```

### <a name="GAS-6"></a>[GAS-6] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (89)*:
```solidity
File: erc-20/ERC20Boost.sol

259:     function addGauge(address gauge) external onlyOwner {

273:     function removeGauge(address gauge) external onlyOwner {

285:     function replaceGauge(address oldGauge, address newGauge) external onlyOwner {

```

```solidity
File: erc-20/ERC20Gauges.sol

398:     function addGauge(address gauge) external onlyOwner returns (uint112) {

425:     function removeGauge(address gauge) external onlyOwner {

449:     function replaceGauge(address oldGauge, address newGauge) external onlyOwner {

455:     function setMaxGauges(uint256 newMax) external onlyOwner {

463:     function setContractExceedMaxGauges(address account, bool canExceedMax) external onlyOwner {

```

```solidity
File: erc-20/ERC20MultiVotes.sol

96:     function setMaxDelegates(uint256 newMax) external onlyOwner {

104:     function setContractExceedMaxDelegates(address account, bool canExceedMax) external onlyOwner {

```

```solidity
File: gauges/BaseV2Gauge.sol

101:     function attachUser(address user) external onlyStrategy {

106:     function detachUser(address user) external onlyStrategy {

128:     function addBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {

144:     function removeBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {

```

```solidity
File: gauges/UniswapV3Gauge.sol

62:     function setMinimumWidth(uint24 _minimumWidth) external onlyOwner {

```

```solidity
File: gauges/factories/BaseV2GaugeFactory.sol

109:     function createGauge(address strategy, bytes memory data) external onlyOwner {

130:     function removeGauge(BaseV2Gauge gauge) external onlyOwner {

144:     function addBribeToGauge(BaseV2Gauge gauge, address bribeToken) external onlyOwnerOrBribesFactoryOwner {

151:     function removeBribeFromGauge(BaseV2Gauge gauge, address bribeToken) external onlyOwnerOrBribesFactoryOwner {

```

```solidity
File: gauges/factories/BaseV2GaugeManager.sol

94:     function addGauge(address gauge) external onlyActiveGaugeFactory {

100:     function removeGauge(address gauge) external onlyActiveGaugeFactory {

110:     function addGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {

121:     function removeGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {

137:     function changebHermesGaugeOwner(address newOwner) external onlyAdmin {

145:     function changeAdmin(address newAdmin) external onlyAdmin {

```

```solidity
File: gauges/factories/BribesFactory.sol

72:     function addGaugetoFlywheel(address gauge, address bribeToken) external onlyGaugeFactory {

```

```solidity
File: gauges/factories/UniswapV3GaugeFactory.sol

98:     function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {

```

```solidity
File: hermes/minters/BaseV2Minter.sol

86:     function setDao(address _dao) external onlyOwner {

92:     function setDaoShare(uint256 _daoShare) external onlyOwner {

98:     function setTailEmission(uint256 _tail_emission) external onlyOwner {

```

```solidity
File: hermes/tokens/HERMES.sol

62:     function mint(address account, uint256 amount) external onlyOwner {

```

```solidity
File: hermes/tokens/bHermesBoost.sol

28:     function mint(address to, uint256 amount) external onlybHermes {

```

```solidity
File: hermes/tokens/bHermesGauges.sol

35:     function mint(address to, uint256 amount) external onlybHermes {

```

```solidity
File: hermes/tokens/bHermesVotes.sol

26:     function mint(address to, uint256 amount) external onlybHermes {

35:     function burn(address from, uint256 amount) external onlybHermes {

```

```solidity
File: maia/factories/PartnerManagerFactory.sol

58:     function addPartner(PartnerManager newPartnerManager) external onlyOwner {

67:     function addVault(IBaseVault newVault) external onlyOwner {

80:     function removePartner(PartnerManager partnerManager) external onlyOwner {

89:     function removeVault(IBaseVault vault) external onlyOwner {

```

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

188:     function migratePartnerVault(address newPartnerVault) external onlyOwner {

216:     function increaseConversionRate(uint256 newRate) external onlyOwner {

```

```solidity
File: maia/tokens/Maia.sol

55:     function mint(address account, uint256 amount) external onlyOwner {

```

```solidity
File: rewards/base/FlywheelCore.sol

111:     function addStrategyForRewards(ERC20 strategy) external onlyOwner {

125:     function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {

137:     function setBooster(IFlywheelBooster newBooster) external onlyOwner {

```

```solidity
File: rewards/depots/MultiRewardsDepot.sol

38:     function getRewards() external override(RewardsDepot, IMultiRewardsDepot) onlyFlywheelRewards returns (uint256) {

47:     function addAsset(address rewardsContract, address asset) external onlyOwner {

57:     function removeAsset(address rewardsContract) external onlyOwner {

```

```solidity
File: rewards/depots/SingleRewardsDepot.sol

32:     function getRewards() external override onlyFlywheelRewards returns (uint256 balance) {

```

```solidity
File: rewards/rewards/FlywheelAcummulatedRewards.sol

42:     function getAccruedRewards(ERC20 strategy) external override onlyFlywheel returns (uint256 amount) {

```

```solidity
File: rewards/rewards/FlywheelInstantRewards.sol

33:     function getAccruedRewards() external override onlyFlywheel returns (uint256) {

```

```solidity
File: talos/TalosOptimizer.sol

62:     function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {

68:     function setTwapDuration(uint32 _twapDuration) external onlyOwner {

74:     function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {

80:     function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {

85:     function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {

```

```solidity
File: talos/base/TalosBaseStrategy.sol

298:     function rerange() external virtual override nonReentrant checkDeviation onlyStrategyManager {

311:     function rebalance() external virtual override nonReentrant checkDeviation onlyStrategyManager {

394:     function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {

```

```solidity
File: talos/boost-aggregator/BoostAggregator.sol

143:     function addWhitelistedAddress(address user) external onlyOwner {

148:     function removeWhitelistedAddress(address user) external onlyOwner {

153:     function setProtocolFee(uint256 _protocolFee) external onlyOwner {

159:     function withdrawProtocolFees(address to) external onlyOwner {

165:     function withdrawAllGaugeBoost(address to) external onlyOwner {

172:     function withdrawGaugeBoost(address to, uint256 amount) external onlyOwner {

180:     function decrementGaugesBoostIndexed(uint256 boost, uint256 offset, uint256 num) external onlyOwner {

```

```solidity
File: ulysses-amm/UlyssesPool.sol

159:     function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {

223:     function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {

308:     function setFees(Fees calldata _fees) external nonReentrant onlyOwner {

```

```solidity
File: ulysses-amm/UlyssesToken.sol

44:     function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {

60:     function removeAsset(address asset) external nonReentrant onlyOwner {

88:     function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {

```

```solidity
File: ulysses-omnichain/BaseBranchRouter.sol

37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {

```

```solidity
File: ulysses-omnichain/BranchPort.sol

99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

```

```solidity
File: ulysses-omnichain/CoreRootRouter.sol

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

74:     function initialize(address _bridgeAgentAddress) external onlyOwner {

```

```solidity
File: ulysses-omnichain/RootPort.sol

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

158:     function forefeitOwnership(address _owner) external onlyOwner {

399:     function toggleBridgeAgent(address _bridgeAgent) external onlyOwner {

406:     function addBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {

413:     function toggleBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {

484:     function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external onlyOwner {

491:     function addEcosystemToken(address _ecoTokenGlobalAddress) external onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

40:     function initialize(address _coreRouter) external onlyOwner {

```

```solidity
File: ulysses-omnichain/token/ERC20hTokenBranch.sol

23:     function mint(address account, uint256 amount) external override onlyOwner returns (bool) {

29:     function burn(uint256 value) public override onlyOwner {

```

### <a name="GAS-7"></a>[GAS-7] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
*Saves 5 gas per loop*

*Instances (78)*:
```solidity
File: erc-20/ERC20Boost.sol

55:                 i++;

101:                 i++;

166:                 i++;

224:                 i++;

245:                 i++;

```

```solidity
File: erc-20/ERC20Gauges.sol

117:                 i++;

158:                 i++;

266:                 i++;

346:                 i++;

548:                     i++;

```

```solidity
File: erc-20/ERC20MultiVotes.sol

328:         for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {

375:         require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

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
File: gauges/BaseV2Gauge.sol

118:                 i++;

```

```solidity
File: gauges/factories/BaseV2GaugeFactory.sol

81:                 i++;

97:                 i++;

```

```solidity
File: gauges/factories/BaseV2GaugeManager.sol

68:                 i++;

84:                 i++;

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

142:         proposalCount++;

181:         for (uint256 i = 0; i < proposal.targets.length; i++) {

215:         for (uint256 i = 0; i < proposal.targets.length; i++) {

251:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

144:         proposalCount++;

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

144:         proposalCount++;

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

144:         proposalCount++;

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

144:         proposalCount++;

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

144:         proposalCount++;

183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

176:         for (uint256 i = 0; i < size; i++) {

```

```solidity
File: ulysses-amm/UlyssesPool.sol

130:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

175:         for (uint256 i = 1; i < index; i++) {

211:         for (uint256 i = 1; i <= index; i++) {

232:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

296:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

```

```solidity
File: ulysses-amm/UlyssesToken.sol

95:         for (uint256 i = 0; i < assets.length; i++) {

111:         for (uint256 i = 0; i < assets.length; i++) {

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

283:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

426:             bytes1(0x07), depositNonce++, _settlementNonce, msg.value.toUint128(), _gasToBoostSettlement

719:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {

938:         return depositNonce++;

1354:         for (uint256 i = 0; i < _deposits.length; i++) {

```

```solidity
File: ulysses-omnichain/BranchPort.sol

109:         bridgeAgentFactoriesLenght++;

280:                 i++;

293:         bridgeAgentsLenght++;

311:         bridgeAgentFactoriesLenght++;

334:         strategyTokensLenght++;

355:         portStrategiesLenght++;

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

278:             abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));

633:         return settlementNonce++;

```

```solidity
File: ulysses-omnichain/RootPort.sol

135:         bridgeAgentFactoriesLenght++;

370:         bridgeAgentsLenght++;

407:         bridgeAgentFactories[bridgeAgentsLenght++] = _bridgeAgentFactory;

```

```solidity
File: ulysses-omnichain/VirtualAccount.sol

48:         for (uint256 i = 0; i < calls.length; i++) {

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

45:         hTokensLenght++;

67:         hTokensLenght++;

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

67:         hTokensLenght++;

```

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

502:         incentives[incentiveId].numberOfStakes++;

```

### <a name="GAS-8"></a>[GAS-8] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (80)*:
```solidity
File: erc-20/ERC20MultiVotes.sol

360:     bytes32 public constant DELEGATION_TYPEHASH =

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

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
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

8:     string public constant name = "bHermes Governor Bravo Severity 1";

11:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.001 ether; // 0.1% of GovToken

14:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.01 ether; // 1% of GovToken

17:     uint256 public constant MIN_VOTING_PERIOD = 5760; // About 24 hours

20:     uint256 public constant MAX_VOTING_PERIOD = 40320; // About 1 week

23:     uint256 public constant MIN_VOTING_DELAY = 1;

26:     uint256 public constant MAX_VOTING_DELAY = 40320; // About 1 week

29:     uint256 public constant quorumVotes = 0.04 ether; // 4% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

35:     uint256 public constant DIVISIONER = 1 ether;

44:     bytes32 public constant DOMAIN_TYPEHASH =

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

8:     string public constant name = "bHermes Governor Bravo Severity 2";

11:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.0025 ether; // 0.25% of GovToken

14:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.025 ether; // 2.5% of GovToken

17:     uint256 public constant MIN_VOTING_PERIOD = 11520; // About 48 hours

20:     uint256 public constant MAX_VOTING_PERIOD = 80640; // About 2 weeks

23:     uint256 public constant MIN_VOTING_DELAY = 11520; // About 48 hours

26:     uint256 public constant MAX_VOTING_DELAY = 40320; // About 1 week

29:     uint256 public constant quorumVotes = 0.125 ether; // 12.5% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

35:     uint256 public constant DIVISIONER = 1 ether;

44:     bytes32 public constant DOMAIN_TYPEHASH =

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

8:     string public constant name = "bHermes Governor Bravo Severity 3";

11:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

14:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

17:     uint256 public constant MIN_VOTING_PERIOD = 40320; // About 1 weeks

20:     uint256 public constant MAX_VOTING_PERIOD = 120960; // About 3 weeks

23:     uint256 public constant MIN_VOTING_DELAY = 23040; // About 4 days

26:     uint256 public constant MAX_VOTING_DELAY = 40320; // About 1 week

29:     uint256 public constant quorumVotes = 0.25 ether; // 25,000,000 = about 25% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

35:     uint256 public constant DIVISIONER = 1 ether;

44:     bytes32 public constant DOMAIN_TYPEHASH =

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

8:     string public constant name = "bHermes Governor Bravo Severity 4";

11:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.0075 ether; // 0.75% of GovToken

14:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.075 ether; // 7.5% of GovToken

17:     uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

20:     uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

23:     uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

26:     uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

29:     uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

35:     uint256 public constant DIVISIONER = 1 ether;

44:     bytes32 public constant DOMAIN_TYPEHASH =

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

8:     string public constant name = "bHermes Governor Bravo Severity 5";

11:     uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.01 ether; // 1% of GovToken

14:     uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.1 ether; // 10% of GovToken

17:     uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

20:     uint256 public constant MAX_VOTING_PERIOD = 241920; // About 6 weeks

23:     uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

26:     uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

29:     uint256 public constant quorumVotes = 0.5 ether; // 50% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

35:     uint256 public constant DIVISIONER = 1 ether;

44:     bytes32 public constant DOMAIN_TYPEHASH =

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: ulysses-omnichain/lib/AnycallFlags.sol

8:     uint256 public constant FLAG_NONE = 0x0;

9:     uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;

10:     uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;

11:     uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;

12:     uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;

15:     uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;

16:     uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```

### <a name="GAS-9"></a>[GAS-9] Use shift Right/Left instead of division/multiplication if possible

*Instances (4)*:
```solidity
File: maia/libraries/DateTimeLib.sol

40:         uint256 epochDay = timestamp / 86400;

57:             uint256 day = timestamp / 86400;

```

```solidity
File: talos/libraries/PoolVariables.sol

247:             ? int256((cache.amount0Desired - cache.amount0) / 2)

248:             : int256((cache.amount1Desired - cache.amount1) / 2); // always positive. "overflow" safe convertion cuz we are dividing by 2

```

### <a name="GAS-10"></a>[GAS-10] Splitting require() statements that use && saves gas

*Instances (1)*:
```solidity
File: talos/base/TalosBaseStrategy.sol

408:         require(balance0 >= amount0 && balance1 >= amount1);

```

### <a name="GAS-11"></a>[GAS-11] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (75)*:
```solidity
File: erc-20/ERC20Gauges.sol

417:         if (weight > 0) {

441:         if (weight > 0) {

```

```solidity
File: erc-20/ERC20MultiVotes.sol

250:         if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {

```

```solidity
File: erc-4626/ERC4626.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: erc-4626/ERC4626DepositOnly.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

2: pragma solidity >=0.8.0;

52:             require(_weights[i] > 0);

```

```solidity
File: erc-4626/UlyssesERC4626.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: erc-4626/interfaces/IERC4626.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: maia/PartnerUtilityManager.sol

75:         if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {

85:         if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {

95:         if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {

```

```solidity
File: rewards/base/FlywheelCore.sol

127:         if (oldRewardBalance > 0) {

162:         if (strategyRewardsAccrued > 0) {

```

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

234:         if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);

```

```solidity
File: talos/TalosStrategyStaked.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: talos/TalosStrategyVanilla.sol

3: pragma solidity >=0.8.0;

139:         if (_liquidity > 0) {

```

```solidity
File: talos/base/TalosBaseStrategy.sol

3: pragma solidity >=0.8.0;

166:         if (amount0 < amount0Desired) {

226:         if (amount0 < amount0Desired) {

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

```solidity
File: talos/boost-aggregator/BoostAggregator.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: talos/interfaces/IBoostAggregator.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: talos/strategies/TalosStrategySimple.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: ulysses-amm/UlyssesPool.sol

153:         if (claimed > 0) {

191:             if (oldBandwidth > 0) {

256:                     if (oldBandwidth > 0) {

272:             if (oldBandwidth > 0) {

282:                     } else if (leftOverBandwidth > 0) {

911:             if (updateAmount > 0) {

1048:             if (updateAmount > 0) {

```

```solidity
File: ulysses-amm/UlyssesToken.sol

47:         require(_weight > 0);

```

```solidity
File: ulysses-amm/factories/UlyssesFactory.sol

111:                 if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```

```solidity
File: ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

160:         if (gasRemaining > 0) {

```

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

118:         if (_deposit > 0) {

123:         if (_amount - _deposit > 0) {

137:             if (_deposits[i] > 0) {

144:             if (_amounts[i] - _deposits[i] > 0) {

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

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
File: ulysses-omnichain/BranchPort.sol

248:         if (_amount - _deposit > 0) {

252:         if (_deposit > 0) {

268:             if (_deposits[i] > 0) {

275:             if (_amounts[i] - _deposits[i] > 0) {

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

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
File: ulysses-omnichain/RootPort.sol

282:         if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);

283:         if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);

```

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

199:         if (incentive.numberOfStakes > 0) revert EndIncentiveWhileStakesArePresent();

271:         if (reward > 0) hermes.safeTransfer(to, reward);

281:         if (reward > 0) hermes.safeTransfer(to, reward);

```

```solidity
File: uni-v3-staker/libraries/RewardMath.sol

31:         if (boostTotalSupply > 0) {

```

