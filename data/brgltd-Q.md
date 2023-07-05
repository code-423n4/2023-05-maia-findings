| ID    | Title |
| -------- | ------- |
| 01 | Check for contract existence when transferring amounts for arbitrary tokens contracts 
| 02 | Prevent SUBMISSION_INTERVAL from being zero
| 03 | Prevent small first deposit to protect against frontrunning the first deposit inflation of shares vulnerability
| 04 | Unbounded loop
| 05 | Emit events before external calls
| 06 | Lack of CEI
| 07 | Detach logic could be refactored into a single function
| 08 | Declare state variables before they are used
| 09 | Redundant require statement
| 10 | Redundant function
| 11 | Misleading comment
| 12 | Better naming for modifiers
| 13 | State variable could be public
| 14 | Require vs custom error
| 15 | Usage of returned named variables and manual return on the same contract
| 16 | Modifier could be reused
| 17 | `PartnerUtilityManager.forfeitPartnerGovernance()` should emit an event
| 18 | Add descriptive error messages
| 19 | Consistent naming for function arguments
| 20 | Use SafeCast directly
| 21 | Use the conditional directly
| 22 | `UlyssesToken.removeAsset()` can be improved
| 23 | Misleading custom error name
| 24 | Function `IncentiveTime.getEnd()` can be reused
| 25 | Downcasting block.number
| 26 | Elliptic curve recovery function
| 27 | Lack of amount zero check in `PartnerUtilityManager.claimPartnerGovernance()`
| 28 | Check if hermes reward is valid when creating an incentive
| 29 | Missing event on parameter changes
| 30 | Lack of old and new value in event
| 31 | Lack of check for stale values
| 32 | Lack of comments in struct field
| 33 | Use time units 
| 34 | Multiples of ten should use scientific notation.
| 35 | Imports should be grouped

## [01] Check for contract existence when transferring amounts for arbitrary tokens contracts 

Since solady won't check for [contract existence](https://github.com/Vectorized/solady/blob/main/src/utils/SafeTransferLib.sol#L10-L11) when making a transfer, consider checking for contract existence to avoid creating balances or updating state variables for not yet existing contracts. 

This could open the door for an attack vector where an attacker makes a transfer for non yet existing tokens and steal funds from future users in case the contracts gets created on the same address provided by the attacker. For this, the attacker would need to know the address for the token beforehand which can be challenging, but since some protocols use the same address on different chains, e.g. $1INCH is using the same token address for Ethereum and BSC, the attack vector is possible.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L36

## [02] Transfer will not work on weird but widely used ERC20s like USDT and BNB

Consider replacing transfer with safeTransfer on `TalosBaseStrategy.collectProtocolFees()` to support non standard tokens that don't return a boolean.

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409-L410

## [03] Prevent small first deposit to protect against frontrunning the first deposit inflation of shares vulnerability

Frontrunning the first depositor with a small deposit and transferring a large amount of assets to increase the `totalAssets()` to steal shares from future users is a [know exploit](https://github.com/transmissions11/solmate/issues/178) for ERC4626.

Although the protocol is preventing zero shares deposits, currently it would be possible to deposit 1 wei on the first deposit

Therefore, it would be useful to add an extra validation to set a min acceptable value for the first deposit.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L34

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L120-L122

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L106-L110

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L100

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L225-L227

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L194-L208

## [04] Unbounded loop

Currently, `gauges` can grow indefinitely, e.g. there's no maximum limit. If the array grows too large, calling `BaseV2GaugeFactory.newEpoch()` might run out of gas and revert.

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L73-L84

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L87-L100

Consider assigning a maximum size limit for `gauges` in `BaseV2GaugeFactory.createGauge()`.

## [05] Emit events before external calls

Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L72-L74

## [06] Lack of CEI

Consider making state changes before external calls to follow the CEI pattern.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127-L131

## [07] Detach logic could be refactored into a single function

Consider refactoring the detach logic into a single function to improve code reusability.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L213-L216

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L239-L242

## [08] Declare state variables before they are used

Consider declaring state variables before they are used, and before function definitions.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L43

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L120

## [09] Redundant require statement

The require statement on `ERC20Boost.decrementAllGaugesAllBoost()` can be removed since the current gauge will be present on `_userGauges`.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L239

## [10] Redundant function

`ERC20MultiVotes.userUnusedVotes()` can be removed, since calling `ERC20MultiVotes.getVotes()` has the same effect.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L53-L55

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L47-L50

## [11] Misleading comment

The following comment should be refactored to: "add to deprecated and fail loud if already present"

```
function _removeGauge(address gauge) internal {
    // add to deprecated and fail loud if not present
    if (!_deprecatedGauges.add(gauge)) revert InvalidGauge();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L278

## [12] Better naming for modifiers

Consider prefixing the modifiers name with only, e.g. onlyNotAttached

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L326

## [13] State variable could be public

Instead of declaring as private and creating a getter function, the variable could be declared as public.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L117

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L126-L128

## [14] Require vs custom error

Consider using only one approach for error handling.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L225

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L351

## [15] Usage of returned named variables and manual return on the same contract

Some functions return named variables, others return explicit values.

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L225

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L261

## [16] Modifier could be reused

Instead of copying the same modifier on multiple contracts, consider declaring on a single library contract to be reused where necessary.

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol#L39-L42

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32-L35

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39-L42

## [17] `PartnerUtilityManager.forfeitPartnerGovernance()` should emit an event

Consider adding an event for `PartnerUtilityManager.forfeitPartnerGovernance()`, since forfeit the functions from `UtilityManager` are also emitting events.

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L101-L105

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L69-L93

## [18] Add descriptive error messages

Consider adding descriptive errors messages to facilitate debugging.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L116

## [19] Consistent naming for function arguments

Consider using only one approach, e.g. using leading underscore or not using it.

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L53

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L62

## [20] Use SafeCast directly

SafeCast could be used directly instead of checking the upper bound manually.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L187

## [21] Use the conditional directly

Consider refactoring `FlywheelGaugeRewards.getAccruedRewards()` with the following:

```
if (!incompleteCycle) {
    accruedRewards += cycleRewardsNext;
    cycleRewardsNext = 0;
}
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L221-L226

## [22] `UlyssesToken.removeAsset()` can be improved

One improvement that can be done on `UlyssesToken.removeAsset()` is to check if the asset being removed is the last item, and in this case just pop directly. This would also save gas to preventing unnecessary storage updates.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L60-L85

## [23] Misleading custom error name

Consider refactor `IncentiveStartTimeMustBeNowOrInTheFuture` to `IncentiveStartTimeMustBeInTheFuture`, since the check prevents from being now.

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L164

## [24] Function `IncentiveTime.getEnd()` can be reused

Consider reusing `IncentiveTime.getEnd()` in `IncentiveTime.getEndAndDuration()`.

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L41

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L31-L33

## [25] Downcasting block.number

Consider refactoring votes to use less than 222 bits so that block.number can use more that 32 bits, and the struct still fits into 256 bits.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L19-L22

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L253

## [26] Elliptic curve recovery function

Consider using OZ `ECDSA` instead of the built-in ecrecover, since `ECDSA` will also check for signer 0, and provide extra replayability protections.

## [27] Lack of amount zero check in `PartnerUtilityManager.claimPartnerGovernance()`

Consider adding an early return for amount zero in `PartnerUtilityManager.claimPartnerGovernance()`, since this functionality is present on `UtilityManager` claim functions.

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L158-L161

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L111

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L120

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L129

## [28] Check if hermes reward is valid when creating an incentive

Consider adding a check to verify that the provided hermes address has code - e.g. checking `account.code.length` - either on `UniswapV3Gauge constructor` or `UniswapV3Gauge.createIncentive()`.

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L177

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L129

## [29] Missing event on parameter changes

Adding events on parameter changes will facilitate offchain monitoring.

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L85-L101

## [30] Lack of old and new value in event

Events that contain parameter changes should contain both the old and the new value.

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L63-L66

## [31] Lack of check for stale values

Add a check ensuring that the new value if different than the current value to avoid emitting unnecessary events.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L137-L141

## [32] Lack of comments in struct field

Consider commenting all the struct fields to improve the code documentation.

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L35-L40

## [33] Use time units 

Consider replacing 86400 with `1 days`.

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L24

## [34] Multiples of ten should use scientific notation.

Using scientific notation for multiples of ten will improve code readability.

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L56

## [35] Imports should be grouped

Consider grouping interface imports together.

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L16-L19
