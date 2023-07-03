## QA Report for Maia dao ecosystem contest

### Overview


| |Issue|
|-|:-|
| [01] | `MultiRewardsDepot#removeAsset` IS DEFINED BUT NEVER USED |
| [02] | `ERC20Gauges#replaceGauge` IS DEFINED BUT NEVER USED |
| [03] | USER IS OBLIGATED TO UNSTAKE AND RESTAKE TO BOOST HIS POSITION |
| [04] | EVENT NOT EMMITED WHEN UPDATING CRITICAL PARAMETER |
| [05] | THE FIRST `partnerVault` EVER IS NOT APPROVED TO USE UTILITY TOKENS |
| [06] | PARTNER GOVERNANCE TOKENS ARE CLAIMED BUT THEY ARE ALREADY IN `ERC4626PartnerManager`|
| [07] | `BaseBranchRouter` MISSES `retrieveDeposit` FUNCTION |
| [08] | FUNCTION NAMING IS CONFUSING |
| [09] | `BranchBridgeAgent._sendRetrieveOrRetry`  MISSING NATSPEC COMMENTS |

## [01] `MultiRewardsDepot#removeAsset` IS DEFINED BUT NEVER USED 

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L57

`ERC20Gauges#replaceGauge` can only be called by the owner, which is `BaseV2GaugeManager`, but it does not have a function with the capability of calling it, thus `ERC20Gauges#replaceGauge` is defined but never used .

## [02] `ERC20Gauges#replaceGauge` IS DEFINED BUT NEVER USED 

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L449

MultiRewardsDepot#removeAsset can only be called by the owner, which is BaseV2Gauge, but it does not have a function with the capability of calling it, thus MultiRewardsDepot#removeAsset is defined but never used .

## [03] USER IS OBLIGATED TO UNSTAKE AND RESTAKE TO BOOST HIS POSITION

If a user thinks that his rewards are not enough and  want’s to boost ( or boost more )  his staking position he needs to restake ( gas consuming / not front-end friendly) . ( not mentionned in the docs)

## [04] EVENT NOT EMMITED WHEN UPDATING CRITICAL PARAMETER

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L216

`ERC4626PartnerManager#increaseConversionRate` update critical parameter but does not emit an event .

## [05] THE FIRST `partnerVault` EVER IS NOT APPROVED TO USE UTILITY TOKENS

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L188

in `ERC4626PartnerManager`, the first partnerVault ever is not approved to use utility tokens, but you have to set address(0) in the constructor and than call `migratePartnerVault` with the partner vault address, which is not good for consistency, it should be approved it in the constructor .

## [06] PARTNER GOVERNANCE TOKENS ARE CLAIMED BUT THEY ARE ALREADY IN `ERC4626PartnerManager`

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L96

`ERC4626PartnerManager` calls `claimOutstanding` in different functions which calls `claimPartnerGovernance` and since PartnereGovernance tokens are already ERC4626PartnerManager its not coeherent .

## [07] `BaseBranchRouter` MISSES `retrieveDeposit` FUNCTION

according to the documentation the Branch Router is the main users entry-point, and `retrieveDeposit` is for users, it must be callable via `BaseBranchRouter`, it is still accessible via Branche Bridge Agent, but it should be also in `BaseBranchRouter`.

## [08] FUNCTION NAMING IS CONFUSING

`ERC20Boost#decrementGaugeBoost` serves the off-case where users don't wan't to spend gas detatching and re-attatching boost during the period the gauge is disabled since the same boost can be attached to several gauges unlike votes it makes sense to accommodate for this even more being a cross-chain available system! but the name of the function do not reflect the function's purpose .

## [09] `BranchBridgeAgent#_sendRetrieveOrRetry` MISSING NATSPEC COMMENTS
 
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L441