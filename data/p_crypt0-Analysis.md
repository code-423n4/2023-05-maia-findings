# Methodology (approach to evaluating the codebase)
Since the codebase was large, I applied a strict methodological approach in auditing this codebase.

## Tokens
First, I set to review all ERC20 based-tokens to establish their behaviour was expected and that inherited actions such as burn, mint and transfer weren't overridden with code which would allow for malicious or unexpected behaviour.

Of the tokens, I audited the following contracts (in order):
- ERC20hTokenBranch.sol
- ERC20hTokenRoot.sol
- bHermesBoost.sol
- bHermesGauges.sol
- bHermesVotes.sol
- ERC20MultiVotes.sol
- Hermes.sol
- Maia.sol
- solmate.sol

For each of the files, I performed a manual review of logic on a line-by-line basis, however, paying particular attention to functionality that could put user funds at risk, such as arbitrary transferFrom permissions.

After reviewing these files, I was satisfied that no malicious behaviour was likely to occur from this since the transfer logic was inherited from the solmate library and were not overidden.

Additionally, I checked to see whether tokens could logically be minted arbitrarily and for the cases above, it doesn't appear to be the case since mint logics are gated by onlyOwner modifiers.

### Warning about owners
Token contract logic doesn't restrict owners to be non-zero addresses. As such, tokens could be minted to a degree and then abandoned due to an inability to mint/burn.

## Factories
Following tokens, I checked factories to see whether code used to deploy tokens had malicious setup parameters.

Of the factories, I performed a review of the following: 
- TalosStrategyStakedFactory.sol
- ERC20hTokenRootFactory.sol
- TalosBaseStrategyFactory.sol (abstract)
- TalosStrategyVanillaFactory.sol
- BoostAggregatorFactory.sol
- OptimizerFactory.sol

### Comments on Factories
TalosStrategyStakedFactory.sol had interesting logic that instantiated a FlyWheelCoreInstant object, which inherits from Core (`FlywheelCore.sol`). Flywheelcoreinstant came with a warning, that `rewardsaccrual` should be performed automatically and at least every time there is a token transfer event. Additionally, it should be performed any time rewards are deposited in the rewards depot.

Care must be put on these actions!

ERC20hTokenRootFactory.sol uses `using SafeTransferLib for address;` But no operations are ever performed with it.

TalosStrategyVanillaFactory
Is `TalosBaseStrategyFactory`.

Adds functionality to create a v3 strategy using DeployVanilla library's `createTalosV3Vanilla() function`

The function calls go as:
- `TalosStrategyVanillaFactory` calls -> `DeployVanilla.createTalosV3Vanilla()`
	- `DeployVanilla` library calls function `createTalosV3Vanilla`, which returns  a `new TalosStrategyVanilla`
		- `TalosStrategyVanilla`'s constructor initiates the `TalosStrategySimple` constructor
			- `TalosStrategySimple` implements `TalosBaseStrategy`'s constructor
				- `TalosBaseStrategy`'s constructor implements an ERC20 with the parameters `(Talos LP, TLP, 18)`
					- ERC20 constructor
				- `TalosBaseStrategy`'s constructor initialises owner to be `_owner`
				- `TalosBaseStrategy`'s initiates
					- optimizer
					- nonfungiblepositionmanager
					- strategymanager
					- pool
					- token0
					- token1
					- tickspacing
					- poolfee
				- TalosBaseStrategy's constructor ERC is the LP token, token0 and token1 are the constituent tokens.

So from this call chain, an ERC20 token gets initiated for LP, which stores the information about pools and constituent tokens. Importantly, none of this is stored on-chain.

Thus, key take-aways from this contract are that nothing is stored.


OptimizerFactory.sol
This factory implements IOptimizerFactory - does it fully implement it?

Constructor defines a Talos Optimizer by passing in the value address(0) and adds it to a list of optimizers. This has the effect of setting a null TalosOptimizer TalosOptimizer's constructor parameter

Can create TalosOptimizer.


Of Note:

  `mapping(TalosOptimizer => uint256) public optimizerIds;`
	
	And 
	
```
    optimizerIds[optimizer] = optimizers.length;
	optimizers.push(optimizer);
```

Why are these of note?

Well, for the first optimizer in the constructor, no Id is listed for it, however, it is pushed to the `optimizers`. Thus, the first id for an optimizer is going to be 1,  (`optimizers.length`) not 0.








### Time spent:
30 hours