## [L-01] Division Before Multiplication (Instances Missed By The Bot)
Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.

Vulnerable Code 
``` solidity
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
##### Recommended Mitigation Step
Consider ordering multiplication before division.
##### Reference
[Division Before Multiplication](https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply)

## [L-02] ```ERC20Boost.decrementGaugeBoost``` ignores return value by ```_userGauges[msg.sender].remove(gauge)``` 
The return value of an external call is not stored in a local or state variable. It fails to handle the return value when attempting to remove the gauge associated with the user, which could potentially allow an attacker to bypass gauge removal and manipulate the contract state.
Vulnerable Code
``` solidty
_userGauges[msg.sender].remove(gauge);
```
##### Vulnerable Code Link 
[ERCBoost.sol#L178](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L178)
[BaseV2Guage.sol#L115](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/BaseV2Gauge.sol#L115)
[FlyWheelGuageRewards.sol#L76](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L76)
##### Tools Used 
Slither
##### Recommended Mitigation Step
Ensure that all the return values of the function calls are used.
##### Reference 
[Unused Return](https://github.com/crytic/slither/wiki/Detector-Documentation#unused-return)
## [L-03] ```IUniswapV3Staker.tokenIdRewards``` Shadows ```IUniswapV3Staker.rewards```
##### Vulnerable Code Link
[IUniswapV3Staker.sol#L156](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L156)
[IUniswapV3Staker.sol#L162](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L162)
##### Tools Used
Slither
##### Recommended Mitigation Step
Rename the local variables that shadow another component.
##### Reference
[Local Variable Shadowing](https://github.com/crytic/slither/wiki/Detector-Documentation#local-variable-shadowing)
## [L-04] ```UtilityManager.checkWeight``` does not always execute _; or revertModifier
If a modifier does not execute _ or revert, the execution of the function will return the default value, which can be misleading for the caller.
Vulnerable Code 
``` solidity
    modifier checkWeight(uint256 amount) virtual;
```
##### Vulnerable Code Link
[UtitlityManager.sol#L141](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/UtilityManager.sol#L141)
[UtitlityManager.sol#L145](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/UtilityManager.sol#L145)
[UtitlityManager.sol#L147](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/UtilityManager.sol#L147)
[RewardsDepot.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/depots/RewardsDepot.sol#L24)
##### Tools Used 
Slither
##### Recommended Mitigation Step 
All the paths in a modifier must execute _ or revert.
##### Reference 
[Incorrect Modifier](https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-modifier)
## [L-05] Dangerous Usage Of ```block.timestamp```
```block.timestamp``` can be manipulated by miners. Suppose Bob's contract relies on ```block.timestamp``` for its randomness. Eve is a miner and manipulates ```block.timestamp``` to exploit Bob's contract.
Vulnerable Code 
``` solidity
            if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError();
```
##### Vulnerable Code Link
[ERC20Guages.sol#L205](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L205)
[FlywheelAcummulatedRewards.sol#L46](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L46)
##### Tools Used 
Solidity Visual Developer / Slither 
##### Recommended Mitigation Steps
Avoid relying on ```block.timestamp```
##### Reference 
[block.timestamp](https://github.com/crytic/slither/wiki/Detector-Documentation#block-timestamp)
