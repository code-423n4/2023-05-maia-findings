##  L-01 Incorrect Natspec
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L111-L130

##  L-02 No need to push an optimizer in the constructor as there is no way to delete an optimizer 
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/factories/OptimizerFactory.sol#L24-L28 

## L-03 Outdated Docs in increaseConversionRate in IERC4626PartnerManager.sol 
 https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/interfaces/IERC4626PartnerManager.sol#L64

## L-04 Last 2 variables in DeployArbitrumBranchBridgeAgent.sol deploy function are interchanged
 https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#LL44C2-L45C36

## L-05 Interface functions do not exist on the Optimizer contract
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/interfaces/IOptimizerFactory.sol#L16-L21

## L-06 block.number will not be a reliable source of timing on Optimism
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/VirtualAccount.sol#L46

## L-07 Inconsistent usage of variable naming for mint and burn functions (amount vs. value)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L22C4-L28
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L47-L55

## L-08 Misleading naming of requiresCoreRouter() modifier in ERC20hTokenRootFactory.sol as modifier checks for more than just the core router
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L75

## L-09 Mixed usage of uint24 and uint256 for chain ids. See examples below:
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L378 (uint24)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L47 (uint256)

## L-10 Function "getLocalTokenFromUnder" should be named "getLocalTokenFromUnderlying"
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootPort.sol#L69

## L-11 Unnecessary single-line comment of multiple lines of already existing multi-line comment
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/strategies/TalosStrategySimple.sol#L26-L28

## L-12 Broken sentence in the documentation of ArbitrumCoreBranchRouter.sol
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L21-L23 (some words may be missing in the "Branch Chain is not available" part)

## L-13 Typo in multiple files where it should be "not enough budget" instead of "no enough budget"
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L131-L132
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L98
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L101
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L1230-L1231
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L1320-L1321
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L101
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L104

## L-14 Typo in Natspec of IVirtualAccount.sol (should be "performed calls" instead of "perfomed call)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L15

## L-15 @notice in Natspec of "createBridgeAgent" function of ArbitrumBranchBridgeAgentFactory is incorrect (should be: "Creates a new bridge agent for root chain." and maybe "root bridge agent" instead of just "bridge agent" as 2 lines below "root bridge agent" is used)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L75

## L-16 Missing zero address check for "_coreRootBridgeAgent" in "initialize" function in ArbitrumBranchBridgeAgentFactory.sol
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L55
Compare with the "initialize" function in BranchBridgeAgentFactory.sol which does this check: https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L84

## L-17 Unused "requiresPort" modifier in ERC20hTokenBranchFactory.sol
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L81

## L-18 Typo of writing "length" as "lenght" in multiple contracts
https://github.com/search?q=repo%3Acode-423n4%2F2023-05-maia%20lenght&type=code

## L-19 Asymmetry in implementation of "bridgeOut" and "bridgeOutMultiple" in BranchPort.sol
Both functions do the same just "bridgeOutMultiple" does it for multiple local addresses. The functions process the 2 if statements in their function body in a different order. This e.g. makes it harder to compare the functions for correctness. Compare https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L248-L256 vs. https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L268-L278

## L-20 Risk of accidentally toggling on toggled off items in BranchPort.sol
There are sets of 2 functions in that contract where one function adds an item and another function toggles the item state. As add function does not check whether the item was already added, it could accidentally be added and toggled on again. A side effect of this is that the arrays that track the history of added items will also hold duplicate items. The following combinations of functions were identified: addStrategyToken + toggleStrategyToken, addPortStrategy + togglePortStrategy, addBridgeAgentFactory + toggleBridgeAgentFactory, addBridgeAgent + toggleBridgeAgent. Example of toggling on again by adding: https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L291

## L-21 NapSpec of increaseConversionRate() function in IERC4626PartnerManager.sol is wrong
In https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/interfaces/IERC4626PartnerManager.sol#L64 it talks about "address(partnerVault)" but in the implementation in uses "address(this)".

## L-22 Same NatSpec and implementation for "maxDeposit" and "maxMint" functions in ERC4626PartnerManager
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L159-L160
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L159-L160

## L-23 Inconsistent ways used to increase settlementNonce in DeployRootBridgeAgent.sol and depositNonce in BranchBridgeAgent.sol
In both files nonces are either increased by in place calling ++ or via a dedicated "_getAndIncrement*" function.
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L278 vs. https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L633, https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol vs. https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol.

## L-24 UlyssesPoolDeployer.sol should use post-increment instead of pre-increment for poolId and tokenId
These state variabless are commented as "next poolId" and "next tokenId". Due to the pre-increment, they are not used as such. It is also the only occurrence in the protocol where increments are used like this.
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/factories/UlyssesFactory.sol#L85, https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/factories/UlyssesFactory.sol#L141

## L-25 Multiple contracts either use Ownable but don't need to use it or import it without using it
1) Contracts that don't need to use it (no onlyOwner modifier present): BribesFactory.sol, FlywheelGaugeRewards.sol, TalosBaseStrategyFactory.sol, ERC20hTokenBranchFactory.sol; 2) Contracts that import Ownable but don't use it: UniswapV3GaugeFactory.sol, UtilityManager.sol, bHermesBoost.sol, bHermesGauges.sol, bHermesVotes.sol, vMaia.sol, PartnerManagerFactory.sol, FlywheelCoreInstant.sol, FlywheelCore.sol, FlywheelInstantRewards.sol, TalosStrategyVanilla.sol, ArbitrumBranchPort.sol, CoreBranchRouter.sol, RootBridgeAgent.sol, RootBridgeAgent.sol, RootBridgeAgentFactory.sol, ERC20hTokenRoot.sol

## L-26 Missing comments in GovernorBravoDelegate* contracts on a subtlety which is actually the only protection against flash loans
A proposal pending state is judged by "block.number <= proposal.startBlock" which on the first look may be incorrect since the "active" state is also inclusive of the endBlock. See: https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L304. But the startBlock is excluded intentionally so the proposal starts 1 block later than the block when the user balance is passed. See: https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L367. This should be documented.

## L-27 Unused MIN_RESERVE_RATION constant in BranchPort.sol
In https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L92 MIN_RESERVE_RATION is defined but it is never used in that contract. It may have been missed to be used for a second check in the "addStrategyToken" function: https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L331.

## L-28 Use Existing Modifiers Instead
Here the checks https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L144 are already done inside the modifiers at
L69 , L77, L85 . Use that insetad for better code consistency.

## L-29 Have a check for forfeightMultiple that none of the calls would revrt
We should have a check in this function https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L52
that none of the forfeit calls would revert (ensure userClaimedX >= amount) , this can save gas too as if the first two forfeit calls succeeded
but the last one reverted that would waste gas.

## L-30 better to have a check amount > userClaimedWeight
better to have a check amount > userClaimedWeight at https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L71
with an error string so the user knows the call reverted due to insufficient claimed weight.
Same for L78 and L87

## L-31 reward Variable can't be negative
The check here https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L138 is useless since reward is a uint256
which can't be negative , a check for zero value should be enough.

## L-32 Unnecessary check at MultiRewardsDepot contract
The check at https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L48 can be done without the OR operator too
i.e if (_isAsset[asset] ) should be enough.

## L-33 Inconsitencies between function implementations
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L104 This function checks the 0 liquidity case with == while this function
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L129 checks the 0 liquidity at L139 with >

## L-34 Misleading comment on PartnerFactory creating vMaia contracts
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/vMaia.sol#L39-L50
the factory does not actually create vMaia contracts, it adds them to the registry 

## L-35 Large number of interface functions do not exist in the target contract 
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/interfaces/ITalosBaseStrategy.sol#L37-L98
Most of the highlighted functions in the interface are variables that do not exist in the main contract 

## L-36 No point in using castvotebySig over castvote
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L344

## L-37 CEI pattern to prevent re-entrancy violated 
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409-L414


## L-38 Claim reward is buggy 
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L262
All rewards are transferred if 0 is passed



