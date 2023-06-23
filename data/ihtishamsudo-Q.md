## [L-01] Division Before Multiplication (Instances Missed By The Bot)
Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.

Vulnerable Code 
``` solidty
uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```
If ```WEEK``` Value Is Somehow Greater Than ```block.timestamp```, Then The Value of ```uint256 _newEpoch``` Will Be Zero.
##### Vulnerable Code Link
[BaseV2Guage.sol#L84](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/BaseV2Gauge.sol#L84)
[BaseV2Guage.sol#L68](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/BaseV2Gauge.sol#L68)
[FlywheelAcummulatedRewards.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50)
[FlywheelGaugeRewards.sol#60](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L60)
[FlywheelGaugeRewards.sol#79](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L79)
[FlywheelGaugeRewards.sol#114](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L114)

##### Tool Used 
Manual / Slither
##### Reference
[Division Before Multiplication](https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply)