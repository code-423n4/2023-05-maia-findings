# Informational Report

## 1. `isSupportedDays` never used
*[DateTimeLib.sol#L38](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/libraries/DateTimeLib.sol#L38)*

### Description
The comment explicitly states to use `isSupportedDays` to validate the input, but this is not done.

### Remediation
Include additional validation on the input parameter, as the comment suggests.

## 2. Return variable for `_period`
*[BaseV2Minter.sol#L123](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L123)*

### Description
The variable `_period` is defined and explicitly returned at the end of the function. You can remove the final return statement by defining `_period` in the function definition.

### Remediation
Define the return variable `_period` as part of the function definition.

## 3. `performData defined at the start of the function
*[TalosManager.sol#L91](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L91)*

### Description
The return variable `performData` value is an empty string in this function. The function can be refactored for simplicity and readability.

### Remediation
Move the assigning of `performData = "";` to the top of the function. In the return statement, in the catch, [TalosManager.sol#L98](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L98), change the value from an empty string to `performData`. This change ensures consistency in what is returned and improves readability.

## 4. Untested functionality
*[TalosManager.sol#L112](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L112)*

### Description
The functions `checkUpkeep()` and `performUpkeep()` are never tested.

### Remediation
Include additional unit tests to verify the intended functionality of these functions.

## 5. Duplication of logic can be refactored
*[TalosBaseStrategy.sol#L166-L174](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166-L174), [TalosBaseStrategy.sol#L226-L234](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L226-L234)*

### Description
There is an exact duplication of the logic between [TalosBaseStrategy.sol#L166-L174](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166-L174) and [TalosBaseStrategy.sol#L226-L234](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L226-L234). The two if statements in the duplicated logic share similarities that warrant a refactor into a distinct helper function.

### Remediation
Create a new internal called `validateAndTransfer()` that takes three parameters:

- `uint256 amount`
- `uint256 amountDesired`
- `ERC20 _token`

This helper function can implement the logic of a single if statement: [TalosBaseStrategy.sol#L166-L169](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166-L169) and can replace the duplicated code.

## 6. Generic require error message
*[FlywheelCore.sol#L116](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L116)*

### Description
The error message in the `require` check of `_addStrategyForRewards` says "strategy."

### Remediation
Change the error message to something more descriptive "This strategy has already been added."

## 7. Missing event emit
*[TalosStrategyStaked.sol#L140](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L140)*

### Description
The function `_earnFees()` in [TalosStrategyStaked.sol#L140](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L140)closely resembles the `_earnFees()` function in [TalosStrategyVanilla.sol#L104](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L104). However, in [TalosStrategyStaked.sol#L140](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L140) the function does not emit the `CollectFees` event.

### Remediation
Add the `CollectFees` event at the end of the `_earnFees()` function.

## 8. Poorly named event
*[TalosBaseStrategy.sol#L394](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L394)*

### Description
The `collectProtocolFees()` function emits an event named `RewardPaid.` This event name must be clarified since the function works with fees, not rewards.

### Remediation
Modify the event name to be more descriptive and align it with the actions performed within the function, for example, `ProtocolFeesPaid.`
