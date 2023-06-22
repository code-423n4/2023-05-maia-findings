* L-01 Incorrect Natspec
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L111-L130

* L-02 No need to push an optimizer in the constructor as there is no way to delete an optimizer 
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/factories/OptimizerFactory.sol#L24-L28 

* L-03 Outdated Docs in increaseConversionRate in IERC4626PartnerManager.sol 
 https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/interfaces/IERC4626PartnerManager.sol#L64

* L-04 Last 2 variables in DeployArbitrumBranchBridgeAgent.sol deploy function are interchanged
 https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#LL44C2-L45C36

* L-05 Interface functions do not exist on the Optimizer contract
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/interfaces/IOptimizerFactory.sol#L16-L21

* L-06 block.number will not be a reliable source of timing on Optimism
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/VirtualAccount.sol#L46

* L-07 Inconsistent usage of variable naming for mint and burn functions (amount vs. value)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L22C4-L28
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L47-L55

* L-08 Misleading naming of requiresCoreRouter() modifier in ERC20hTokenRootFactory.sol as modifier checks for more than just the core router
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L75

* L-09 Mixed usage of uint24 and uint256 for chain ids. See examples below:
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L378 (uint24)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L47 (uint256)

* L-10 Function "getLocalTokenFromUnder" should be named "getLocalTokenFromUnderlying"
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootPort.sol#L69

* L-11 Unnecessary single-line comment of multiple lines of already existing multi-line comment
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/strategies/TalosStrategySimple.sol#L26-L28

* L-12 Broken sentence in the documentation of ArbitrumCoreBranchRouter.sol
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L21-L23 (some words may be missing in the "Branch Chain is not available" part)

* L-13 Typo in multiple files where it should be "not enough budget" instead of "no enough budget"
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L131-L132
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L98
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L101
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L1230-L1231
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L1320-L1321
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L101
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L104

L-14 Typo in Natspec of IVirtualAccount.sol (should be "performed calls" instead of "perfomed call)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L15

L-15 @notice in Natspec of "createBridgeAgent" function of ArbitrumBranchBridgeAgentFactory is incorrect (should be: "Creates a new bridge agent for root chain." and maybe "root bridge agent" instead of just "bridge agent" as 2 lines below "root bridge agent" is used)
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L75

L-16 Missing zero address check for "_coreRootBridgeAgent" in "initialize" function in ArbitrumBranchBridgeAgentFactory.sol
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L55
Compare with the "initialize" function in BranchBridgeAgentFactory.sol which does this check: https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L84

L-17 Unused "requiresPort" modifier in ERC20hTokenBranchFactory.sol
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L81

L-18 Typo of writing "length" as "lenght" in multiple contracts
https://github.com/search?q=repo%3Acode-423n4%2F2023-05-maia%20lenght&type=code
