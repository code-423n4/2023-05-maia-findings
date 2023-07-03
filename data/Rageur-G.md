## GAS-1: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* BaseV2Minter.sol (Line: 126)
* BranchBridgeAgent.sol (Line: 1411, 1416)
* BranchPort.sol (Line: 194, 332)
* ERC20Boost.sol (Line: 177, 212)
* ERC20Gauges.sol (Line: 29, 205, 523)
* ERC20MultiVotes.sol (Line: 59, 318, 364)
* FlywheelAcummulatedRewards.sol (Line: 46)
* FlywheelGaugeRewards.sol (Line: 83, 90, 118, 132, 133, 142, 183, 187, 215)
* GovernorBravoDelegateMaia.sol (Line: 124, 304, 306, 308, 314, 363, 527, 532)
* GovernorBravoDelegateSeverity1.sol (Line: 126, 306, 308, 310, 316, 365, 529, 534)
* GovernorBravoDelegateSeverity2.sol (Line: 126, 306, 308, 310, 316, 365, 529, 534)
* GovernorBravoDelegateSeverity3.sol (Line: 126, 306, 308, 310, 316, 365, 529, 534)
* GovernorBravoDelegateSeverity4.sol (Line: 126, 306, 308, 310, 316, 365, 529, 534)
* GovernorBravoDelegateSeverity5.sol (Line: 126, 306, 308, 310, 316, 365, 529, 534)
* PoolVariables.sol (Line: 89)
* RewardMath.sol (Line: 65)
* RootBridgeAgent.sol (Line: 758, 761)
* TalosBaseStrategy.sol (Line: 408)
* TalosManager.sol (Line: 70, 71, 82, 83)
* TalosOptimizer.sol (Line: 44, 86)
* UlyssesPool.sol (Line: 211, 317, 686, 733, 743)
* UniswapV3Staker.sol (Line: 138, 158, 164, 456, 506)
* vMaia.sol (Line: 104)

## GAS-2: Add unchecked{} for subtractions where the operands cannot underflow because of a previous require() or if statement

### Description

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
 if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
 This will stop the check for overflow and underflow so it will save gas.

### Affected file

* BaseV2Minter.sol (Line: 140)
* GovernorBravoDelegateMaia.sol (Line: 533)
* GovernorBravoDelegateSeverity1.sol (Line: 535)
* GovernorBravoDelegateSeverity2.sol (Line: 535)
* GovernorBravoDelegateSeverity3.sol (Line: 535)
* GovernorBravoDelegateSeverity4.sol (Line: 535)
* GovernorBravoDelegateSeverity5.sol (Line: 535)
* PartnerUtilityManager.sol (Line: 131, 151)
* TalosBaseStrategy.sol (Line: 167, 172, 227, 232)
* UlyssesPool.sol (Line: 139, 218, 303)
* UlyssesToken.sol (Line: 116)

## GAS-3: Cache the mapping values rather than fetch it every time

### Affected file

* BranchBridgeAgent.sol (Line: 327, 338, 339, 340, 342, 353, 354, 355, 357, 365, 373, 375, 376, 377, 378, 387, 389, 390, 391, 392, 1148, 1170, 1194)
* ERC20Boost.sol (Line: 204, 213, 232, 239)
* ERC20Gauges.sol (Line: 210, 211)
* ERC20MultiVotes.sol (Line: 324, 339)
* ERC4626MultiToken.sol (Line: 52, 54)
* RootBridgeAgent.sol (Line: 257, 260, 552, 568,  919, 944, 969, 993, 1017, 1053, 1088, 1123)
* UlyssesFactory.sol (Line: 111, 124)
* UlyssesPool.sol (Line: 131, 133, 135, 176, 178, 190, 194, 212, 214, 233, 235, 239, 249, 255, 260, 284, 297, 299)

## GAS-4: Caching global variables is more expensive than using the actual variable

### Description

 It’s cheaper to use global variable as compared to caching.

### Affected file

* BaseV2Minter.sol (Line: 59)
* GovernorBravoDelegator.sol (Line: 17, 18)
* RootBridgeAgent.sol (Line: 218)
* SingleRewardsDepot.sol (Line: 24)
* VirtualAccount.sol (Line: 46)
* bHermesBoost.sol (Line: 24)
* bHermesGauges.sol (Line: 31)
* bHermesVotes.sol (Line: 22)

## GAS-5: Change ```public``` state variable visibility to ```private```

### Description

If it is preferred to change the visibility of the ```owner``` state variable to private, this will save significant gas.

### Affected file

* BaseV2GaugeManager.sol (Line: 20)
* GovernorBravoInterfaces.sol (Line: 62, 62, 65, 65)

## GAS-6: Do not calculate constants

### Description

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

### Affected file

* BaseV2Minter.sol (Line: 24)
* ERC20MultiVotes.sol (Line: 360)
* GovernorBravoDelegateMaia.sol (Line: 42, 46)
* GovernorBravoDelegateSeverity1.sol (Line: 44, 48)
* GovernorBravoDelegateSeverity2.sol (Line: 44, 48)
* GovernorBravoDelegateSeverity3.sol (Line: 44, 48)
* GovernorBravoDelegateSeverity4.sol (Line: 44, 48)
* GovernorBravoDelegateSeverity5.sol (Line: 44, 48)
* TalosStrategyVanilla.sol (Line: 47)

## GAS-7: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Affected file

* ArbitrumBranchBridgeAgent.sol (Line: 126, 134, 142, 149, 156, 174, 181, 184, 189, 194)
* ArbitrumBranchBridgeAgent.sol (Line: 126, 134, 142, 149, 156, 174, 181, 184, 189, 194)
* BranchBridgeAgent.sol (Line: 1109)
* FlywheelBribeRewards.sol (Line: 32)
* FlywheelCoreInstant.sol (Line: 40)
* FlywheelCoreStrategy.sol (Line: 39)
* RewardsDepot.sol (Line: 19)
* TalosStrategySimple.sol (Line: 30, 36)
* TalosStrategyStaked.sol (Line: 101, 108, 114, 121, 127, 134)
* TalosStrategyVanilla.sol (Line: 72, 79, 83, 90, 94, 100)
* TalosStrategyVanillaFactory.sol (Line: 33)
* UlyssesPool.sol (Line: 1200, 1209, 1218)
* UniswapV3Gauge.sol (Line: 53)
* UniswapV3GaugeFactory.sol (Line: 76, 89)
* vMaia.sol (Line: 102)

## GAS-8: Multiple accesses of a mapping/array should use a local variable cache

### Description

Caching a mapping’s value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable’s reference instead of repeatedly fetching it.
To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

### Affected file

* BranchBridgeAgent.sol (Line: 332, 408, 411, 1162, 1186, 1210)
* ERC20MultiVotes.sol (Line: 250, 251, 340, 343)
* RootBridgeAgent.sol (Line: 936, 961, 985, 1009, 1045, 1080, 1115, 1138, 1148)
* UlyssesPool.sol (Line: 257, 257, 281, 283)
* UniswapV3Staker.sol (Line: 266, 268, 402, 406)

## GAS-9: Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Description

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

### Affected file

* BoostAggregator.sol (Line: 39, 48)
* BranchPort.sol (Line: 27, 40, 54, 63, 66, 71, 80, 83, 86, 89)
* ERC20Boost.sol (Line: 28, 31, 33)
* ERC20Gauges.sol (Line: 45, 49, 53, 58, 395)
* ERC20MultiVotes.sol (Line: 29, 93, 117, 120, 123)
* MultiRewardsDepot.sol (Line: 17, 20, 23)
* RootPort.sol (Line: 47, 61, 70, 77, 90, 93, 96, 99, 102)
* UniswapV3Staker.sol (Line: 34, 52, 81)
* UtilityManager.sol (Line: 32, 34, 36)

## GAS-10: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* ArbitrumCoreBranchRouter.sol (Line: 117)
* BaseBranchRouter.sol (Line: 106, 117, 128)
* BranchBridgeAgent.sol (Line: 1123, 1232)
* CoreBranchRouter.sol (Line: 235, 235)
* ERC20Gauges.sol (Line: 188, 249, 273, 326)
* FlywheelGaugeRewards.sol (Line: 200)
* GovernorBravoDelegateMaia.sol (Line: 277)
* GovernorBravoDelegateSeverity1.sol (Line: 279)
* GovernorBravoDelegateSeverity2.sol (Line: 279)
* GovernorBravoDelegateSeverity3.sol (Line: 279)
* GovernorBravoDelegateSeverity4.sol (Line: 279)
* GovernorBravoDelegateSeverity5.sol (Line: 279)
* IncentiveId.sol (Line: 16)
* IncentiveTime.sol (Line: 17, 23)
* MulticallRootRouter.sol (Line: 351, 351, 427)
* RootBridgeAgent.sol (Line: 865, 1182)
* SingleRewardsDepot.sol (Line: 32)
* TalosManager.sol (Line: 91)
* UlyssesToken.sol (Line: 39)
* VirtualAccount.sol (Line: 45, 45)

## GAS-11: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* BaseBranchRouter.sol (Line: 138, 146)
* BaseFlywheelRewards.sol (Line: 43)
* BaseV2Gauge.sol (Line: 155)
* BaseV2GaugeFactory.sol (Line: 160)
* BaseV2GaugeManager.sol (Line: 155, 160)
* BoostAggregator.sol (Line: 190)
* BranchBridgeAgent.sol (Line: 1366, 1374, 1380, 1393, 1404)
* BranchPort.sol (Line: 397, 403, 409, 415, 423)
* BribesFactory.sol (Line: 105)
* CoreRootRouter.sol (Line: 438, 446)
* ERC20Boost.sol (Line: 341)
* ERC20hTokenBranchFactory.sol (Line: 75, 81)
* ERC20hTokenRoot.sol (Line: 57)
* ERC20hTokenRootFactory.sol (Line: 74)
* ERC4626PartnerManager.sol (Line: 294, 302, 310, 318, 325)
* MultiRewardsDepot.sol (Line: 72)
* MulticallRootRouter.sol (Line: 494, 502)
* PartnerUtilityManager.sol (Line: 168)
* RewardsDepot.sol (Line: 24)
* RootBridgeAgent.sol (Line: 1273, 1281, 1296, 1307, 1313, 1321, 1327)
* RootPort.sol (Line: 510, 516, 522, 528)
* SingleRewardsDepot.sol (Line: 40)
* TalosBaseStrategy.sol (Line: 423, 430)
* UniswapV3Gauge.sol (Line: 73)
* UtilityManager.sol (Line: 141, 144, 147)
* VirtualAccount.sol (Line: 69)
* bHermes.sol (Line: 69, 77, 85)
* bHermesBoost.sol (Line: 32)
* bHermesGauges.sol (Line: 39)
* bHermesVotes.sol (Line: 39)
* vMaia.sol (Line: 67, 75, 83)

## GAS-12: Require()/Revert() statements that check input arguments should be at the top of the function

### Description

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

### Affected file

* BranchPort.sol (Line: 103, 104, 303)
* RootPort.sol (Line: 147, 148, 149)

## GAS-13: Should use arguments instead of state variable

### Description

Using function's parameter cost less gas then a state variable.

### Affected file

* BaseBranchRouter.sol (Line: 40)
* BoostAggregator.sol (Line: 67, 68)
* RootBridgeAgentFactory.sol (Line: 62)

## GAS-14: Splitting require() statements that use && saves gas

### Description

Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas.

### Affected file

* GovernorBravoDelegateMaia.sol (Line: 67, 71, 75, 119, 237, 298, 399, 415, 432, 507)
* GovernorBravoDelegateSeverity1.sol (Line: 69, 73, 77, 121, 239, 300, 401, 417, 434, 509)
* GovernorBravoDelegateSeverity2.sol (Line: 69, 73, 77, 121, 239, 300, 401, 417, 434, 509)
* GovernorBravoDelegateSeverity3.sol (Line: 69, 73, 77, 121, 239, 300, 401, 417, 434, 509)
* GovernorBravoDelegateSeverity4.sol (Line: 69, 73, 77, 121, 239, 300, 401, 417, 434, 509)
* GovernorBravoDelegateSeverity5.sol (Line: 69, 73, 77, 121, 239, 300, 401, 417, 434, 509)
* TalosBaseStrategy.sol (Line: 408)

## GAS-15: Use constants instead of type(uintx).max

### Description

It uses more gas in the distribution process and also for each transaction than constant usage.

### Affected file

* BaseFlywheelRewards.sol (Line: 36)
* ERC4626.sol (Line: 67, 84, 149, 154)
* ERC4626DepositOnly.sol (Line: 99, 104)
* ERC4626MultiToken.sol (Line: 143, 165, 200, 270, 275)
* ERC4626PartnerManager.sol (Line: 200, 201, 202, 203)
* FlywheelGaugeRewards.sol (Line: 133, 187)
* TalosBaseStrategy.sol (Line: 130, 131, 249, 285, 286, 367, 368)
* TalosStrategyStaked.sol (Line: 151, 152)
* TalosStrategyVanilla.sol (Line: 111, 112)
* UlyssesERC4626.sol (Line: 68, 113, 117)
* UlyssesRouter.sol (Line: 40)
* UniswapV3Gauge.sol (Line: 45)
* UniswapV3Staker.sol (Line: 70, 385, 386, 456, 506, 509)

## GAS-16: Use elementary types or a user-defined type instead of a struct that has only one member

### Affected file

* IRootBridgeAgent.sol (Line: 10)
* PoolActions.sol (Line: 26)

## GAS-17: Use hardcoded address instead address(this)

### Description

Instead of using ```address(this)```, it is more gas-efficient to pre-calculate and use the hardcoded ```address```

### Affected file

* ArbitrumBranchBridgeAgent.sol (Line: 158)
* ArbitrumBranchPort.sol (Line: 52, 120, 140)
* ArbitrumCoreBranchRouter.sol (Line: 104)
* BaseV2Gauge.sol (Line: 70, 115)
* BaseV2Minter.sol (Line: 138, 140)
* BoostAggregator.sol (Line: 90, 168, 175)
* BranchBridgeAgent.sol (Line: 1020, 1078, 1093, 1103, 1325)
* BranchPort.sol (Line: 127, 138, 180, 249, 254, 271, 276)
* BribesFactory.sol (Line: 86)
* CoreBranchRouter.sol (Line: 111, 154)
* ERC20hTokenRootFactory.sol (Line: 61)
* ERC4626.sol (Line: 37, 51)
* ERC4626DepositOnly.sol (Line: 37, 51)
* ERC4626MultiToken.sol (Line: 69)
* ERC4626PartnerManager.sol (Line: 162, 168, 208, 219, 226, 244)
* FlywheelGaugeRewards.sol (Line: 88, 90, 130, 132)
* GovernorBravoDelegateMaia.sol (Line: 346)
* GovernorBravoDelegateSeverity1.sol (Line: 348)
* GovernorBravoDelegateSeverity2.sol (Line: 348)
* GovernorBravoDelegateSeverity3.sol (Line: 348)
* GovernorBravoDelegateSeverity4.sol (Line: 348)
* GovernorBravoDelegateSeverity5.sol (Line: 348)
* PartnerUtilityManager.sol (Line: 75, 85, 95, 104, 128, 139, 148)
* PoolActions.sol (Line: 47, 84, 98, 99)
* PoolVariables.sol (Line: 227, 228)
* RewardsDepot.sol (Line: 20)
* RootBridgeAgent.sol (Line: 224, 668, 685, 729, 766, 851, 1237, 1328)
* RootPort.sol (Line: 311, 350)
* TalosBaseStrategy.sol (Line: 125, 126, 146, 196, 197, 366, 406, 407)
* TalosStrategyStaked.sol (Line: 90, 95, 150, 177)
* TalosStrategyStakedFactory.sol (Line: 51)
* TalosStrategyVanilla.sol (Line: 110, 130, 131)
* UlyssesERC4626.sol (Line: 36, 52)
* UlyssesFactory.sol (Line: 87, 100)
* UlyssesPool.sol (Line: 103, 108, 127, 218, 303, 1109)
* UlyssesRouter.sol (Line: 74)
* UlyssesToken.sol (Line: 84, 112, 118)
* UniswapV3GaugeFactory.sol (Line: 83)
* UniswapV3Staker.sol (Line: 151, 177, 254)
* UtilityManager.sol (Line: 72, 81, 90)
* VirtualAccount.sol (Line: 37)
* bHermes.sol (Line: 116, 129, 130, 131)

## GAS-18: Using > 0 costs more gas than != 0 when used on a uint in a require() statement

### Description

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance.

### Affected file

* ArbitrumBranchBridgeAgent.sol (Line: 160)
* ArbitrumBranchPort.sol (Line: 118, 123, 137, 144)
* BranchBridgeAgent.sol (Line: 474, 499, 521, 543, 623, 627, 979, 983, 1328)
* BranchPort.sol (Line: 248, 252, 268, 275)
* ERC20Gauges.sol (Line: 417, 441)
* ERC20MultiVotes.sol (Line: 250)
* ERC4626MultiToken.sol (Line: 52)
* FlywheelCore.sol (Line: 127, 162)
* FlywheelGaugeRewards.sol (Line: 234)
* PartnerUtilityManager.sol (Line: 75, 85, 95)
* RewardMath.sol (Line: 31)
* RootBridgeAgent.sol (Line: 55, 301, 347, 451, 457, 750, 757, 1161, 1171, 1240)
* RootPort.sol (Line: 282, 283)
* TalosBaseStrategy.sol (Line: 409, 410)
* TalosStrategyVanilla.sol (Line: 139)
* UlyssesFactory.sol (Line: 111)
* UlyssesPool.sol (Line: 153, 191, 256, 272, 282, 911, 1048)
* UlyssesToken.sol (Line: 47)
* UniswapV3Staker.sol (Line: 199, 271, 281)

## GAS-19: Using private rather than public for constants saves gas

### Description

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

### Affected file

* AnycallFlags.sol (Line: 8, 9, 10, 11, 12, 15, 16)
* ERC20MultiVotes.sol (Line: 360)
* GovernorBravoDelegateMaia.sol (Line: 9, 12, 15, 18, 21, 24, 27, 30, 33, 36, 42, 46)
* GovernorBravoDelegateSeverity1.sol (Line: 8, 11, 14, 17, 20, 23, 26, 29, 32, 35, 44, 48)
* GovernorBravoDelegateSeverity2.sol (Line: 8, 11, 14, 17, 20, 23, 26, 29, 32, 35, 44, 48)
* GovernorBravoDelegateSeverity3.sol (Line: 8, 11, 14, 17, 20, 23, 26, 29, 32, 35, 44, 48)
* GovernorBravoDelegateSeverity4.sol (Line: 8, 11, 14, 17, 20, 23, 26, 29, 32, 35, 44, 48)
* GovernorBravoDelegateSeverity5.sol (Line: 8, 11, 14, 17, 20, 23, 26, 29, 32, 35, 44, 48)

## GAS-20: With assembly, ```.call (bool success)``` transfer can be done gas-optimized

### Description

```return``` data ```(bool success,)``` has to be stored due to EVM architecture, but in a usage in assembly, 'out' and 'outsize' values are given (0,0), this storage disappears and gas optimization is provided.

### Affected file

* GovernorBravoDelegator.sol (Line: 59, 60, 72, 73)
* MulticallRootRouter.sol (Line: 281, 289, 309, 357, 365, 385, 433, 441, 461)
* VirtualAccount.sol (Line: 49)

## GAS-21: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas.

### Affected file

* ArbitrumCoreBranchRouter.sol (Line: 53, 98)
* CoreBranchRouter.sol (Line: 48, 72, 105, 148)
* CoreRootRouter.sol (Line: 107, 158, 238, 259, 282, 309)
* ERC20MultiVotes.sol (Line: 368)
* GovernorBravoDelegateMaia.sol (Line: 346, 347)
* GovernorBravoDelegateSeverity1.sol (Line: 348, 349)
* GovernorBravoDelegateSeverity2.sol (Line: 348, 349)
* GovernorBravoDelegateSeverity3.sol (Line: 348, 349)
* GovernorBravoDelegateSeverity4.sol (Line: 348, 349)
* GovernorBravoDelegateSeverity5.sol (Line: 348, 349)
* IncentiveId.sol (Line: 17)
* PoolActions.sol (Line: 51)
* RootBridgeAgent.sol (Line: 689, 733)