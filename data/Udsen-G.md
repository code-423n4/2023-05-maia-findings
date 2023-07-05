## 1. `for` LOOP COMPLETES ALL ITS ITERATIONS EVEN IF THE TRANSACTION IS GOING TO REVERT LATER

In the `ERC4626MultiToken.convertToShares()` function if any of the `assetsAmounts[i]` happens to be `0`, it will always output `shares = 0`. This will eventually prompt the entire transaction to revert since the following condition will revert in the `ERC4626MultiToken.deposit()` function.

    require((shares = previewDeposit(assetsAmounts)) != 0, "ZERO_SHARES")

But the problem is if the `assetsAmounts[0] = 0` then the entire `for` loop will still execute even though the end result is already confirmed which is the transaction reversion. Hence this is unneccasary gas wastage on the `msg.sender`. 

Hence it is recommended to revert the transaction inside the `ERC4626MultiToken.convertToShares()` function if the `assetsAmounts[i] == 0` condition occurs, so redundant function execution can be ommitted. This is recommended if all the `assetsAmounts` should be non-zero.

If it is acceptable for any of the `assetsAmounts[i]` to zero, then we can bypass the case where the `assetsAmounts[i] == 0` by using the `continue` keyword and calculate the shares for the remaining `assetsAmounts`.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L100
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L201-L206

Similar to the above it is recommended to check for `shares != 0` in the `ERC4626MultiToken.mint()` function. Because if the `shares == 0` then both the `ERC4626MultiToken.previewMint` and `ERC4626MultiToken.receiveAssets` functions will be executed with thier underlying `for` loops iterating over the length of the `assets` array. But the net result of this will be zero. Hence it is recommended to revert the transaction if the `shares == 0` when calling the `ERC4626MultiToken.mint()` function.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L119
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L122
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L235-L240
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L216-L221

## 2. `if` CHECK SHOULD BE PERFORMED BEFORE THE `state` VARIABLE ASSIGNMENT

In the `UlyssesERC4626.constructor()` the `ERC20(_asset).decimals() != 18` check is performed after the assigning the `_asset` to the `asset` state variable. Hence if the transaction reverts due to `decimals != 18` this will result in loss of deployment gas fees of one `SSTORE` which could amount to `5000` gas units.

Hence it is recommended to first perform the `if` check and then perform the state variable assignment.

Hence the constructor can be udpated as follows:

    constructor(address _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, 18) {
        if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();

        asset = _asset;        
    }

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L24-L28

## 3. `uint224` VARIABLE CAN BE DECLARED AS `uint256` TO SAVE GAS

In the `FlywheelCore.accrueStrategy()` function, the `deltaIndex` is used to calculate the strategy rewards per token and add it to the current value of `rewardsIndex`. Here the `deltaIndex` is declared as `uint224`. But this serves no purpose since all the other local variables are declared as `uint256`. 

Since `delataIndex` is of `uint224` there is more gas usage since the variable's first `224` bits needs to be extracted from the `256` bit long memory word during `MLOAD` in following operation.

            rewardsIndex += deltaIndex;

Further to this there needs to be safe down casting performed to downcast `uint256` to `uin224` in the following operation which is gas consuming as well.

            if (supplyTokens != 0) {
                deltaIndex = ((strategyRewardsAccrued * ONE) / supplyTokens).toUint224();
            }

Both of above gas usages can be ommitted by declaring the `deltaIndex` as a `uint256`, since declaring it as a `uint224` serves no purpose that could benefit the `FlywheelCore.accrueStrategy()` function implementation.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L175
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L170-L172

## 4. USE `delete` TO CLEAR STORAGE TO GET A GAS REFUND

In the `FlywheelGaugeRewardsqueueRewardsForCycle()` function the following storage variables are reassigned to `0`. But it is recommended to `delte` these storage variables to get a gas refund of `15,000` each.

        nextCycleQueuedRewards = 0;
        paginationOffset = 0;

The updated code should be as follows:

        delete nextCycleQueuedRewards;
        delete paginationOffset;

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L100-L101
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L100-L122
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L145-L146
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L165

## 5. SUBSTRACTION OPERATION IN EMISSION CALCULATION CAN BE `unchecked`

The `BaseV2Minter.circulatingSupply()` function is used to calculate the circulating supply of the `hermes` token which will inturn be used to calculate the weekly reward emission amount in the `BaseV2Minter` contract.

The circulating supply is calculated as follows:

        return HERMES(underlying).totalSupply() - vault.totalAssets();

The above can be `unchecked` since it can not underlfow. Since the `totalSupply` of the token is the existing amount of the token and it is anyway greater than the `vault.totalAssets()` amount.

Hence above can be updated as follows:

    unchecked {
        return HERMES(underlying).totalSupply() - vault.totalAssets();
    }

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L109

## 6. EMPTY `if` BLOCK CAN BE REFACTORED TO SAVE GAS 

In the `FlywheelGaugeRewards.getAccruedRewards()` function, `if/else` statement is used to verify whether a cycle is incomplete or not and to execute the function body accordingly. But there is an empty `if` clause in the satement as shown below:

        if (incompleteCycle) {
            // If current cycle queue incomplete, do nothing to current cycle rewards or accrued
        } else {
            accruedRewards += cycleRewardsNext;
            cycleRewardsNext = 0;
        }

Above `if/else` block can be refactored as follows to save gas.

        if (!incompleteCycle) {
            accruedRewards += cycleRewardsNext;
            cycleRewardsNext = 0;
        }

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L221-L226

## 7. CONDITIONAL CHECKS CAN BE PERFORMED PRIOR TO `for` LOOP EXECUTION TO SAVE GAS IN THE EVENT OF A `revert`

In the `UlyssesPool.setWeight` function, there is a `oldTotalWeights == newTotalWeights` condition inside a `if` close if results in `true` will revert the transaction. But this check is performed after a `for` loop iteration with length of `bandwidthStateList.length` as shown below:


        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

            oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
        }

        ...

        if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights) {
            revert InvalidWeight();
        }

 and if the transaction is reverted due to `oldTotalWeights == newTotalWeights` condition the entire gas spent on the `for` loop execution will be lost.

But the same conditon can be checked as follows before the `for` loop execution and revert then and there thus saving gas of `for` loop execution since `for` loop is not exeucted yet.

This is because the onlyway `oldTotalWeights == newTotalWeights` could be true is if `bandwidthStateList[poolIndex].weight == weight`.

    if(bandwidthStateList[poolIndex].weight == weight){
        revert InvalidWeight();
    }

    for (uint256 i = 1; i < bandwidthStateList.length; i++) {
        uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

        oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
    }    

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L243-L245
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L232-L236

## 8. VARIABLE DECLARATION CAN BE DONE OUTSIDE THE `for` LOOP AND NO NEED TO `initialize` TO THE `default` VALUE OF `0`

`uint256 i` can be declared outside the `for` loop and no need to `initialize` to the `defaul` value of `0`.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L50
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L68
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L79
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L201
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L202
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L235
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L250
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L216
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L176

## 9. `state` VARIABLES SHOULD BE CACHED INTO `memory` VARIABLES WHEN ITERATING THROUGH A `for` LOOP

`weights[i]` is a state variable storing the weightages of the assets of the vault. It is called inside a `for` loop till `i < assets.length` in the `ERC4626MultiToken.convertToShares` function. Hence it is recommended to cache the `storage array` into a `memory array` and read from there. This will save multiple `SLOAD`s. Currently a single `SLOAD` costs `800` gas unit. And an MLOAD will only cost `3` gas units.

Hence by moving into a memeory array for the `weights[i]` we can save `797` gas units per each iteration. This will result in a considerable gas saving if the `assets.length` is a higher value.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L202
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L236
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L251
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L217