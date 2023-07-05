Team member A:

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

Team Member B.

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

=================================================================

Team Member C:

Team member C, QA issue #1:  Unused modifier inside of ERC20hTokenBranchFactory.sol:

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L81-L84

There is an unused requiresPort() modifier inside of ERC20hTokenBranchFactory.sol.  The modifier appears to be useless, as the only external function (createToken) is never called within BranchPort.sol.  Thus, the modifier can be removed.

    modifier requiresPort() {
        if (msg.sender != localPortAddress) revert UnrecognizedPort();
        _;
    }


Team member C, QA issue #2: Unused modifier inside of CoreRootRouter.sol:

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L438-L443

There is an unused lock() modifier inside of CoreRootRouter, and although functions within the contracts appear to be safe from reentrancy, it won’t hurt to use that modifier within all public/external functions in the contract that make external calls (addBranchToBridgeAgent(), toggleBranchBridgeAgentFactory(), removeBranchBridgeAgent(), manageStrategyToken(), managePortStrategy()).

    modifier lock() {
        require(_unlocked == 1);
        _unlocked = 2;
        _;
        _unlocked = 1;
    }


Team member C, QA issue #3: Setting variables to immutable:

3 variables that are used within modifiers never get changed after being set in the constructor, yet they are not marked as immutable (see list below).  Consider making them immutable to make the security/gas efficiency of modifiers more robust.



rootPortAddress within ERC20hTokenRoot is not marked as immutable. 

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L19
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L57-L60

    address public rootPortAddress;
 
Likewise, the bridgeAgentExecutorAddress variable inside BranchBridgeAgent.sol is not naked immutable.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L112
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1374-L1377

    address public bridgeAgentExecutorAddress;
 
 
Strategy inside of BaseV2Gauge is not marked as immutable.

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L41
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L155-L158

    address public override strategy;
 
 


Team member C, QA issue #4: The function ClaimRewards() inside of FlywheelCore.sol does not check that the msg.sender is equal to the inputted user address.  

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L94-L104


This means that the user address can be arbitrarily inputted, and rewards can be claimed on behalf of anyone, even if the inputted user doesn’t wish for their rewards to be claimed.  This would not result in lost funds (due to the fact that accrued rewards would still make their way to the rightful owner), but it is an issue of timing.  Giving anybody the right to claim rewards on behalf of others seems unnecessary within the ecosystem because there are no other contracts that call claimRewards().  



Overall, it might be worth considering an additional check that requires msg.sender equals the inputted user address.  This would ensure that msg.sender would only have the right to claim their own rewards, rather than the rewards of others.

    function claimRewards(address user) external {
        uint256 accrued = rewardsAccrued[user];

        if (accrued != 0) {
            rewardsAccrued[user] = 0;

            rewardToken.safeTransferFrom(address(flywheelRewards), user, accrued);

            emit ClaimRewards(user, accrued);
        }
    }

