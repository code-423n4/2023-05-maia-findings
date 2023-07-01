## Non Critical Issues

|       | Issue                                                                                    |
| ----- | :--------------------------------------------------------------------------------------- |
| NC-1  | ADD A TIMELOCK TO CRITICAL FUNCTIONS                                                     |
| NC-2  | ADD TO BLACKLIST FUNCTION                                                                |
| NC-3  | Avoid passing as reference the `chainid`                                                 |
| NC-4  | Boolean equality                                                                         |
| NC-5  | USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)                                     |
| NC-6  | GENERATE PERFECT CODE HEADERS EVERY TIME                                                 |
| NC-7  | INCONSISTENT SOLIDITY VERSIONS                                                           |
| NC-8  | MARK VISIBILITY OF INITIALIZE(…) FUNCTIONS AS EXTERNAL                                   |
| NC-9  | CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT |
| NC-10 | LACK OF EVENT EMISSION AFTER CRITICAL INITIALIZE() FUNCTIONS                             |
| NC-11 | LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION                                    |
| NC-12 | FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION                            |
| NC-13 | NO SAME VALUE INPUT CONTROL                                                              |
| NC-14 | Omissions in events                                                                      |
| NC-15 | SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC                                       |
| NC-16 | USE A MORE RECENT VERSION OF SOLIDITY                                                    |
| NC-17 | FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS                  |

### [NC-1] ADD A TIMELOCK TO CRITICAL FUNCTIONS

#### Description:

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Gauges.sol

455:     function setMaxGauges(uint256 newMax) external onlyOwner {

463:     function setContractExceedMaxGauges(address account, bool canExceedMax) external onlyOwner {

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

96:     function setMaxDelegates(uint256 newMax) external onlyOwner {

104:     function setContractExceedMaxDelegates(address account, bool canExceedMax) external onlyOwner {

```

```solidity
File: src/erc-20/interfaces/IERC20Gauges.sol

234:     function setMaxGauges(uint256 newMax) external;

239:     function setContractExceedMaxGauges(address account, bool canExceedMax) external;

```

```solidity
File: src/erc-20/interfaces/IERC20MultiVotes.sol

81:     function setMaxDelegates(uint256 newMax) external;

86:     function setContractExceedMaxDelegates(address account, bool canExceedMax) external;

```

```solidity
File: src/gauges/UniswapV3Gauge.sol

62:     function setMinimumWidth(uint24 _minimumWidth) external onlyOwner {

```

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

98:     function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {

```

```solidity
File: src/gauges/interfaces/IUniswapV3Gauge.sol

76:     function setMinimumWidth(uint24 _minimumWidth) external;

```

```solidity
File: src/gauges/interfaces/IUniswapV3GaugeFactory.sol

60:     function setMinimumWidth(address gauge, uint24 minimumWidth) external;

```

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

62:     function setTailEmission(uint256 _tail_emission) external;

68:     function setDao(address _dao) external;

74:     function setDaoShare(uint256 _dao_share) external;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

86:     function setDao(address _dao) external onlyOwner {

92:     function setDaoShare(uint256 _daoShare) external onlyOwner {

98:     function setTailEmission(uint256 _tail_emission) external onlyOwner {

```

```solidity
File: src/rewards/base/FlywheelCore.sol

125:     function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {

137:     function setBooster(IFlywheelBooster newBooster) external onlyOwner {

```

```solidity
File: src/rewards/interfaces/IFlywheelBribeRewards.sol

28:     function setRewardsDepot(RewardsDepot rewardsDepot) external;

```

```solidity
File: src/rewards/interfaces/IFlywheelCore.sol

95:     function setFlywheelRewards(address newFlywheelRewards) external;

98:     function setBooster(IFlywheelBooster newBooster) external;

```

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

37:     function setRewardsDepot(RewardsDepot rewardsDepot) external {

```

```solidity
File: src/talos/TalosOptimizer.sol

62:     function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {

68:     function setTwapDuration(uint32 _twapDuration) external onlyOwner {

74:     function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {

80:     function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {

85:     function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {

```

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

100:     function setOwnRewardsDepot(address rewardsDepot) external {

153:     function setProtocolFee(uint256 _protocolFee) external onlyOwner {

```

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

64:     function setOwnRewardsDepot(address rewardsDepot) external;

96:     function setProtocolFee(uint256 _protocolFee) external;

```

```solidity
File: src/talos/interfaces/ITalosOptimizer.sol

36:     function setMaxTotalSupply(uint256 _maxTotalSupply) external;

42:     function setTwapDuration(uint32 _twapDuration) external;

48:     function setMaxTwapDeviation(int24 _maxTwapDeviation) external;

54:     function setTickRange(int24 _tickRangeMultiplier) external;

60:     function setPriceImpact(uint24 _priceImpactPercentage) external;

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

308:     function setFees(Fees calldata _fees) external nonReentrant onlyOwner {

323:     function setProtocolFee(uint256 _protocolFee) external nonReentrant {

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

88:     function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {

```

```solidity
File: src/ulysses-amm/interfaces/IUlyssesPool.sol

154:     function setWeight(uint256 poolId, uint8 weight) external;

160:     function setFees(Fees calldata _fees) external;

167:     function setProtocolFee(uint256 _protocolFee) external;

```

```solidity
File: src/ulysses-amm/interfaces/IUlyssesToken.sol

62:     function setWeights(uint256[] memory _weights) external;

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

301:     function setCoreRouter(address _newCoreRouter) external requiresCoreRouter {

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

247:     function setAddresses(address _globalAddress, address _localAddress, address _underlyingAddress, uint24 _fromChain)

261:     function setLocalAddress(address _globalAddress, address _localAddress, uint24 _fromChain)

484:     function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/interfaces/IBranchPort.sol

142:     function setCoreRouter(address _newCoreRouter) external;

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootPort.sol

199:     function setAddresses(address _globalAddress, address _localAddress, address _underlyingAddress, uint24 _fromChain)

208:     function setLocalAddress(address _globalAddress, address _localAddress, uint24 _fromChain) external;

300:     function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external;

```

### [NC-2] ADD TO BLACKLIST FUNCTION

#### Description:

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

#### Recommended Mitigation Steps:

Add to Blacklist function and modifier.

### [NC-3] Avoid passing as reference the `chainid`

#### Description:

Instead of passing chainid as reference you could get current `chainid` using `block.chainid`

Please see:

https://docs.soliditylang.org/en/v0.8.0/units-and-global-variables.html#block-and-transaction-properties

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

539:             chainId := chainid()

```

### [NC-4] Boolean equality

#### Description:

Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

https://github.com/crytic/slither/wiki/Detector-Documentation#boolean-equality

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

366:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

```

### [NC-5] USE OF BYTES.CONCAT() INSTEAD OF ABI.ENCODEPACKED(,)

#### Description:

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,).

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

348:         bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

56:         bytes memory packedData = abi.encodePacked(bytes1(0x02), data);

101:         bytes memory packedData = abi.encodePacked(bytes1(0x04), data);

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

435:         bytes memory packedData = abi.encodePacked(bytes1(0x08), _depositNonce, msg.value.toUint128(), uint128(0));

481:             abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas);

657:             abi.encodePacked(bytes1(0x01), depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas);

```

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

51:         bytes memory packedData = abi.encodePacked(bytes1(0x01), data);

75:         bytes memory packedData = abi.encodePacked(bytes1(0x02), data);

108:         bytes memory packedData = abi.encodePacked(bytes1(0x03), data);

151:         bytes memory packedData = abi.encodePacked(bytes1(0x04), data);

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

112:         bytes memory packedData = abi.encodePacked(bytes1(0x02), data);

163:         bytes memory packedData = abi.encodePacked(bytes1(0x01), data);

241:         bytes memory packedData = abi.encodePacked(bytes1(0x03), data);

262:         bytes memory packedData = abi.encodePacked(bytes1(0x04), data);

285:         bytes memory packedData = abi.encodePacked(bytes1(0x05), data);

312:         bytes memory packedData = abi.encodePacked(bytes1(0x06), data);

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

278:             abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));

558:         bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain));

```

### [NC-6] GENERATE PERFECT CODE HEADERS EVERY TIME

#### Description:

I recommend using header for Solidity code layout and readability:
https://github.com/transmissions11/headers

```solidity
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [NC-7] INCONSISTENT SOLIDITY VERSIONS

#### Description:

The project is compiled with different versions of Solidity, which is not recommended because it can lead to undefined behaviors.

It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks.

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

### [NC-8] MARK VISIBILITY OF INITIALIZE(…) FUNCTIONS AS EXTERNAL

#### Description:

If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}\_init function, not the parent public initialize(...) function.

External instead of public would give more sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally).

From a security point of view, it might be safer so that it cannot be called internally by accident in the child contract.

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

56:  function initialize(
        address timelock_,
        address govToken_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThreshold_
    ) public virtual {

```

### [NC-9] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### Description:

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

46:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

### [NC-10] LACK OF EVENT EMISSION AFTER CRITICAL INITIALIZE() FUNCTIONS

#### Description:

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the initialize() functions.

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

56:     function initialize(

```

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

52:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

74:     function initialize(address _bridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

40:     function initialize(address _coreRouter) external onlyOwner {

```

### [NC-11] LARGE MULTIPLES OF TEN SHOULD USE SCIENTIFIC NOTATION

#### Description:

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.

https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

https://github.com/code-423n4/2022-10-holograph-findings/blob/main/data/Rolezn-Q.md#NC%E2%80%917

#### **Proof Of Concept**

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

56:     uint256 private constant DIVISIONER = 10000;

```

### [NC-12] FOR EXTENDED “USING-FOR” USAGE, USE THE LATEST PRAGMA VERSION

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

#### Recommended Mitigation Steps:

Use solidity pragma version min. 0.8.13

### [NC-13] NO SAME VALUE INPUT CONTROL

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Gauges.sol

30:         gaugeCycleLength = _gaugeCycleLength;

31:         incrementFreezeWindow = _incrementFreezeWindow;

```

```solidity
File: src/erc-4626/ERC4626.sol

24:         asset = _asset;

```

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

24:         asset = _asset;

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

42:         assets = _assets;

43:         weights = _weights;

63:         totalWeights = _totalWeights;

```

```solidity
File: src/erc-4626/UlyssesERC4626.sol

25:         asset = _asset;

```

```solidity
File: src/gauges/BaseV2Gauge.sol

63:         flywheelGaugeRewards = _flywheelGaugeRewards;

66:         strategy = _strategy;

```

```solidity
File: src/gauges/UniswapV3Gauge.sol

40:         uniswapV3Staker = _uniswapV3Staker;

41:         minimumWidth = _minimumWidth;

63:         minimumWidth = _minimumWidth;

```

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

58:         bribesFactory = _bribesFactory;

59:         bHermesBoostToken = _bHermesBoost;

60:         gaugeManager = _gaugeManager;

```

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

44:         admin = _admin;

46:         bHermesGaugeWeight = _bHermes.gaugeWeight();

47:         bHermesGaugeBoost = _bHermes.gaugeBoost();

```

```solidity
File: src/gauges/factories/BribesFactory.sol

57:         gaugeManager = _gaugeManager;

58:         flywheelGaugeWeightBooster = _flywheelGaugeWeightBooster;

59:         rewardsCycleLength = _rewardsCycleLength;

```

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

59:         flywheelGaugeRewards = _flywheelGaugeRewards;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

60:         dao = _dao;

80:         flywheelGaugeRewards = _flywheelGaugeRewards;

88:         dao = _dao;

94:         daoShare = _daoShare;

100:         tailEmission = _tail_emission;

```

```solidity
File: src/maia/PartnerUtilityManager.sol

44:         partnerVault = _partnerVault;

```

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

39:         bHermes = _bHermes;

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

74:         partnerVault = _partnerVault;

75:         factory = _factory;

76:         bHermesRate = _bHermesRate;

```

```solidity
File: src/maia/vMaia.sol

112:         currentMonth = _currentMonth;

```

```solidity
File: src/rewards/base/BaseFlywheelRewards.sol

32:         flywheel = _flywheel;

34:         rewardToken = _rewardToken;

```

```solidity
File: src/rewards/base/FlywheelCore.sol

47:         rewardToken = _rewardToken;

48:         flywheelRewards = _flywheelRewards;

49:         flywheelBooster = _flywheelBooster;

```

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

23:         asset = _asset;

```

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

34:         rewardsCycleLength = _rewardsCycleLength;

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

55:         rewardToken = _rewardToken;

62:         gaugeToken = _gaugeToken;

64:         minter = _minter;

```

```solidity
File: src/talos/TalosManager.sol

52:         ticksFromLowerRebalance = _ticksFromLowerRebalance;

53:         ticksFromUpperRebalance = _ticksFromUpperRebalance;

54:         ticksFromLowerRerange = _ticksFromLowerRerange;

55:         ticksFromUpperRerange = _ticksFromUpperRerange;

```

```solidity
File: src/talos/TalosOptimizer.sol

50:         twapDuration = _twapDuration;

51:         maxTwapDeviation = _maxTwapDeviation;

52:         tickRangeMultiplier = _tickRangeMultiplier;

53:         priceImpactPercentage = _priceImpactPercentage;

54:         maxTotalSupply = _maxTotalSupply;

64:         maxTotalSupply = _maxTotalSupply;

70:         twapDuration = _twapDuration;

76:         maxTwapDeviation = _maxTwapDeviation;

81:         tickRangeMultiplier = _tickRangeMultiplier;

89:         priceImpactPercentage = _priceImpactPercentage;

```

```solidity
File: src/talos/TalosStrategyStaked.sol

79:         flywheel = _flywheel;

81:         boostAggregator = _boostAggregator;

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

87:         optimizer = _optimizer;

88:         nonfungiblePositionManager = _nonfungiblePositionManager;

89:         strategyManager = _strategyManager;

90:         pool = _pool;

154:         liquidity = _liquidity;

155:         tokenId = _tokenId;

```

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

66:         uniswapV3Staker = _uniswapV3Staker;

69:         hermes = _hermes;

155:         protocolFee = _protocolFee;

```

```solidity
File: src/talos/factories/BoostAggregatorFactory.sol

35:         uniswapV3Staker = _uniswapV3Staker;

```

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

41:         nonfungiblePositionManager = _nonfungiblePositionManager;

42:         optimizerFactory = _optimizerFactory;

```

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

45:         boostAggregatorFactory = _boostAggregatorFactory;

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

92:         id = _id;

319:         fees = _fees;

329:         protocolFee = _protocolFee;

```

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

21:         ulyssesFactory = _ulyssesFactory;

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

31:         id = _id;

91:         weights = _weights;

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

36:         localChainId = _localChainId;

37:         rootPortAddress = _rootPortAddress;

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

39:         localBridgeAgentAddress = _localBridgeAgentAddress;

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

157:         wrappedNativeToken = _wrappedNativeToken;

158:         localChainId = _localChainId;

159:         rootChainId = _rootChainId;

160:         rootBridgeAgentAddress = _rootBridgeAgentAddress;

161:         localAnyCallAddress = _localAnyCallAddress;

162:         localAnyCallExecutorAddress = _localAnyCallExecutorAddress;

163:         localRouterAddress = _localRouterAddress;

164:         localPortAddress = _localPortAddress;

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

106:         coreBranchRouterAddress = _coreBranchRouter;

```

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

27:         localPortAddress = _localPortAddress;

28:         hTokenFactoryAddress = _hTokenFactoryAddress;

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

57:         rootChainId = _rootChainId;

59:         rootPortAddress = _rootPortAddress;

66:         hTokenFactoryAddress = _hTokenFactory;

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

68:         localChainId = _localChainId;

69:         localPortAddress = _localPortAddress;

70:         multicallAddress = _multicallAddress;

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

217:         wrappedNativeToken = _wrappedNativeToken;

219:         daoAddress = _daoAddress;

220:         localChainId = _localChainId;

221:         localAnyCallAddress = _localAnyCallAddress;

222:         localPortAddress = _localPortAddress;

223:         localRouterAddress = _localRouterAddress;

225:         localAnyCallExecutorAddress = _localAnyCallExecutorAddress;

908:         userFeeInfo = _userFeeInfo;

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

117:         localChainId = _localChainId;

118:         wrappedNativeTokenAddress = _wrappedNativeToken;

137:         coreRootRouterAddress = _coreRootRouter;

151:         coreRootBridgeAgentAddress = _coreRootBridgeAgent;

152:         localBranchPortAddress = _localBranchPortAddress;

457:         uint24 chainId = _chainId;

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

26:         userAddress = _userAddress;

27:         localPortAddress = _localPortAddress;

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

72:         localChainId = _localChainId;

73:         rootChainId = _rootChainId;

74:         rootBridgeAgentFactoryAddress = _rootBridgeAgentFactoryAddress;

75:         wrappedNativeToken = _wrappedNativeToken;

76:         localAnyCallAddress = _localAnyCallAddress;

77:         localAnyCallExecutorAddress = _localAnyCallExecutorAddress;

78:         localCoreBranchRouterAddress = _localCoreBranchRouterAddress;

79:         localPortAddress = _localPortAddress;

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

30:         localChainId = _localChainId;

31:         localPortAddress = _localPortAddress;

47:         localCoreRouterAddress = _coreRouter;

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

35:         localChainId = _localChainId;

36:         rootPortAddress = _rootPortAddress;

42:         coreRootRouterAddress = _coreRouter;

```

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

59:         rootChainId = _rootChainId;

60:         wrappedNativeToken = _wrappedNativeToken;

61:         localAnyCallAddress = _localAnyCallAddress;

63:         rootPortAddress = _rootPortAddress;

64:         daoAddress = _daoAddress;

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

47:         localChainId = _localChainId;

48:         factoryAddress = _factoryAddress;

49:         rootPortAddress = _rootPortAddress;

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

123:         factory = _factory;

124:         nonfungiblePositionManager = _nonfungiblePositionManager;

125:         maxIncentiveStartLeadTime = _maxIncentiveStartLeadTime;

126:         uniswapV3GaugeFactory = _uniswapV3GaugeFactory;

127:         hermesGaugeBoost = _hermesGaugeBoost;

128:         minter = _minter;

129:         hermes = _hermes;

```

### [NC-14] Omissions in events

#### Description:

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters.

The events should include the new value and old value where possible.

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Boost.sol

269:         emit AddGauge(gauge);

281:         emit RemoveGauge(gauge);

```

```solidity
File: src/erc-20/ERC20Gauges.sol

421:         emit AddGauge(gauge);

445:         emit RemoveGauge(gauge);

```

```solidity
File: src/gauges/BaseV2Gauge.sol

140:         emit AddedBribeFlywheel(bribeFlywheel);

151:         emit RemoveBribeFlywheel(bribeFlywheel);

```

```solidity
File: src/gauges/UniswapV3Gauge.sol

43:         emit NewMinimumWidth(_minimumWidth);

65:         emit NewMinimumWidth(_minimumWidth);

```

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

117:         emit AddedGaugeFactory(address(gaugeFactory));

129:         emit RemovedGaugeFactory(address(gaugeFactory));

141:         emit ChangedbHermesGaugeOwner(newOwner);

148:         emit ChangedAdmin(newAdmin);

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

220:         emit ProposalExecuted(proposalId);

257:         emit ProposalCanceled(proposalId);

```

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

85:         emit RemovedPartner(partnerManager);

94:         emit RemovedVault(vault);

```

```solidity
File: src/rewards/base/FlywheelCore.sol

121:         emit AddStrategy(address(strategy));

133:         emit FlywheelRewardsUpdate(address(newFlywheelRewards));

140:         emit FlywheelBoosterUpdate(address(newBooster));

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

80:         emit AssetRemoved(asset);

98:             emit AssetRemoved(assets[i]);

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

313:         emit BridgeAgentFactoryAdded(_newBridgeAgentFactory);

320:         emit BridgeAgentFactoryToggled(_newBridgeAgentFactory);

327:         emit BridgeAgentToggled(_bridgeAgent);

345:         emit StrategyTokenToggled(_token);

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

402:         emit BridgeAgentToggled(_bridgeAgent);

409:         emit BridgeAgentFactoryAdded(_bridgeAgentFactory);

416:         emit BridgeAgentFactoryToggled(_bridgeAgentFactory);

480:         emit NewChainAdded(_chainId);

502:         emit EcosystemTokenAdded(_ecoTokenGlobalAddress);

```

### [NC-15] SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC

#### Description:

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

#### Exploit Scenario:

A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

#### **Proof Of Concept**

```solidity
File: hardhat.config.ts

  solidity: {
    compilers: [
      {
        version: '0.8.18',
        settings: {
          viaIR: true,
          optimizer: {
            enabled: true,
            runs: 1_000_000,
          },
```

#### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.

Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [NC-16] USE A MORE RECENT VERSION OF SOLIDITY

#### Description:

For security, it is best practice to use the latest Solidity version.

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

### [NC-17] FOR FUNCTIONS AND VARIABLES FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

#### Description:

Solidity’s standard naming convention for internal and private functions and variables (apart from constants): the mixedCase format starting with an underscore (\_mixedCase starting with an underscore)

Solidity’s standard naming convention for internal and private constants variables: the snake_case format starting with an underscore (\_mixedCase starting with an underscore) and use ALL_CAPS for naming them.

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

80:     function average(uint256 a, uint256 b) internal pure returns (uint256) {

```

```solidity
File: src/erc-4626/ERC4626.sol

171:     function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}

173:     function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

```

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

111:     function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

66:     function receiveAssets(uint256[] memory assetsAmounts) private {

77:     function sendAssets(uint256[] memory assetsAmounts, address receiver) private {

292:     function beforeWithdraw(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}

294:     function afterDeposit(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}

```

```solidity
File: src/erc-4626/UlyssesERC4626.sol

129:     function beforeDeposit(uint256 assets) internal virtual returns (uint256 shares);

132:     function beforeMint(uint256 shares) internal virtual returns (uint256 assets);

135:     function afterRedeem(uint256 shares) internal virtual returns (uint256 assets);

```

```solidity
File: src/gauges/BaseV2Gauge.sol

98:     function distribute(uint256 amount) internal virtual;

```

```solidity
File: src/gauges/UniswapV3Gauge.sol

53:     function distribute(uint256 amount) internal override {

```

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

125:     function afterCreateGauge(address strategy, bytes memory data) internal virtual;

127:     function newGauge(address strategy, bytes memory data) internal virtual returns (BaseV2Gauge gauge);

```

```solidity
File: src/gauges/factories/BribesFactory.sol

26:     FlywheelBoosterGaugeWeight private immutable flywheelGaugeWeightBooster;

```

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

76:     function newGauge(address strategy, bytes memory data) internal override returns (BaseV2Gauge) {

89:     function afterCreateGauge(address strategy, bytes memory) internal override {

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

196:     ) internal {

361:     function castVoteInternal(address voter, uint256 proposalId, uint8 support) internal returns (uint96) {

525:     function add256(uint256 a, uint256 b) internal pure returns (uint256) {

531:     function sub256(uint256 a, uint256 b) internal pure returns (uint256) {

536:     function getChainIdInternal() internal view returns (uint256) {

```

```solidity
File: src/governance/GovernorBravoDelegator.sol

59:     function delegateTo(address callee, bytes memory data) internal {

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

24:     uint256 internal constant week = 86400 * 7;

26:     uint256 internal constant base = 1000;

28:     uint256 internal constant max_tail_emission = 100;

29:     uint256 internal constant max_dao_share = 300;

51:     address internal initializer;

```

```solidity
File: src/maia/libraries/DateTimeLib.sol

39:     function getMonth(uint256 timestamp) internal pure returns (uint256 month) {

55:     function isTuesday(uint256 timestamp) internal pure returns (bool result, uint256 startOfDay) {

```

```solidity
File: src/maia/vMaia.sol

34:     uint256 private currentMonth;

35:     uint256 private unstakePeriodEnd;

102:     function beforeWithdraw(uint256, uint256) internal override {

```

```solidity
File: src/rewards/base/FlywheelCore.sol

148:     uint256 private constant ONE = 1e18;

157:     function accrueStrategy(ERC20 strategy, uint256 state) private returns (uint256 rewardsIndex) {

181:     function accrueUser(ERC20 strategy, address user, uint256 index) private returns (uint256) {

```

```solidity
File: src/rewards/booster/FlywheelBoosterGaugeWeight.sol

42:     bHermesGauges private immutable bhermes;

```

```solidity
File: src/rewards/depots/RewardsDepot.sol

19:     function transferRewards(address _asset, address _rewardsContract) internal returns (uint256 balance) {

```

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

13:     address private immutable asset;

16:     address private immutable rewardsContract;

```

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

60:     function getNextCycleRewards(ERC20 strategy) internal virtual returns (uint256);

```

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

32:     function getNextCycleRewards(ERC20 strategy) internal override returns (uint256) {

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

45:     uint32 internal nextCycle;

48:     uint112 internal nextCycleQueuedRewards;

51:     uint32 internal paginationOffset;

170:         internal

```

```solidity
File: src/talos/TalosManager.sol

66:     function getRebalance(ITalosBaseStrategy position) private view returns (bool) {

78:     function getRerange(ITalosBaseStrategy position) private view returns (bool) {

```

```solidity
File: src/talos/TalosStrategyStaked.sol

52:     bool private stakeFlag = false;

101:     function beforeRedeem(uint256 _tokenId, address _owner) internal override {

108:     function afterRedeem(uint256 _tokenId) internal override {

114:     function beforeDeposit(uint256 _tokenId, address _receiver) internal override {

121:     function afterDeposit(uint256 _tokenId) internal override {

127:     function beforeRerange(uint256 _tokenId) internal override {

134:     function afterRerange(uint256 _tokenId) internal override {

```

```solidity
File: src/talos/TalosStrategyVanilla.sol

47:     uint24 private constant protocolFee = 2 * 1e5; //20%

48:     uint24 private constant GLOBAL_DIVISIONER = 1e6;

72:     function beforeRedeem(uint256 _tokenId, address) internal override {

79:     function afterRedeem(uint256 _tokenId) internal override {}

83:     function beforeDeposit(uint256 _tokenId, address) internal override {

90:     function afterDeposit(uint256 _tokenId) internal override {}

94:     function beforeRerange(uint256 _tokenId) internal override {

100:     function afterRerange(uint256 _tokenId) internal override {}

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

77:     uint24 internal constant MULTIPLIER = 1e6;

373:     function beforeDeposit(uint256 _tokenId, address _receiver) internal virtual;

375:     function afterDeposit(uint256 _tokenId) internal virtual;

377:     function beforeRedeem(uint256 _tokenId, address _owner) internal virtual;

379:     function afterRedeem(uint256 _tokenId) internal virtual;

381:     function beforeRerange(uint256 _tokenId) internal virtual;

383:     function afterRerange(uint256 _tokenId) internal virtual;

385:     function doRerange() internal virtual returns (uint256 amount0, uint256 amount1);

387:     function doRebalance() internal virtual returns (uint256 amount0, uint256 amount1);

```

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

56:     uint256 private constant DIVISIONER = 10000;

```

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

77:     ) internal virtual returns (TalosBaseStrategy);

```

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

67:     ) internal override returns (TalosBaseStrategy strategy) {

```

```solidity
File: src/talos/factories/TalosStrategyVanillaFactory.sol

38:     ) internal override returns (TalosBaseStrategy) {

```

```solidity
File: src/talos/libraries/PoolActions.sol

38:     function swapToEqualAmounts(ActionParams memory actionParams, int24 baseThreshold) internal {

61:         internal

96:     ) private view returns (uint256 balance0, uint256 balance1, int24 tickLower, int24 tickUpper) {

```

```solidity
File: src/talos/libraries/PoolVariables.sol

23:     uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)

48:         internal

72:     ) internal view returns (uint128) {

88:     function checkRange(int24 tickLower, int24 tickUpper) internal pure {

96:     function floor(int24 tick, int24 tickSpacing) internal pure returns (int24) {

116:     ) internal view returns (int24 tickLower, int24 tickUpper) {

161:     ) internal view returns (uint256 amount0, uint256 amount1) {

169:         private

186:         internal

207:     function getTwap(IUniswapV3Pool pool, uint32 twapDuration) private view returns (int24) {

224:     ) internal returns (bool zeroForOne, int256 amountSpecified, uint160 sqrtPriceLimitX96) {

```

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

30:     function doRerange() internal override returns (uint256 amount0, uint256 amount1) {

36:     function doRebalance() internal override returns (uint256 amount0, uint256 amount1) {

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

43:     uint256 private constant MIN_SWAP_AMOUNT = 1e4;

46:     uint256 private constant MAX_TOTAL_WEIGHT = 256;

49:     uint256 private constant MAX_DESTINATIONS = 15;

52:     uint256 private constant MAX_PROTOCOL_FEE = 1e16;

55:     uint256 private constant MAX_LAMBDA1 = 1e17;

58:     uint256 private constant MIN_SIGMA2 = 1e16;

65:     uint256 private constant DIVISIONER = 1 ether;

351:     ) private view returns (uint256[] memory bandwidthUpdateAmounts, uint256 length) {

547:     ) private returns (uint256 positivefee, uint256 negativeFee) {

681:         internal

849:     ) private pure returns (uint256 fee) {

893:     function ulyssesSwap(uint256 assets) private returns (uint256 output) {

942:     function ulyssesAddLP(uint256 amount, bool depositFees) private returns (uint256 output) {

1026:     function ulyssesRemoveLP(uint256 shares) private returns (uint256 assets) {

1200:     function beforeDeposit(uint256 assets) internal override returns (uint256 shares) {

1209:     function beforeMint(uint256 shares) internal override returns (uint256 assets) {

1218:     function afterRedeem(uint256 shares) internal override returns (uint256 assets) {

```

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

15:     mapping(uint256 => UlyssesPool) private pools;

31:     function getUlyssesLP(uint256 id) private returns (UlyssesPool ulysses) {

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

110:     function updateAssetBalances() internal {

```

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

81:     ) internal override {

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

68:     uint8 internal constant PARAMS_START = 1;

70:     uint8 internal constant PARAMS_START_SIGNED = 21;

72:     uint8 internal constant PARAMS_ENTRY_SIZE = 32;

74:     uint8 internal constant PARAMS_GAS_OUT = 16;

78:     uint8 internal constant PARAMS_TKN_START = 5;

80:     uint8 internal constant PARAMS_AMT_OFFSET = 64;

82:     uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;

137:     uint256 internal constant MIN_FALLBACK_RESERVE = 185_000; // 100_000 for anycall + 85_000 fallback execution overhead

138:     uint256 internal constant MIN_EXECUTION_OVERHEAD = 160_000; // 100_000 for anycall + 35_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Executions

139:     uint256 internal constant TRANSFER_OVERHEAD = 24_000;

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

32:     uint8 internal constant PARAMS_START = 1;

34:     uint8 internal constant PARAMS_START_SIGNED = 21;

36:     uint8 internal constant PARAMS_END_SIGNED_OFFSET = 26;

38:     uint8 internal constant PARAMS_ENTRY_SIZE = 32;

40:     uint8 internal constant PARAMS_ADDRESS_SIZE = 20;

42:     uint8 internal constant PARAMS_TKN_SET_SIZE = 128;

44:     uint8 internal constant PARAMS_GAS_OUT = 16;

48:     uint8 internal constant PARAMS_TKN_START = 5;

50:     uint8 internal constant PARAMS_AMT_OFFSET = 64;

52:     uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

91:     uint256 internal constant DIVISIONER = 1e4;

92:     uint256 internal constant MIN_RESERVE_RATIO = 3e3;

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

100:     uint8 internal constant PARAMS_START = 1;

102:     uint8 internal constant PARAMS_START_SIGNED = 21;

104:     uint8 internal constant PARAMS_ADDRESS_SIZE = 20;

106:     uint8 internal constant PARAMS_GAS_IN = 32;

108:     uint8 internal constant PARAMS_GAS_OUT = 16;

112:     uint8 internal constant PARAMS_TKN_START = 5;

114:     uint8 internal constant PARAMS_AMT_OFFSET = 64;

116:     uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;

180:     uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; // 100_000 for anycall + 55_000 for fallback

181:     uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution

640:     uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)

643:     mapping(address => bool) private approvedGasPool;

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

33:     uint8 internal constant PARAMS_START = 1;

35:     uint8 internal constant PARAMS_START_SIGNED = 21;

37:     uint8 internal constant PARAMS_END_OFFSET = 9;

39:     uint8 internal constant PARAMS_END_SIGNED_OFFSET = 29;

41:     uint8 internal constant PARAMS_ENTRY_SIZE = 32;

43:     uint8 internal constant PARAMS_ADDRESS_SIZE = 20;

45:     uint8 internal constant PARAMS_TKN_SET_SIZE = 104;

47:     uint8 internal constant PARAMS_TKN_SET_SIZE_MULTIPLE = 128;

49:     uint8 internal constant PARAMS_GAS_IN = 32;

51:     uint8 internal constant PARAMS_GAS_OUT = 16;

55:     uint8 internal constant PARAMS_TKN_START = 5;

57:     uint8 internal constant PARAMS_AMT_OFFSET = 64;

59:     uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

293:     function mint(address _to, address _hToken, uint256 _amount, uint24 _fromChain) internal {

349:     function addVirtualAccount(address _user) internal returns (VirtualAccount newAccount) {

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

58:     mapping(uint256 => IncentiveKey) private stakedIncentiveKey;

```

```solidity
File: src/uni-v3-staker/libraries/IncentiveId.sol

16:     function compute(IUniswapV3Staker.IncentiveKey memory key) internal pure returns (bytes32 incentiveId) {

```

```solidity
File: src/uni-v3-staker/libraries/IncentiveTime.sol

13:     uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))

15:     uint256 private constant INCENTIVES_OFFSET = 12 hours;

17:     function computeStart(uint256 timestamp) internal pure returns (uint96 start) {

23:     function computeEnd(uint256 timestamp) internal pure returns (uint96 end) {

31:     function getEnd(uint96 start) internal pure returns (uint96 end) {

```

```solidity
File: src/uni-v3-staker/libraries/NFTPositionInfo.sol

25:     ) internal view returns (IUniswapV3Pool pool, int24 tickLower, int24 tickUpper, uint128 liquidity) {

```

```solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

27:     ) internal pure returns (uint160 boostedSecondsInsideX128) {

63:     ) internal pure returns (uint256) {

```

## Low Issues

|      | Issue                                                                          |
| ---- | :----------------------------------------------------------------------------- |
| L-1  | AVOID `TRANSFER()`/`SEND()` AS REENTRANCY MITIGATIONS                          |
| L-2  | Avoid using low call function ecrecover                                        |
| L-3  | The critical parameters in initialize(...) are not set safely                  |
| L-4  | Do not use deprecated library functions                                        |
| L-5  | `ECRECOVER` MAY RETURN EMPTY ADDRESS                                           |
| L-6  | Initializers could be front-run                                                |
| L-7  | MISSING CALLS TO \_\_REENTRANCYGUARD_INIT FUNCTIONS OF INHERITED CONTRACTS     |
| L-8  | Revert if ecrecover is address(0)                                              |
| L-9  | THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS |
| L-10 | Block values as a proxy for time                                               |
| L-11 | THROW AN ERROR IF \_TOKENID IS NOT A VALID NFT                                 |
| L-12 | Account existence check for low-level calls                                    |
| L-13 | USE `_SAFEMINT` INSTEAD OF `_MINT`                                             |
| L-14 | USE `SAFETRANSFER` INSTEAD OF `TRANSFER`                                       |

### [L-1] AVOID `TRANSFER()`/`SEND()` AS REENTRANCY MITIGATIONS

#### Description:

Although `transfer()` and `send()` have been recommended as a security best-practice to prevent reentrancy attacks because they only forward 2300 gas, the gas repricing of opcodes may break deployed contracts. Use `call()` instead, without hardcoded gas limits along with checks-effects-interactions pattern or reentrancy guards for reentrancy protection.

https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

https://swcregistry.io/docs/SWC-134

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Boost.sol

313:         return super.transfer(to, amount);

```

```solidity
File: src/erc-20/ERC20Gauges.sol

498:         return super.transfer(to, amount);

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

293:         return super.transfer(to, amount);

```

```solidity
File: src/hermes/bHermes.sol

148:         return super.transfer(to, amount);

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

270:         return super.transfer(to, amount);

```

```solidity
File: src/talos/TalosStrategyStaked.sol

91:         return super.transfer(_to, _amount);

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

#### Recommended Mitigation Steps:

Using low-level `call.value(amount)` with the corresponding result check or using the OpenZeppelin `Address.sendValue` is advised:https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L60

### [L-2] Avoid using low call function ecrecover

#### Description:

Use OZ library ECDSA that its battle tested to avoid classic errors.

contracts/utils/cryptography/ECDSA.sol

https://docs.openzeppelin.com/contracts/4.x/api/utils#ECDSA

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

365:         address signer = ecrecover(

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

349:         address signatory = ecrecover(digest, v, r, s);

```

### [L-3] The critical parameters in initialize(...) are not set safely

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

56:     function initialize(

```

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

52:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

74:     function initialize(address _bridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

40:     function initialize(address _coreRouter) external onlyOwner {

```

### [L-4] Do not use deprecated library functions

#### Description:

You use safeApprove of openZeppelin although it’s deprecated. (see [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/566a774222707e424896c0c390a84dc3c13bdcb2/contracts/token/ERC20/utils/SafeERC20.sol#L38%3E)).

You should change it to increase/decrease Allowance as OpenZeppilin says.

#### **Proof Of Concept**

```solidity
File: src/gauges/UniswapV3Gauge.sol

45:         rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

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
File: src/rewards/base/BaseFlywheelRewards.sol

36:         _rewardToken.safeApprove(address(_flywheel), type(uint256).max);

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

130:         address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

131:         address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

```

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

40:             address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);

```

### [L-5] `ECRECOVER` MAY RETURN EMPTY ADDRESS

#### Description:

There is a common issue that ecrecover returns empty (0x0) address when the signature is invalid. Functions should check that before returning the result of ecrecover.

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

365:         address signer = ecrecover(

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

349:         address signatory = ecrecover(digest, v, r, s);

```

#### Recommended Mitigation Steps:

See the solution here: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v3.4.0/contracts/cryptography/ECDSA.sol#L68

### [L-6] Initializers could be front-run

#### Description:

Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

56:     function initialize(

```

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

52:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

74:     function initialize(address _bridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

40:     function initialize(address _coreRouter) external onlyOwner {

```

### [L-7] MISSING CALLS TO \_\_REENTRANCYGUARD_INIT FUNCTIONS OF INHERITED CONTRACTS

#### Description:

Most contracts use the delegateCall proxy pattern and hence their implementations require the use of `initialize()` functions instead of constructors. This requires derived contracts to call the corresponding init functions of their inherited base contracts. This is done in most places except a few.

Impact: The inherited base classes do not get initialized which may lead to undefined behavior.

Missing call to `__ReentrancyGuard_init`

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

56:     function initialize(

```

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

52:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external;

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

74:     function initialize(address _bridgeAgentAddress) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

40:     function initialize(address _coreRouter) external onlyOwner {

```

#### Recommended Mitigation Steps:

Add missing calls to init functions of inherited contracts.

### [L-8] Revert if ecrecover is address(0)

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20MultiVotes.sol

365:         address signer = ecrecover(

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

349:         address signatory = ecrecover(digest, v, r, s);

```

### [L-9] THE SAFETRANSFER FUNCTION DOES NOT CHECK FOR POTENTIALLY SELF-DESTROYED TOKENS

#### Description:

If a pair gets created and after a while one of the tokens gets self-destroyed (maybe because of a bug) then `safeTransfer` would still succeed. It’s probably a good idea to check if the contract still exists by checking the bytecode length.

#### **Proof Of Concept**

```solidity
File: src/erc-4626/ERC4626.sol

76:         address(asset).safeTransfer(receiver, assets);

96:         address(asset).safeTransfer(receiver, assets);

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

80:             assets[i].safeTransfer(receiver, assetsAmounts[i]);

```

```solidity
File: src/erc-4626/UlyssesERC4626.sol

79:         asset.safeTransfer(receiver, assets);

```

```solidity
File: src/hermes/UtilityManager.sol

113:         address(gaugeWeight).safeTransfer(msg.sender, amount);

122:         address(gaugeBoost).safeTransfer(msg.sender, amount);

131:         address(governance).safeTransfer(msg.sender, amount);

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

143:             underlying.safeTransfer(address(vault), _growth);

145:             if (dao != address(0)) underlying.safeTransfer(dao, share);

166:         underlying.safeTransfer(msg.sender, totalQueuedForCycle);

```

```solidity
File: src/maia/PartnerUtilityManager.sol

160:         address(partnerGovernance).safeTransfer(msg.sender, amount);

```

```solidity
File: src/rewards/depots/RewardsDepot.sol

21:         _asset.safeTransfer(_rewardsContract, balance);

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

234:         if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

168:             address(_token0).safeTransfer(msg.sender, refund0);

173:             address(_token1).safeTransfer(msg.sender, refund1);

228:             address(_token0).safeTransfer(msg.sender, refund0);

233:             address(_token1).safeTransfer(msg.sender, refund1);

339:         if (zeroForOne) address(token0).safeTransfer(msg.sender, uint256(amount0));

340:         else address(token1).safeTransfer(msg.sender, uint256(amount1));

```

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

168:         address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));

176:         address(hermesGaugeBoost).safeTransfer(to, amount);

```

```solidity
File: src/ulysses-amm/UlyssesPool.sol

154:             asset.safeTransfer(factory.owner(), claimed);

1189:         asset.safeTransfer(user, output);

```

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

92:         address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount);

```

```solidity
File: src/ulysses-amm/UlyssesToken.sol

84:         asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));

116:                 assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

127:         address(wrappedNativeToken).safeTransfer(rootBridgeAgentAddress, _gasToBridgeOut);

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

72:         underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));

81:         _underlyingAddress.safeTransfer(

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

930:         address(wrappedNativeToken).safeTransfer(localPortAddress, _gasToBridgeOut);

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

166:         _token.safeTransfer(msg.sender, _amount);

211:         _underlyingAddress.safeTransfer(

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

651:         address(data.tokenIn).safeTransfer(msg.sender, uint256(amount0 > 0 ? amount0 : amount1));

751:                 address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], userFeeInfo.gasToBridgeOut);

```

```solidity
File: src/ulysses-omnichain/RootPort.sol

282:         if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);

320:         _hToken.safeTransfer(_to, _amount);

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

32:         _token.safeTransfer(msg.sender, _amount);

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

204:         hermes.safeTransfer(minter, refund);

271:         if (reward > 0) hermes.safeTransfer(to, reward);

281:         if (reward > 0) hermes.safeTransfer(to, reward);

```

### [L-10] Block values as a proxy for time

#### Description:

Contracts often need access to time values to perform certain types of functionality. Values such as block.timestamp, and block.number can give you a sense of the current time or a time delta, however, they are not safe to use for most purposes.

In the case of block.timestamp, developers often attempt to use it to trigger time-dependent events. As Ethereum is decentralized, nodes can synchronize time only to some degree. Moreover, malicious miners can alter the timestamp of their blocks, especially if they can gain advantages by doing so. However, miners cant set a timestamp smaller than the previous one (otherwise the block will be rejected), nor can they set the timestamp too far ahead in the future. Taking all of the above into consideration, developers cant rely on the preciseness of the provided timestamp.

As for block.number, considering the block time on Ethereum is generally about 14 seconds, it`s possible to predict the time delta between blocks. However, block times are not constant and are subject to change for a variety of reasons, e.g. fork reorganisations and the difficulty bomb. Due to variable block times, block.number should also not be relied on for precise calculations of time.

Reference: https://swcregistry.io/docs/SWC-116
Reference: (https://github.com/kadenzipfel/smart-contract-vulnerabilities/blob/master/vulnerabilities/timestamp-dependence.md)

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Gauges.sol

75:         uint32 nowPlusOneCycle = block.timestamp.toUint32() + gaugeCycleLength;

205:             if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError();

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

59:         if (blockNumber >= block.number) revert BlockError();

250:         if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {

253:             ckpts.push(Checkpoint({fromBlock: block.number.toUint32(), votes: newWeight.toUint224()}));

364:         require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");

```

```solidity
File: src/gauges/BaseV2Gauge.sol

68:         epoch = (block.timestamp / WEEK) * WEEK;

84:         uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;

```

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

115:             govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()

139:         uint256 startBlock = add256(block.number, votingDelay);

180:         uint256 eta = add256(block.timestamp, timelock.delay());

238:                     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())

244:                     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),

304:         } else if (block.number <= proposal.startBlock) {

306:         } else if (block.number <= proposal.endBlock) {

314:         } else if (block.timestamp >= add256(proposal.eta, timelock.GRACE_PERIOD())) {

390:         return (whitelistAccountExpirations[account] > block.timestamp);

```

```solidity
File: src/hermes/minters/BaseV2Minter.sol

82:         activePeriod = (block.timestamp / week) * week;

126:         if (block.timestamp >= _period + week && initializer == address(0)) {

127:             _period = (block.timestamp / week) * week;

```

```solidity
File: src/maia/vMaia.sol

59:         currentMonth = DateTimeLib.getMonth(block.timestamp);

104:         if (unstakePeriodEnd >= block.timestamp) return;

106:         uint256 _currentMonth = DateTimeLib.getMonth(block.timestamp);

109:         (bool isTuesday, uint256 _unstakePeriodStart) = DateTimeLib.isTuesday(block.timestamp);

```

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

43:         uint32 timestamp = block.timestamp.toUint32();

```

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

60:         gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

79:         uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

114:         uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

```

```solidity
File: src/talos/TalosStrategyVanilla.sol

148:                     deadline: block.timestamp

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

147:                 deadline: block.timestamp

208:                 deadline: block.timestamp

269:                     deadline: block.timestamp

359:                 deadline: block.timestamp

```

```solidity
File: src/talos/libraries/PoolActions.sol

85:                 deadline: block.timestamp

```

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

50:         if (globalToken == address(0)) revert UnknownUnderlyingToken();

63:             revert UnknownToken();

68:         if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();

```

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

154:             return (false, "unknown selector");

```

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

108:         return (false, "unknown selector");

119:         return (false, "unknown selector");

130:         return (false, "unknown selector");

```

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1217:             return (false, "unknown selector");

1305:             return (false, "unknown selector");

```

```solidity
File: src/ulysses-omnichain/BranchPort.sol

194:         if (block.timestamp - lastManaged[msg.sender][_token] >= 1 days) {

198:         lastManaged[msg.sender][_token] = block.timestamp;

```

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

279:             return (false, "unknown selector");

```

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

344:             return (false, "unknown selector");

373:             return (false, "unknown selector");

```

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1165:             return (false, "unknown selector");

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

46:         blockNumber = block.number;

```

```solidity
File: src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol

45:     error UnknownToken();

46:     error UnknownUnderlyingToken();

```

```solidity
File: src/ulysses-omnichain/interfaces/IRootPort.sol

345:     error UnknowHTokenFactory();

```

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

140:         uint96 startTime = IncentiveTime.computeEnd(block.timestamp);

164:         if (startTime <= block.timestamp) revert IncentiveStartTimeMustBeNowOrInTheFuture();

165:         if (startTime - block.timestamp > maxIncentiveStartLeadTime) {

188:         if (block.timestamp < IncentiveTime.getEnd(key.startTime)) {

296:             IncentiveTime.getEndAndDuration(key.startTime, deposit.stakedTimestamp, block.timestamp);

332:             block.timestamp

369:             IncentiveTime.getEndAndDuration(key.startTime, deposit.stakedTimestamp, block.timestamp);

374:         if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();

435:             block.timestamp

479:         IncentiveKey memory key = IncentiveKey({pool: pool, startTime: IncentiveTime.computeStart(block.timestamp)});

501:         deposits[tokenId].stakedTimestamp = uint40(block.timestamp);

```

### [L-11] THROW AN ERROR IF \_TOKENID IS NOT A VALID NFT

#### Description:

According to `EIP-721` and specifically, the metadata extension, functions should throw an error if `_tokenId` is not a valid NFT. Contrary, the current implementation returns an empty string.

#### **Proof Of Concept**

```solidity
File: src/talos/TalosStrategyStaked.sol

101:     function beforeRedeem(uint256 _tokenId, address _owner) internal override {

108:     function afterRedeem(uint256 _tokenId) internal override {

114:     function beforeDeposit(uint256 _tokenId, address _receiver) internal override {

121:     function afterDeposit(uint256 _tokenId) internal override {

127:     function beforeRerange(uint256 _tokenId) internal override {

134:     function afterRerange(uint256 _tokenId) internal override {

140:     function _earnFees(uint256 _tokenId) internal {

164:     function _unstake(uint256 _tokenId) internal {

173:     function _stake(uint256 _tokenId) internal {

```

```solidity
File: src/talos/TalosStrategyVanilla.sol

72:     function beforeRedeem(uint256 _tokenId, address) internal override {

79:     function afterRedeem(uint256 _tokenId) internal override {}

83:     function beforeDeposit(uint256 _tokenId, address) internal override {

90:     function afterDeposit(uint256 _tokenId) internal override {}

94:     function beforeRerange(uint256 _tokenId) internal override {

100:     function afterRerange(uint256 _tokenId) internal override {}

104:     function _earnFees(uint256 _tokenId) internal {

129:     function _compoundFees(uint256 _tokenId) internal returns (uint256 amount0, uint256 amount1) {

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

349:     function _withdrawAll(uint256 _tokenId) internal {

373:     function beforeDeposit(uint256 _tokenId, address _receiver) internal virtual;

375:     function afterDeposit(uint256 _tokenId) internal virtual;

377:     function beforeRedeem(uint256 _tokenId, address _owner) internal virtual;

379:     function afterRedeem(uint256 _tokenId) internal virtual;

381:     function beforeRerange(uint256 _tokenId) internal virtual;

383:     function afterRerange(uint256 _tokenId) internal virtual;

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

36:     function withdrawERC721(address _token, uint256 _tokenId) external requiresApprovedCaller {

```

```solidity
File: src/ulysses-omnichain/interfaces/IVirtualAccount.sol

42:     function withdrawERC721(address _token, uint256 _tokenId) external;

```

#### Recommended Mitigation Steps:

Consider throwing an error if \_tokenId is not a valid NFT.

### [L-12] Account existence check for low-level calls

#### Description:

Low-level calls `call`/`delegatecall`/`staticcall` return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.

https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-callsn

#### **Proof Of Concept**

```solidity
File: src/governance/GovernorBravoDelegator.sol

60:         (bool success, bytes memory returnData) = callee.delegatecall(data);

73:         (bool success,) = implementation.delegatecall(msg.data);

```

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

281:             IVirtualAccount(userAccount).call(calls);

289:             IVirtualAccount(userAccount).call(calls);

309:             IVirtualAccount(userAccount).call(calls);

357:             IVirtualAccount(userAccount).call(calls);

365:             IVirtualAccount(userAccount).call(calls);

385:             IVirtualAccount(userAccount).call(calls);

433:             IVirtualAccount(userAccount).call(calls);

441:             IVirtualAccount(userAccount).call(calls);

461:             IVirtualAccount(userAccount).call(calls);

```

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

41:     function call(Call[] calldata calls)

49:             (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

```

```solidity
File: src/ulysses-omnichain/interfaces/IVirtualAccount.sol

48:     function call(Call[] calldata callInput) external returns (uint256 blockNumber, bytes[] memory);

```

#### Recommended Mitigation Steps:

In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

### [L-13] USE `_SAFEMINT` INSTEAD OF `_MINT`

#### Description:

According to openzepplin’s ERC721, the use of `_mint` is discouraged, use `safeMint` whenever possible.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-mint-address-uint256-

#### **Proof Of Concept**

```solidity
File: src/erc-4626/ERC4626.sol

39:         _mint(receiver, shares);

53:         _mint(receiver, shares);

```

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

39:         _mint(receiver, shares);

53:         _mint(receiver, shares);

```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

105:         _mint(receiver, shares);

124:         _mint(receiver, shares);

```

```solidity
File: src/erc-4626/UlyssesERC4626.sol

42:         _mint(receiver, shares);

54:         _mint(receiver, shares);

```

```solidity
File: src/hermes/bHermes.sol

128:     function _mint(address to, uint256 amount) internal virtual override {

132:         super._mint(to, amount);

```

```solidity
File: src/hermes/tokens/HERMES.sol

63:         _mint(account, amount);

```

```solidity
File: src/hermes/tokens/bHermesBoost.sol

29:         _mint(to, amount);

```

```solidity
File: src/hermes/tokens/bHermesGauges.sol

36:         _mint(to, amount);

```

```solidity
File: src/hermes/tokens/bHermesVotes.sol

27:         _mint(to, amount);

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

240:     function _mint(address to, uint256 amount) internal virtual override {

245:         super._mint(to, amount);

```

```solidity
File: src/maia/tokens/Maia.sol

56:         _mint(account, amount);

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

157:         _mint(receiver, shares);

218:         _mint(receiver, shares);

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol

24:         _mint(account, amount);

```

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

74:         _mint(to, amount);

```

#### Recommended Mitigation Steps:

Use `_safeMint` whenever possible instead of `_mint`

### [L-14] USE `SAFETRANSFER` INSTEAD OF `TRANSFER`

#### Description:

It is good to add a `require()` statement that checks the return value of token transfers or to use something like OpenZeppelin’s `safeTransfer`/`safeTransferFrom` unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

For example, Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)‘s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

#### **Proof Of Concept**

```solidity
File: src/erc-20/ERC20Boost.sol

313:         return super.transfer(to, amount);

```

```solidity
File: src/erc-20/ERC20Gauges.sol

498:         return super.transfer(to, amount);

```

```solidity
File: src/erc-20/ERC20MultiVotes.sol

293:         return super.transfer(to, amount);

```

```solidity
File: src/hermes/bHermes.sol

148:         return super.transfer(to, amount);

```

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

270:         return super.transfer(to, amount);

```

```solidity
File: src/talos/TalosStrategyStaked.sol

91:         return super.transfer(_to, _amount);

```

```solidity
File: src/talos/base/TalosBaseStrategy.sol

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

#### Recommended Mitigation Steps:

Consider using `safeTransfer`/`safeTransferFrom` or `require()` consistently.
