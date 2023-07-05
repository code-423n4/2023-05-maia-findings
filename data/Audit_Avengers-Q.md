A.

This report evaluates the tick range and minimum width considerations in the MaiaDAO Uniswap V3 staking protocol.

**Assumptions:**
- The tick spacing of the pool is set to a constant value greater than zero during pool deployment.
- It is assumed that the pool deployment is not compromised by a rogue owner/attacker.
- The ability to mint Talos LP NFTs with a tick range of zero.
- The ability to set the gauge minimum width to zero.
- The ability to set the gauge minimum width to the maximum or near-maximum value of uint24.

**Findings:**
The primary concern is the invariant validity of the `if` statement that checks the tick range against the gauge's minimum width, against all possible cases/scenarios:

https://github.com/code-423n4/2023-05-maia/blob/7492906aff17e3ee8d7773ba5bf51a171051e401/src/uni-v3-staker/UniswapV3Staker.sol#L489

```solidity
if (uint24(tickUpper - tickLower) < poolsMinimumWidth[pool]) revert RangeTooSmallError();
```

To ensure the effectiveness of this check, have evaluated its behavior across different scenarios.

**Case A: Stablecoin Pool**
1) If `poolsMinimumWidth[pool]` is set to 0, staking with a tick range of zero continues, which is not ideal as the minimum range should match the pool's tick spacing for accurate reward calculations.
2) If `poolsMinimumWidth[pool]` is set to 1, staking with a tick range of zero reverts, which is expected.
   - Staking with a tick range greater than or equal to 1 continues.

**Case B: Non-Stablecoin Pool**
1) If `poolsMinimumWidth[pool]` is set to 0, staking with a tick range of zero continues, which can interfere with reward calculations / prevent rewards from being earned via that gauge.
2) If `poolsMinimumWidth[pool]` is set to 1, staking with a tick range of zero reverts, as intended.
   - Staking with a tick range greater than or equal to 1 continues.
3) If `poolsMinimumWidth[pool]` is set to the maximum value for the uint24 type, staking with a tick range of zero reverts, as expected.
   - Staking with a tick range greater than or equal to 1 but smaller than the maximum value reverts, which will lead to a denial-of-service (DoS) of the staking service via that specific gauge.

**Recommendations:**
1. Ensure Minimum Tick Range Requirement: Review the staking logic to enforce that the tick range must be greater than or equal to the pool's tick spacing to ensure accurate reward calculations.

2. Mitigate DoS on gauge's staking service: Implement a check to prevent gauge owners from setting the minimum width of tick ranges excessively high, which could lead to DoS on the staking mechanism via that specific gauge.

=====================================================

B.

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

