## Rewards logic

NOTE: tested with `forge test --gas-report --match-path "test\\rewards\\*"`

### 1. **Refactor `BaseFlywheelRewards` to reduce actions**

[src\rewards\base\BaseFlywheelRewards.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol)

`BaseFlywheelRewards` stores `flywheel` as an instance of the `FlywheelCore` contract, which is only needed to call `FlywheelCore.rewardToken()` once in constructor. There is no other use of the `FlywheelCore` interface, including implementations. But this requires every time to cast `flywheel` to `address` type in every call that use `onlyFlywheel` modifier.


Store `flywheel` as `address` type and cast it to `FlywheelCore` only in constructor

#### PoC:

```diff
diff --git a/src/rewards/base/BaseFlywheelRewards.sol b/src/rewards/base/BaseFlywheelRewards.sol
index 74c9849..37a240b 100644
--- a/src/rewards/base/BaseFlywheelRewards.sol
+++ b/src/rewards/base/BaseFlywheelRewards.sol
@@ -26,14 +26,14 @@ abstract contract BaseFlywheelRewards is IFlywheelRewards {
     address public immutable override rewardToken;
 
     /// @inheritdoc IFlywheelRewards
-    FlywheelCore public immutable override flywheel;
+    address public immutable override flywheel;
 
-    constructor(FlywheelCore _flywheel) {
+    constructor(address _flywheel) {
         flywheel = _flywheel;
-        address _rewardToken = _flywheel.rewardToken();
+        address _rewardToken = FlywheelCore(_flywheel).rewardToken();
         rewardToken = _rewardToken;
 
-        _rewardToken.safeApprove(address(_flywheel), type(uint256).max);
+        _rewardToken.safeApprove(_flywheel, type(uint256).max);
     }
 
     /*//////////////////////////////////////////////////////////////
@@ -41,7 +41,7 @@ abstract contract BaseFlywheelRewards is IFlywheelRewards {
     //////////////////////////////////////////////////////////////*/
 
     modifier onlyFlywheel() {
-        if (msg.sender != address(flywheel)) revert FlywheelError();
+        if (msg.sender != flywheel) revert FlywheelError();
         _;
     }
 }
diff --git a/src/rewards/interfaces/IFlywheelRewards.sol b/src/rewards/interfaces/IFlywheelRewards.sol
index 9b1d9c9..fde01dd 100644
--- a/src/rewards/interfaces/IFlywheelRewards.sol
+++ b/src/rewards/interfaces/IFlywheelRewards.sol
@@ -35,7 +35,7 @@ interface IFlywheelRewards {
-    function flywheel() external view returns (FlywheelCore);
+    function flywheel() external view returns (address);
diff --git a/src/rewards/rewards/FlywheelAcummulatedRewards.sol b/src/rewards/rewards/FlywheelAcummulatedRewards.sol
index c349857..ed67345 100644
--- a/src/rewards/rewards/FlywheelAcummulatedRewards.sol
+++ b/src/rewards/rewards/FlywheelAcummulatedRewards.sol
-    constructor(FlywheelCore _flywheel, uint256 _rewardsCycleLength) BaseFlywheelRewards(_flywheel) {
+    constructor(address _flywheel, uint256 _rewardsCycleLength) BaseFlywheelRewards(_flywheel) {
diff --git a/src/rewards/rewards/FlywheelBribeRewards.sol b/src/rewards/rewards/FlywheelBribeRewards.sol
index b33ef1b..8ef7cb1 100644
--- a/src/rewards/rewards/FlywheelBribeRewards.sol
+++ b/src/rewards/rewards/FlywheelBribeRewards.sol
-    constructor(FlywheelCore _flywheel, uint256 _rewardsCycleLength)
+    constructor(address _flywheel, uint256 _rewardsCycleLength)
diff --git a/src/rewards/rewards/FlywheelInstantRewards.sol b/src/rewards/rewards/FlywheelInstantRewards.sol
index 240d432..a7af055 100644
--- a/src/rewards/rewards/FlywheelInstantRewards.sol
+++ b/src/rewards/rewards/FlywheelInstantRewards.sol
-    constructor(FlywheelCore _flywheel) BaseFlywheelRewards(_flywheel) {
+    constructor(address _flywheel) BaseFlywheelRewards(_flywheel) {
```

#### gas change:

```diff
diff --git a/OLD.gas b/NEW.gas
index acbdad7..de6223f 100644
--- a/OLD.gas
+++ b/NEW.gas
@@ -3,8 +3,8 @@
 | Deployment Cost                                                  | Deployment Size |       |        |        |         |
 | 872256                                                           | 4707            |       |        |        |         |
 | Function Name                                                    | min             | avg   | median | max    | # calls |
-| accrue(address,address)(uint256)                                 | 2777            | 42344 | 52578  | 55468  | 6       |
-| accrue(address,address,address)(uint256,uint256)                 | 2920            | 56020 | 58785  | 103590 | 4       |
+| accrue(address,address)(uint256)                                 | 2777            | 45660 | 55282  | 55468  | 6       |
+| accrue(address,address,address)(uint256,uint256)                 | 2920            | 46070 | 47835  | 85690  | 4       |
-| setFlywheelRewards                                               | 2574            | 23731 | 24802  | 29908  | 16      |
+| setFlywheelRewards                                               | 2574            | 22469 | 24802  | 24802  | 16      |

 
@@ -22,8 +22,8 @@
 | Deployment Cost                                            | Deployment Size |       |        |       |         |
 | 872656                                                     | 4709            |       |        |       |         |
 | Function Name                                              | min             | avg   | median | max   | # calls |
-| accrue(address,address)(uint256)                           | 2777            | 45870 | 55533  | 55719 | 6       |
-| accrue(address,address,address)(uint256,uint256)           | 2920            | 51233 | 58036  | 85941 | 4       |
+| accrue(address,address)(uint256)                           | 2777            | 39236 | 42972  | 55533 | 6       |
+| accrue(address,address,address)(uint256,uint256)           | 2920            | 56208 | 59036  | 103841| 4       |
```

### 2.  **Avoid function that only call another function**

- [src\rewards\base\FlywheelCore.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol)

`accrue(ERC20 strategy, address user) external returns (uint256)` only call `_accrue(ERC20 strategy, address user) internal returns (uint256)` without checking or modifying the arguments, in that sense the function is useless.

#### PoC:

```diff
diff --git a/src/rewards/base/FlywheelCore.sol b/src/rewards/base/FlywheelCore.sol
index 308b804..4036dda 100644
--- a/src/rewards/base/FlywheelCore.sol
+++ b/src/rewards/base/FlywheelCore.sol
@@ -63,15 +63,11 @@ abstract contract FlywheelCore is Ownable, IFlywheelCore {
 
     /// @inheritdoc IFlywheelCore
     function accrue(address user) external returns (uint256) {
-        return _accrue(ERC20(msg.sender), user);
+        return accrue(ERC20(msg.sender), user);
     }
 
     /// @inheritdoc IFlywheelCore
-    function accrue(ERC20 strategy, address user) external returns (uint256) {
-        return _accrue(strategy, user);
-    }
-
-    function _accrue(ERC20 strategy, address user) internal returns (uint256) {
+    function accrue(ERC20 strategy, address user) public returns (uint256) {
         uint256 index = strategyIndex[strategy];
 
         if (index == 0) return 0;

```

#### gas change:

```diff
diff --git a/OLD.gas b/NEW.gas
index acbdad7..7cba39b 100644
--- a/OLD.gas
+++ b/NEW.gas
@@ -1,9 +1,9 @@
 | src/rewards/FlywheelCoreInstant.sol:FlywheelCoreInstant contract |                                  |      |        |       |         |
 | ---------------------------------------------------------------- | -------------------------------- | ---- | ------ | ----- | ------- |
 | Deployment Cost                                                  | Deployment Size                  |      |        |       |         |
 | -                                                                | 872256                           | 4707 |        |       |         |   |
 | +                                                                | 869856                           | 4695 |        |       |         |   |
 | Function Name                                                    | min                              | avg  | median | max   | # calls |
 | -                                                                | accrue(address,address)(uint256) | 2777 | 42344  | 52578 | 55468   | 6 |
 | +                                                                | accrue(address,address)(uint256) | 2732 | 42299  | 52533 | 55423   | 6 |
@@ -20,10 +20,10 @@
 | src/rewards/FlywheelCoreStrategy.sol:FlywheelCore contract |                                                  |      |        |       |         |
 | ---------------------------------------------------------- | ------------------------------------------------ | ---- | ------ | ----- | ------- |
 | Deployment Cost                                            | Deployment Size                                  |      |        |       |         |
 | -                                                          | 872656                                           | 4709 |        |       |         |   |
 | +                                                          | 870256                                           | 4697 |        |       |         |   |
 | Function Name                                              | min                                              | avg  | median | max   | # calls |
 | -                                                          | accrue(address,address)(uint256)                 | 2777 | 45870  | 55533 | 55719   | 6 |
 | -                                                          | accrue(address,address,address)(uint256,uint256) | 2920 | 51233  | 58036 | 85941   | 4 |
 | +                                                          | accrue(address,address)(uint256)                 | 2732 | 42508  | 52784 | 55488   | 6 |
 | +                                                          | accrue(address,address,address)(uint256,uint256) | 2920 | 46258  | 48086 | 85941   | 4 |
```

### 3. **Avoid Casting type to itself**

- [src\rewards\base\FlywheelCore.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol)

`flywheelRewards`  is cast to `address` type several times in the file even though it is already stored as  `address` type

#### PoC:

```diff
diff --git a/src/rewards/base/FlywheelCore.sol b/src/rewards/base/FlywheelCore.sol
index 308b804..d0ddc61 100644
--- a/src/rewards/base/FlywheelCore.sol
+++ b/src/rewards/base/FlywheelCore.sol
@@ -97,7 +97,7 @@ abstract contract FlywheelCore is Ownable, IFlywheelCore {
         if (accrued != 0) {
             rewardsAccrued[user] = 0;
 
-            rewardToken.safeTransferFrom(address(flywheelRewards), user, accrued);
+            rewardToken.safeTransferFrom(flywheelRewards, user, accrued);
 
             emit ClaimRewards(user, accrued);
         }
@@ -123,9 +123,9 @@ abstract contract FlywheelCore is Ownable, IFlywheelCore {
 
     /// @inheritdoc IFlywheelCore
     function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {
-        uint256 oldRewardBalance = rewardToken.balanceOf(address(flywheelRewards));
+        uint256 oldRewardBalance = rewardToken.balanceOf(flywheelRewards);
         if (oldRewardBalance > 0) {
-            rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance);
+            rewardToken.safeTransferFrom(flywheelRewards, address(newFlywheelRewards), oldRewardBalance);
         }
 
         flywheelRewards = newFlywheelRewards;

```

#### gas diff:


```diff
diff --git a/OLD.gas b/NEW.gas
index acbdad7..b2231e7 100644
--- a/OLD.gas
+++ b/NEW.gas
@@ -3,7 +3,7 @@
 | Deployment Cost                                                  | Deployment Size |       |        |        |         |
 | 872256                                                           | 4707            |       |        |        |         |
 | Function Name                                                    | min             | avg   | median | max    | # calls |
-| accrue(address,address)(uint256)                                 | 2777            | 42344 | 52578  | 55468  | 6       |
+| accrue(address,address)(uint256)                                 | 2777            | 39027 | 42721  | 55282  | 6       |
 | accrue(address,address,address)(uint256,uint256)                 | 2920            | 56020 | 58785  | 103590 | 4       |
 | addStrategyForRewards                                            | 2486            | 63505 | 72508  | 72508  | 8       |
 | claimRewards                                                     | 532             | 12210 | 12210  | 23888  | 2       |
@@ -22,10 +22,10 @@
 | Deployment Cost                                            | Deployment Size |       |        |       |         |
 | 872656                                                     | 4709            |       |        |       |         |
 | Function Name                                              | min             | avg   | median | max   | # calls |
-| accrue(address,address)(uint256)                           | 2777            | 45870 | 55533  | 55719 | 6       |
-| accrue(address,address,address)(uint256,uint256)           | 2920            | 51233 | 58036  | 85941 | 4       |
+| accrue(address,address)(uint256)                           | 2777            | 39236 | 42879  | 55719 | 6       |
+| accrue(address,address,address)(uint256,uint256)           | 2920            | 46258 | 48086  | 85941 | 4       |
```

## Hermes + Maia modules

NOTE: Tested with `forge test --gas-report`

[src\hermes\bHermes.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol)

[src\hermes\UtilityManager.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol)

[src\maia\vMaia.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol)

[src\maia\tokens\ERC4626PartnerManager.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol)

[src\maia\PartnerUtilityManager.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol)

`bHermes` constructor cast arguments to `address` type then pass it to `UtilityManager` which cast it to initial types.

PoC:

```diff
```diff
\ No newline at end of file
diff --git a/diff.md b/diff.md
new file mode 100644
index 0000000..e69de29
diff --git a/src/hermes/UtilityManager.sol b/src/hermes/UtilityManager.sol
index ce5fa62..5bec1cc 100644
--- a/src/hermes/UtilityManager.sol
+++ b/src/hermes/UtilityManager.sol
@@ -41,10 +41,10 @@ abstract contract UtilityManager is IUtilityManager {
      * @param _gaugeBoost The address of the bHermesBoost contract.
      * @param _governance The address of the bHermesVotes contract.
      */
-    constructor(address _gaugeWeight, address _gaugeBoost, address _governance) {
-        gaugeWeight = bHermesGauges(_gaugeWeight);
-        gaugeBoost = bHermesBoost(_gaugeBoost);
-        governance = ERC20Votes(_governance);
+    constructor(bHermesGauges _gaugeWeight, bHermesBoost _gaugeBoost, ERC20Votes _governance) {
+        gaugeWeight = _gaugeWeight;
+        gaugeBoost = _gaugeBoost;
+        governance = _governance;
     }
 
     /*///////////////////////////////////////////////////////////////
diff --git a/src/hermes/bHermes.sol b/src/hermes/bHermes.sol
index e84ba9e..e28011b 100644
--- a/src/hermes/bHermes.sol
+++ b/src/hermes/bHermes.sol
@@ -54,9 +54,9 @@ contract bHermes is UtilityManager, ERC4626DepositOnly {
 
     constructor(ERC20 _hermes, address _owner, uint32 _gaugeCycleLength, uint32 _incrementFreezeWindow)
         UtilityManager(
-            address(new bHermesGauges(_owner, _gaugeCycleLength, _incrementFreezeWindow)),
-            address(new bHermesBoost(_owner)),
-            address(new bHermesVotes(_owner))
+            new bHermesGauges(_owner, _gaugeCycleLength, _incrementFreezeWindow),
+            new bHermesBoost(_owner),
+            new bHermesVotes(_owner)
         )
         ERC4626DepositOnly(_hermes, "Burned Hermes: Gov + Yield + Boost", "bHermes")
     {}
diff --git a/src/maia/PartnerUtilityManager.sol b/src/maia/PartnerUtilityManager.sol
index 616017d..7a77fd6 100644
--- a/src/maia/PartnerUtilityManager.sol
+++ b/src/maia/PartnerUtilityManager.sol
@@ -4,6 +4,9 @@ pragma solidity ^0.8.0;
 import {SafeTransferLib} from "solady/utils/SafeTransferLib.sol";
 
 import {ERC20Votes, UtilityManager, IUtilityManager} from "@hermes/UtilityManager.sol";
+import {bHermesBoost} from "@hermes/tokens/bHermesBoost.sol";
+import {bHermesGauges} from "@hermes/tokens/bHermesGauges.sol";
+import {bHermesVotes as ERC20Votes} from "@hermes/tokens/bHermesVotes.sol";
 
 import {IBaseVault} from "./interfaces/IBaseVault.sol";
 import {IPartnerUtilityManager} from "./interfaces/IPartnerUtilityManager.sol";
@@ -34,13 +37,13 @@ abstract contract PartnerUtilityManager is UtilityManager, IPartnerUtilityManage
      * @param _partnerVault The address of the partner vault.
      */
     constructor(
-        address _gaugeWeight,
-        address _gaugeBoost,
-        address _governance,
-        address _partnerGovernance,
+        bHermesGauges _gaugeWeight,
+        bHermesBoost _gaugeBoost,
+        ERC20Votes _governance,
+        ERC20Votes _partnerGovernance,
         address _partnerVault
     ) UtilityManager(_gaugeWeight, _gaugeBoost, _governance) {
-        partnerGovernance = ERC20Votes(_partnerGovernance);
+        partnerGovernance = _partnerGovernance;
         partnerVault = _partnerVault;
     }
 
diff --git a/src/maia/tokens/ERC4626PartnerManager.sol b/src/maia/tokens/ERC4626PartnerManager.sol
index b912bab..562f08a 100644
--- a/src/maia/tokens/ERC4626PartnerManager.sol
+++ b/src/maia/tokens/ERC4626PartnerManager.sol
@@ -58,10 +58,10 @@ abstract contract ERC4626PartnerManager is PartnerUtilityManager, Ownable, ERC46
         address _owner
     )
         PartnerUtilityManager(
-            address(bHermes(_bhermes).gaugeWeight()),
-            address(bHermes(_bhermes).gaugeBoost()),
-            address(bHermes(_bhermes).governance()),
-            address(new ERC20MultiVotes(_owner)),
+            bHermes(_bhermes).gaugeWeight(),
+            bHermes(_bhermes).gaugeBoost(),
+            bHermes(_bhermes).governance(),
+            new ERC20MultiVotes(_owner),
             partnerVault
         )
         ERC4626(
```