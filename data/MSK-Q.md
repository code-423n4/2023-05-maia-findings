## [L-01] ```queueRewardsForCyclePaginated``` have  ```reentrancies``` that allow manipulation of the order or value of events.
##### Vulnerable Code 
``` solidity 
//External calls
minter.updatePeriod()
newRewards = minter.getRewards()
//Events Emitted After The Call
CycleStart(currentCycle,queued)
_queueRewards(gauges,currentCycle,lastCycle,queued)
```
This may cause issues for offchain components that rely on the values of events e.g. checking for the amount deposited to a bridge.
##### Vulnerable Code Link 
[FlywheelGaugeRewards.sol#L107](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L107)
##### Recommended Pattern 
Apply the ```check-effects-interactions``` pattern.

