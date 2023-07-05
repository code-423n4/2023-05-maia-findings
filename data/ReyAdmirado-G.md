| | issue |
| ----------- | ----------- |
| 1 | [Structs can be packed into fewer storage slots](#1-struct-can-be-packed-into-fewer-storage-slots) |
| 2 | [state variables can be packed into fewer storage slots](#2-state-variables-can-be-packed-into-fewer-storage-slots) |
| 3 | [expressions for constant values such as a call to keccak256(), should use immutable rather than constant](#3-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant) |
| 4 | [state variables should be cached in stack variables rather than re-reading them from storage](#4-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 5 | [can make the variable outside the loop to save gas](#5-can-make-the-variable-outside-the-loop-to-save-gas) |
| 6 | [using > 0 costs more gas than != 0 when used on a uint in a require() statement](#6-using--0-costs-more-gas-than--0-when-used-on-a-uint-in-a-require-statement) |
| 7 | [splitting require() statements that use `&&` saves gas](#7-splitting-require-statements-that-use--saves-gas) |
| 8 | [require() or revert() statements that check input arguments should be at the top of the function](#8-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function) |
| 9 | [Avoid a SLOAD optimistically](#9-avoid-a-sload-optimistically) |
| 10 | [abi.encode() is less efficient than abi.encodepacked()](#10-abiencode-is-less-efficient-than-abiencodepacked) |
| 11 | [using private rather than public for constants, saves gas](#11-using-private-rather-than-public-for-constants-saves-gas) |
| 12 | [avoid an unnecessary sstore by not writing a default value for bools](#12-avoid-an-unnecessary-sstore-by-not-writing-a-default-value-for-bools) |
| 13 | [public library function should be made private/internal](#13-public-library-function-should-be-made-privateinternal) |
| 14 | [ Ternary over if ... else](#14-ternary-over-if--else) |
| 15 | [should use arguments instead of state variable](#15-should-use-arguments-instead-of-state-variable) |
| 16 | [use existing cached version of state var](#16-use-existing-cached-version-of-state-var) |
| 17 | [Optimize names to save gas](#17-optimize-names-to-save-gas) |
| 18 | [Use abi.encodeWithSelector instead of abi.encodeWithSignature](#18-use-abiencodewithselector-instead-of-abiencodewithsignature) |
| 19 | [part of the code can be pre calculated](#19-part-of-the-code-can-be-pre-calculated) |
| 20 | [require() Should Be Used Instead Of assert()](#20-require-should-be-used-instead-of-assert) |

## 1. Struct can be packed into fewer storage slots

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

`proposer`#L109 can be put together with `canceled` and `executed`#L133 to reduce slots used for this struct by 1
- [GovernorBravoInterfaces.sol#L109-L133](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L)


## 2. state variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

`liquidity`#L39 can be put near `tickLower`#L48 to reduce slots used by 1. also all functions that access `liquidity` as well as `tickLower` or `tickUpper` or `initialized` will have reduced gas usage.
- [TalosBaseStrategy.sol#L39](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L39)

`gaugeCycle`#L36 can be put together with `nextCycle`#L45 to reduce slots used by 1. also there is access to `gaugeCycle` and state vars in the same slot twice so this will reduce gas used as well
- [FlywheelGaugeRewards.sol#L36](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L36)

`_setup` can be put near `localBranchPortAddress` to reduce slots needed by 1
- [RootPort.sol#L126](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L126)

change the size of `_unlocked` from uint256 to something small like uint8 (its only 1 or 2 so there will be no problem changing this) so it can be put together with a address state var like `localBridgeAgentAddress` because they are accessed in a function together 5 times so we can save 500 gas and a 20000 for Gsset
- [BaseBranchRouter.sol#L143](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L143)

same instances as above here we can put it with other address type state vars if we make it smaller
- [MulticallRootRouter.sol#L491](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L491)
- [BranchPort.sol#L420](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L420)
- [CoreRootRouter.sol#L435](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L435)
- [BranchBridgeAgent.sol#L1363](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1363)
- [RootBridgeAgent.sol#L1270](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1270)


## 3. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [GovernorBravoDelegateMaia.sol#L42](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42)
- [GovernorBravoDelegateMaia.sol#L46](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46)


## 4. state variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 


`_assets[rewardsContract]` can be cached before emit and use the cached version in emit and #L62. reduces one complex SLOAD
- [MultiRewardsDepot.sol#L60](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L60)

`partnerIds[partnerManager]` is being read twice in #L81 and #L82 so we can cache it bofore the if statement to reduce one complex SLOAD
- [PartnerManagerFactory.sol#L81](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L81)

same logic as above for `vaultIds[vault]`
- [PartnerManagerFactory.sol#L90](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L90)

`gaugeFactoryIds[gaugeFactory]` is being read twice in #L122 and #L125 cache it so we reduce one complex SLOAD
- [BaseV2GaugeManager.sol#L122](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L122)

`localPortAddress` is being read twice in #L83 and #L93 cache it before to save 100 gas
- [ArbitrumCoreBranchRouter.sol#L93](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L93)

`rootPortAddress` is being read twice in #L49 and #L54 we can cache it before to save 100 gas
- [ArbitrumBranchPort.sol#L49](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L49)

`rootPortAddress` same as above but 3 times #L62 and #L66 and #L70 we can cache it before to save 200 gas
- [ArbitrumBranchPort.sol#L62](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L62)

`localChainId` is being read twice in #L62 and #L66 we can cache it before to save 100 gas
- [ArbitrumBranchPort.sol#L62](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L62)

`isBridgeAgent[_bridgeAgent]` is being read twice in #L367 and #L372 cache it before if statement and use that instead to reduce one complex SLOAD
- [RootPort.sol#L372](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L372)

`coreRootRouterAddress` can be cached before #L435 to reduce gas usage by 100
- [RootPort.sol#L435](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L435)

`bridgeAgentAddress` same here in #L120 #L123 and in #L148 and #L155 .saves 100 in each functions
- [MulticallRootRouter.sol#L123](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L123)
- [MulticallRootRouter.sol#L155](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L155)

`localPortAddress` is being read in both #L133 and #143 cache it before to save 100
- [CoreBranchRouter.sol#L133-L143](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L133-L143)

same here
- [CoreBranchRouter.sol#L178](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L178)

`localPortAddress` will be read twice one in #L164 and the other read will be inside if statement or else statement, in both case we will save 100 if we cache it before 
- [CoreBranchRouter.sol#L164](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L164)

same here 
- [CoreBranchRouter.sol#L190](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L190)

same here but second read is inside of the 2 elses or just inside the if
- [CoreBranchRouter.sol#L212](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L212)

`flywheelRewards` in #L126 and #L128can be cached to save 100 gas
- [FlywheelCore.sol#L126](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L126)

`getDeposit[_depositNonce].tokens[0]` is being read twice inside if statement so we can cache it at the start of if state ment to reduce one complex SLOAD. used in #L339 and #342
- [BranchBridgeAgent.sol#L342](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L342)

same as above in else #L354 and #357
- [BranchBridgeAgent.sol#L357](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L357)

`uint8(getDeposit[_depositNonce].hTokens.length` will be read twice if only `uint8(getDeposit[_depositNonce].hTokens.length) == 1` is false. so we can cache it before the #L332 so we only risk losing 3 gas if the first if is true but save a lot of gas if be false. used in #L332 and #L365
- [BranchBridgeAgent.sol#L365](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L365)

we can cache `userFeeInfo.gasToBridgeOut` inside the if statement of #L750 because if that if statement be true and inside it runs `userFeeInfo.gasToBridgeOut` is gonna be read twice in #L751 and #754
- [RootBridgeAgent.sol#L754](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L754)

if we cache `liquidity` before #L261 and we dont use `-=` in #L277 and instead use the cached version for the SLOAD of it we are gonna use less gas 
- [TalosBaseStrategy.sol#L277](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L277)

cache `pendingAdmin` at start of the function and use the cached version for #L508 and #L513 and #L516 and in lines #L521 instead of `admin` use that cached version of `pendingAdmin` because they are the same and in #L522 just return address(0) instead of reading pendingAdmin. overall saves 400 gas
- [GovernorBravoDelegateMaia.sol#L508](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L508)

usually in real use of the project these if statements like #L75 and #L85 and #L95 conditions happens and the inside of them will happen. with knowing this `partnerVault` should be cached before the if steatements. risking losing 3 gas for each function but saving 100 if conition be true
- [PartnerUtilityManager.sol#L75](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75)
- [PartnerUtilityManager.sol#L85](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85)
- [PartnerUtilityManager.sol#L95](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95)

`gaugeIds[gauge]` is being read twice cache it before to reduce one complex SLOAD. used in #L131 and #L132
- [BaseV2GaugeFactory.sol#L131](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L131)


## 5. can make the variable outside the loop to save gas

make the variable outside the loop and only give the value to variable inside

- [ERC4626MultiToken.sol#L202](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L202)
- [ERC4626MultiToken.sol#L251](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L251)

- [ERC20Boost.sol#L157](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L157)
- [ERC20Boost.sol#L160](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L160)
- [ERC20Boost.sol#L208](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L208)
- [ERC20Boost.sol#L237](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L237)

- [ERC20MultiVotes.sol#L329](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L329)
- [ERC20MultiVotes.sol#L330](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L330)
- [ERC20MultiVotes.sol#L332](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L332)

- [ERC20Gauges.sol#L260](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L260)
- [ERC20Gauges.sol#L261](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L261)
- [ERC20Gauges.sol#L339](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L339)
- [ERC20Gauges.sol#L340](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L340)
- [ERC20Gauges.sol#L537](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L537)
- [ERC20Gauges.sol#L538](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L538)

- [UlyssesToken.sol#L112](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112)
- [UlyssesToken.sol#L113](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L113)

- [UlyssesFactory.sol#L146](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L146)

- [FlywheelGaugeRewards.sol#L177](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L177)
- [FlywheelGaugeRewards.sol#L179](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L179)
- [FlywheelGaugeRewards.sol#L185](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L185)
- [FlywheelGaugeRewards.sol#L186](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L186)

- [UlyssesPool.sol#L131](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L131)
- [UlyssesPool.sol#L176](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L176)
- [UlyssesPool.sol#L190](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L190)
- [UlyssesPool.sol#L212](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L212)
- [UlyssesPool.sol#L233](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L233)
- [UlyssesPool.sol#L255](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L255)
- [UlyssesPool.sol#L297](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L297)
- [UlyssesPool.sol#L908](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L908)
- [UlyssesPool.sol#L920](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L920)
- [UlyssesPool.sol#L963](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L963)
- [UlyssesPool.sol#L972](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L972)


## 6. using > 0 costs more gas than != 0 when used on a uint in a require() statement

This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

- [ERC4626MultiToken.sol#L52](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52)

- [ERC20MultiVotes.sol#L250](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250)

- [TalosBaseStrategy.sol#L409](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409)
- [TalosBaseStrategy.sol#L410](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410)

- [ERC20Gauges.sol#L417](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417)
- [ERC20Gauges.sol#L441](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L441)

- [UlyssesToken.sol#L47](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47)

- [TalosStrategyVanilla.sol#L139](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139)

- [FlywheelGaugeRewards.sol#L234](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234)

- [ArbitrumBranchBridgeAgent.sol#L160](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160)

- [ArbitrumBranchPort.sol#L118](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118)
- [ArbitrumBranchPort.sol#L123](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L123)
- [ArbitrumBranchPort.sol#L137](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L137)
- [ArbitrumBranchPort.sol#L144](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L144)

- [RootPort.sol#L282](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L282)
- [RootPort.sol#L283](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L283)

- [BranchPort.sol#L248](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L248)
- [BranchPort.sol#L252](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L252)
- [BranchPort.sol#L268](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L268)
- [BranchPort.sol#L275](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L275)

- [FlywheelCore.sol#L127](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127)
- [FlywheelCore.sol#L162](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L162)

13 instances in 
- [BranchBridgeAgent.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L471)

13 instances in 
- [UlyssesPool.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L153)

12 instances in 
- [RootBridgeAgent.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L57)

- [UniswapV3Staker.sol#L199](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L199)
- [UniswapV3Staker.sol#L271](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L271)
- [UniswapV3Staker.sol#L281](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L281)

- [PartnerUtilityManager.sol#L75](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75)
- [PartnerUtilityManager.sol#L85](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85)
- [PartnerUtilityManager.sol#L95](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95)


## 7. splitting require() statements that use `&&` saves gas

Instead of using operator && on a single require. Using a two require can save more gas.

- [TalosBaseStrategy.sol#L408](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L408)

- [GovernorBravoDelegateMaia.sol#L68](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L68)
- [GovernorBravoDelegateMaia.sol#L71](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L71)
- [GovernorBravoDelegateMaia.sol#L74](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L74)
- [GovernorBravoDelegateMaia.sol#L120](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L120)
- [GovernorBravoDelegateMaia.sol#L237](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L237)
- [GovernorBravoDelegateMaia.sol#L298](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L298)
- [GovernorBravoDelegateMaia.sol#L400](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L400)
- [GovernorBravoDelegateMaia.sol#L416](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L416)
- [GovernorBravoDelegateMaia.sol#L433](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L433)
- [GovernorBravoDelegateMaia.sol#L508](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L508)


## 8. require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

require in #L43 is cheaper than the one on #L42 so swap the places
- [GovernorBravoDelegator.sol#L42-L43](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L)

first require is the most expensive so do it last
- [RootPort.sol#L129-L131](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L129-L131)

the 2 first requires are most expensive so do them lsat
- [RootPort.sol#L145-L149](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L145-L149)

the 2 first requires are most expensive so do them lsat
- [BranchPort.sol#L100-L104](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L100-L104)

swap the require places to do the cheaper one first
- [BranchPort.sol#L302-L303](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L302-L303)

bring #L63 and #L64 requires to the end of the requires
- [GovernorBravoDelegateMaia.sol#L63-L77](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L63-L77)

bring #L123 and #L120 requires to the startof the requires because they are the cheapest
- [GovernorBravoDelegateMaia.sol#L112-L135](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L112-L135)

swap these 
- [GovernorBravoDelegateMaia.sol#L362-L363](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L362-L363)

swap these 
- [GovernorBravoDelegateMaia.sol#L398-L399](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L398-L399)

swap these 
- [GovernorBravoDelegateMaia.sol#L414-L415](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L414-L415)

swap these 
- [GovernorBravoDelegateMaia.sol#L431-L432](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L431-L432)

## 9. Avoid a SLOAD optimistically

There is a chance that the first part will be true so the second part doesn’t need to be checked, so it is better to use the part that we have first instead of the part that needs to be called.

`depositOwner == address(0)` is way cheaper than `getSettlement[_depositNonce].status != SettlementStatus.Failed` because `getSettlement[_depositNonce].status` should be read from storage. so we can swap the sides of || to optimistically save lots of  gas
- [RootBridgeAgent.sol#L260](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L260)

`destinationId == id` is way cheaper than `destinationIds[address(destination)] != 0` because `destinationIds[address(destination)]` should be read from storage. so we can swap the sides of || to optimistically save lots of gas
- [UlyssesPool.sol#L165](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L165)

same logics here for the sides of ||
- [UlyssesPool.sol#L243](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L243)


## 10. abi.encode() is less efficient than abi.encodepacked()

- [ArbitrumCoreBranchRouter.sol#L53](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L53)
- [ArbitrumCoreBranchRouter.sol#L98](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L98)

- [RootBridgeAgent.sol#L689](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689)

- [RootBridgeAgent.sol#L733](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L733)


## 11. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [ERC20MultiVotes.sol#L360](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360)

- [TalosBaseStrategy.sol#L77](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L77)

- [GovernorBravoDelegateMaia.sol#L9-L36](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9-L36)
- [GovernorBravoDelegateMaia.sol#L42](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42)
- [GovernorBravoDelegateMaia.sol#L46](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46)

- [AnycallFlags.sol#L8-L16](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L8-L16)


## 12. avoid an unnecessary sstore by not writing a default value for bools

- [TalosStrategyStaked.sol#L52](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L52)


## 13. public library function should be made private/internal

Changing from public will remove the compiler-introduced checks for msg.value and decrease the contract’s method ID table size

- [UlyssesFactory.sol#L25](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L25)

- [TalosStrategyVanilla.sol#L22](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L22)

- [TalosStrategyStaked.sol#L20](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L20)

- [ArbitrumBranchBridgeAgent.sol#L37](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L37)

- [RootBridgeAgentExecutor.sol#L14](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L14)

- [BranchBridgeAgent.sol#L34](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L34)

- [RootBridgeAgent.sol#L68](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L68)


## 14. Ternary over if ... else

Using ternary operator instead of the if else statement saves gas.

- [ERC20MultiVotes.sol#L72-L75](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L72-L75)

- [TalosStrategyStaked.sol#L339-L340](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L339-L340)

- [BranchBridgeAgentExecutor.sol#L108-L112](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L108-L112)

- [UlyssesToken.sol#L115-L118](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L115-L118)

- [BoostAggregator.sol#L125-L130](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L)

- [FlywheelGaugeRewards.sol#L221-L225](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L221-L225)

- [CoreBranchRouter.sol#L164-L167](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L164-L167)
- [CoreBranchRouter.sol#L190-L193](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L190-L193)


## 15. should use arguments instead of state variable

This will save 100 gas because it gets rid of a storage read and instead uses a argument that we already have which gives the same result

use `_localBridgeAgentAddress` instead of `localBridgeAgentAddress` to save 100 gas
- [BaseBranchRouter.sol#L40](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L40)


## 16. use existing cached version of state var

saves gas because of less SLOADs

use the `_tokenId` stack var instead of reading `tokenId` from storage. saves 100 gas
- [TalosBaseStrategy.sol#L160](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L160)

same here with more instances
- [TalosBaseStrategy.sol#L305](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L305)
- [TalosBaseStrategy.sol#L307](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L307)
- [TalosBaseStrategy.sol#L318](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L318)
- [TalosBaseStrategy.sol#L320](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L320)

use `implementation_` in emit instead of reading it from storage. saves 100
- [GovernorBravoDelegator.sol#L50](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L50)

instead of `votingDelay` use `newVotingDelay` to save 100 gas
- [GovernorBravoDelegateMaia.sol#L406](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L406)

same with `votingPeriod` and `newVotingPeriod`
- [GovernorBravoDelegateMaia.sol#L422](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L422)

same with `proposalThreshold` and `newProposalThreshold`
- [GovernorBravoDelegateMaia.sol#L439](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L439)

same with `whitelistGuardian` and `account`
- [GovernorBravoDelegateMaia.sol#L466](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L466)


## 17. Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions.

See more [here](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).
you can use this [tool](https://emn178.github.io/solidity-optimize-name/) to get the optimized version for function and properties signitures 



## 18. Use abi.encodeWithSelector instead of abi.encodeWithSignature

abi.encodeWithSelector is much cheaper than abi.encodeWithSignature because it doesn’t require to compute the selector from the string.

- [GovernorBravoDelegator.sol#L33](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L33)


## 19. part of the code can be pre calculated

these parts of the code can be pre calculated and given to the contract as constants this will stop the use of extra operations
even if its for code readability consider putting comments instead.

these can be pre calculated to reduce one operation usage 

`PARAMS_START_SIGNED + PARAMS_TKN_START` can be pre calculated to reduce one operation usage
- [BranchBridgeAgentExecutor.sol#L134](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L134)
- [BranchBridgeAgentExecutor.sol#L143](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L143)

`PARAMS_START + PARAMS_END_OFFSET` 
- [RootBridgeAgentExecutor.sol#L180](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L180)

`PARAMS_START + PARAMS_END_SIGNED_OFFSET`
- [RootBridgeAgentExecutor.sol#L287](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L287)

`PARAMS_START + PARAMS_START`
- [BranchBridgeAgent.sol#L1259](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1259)

`PARAMS_TKN_START + PARAMS_START`
- [BranchBridgeAgent.sol#L1259](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1259)

`PARAMS_START_SIGNED + PARAMS_TKN_START`
- [BranchBridgeAgent.sol#L1274](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1274)

`PARAMS_START_SIGNED + PARAMS_START` 
- [BranchBridgeAgent.sol#L1290](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1290)

`INCENTIVES_DURATION + INCENTIVES_OFFSET`
- [IncentiveTime.sol#L20](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L20)
- [IncentiveTime.sol#L27](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L27)


these reduce 2 operations
`PARAMS_START_SIGNED + PARAMS_TKN_START + PARAMS_START`
- [BranchBridgeAgent.sol#L1290](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1290)


## 20. require() Should Be Used Instead Of assert()

- [RewardMath.sol#L65](https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/RewardMath.sol#L65)

- [FlywheelGaugeRewards.sol#L183](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L183)
- [FlywheelGaugeRewards.sol#L215](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L215)
