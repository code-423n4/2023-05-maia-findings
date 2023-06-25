## Title
Lack of indexed parameters in events

## Details and Impact
None of the parameters in the events defined in the GovernorBravoDelegateMaia contract are indexed. Consider indexing event parameters to avoid hindering the task of off-chain services searching and filtering for specific events.

Events defined in GovernorBravoInterfaces.sol but emitted in the GovernorBravoDelegateMaia.sol(in Scope).

## Links
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L6

## Title
Missing SafeCast

## Details and Impact
In the PoolVariables.sol#L246, Use SafeCast to increase the accuracy of the calculation.

## Links
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/libraries/PoolVariables.sol#L246
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/libraries/PoolVariables.sol#L251
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L485
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/libraries/IncentiveTime.sol#L9


## Title
Signatures vulnerable attacks

## Details and Impact
ecrecover() accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin's ECDSA library to perform the extra checks necessary in order to prevent this attack.

## Links
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L43