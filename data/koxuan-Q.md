# Report


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Expressions for constant values such as a call to keccak256(), should use immutable rather than constant | 6 |
| [NC-2](#NC-2) | Constants in comparisons should appear on the left side | 163 |
| [NC-3](#NC-3) | delete keyword can be used instead of setting to 0 | 33 |
| [NC-4](#NC-4) | Lines are too long | 4 |
| [NC-5](#NC-5) | Constants should be defined rather than using magic numbers | 8 |
| [NC-6](#NC-6) | Variable names don't follow the Solidity style guide | 23 |
### <a name="NC-1"></a>[NC-1] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant
constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

*Instances (6)*:
```solidity
File: governance/GovernorBravoDelegateMaia.sol

46:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

48:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

### <a name="NC-2"></a>[NC-2] Constants in comparisons should appear on the left side
Constants should appear on the left side of comparisons, to avoid accidental assignment

*Instances (163)*:
```solidity
File: erc-20/ERC20Gauges.sol

464:         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts

```

```solidity
File: erc-20/ERC20MultiVotes.sol

49:         return pos == 0 ? 0 : _checkpoints[account][pos - 1].votes;

77:         return high == 0 ? 0 : ckpts[high - 1].votes;

105:         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts

169:         if (count == 1) {

191:         if (delegatee == address(0) || free < amount || amount == 0) revert DelegationError();

224:         if (newDelegates == 0) {

247:         uint256 oldWeight = pos == 0 ? 0 : ckpts[pos - 1].votes;

```

```solidity
File: erc-4626/ERC4626.sol

109:         return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());

116:         return supply == 0 ? shares : shares.mulDiv(totalAssets(), supply);

128:         return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);

135:         return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets());

```

```solidity
File: erc-4626/ERC4626DepositOnly.sol

71:         return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());

78:         return supply == 0 ? shares : shares.mulDiv(totalAssets(), supply);

90:         return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

48:         if (length != _assets.length || length == 0) revert InvalidLength();

51:             require(ERC20(_assets[i]).decimals() == 18);

172:             if (assetsAmounts[i] == 0) revert ZeroAssets();

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

146:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

310:         } else if (proposal.eta == 0) {

369:         if (support == 0) {

371:         } else if (support == 1) {

373:         } else if (support == 2) {

476:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

```

```solidity
File: hermes/UtilityManager.sol

70:         if (amount == 0) return;

79:         if (amount == 0) return;

88:         if (amount == 0) return;

111:         if (amount == 0) return;

120:         if (amount == 0) return;

129:         if (amount == 0) return;

```

```solidity
File: maia/libraries/DateTimeLib.sol

59:             result = ((day + 3) % 7) + 1 == 2;

```

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

189:         if (factory.vaultIds(IBaseVault(newPartnerVault)) == 0) revert UnrecognizedVault();

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

312:         } else if (proposal.eta == 0) {

371:         if (support == 0) {

373:         } else if (support == 1) {

375:         } else if (support == 2) {

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

312:         } else if (proposal.eta == 0) {

371:         if (support == 0) {

373:         } else if (support == 1) {

375:         } else if (support == 2) {

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

312:         } else if (proposal.eta == 0) {

371:         if (support == 0) {

373:         } else if (support == 1) {

375:         } else if (support == 2) {

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

312:         } else if (proposal.eta == 0) {

371:         if (support == 0) {

373:         } else if (support == 1) {

375:         } else if (support == 2) {

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

148:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");

312:         } else if (proposal.eta == 0) {

371:         if (support == 0) {

373:         } else if (support == 1) {

375:         } else if (support == 2) {

478:         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");

```

```solidity
File: rewards/base/FlywheelCore.sol

77:         if (index == 0) return 0;

87:         if (index == 0) return (0, 0);

116:         require(strategyIndex[strategy] == 0, "strategy");

190:         if (supplierIndex == 0) {

```

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

128:         if (offset == 0) {

174:         if (size == 0) revert EmptyGaugesError();

183:             assert(queuedRewards.storedCycle == 0 || queuedRewards.storedCycle >= lastCycle);

210:         if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {

210:         if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {

```

```solidity
File: talos/TalosOptimizer.sol

44:         if (_priceImpactPercentage >= 1e6 || _priceImpactPercentage == 0) {

47:         if (_maxTotalSupply == 0) revert MaxTotalSupplyIsZero();

63:         if (_maxTotalSupply == 0) revert MaxTotalSupplyIsZero();

86:         if (_priceImpactPercentage >= 1e6 || _priceImpactPercentage == 0) {

```

```solidity
File: talos/TalosStrategyStaked.sol

141:         if (liquidity == 0) return; // can't unstake when liquidity is zero

174:         if (liquidity == 0) return; // can't stake when liquidity is zero

```

```solidity
File: talos/TalosStrategyVanilla.sol

105:         if (liquidity == 0) return; // no fees to collect when liquidity is zero

```

```solidity
File: talos/base/TalosBaseStrategy.sol

151:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

151:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

212:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

212:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

215:         shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;

252:         if (shares == 0) revert RedeemingZeroShares();

273:             if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

273:             if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

335:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

335:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

351:         if (_liquidity == 0) return;

```

```solidity
File: talos/factories/TalosBaseStrategyFactory.sol

61:         if (optimizerFactory.optimizerIds(TalosOptimizer(address(optimizer))) == 0) {

```

```solidity
File: ulysses-amm/UlyssesPool.sol

160:         if (weight == 0) revert InvalidWeight();

167:         if (destinationId == 0) revert NotUlyssesLP();

224:         if (weight == 0) revert InvalidWeight();

228:         if (poolIndex == 0) revert NotUlyssesLP();

```

```solidity
File: ulysses-amm/UlyssesToken.sol

46:         require(ERC20(asset).decimals() == 18);

66:         if (newAssetsLength == 0) revert CannotRemoveLastAsset();

```

```solidity
File: ulysses-omnichain/ArbitrumCoreBranchRouter.sol

118:         if (_data[0] == 0x02) {

131:         } else if (_data[0] == 0x03) {

137:         } else if (_data[0] == 0x04) {

142:         } else if (_data[0] == 0x05) {

147:         } else if (_data[0] == 0x06) {

```

```solidity
File: ulysses-omnichain/BaseBranchRouter.sol

147:         require(_unlocked == 1);

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

332:         if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {

1143:         if (flag == 0x00) {

1165:         } else if (flag == 0x01) {

1189:         } else if (flag == 0x02) {

1243:         if ((flag == 0x00) || (flag == 0x01) || (flag == 0x02)) {

1243:         if ((flag == 0x00) || (flag == 0x01) || (flag == 0x02)) {

1243:         if ((flag == 0x00) || (flag == 0x01) || (flag == 0x02)) {

1258:         } else if (flag == 0x03) {

1272:         } else if ((flag == 0x04) || (flag == 0x05)) {

1272:         } else if ((flag == 0x04) || (flag == 0x05)) {

1287:         } else if (flag == 0x06) {

1341:         return _decimals == 18 ? _amount : _amount * (10 ** _decimals) / 1 ether;

1367:         require(_unlocked == 1);

```

```solidity
File: ulysses-omnichain/BranchPort.sol

389:         return _decimals == 18 ? _amount : _amount * 1 ether / (10 ** _decimals);

424:         require(_unlocked == 1);

```

```solidity
File: ulysses-omnichain/CoreBranchRouter.sol

237:         if (_data[0] == 0x01) {

243:         } else if (_data[0] == 0x02) {

257:         } else if (_data[0] == 0x03) {

262:         } else if (_data[0] == 0x04) {

267:         } else if (_data[0] == 0x05) {

272:         } else if (_data[0] == 0x06) {

```

```solidity
File: ulysses-omnichain/CoreRootRouter.sol

331:         if (_funcId == 0x03) {

337:         } else if (_funcId == 0x04) {

358:         if (_funcId == 0x01) {

365:         } else if (_funcId == 0x02) {

439:         require(_unlocked == 1);

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

194:         if (funcId == 0x01) {

200:         } else if (funcId == 0x02) {

216:         } else if (funcId == 0x03) {

277:         if (funcId == 0x01) {

284:         } else if (funcId == 0x02) {

304:         } else if (funcId == 0x03) {

353:         if (funcId == 0x01) {

360:         } else if (funcId == 0x02) {

380:         } else if (funcId == 0x03) {

429:         if (funcId == 0x01) {

436:         } else if (funcId == 0x02) {

456:         } else if (funcId == 0x03) {

495:         require(_unlocked == 1);

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

246:         if (initialGas == 0) {

648:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

648:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

914:         if (flag == 0x00) {

939:         } else if (flag == 0x01) {

964:         } else if (flag == 0x02) {

988:         } else if (flag == 0x03) {

1012:         } else if (flag == 0x04) {

1048:         } else if (flag == 0x05) {

1083:         } else if (flag == 0x06) {

1118:         } else if (flag == 0x07) {

1141:         } else if (flag == 0x08) {

1197:         if (flag == 0x00) {

1202:         } else if (flag == 0x01) {

1207:         } else if (flag == 0x02) {

1234:         if (initialGas == 0) revert GasErrorOrRepeatedTx();

1274:         require(_unlocked == 1);

```

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

198:         if (refund == 0) revert EndIncentiveNoRefundAvailable();

310:             if (liquidity == 0) revert TokenNotStaked();

411:             if (liquidity == 0) revert TokenNotStaked();

483:         if (incentives[incentiveId].totalRewardUnclaimed == 0) revert NonExistentIncentiveError();

486:         if (liquidity == 0) revert NoLiquidityError();

```

### <a name="NC-3"></a>[NC-3] delete keyword can be used instead of setting to 0
It's clearer and reflects the intention of the programmer

*Instances (33)*:
```solidity
File: erc-20/ERC20Boost.sol

249:         getUserBoost[msg.sender] = 0;

```

```solidity
File: governance/GovernorBravoDelegateMaia.sol

149:         newProposal.eta = 0;

156:         newProposal.forVotes = 0;

157:         newProposal.againstVotes = 0;

158:         newProposal.abstainVotes = 0;

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

151:         newProposal.eta = 0;

158:         newProposal.forVotes = 0;

159:         newProposal.againstVotes = 0;

160:         newProposal.abstainVotes = 0;

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

151:         newProposal.eta = 0;

158:         newProposal.forVotes = 0;

159:         newProposal.againstVotes = 0;

160:         newProposal.abstainVotes = 0;

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

151:         newProposal.eta = 0;

158:         newProposal.forVotes = 0;

159:         newProposal.againstVotes = 0;

160:         newProposal.abstainVotes = 0;

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

151:         newProposal.eta = 0;

158:         newProposal.forVotes = 0;

159:         newProposal.againstVotes = 0;

160:         newProposal.abstainVotes = 0;

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

151:         newProposal.eta = 0;

158:         newProposal.forVotes = 0;

159:         newProposal.againstVotes = 0;

160:         newProposal.abstainVotes = 0;

```

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

202:         incentive.totalRewardUnclaimed = 0;

268:             rewards[msg.sender] = 0;

279:         rewards[msg.sender] = 0;

406:                 _userAttachements[owner][key.pool] = 0;

426:         deposit.stakedTimestamp = 0;

454:         stake.secondsPerLiquidityInsideInitialX128 = 0;

455:         stake.liquidityNoOverflow = 0;

456:         if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

```

### <a name="NC-4"></a>[NC-4] Lines are too long
Recommended by solidity docs to keep lines to 120 characters or lesser

*Instances (4)*:
```solidity
File: erc-20/ERC20Gauges.sol

77:             return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

138:     uint256 internal constant MIN_EXECUTION_OVERHEAD = 160_000; // 100_000 for anycall + 35_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Executions

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

181:     uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution

```

```solidity
File: uni-v3-staker/libraries/IncentiveTime.sol

13:     uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))

```

### <a name="NC-5"></a>[NC-5] Constants should be defined rather than using magic numbers

*Instances (8)*:
```solidity
File: maia/libraries/DateTimeLib.sol

47:             let doy := sub(doe, sub(add(mul(365, yoe), shr(2, yoe)), div(yoe, 100)))

```

```solidity
File: ulysses-amm/UlyssesPool.sol

400:                 let weight := shr(248, slot)

490:                     let weight := shr(248, sload(add(bandwidthStateListStart, mul(i, 2))))

559:             weight := shr(248, slot)

669:             sstore(destinationState.slot, or(bandwidth, shl(248, weight)))

702:             let sigma2 := shr(192, feeSlot)

706:             lambda2 := and(shr(64, feeSlot), 0xffffffffffffffff)

```

```solidity
File: uni-v3-staker/libraries/IncentiveTime.sol

13:     uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))

```

### <a name="NC-6"></a>[NC-6] Variable names don't follow the Solidity style guide
Constant variable should be all caps  each word should use all capital letters, with underscores separating each word (CONSTANT_CASE)

*Instances (23)*:
```solidity
File: governance/GovernorBravoDelegateMaia.sol

9:     string public constant name = "vMaia Governor Bravo";

30:     uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

33:     uint256 public constant proposalMaxOperations = 10; // 10 actions

```

```solidity
File: hermes/minters/BaseV2Minter.sol

24:     uint256 internal constant week = 86400 * 7;

26:     uint256 internal constant base = 1000;

28:     uint256 internal constant max_tail_emission = 100;

29:     uint256 internal constant max_dao_share = 300;

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

8:     string public constant name = "bHermes Governor Bravo Severity 1";

29:     uint256 public constant quorumVotes = 0.04 ether; // 4% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

8:     string public constant name = "bHermes Governor Bravo Severity 2";

29:     uint256 public constant quorumVotes = 0.125 ether; // 12.5% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

8:     string public constant name = "bHermes Governor Bravo Severity 3";

29:     uint256 public constant quorumVotes = 0.25 ether; // 25,000,000 = about 25% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

8:     string public constant name = "bHermes Governor Bravo Severity 4";

29:     uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

8:     string public constant name = "bHermes Governor Bravo Severity 5";

29:     uint256 public constant quorumVotes = 0.5 ether; // 50% of GovToken

32:     uint256 public constant proposalMaxOperations = 10; // 10 actions

```

```solidity
File: talos/TalosStrategyVanilla.sol

47:     uint24 private constant protocolFee = 2 * 1e5; //20%

```


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) |  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 6 |
| [L-2](#L-2) | Do not use deprecated library functions | 13 |
| [L-3](#L-3) | Divide before Multiplication | 11 |
| [L-4](#L-4) | Empty Function Body - Consider commenting why | 31 |
| [L-5](#L-5) | Initializers could be front-run | 26 |
| [L-6](#L-6) | ERC20 tokens that do not implement optional decimals method cannot be used | 20 |
| [L-7](#L-7) | _safeMint() should be used rather than _mint() | 21 |
| [L-8](#L-8) | Unsafe ERC20 operation(s) | 17 |
| [L-9](#L-9) | Unspecific compiler version pragma | 11 |
### <a name="L-1"></a>[L-1]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*Instances (6)*:
```solidity
File: governance/GovernorBravoDelegateMaia.sol

348:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

350:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

350:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

350:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

350:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

350:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

### <a name="L-2"></a>[L-2] Do not use deprecated library functions

*Instances (13)*:
```solidity
File: gauges/UniswapV3Gauge.sol

45:         rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);

```

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

195:         address(gaugeWeight).safeApprove(oldPartnerVault, 0);

196:         address(gaugeBoost).safeApprove(oldPartnerVault, 0);

197:         address(governance).safeApprove(oldPartnerVault, 0);

198:         address(partnerGovernance).safeApprove(oldPartnerVault, 0);

200:         address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);

201:         address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);

202:         address(governance).safeApprove(newPartnerVault, type(uint256).max);

203:         address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);

```

```solidity
File: rewards/base/BaseFlywheelRewards.sol

36:         _rewardToken.safeApprove(address(_flywheel), type(uint256).max);

```

```solidity
File: talos/base/TalosBaseStrategy.sol

130:         address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

131:         address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

```

```solidity
File: ulysses-amm/UlyssesRouter.sol

40:             address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);

```

### <a name="L-3"></a>[L-3] Divide before Multiplication
Unnecessary loss of precision caused by divide before multiplication

*Instances (11)*:
```solidity
File: erc-20/ERC20Gauges.sol

77:             return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow

```

```solidity
File: gauges/BaseV2Gauge.sol

68:         epoch = (block.timestamp / WEEK) * WEEK;

84:         uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;

```

```solidity
File: hermes/minters/BaseV2Minter.sol

82:         activePeriod = (block.timestamp / week) * week;

127:             _period = (block.timestamp / week) * week;

```

```solidity
File: rewards/rewards/FlywheelAcummulatedRewards.sol

50:             uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

```

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

60:         gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

79:         uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

114:         uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

```

```solidity
File: ulysses-omnichain/BranchPort.sol

389:         return _decimals == 18 ? _amount : _amount * 1 ether / (10 ** _decimals);

```

```solidity
File: uni-v3-staker/libraries/IncentiveTime.sol

20:         return uint96(((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) * INCENTIVES_DURATION + INCENTIVES_OFFSET);

```

### <a name="L-4"></a>[L-4] Empty Function Body - Consider commenting why

*Instances (31)*:
```solidity
File: erc-4626/ERC4626.sol

171:     function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}

173:     function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

```

```solidity
File: erc-4626/ERC4626DepositOnly.sol

111:     function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

292:     function beforeWithdraw(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}

294:     function afterDeposit(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}

```

```solidity
File: hermes/bHermes.sol

62:     {}

```

```solidity
File: hermes/minters/BaseV2Minter.sol

152:             try flywheelGaugeRewards.queueRewardsForCycle() {} catch {}

152:             try flywheelGaugeRewards.queueRewardsForCycle() {} catch {}

```

```solidity
File: maia/vMaia.sol

91:     function claimBoost(uint256 amount) public override {}

```

```solidity
File: rewards/FlywheelCoreInstant.sol

38:     ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}

```

```solidity
File: rewards/FlywheelCoreStrategy.sol

37:     ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}

```

```solidity
File: rewards/rewards/FlywheelBribeRewards.sol

29:     {}

```

```solidity
File: talos/TalosManager.sol

96:         try strategy.pool().checkDeviation(optimizer.maxTwapDeviation(), optimizer.twapDuration()) {}

```

```solidity
File: talos/TalosStrategyStaked.sol

179:         } catch {}

```

```solidity
File: talos/TalosStrategyVanilla.sol

64:     ) TalosStrategySimple(_pool, _optimizer, _nonfungiblePositionManager, _strategyManager, _owner) {}

79:     function afterRedeem(uint256 _tokenId) internal override {}

90:     function afterDeposit(uint256 _tokenId) internal override {}

100:     function afterRerange(uint256 _tokenId) internal override {}

```

```solidity
File: talos/factories/TalosStrategyVanillaFactory.sol

26:     {}

```

```solidity
File: talos/strategies/TalosStrategySimple.sol

24:     ) TalosBaseStrategy(_pool, _strategy, _nonfungiblePositionManager, _strategyManager, _owner) {}

```

```solidity
File: ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

90:     {}

181:     function _replenishGas(uint256) internal override {}

```

```solidity
File: ulysses-omnichain/ArbitrumCoreBranchRouter.sol

40:     {}

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

1328:         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

1328:         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

1419:     fallback() external payable {}

```

```solidity
File: ulysses-omnichain/CoreBranchRouter.sol

284:     fallback() external payable {}

```

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

1240:         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

1240:         if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

1334:     fallback() external payable {}

```

```solidity
File: ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

52:     {}

```

### <a name="L-5"></a>[L-5] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (26)*:
```solidity
File: governance/GovernorBravoDelegateMaia.sol

56:     function initialize(

```

```solidity
File: governance/GovernorBravoDelegator.sol

23:                 "initialize(address,address,uint256,uint256,uint256)",

```

```solidity
File: hermes/interfaces/IBaseV2Minter.sol

52:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external;

```

```solidity
File: hermes/minters/BaseV2Minter.sol

51:     address internal initializer;

59:         initializer = msg.sender;

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

79:         if (initializer != msg.sender) revert NotInitializer();

81:         initializer = address(0);

126:         if (block.timestamp >= _period + week && initializer == address(0)) {

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity1.sol

58:     function initialize(

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity2.sol

58:     function initialize(

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity3.sol

58:     function initialize(

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity4.sol

58:     function initialize(

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegateSeverity5.sol

58:     function initialize(

```

```solidity
File: out-of-scope/governance/GovernorBravoDelegator.sol

22:                 "initialize(address,address,uint256,uint256,uint256)",

```

```solidity
File: talos/base/TalosBaseStrategy.sol

102:     function init(uint256 amount0Desired, uint256 amount1Desired, address receiver)

```

```solidity
File: talos/interfaces/ITalosBaseStrategy.sol

106:     function init(uint256 amount0Desired, uint256 amount1Desired, address receiver)

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

### <a name="L-6"></a>[L-6] ERC20 tokens that do not implement optional decimals method cannot be used
Underlying token that does not implement optional decimals method cannot be used 
 
 > [EIP-20](https://eips.ethereum.org/EIPS/eip-20#decimals) OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.

*Instances (20)*:
```solidity
File: erc-4626/ERC4626.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```

```solidity
File: erc-4626/ERC4626DepositOnly.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

51:             require(ERC20(_assets[i]).decimals() == 18);

```

```solidity
File: erc-4626/UlyssesERC4626.sol

27:         if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();

```

```solidity
File: ulysses-amm/UlyssesToken.sol

46:         require(ERC20(asset).decimals() == 18);

```

```solidity
File: ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

104:             msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())

```

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

72:         underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));

82:             _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

120:                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

141:                     _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

```

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

252:             _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),

284:             _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

342:                         getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

357:                         getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

687:             _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),

720:             deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

1355:             deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals());

```

```solidity
File: ulysses-omnichain/BranchPort.sol

212:             _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

254:                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

272:                     _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

```

### <a name="L-7"></a>[L-7] _safeMint() should be used rather than _mint()
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function

*Instances (21)*:
```solidity
File: erc-4626/ERC4626.sol

39:         _mint(receiver, shares);

53:         _mint(receiver, shares);

```

```solidity
File: erc-4626/ERC4626DepositOnly.sol

39:         _mint(receiver, shares);

53:         _mint(receiver, shares);

```

```solidity
File: erc-4626/ERC4626MultiToken.sol

105:         _mint(receiver, shares);

124:         _mint(receiver, shares);

```

```solidity
File: erc-4626/UlyssesERC4626.sol

42:         _mint(receiver, shares);

54:         _mint(receiver, shares);

```

```solidity
File: hermes/bHermes.sol

128:     function _mint(address to, uint256 amount) internal virtual override {

132:         super._mint(to, amount);

```

```solidity
File: hermes/tokens/HERMES.sol

63:         _mint(account, amount);

```

```solidity
File: hermes/tokens/bHermesBoost.sol

29:         _mint(to, amount);

```

```solidity
File: hermes/tokens/bHermesGauges.sol

36:         _mint(to, amount);

```

```solidity
File: hermes/tokens/bHermesVotes.sol

27:         _mint(to, amount);

```

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

240:     function _mint(address to, uint256 amount) internal virtual override {

245:         super._mint(to, amount);

```

```solidity
File: maia/tokens/Maia.sol

56:         _mint(account, amount);

```

```solidity
File: talos/base/TalosBaseStrategy.sol

157:         _mint(receiver, shares);

218:         _mint(receiver, shares);

```

```solidity
File: ulysses-omnichain/token/ERC20hTokenBranch.sol

24:         _mint(account, amount);

```

```solidity
File: ulysses-omnichain/token/ERC20hTokenRoot.sol

74:         _mint(to, amount);

```

### <a name="L-8"></a>[L-8] Unsafe ERC20 operation(s)

*Instances (17)*:
```solidity
File: erc-20/ERC20Boost.sol

313:         return super.transfer(to, amount);

329:         return super.transferFrom(from, to, amount);

```

```solidity
File: erc-20/ERC20Gauges.sol

498:         return super.transfer(to, amount);

510:         return super.transferFrom(from, to, amount);

```

```solidity
File: erc-20/ERC20MultiVotes.sol

293:         return super.transfer(to, amount);

305:         return super.transferFrom(from, to, amount);

```

```solidity
File: hermes/bHermes.sol

148:         return super.transfer(to, amount);

166:         return super.transferFrom(from, to, amount);

```

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

270:         return super.transfer(to, amount);

286:         return super.transferFrom(from, to, amount);

```

```solidity
File: talos/TalosStrategyStaked.sol

91:         return super.transfer(_to, _amount);

96:         return super.transferFrom(_from, _to, _amount);

```

```solidity
File: talos/base/TalosBaseStrategy.sol

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

120:         ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

148:             ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);

```

```solidity
File: ulysses-omnichain/VirtualAccount.sol

37:         ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);

```

### <a name="L-9"></a>[L-9] Unspecific compiler version pragma

*Instances (11)*:
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
File: talos/TalosStrategyStaked.sol

2: pragma solidity >=0.8.0;

```

```solidity
File: talos/TalosStrategyVanilla.sol

3: pragma solidity >=0.8.0;

```

```solidity
File: talos/base/TalosBaseStrategy.sol

3: pragma solidity >=0.8.0;

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


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Centralization Risk for trusted owners | 111 |
### <a name="M-1"></a>[M-1] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (111)*:
```solidity
File: erc-20/ERC20Boost.sol

19: abstract contract ERC20Boost is ERC20, Ownable, IERC20Boost {

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

20: abstract contract ERC20MultiVotes is ERC20, Ownable, IERC20MultiVotes {

96:     function setMaxDelegates(uint256 newMax) external onlyOwner {

104:     function setContractExceedMaxDelegates(address account, bool canExceedMax) external onlyOwner {

```

```solidity
File: gauges/BaseV2Gauge.sol

20: abstract contract BaseV2Gauge is Ownable, IBaseV2Gauge {

128:     function addBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {

144:     function removeBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {

```

```solidity
File: gauges/UniswapV3Gauge.sol

62:     function setMinimumWidth(uint24 _minimumWidth) external onlyOwner {

```

```solidity
File: gauges/factories/BaseV2GaugeFactory.sol

18: abstract contract BaseV2GaugeFactory is Ownable, IBaseV2GaugeFactory {

109:     function createGauge(address strategy, bytes memory data) external onlyOwner {

130:     function removeGauge(BaseV2Gauge gauge) external onlyOwner {

```

```solidity
File: gauges/factories/BaseV2GaugeManager.sol

14: contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {

110:     function addGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {

121:     function removeGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {

```

```solidity
File: gauges/factories/BribesFactory.sol

18: contract BribesFactory is Ownable, IBribesFactory {

```

```solidity
File: gauges/factories/UniswapV3GaugeFactory.sol

98:     function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {

```

```solidity
File: hermes/minters/BaseV2Minter.sol

16: contract BaseV2Minter is Ownable, IBaseV2Minter {

86:     function setDao(address _dao) external onlyOwner {

92:     function setDaoShare(uint256 _daoShare) external onlyOwner {

98:     function setTailEmission(uint256 _tail_emission) external onlyOwner {

```

```solidity
File: hermes/tokens/HERMES.sol

47: contract HERMES is ERC20, Ownable {

62:     function mint(address account, uint256 amount) external onlyOwner {

```

```solidity
File: maia/factories/PartnerManagerFactory.sol

12: contract PartnerManagerFactory is Ownable, IPartnerManagerFactory {

58:     function addPartner(PartnerManager newPartnerManager) external onlyOwner {

67:     function addVault(IBaseVault newVault) external onlyOwner {

80:     function removePartner(PartnerManager partnerManager) external onlyOwner {

89:     function removeVault(IBaseVault vault) external onlyOwner {

```

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

22: abstract contract ERC4626PartnerManager is PartnerUtilityManager, Ownable, ERC4626, IERC4626PartnerManager {

188:     function migratePartnerVault(address newPartnerVault) external onlyOwner {

216:     function increaseConversionRate(uint256 newRate) external onlyOwner {

```

```solidity
File: maia/tokens/Maia.sol

40: contract Maia is ERC20, Ownable {

55:     function mint(address account, uint256 amount) external onlyOwner {

```

```solidity
File: rewards/base/FlywheelCore.sol

15: abstract contract FlywheelCore is Ownable, IFlywheelCore {

111:     function addStrategyForRewards(ERC20 strategy) external onlyOwner {

125:     function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {

137:     function setBooster(IFlywheelBooster newBooster) external onlyOwner {

```

```solidity
File: rewards/depots/MultiRewardsDepot.sol

11: contract MultiRewardsDepot is Ownable, RewardsDepot, IMultiRewardsDepot {

47:     function addAsset(address rewardsContract, address asset) external onlyOwner {

57:     function removeAsset(address rewardsContract) external onlyOwner {

```

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

18: contract FlywheelGaugeRewards is Ownable, IFlywheelGaugeRewards {

```

```solidity
File: talos/TalosOptimizer.sol

10: contract TalosOptimizer is Ownable, ITalosOptimizer {

62:     function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {

68:     function setTwapDuration(uint32 _twapDuration) external onlyOwner {

74:     function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {

80:     function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {

85:     function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {

```

```solidity
File: talos/base/TalosBaseStrategy.sol

24: abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBaseStrategy {

394:     function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {

```

```solidity
File: talos/boost-aggregator/BoostAggregator.sol

19: contract BoostAggregator is Ownable, IBoostAggregator {

143:     function addWhitelistedAddress(address user) external onlyOwner {

148:     function removeWhitelistedAddress(address user) external onlyOwner {

153:     function setProtocolFee(uint256 _protocolFee) external onlyOwner {

159:     function withdrawProtocolFees(address to) external onlyOwner {

165:     function withdrawAllGaugeBoost(address to) external onlyOwner {

172:     function withdrawGaugeBoost(address to, uint256 amount) external onlyOwner {

180:     function decrementGaugesBoostIndexed(uint256 boost, uint256 offset, uint256 num) external onlyOwner {

```

```solidity
File: talos/factories/TalosBaseStrategyFactory.sol

17: abstract contract TalosBaseStrategyFactory is Ownable, ITalosBaseStrategyFactory {

```

```solidity
File: ulysses-amm/UlyssesPool.sol

19: contract UlyssesPool is UlyssesERC4626, Ownable, IUlyssesPool {

159:     function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {

223:     function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {

308:     function setFees(Fees calldata _fees) external nonReentrant onlyOwner {

```

```solidity
File: ulysses-amm/UlyssesToken.sol

15: contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {

44:     function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {

60:     function removeAsset(address asset) external nonReentrant onlyOwner {

88:     function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {

```

```solidity
File: ulysses-amm/factories/UlyssesFactory.sol

38: contract UlyssesFactory is Ownable, IUlyssesFactory {

65:     function renounceOwnership() public payable override onlyOwner {

```

```solidity
File: ulysses-omnichain/BaseBranchRouter.sol

21: contract BaseBranchRouter is IBranchRouter, Ownable {

37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {

```

```solidity
File: ulysses-omnichain/BranchBridgeAgentExecutor.sol

25: contract BranchBridgeAgentExecutor is Ownable {

```

```solidity
File: ulysses-omnichain/BranchPort.sol

16: contract BranchPort is Ownable, IBranchPort {

99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

113:     function renounceOwnership() public payable override onlyOwner {

```

```solidity
File: ulysses-omnichain/CoreRootRouter.sol

38: contract CoreRootRouter is IRootRouter, Ownable {

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

232:     ) external payable onlyOwner {

280:     ) external payable onlyOwner {

307:     ) external payable onlyOwner {

```

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

47: contract MulticallRootRouter is IRootRouter, Ownable {

74:     function initialize(address _bridgeAgentAddress) external onlyOwner {

```

```solidity
File: ulysses-omnichain/RootBridgeAgentExecutor.sol

26: contract RootBridgeAgentExecutor is Ownable {

264:     ) external onlyOwner returns (bool success, bytes memory result) {

```

```solidity
File: ulysses-omnichain/RootPort.sol

20: contract RootPort is Ownable, IRootPort {

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

144:     ) external onlyOwner {

158:     function forefeitOwnership(address _owner) external onlyOwner {

165:     function renounceOwnership() public payable override onlyOwner {

399:     function toggleBridgeAgent(address _bridgeAgent) external onlyOwner {

406:     function addBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {

413:     function toggleBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {

433:     ) external onlyOwner {

484:     function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external onlyOwner {

491:     function addEcosystemToken(address _ecoTokenGlobalAddress) external onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

16: contract BranchBridgeAgentFactory is Ownable, IBranchBridgeAgentFactory {

83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

11: contract ERC20hTokenBranchFactory is Ownable, IERC20hTokenBranchFactory {

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```

```solidity
File: ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

12: contract ERC20hTokenRootFactory is Ownable, IERC20hTokenRootFactory {

40:     function initialize(address _coreRouter) external onlyOwner {

```

```solidity
File: ulysses-omnichain/token/ERC20hTokenBranch.sol

11: contract ERC20hTokenBranch is ERC20, Ownable, IERC20hTokenBranch {

23:     function mint(address account, uint256 amount) external override onlyOwner returns (bool) {

29:     function burn(uint256 value) public override onlyOwner {

```

