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







