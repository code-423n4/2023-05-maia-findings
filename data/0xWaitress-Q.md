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


[QA-2] afterCreateGauge in UniswapV3GaugeFactory is implemented but not exposed, nor internally called in any function.

UniswapV3GaugeFactory is an actual implemented contract. Its function `afterCreateGauge` aims to update the staker to get the latest minimumWidth as well as strategy address. However the function is internal, and not called anywhere in the codebase or UniswapV3GaugeFactory. This creates confusion to where the function should be used.

```solidity
    function afterCreateGauge(address strategy, bytes memory) internal override {
        uniswapV3Staker.updateGauges(IUniswapV3Pool(strategy));
    }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L89-L91

## Recommendation 
Either use the internal function somewhere or expose it as a public/external function.