# Low-Level and Non-Critical issues

## Summary

### Low Level List

| Number | Issue Details                                                                             | Instances |
| :----: | :---------------------------------------------------------------------------------------- | :-------: |
| [L-01] | All address should be checked for address(0) in constructor specially owner               |     3     |
| [L-02] | Division before multiply can lead to precision loss                                       |     2     |
| [L-03] | Check receiver address for address(0) before transferring or minting any tokens OR Ethers |     2     |

Total 3 Low Level Issues

# Low-Level issues:

## [L-01] All address should be checked for address(0) in constructor specially owner

Setting owner to zero is very troublesome for protocol Or if you want to redeploy, deployment gas cost will occur so it's better to check in constructor address type args to address(0),so that if by mistake address(0) problem would be less at deployment time it will fail.
Check for address(0) using assembly.

_3 Instances_

### 1. \_owner and \_rewardToken can be checked for address(0)

```solidity
File: src/rewards/base/FlywheelCore.sol
45:  constructor(address _rewardToken, address _flywheelRewards, IFlywheelBooster _flywheelBooster, address _owner) {
46:        _initializeOwner(_owner);
        rewardToken = _rewardToken;
        flywheelRewards = _flywheelRewards;
        flywheelBooster = _flywheelBooster;
    }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L45C3-L50C6

### 2. \_owner can be checked for address(0)

```solidity
File : src/rewards/depots/MultiRewardsDepot.sol

29: constructor(address _owner) {
30:   _initializeOwner(_owner);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L29C4-L31C6

### 3. missing address 0 check for immutable variable `_strategyOwner` and `_owner`

```solidity
File: src/talos/base/TalosBaseStrategy.sol
79: constructor(
        IUniswapV3Pool _pool,
        ITalosOptimizer _optimizer,
        INonfungiblePositionManager _nonfungiblePositionManager,
        address _strategyManager,
        address _owner
    ) ERC20("TALOS LP", "TLP", 18) {
        _initializeOwner(_owner);
        optimizer = _optimizer;
        nonfungiblePositionManager = _nonfungiblePositionManager;
89:        strategyManager = _strategyManager;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L79C5-L89C44

## [L-02] Division before multiply can lead to precision loss

Division before multiply can lead to precision loss and can be resulted with unintended consequences.It can be vulnerable to attack also.

_2 Instances_

### Instance#1-2:

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
           // seed initial gaugeCycle
60:       gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
              ...
79:       uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L60

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L79

Recommended Solution : Always prefer to multiply before division, If both in same operation

```diff
- 60:       gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
+ 60:       gaugeCycle = (block.timestamp.toUint32() * gaugeCycleLength) / gaugeCycleLength;
              ...
- 79:       uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
+ 79:       uint32 currentCycle = (block.timestamp.toUint32() * gaugeCycleLength) / gaugeCycleLength;
```

Note: Apart from these, this vulnerability exists in many places across the protocol so updae them before deploying.

## [L-03] Check receiver address for address(0) before transferring or minting any tokens OR Ethers

If you accidentally transfer assets to address(0) they are gone forever so make sure you are transferring to right address.

_2 Instances_

### Instance#1-2: Make sure `to` is not address(0) before transfer

```solidity
168:   address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));
         ...
176:    address(hermesGaugeBoost).safeTransfer(to, amount);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L168
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L176

## Total 4 Non Critical issues

# Non Critcal Issues

## [NC-01] : Named return is confusing

return from function explicitly make code readable

```solidity
File:src/rewards/base/FlywheelCore.sol
157:   function accrueStrategy(ERC20 strategy, uint256 state) private returns (uint256 rewardsIndex) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L157

## [NC-02] :Some Contracts are not following proper solidity style guide layout

In some contract variables are declared after function which not proper layout of solidity

```solidity
File: src/rewards/base/FlywheelCore.sol

137:  function setBooster(IFlywheelBooster newBooster) external onlyOwner {
        flywheelBooster = newBooster;

        emit FlywheelBoosterUpdate(address(newBooster));
    }

    /*///////////////////////////////////////////////////////////////
                    INTERNAL ACCOUNTING LOGIC
    //////////////////////////////////////////////////////////////*/

    /// @notice the fixed point factor of flywheel
    uint256 private constant ONE = 1e18;

    /// @inheritdoc IFlywheelCore
    mapping(ERC20 => uint256) public strategyIndex;

    /// @inheritdoc IFlywheelCore
    mapping(ERC20 => mapping(address => uint256)) public userIndex;

    /// @notice accumulate global rewards on a strategy
    function accrueStrategy(ERC20 strategy, uint256 state) private returns (uint256 rewardsIndex) {
        // calculate accrued rewards through rewards module
        uint256 strategyRewardsAccrued = _getAccruedRewards(strategy);

        rewardsIndex = state;
        if (strategyRewardsAccrued > 0) {
            // use the booster or token supply to calculate the reward index denominator
            uint256 supplyTokens = address(flywheelBooster) != address(0)
                ? flywheelBooster.boostedTotalSupply(strategy)
                : strategy.totalSupply();

168:            uint224 deltaIndex;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L137C3-L168C32

Recommendation: Update using below guide layout of variables
Inside each contract, library or interface, use the following order:

Type declarations
State variables
Events
Errors
Modifiers
Functions

https://docs.soliditylang.org/en/latest/style-guide.html#order-of-layout

## [NC-03] : Start contract name with capital letters

```solidity
Files: src/rewards/booster/FlywheelBoosterGaugeWeight.sol
52:  contract bHermes is UtilityManager, ERC4626DepositOnly {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L52

## [NC-04] : Use {} in if() block it makes code more readable

```diff
- 73:   if (!_isRewardsContract[msg.sender]) revert FlywheelRewardsError();
+ 73:   if (!_isRewardsContract[msg.sender]){
           revert FlywheelRewardsError();
          }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L73

Note: This issue is in many places across the protocol so make sure to update them like above
