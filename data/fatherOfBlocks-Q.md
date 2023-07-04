**src/hermes/tokens/HERMES.sol**
- The Hermes token does not allow burning tokens, this is necessary since it allows the owner to mine, but does not allow the opposite operation.


**src/maia/tokens/Maia.sol**
- The Maia token does not allow burning tokens, this is necessary since it allows the owner to mine, but it does not allow the opposite operation.


**src/rewards/booster/FlywheelBoosterGaugeWeight.sol**
- L49 - Since the bhermes variable is immutable in the constructor, it should be validated that the input is != 0.


**src/rewards/depots/SingleRewardsDepot.sol**
- L4 - The IRewardsDepot import is not used throughout the contract, therefore it generates an extra gas expense and on top of that in the deploy and the explorer display makes the code less readable.

- L23 - Since the asset variable is immutable in the constructor, it should be validated that the input is != 0.


**src/ulysses-omnichain/token/ERC20hTokenBranch.sol**
- L23/29 - The signature of the mint() function returns a bool, but burn() does not. This is somewhat strange, since burn and mint are opposite functions, therefore they should have similar signatures. If for the mint it is necessary to know if I did it or not, the same should happen with the burn.


**src/hermes/tokens/bHermesBoost.sol**
- L4 - The Ownable import is not used throughout the contract, therefore it generates an extra cost of gas and on top of that in the deploy and the explorer display makes the code less readable.

- The bHermesBoost token does not allow burning tokens, this is necessary since it allows the bHermes to mine, but does not allow the opposite operation.


**src/rewards/rewards/FlywheelInstantRewards.sol**
- L5 - The Ownable import is not used throughout the contract, therefore it generates an extra cost of gas and on top of that in the deploy and the explorer display makes the code less readable.


**src/rewards/FlywheelCoreInstant.sol**
- L4/5/6 - The Ownable imports, SafeCastLib and SafeTransferLib, are not used throughout the contract, therefore they generate an extra cost of gas and on top of that in the deploy and the explorer display makes the code less readable.


**src/rewards/FlywheelCoreStrategy.sol**
- L4/5/6 - The Ownable imports, SafeCastLib and SafeTransferLib, are not used throughout the contract, therefore they generate an extra cost of gas and on top of that in the deploy and the explorer display makes the code less readable.


**src/hermes/tokens/bHermesGauges.sol**
- The bHermesGauges token does not allow to burn tokens, this is necessary since it allows the bHermes to mine, but it does not allow the opposite operation.


**src/talos/factories/BoostAggregatorFactory.sol**
- L35 - Since the uniswapV3Staker variable is immutable in the constructor, it should be validated that the input is != 0.


**src/gauges/UniswapV3Gauge.sol**
- L40/41/63 - Since the uniswapV3Staker and minimumWidth variables are immutable, the constructor should validate that the inputs are != 0.


**src/rewards/depots/MultiRewardsDepot.sol**
- L47/57 - The addAsset() function performs two actions: setting an address inside _isAsset and inside _isRewardsContract, this makes the name not as clear as possible. The same thing happens with removeAsset().


**src/ulysses-omnichain/token/ERC20hTokenRoot.sol**
- L72/84 - The signature of the mint() function returns a bool, but burn() does not. This is somewhat strange, since burn and mint are opposite functions, therefore they should have similar signatures. If for the mint it is necessary to know if I did it or not, the same should happen with the burn.

- L28 - The name of the variable in storage is getTokenBalance and this is somewhat confusing, since the get keyword is normally used to get values.


**src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol**
- L81 - The mofifier requiresPort is created, but it is never used, therefore it should be removed.


**src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol**
- L38 - The name of the variable in storage is getBridgeAgentManager and this is somewhat confusing, since the get keyword is normally used to get values.

- L61 - Since the localAnyCallAddress variable is immutable in the constructor, it should be validated that the input is != 0.


**src/talos/TalosOptimizer.sol**
- L42/43/44/47/63/69/75/86 - Multiple validations are performed in the constructor and in the setter functions and most of them are repeated, therefore it would be more beneficial to unify each validation in a modifier function.


**src/gauges/factories/BribesFactory.sol**
- L57/58/59 - As the gaugeManager, flywheelGaugeWeightBooster and rewardsCycleLength variables are immutable, the constructor should validate that the inputs are != 0.


**src/gauges/factories/UniswapV3GaugeFactory.sol**
- L59 - Since the flywheelGaugeRewards variable is immutable in the constructor, it should be validated that the input is != 0.


**src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol**
- L88 - The _rootBridgeAgentFactoryAddress input is requested, but it can only have one possible value, therefore another way of implementing it would be to directly not request that input and directly eliminate the validation, assuming that this is the factory.


**src/talos/TalosStrategyVanilla.sol**
- L5/8 - The Ownable and ERC20 imports are not used throughout the contract, therefore they generate an extra cost of gas and on top of that in the deploy and the explorer display makes the code less readable.
