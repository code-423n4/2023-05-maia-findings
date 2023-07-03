# Maia DAO - QA Report

## Summary

| Risk | Title | File | Instances |
| :--: | --- | :---: | :---: |
| L-01 | The `restakeToken` is missing in the `IUniswapV3Staker` interface | [IUniswapV3Staker.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol) | 1 | 
| L-02 | The functionality of `decrementGaugeBoost` and `decrementGaugesBoostIndexed` differs | [ERC20Boost.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol) | 1 | 
| L-03 | Incorrect NatSpec comment | --- | 4 | 
| N-01 | `userDelegatedVotes` does not belong to admin operations | [IERC20MultiVotes.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20MultiVotes.sol#L91) | 1 | 
| N-02 | The return value of `EnumerableSet.remove` is unchecked | [ERC20Boost.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L178) | 1 | 
| N-03 | Missing NatSpec on `queueRewardsForCyclePaginated` | [IFlywheelGaugeRewards.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/interfaces/IFlywheelGaugeRewards.sol) | 1 | 
| N-04 | Misleading function name | [BaseV2Minter.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol) | 1 | 
| N-05 | Incorrect comment | [ERC20Boost.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol) | 1 | 
| N-06 | Typo in the comment | [FlywheelGaugeRewards.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol) | 1 | 
| Total | 9 issues | --- | 12 |

## [L-01] The `restakeToken` is missing in the `IUniswapV3Staker` interface
The external function [`UniswapV3Staker#restakeToken`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L340-L348) is missing from the [`IUniswapV3Staker.sol`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol). 

Since the re-staking functionality is supposed to be permissionless, off-chain automation tools will call `restakeToken` to keep the user's token always staked. It is important that they have frictionless access to it via the interface.

Fix:
```diff
diff --git a/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol b/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
index 895c505..f957dd8 100644
--- a/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
+++ b/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
@@ -239,6 +239,14 @@ interface IUniswapV3Staker is IERC721Receiver {
     /// @param tokenId The ID of the token to stake
     function stakeToken(uint256 tokenId) external;
 
+    /*//////////////////////////////////////////////////////////////
+                            RESTAKE TOKEN LOGIC
+    //////////////////////////////////////////////////////////////*/
+    
+    /// @notice Restakes a Uniswap V3 LP token
+    /// @param tokenId The ID of the token to restake
+    function restakeToken(uint256 tokenId) external;
+
     /*//////////////////////////////////////////////////////////////
                         GAUGE UPDATE LOGIC
     //////////////////////////////////////////////////////////////*/

```

## [L-02] The functionality of `decrementGaugeBoost` and `decrementGaugesBoostIndexed` differs
According to the spec in the [`IERC20Boost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L178) interface, the [`decrementGaugeBoost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L175-L187) and [`decrementGaugesBoostIndexed`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L203-L227) should function the same, with the latter being an indexed version of the former. 

It is not the case. The difference lies in the way they handle deprecated gauges. The `decrementGaugesBoostIndexed` function, when supplied with a deprecated gauge, will remove the user gauge boost entirely. The `decrementGaugeBoost` will either decrease the deprecated gauge's user boost or delete it, depending on whether the [`uint256 boost` is `>= gaugeState.userGaugeBoost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L177C9-L177C48). 

This is happening because `decrementGaugesBoostIndexed` explicitly checks if [`_deprecatedGauges.contains(gauge)`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L212) while `decrementGaugeBoost` [does not](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L177). 

It is up to the protocol team to decide whether the user gauge boost should be removed from deprecated gauges or just be decremented. 

Things to consider:
- Users expect consistent behaviour from similar functions.
- Since gauges can be reactivated, someone might not want to remove the boost from it.

If boost were to be removed:
```diff
diff --git a/src/erc-20/ERC20Boost.sol b/src/erc-20/ERC20Boost.sol
index a1da4df..9f13074 100644
--- a/src/erc-20/ERC20Boost.sol
+++ b/src/erc-20/ERC20Boost.sol
@@ -174,7 +174,7 @@ abstract contract ERC20Boost is ERC20, Ownable, IERC20Boost {
     /// @inheritdoc IERC20Boost
     function decrementGaugeBoost(address gauge, uint256 boost) public {
         GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
-        if (boost >= gaugeState.userGaugeBoost) {
+        if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
             _userGauges[msg.sender].remove(gauge);
             delete getUserGaugeBoost[msg.sender][gauge];
```

And if it were to be decremented:
```diff
diff --git a/src/erc-20/ERC20Boost.sol b/src/erc-20/ERC20Boost.sol
index a1da4df..51bad76 100644
--- a/src/erc-20/ERC20Boost.sol
+++ b/src/erc-20/ERC20Boost.sol
@@ -209,7 +209,7 @@ abstract contract ERC20Boost is ERC20, Ownable, IERC20Boost {
 
             GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
 
-            if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
+            if (boost >= gaugeState.userGaugeBoost) {
                 require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
                 delete getUserGaugeBoost[msg.sender][gauge];
```
## [L-03] Incorrect NatSpec comment
### `IUniswapV3Staker#claimAllRewards`
The `@notice` comment on the [`IUniswapV3Staker#claimAllRewards`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L207) function is incorrect. It states that the `claimAllRewards` function transfers `amountRequested` to the recipient, while it transfers all of the rewards. 

It also benefits the users to add the information that the `unstakeToken` or `restakeToken` should be called before this function. Otherwise, the reward balance won't be updated. 

Fix:
```diff
diff --git a/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol b/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
index 895c505..6b57bed 100644
--- a/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
+++ b/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
@@ -204,7 +204,7 @@ interface IUniswapV3Staker is IERC721Receiver {
     /// @return reward The amount of reward tokens claimed
     function claimReward(address to, uint256 amountRequested) external returns (uint256 reward);
 
-    /// @notice Transfers `amountRequested` of accrued `rewardToken` rewards from the contract to the recipient `to`
+    /// @notice Transfers all of the accrued `rewardToken` rewards from the contract to the recipient `to`
     /// @param to The address where claimed rewards will be sent to
     /// @return reward The amount of reward tokens claimed
     function claimAllRewards(address to) external returns (uint256 reward);
```

### `IERC20Boost#numDeprecatedGauges`
The `@notice` comment on the [`numDeprecatedGauges`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L99-L102) function is incorrect. This function returns the number of **deprecated** gauges, not **live** gauges. 

Fix:
```diff
diff --git a/src/erc-20/interfaces/IERC20Boost.sol b/src/erc-20/interfaces/IERC20Boost.sol
index 7dfe65c..1ecbdec 100644
--- a/src/erc-20/interfaces/IERC20Boost.sol
+++ b/src/erc-20/interfaces/IERC20Boost.sol
@@ -97,7 +97,7 @@ interface IERC20Boost {
     function deprecatedGauges() external view returns (address[] memory);
 
     /**
-     * @notice returns the number of live gauges
+     * @notice returns the number of deprecated gauges
      */
     function numDeprecatedGauges() external view returns (uint256);
```

### `IERC20Boost#freeGaugeBoost`
The `@notice` comment on the [`freeGaugeBoost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L105) function is incorrect. It was copied over from the [`userGauges`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L110) function. 

The function returns the amount of boost that the user has left to allocate.

Fix: 
```diff
diff --git a/src/erc-20/interfaces/IERC20Boost.sol b/src/erc-20/interfaces/IERC20Boost.sol
index 7dfe65c..3aee801 100644
--- a/src/erc-20/interfaces/IERC20Boost.sol
+++ b/src/erc-20/interfaces/IERC20Boost.sol
@@ -102,7 +102,7 @@ interface IERC20Boost {
     function numDeprecatedGauges() external view returns (uint256);
 
     /**
-     * @notice returns the set of gauges the user has allocated to, they may be live or deprecated.
+     * @notice returns the amount of boost that the user has left to allocate to gauges.
      */
     function freeGaugeBoost(address user) external view returns (uint256);
```
### `IFlywheelGaugeRewards#queueRewardsForCyclePaginated`
The `@notice` comment on the [`queueRewardsForCyclePaginated`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L74-L75) function is incorrect. It was copied over from the [`queueRewardsForCycle`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L68-L72) function. 

It does not iterate over all live gauges but on the number of gauges specified in the `numRewards` parameter.

Fix:
```diff
diff --git a/src/rewards/interfaces/IFlywheelGaugeRewards.sol b/src/rewards/interfaces/IFlywheelGaugeRewards.sol
index e1a9137..2618557 100644
--- a/src/rewards/interfaces/IFlywheelGaugeRewards.sol
+++ b/src/rewards/interfaces/IFlywheelGaugeRewards.sol
@@ -71,7 +71,7 @@ interface IFlywheelGaugeRewards {
      */
     function queueRewardsForCycle() external returns (uint256 totalQueuedForCycle);
 
-    /// @notice Iterates over all live gauges and queues up the rewards for the cycle
+    /// @notice Iterates over a number of gauges specified by `numRewards` and queues up the rewards for the cycle
     function queueRewardsForCyclePaginated(uint256 numRewards) external;
```

## [N-01] `userDelegatedVotes` does not belong to admin operations
*Code styling*

The `userDelegatedVotes` function is placed next to the admin operations in the `IERC20MultiVotes` interface. It is a view function that should be placed a couple of lines below, next to other "Delegation Logic" functions. 

[IERC20MultiVotes#userDelegatedVotes](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20MultiVotes.sol#L91)

Fix: 
```diff
diff --git a/src/erc-20/interfaces/IERC20MultiVotes.sol b/src/erc-20/interfaces/IERC20MultiVotes.sol
index c628293..476c9aa 100644
--- a/src/erc-20/interfaces/IERC20MultiVotes.sol
+++ b/src/erc-20/interfaces/IERC20MultiVotes.sol
@@ -85,15 +85,15 @@ interface IERC20MultiVotes {
      */
     function setContractExceedMaxDelegates(address account, bool canExceedMax) external;
 
+    /*///////////////////////////////////////////////////////////////
+                        DELEGATION LOGIC
+    //////////////////////////////////////////////////////////////*/
+    
     /**
      * @notice mapping from a delegator to the total number of delegated votes.
      */
     function userDelegatedVotes(address) external view returns (uint256);
 
-    /*///////////////////////////////////////////////////////////////
-                        DELEGATION LOGIC
-    //////////////////////////////////////////////////////////////*/
-
     /**
      * @notice Get the amount of votes currently delegated by `delegator` to `delegatee`.
      * @param delegator the account which is delegating votes to `delegatee`.
```


## [N-02] The return value of `EnumerableSet.remove` is unchecked
The `EnumerableSet.remove` function returns a status boolean on a successful removal. The [`ERC20Boost#decrementGaugeBoost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L178) function does not check for that value. Other similar functions like [`decrementGaugeAllBoost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L191C1-L191C1), [`decrementGaugesBoostIndexed`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L213) and [`decrementAllGaugesAllBoost`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L239C13-L239C13) do check the return value. 

In a situation where incorrect gauge address were passed to the `decrementGaugeBoost` function, the `Detach` event would be emitted nonetheless. 

Fix:
```diff
diff --git a/src/erc-20/ERC20Boost.sol b/src/erc-20/ERC20Boost.sol
index a1da4df..f12fd2c 100644
--- a/src/erc-20/ERC20Boost.sol
+++ b/src/erc-20/ERC20Boost.sol
@@ -175,7 +175,7 @@ abstract contract ERC20Boost is ERC20, Ownable, IERC20Boost {
     function decrementGaugeBoost(address gauge, uint256 boost) public {
         GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
         if (boost >= gaugeState.userGaugeBoost) {
-            _userGauges[msg.sender].remove(gauge);
+            require(_userGauges[msg.sender].remove(gauge));
             delete getUserGaugeBoost[msg.sender][gauge];
 
             emit Detach(msg.sender, gauge);
```
## [N-03] Missing NatSpec on `queueRewardsForCyclePaginated`
The NatSpec `@param numRewards` is missing in the function [`IFlywheelGaugeRewards#queueRewardsForCyclePaginated`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L74-L75) 

Fix:
```diff
diff --git a/src/rewards/interfaces/IFlywheelGaugeRewards.sol b/src/rewards/interfaces/IFlywheelGaugeRewards.sol
index e1a9137..657fbea 100644
--- a/src/rewards/interfaces/IFlywheelGaugeRewards.sol
+++ b/src/rewards/interfaces/IFlywheelGaugeRewards.sol
@@ -71,7 +71,10 @@ interface IFlywheelGaugeRewards {
      */
     function queueRewardsForCycle() external returns (uint256 totalQueuedForCycle);
 
-    /// @notice Iterates over all live gauges and queues up the rewards for the cycle
+    /**
+     * @notice Iterates over all live gauges and queues up the rewards for the cycle
+     * @param numRewards the number of gauges to queue rewards for
+     */
     function queueRewardsForCyclePaginated(uint256 numRewards) external;
```

## [N-04] Misleading function name
The [`BaseV2Minter#getRewards`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L162-L167) function has a misleading function name. Usually, the functions with `get` and `set` prefixes are used for getters and setters (see [Admin setters in the same contract](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L85-L101)).

The `getRewards` function is by no means a simple getter since it handles the transfer of HERMES tokens from the `BaseV2Minter` for weekly rewards. 

Consider changing the naming convention to describe code intentions better. 
## [N-05] Incorrect comment
The comment on the internal function [`ERC20Boost#_removeGauge`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L278) is incorrect. It states that the removal of the gauge should fail if the gauge were not present in the `_deprecatedGauges` set. 

The correct behaviour would be: the removal should fail if the gauge were already present in the `_deprecatedGauges` set.

Fix:
```diff
diff --git a/src/erc-20/ERC20Boost.sol b/src/erc-20/ERC20Boost.sol
index a1da4df..a0f808f 100644
--- a/src/erc-20/ERC20Boost.sol
+++ b/src/erc-20/ERC20Boost.sol
@@ -275,7 +275,7 @@ abstract contract ERC20Boost is ERC20, Ownable, IERC20Boost {
     }
 
     function _removeGauge(address gauge) internal {
-        // add to deprecated and fail loud if not present
+        // add to deprecated and fail loud if already present
         if (!_deprecatedGauges.add(gauge)) revert InvalidGauge();
 
         emit RemoveGauge(gauge);
```
## [N-06] Typo in the comment
There is a typo in the word *rewards* in the [`FlywheelGaugeRewards#queueRewardsForCycle`](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L73) function.

Fix:
```diff
-        /// @dev Update minter cycle and queue rewars if needed.
+        /// @dev Update minter cycle and queue rewards if needed.
```