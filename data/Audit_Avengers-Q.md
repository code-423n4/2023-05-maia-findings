# Issues
- _bHermes address provided to the constructor is missing a zero address check. This could result in unexpected behavior when attempting to use the contract.
- _owner address provided to the constructor is missing a zero address check. This could result in the contract being unusable due to the owner being a zero address.

[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/factories/PartnerManagerFactory.sol#LL37C4-L41C4)

- _owner address provided to the constructor is missing a zero address check. This could result in the contract being unusable due to the owner being a zero address.
-- confirmed no effect/no revert to the creation of the optimizer using the testCreateTalosOptimizer found in: test/talos/factories/OptimizerFactoryTest.t.sol adjusting the owner address to a zero address using `owner = address(0);`

[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/TalosOptimizer.sol#L34C5-L55C6)

File:   src/maia/factories/PartnerManagerFactory.sol
```
constructor(ERC20 _bHermes, address _owner) {
        _initializeOwner(_owner); // no zero address check for owner, zac implemented in other contracts e.g. UlyssesFactory.sol
        bHermes = _bHermes; // no zero address check
        partners.push(PartnerManager(address(0)));
    }
```

File: src/talos/TalosOptimizer.sol
```
constructor(
        uint32 _twapDuration,
        int24 _maxTwapDeviation,
        int24 _tickRangeMultiplier,
        uint24 _priceImpactPercentage,
        uint256 _maxTotalSupply,
        address _owner // no zero check for owner
    ) {
        if (_maxTwapDeviation < 20) revert MaxTwapDeviationTooLow();
        if (_twapDuration < 100) revert TwapDurationTooLow();
        if (_priceImpactPercentage >= 1e6 || _priceImpactPercentage == 0) {
            revert PriceImpactPercentageInvalid();
        }
        if (_maxTotalSupply == 0) revert MaxTotalSupplyIsZero();

        _initializeOwner(_owner);
        twapDuration = _twapDuration;
        maxTwapDeviation = _maxTwapDeviation;
        tickRangeMultiplier = _tickRangeMultiplier;
        priceImpactPercentage = _priceImpactPercentage;
        maxTotalSupply = _maxTotalSupply;
    }
```
## Recommendation
Implement a zero address check using the require function and the != (inequality) operator with address(0).

### Example:
```
// zero address check nHermes token contract.
require(_owner != address(0), "Owner cannot be 0");
require(bHermes != address(0), "Invalid partner bHermes token address");

// assign after successful zero address checks.
_initializeOwner(_owner);
bHermes = _bHermes;
partners.push(PartnerManager(address(0)));
```

