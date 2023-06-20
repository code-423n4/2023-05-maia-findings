https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/PartnerUtilityManager.sol#L168
modifier checkPartnerGovernance(uint256 amount) virtual;
In Solidity, modifiers cannot be declared as virtual or overridden in derived contracts. They are meant to modify the behavior of functions within the same contract and do not support inheritance or virtual dispatch.
