## Issues

partnerVault & partnerGovernance addresses provided to the constructor are missing zero address checks, this could result in unexpected behavior when attempting to use the contract.

For example, several functions rely on the partnerVault contract which would be rendered unusable

- forfeitWeight()
- forfeitBoost()
- forfeitGovernance()

[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/PartnerUtilityManager.sol#LL36C3-L45C3)

File:   src/maia/PartnerUtilityManager.sol

```
    constructor(
        address _gaugeWeight,
        address _gaugeBoost,
        address _governance,
        address _partnerGovernance,
        address _partnerVault
    ) UtilityManager(_gaugeWeight, _gaugeBoost, _governance) {
        partnerGovernance = ERC20Votes(_partnerGovernance); // no zero address check
        partnerVault = _partnerVault; // no zero address check
    }

```

## Recommendation

Implement a zero address check using the require function and the != (inequality) operator with address(0).

### Example:
```
// zero address check governance and vault contracts.
require(partnerGovernance != address(0), "Invalid partner governance address");
require(partnerVault != address(0), "Invalid partner vault address");

// assign after successful zero address checks.
partnerGovernance = ERC20Votes(_partnerGovernance);
partnerVault = _partnerVault;
```