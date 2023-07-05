## 1. VAULT ACCEPTS LIMITED NUMBER OF ASSETS

In the `ERC4626MultiToken.constructor()` following `require` statement is performed to make sure only assets with `18` decimal places are accepted as deposits to the vault. There are multiple ERC20 assets which are not 18 decimal numbers. Hence the protocol vault limits the number of assets that can be deposited into the vault thus limiting the user experience.

         require(ERC20(_assets[i]).decimals() == 18);

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L51

## 2. `assetsAmounts` PASSED IN COULD BE OUT OF ORDER IN COMPARISON TO `assets` ARRAY

In the `ERC4626MultiToken.convertToShares()` function, `assetsAmounts` are used to calculate the shares to mint. Here the `assetsAmounts` are expected to be passed in the order of the `assets` array. If there is any misconfiguration then it will effect the internal `shares` calculation since the `weights[i]` would be different for the respective `assetsAmounts[i]`. Hence the minted `share` amount could be errorneous which could impact the overall accounting of the protocol.

Hence it is recommended to implmenet a validation to verfiy that the `assetsAmounts` are in the correct order of the `assets` array. Can be done by passing in a mapping which includes the `assetId` for the respective `assetAmount` when calling the `ERC4626MultiToken.deposit()` function.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L202
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L69

## 3. WHEN EMITTING EVENTS INCLDUE BOTH OLD AND NEW VALUES DURING CRITICAL PARAMETER CHANGES

        emit FlywheelBoosterUpdate(address(newBooster));

In the above it is recommended inclued the old booster address as well in the event to emitted.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L140
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L65
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L548
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L558

## 4. CRITICAL STATE CHANGES SHOULD EMIT `events`.

The `BaseV2Minter` contract has three setter functions to update three state variables (`dao, daoShare, tailEmission`). But none of the above setter functions emit `events`. It is recommended to emit events for critical state parameter changes at the end of the respective functions.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L88
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L94
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L100

## 5. `require` STATEMENTS SHOULD HAVE DESCRIPTIVE REVERT MESSAGES

Following instance there is no descriptive revert message in the `require` statement.

        require(queuedRewards.storedCycle < currentCycle);

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L182

## 6. USE `require` INPLACE OF `assert` STATEMENT.

Prior to solidity version 0.8.0, hitting an assert consumes the ** remainder of the transaction's available gas** rather than returning it, as require() / revert() do. assert() should be avoided even past solidity version 0.8.0 as itsdocumentation states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

`assert` statement is used for invariants which are always true. Hence in the smart contract it is recommened to replace the `assert` with the `require` statement.

        assert(queuedRewards.storedCycle == 0 || queuedRewards.storedCycle >= lastCycle);

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L183
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L215

## 7. THERE IS NO `address(0)` CHECK FOR `to` ADDRESS

The `UniswapV3Staker.claimReward` and `UniswapV3Staker.claimRewards` function are used to let the users claim thier respective rewards. Here the users are allowed to pass in `to` address to which the rewards will be transferred. But there is no input validation for the `to` address. But there is input validation for the `amountRequested != 0`.

If user mistakenly passes in default value for the `to` address, his rewards will be sent to the `address(0)`, as a result the rewards will be permenantly lost. Hence it is recommended to perform input validation of `address(0)` for the `to` address.  

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L262-L274
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L277-L284

## 8. TYPOS IN THE COMMENTS

    // Own Scope to avoid stack `to` deep
    This should be corrected as follows:
    // Own Scope to avoid stack `too` deep

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L111

    /// @notice Transfer balance of token to rewards contract
    This should be corrected as follows:
    /// @notice Transfer balance of token to rewards `manager` contract

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L18

    /// @notice calculate and transfer accrued rewards to `flywheel core`
    This should be corrected as follows:
    /// @notice calculate and transfer accrued rewards to `flywheel rewards manager`

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelBribeRewards.sol#L31

    /// @dev Update minter cycle and queue `rewars` if needed.   
    This should be corrected as follows:
    /// @dev Update minter cycle and queue `rewards` if needed.  
	
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L73
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L108
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L201	

    // include `uncompleted` cycle    
    This should be corrected as follows:
    // include `incompleted` cycle
	
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L92


## 9. INITIALIZATION TO DEFUALT VALUE CAN BE OMMITTED.

    bool private stakeFlag = false;
	
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L52

## 10. BOOLEAN RETURN VALUE OF `transfer` AND `transferFrom` IS NOT CHECKED.

It is recommended to check for the return value and revert if the return value is `false` or else use `safeTransfer` and `safeTransferFrom`, from the `openzeppelin` `safeERC20.sol` library.

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L91
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L96

## 11. `address(0)` CHECK IS NOT PERFORMED FOR THE CRITICAL INPUT PARAMETERS IN THE CONSTRUCTOR

check for `address(_bHermesGauges) != address(0)` else in the worse case scenario could lead to redeployment (if the `_bHermesGauges` is uninitialized).

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L48-L50
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L55
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L62
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L30
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L80