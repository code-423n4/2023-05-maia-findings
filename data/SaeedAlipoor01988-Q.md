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


## Title
ERC4626DepositOnly is not EIP-4626 complaint

## Details and Impact
ERC4626DepositOnly contract is supposed to be ERC4626-compliant but some functionality of the code doesn't follow the standard. maxDeposit() and maxMint() functions in the ERC4626DepositOnly contract, return value uint256.max which is wrong and they should consider HERMES token supply when calculating maximum deposits. This can cause 3rd party contract to fail to integrate with protocol because PublicVault doesn't fully comply with ERC4626 standard.

## Links
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626DepositOnly.sol#L98
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626DepositOnly.sol#L103