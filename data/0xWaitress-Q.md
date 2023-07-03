[QA-1]
the bool isNotRestake is always passed as `true` in _unstakeToken in UniswapV3Staker. This seems unnecessary.

There are 3 instances of usage:

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L342
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L357
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L362

all of which pass true for the bool `isNotRestake`. 

Secondly, the variable isNotRestake does not affect the execution context, so it's quite redundent and a check of "block.timstamp < endTime" before the call to _unstakeToken would be sufficient to achieve the current logic.


## Recommendation
remove isNotRestake or refactor it to implement the logic that `block.timestamp < endTime` is ensured before even calling `_unstakeToken`


[QA-2] flywheelGaugeRewards cannot be updated after being initialised on BaseV2Minter, thus should be verified that it's not a zero address.

```solidity
    function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {
        if (initializer != msg.sender) revert NotInitializer();
        flywheelGaugeRewards = _flywheelGaugeRewards;
        initializer = address(0);
        activePeriod = (block.timestamp / week) * week;
    }
```


## Recommendation
```solidity
+++ require(_flywheelGaugeRewards != address(0));
```


[QA-3] createPools at UlyssesFactory can bundle calldata to remove length checks and improve readability.


```solidity
function createPools(ERC20[] calldata assets, uint8[][] calldata weights, address owner)
```

## Recommendation 
```
struct poolInput {
   ERC20 asset;
   uint8[] memory weights;
}

function createPools(poolInput[] calldata inputs, address owner) 
```