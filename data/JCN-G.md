# Summary
Some optimizations were benchmarked via the protocol's tests, i.e. using the following config defined in the protocol's test suite: `solc version 0.8.18`, `optimizer on`, and `200 runs`. Optimizations that were not benchmarked are explained via EVM gas costs and opcodes. 

*Notes*: 

- Only optimizations to state-mutating functions and `view`/`pure` functions invoked by state-mutating functions are highlighted below.
- Only runtime gas is highlighted below, as it will inevitably outweight deployment gas costs throughout the lifetime of the protocol.
- Some code snippets may be truncated to save space. Code snippets may also be accompanied by `@audit` tags in comments to aid in explaining the issue.

## Gas Optimizations
| Number |Issue|Instances|Estimated Gas| 
|-|:-|:-:|:-:| 
| [G-01](#state-variables-can-be-cached-instead-of-re-reading-them-from-storage) | State variables can be cached instead of re-reading them from storage | 44 | 4400 |
| [G-02](#state-variables-can-be-packed-into-fewer-storage-slots) | State variables can be packed into fewer storage slots | 13 | 32000 |
| [G-03](#structs-can-be-packed-into-fewer-storage-slots) | Structs can be packed into fewer storage slots | 2 | 8000 |
| [G-04](#get-rid-of-redundant-storage-variable) | Get rid of redundant storage variable | 8 | 93285 |
| [G-05](#cache-state-variables-outside-of-loop-to-avoid-readingwriting-storage-on-every-iteration) | Cache state variables outside of loop to avoid reading/writing storage on every iteration | 6 | 990 |
| [G-06](#avoid-emitting-storage-variables) | Avoid emitting storage variables | 6 | 600 |
| [G-07](#refactor-code-to-save-1-sload) | Refactor code to save 1 SLOAD | 2 | 521 |
| [G-08](#refactor-functions-to-avoid-unnecessary-sloads-and-sstores) | Refactor functions to avoid unnecessary SLOADs and SSTOREs | 20 | 3649 |
| [G-09](#refactor-code-to-avoid-redundant-external-calls) | Refactor code to avoid redundant external calls | 4 | 2623 |
| [G-10](#use-calldata-instead-of-memory-for-function-parameters) | Use calldata instead of memory for function parameters | 7 | 8463 | 
| [G-11](#use-struct-dot-notation-to-avoid-memory-allocation) | Use struct `dot` notation to avoid memory allocation | 8 | 1416 | 
| [G-12](#re-use-memory-arrays-to-avoid-extra-memory-allocation) | Re-use memory arrays to avoid extra memory allocation | 3 | 494 | 
| [G-13](#usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) | Usage of `uint`/`int` smaller than 32 bytes (256 bits) incurs overhead | - | 147 | 
| [G-14](#multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache) | Multiple accesses of a mapping/array should use a local variable cache | 2 | 477 |
| [G-15](#cache-calculation-to-avoid-performing-computation-multiple-times) | Cache calculation to avoid performing computation multiple times | 2 | 697 |
| [G-16](#using-safemath-like-functions-with-compiler-version-080-wastes-gas) | Using `SafeMath-like` functions with compiler version ^0.8.0 wastes gas | - | - |
| [G-17](#a-mapping-is-more-efficient-than-an-array) | A mapping is more efficient than an array | - | 281 |
| [G-18](#remove-event-to-avoid-emitting-redundant-data) | Remove event to avoid emitting redundant data | - | 375 |
| [G-19](#cache-calldata-pointers-for-complex-types-to-avoid-offset-calculations) | Cache calldata pointers for complex types to avoid offset calculations | - | 142 |

*Total Estimated Gas Saved: `158560`*

## State variables can be cached instead of re-reading them from storage
Caching of a state variable replaces each `Gwarmaccess (100 gas)` with a much cheaper stack read.

Total Instances: `44`

Estimated Gas Saved: `44 * 100 = 4400`

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L311-L320

### Cache `tokenId` after `doRebalance()` to save 1 SLOAD
```solidity
File: src/talos/base/TalosBaseStrategy.sol
311:    function rebalance() external virtual override nonReentrant checkDeviation onlyStrategyManager {
...
317:        (uint256 amount0, uint256 amount1) = doRebalance();
318:        emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1); // @audit: 1st sload
319:
320:        afterRerange(tokenId); // tokenId changed in doRerange // @audit: 2nd sload
```
```diff
diff --git a/src/talos/base/TalosBaseStrategy.sol b/src/talos/base/TalosBaseStrategy.sol
index 8cb5a4d..d1eeb6f 100644
--- a/src/talos/base/TalosBaseStrategy.sol
+++ b/src/talos/base/TalosBaseStrategy.sol
@@ -315,9 +315,11 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa
         _withdrawAll(_tokenId);

         (uint256 amount0, uint256 amount1) = doRebalance();
-        emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

-        afterRerange(tokenId); // tokenId changed in doRerange
+        _tokenId = tokenId;
+        emit Rerange(_tokenId, tickLower, tickUpper, amount0, amount1);
+
+        afterRerange(_tokenId); // tokenId changed in doRerange
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L298-L307

### Cache `tokenId` after `doRerange()` to save 1 SLOAD
```solidity
File: src/talos/base/TalosBaseStrategy.sol
298:    function rerange() external virtual override nonReentrant checkDeviation onlyStrategyManager {
...
304:        (uint256 amount0, uint256 amount1) = doRerange();
305:        emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1); // @audit: 1st sload
306:
307:        afterRerange(tokenId); // tokenId changed in doRerange // @audit: 2nd sload
```
```diff
diff --git a/src/talos/base/TalosBaseStrategy.sol b/src/talos/base/TalosBaseStrategy.sol
index 8cb5a4d..93d55bb 100644
--- a/src/talos/base/TalosBaseStrategy.sol
+++ b/src/talos/base/TalosBaseStrategy.sol
@@ -302,9 +302,11 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa
         _withdrawAll(_tokenId);

         (uint256 amount0, uint256 amount1) = doRerange();
-        emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

-        afterRerange(tokenId); // tokenId changed in doRerange
+        _tokenId = tokenId; // @audit: tokenId was updated in above call, according to comment below
+        emit Rerange(_tokenId, tickLower, tickUpper, amount0, amount1);
+
+        afterRerange(_tokenId); // tokenId changed in doRerange
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L261-L277

### Cache `liquidity` to save 1 SLOAD
```solidity
File: src/talos/base/TalosBaseStrategy.sol
261:            uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply); // @audit: 1st sload
...
277:            liquidity -= liquidityToDecrease; // @audit: 2nd sload
```
```diff
diff --git a/src/talos/base/TalosBaseStrategy.sol b/src/talos/base/TalosBaseStrategy.sol
index 8cb5a4d..8e6bdde 100644
--- a/src/talos/base/TalosBaseStrategy.sol
+++ b/src/talos/base/TalosBaseStrategy.sol
@@ -258,7 +258,8 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa

         INonfungiblePositionManager _nonfungiblePositionManager = nonfungiblePositionManager; // Saves an extra SLOAD
         {
-            uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);
+            uint128 _liquidity = liquidity;
+            uint128 liquidityToDecrease = uint128((_liquidity * shares) / totalSupply);

             (amount0, amount1) = _nonfungiblePositionManager.decreaseLiquidity(
                 INonfungiblePositionManager.DecreaseLiquidityParams({
@@ -274,7 +275,7 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa

             _burn(_owner, shares);

-            liquidity -= liquidityToDecrease;
+            liquidity = _liquidity - liquidityToDecrease;
         }
         emit Redeem(msg.sender, receiver, _owner, amount0, amount1, shares);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L214-L219

### Use already cached variable (`supply`) to save 1 SLOAD
```solidity
File: src/talos/base/TalosBaseStrategy.sol
214:        uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero. // @audit: 1st sload
215:        shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;
216:        liquidity += liquidityDifference;
217:
218:        _mint(receiver, shares);
219:        if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply(); // @audit: 2nd sload, use `supply`
```
```diff
diff --git a/src/talos/base/TalosBaseStrategy.sol b/src/talos/base/TalosBaseStrategy.sol
index 8cb5a4d..a45c472 100644
--- a/src/talos/base/TalosBaseStrategy.sol
+++ b/src/talos/base/TalosBaseStrategy.sol
@@ -216,7 +216,7 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa
         liquidity += liquidityDifference;

         _mint(receiver, shares);
-        if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
+        if (supply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();

         emit Deposit(msg.sender, receiver, amount0, amount1, shares);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L72-L76

### Cache `flywheelTokens[bribeToken]` to save 1 SLOAD
```solidity
File: src/gauges/factories/BribesFactory.sol
72:    function addGaugetoFlywheel(address gauge, address bribeToken) external onlyGaugeFactory {
73:        if (address(flywheelTokens[bribeToken]) == address(0)) createBribeFlywheel(bribeToken); // @audit: 1st sload
74:
75:        flywheelTokens[bribeToken].addStrategyForRewards(ERC20(gauge)); // @audit: 2nd sload
76:    }
```
```diff
diff --git a/src/gauges/factories/BribesFactory.sol b/src/gauges/factories/BribesFactory.sol
index ec52084..b810bed 100644
--- a/src/gauges/factories/BribesFactory.sol
+++ b/src/gauges/factories/BribesFactory.sol
@@ -70,9 +70,10 @@ contract BribesFactory is Ownable, IBribesFactory {

     /// @inheritdoc IBribesFactory
     function addGaugetoFlywheel(address gauge, address bribeToken) external onlyGaugeFactory {
-        if (address(flywheelTokens[bribeToken]) == address(0)) createBribeFlywheel(bribeToken);
+        FlywheelCore _flywheelToken = flywheelTokens[bribeToken];
+        if (address(_flywheelToken) == address(0)) createBribeFlywheel(bribeToken);

-        flywheelTokens[bribeToken].addStrategyForRewards(ERC20(gauge));
+        _flywheelToken.addStrategyForRewards(ERC20(gauge));
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L83-L93

### Cache `localPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
83:        if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) { // @audit: 1st sload
84:            revert UnrecognizedBridgeAgentFactory();
85:        }
86:
87:        //Create Token
88:        address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(
89:            _newBranchRouter, _rootBridgeAgent, _rootBridgeAgentFactory
90:        );
91:
92:        //Check BridgeAgent Address
93:        if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) { // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol b/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
index a20fc93..3c82e9e 100644
--- a/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
+++ b/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
@@ -80,7 +80,8 @@ contract ArbitrumCoreBranchRouter is CoreBranchRouter {
         uint128
     ) internal override {
         //Check if msg.sender is a valid BridgeAgentFactory
-        if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {
+        address _localPortAddress = localPortAddress;
+        if (!IPort(_localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {
             revert UnrecognizedBridgeAgentFactory();
         }

@@ -90,7 +91,7 @@ contract ArbitrumCoreBranchRouter is CoreBranchRouter {
         );

         //Check BridgeAgent Address
-        if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {
+        if (!IPort(_localPortAddress).isBridgeAgent(newBridgeAgent)) {
             revert UnrecognizedBridgeAgent();
         }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L98-L99

### Cache `assets[i]` to save 1 SLOAD
```solidity
File: src/ulysses-amm/UlyssesToken.sol
98:            emit AssetRemoved(assets[i]); // @audit: 1st sload
99:            emit AssetAdded(assets[i], _weights[i]); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-amm/UlyssesToken.sol b/src/ulysses-amm/UlyssesToken.sol 
index 552a125..8f581cd 100644
--- a/src/ulysses-amm/UlyssesToken.sol 
+++ b/src/ulysses-amm/UlyssesToken.sol 
@@ -94,9 +94,10 @@ contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {

         for (uint256 i = 0; i < assets.length; i++) {
             newTotalWeights += _weights[i];
-
-            emit AssetRemoved(assets[i]);
-            emit AssetAdded(assets[i], _weights[i]);
+
+            address _asset = assets[i];
+            emit AssetRemoved(_assets);
+            emit AssetAdded(_assets, _weights[i]);
         }

         totalWeights = newTotalWeights;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112-L118

### Cache `assets[i]` to save 1 SLOAD
```solidity
File: src/ulysses-amm/UlyssesToken.sol
112:            uint256 assetBalance = assets[i].balanceOf(address(this)); // @audit: 1st sload
113:            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
114:
115:            if (assetBalance > newAssetBalance) {
116:                assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance); // @audit: 2nd sload
117:            } else {
118:                assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-amm/UlyssesToken.sol b/src/ulysses-amm/UlyssesToken.sol
index 552a125..5660a22 100644
--- a/src/ulysses-amm/UlyssesToken.sol
+++ b/src/ulysses-amm/UlyssesToken.sol
@@ -109,14 +109,16 @@ contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {
      */
     function updateAssetBalances() internal {
         for (uint256 i = 0; i < assets.length; i++) {
-            uint256 assetBalance = assets[i].balanceOf(address(this));
+            address asset = assets[i];
+            uint256 assetBalance = assets.balanceOf(address(this));
             uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);

             if (assetBalance > newAssetBalance) {
-                assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);
+                assets.safeTransfer(msg.sender, assetBalance - newAssetBalance);
             } else {
-                assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);
+                assets.safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);
             }
         }
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L37-L40

### Use calldata value instead of re-reading `localBridgeAgentAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol
37:    function initialize(address _localBridgeAgentAddress) external onlyOwner {
38:        require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");
39:        localBridgeAgentAddress = _localBridgeAgentAddress;
40:        bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress(); // @audit: unnecessary sload, use calldata
```
```diff
diff --git a/src/ulysses-omnichain/BaseBranchRouter.sol b/src/ulysses-omnichain/BaseBranchRouter.sol
index 4ef1023..0f3214d 100644
--- a/src/ulysses-omnichain/BaseBranchRouter.sol
+++ b/src/ulysses-omnichain/BaseBranchRouter.sol
@@ -37,7 +37,7 @@ contract BaseBranchRouter is IBranchRouter, Ownable {
     function initialize(address _localBridgeAgentAddress) external onlyOwner {
         require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");
         localBridgeAgentAddress = _localBridgeAgentAddress;
-        bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress();
+        bridgeAgentExecutorAddress = IBridgeAgent(_localBridgeAgentAddress).bridgeAgentExecutorAddress();
         renounceOwnership();
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L62-L70

### Cache `rootPortAddress` and `localChainId` to save 3 SLOADs
```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol
62:        if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) { // @audit: 1st two sloads
63:            revert UnknownToken();
64:        }
65:
66:        address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId); // @audit: 2nd two sloads
67:
68:        if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();
69:
70:        IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit); // @audit: 3rd sload
```
```diff
diff --git a/src/ulysses-omnichain/ArbitrumBranchPort.sol b/src/ulysses-omnichain/ArbitrumBranchPort.sol
index ad1a3af..b6526a7 100644
--- a/src/ulysses-omnichain/ArbitrumBranchPort.sol
+++ b/src/ulysses-omnichain/ArbitrumBranchPort.sol
@@ -59,15 +59,17 @@ contract ArbitrumBranchPort is BranchPort, IArbitrumBranchPort {
         external
         requiresBridgeAgent
     {
-        if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {
+        address _rootPortAddress = rootPortAddress;
+        uint24 _localChainId = localChainId;
+        if (!IRootPort(_rootPortAddress).isGlobalToken(_globalAddress, _localChainId)) {
             revert UnknownToken();
         }

-        address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);
+        address underlyingAddress = IRootPort(_rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, _localChainId);

         if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();

-        IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);
+        IRootPort(_rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);

         underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L49-L54

### Cache `rootPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol
49:        address globalToken = IRootPort(rootPortAddress).getLocalTokenFromUnder(_underlyingAddress, localChainId); // @audit: 1st sload
50:        if (globalToken == address(0)) revert UnknownUnderlyingToken();
51:
52:        _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);
53:
54:        IRootPort(rootPortAddress).mintToLocalBranch(_recipient, globalToken, _deposit); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/ArbitrumBranchPort.sol b/src/ulysses-omnichain/ArbitrumBranchPort.sol
index ad1a3af..ba2d37f 100644
--- a/src/ulysses-omnichain/ArbitrumBranchPort.sol
+++ b/src/ulysses-omnichain/ArbitrumBranchPort.sol
@@ -46,12 +46,13 @@ contract ArbitrumBranchPort is BranchPort, IArbitrumBranchPort {
         external
         requiresBridgeAgent
     {
-        address globalToken = IRootPort(rootPortAddress).getLocalTokenFromUnder(_underlyingAddress, localChainId);
+        address _rootPortAddress = rootPortAddress;
+        address globalToken = IRootPort(_rootPortAddress).getLocalTokenFromUnder(_underlyingAddress, localChainId);
         if (globalToken == address(0)) revert UnknownUnderlyingToken();

         _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);

-        IRootPort(rootPortAddress).mintToLocalBranch(_recipient, globalToken, _deposit);
+        IRootPort(_rootPortAddress).mintToLocalBranch(_recipient, globalToken, _deposit);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L212-L220

### Cache `localPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol
212:        if (!IPort(localPortAddress).isPortStrategy(_portStrategy, _underlyingToken)) { // @audit: 1st sload
213:            //Add new Port Strategy if new.
214:            IPort(localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit); // @audit 2nd sload
215:        } else if (_isUpdateDailyLimit) {
216:            //Or Update daily limit.
217:            IPort(localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit); // @audit: 2nd sload
218:        } else {
219:            //Or Toggle Port Strategy.
220:            IPort(localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/CoreBranchRouter.sol b/src/ulysses-omnichain/CoreBranchRouter.sol
index 8d86a23..1fbadde 100644
--- a/src/ulysses-omnichain/CoreBranchRouter.sol
+++ b/src/ulysses-omnichain/CoreBranchRouter.sol
@@ -209,15 +209,16 @@ contract CoreBranchRouter is BaseBranchRouter {
         uint256 _dailyManagementLimit,
         bool _isUpdateDailyLimit
     ) internal {
-        if (!IPort(localPortAddress).isPortStrategy(_portStrategy, _underlyingToken)) {
+        address _localPortAddress = localPortAddress;
+        if (!IPort(_localPortAddress).isPortStrategy(_portStrategy, _underlyingToken)) {
             //Add new Port Strategy if new.
-            IPort(localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);
+            IPort(_localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);
         } else if (_isUpdateDailyLimit) {
             //Or Update daily limit.
-            IPort(localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);
+            IPort(_localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);
         } else {
             //Or Toggle Port Strategy.
-            IPort(localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken);
+            IPort(_localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken);
         }
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L189-L193

### Cache `localPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol
189:    function _manageStrategyToken(address _underlyingToken, uint256 _minimumReservesRatio) internal {
190:        if (!IPort(localPortAddress).isStrategyToken(_underlyingToken)) { // @audit: 1st sload
191:            IPort(localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio); // @audit: 2nd sload
192:        } else {
193:            IPort(localPortAddress).toggleStrategyToken(_underlyingToken); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/CoreBranchRouter.sol b/src/ulysses-omnichain/CoreBranchRouter.sol
index 8d86a23..f71cacd 100644
--- a/src/ulysses-omnichain/CoreBranchRouter.sol
+++ b/src/ulysses-omnichain/CoreBranchRouter.sol
@@ -187,10 +187,11 @@ contract CoreBranchRouter is BaseBranchRouter {
      *
      */
     function _manageStrategyToken(address _underlyingToken, uint256 _minimumReservesRatio) internal {
-        if (!IPort(localPortAddress).isStrategyToken(_underlyingToken)) {
-            IPort(localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio);
+        address _localPortAddress = localPortAddress;
+        if (!IPort(_localPortAddress).isStrategyToken(_underlyingToken)) {
+            IPort(_localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio);
         } else {
-            IPort(localPortAddress).toggleStrategyToken(_underlyingToken);
+            IPort(_localPortAddress).toggleStrategyToken(_underlyingToken);
         }
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L177-L180

### Cache `localPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol
177:    function _removeBranchBridgeAgent(address _branchBridgeAgent) internal {
178:        if (!IPort(localPortAddress).isBridgeAgent(_branchBridgeAgent)) revert UnrecognizedBridgeAgent(); // @audit: 1st sload
179:        IPort(localPortAddress).toggleBridgeAgent(_branchBridgeAgent); // @audit: 2nd sload
180:    }
```
```diff
diff --git a/src/ulysses-omnichain/CoreBranchRouter.sol b/src/ulysses-omnichain/CoreBranchRouter.sol
index 8d86a23..29584ab 100644
--- a/src/ulysses-omnichain/CoreBranchRouter.sol
+++ b/src/ulysses-omnichain/CoreBranchRouter.sol
@@ -175,8 +175,9 @@ contract CoreBranchRouter is BaseBranchRouter {
      *
      */
     function _removeBranchBridgeAgent(address _branchBridgeAgent) internal {
-        if (!IPort(localPortAddress).isBridgeAgent(_branchBridgeAgent)) revert UnrecognizedBridgeAgent();
-        IPort(localPortAddress).toggleBridgeAgent(_branchBridgeAgent);
+        address _localPortAddress = localPortAddress;
+        if (!IPort(_localPortAddress).isBridgeAgent(_branchBridgeAgent)) revert UnrecognizedBridgeAgent();
+        IPort(_localPortAddress).toggleBridgeAgent(_branchBridgeAgent);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L164-L167

### Cache `localPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol
164:        if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) { // @audit: 1st sload
165:            IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress); // @audit: 2nd sload
166:        } else {
167:            IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/CoreBranchRouter.sol b/src/ulysses-omnichain/CoreBranchRouter.sol
index 8d86a23..5b9a181 100644
--- a/src/ulysses-omnichain/CoreBranchRouter.sol
+++ b/src/ulysses-omnichain/CoreBranchRouter.sol
@@ -161,10 +161,11 @@ contract CoreBranchRouter is BaseBranchRouter {
      *
      */
     function _toggleBranchBridgeAgentFactory(address _newBridgeAgentFactoryAddress) internal {
-        if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {
-            IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);
+        address _localPortAddress = localPortAddress;
+        if (!IPort(_localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {
+            IPort(_localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);
         } else {
-            IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);
+            IPort(_localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);
         }
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L133-L143

### Cache `localPortAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol
133:        if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {
134:            revert UnrecognizedBridgeAgentFactory();
135:        }
136:
137:        //Create Token
138:        address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(
139:            _newBranchRouter, _rootBridgeAgent, _rootBridgeAgentFactory
140:        );
141:
142:        //Check BridgeAgent Address
143:        if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {
```
```diff
diff --git a/src/ulysses-omnichain/CoreBranchRouter.sol b/src/ulysses-omnichain/CoreBranchRouter.sol
index 8d86a23..b9a18bb 100644
--- a/src/ulysses-omnichain/CoreBranchRouter.sol
+++ b/src/ulysses-omnichain/CoreBranchRouter.sol
@@ -130,7 +130,8 @@ contract CoreBranchRouter is BaseBranchRouter {
         uint128 _remoteExecutionGas
     ) internal virtual {
         //Check if msg.sender is a valid BridgeAgentFactory
-        if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {
+        address _localPortAddress = localPortAddress;
+        if (!IPort(_localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {
             revert UnrecognizedBridgeAgentFactory();
         }

@@ -140,7 +141,7 @@ contract CoreBranchRouter is BaseBranchRouter {
         );

         //Check BridgeAgent Address
-        if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {
+        if (!IPort(_localPortAddress).isBridgeAgent(newBridgeAgent)) {
             revert UnrecognizedBridgeAgent();
         }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L146-L155

### Cache `bridgeAgentAddress` to save 1 SLOAD

*Note: The bot race recognized that we can cache the state variable outside the loop to save 1 SLOAD on each iteration, but failed to recognize that we can then use that cached variable again in line 155 to save another SLOAD. Due to this, I am including this instance here for completeness.*
```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol
146:        for (uint256 i = 0; i < outputTokens.length;) {
147:            //Approve Root Port to spend output hTokens.
148:            ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]); // @audit: 1st sload
149:            unchecked {
150:                ++i;
151:            }
152:        }
153:
154:        //Move output hTokens from Root to Branch and call 'clearTokens'.
155:        IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}( // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/MulticallRootRouter.sol b/src/ulysses-omnichain/MulticallRootRouter.sol
index d4f47b1..27dcaf0 100644
--- a/src/ulysses-omnichain/MulticallRootRouter.sol
+++ b/src/ulysses-omnichain/MulticallRootRouter.sol
@@ -143,16 +143,17 @@ contract MulticallRootRouter is IRootRouter, Ownable {
         uint24 toChain
     ) internal virtual {
         //For each output token
+        address payable _bridgeAgentAddress = bridgeAgentAddress;
         for (uint256 i = 0; i < outputTokens.length;) {
             //Approve Root Port to spend output hTokens.
-            ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);
+            ERC20hTokenRoot(outputTokens[i]).approve(_bridgeAgentAddress, amountsOut[i]);
             unchecked {
                 ++i;
             }
         }

         //Move output hTokens from Root to Branch and call 'clearTokens'.
-        IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(
+        IBridgeAgent(_bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(
             owner, recipient, "", outputTokens, amountsOut, depositsOut, toChain
         );
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L120-L125

### Cache `bridgeAgentAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol
120:        ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut); // @audit: 1st sload
121:
122:        //Move output hTokens from Root to Branch and call 'clearToken'.
123:        IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}( // @audit: 2nd sload
124:            owner, recipient, "", outputToken, amountOut, depositOut, toChain
125:        );
```
```diff
diff --git a/src/ulysses-omnichain/MulticallRootRouter.sol b/src/ulysses-omnichain/MulticallRootRouter.sol
index d4f47b1..eb1b2d9 100644
--- a/src/ulysses-omnichain/MulticallRootRouter.sol
+++ b/src/ulysses-omnichain/MulticallRootRouter.sol
@@ -117,10 +117,11 @@ contract MulticallRootRouter is IRootRouter, Ownable {
         uint24 toChain
     ) internal virtual {
         //Approve Root Port to spend/send output hTokens.
-        ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);
+        address payable _bridgeAgentAddress = bridgeAgentAddress;
+        ERC20hTokenRoot(outputToken).approve(_bridgeAgentAddress, amountOut);

         //Move output hTokens from Root to Branch and call 'clearToken'.
-        IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(
+        IBridgeAgent(_bridgeAgentAddress).callOutAndBridge{value: msg.value}(
             owner, recipient, "", outputToken, amountOut, depositOut, toChain
         );
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L434-L444

### Cache `coreRootRouterAddress` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootPort.sol
434:        address newGlobalToken = address(
435:            IERC20hTokenRootFactory(ICoreRootRouter(coreRootRouterAddress).hTokenFactoryAddress()).createToken( // @audit: 1st sload
436:                _wrappedGasTokenName, _wrappedGasTokenSymbol
437:            )
438:        );
439:
440:        ERC20hTokenRoot(newGlobalToken).mint(_pledger, _pledgedInitialAmount, _chainId);
441:
442:        IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent( // @audit: 2nd sload
443:            _coreBranchBridgeAgentAddress, _chainId
444:        );
```
```diff
diff --git a/src/ulysses-omnichain/RootPort.sol b/src/ulysses-omnichain/RootPort.sol
index 80d2bea..9371fda 100644
--- a/src/ulysses-omnichain/RootPort.sol
+++ b/src/ulysses-omnichain/RootPort.sol
@@ -431,15 +431,16 @@ contract RootPort is Ownable, IRootPort {
         address _newLocalBranchWrappedNativeTokenAddress,
         address _newUnderlyingBranchWrappedNativeTokenAddress
     ) external onlyOwner {
+        address _coreRootRouterAddress = coreRootRouterAddress;
         address newGlobalToken = address(
-            IERC20hTokenRootFactory(ICoreRootRouter(coreRootRouterAddress).hTokenFactoryAddress()).createToken(
+            IERC20hTokenRootFactory(ICoreRootRouter(_coreRootRouterAddress).hTokenFactoryAddress()).createToken(
                 _wrappedGasTokenName, _wrappedGasTokenSymbol
             )
         );

         ERC20hTokenRoot(newGlobalToken).mint(_pledger, _pledgedInitialAmount, _chainId);

-        IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(
+        IBridgeAgent(ICoreRootRouter(_coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(
             _coreBranchBridgeAgentAddress, _chainId
         );
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L367-L372

### Cache `isBridgeAgent[_bridgeAgent]` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootPort.sol
367:        if (isBridgeAgent[_bridgeAgent]) revert AlreadyAddedBridgeAgent(); // @audit: 1st sload
368:
369:        bridgeAgents.push(_bridgeAgent);
370:        bridgeAgentsLenght++;
371:        getBridgeAgentManager[_bridgeAgent] = _manager;
372:        isBridgeAgent[_bridgeAgent] = !isBridgeAgent[_bridgeAgent]; // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/RootPort.sol b/src/ulysses-omnichain/RootPort.sol
index 80d2bea..899ddab 100644
--- a/src/ulysses-omnichain/RootPort.sol
+++ b/src/ulysses-omnichain/RootPort.sol
@@ -364,12 +364,13 @@ contract RootPort is Ownable, IRootPort {

     /// @inheritdoc IRootPort
     function addBridgeAgent(address _manager, address _bridgeAgent) external requiresBridgeAgentFactory {
-        if (isBridgeAgent[_bridgeAgent]) revert AlreadyAddedBridgeAgent();
+        bool _isBridgeAgent = isBridgeAgent[_bridgeAgent];
+        if (_isBridgeAgent) revert AlreadyAddedBridgeAgent();

         bridgeAgents.push(_bridgeAgent);
         bridgeAgentsLenght++;
         getBridgeAgentManager[_bridgeAgent] = _manager;
-        isBridgeAgent[_bridgeAgent] = !isBridgeAgent[_bridgeAgent];
+        isBridgeAgent[_bridgeAgent] = !_isBridgeAgent;

         emit BridgeAgentAdded(_bridgeAgent, _manager);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L430-L444

### Cache `incentive.totalRewardUnclaimed` and `incentive.totalSecondsClaimedX128` to save 2 SLOADs
```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol
430:            incentive.totalRewardUnclaimed, // @audit: 1st sload
431:            incentive.totalSecondsClaimedX128, // @audit: 2nd sload
432:            key.startTime,
433:            endTime,
434:            secondsInsideX128,
435:            block.timestamp
436:        );
437:
438:        unchecked {
439:            // if this overflows, e.g. after 2^32-1 full liquidity seconds have been claimed,
440:            // reward rate will fall drastically so it's safe
441:            // can't overflow if incentiveDuration < 2^32-1 seconds
442:            incentive.totalSecondsClaimedX128 += secondsInsideX128; // @audit: 2nd sload
443:            // reward is never greater than total reward unclaimed
444:            incentive.totalRewardUnclaimed -= reward; // @audit: 2nd sload
```
```diff
diff --git a/src/uni-v3-staker/UniswapV3Staker.sol b/src/uni-v3-staker/UniswapV3Staker.sol
index 5970379..f40d77d 100644
--- a/src/uni-v3-staker/UniswapV3Staker.sol
+++ b/src/uni-v3-staker/UniswapV3Staker.sol
@@ -426,9 +426,11 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {
         deposit.stakedTimestamp = 0;
         Incentive storage incentive = incentives[incentiveId];
         incentive.numberOfStakes--;
+        uint256 _totalRewardUnclaimed = incentive.totalRewardUnclaimed;
+        uint160 _totalSecondsClaimedX128 = incentive.totalSecondsClaimedX128;
         uint256 reward = RewardMath.computeBoostedRewardAmount(
-            incentive.totalRewardUnclaimed,
-            incentive.totalSecondsClaimedX128,
+            _totalRewardUnclaimed,
+            _totalSecondsClaimedX128,
             key.startTime,
             endTime,
             secondsInsideX128,
@@ -439,9 +441,9 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {
             // if this overflows, e.g. after 2^32-1 full liquidity seconds have been claimed,
             // reward rate will fall drastically so it's safe
             // can't overflow if incentiveDuration < 2^32-1 seconds
-            incentive.totalSecondsClaimedX128 += secondsInsideX128;
+            incentive.totalSecondsClaimedX128 = _totalSecondsClaimedX128 + secondsInsideX128;
             // reward is never greater than total reward unclaimed
-            incentive.totalRewardUnclaimed -= reward;
+            incentive.totalRewardUnclaimed = _totalRewardUnclaimed - reward;
             // this only overflows if a token has a total supply greater than type(uint256).max
             rewards[owner] += reward;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L507-L522

### Use new storage variable values instead of re-reading from storage to save 4 SLOADs
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
507:        require(
508:            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only" // @audit: 1st sload
509:        );
510:
511:        // Save current values for inclusion in log
512:        address oldAdmin = admin; // @audit: 1st sload
513:        address oldPendingAdmin = pendingAdmin; // @audit: 2nd sload
514:
515:        // Store admin with value pendingAdmin
516:        admin = pendingAdmin; // @audit: 3rd sload
517:
518:        // Clear the pending value
519:        pendingAdmin = address(0);
520:
521:        emit NewAdmin(oldAdmin, admin); // @audit: 2nd sload
522:        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin); // @audit: 4th sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..89474d3 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -504,22 +504,22 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
      */
     function _acceptAdmin() external {
         // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
+        address oldPendingAdmin = pendingAdmin;
         require(
-            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"
+            msg.sender == oldPendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"
         );

         // Save current values for inclusion in log
         address oldAdmin = admin;
-        address oldPendingAdmin = pendingAdmin;

         // Store admin with value pendingAdmin
-        admin = pendingAdmin;
+        admin = oldPendingAdmin;

         // Clear the pending value
         pendingAdmin = address(0);

-        emit NewAdmin(oldAdmin, admin);
-        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
+        emit NewAdmin(oldAdmin, oldPendingAdmin);
+        emit NewPendingAdmin(oldPendingAdmin, address(0));
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L477-L478

### Cache function call and use cached value when setting `initialProposalId` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
477:        proposalCount = GovernorAlpha(governorAlpha).proposalCount(); // @audit: cache new `porposalCount` value
478:        initialProposalId = proposalCount; // @audit: unnecessary sload. Use cached `proposalCount` value 
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..6a64890 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -474,8 +474,9 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
     function _initiate(address governorAlpha) external {
         require(msg.sender == admin, "GovernorBravo::_initiate: admin only");
         require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");
-        proposalCount = GovernorAlpha(governorAlpha).proposalCount();
-        initialProposalId = proposalCount;
+        uint256 _newProposalCount = GovernorAlpha(governorAlpha).proposalCount();
+        proposalCount = _newProposalCount;
+        initialProposalId = _newProposalCount;
         timelock.acceptAdmin();
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L234-L246

### Cache `proposal.proposer` to save 2 SLOADs
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
234:        if (msg.sender != proposal.proposer && msg.sender != admin) { // @audit: 1st sload
235:            // Whitelisted proposers can't be canceled for falling below proposal threshold
236:            if (isWhitelisted(proposal.proposer)) { // @audit: 2nd sload
237:                require(
238:                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()) // @audit: 3rd sload
239:                        && msg.sender == whitelistGuardian,
240:                    "GovernorBravo::cancel: whitelisted proposer"
241:                );
242:            } else {
243:                require(
244:                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()), // @audit: 3rd sload
245:                    "GovernorBravo::cancel: proposer above threshold"
246:                );
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..ed27f8f 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -231,17 +231,18 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE

         // Proposer can cancel
         // admin is Emergency DAO and can cancel any proposal
-        if (msg.sender != proposal.proposer && msg.sender != admin) {
+        address _proposer = proposal.proposer;
+        if (msg.sender != _proposer && msg.sender != admin) {
             // Whitelisted proposers can't be canceled for falling below proposal threshold
-            if (isWhitelisted(proposal.proposer)) {
+            if (isWhitelisted(_proposer)) {
                 require(
-                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
+                    (govToken.getPriorVotes(_proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
                         && msg.sender == whitelistGuardian,
                     "GovernorBravo::cancel: whitelisted proposer"
                 );
             } else {
                 require(
-                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
+                    (govToken.getPriorVotes(_proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
                     "GovernorBravo::cancel: proposer above threshold"
                 );
             }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L197-L201

### Cache `timelock` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
197:        require(
198:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))), // @audit: 1st sload
199:            "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
200:        );
201:        timelock.queueTransaction(target, value, signature, data, eta); // @audit: 2nd sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..5bb3eff 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -194,11 +194,12 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         bytes memory data,
         uint256 eta
     ) internal {
+        TimelockInterface _timelock = timelock;
         require(
-            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
+            !_timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
             "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
         );
-        timelock.queueTransaction(target, value, signature, data, eta);
+        _timelock.queueTransaction(target, value, signature, data, eta);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L148-L162

### Use `msg.sender` instead of reading from storage to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
148:        newProposal.proposer = msg.sender;
...
162:        latestProposalIds[newProposal.proposer] = newProposal.id; // @audit: unnecessary sload. Use `msg.sender` instead of `newProposal.proposer`
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..ed22a7d 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -159,7 +159,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         newProposal.canceled = false;
         newProposal.executed = false;

-        latestProposalIds[newProposal.proposer] = newProposal.id;
+        latestProposalIds[msg.sender] = newProposal.id;

         emit ProposalCreated(
             newProposal.id, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock, description
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L147-L167

### Use `newProposalID` instead of reading from storage to save 3 SLOADs
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
147:        newProposal.id = newProposalID;
...
162:        latestProposalIds[newProposal.proposer] = newProposal.id; // @audit: 1st sload
163:
164:        emit ProposalCreated(
165:            newProposal.id, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock, description // @audit: 2nd sload
166:        );
167:        return newProposal.id; // @audit: 3rd sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..2184768 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -159,12 +159,12 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         newProposal.canceled = false;
         newProposal.executed = false;

-        latestProposalIds[newProposal.proposer] = newProposal.id;
+        latestProposalIds[newProposal.proposer] = newProposalID;

         emit ProposalCreated(
-            newProposal.id, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock, description
+            newProposalID, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock, description
         );
-        return newProposal.id;
+        return newProposalID;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L142-L143

### Cache updated `proposalCount` in `newProposalID` and then set `proposalCount` to `newProposalID` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
142:        proposalCount++; // @audit: 1st sload
143:        uint256 newProposalID = proposalCount; // @audit: 2nd sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..d22a4ae 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -139,8 +139,8 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         uint256 startBlock = add256(block.number, votingDelay);
         uint256 endBlock = add256(startBlock, votingPeriod);

-        proposalCount++;
-        uint256 newProposalID = proposalCount;
+        uint256 newProposalID = proposalCount + 1;
+        proposalCount = newProposalID;
         Proposal storage newProposal = proposals[newProposalID];
         // This should never happen but add a check in case.
         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L216-L217

### Cache `proposal.values[i]` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
216:            timelock.executeTransaction{value: proposal.values[i]}( // @audit: 1st sload 
217:                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta // @audit: 2nd sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..93a9189 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -213,8 +213,9 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         Proposal storage proposal = proposals[proposalId];
         proposal.executed = true;
         for (uint256 i = 0; i < proposal.targets.length; i++) {
-            timelock.executeTransaction{value: proposal.values[i]}(
-                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta
+            uint256 _proposalValue = proposal.values[i];
+            timelock.executeTransaction{value: _proposalValue}(
+                proposal.targets[i], _proposalValue, proposal.signatures[i], proposal.calldatas[i], proposal.eta
             );
         }
         emit ProposalExecuted(proposalId);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L839-L845

### Cache `getSettlement[_settlementNonce].gasToBridgeOut` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
839:        if (minExecCost > getSettlement[_settlementNonce].gasToBridgeOut) { // @audit: 1st sload
840:            _forceRevert();
841:            return;
842:        }
843:
844:        //Update user deposit reverts if not enough gas
845:        getSettlement[_settlementNonce].gasToBridgeOut -= minExecCost.toUint128(); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..391cd1b 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -836,13 +836,14 @@ contract RootBridgeAgent is IRootBridgeAgent {
         uint256 minExecCost = tx.gasprice * (MIN_FALLBACK_RESERVE + _initialGas - gasLeft);

         //Check if sufficient balance
-        if (minExecCost > getSettlement[_settlementNonce].gasToBridgeOut) {
+        uint128 _gasToBridgeOut = getSettlement[_settlementNonce].gasToBridgeOut;
+        if (minExecCost > _gasToBridgeOut) {
             _forceRevert();
             return;
         }

         //Update user deposit reverts if not enough gas
-        getSettlement[_settlementNonce].gasToBridgeOut -= minExecCost.toUint128();
+        getSettlement[_settlementNonce].gasToBridgeOut = _gasToBridgeOut - minExecCost.toUint128();
     }

     function _replenishGas(uint256 _executionGasSpent) internal {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L748-L759

### Cache `userFeeInfo.gasToBridgeOut` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
748:        if (_toChain == localChainId) {
749:            //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
750:            if (_initialGas > 0) {
751:                address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], userFeeInfo.gasToBridgeOut); // @audit: 1st sload
752:            }
753:
754:            return uint128(userFeeInfo.gasToBridgeOut); // @audit: 2nd sload
755:        }
756:
757:        if (_initialGas > 0) {
758:            if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees(); // @audit: 1st sload
759:            (amountOut, gasToken) = _gasSwapOut(userFeeInfo.gasToBridgeOut, _toChain); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..48b9289 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -747,16 +747,18 @@ contract RootBridgeAgent is IRootBridgeAgent {

         if (_toChain == localChainId) {
             //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
+            uint128 _gasToBridgeOut = userFeeInfo.gasToBridgeOut;
             if (_initialGas > 0) {
-                address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], userFeeInfo.gasToBridgeOut);
+                address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], _gasToBridgeOut);
             }

-            return uint128(userFeeInfo.gasToBridgeOut);
+            return uint128(_gasToBridgeOut);
         }

         if (_initialGas > 0) {
-            if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
-            (amountOut, gasToken) = _gasSwapOut(userFeeInfo.gasToBridgeOut, _toChain);
+            uint128 _gasToBridgeOut = userFeeInfo.gasToBridgeOut;
+            if (_gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
+            (amountOut, gasToken) = _gasSwapOut(_gasToBridgeOut, _toChain);
         } else {
             if (msg.value <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
             wrappedNativeToken.deposit{value: msg.value}();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L598-L606

### Cache `settlement.hTokens[i]` and `settlement.toChain` to save 2 SLOADs
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
598:            if (settlement.hTokens[i] != address(0)) { // @audit: 1st sload
599:                //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
600:                IPort(localPortAddress).bridgeToRoot(
601:                    msg.sender,
602:                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain), // @audit: 1st sload for `toChain` & 2nd sload for `hTokens[i]`
603:                    settlement.amounts[i],
604:                    settlement.deposits[i],
605:                    settlement.toChain // @audit: 2nd sload
606:                );
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..11f0722 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -594,15 +594,17 @@ contract RootBridgeAgent is IRootBridgeAgent {

         //Clear Global hTokens To Recipient on Root Chain cancelling Settlement to Branch
         for (uint256 i = 0; i < settlement.hTokens.length;) {
+            address _hToken = settlement.hTokens[i];
             //Check if asset
-            if (settlement.hTokens[i] != address(0)) {
+            if (_hToken != address(0)) {
+                uint24 _toChain = settlement.toChain;
                 //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
                 IPort(localPortAddress).bridgeToRoot(
                     msg.sender,
-                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain),
+                    IPort(localPortAddress).getGlobalTokenFromLocal(_hToken, _toChain),
                     settlement.amounts[i],
                     settlement.deposits[i],
-                    settlement.toChain
+                    _toChain
                 );
             }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1069-L1075

### Cache `getDeposit[_depositNonce].depositedGas` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
1069:        if (minExecCost > getDeposit[_depositNonce].depositedGas) { // @audit: 1st sload
1070:            _forceRevert();
1071:            return;
1072:        }
1073:
1074:        //Update user deposit reverts if not enough gas => user must boost deposit with gas
1075:        getDeposit[_depositNonce].depositedGas -= minExecCost.toUint128(); // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..22b080c 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -1066,13 +1066,14 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         uint256 minExecCost = tx.gasprice * (MIN_FALLBACK_RESERVE + _initialGas - gasLeft);

         //Check if sufficient balance
-        if (minExecCost > getDeposit[_depositNonce].depositedGas) {
+        uint128 _depositedGas = getDeposit[_depositNonce].depositedGas;
+        if (minExecCost > _depositedGas) {
             _forceRevert();
             return;
         }

         //Update user deposit reverts if not enough gas => user must boost deposit with gas
-        getDeposit[_depositNonce].depositedGas -= minExecCost.toUint128();
+        getDeposit[_depositNonce].depositedGas = _depositedGas - minExecCost.toUint128();

         //Withdraw Gas
         IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L332-L364

### Cache `getDeposit[_depositNonce].tokens[0]` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
332:        if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) { 
333:            if (_isSigned) {
334:                packedData = abi.encodePacked(
335:                    bytes1(0x05),
336:                    msg.sender,
337:                    _depositNonce,
338:                    getDeposit[_depositNonce].hTokens[0],
339:                    getDeposit[_depositNonce].tokens[0], // @audit: 1st sload for `tokens[0]`
340:                    getDeposit[_depositNonce].amounts[0],
341:                    _normalizeDecimals(
342:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals() // @audit: 2nd sload for `tokens[0]`
343:                    ),
344:                    _toChain,
345:                    _params,
346:                    msg.value.toUint128(),
347:                    _remoteExecutionGas
348:                );
349:            } else {
350:                packedData = abi.encodePacked(
351:                    bytes1(0x02),
352:                    _depositNonce,
353:                    getDeposit[_depositNonce].hTokens[0],
354:                    getDeposit[_depositNonce].tokens[0], // @audit: 1st sload for `tokens[0]`
355:                    getDeposit[_depositNonce].amounts[0],
356:                    _normalizeDecimals(
357:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals() // @audit: 2nd sload for `tokens[0]`
358:                    ),
359:                    _toChain,
360:                    _params,
361:                    msg.value.toUint128(),
362:                    _remoteExecutionGas
363:                );
364:            }     
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..e2286e7 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -331,15 +331,16 @@ contract BranchBridgeAgent is IBranchBridgeAgent {

         if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {
             if (_isSigned) {
+                address token = getDeposit[_depositNonce].tokens[0];
                 packedData = abi.encodePacked(
                     bytes1(0x05),
                     msg.sender,
                     _depositNonce,
                     getDeposit[_depositNonce].hTokens[0],
-                    getDeposit[_depositNonce].tokens[0],
+                    token,
                     getDeposit[_depositNonce].amounts[0],
                     _normalizeDecimals(
-                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
+                        getDeposit[_depositNonce].deposits[0], ERC20(token).decimals()
                     ),
                     _toChain,
                     _params,
@@ -347,14 +348,15 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                     _remoteExecutionGas
                 );
             } else {
+                address token = getDeposit[_depositNonce].tokens[0];
                 packedData = abi.encodePacked(
                     bytes1(0x02),
                     _depositNonce,
                     getDeposit[_depositNonce].hTokens[0],
-                    getDeposit[_depositNonce].tokens[0],
+                    token,
                     getDeposit[_depositNonce].amounts[0],
                     _normalizeDecimals(
-                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
+                        getDeposit[_depositNonce].deposits[0], ERC20(token).decimals()
                     ),
                     _toChain,
                     _params,
```

## State variables can be packed into fewer storage slots
The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a `Gwarmaccess (100 gas)` versus a `Gcoldsload (2100 gas)`.

Total Instances: `13`

Estimated Gas Saved: `16 (slots) * 2000 = 32000`

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L40-L67

### Pack `totalWeights` and `protocolFee` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `totalWeights` and `protocolFee` both have a max upper bound and therefore their `uint` type can be reduced and they can be packed together into one slot. `totalWeights` needs to be `>= uint8` to hold the value `256` and `protocolFee` needs to be `>= uint56` to hold the value `1e16`.*
```solidity
File: src/ulysses-amm/UlyssesPool.sol
40:    uint256 public totalWeights;
...
45:    /// @notice The maximum sum of all weights
46:    uint256 private constant MAX_TOTAL_WEIGHT = 256; // @audit: max value that `totalWeights` is allowed to hold. 
...
51:    /// @notice The maximum protocol fee that can be set (1%)
52:    uint256 private constant MAX_PROTOCOL_FEE = 1e16; // @audit: max value that `protocolFee` is allowed to hold
...
67:    uint256 public protocolFee = 1e14;
```
```diff
diff --git a/src/ulysses-amm/UlyssesPool.sol b/src/ulysses-amm/UlyssesPool.sol
index 19f453e..1e47fe1 100644
--- a/src/ulysses-amm/UlyssesPool.sol
+++ b/src/ulysses-amm/UlyssesPool.sol
@@ -37,7 +37,9 @@ contract UlyssesPool is UlyssesERC4626, Ownable, IUlyssesPool {
     mapping(address => uint256) public destinationIds;

     /// @notice Sum of all weights
-    uint256 public totalWeights;
+    uint128 public totalWeights;
+
+    uint128 public protocolFee = 1e14;

     /// @notice The minimum amount that can be swapped
     uint256 private constant MIN_SWAP_AMOUNT = 1e4;
@@ -64,7 +66,6 @@ contract UlyssesPool is UlyssesERC4626, Ownable, IUlyssesPool {
     /// @notice The divisioner for fee calculations
     uint256 private constant DIVISIONER = 1 ether;

-    uint256 public protocolFee = 1e14;

     /// @notice The current rebalancing fees
     Fees public fees = Fees({lambda1: 20e14, lambda2: 4980e14, sigma1: 6000e14, sigma2: 500e14});
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L39-L54

### Rearrange storage variables to pack `liquidity`, `tickLower`, `tickUpper`, and `initialized` into one storage slot. This saves 1 SLOT (~2000 gas)
```solidity
File: src/talos/base/TalosBaseStrategy.sol
39:    uint128 public override liquidity;
40:
41:    /// @inheritdoc ITalosBaseStrategy
42:    uint256 public protocolFees0;
43:    /// @inheritdoc ITalosBaseStrategy
44:    uint256 public protocolFees1;
45:
46:    /// @notice Current tick lower of Optimizer pool position
47:    /// @inheritdoc ITalosBaseStrategy
48:    int24 public override tickLower;
49:    /// @notice Current tick higher of Optimizer pool position
50:    /// @inheritdoc ITalosBaseStrategy
51:    int24 public override tickUpper;
52:
53:    /// @inheritdoc ITalosBaseStrategy
54:    bool public initialized;
```
```diff
diff --git a/src/talos/base/TalosBaseStrategy.sol b/src/talos/base/TalosBaseStrategy.sol
index 8cb5a4d..6bf93d3 100644
--- a/src/talos/base/TalosBaseStrategy.sol
+++ b/src/talos/base/TalosBaseStrategy.sol
@@ -35,14 +35,14 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa

     /// @inheritdoc ITalosBaseStrategy
     uint256 public override tokenId;
-    /// @inheritdoc ITalosBaseStrategy
-    uint128 public override liquidity;

     /// @inheritdoc ITalosBaseStrategy
     uint256 public protocolFees0;
     /// @inheritdoc ITalosBaseStrategy
     uint256 public protocolFees1;

+    /// @inheritdoc ITalosBaseStrategy
+    uint128 public override liquidity;
     /// @notice Current tick lower of Optimizer pool position
     /// @inheritdoc ITalosBaseStrategy
     int24 public override tickLower;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol#L34-L35

### Pack `currentMonth` and `unstakePeriodEnd` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `currentMonth` and `unstakePeriodEnd` both hold timestamp values and therefore can be reduced to safetly have a size >= `uint40`.*
```solidity
File: src/maia/vMaia.sol
34:    uint256 private currentMonth;
35:    uint256 private unstakePeriodEnd;
```
```diff
diff --git a/src/maia/vMaia.sol b/src/maia/vMaia.sol
index 3aa70cf..4176472 100644
--- a/src/maia/vMaia.sol
+++ b/src/maia/vMaia.sol
@@ -31,8 +31,8 @@ contract vMaia is ERC4626PartnerManager {
                          vMAIA STATE
     //////////////////////////////////////////////////////////////*/

-    uint256 private currentMonth;
-    uint256 private unstakePeriodEnd;
+    uint128 private currentMonth;
+    uint128 private unstakePeriodEnd;

     /**
      * @notice Initializes the vMaia token.
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L28-L49

### Pack `dao`, `daoShare`, `tailEmission`, and `activePeriod` into one storage slot to save 3 SLOTs (~6000 gas)

*Note: `daoShare` and `tailEmission` have max upper bounds and therefore can be reduced to a lower `uint` size (`uint16` should suffice). `activePeriod` holds timestamps and can therefore be a `uint` type >= `uint40`.*

```solidity
File: src/hermes/minters/BaseV2Minter.sol
28:    uint256 internal constant max_tail_emission = 100; // @audit: max value that `tailEmission` is allowed to hold
29:    uint256 internal constant max_dao_share = 300; // @audit: max value that `daoShare` is allowed to hold
...
39:    address public override dao;
40:
41:    /// @inheritdoc IBaseV2Minter
42:    uint256 public override daoShare = 100;
43:    uint256 public override tailEmission = 20;
44:    /// @inheritdoc IBaseV2Minter
45:
46:    /// @inheritdoc IBaseV2Minter
47:    uint256 public override weekly;
48:    /// @inheritdoc IBaseV2Minter
49:    uint256 public override activePeriod;
```
```diff
diff --git a/src/hermes/minters/BaseV2Minter.sol b/src/hermes/minters/BaseV2Minter.sol
index 7d7f013..d94a195 100644
--- a/src/hermes/minters/BaseV2Minter.sol
+++ b/src/hermes/minters/BaseV2Minter.sol
@@ -37,16 +37,16 @@ contract BaseV2Minter is Ownable, IBaseV2Minter {
     FlywheelGaugeRewards public override flywheelGaugeRewards;
     /// @inheritdoc IBaseV2Minter
     address public override dao;
+    /// @inheritdoc IBaseV2Minter
+    uint64 public override activePeriod;

     /// @inheritdoc IBaseV2Minter
-    uint256 public override daoShare = 100;
-    uint256 public override tailEmission = 20;
+    uint16 public override daoShare = 100;
+    uint16 public override tailEmission = 20;
     /// @inheritdoc IBaseV2Minter

     /// @inheritdoc IBaseV2Minter
     uint256 public override weekly;
-    /// @inheritdoc IBaseV2Minter
-    uint256 public override activePeriod;

     address internal initializer;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L23

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L143

### Pack `localBridgeAgentAddress` and `_unlocked` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `_unlocked` is a mutex, thus we can reduce the `uint` type to `uint8`.*

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol
23:     address public localBridgeAgentAddress;
...
143:    uint256 internal _unlocked = 1;
```
```diff
diff --git a/src/ulysses-omnichain/BaseBranchRouter.sol b/src/ulysses-omnichain/BaseBranchRouter.sol
index 4ef1023..3615d02 100644
--- a/src/ulysses-omnichain/BaseBranchRouter.sol
+++ b/src/ulysses-omnichain/BaseBranchRouter.sol
@@ -21,6 +21,7 @@ import {
 contract BaseBranchRouter is IBranchRouter, Ownable {
     /// @inheritdoc IBranchRouter
     address public localBridgeAgentAddress;
+    uint8 internal _unlocked = 1;

     /// @inheritdoc IBranchRouter
     address public bridgeAgentExecutorAddress;
@@ -140,7 +141,6 @@ contract BaseBranchRouter is IBranchRouter, Ownable {
         _;
     }

-    uint256 internal _unlocked = 1;

     /// @notice Modifier for a simple re-entrancy check.
     modifier lock() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L36-L51

### Rearrange storage variables to pack `gaugeCycle`, `nextCycle`, `nextCycleQueuedRewards`, and `paginationOffset` into one storage slot. This saves 1 SLOT (~2000 gas)
```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
36:    uint32 public override gaugeCycle;
37:
38:    /// @inheritdoc IFlywheelGaugeRewards
39:    uint32 public immutable override gaugeCycleLength;
49:
41:    /// @inheritdoc IFlywheelGaugeRewards
42:    mapping(ERC20 => QueuedRewards) public override gaugeQueuedRewards;
43:
44:    /// @notice the start of the next cycle being partially queued
45:    uint32 internal nextCycle;
46:
47:    // rewards that made it into a partial queue but didn't get completed
48:    uint112 internal nextCycleQueuedRewards;
49:
50:    // the offset during pagination of the queue
51:    uint32 internal paginationOffset;
```
```diff
diff --git a/src/rewards/rewards/FlywheelGaugeRewards.sol b/src/rewards/rewards/FlywheelGaugeRewards.sol
index a33e81a..99110e5 100644
--- a/src/rewards/rewards/FlywheelGaugeRewards.sol
+++ b/src/rewards/rewards/FlywheelGaugeRewards.sol
@@ -32,15 +32,15 @@ contract FlywheelGaugeRewards is Ownable, IFlywheelGaugeRewards {
     /// @inheritdoc IFlywheelGaugeRewards
     address public immutable override rewardToken;

-    /// @inheritdoc IFlywheelGaugeRewards
-    uint32 public override gaugeCycle;
-
     /// @inheritdoc IFlywheelGaugeRewards
     uint32 public immutable override gaugeCycleLength;

     /// @inheritdoc IFlywheelGaugeRewards
     mapping(ERC20 => QueuedRewards) public override gaugeQueuedRewards;

+    /// @inheritdoc IFlywheelGaugeRewards
+    uint32 public override gaugeCycle;
+
     /// @notice the start of the next cycle being partially queued
     uint32 internal nextCycle;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L62

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L491

### Pack `bridgeAgentExecutorAddress` and `_unlocked` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `_unlocked` is a mutex, thus we can reduce the `uint` type to `uint8`.*

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol
62:     address public bridgeAgentExecutorAddress;
...
491:    uint256 internal _unlocked = 1;
```
```diff
diff --git a/src/ulysses-omnichain/MulticallRootRouter.sol b/src/ulysses-omnichain/MulticallRootRouter.sol
index d4f47b1..81a91f9 100644
--- a/src/ulysses-omnichain/MulticallRootRouter.sol
+++ b/src/ulysses-omnichain/MulticallRootRouter.sol
@@ -61,6 +61,8 @@ contract MulticallRootRouter is IRootRouter, Ownable {

     address public bridgeAgentExecutorAddress;

+    uint8 internal _unlocked = 1;
+
     constructor(uint256 _localChainId, address _localPortAddress, address _multicallAddress) {
         require(_localPortAddress != address(0), "Local Port Address cannot be 0");
         require(_multicallAddress != address(0), "Multicall Address cannot be 0");
@@ -488,7 +490,6 @@ contract MulticallRootRouter is IRootRouter, Ownable {
                             MODIFIERS
     ////////////////////////////////////////////////////////////*/

-    uint256 internal _unlocked = 1;

     /// @notice Modifier for a simple re-entrancy check.
     modifier lock() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L40-L126

### Pack `coreRootBridgeAgentAddress` and `_setup` into one storage slot to save 1 SLOT (~2000 gas)
```solidity
File: src/ulysses-omnichain/RootPort.sol
40:     address public coreRootBridgeAgentAddress;
...
126:    bool internal _setup;
```
```diff
diff --git a/src/ulysses-omnichain/RootPort.sol b/src/ulysses-omnichain/RootPort.sol
index 80d2bea..b807ebc 100644
--- a/src/ulysses-omnichain/RootPort.sol
+++ b/src/ulysses-omnichain/RootPort.sol
@@ -39,6 +39,8 @@ contract RootPort is Ownable, IRootPort {
     /// @notice The address of the core router in charge of adding new tokens to the system.
     address public coreRootBridgeAgentAddress;

+    bool internal _setup;
+
     /*///////////////////////////////////////////////////////////////
                         VIRTUAL ACCOUNT
     //////////////////////////////////////////////////////////////*/
@@ -123,7 +125,6 @@ contract RootPort is Ownable, IRootPort {
         _setup = true;
     }

-    bool internal _setup;

     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {
         require(_setup, "Setup ended.");
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L79-L85

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L12-L27

### Pack `votingDelay`, `votingPeriod`, and `proposalThreshold` into one storage slot to save 2 SLOTs (~4000 gas)

*Note: All listed state variables are enforced to always stay within specified bounds. `uint80` is sufficient enough to hold the max values for each variable.*

```solidity
File: src/governance/GovernorBravoInterfaces.sol
79:    uint256 public votingDelay;
80:
81:    /// @notice The duration of voting on a proposal, in blocks
82:    uint256 public votingPeriod;
83:
84:    /// @notice The number of votes required in order for a voter to become a proposer
85:    uint256 public proposalThreshold;
```

*Below are the bounds for the above state variables*

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
12:    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken
13:
14:    /// @notice The maximum setable proposal threshold
15:    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken
16:
17:    /// @notice The minimum setable voting period
18:    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks
19:
20:    /// @notice The max setable voting period
21:    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks
22:
23:    /// @notice The min setable voting delay
24:    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks
25:
26:    /// @notice The max setable voting delay
27:    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks
```
```diff
diff --git a/src/governance/GovernorBravoInterfaces.sol b/src/governance/GovernorBravoInterfaces.sol
index e89b6a4..35d0cb1 100644
--- a/src/governance/GovernorBravoInterfaces.sol
+++ b/src/governance/GovernorBravoInterfaces.sol
@@ -75,17 +75,17 @@ contract GovernorBravoDelegatorStorage {
  * GovernorBravoDelegateStorageVX.
  */
 contract GovernorBravoDelegateStorageV1 is GovernorBravoDelegatorStorage {
+    /// @notice Initial proposal id set at become
+    uint256 public initialProposalId;
+
     /// @notice The delay before voting on a proposal may take place, once proposed, in blocks
-    uint256 public votingDelay;
+    uint80 public votingDelay;

     /// @notice The duration of voting on a proposal, in blocks
-    uint256 public votingPeriod;
+    uint80 public votingPeriod;

     /// @notice The number of votes required in order for a voter to become a proposer
-    uint256 public proposalThreshold;
-
-    /// @notice Initial proposal id set at become
-    uint256 public initialProposalId;
+    uint80 public proposalThreshold;

     /// @notice The total number of proposals
     uint256 public proposalCount;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L164

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1270

### Pack `settlementNonce` and `_unlocked` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `_unlocked` is a mutex, thus we can reduce the `uint` type to `uint8`.*
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
164:     uint32 public settlementNonce;

1270:    uint256 internal _unlocked = 1;
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..e2d5a0f 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -162,6 +162,7 @@ contract RootBridgeAgent is IRootBridgeAgent {

     /// @notice Deposit nonce used for identifying transaction.
     uint32 public settlementNonce;
+    uint8 internal _unlocked = 1;

     /// @notice Mapping from Settlement nonce to Deposit Struct.
     mapping(uint32 => Settlement) public getSettlement;
@@ -1267,7 +1268,6 @@ contract RootBridgeAgent is IRootBridgeAgent {
                             MODIFIERS
     //////////////////////////////////////////////////////////////*/

-    uint256 internal _unlocked = 1;

     /// @notice Modifier for a simple re-entrancy check.
     modifier lock() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L112-L119

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1363

### Pack `bridgeAgentExecutorAddress`, `depositNonce`, and `_unlocked` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `_unlocked` is a mutex, thus we can reduce its `uint` size to `uint8`.*
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
112:     address public bridgeAgentExecutorAddress;
...
119:     uint32 public depositNonce;

1363:    uint256 internal _unlocked = 1;
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..c313b13 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -118,6 +118,8 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
     /// @notice Deposit nonce used for identifying transaction.
     uint32 public depositNonce;

+    uint8 internal _unlocked = 1;
+
     /// @notice Mapping from Pending deposits hash to Deposit Struct.
     mapping(uint32 => Deposit) public getDeposit;

@@ -1360,7 +1362,6 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                                 MODIFIERS
     //////////////////////////////////////////////////////////////*/

-    uint256 internal _unlocked = 1;

     /// @notice Modifier for a simple re-entrancy check.
     modifier lock() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L20

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L420

### Pack `coreBranchRouterAddress` and `_unlocked` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `_unlocked` is a mutex, thus we can reduce its `uint` size to `uint8`.*
```solidity
File: src/ulysses-omnichain/BranchPort.sol
20:     address public coreBranchRouterAddress;
...
420:    uint256 internal _unlocked = 1;
```
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..d9949fe 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -18,6 +18,7 @@ contract BranchPort is Ownable, IBranchPort {

     /// @notice Local Core Branch Router Address.
     address public coreBranchRouterAddress;
+    uint8 internal _unlocked = 1;

     /*///////////////////////////////////////////////////////////////
                         BRIDGE AGENT STATE
@@ -417,7 +418,6 @@ contract BranchPort is Ownable, IBranchPort {
         _;
     }

-    uint256 internal _unlocked = 1;

     /// @notice Modifier for a simple re-entrancy check.
     modifier lock() {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L54

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L435

### Pack `hTokenFactoryAddress` and `_unlocked` into one storage slot to save 1 SLOT (~2000 gas)

*Note: `_unlocked` is a mutex, thus we can reduce its `uint` size to `uint8`.*
```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol
54:     address public hTokenFactoryAddress;

435:    uint256 internal _unlocked = 1;
```
```diff
diff --git a/src/ulysses-omnichain/CoreRootRouter.sol b/src/ulysses-omnichain/CoreRootRouter.sol
index 1397722..fa8cf84 100644
--- a/src/ulysses-omnichain/CoreRootRouter.sol
+++ b/src/ulysses-omnichain/CoreRootRouter.sol
@@ -53,6 +53,8 @@ contract CoreRootRouter is IRootRouter, Ownable {
     /// @notice Uni V3 Factory Address
     address public hTokenFactoryAddress;

+    uint8 internal _unlocked = 1;
+
     constructor(uint24 _rootChainId, address _wrappedNativeToken, address _rootPortAddress) {
         rootChainId = _rootChainId;
         wrappedNativeToken = WETH9(_wrappedNativeToken);
@@ -432,7 +434,6 @@ contract CoreRootRouter is IRootRouter, Ownable {
                             MODIFIERS
     ///////////////////////////////////////////////////////////////*/

-    uint256 internal _unlocked = 1;

     /// @notice Modifier for a simple re-entrancy check.
     modifier lock() {
```

## Structs can be packed into fewer storage slots
The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if values combined are <= 32 bytes). If the variables packed together are retrieved together in functions (more likely with structs) we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a `Gwarmaccess (100 gas)` versus a `Gcoldsload (2100 gas)`.

Total Instances: `2`

Estimated Gas Saved: `4 (slots) * 2000 = 8000`

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136

### Pack `eta`, `startBlock`, `endBlock`, `canceled`, and `executed` to save 3 SLOTs (~6000 gas)

*Note: `eta`, `startBlock`, and `endBlock` all hold timestamps or block numbers and therefore can safetly be of `uint` size `>= uint40`.*
```solidity
File: src/governance/GovernorBravoInterfaces.sol
105:    struct Proposal {
...
110:        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
111:        uint256 eta;
...
120:        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
121:        uint256 startBlock;
122:        /// @notice The block at which voting ends: votes must be cast prior to this block
123:        uint256 endBlock;
...
130:        /// @notice Flag marking whether the proposal has been canceled
131:        bool canceled;
132:        /// @notice Flag marking whether the proposal has been executed
133:        bool executed;
...
136    }
```
```diff
diff --git a/src/governance/GovernorBravoInterfaces.sol b/src/governance/GovernorBravoInterfaces.sol
index e89b6a4..5a5d862 100644
--- a/src/governance/GovernorBravoInterfaces.sol
+++ b/src/governance/GovernorBravoInterfaces.sol
@@ -107,8 +107,6 @@ contract GovernorBravoDelegateStorageV1 is GovernorBravoDelegatorStorage {
         uint256 id;
         /// @notice Creator of the proposal
         address proposer;
-        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
-        uint256 eta;
         /// @notice the ordered list of target addresses for calls to be made
         address[] targets;
         /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
@@ -117,16 +115,18 @@ contract GovernorBravoDelegateStorageV1 is GovernorBravoDelegatorStorage {
         string[] signatures;
         /// @notice The ordered list of calldata to be passed to each call
         bytes[] calldatas;
-        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
-        uint256 startBlock;
-        /// @notice The block at which voting ends: votes must be cast prior to this block
-        uint256 endBlock;
         /// @notice Current number of votes in favor of this proposal
         uint256 forVotes;
         /// @notice Current number of votes in opposition to this proposal
         uint256 againstVotes;
         /// @notice Current number of votes for abstaining for this proposal
         uint256 abstainVotes;
+        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
+        uint80 eta;
+        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
+        uint80 startBlock;
+        /// @notice The block at which voting ends: votes must be cast prior to this block
+        uint80 endBlock;
         /// @notice Flag marking whether the proposal has been canceled
         bool canceled;
         /// @notice Flag marking whether the proposal has been executed
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L16-L24

### Pack `depositedGas` and `owner` into one storage slot to save 1 SLOT (~2000 gas)

*Note: If within reason, we will need to reduce the `uint` type of `depositedGas` to `uint96` in order to pack it into one slot with the `owner` variable.*
```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
16:  struct Deposit {
17:      uint128 depositedGas;
18:      address owner;
19:      DepositStatus status;
20:      address[] hTokens;
21:      address[] tokens;
22:      uint256[] amounts;
23:      uint256[] deposits;
}
```
```diff
diff --git a/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol b/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
index 574d075..6340017 100644
--- a/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
@@ -14,7 +14,7 @@ enum DepositStatus {
 }

 struct Deposit {
-    uint128 depositedGas;
+    uint96 depositedGas;
     address owner;
     DepositStatus status;
     address[] hTokens;
@@ -399,3 +399,4 @@ interface IBranchBridgeAgent is IApp {
     error UnrecognizedCallerNotRouter();
     error UnrecognizedBridgeAgentExecutor();
 }
```

## Get rid of redundant storage variable
When you create a dynamic array in solidity the array already holds the array length. Creating an additional state variable to keep track of the length is unnecessary and incurs extra storage operations for the variable (SLOAD & SSTORE).

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L24-L26

*Gas Savings for `ERC20hTokenRootFactory.createToken`, obtained via protocol's tests: Avg 9451 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  870666  |  902897  |  877833 |    162   |
| After  |  865547  |  880678  |  868382 |    162   |

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
24:    ERC20hTokenRoot[] public hTokens;
25:
26:    uint256 public hTokensLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol b/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
index 59b410b..7216cb5 100644
--- a/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
+++ b/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
@@ -23,8 +23,6 @@ contract ERC20hTokenRootFactory is Ownable, IERC20hTokenRootFactory {

     ERC20hTokenRoot[] public hTokens;

-    uint256 public hTokensLenght;
-
     /**
      * @notice Constructor for ERC20 hToken Contract
      *     @param _localChainId Local Network Identifier.
@@ -64,7 +62,6 @@ contract ERC20hTokenRootFactory is Ownable, IERC20hTokenRootFactory {
             _symbol
         );
         hTokens.push(newToken);
-        hTokensLenght++;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L22-L25

*Gas Savings for `ERC20hTokenBranchFactory.createToken`, obtained via protocol's tests: Avg 5119 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  911691  |  934149  |  922056 |    13    |
| After  |  906572  |  929030  |  916937 |    13    |

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
22:    ERC20hTokenBranch[] public hTokens;
23:
24:    /// @notice Number of hTokens deployed in current chain.
25:    uint256 public hTokensLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol b/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
index e0ba37e..0824882 100644
--- a/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
+++ b/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
@@ -21,9 +21,6 @@ contract ERC20hTokenBranchFactory is Ownable, IERC20hTokenBranchFactory {
     /// @notice Local hTokens deployed in current chain.
     ERC20hTokenBranch[] public hTokens;

-    /// @notice Number of hTokens deployed in current chain.
-    uint256 public hTokensLenght;
-
     constructor(uint24 _localChainId, address _localPortAddress) {
         require(_localPortAddress != address(0), "Port address cannot be 0");

@@ -42,7 +39,6 @@ contract ERC20hTokenBranchFactory is Ownable, IERC20hTokenBranchFactory {
         );

         hTokens.push(newToken);
-        hTokensLenght++;

         localCoreRouterAddress = _coreRouter;

@@ -64,7 +60,6 @@ contract ERC20hTokenBranchFactory is Ownable, IERC20hTokenBranchFactory {
     {
         newToken = new ERC20hTokenBranch(_name, _symbol, localPortAddress);
         hTokens.push(newToken);
-        hTokensLenght++;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L74-L77

*Gas Savings for `BranchPort.addPortStrategy`, obtained via protocol's tests: Avg 11115 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  58297   |  111691  |  58297  |    2     |
| After  |  47182   |  89483   |  47182  |    2     |

```solidity
File: src/ulysses-omnichain/BranchPort.sol
74:    address[] public portStrategies;
75:
76:    /// @notice Number of Port Strategies deployed in current branch chain.
77:    uint256 public portStrategiesLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..7b910aa 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -73,9 +73,6 @@ contract BranchPort is Ownable, IBranchPort {
     /// @notice Port Strategy Addresses deployed in current branch chain.
     address[] public portStrategies;

-    /// @notice Number of Port Strategies deployed in current branch chain.
-    uint256 public portStrategiesLenght;
-
     /// @notice Mapping returns the amount of Strategy Token debt a given Port Startegy has.  Strategy => Token => uint256.
     mapping(address => mapping(address => uint256)) public getPortStrategyTokenDebt;

@@ -352,7 +349,6 @@ contract BranchPort is Ownable, IBranchPort {
     {
         if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
         portStrategies.push(_portStrategy);
-        portStrategiesLenght++;
         strategyDailyLimitAmount[_portStrategy][_token] = _dailyManagementLimit;
         isPortStrategy[_portStrategy][_token] = true;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L57-L60

*Gas Savings for `BranchPort.addStrategyToken`, obtained via protocol's tests: Avg 16644 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  112843  |  112843  |  85281  |    4     |
| After  |  90651   |  90651   |  68637  |    4     |

```solidity
File: src/ulysses-omnichain/BranchPort.sol
57:    address[] public strategyTokens;
58:
59:    /// @notice Number of Port Strategies deployed in current branch chain.
60:    uint256 public strategyTokensLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..4da3c29 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -56,9 +56,6 @@ contract BranchPort is Ownable, IBranchPort {
     /// @notice List of Tokens whitelisted for usage in Port Strategies.
     address[] public strategyTokens;

-    /// @notice Number of Port Strategies deployed in current branch chain.
-    uint256 public strategyTokensLenght;
-
     /// @notice Mapping returns a given token's total debt incurred by Port Strategies.
     mapping(address => uint256) public getStrategyTokenDebt;

@@ -331,7 +328,6 @@ contract BranchPort is Ownable, IBranchPort {
     function addStrategyToken(address _token, uint256 _minimumReservesRatio) external requiresCoreRouter {
         if (_minimumReservesRatio >= DIVISIONER) revert InvalidMinimumReservesRatio();
         strategyTokens.push(_token);
-        strategyTokensLenght++;
         getMinimumTokenReserveRatio[_token] = _minimumReservesRatio;
         isStrategyToken[_token] = true;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L30-L33

*Gas Savings for `BranchPort.addBridgeAgent`, obtained via protocol's tests: Avg 12703 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  89235   |  89235   | 70264   |    107   |
| After  |  67047   |  67047   | 57561   |    107   |

```solidity
File: src/ulysses-omnichain/BranchPort.sol
30:    address[] public bridgeAgents;
31:
32:    /// @notice Number of Branch Routers deployed in current chain.
33:    uint256 public bridgeAgentsLenght; // @audit: length already stored in array above
``` 
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..a5cc648 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -29,9 +29,6 @@ contract BranchPort is Ownable, IBranchPort {
     /// @notice Branch Routers deployed in branc chain.
     address[] public bridgeAgents;

-    /// @notice Number of Branch Routers deployed in current chain.
-    uint256 public bridgeAgentsLenght;
-
     /*///////////////////////////////////////////////////////////////
                     BRIDGE AGENT FACTORIES STATE
     //////////////////////////////////////////////////////////////*/
@@ -290,7 +287,6 @@ contract BranchPort is Ownable, IBranchPort {
     function addBridgeAgent(address _bridgeAgent) external requiresBridgeAgentFactory {
         isBridgeAgent[_bridgeAgent] = true;
         bridgeAgents.push(_bridgeAgent);
-        bridgeAgentsLenght++;
     }

     /*///////////////////////////////////////////////////////////////
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L43-L46

*Gas Savings for `BranchPort.addBridgeAgentFactory`, obtained via protocol's tests: Avg 5116 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  56155   |  56155   | 56155   |    3     |
| After  |  51039   |  51039   | 51039   |    3     |

```solidity
File: src/ulysses-omnichain/BranchPort.sol
43:    address[] public bridgeAgentFactories;
44:
45:    /// @notice Number of Branch Routers deployed in current chain.
46:    uint256 public bridgeAgentFactoriesLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..a498842 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -42,9 +42,6 @@ contract BranchPort is Ownable, IBranchPort {
     /// @notice Branch Routers deployed in branc chain.
     address[] public bridgeAgentFactories;

-    /// @notice Number of Branch Routers deployed in current chain.
-    uint256 public bridgeAgentFactoriesLenght;
-
     /*///////////////////////////////////////////////////////////////
                         PORT STRATEGY STATE
     //////////////////////////////////////////////////////////////*/
@@ -106,7 +103,6 @@ contract BranchPort is Ownable, IBranchPort {
         coreBranchRouterAddress = _coreBranchRouter;
         isBridgeAgentFactory[_bridgeAgentFactory] = true;
         bridgeAgentFactories.push(_bridgeAgentFactory);
-        bridgeAgentFactoriesLenght++;
     }

     /// @notice Function being overrriden to prevent mistakenly renouncing ownership.
@@ -308,7 +304,6 @@ contract BranchPort is Ownable, IBranchPort {
     function addBridgeAgentFactory(address _newBridgeAgentFactory) external requiresCoreRouter {
         isBridgeAgentFactory[_newBridgeAgentFactory] = true;
         bridgeAgentFactories.push(_newBridgeAgentFactory);
-        bridgeAgentFactoriesLenght++;

         emit BridgeAgentFactoryAdded(_newBridgeAgentFactory);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L64-L67

*Gas Savings for `RootPort.addBridgeAgent`, obtained via protocol's tests: Avg 10922 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  81079   |  113279  | 90786   |    125   |
| After  |  75963   |  91063   | 79864   |    125   |

```solidity
File: src/ulysses-omnichain/RootPort.sol
64:    address[] public bridgeAgents;
65:
66:    /// @notice Number of bridgeAgents deployed in current chain.
67:    uint256 public bridgeAgentsLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/RootPort.sol b/src/ulysses-omnichain/RootPort.sol
index 80d2bea..66a365f 100644
--- a/src/ulysses-omnichain/RootPort.sol
+++ b/src/ulysses-omnichain/RootPort.sol
@@ -63,9 +63,6 @@ contract RootPort is Ownable, IRootPort {
     /// @notice Bridge Agents deployed in root chain.
     address[] public bridgeAgents;

-    /// @notice Number of bridgeAgents deployed in current chain.
-    uint256 public bridgeAgentsLenght;
-
     /// @notice Mapping address Bridge Agent => address Bridge Agent Manager
     mapping(address => address) public getBridgeAgentManager;

@@ -367,7 +364,6 @@ contract RootPort is Ownable, IRootPort {
         if (isBridgeAgent[_bridgeAgent]) revert AlreadyAddedBridgeAgent();

         bridgeAgents.push(_bridgeAgent);
-        bridgeAgentsLenght++;
         getBridgeAgentManager[_bridgeAgent] = _manager;
         isBridgeAgent[_bridgeAgent] = !isBridgeAgent[_bridgeAgent];

@@ -404,7 +400,7 @@ contract RootPort is Ownable, IRootPort {

     /// @inheritdoc IRootPort
     function addBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {
-        bridgeAgentFactories[bridgeAgentsLenght++] = _bridgeAgentFactory;
+        bridgeAgentFactories[bridgeAgentFactoriesLenght++] = _bridgeAgentFactory;

         emit BridgeAgentFactoryAdded(_bridgeAgentFactory);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L80-L83

*Gas Savings for `RootPort.initialize`, obtained via protocol's tests: Avg 22215 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  111716  |  111716  | 111716  |    59    |
| After  |  89501   |  89501   | 89501   |    59    |

```solidity
File: src/ulysses-omnichain/RootPort.sol
80:    address[] public bridgeAgentFactories;
81:
82:    /// @notice Number of Bridge Agents deployed in current chain.
83:    uint256 public bridgeAgentFactoriesLenght; // @audit: length already stored in array above
```
```diff
diff --git a/src/ulysses-omnichain/RootPort.sol b/src/ulysses-omnichain/RootPort.sol
index 80d2bea..b6714ca 100644
--- a/src/ulysses-omnichain/RootPort.sol
+++ b/src/ulysses-omnichain/RootPort.sol
@@ -79,9 +79,6 @@ contract RootPort is Ownable, IRootPort {
     /// @notice Bridge Agents deployed in root chain.
     address[] public bridgeAgentFactories;

-    /// @notice Number of Bridge Agents deployed in current chain.
-    uint256 public bridgeAgentFactoriesLenght;
-
     /*///////////////////////////////////////////////////////////////
                             hTOKENS
     //////////////////////////////////////////////////////////////*/
@@ -132,7 +129,6 @@ contract RootPort is Ownable, IRootPort {

         isBridgeAgentFactory[_bridgeAgentFactory] = true;
         bridgeAgentFactories.push(_bridgeAgentFactory);
-        bridgeAgentFactoriesLenght++;

         coreRootRouterAddress = _coreRootRouter;
     }
@@ -404,7 +400,7 @@ contract RootPort is Ownable, IRootPort {

     /// @inheritdoc IRootPort
     function addBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {
-        bridgeAgentFactories[bridgeAgentsLenght++] = _bridgeAgentFactory;
+        bridgeAgentFactories.push(_bridgeAgentFactory);

         emit BridgeAgentFactoryAdded(_bridgeAgentFactory);
     }
```

## Cache state variables outside of loop to avoid reading/writing storage on every iteration
Reading from storage should always try to be avoided within loops. In the following instances, we are able to cache state variables outside of the loop to save a `Gwarmaccess (100 gas)` per loop iteration.

Total Instances: `6`

Estimated Gas Saved: `990` (*This will be more if there are > 1 loop iterations for each instance*)

**Note: These are instances missed by in the bot race**.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L110-L113

### Cache `totalSupply` and `totalWeights` outside of the loop to save 2 SLOADs per loop iteration
```solidity
File: src/ulysses-amm/UlyssesToken.sol
110:    function updateAssetBalances() internal {
111:        for (uint256 i = 0; i < assets.length; i++) {
112:            uint256 assetBalance = assets[i].balanceOf(address(this));
113:            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights); // @audit: 2 sload on every iteration
```
```diff
diff --git a/src/ulysses-amm/UlyssesToken.sol b/src/ulysses-amm/UlyssesToken.sol
index 552a125..38fabcb 100644
--- a/src/ulysses-amm/UlyssesToken.sol
+++ b/src/ulysses-amm/UlyssesToken.sol
@@ -108,9 +108,11 @@ contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {
      * @notice Update the balances of the underlying assets.
      */
     function updateAssetBalances() internal {
+        uint256 _totalSupply = totalSupply;
+        uint256 _totalWeights = totalWeights;
         for (uint256 i = 0; i < assets.length; i++) {
             uint256 assetBalance = assets[i].balanceOf(address(this));
-            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
+            uint256 newAssetBalance = _totalSupply.mulDivUp(weights[i], _totalWeights);

             if (assetBalance > newAssetBalance) {
                 assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L83-L88

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L99-L100

### Cache `poolId` outside of loop and write to `poolId` after loop using the cached value to save 1 SLOAD & 1 SSTORE per loop iteration
```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol
83:    function _createPool(ERC20 asset, address owner) private returns (uint256 _poolId) {
84:        if (address(asset) == address(0)) revert InvalidAsset();
85:        _poolId = ++poolId; // @audit: sload + sstore
86:        pools[_poolId] =
87:            UlyssesPoolDeployer.deployPool(_poolId, address(asset), "Ulysses Pool", "ULP", owner, address(this));
88:    }

99:         for (uint256 i = 0; i < length;) {
100:            poolIds[i] = _createPool(assets[i], address(this)); // @audit: reads and writes to `poolId` slot on every iteration
```
```diff
diff --git a/src/ulysses-amm/factories/UlyssesFactory.sol b/src/ulysses-amm/factories/UlyssesFactory.sol
index 6770eda..c3c4478 100644
--- a/src/ulysses-amm/factories/UlyssesFactory.sol
+++ b/src/ulysses-amm/factories/UlyssesFactory.sol
@@ -95,14 +95,21 @@ contract UlyssesFactory is Ownable, IUlyssesFactory {
         uint256 length = assets.length;

         if (length != weights.length) revert ParameterLengthError();
-
+
+        uint256 _poolId = poolId;
         for (uint256 i = 0; i < length;) {
-            poolIds[i] = _createPool(assets[i], address(this));
+            if (address(assets[i]) == address(0)) revert InvalidAsset();
+            _poolId = ++_poolId;
+            pools[_poolId] =
+                UlyssesPoolDeployer.deployPool(_poolId, address(assets[i]), "Ulysses Pool", "ULP", owner, address(this));
+
+            poolIds[i] = _poolId;

             unchecked {
                 ++i;
             }
         }
+        poolId = _poolId;

         for (uint256 i = 0; i < length;) {
             if (length != weights[i].length) revert ParameterLengthError();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L107-L111

### Cache `pools[poolIds[i]]` outside of the inner loop to save 1 SLOAD per loop iteration for the inner loop
```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol
107:        for (uint256 i = 0; i < length;) {
108:            if (length != weights[i].length) revert ParameterLengthError();
109:
110:            for (uint256 j = 0; j < length;) {
111:                if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]); // audit: sload for `pools[poolIds[i]]` on each iteration
```
```diff
diff --git a/src/ulysses-amm/factories/UlyssesFactory.sol b/src/ulysses-amm/factories/UlyssesFactory.sol
index 6770eda..7867c94 100644
--- a/src/ulysses-amm/factories/UlyssesFactory.sol
+++ b/src/ulysses-amm/factories/UlyssesFactory.sol
@@ -106,9 +106,10 @@ contract UlyssesFactory is Ownable, IUlyssesFactory {

         for (uint256 i = 0; i < length;) {
             if (length != weights[i].length) revert ParameterLengthError();
-
+
+            UlyssesPool _ulyssesPool = pools[poolIds[i]];
             for (uint256 j = 0; j < length;) {
-                if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);
+                if (j != i && weights[i][j] > 0) _ulyssesPool.addNewBandwidth(poolIds[j], weights[i][j]);

                 unchecked {
                     ++j;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L251-L255

### Cache `timelock` and `proposal.eta` outside of the loop to save 2 SLOADs per iteration
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
251:        for (uint256 i = 0; i < proposal.targets.length; i++) {
252:            timelock.cancelTransaction( // @audit: `timelock` read on every iteration
253:                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta // @audit: `proposal.eta` read on every iteration
254:            );
255:        }
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..c5ac930 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -248,9 +248,11 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         }

         proposal.canceled = true;
+        TimelockInterface _timelock = timelock;
+        uint256 _eta = proposal.eta;
         for (uint256 i = 0; i < proposal.targets.length; i++) {
-            timelock.cancelTransaction(
-                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta
+            _timelock.cancelTransaction(
+                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], _eta
             );
         }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L215-L218

### Cache `timelock` and `proposal.eta` outside of the loop to save 2 SLOADs per iteration
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
215:        for (uint256 i = 0; i < proposal.targets.length; i++) {
216:            timelock.executeTransaction{value: proposal.values[i]}( // @audit: `timelock` read on every iteration
217:                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta // @audit: `proposal.eta` read on every iteration
218:            );
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..9eee09e 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -212,9 +212,11 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         );
         Proposal storage proposal = proposals[proposalId];
         proposal.executed = true;
+        TimelockInterface _timelock = timelock;
+        uint256 _eta = proposal.eta;
         for (uint256 i = 0; i < proposal.targets.length; i++) {
-            timelock.executeTransaction{value: proposal.values[i]}(
-                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta
+            _timelock.executeTransaction{value: proposal.values[i]}(
+                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], _eta
             );
         }
         emit ProposalExecuted(proposalId);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L596-L605

*Gas Savings for `RootBridgeAgent.redeemSettlement`, obtained via protocol's tests: Avg 90 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  30582   |  30582   |  30582  |    1     |
| After  |  30492   |  30492   |  30492  |    1     |

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
596:        for (uint256 i = 0; i < settlement.hTokens.length;) {
597:            //Check if asset
598:            if (settlement.hTokens[i] != address(0)) {
599:                //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
600:                IPort(localPortAddress).bridgeToRoot(
601:                    msg.sender,
602:                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain), // @audit: 1st sload + on every iteration
603:                    settlement.amounts[i],
604:                    settlement.deposits[i],
605:                    settlement.toChain // @audit: 2nd sload + on every iteration
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..dca0e9f 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -593,16 +593,17 @@ contract RootBridgeAgent is IRootBridgeAgent {
         Settlement storage settlement = getSettlement[_settlementNonce];

         //Clear Global hTokens To Recipient on Root Chain cancelling Settlement to Branch
+        uint24 _toChain = settlement.toChain;
         for (uint256 i = 0; i < settlement.hTokens.length;) {
             //Check if asset
             if (settlement.hTokens[i] != address(0)) {
                 //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
                 IPort(localPortAddress).bridgeToRoot(
                     msg.sender,
-                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain),
+                    IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], _toChain),
                     settlement.amounts[i],
                     settlement.deposits[i],
-                    settlement.toChain
+                    _toChain
                 );
             }
```

## Avoid emitting storage variables
In the instances below we can emit calldata/stack values instead of emitting storage values. This will result in using a cheap `CALLDATALOAD`/`DUP` instead of an expensive `SLOAD`.

Total Instances: `6`

Estimated Gas Saved: `6 * 100 = 600`

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L155-L160

### Emit `_tokenId` to save 1 SLOAD
```solidity
File: src/talos/base/TalosBaseStrategy.sol
155:        tokenId = _tokenId;
156:
157:        _mint(receiver, shares);
158:        if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
159:
160:        emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares); // @audit: unnecessary sload, use `_tokenId`
```
```diff
diff --git a/src/talos/base/TalosBaseStrategy.sol b/src/talos/base/TalosBaseStrategy.sol
index 8cb5a4d..3fb8dd6 100644
--- a/src/talos/base/TalosBaseStrategy.sol
+++ b/src/talos/base/TalosBaseStrategy.sol
@@ -157,7 +157,7 @@ abstract contract TalosBaseStrategy is Ownable, ERC20, ReentrancyGuard, ITalosBa
         _mint(receiver, shares);
         if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();

-        emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares);
+        emit Initialize(_tokenId, msg.sender, receiver, amount0, amount1, shares);

         afterDeposit(_tokenId);
         initialized = true;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L41-L50

### Emit `implementation_` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegator.sol
41:    function _setImplementation(address implementation_) public {
...
47:        address oldImplementation = implementation; // @audit: 1st sload
48:        implementation = implementation_;
49:
50:        emit NewImplementation(oldImplementation, implementation); // @audit: 2nd sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegator.sol b/src/governance/GovernorBravoDelegator.sol
index 742d396..3c51e03 100644
--- a/src/governance/GovernorBravoDelegator.sol
+++ b/src/governance/GovernorBravoDelegator.sol
@@ -47,7 +47,7 @@ contract GovernorBravoDelegator is GovernorBravoDelegatorStorage, GovernorBravoE
         address oldImplementation = implementation;
         implementation = implementation_;

-        emit NewImplementation(oldImplementation, implementation);
+        emit NewImplementation(oldImplementation, implementation_);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L404-L406

### Emit `newVotingDelay` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
404:        votingDelay = newVotingDelay;
401:
402:        emit VotingDelaySet(oldVotingDelay, votingDelay); // @audit: unecessary sload. Emit `newVotingDelay`
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..06c7412 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -403,7 +403,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         uint256 oldVotingDelay = votingDelay;
         votingDelay = newVotingDelay;

-        emit VotingDelaySet(oldVotingDelay, votingDelay);
+        emit VotingDelaySet(oldVotingDelay, newVotingDelay);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L420-L422

### Emit `newVotingDelay` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
420:        votingPeriod = newVotingPeriod;
421:
422:        emit VotingPeriodSet(oldVotingPeriod, votingPeriod); // @audit: unnecessary sload. Emit `newVotingPeriod`
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..433839f 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -419,7 +419,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         uint256 oldVotingPeriod = votingPeriod;
         votingPeriod = newVotingPeriod;

-        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
+        emit VotingPeriodSet(oldVotingPeriod, newVotingPeriod);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L437-L439

### Emit `newProposalThreshold` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
437:        proposalThreshold = newProposalThreshold;
438:
439:        emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold); // @audit: unnecessary sload. Emit `newProposalThreshold`
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..3c6eb0b 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -436,7 +436,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         uint256 oldProposalThreshold = proposalThreshold;
         proposalThreshold = newProposalThreshold;

-        emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold);
+        emit ProposalThresholdSet(oldProposalThreshold, newProposalThreshold);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L464-L466

### Emit `account` to save 1 SLOAD
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
464:        whitelistGuardian = account;
465:
466:        emit WhitelistGuardianSet(oldGuardian, whitelistGuardian); // @audit: unnecessary sload. Emit `account`
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..0843064 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -463,7 +463,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         address oldGuardian = whitelistGuardian;
         whitelistGuardian = account;

-        emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);
+        emit WhitelistGuardianSet(oldGuardian, account);
     }
```

## Refactor code to save 1 SLOAD
In the instances below, we are able to slightly modify the code in order to prevent us from performing an extra SLOAD.

Total Instances: `2`

Estimated Gas Saved: `521`

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L128-L137

*Gas Savings for `FlywheelGaugeRewards.queueRewardsForCyclePaginated`, obtained via protocol's tests: Avg 393 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  61742   |  132064  | 78774   |    9     |
| After  |  61578   |  131605  | 78381   |    9     |

### Cache updated value for `nextCycleQueuedRewards` to save 1 SLOAD
```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
128:        if (offset == 0) {
129:            // queue the rewards stream and sanity check the tokens were received
130:            uint256 balanceBefore = rewardToken.balanceOf(address(this));
131:            uint256 newRewards = minter.getRewards();
132:            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);
133:            require(newRewards <= type(uint112).max); // safe cast
134:            nextCycleQueuedRewards += uint112(newRewards); // in case a previous incomplete cycle had rewards, add on // @audit: 1st sload
135:        }
136:
137:        uint112 queued = nextCycleQueuedRewards; // @audit: 2nd sload
```
```diff
diff --git a/src/rewards/rewards/FlywheelGaugeRewards.sol b/src/rewards/rewards/FlywheelGaugeRewards.sol
index a33e81a..0daf720 100644
--- a/src/rewards/rewards/FlywheelGaugeRewards.sol
+++ b/src/rewards/rewards/FlywheelGaugeRewards.sol
@@ -125,16 +125,18 @@ contract FlywheelGaugeRewards is Ownable, IFlywheelGaugeRewards {
         uint32 offset = paginationOffset;

         // important to only calculate the reward amount once to prevent each page from having a different reward amount
+        uint112 _nextCycleQueuedRewards = nextCycleQueuedRewards;
         if (offset == 0) {
             // queue the rewards stream and sanity check the tokens were received
             uint256 balanceBefore = rewardToken.balanceOf(address(this));
             uint256 newRewards = minter.getRewards();
             require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);
             require(newRewards <= type(uint112).max); // safe cast
-            nextCycleQueuedRewards += uint112(newRewards); // in case a previous incomplete cycle had rewards, add on
+            _nextCycleQueuedRewards += uint112(newRewards); // in case a previous incomplete cycle had rewards, add on
+            nextCycleQueuedRewards = _nextCycleQueuedRewards;
         }

-        uint112 queued = nextCycleQueuedRewards;
+        uint112 queued = _nextCycleQueuedRewards;

         uint256 remaining = gaugeToken.numGauges() - offset;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L262-L266

*Gas Savings for `UniswapV3Staker.claimReward`, obtained via protocol's tests: Avg 128 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  27684   |  30484   | 28059   |    41    |
| After  |  27553   |  30353   | 27931   |    41    |

### Update `rewards[msg.sender]` first to save 1 SLOAD
```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol
262:    function claimReward(address to, uint256 amountRequested) external returns (uint256 reward) {
263:        reward = rewards[msg.sender]; // @audit: 1st sload
264:        if (amountRequested != 0 && amountRequested < reward) {
265:            reward = amountRequested;
266:            rewards[msg.sender] -= reward; // @audit: 2nd sload
```
```diff
diff --git a/src/uni-v3-staker/UniswapV3Staker.sol b/src/uni-v3-staker/UniswapV3Staker.sol
index 5970379..7b96f52 100644
--- a/src/uni-v3-staker/UniswapV3Staker.sol
+++ b/src/uni-v3-staker/UniswapV3Staker.sol
@@ -262,8 +262,8 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {
     function claimReward(address to, uint256 amountRequested) external returns (uint256 reward) {
         reward = rewards[msg.sender];
         if (amountRequested != 0 && amountRequested < reward) {
+            rewards[msg.sender] = reward - amountRequested;
             reward = amountRequested;
-            rewards[msg.sender] -= reward;
         } else {
             rewards[msg.sender] = 0;
         }
```

## Refactor functions to avoid unnecessary SLOADs and SSTOREs 
The functions below read storage slots that are previously read in the functions that invoke them. We can refactor the functions so we could pass cached storage variables as stack variables and avoid the extra storage reads that would otherwise take place in the internal functions. Two methods are used when refactoring these instances: In some instances the logic from invoked internal/public functions are inlined so as to more accurately illustrate the optimization. In other instances the invoked internal/public functions are refactored to take in the cached state variables.

Total Instances: `20`

Estimated Gas Saved: `3649`

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L44-L56

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L60-L82

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L88-L104

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L110-L113

### Cache `assets.length` and `totalWeights` and pass cached values into `updateAssetBalances` to save 2 SLOADs for `addAsset`, 2 SLOADs for `removeAsset`, and 3 SLOADs for `setWeights`.
```solidity
File: src/ulysses-amm/UlyssesToken.sol
44:    function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {
...
49:        assetId[asset] = assets.length + 1; // @audit: 1st sload
50:        assets.push(asset);
51:        weights.push(_weight);
52:        totalWeights += _weight; // @audit: 1st sload
...
56:        updateAssetBalances(); // @audit: 2nd sloads

60:    function removeAsset(address asset) external nonReentrant onlyOwner {
61:        // No need to check if index is 0, it will underflow and revert if it is 0
62:        uint256 assetIndex = assetId[asset] - 1;
63:
64:        uint256 newAssetsLength = assets.length - 1; // @audit: 1st sload
...
68:        totalWeights -= weights[assetIndex]; // @audit: 1st sload
...
82:        updateAssetBalances(); // @audit: 2nd sloads

88:    function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {
89:        if (_weights.length != assets.length) revert InvalidWeightsLength(); // @audit: 1st sload
...
95:        for (uint256 i = 0; i < assets.length; i++) { // @audit: 2nd sload
96:            newTotalWeights += _weights[i];
97:
98:            emit AssetRemoved(assets[i]);
99:            emit AssetAdded(assets[i], _weights[i]);
100:        }
101:
102:        totalWeights = newTotalWeights; // @audit: new `totalWeights` value. Can use this in internal function below
103:
104:        updateAssetBalances(); // @audit: 3rd sload for `assets.length` and 1st for `totalWeights`
 
110:    function updateAssetBalances() internal {
111:        for (uint256 i = 0; i < assets.length; i++) {
112:            uint256 assetBalance = assets[i].balanceOf(address(this));
113:            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
```
```diff
diff --git a/src/ulysses-amm/UlyssesToken.sol b/src/ulysses-amm/UlyssesToken.sol
index 552a125..270f91d 100644
--- a/src/ulysses-amm/UlyssesToken.sol
+++ b/src/ulysses-amm/UlyssesToken.sol
@@ -45,27 +45,31 @@ contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {
         if (assetId[asset] != 0) revert AssetAlreadyAdded();
         require(ERC20(asset).decimals() == 18);
         require(_weight > 0);
-
-        assetId[asset] = assets.length + 1;
+
+        uint256 _assetsLength = assets.length;
+        assetId[asset] = _assetsLength + 1;
         assets.push(asset);
         weights.push(_weight);
-        totalWeights += _weight;
+        uint256 _totalWeights = totalWeights + _weight;
+        totalWeights = _totalWeights;

         emit AssetAdded(asset, _weight);

-        updateAssetBalances();
+        updateAssetBalances(_totalWeights, _assetsLength);
     }

     ///@inheritdoc IUlyssesToken
     function removeAsset(address asset) external nonReentrant onlyOwner {
         // No need to check if index is 0, it will underflow and revert if it is 0
         uint256 assetIndex = assetId[asset] - 1;
-
-        uint256 newAssetsLength = assets.length - 1;
+
+        uint256 _assetsLength = assets.length;
+        uint256 newAssetsLength = _assetsLength - 1;

         if (newAssetsLength == 0) revert CannotRemoveLastAsset();
-
-        totalWeights -= weights[assetIndex];
+
+        uint256 _totalWeights = totalWeights - weights[assetIndex];
+        totalWeights = _totalWeights;

         address lastAsset = assets[newAssetsLength];

@@ -79,20 +83,21 @@ contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {

         emit AssetRemoved(asset);

-        updateAssetBalances();
+        updateAssetBalances(_totalWeights, _assetsLength);

         asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));
     }

     ///@inheritdoc IUlyssesToken
     function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {
-        if (_weights.length != assets.length) revert InvalidWeightsLength();
+        uint256 _assetsLength = assets.length;
+        if (_weights.length != _assetsLength) revert InvalidWeightsLength();

         weights = _weights;

         uint256 newTotalWeights;

-        for (uint256 i = 0; i < assets.length; i++) {
+        for (uint256 i = 0; i < _assetsLength; i++) {
             newTotalWeights += _weights[i];

             emit AssetRemoved(assets[i]);
@@ -101,16 +106,16 @@ contract UlyssesToken is ERC4626MultiToken, Ownable, IUlyssesToken {

         totalWeights = newTotalWeights;

-        updateAssetBalances();
+        updateAssetBalances(newTotalWeights, _assetsLength);
     }

     /**
      * @notice Update the balances of the underlying assets.
      */
-    function updateAssetBalances() internal {
-        for (uint256 i = 0; i < assets.length; i++) {
+    function updateAssetBalances(uint256 _totalWeights, uint256 _assetsLength) internal {
+        for (uint256 i = 0; i < _assetsLength; i++) {
             uint256 assetBalance = assets[i].balanceOf(address(this));
-            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
+            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], _totalWeights);

             if (assetBalance > newAssetBalance) {
                 assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L137-L139

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L149-L151

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L172-L183

### Cache `getStrategyTokenDebt[_token]` and pass cached value into `_reservesLacking` and `_minimumReserves` to save 1 SLOAD
```solidity
File:
137:    function _reservesLacking(address _token) internal view returns (uint256) {
138:        uint256 currBalance = ERC20(_token).balanceOf(address(this));
139:        uint256 minReserves = _minimumReserves(currBalance, _token);

149:    function _minimumReserves(uint256 _currBalance, address _token) internal view returns (uint256) {
150:        return ((_currBalance + getStrategyTokenDebt[_token]) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
151:    }

172:    function replenishReserves(address _strategy, address _token, uint256 _amount) external lock {
173:        if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
174:        if (!isPortStrategy[_strategy][_token]) revert UnrecognizedPortStrategy();
175:
176:        uint256 reservesLacking = _reservesLacking(_token);
177:
178:        uint256 amountToWithdraw = _amount < reservesLacking ? _amount : reservesLacking;
179:
180:        IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);
181:
182:        getPortStrategyTokenDebt[_strategy][_token] -= amountToWithdraw;
183:        getStrategyTokenDebt[_token] -= amountToWithdraw;
```
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..996d4f0 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -125,7 +125,7 @@ contract BranchPort is Ownable, IBranchPort {
      */
     function _excessReserves(address _token) internal view returns (uint256) {
         uint256 currBalance = ERC20(_token).balanceOf(address(this));
-        uint256 minReserves = _minimumReserves(currBalance, _token);
+        uint256 minReserves = _minimumReserves(getStrategyTokenDebt[_token], currBalance, _token);
         return currBalance > minReserves ? currBalance - minReserves : 0;
     }

@@ -134,9 +134,9 @@ contract BranchPort is Ownable, IBranchPort {
      *   @param _token Address of a given Strategy Token.
      *   @return uint256 excess reserves
      */
-    function _reservesLacking(address _token) internal view returns (uint256) {
+    function _reservesLacking(uint256 _strategyTokenDebt, address _token) internal view returns (uint256) {
         uint256 currBalance = ERC20(_token).balanceOf(address(this));
-        uint256 minReserves = _minimumReserves(currBalance, _token);
+        uint256 minReserves = _minimumReserves(_strategyTokenDebt, currBalance, _token);
         return currBalance < minReserves ? minReserves - currBalance : 0;
     }

@@ -146,8 +146,8 @@ contract BranchPort is Ownable, IBranchPort {
      *   @param _token Address of a given Strategy Token.
      *   @return uint256 minimum reserves
      */
-    function _minimumReserves(uint256 _currBalance, address _token) internal view returns (uint256) {
-        return ((_currBalance + getStrategyTokenDebt[_token]) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
+    function _minimumReserves(uint256 _strategyTokenDebt, uint256 _currBalance, address _token) internal view returns (uint256) {
+        return ((_currBalance + _strategyTokenDebt) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
     }

     /*///////////////////////////////////////////////////////////////
@@ -172,15 +172,16 @@ contract BranchPort is Ownable, IBranchPort {
     function replenishReserves(address _strategy, address _token, uint256 _amount) external lock {
         if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
         if (!isPortStrategy[_strategy][_token]) revert UnrecognizedPortStrategy();
-
-        uint256 reservesLacking = _reservesLacking(_token);
+
+        uint256 strategyTokenDebt = getStrategyTokenDebt[_token];
+        uint256 reservesLacking = _reservesLacking(strategyTokenDebt, _token);

         uint256 amountToWithdraw = _amount < reservesLacking ? _amount : reservesLacking;

         IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

         getPortStrategyTokenDebt[_strategy][_token] -= amountToWithdraw;
-        getStrategyTokenDebt[_token] -= amountToWithdraw;
+        getStrategyTokenDebt[_token] = strategyTokenDebt - amountToWithdraw;

         emit DebtRepaid(_strategy, _token, amountToWithdraw);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L126-L128

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L149-L163

### Cache `getStrategyTokenDebt[_token]` and pass cached value into `_excessReserves` and `_minimumReserves` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchPort.sol
126:    function _excessReserves(address _token) internal view returns (uint256) {
127:        uint256 currBalance = ERC20(_token).balanceOf(address(this));
128:        uint256 minReserves = _minimumReserves(currBalance, _token); // @audit: reads `getStrategyTokenDebt[_token])`

149:    function _minimumReserves(uint256 _currBalance, address _token) internal view returns (uint256) {
150:        return ((_currBalance + getStrategyTokenDebt[_token]) * getMinimumTokenReserveRatio[_token]) / DIVISIONER; // @audit: 1st sload
151:    }
...
158:    function manage(address _token, uint256 _amount) external requiresPortStrategy(_token) {
159:        if (_amount > _excessReserves(_token)) revert InsufficientReserves(); // @audit: Invokes function that reads `getStrategyTokenDebt[_token]`
160:
161:        _checkTimeLimit(_token, _amount);
162:
163:        getStrategyTokenDebt[_token] += _amount; // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchPort.sol b/src/ulysses-omnichain/BranchPort.sol
index b1bd210..8cc6aa5 100644
--- a/src/ulysses-omnichain/BranchPort.sol
+++ b/src/ulysses-omnichain/BranchPort.sol
@@ -123,9 +123,9 @@ contract BranchPort is Ownable, IBranchPort {
      *   @param _token Address of a given Strategy Token.
      *   @return uint256 excess reserves
      */
-    function _excessReserves(address _token) internal view returns (uint256) {
+    function _excessReserves(uint256 _strategyTokenDebt, address _token) internal view returns (uint256) {
         uint256 currBalance = ERC20(_token).balanceOf(address(this));
-        uint256 minReserves = _minimumReserves(currBalance, _token);
+        uint256 minReserves = _minimumReserves(_strategyTokenDebt, currBalance, _token);
         return currBalance > minReserves ? currBalance - minReserves : 0;
     }

@@ -136,7 +136,7 @@ contract BranchPort is Ownable, IBranchPort {
      */
     function _reservesLacking(address _token) internal view returns (uint256) {
         uint256 currBalance = ERC20(_token).balanceOf(address(this));
-        uint256 minReserves = _minimumReserves(currBalance, _token);
+        uint256 minReserves = _minimumReserves(getStrategyTokenDebt[_token], currBalance, _token);
         return currBalance < minReserves ? minReserves - currBalance : 0;
     }

@@ -146,8 +146,8 @@ contract BranchPort is Ownable, IBranchPort {
      *   @param _token Address of a given Strategy Token.
      *   @return uint256 minimum reserves
      */
-    function _minimumReserves(uint256 _currBalance, address _token) internal view returns (uint256) {
-        return ((_currBalance + getStrategyTokenDebt[_token]) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
+    function _minimumReserves(uint256 _strategyTokenDebt, uint256 _currBalance, address _token) internal view returns (uint256) {
+        return ((_currBalance + _strategyTokenDebt) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
     }

     /*///////////////////////////////////////////////////////////////
@@ -156,11 +156,12 @@ contract BranchPort is Ownable, IBranchPort {

     /// @inheritdoc IBranchPort
     function manage(address _token, uint256 _amount) external requiresPortStrategy(_token) {
-        if (_amount > _excessReserves(_token)) revert InsufficientReserves();
+        uint256 _strategyTokenDebt = getStrategyTokenDebt[_token];
+        if (_amount > _excessReserves(_strategyTokenDebt, _token)) revert InsufficientReserves();

         _checkTimeLimit(_token, _amount);

-        getStrategyTokenDebt[_token] += _amount;
+        getStrategyTokenDebt[_token] = _strategyTokenDebt + _amount;
         getPortStrategyTokenDebt[msg.sender][_token] += _amount;

         _token.safeTransfer(msg.sender, _amount);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L276-L296

*Gas Savings for `RootPort.bridgeToRoot`, obtained via protocol's tests: Avg 242 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  25355   |  71155   | 33920   |    167   |
| After  |  25102   |  70902   | 33678   |    167   |

### Call `ERC20hTokenRoot(_hToken).mint` directly in `bridgeToRoot` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootPort.sol
276:    function bridgeToRoot(address _recipient, address _hToken, uint256 _amount, uint256 _deposit, uint24 _fromChainId)
277:        external
278:        requiresBridgeAgent
279:    {
280:        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken(); // @audit: 1st sload
281:
282:        if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);
283:        if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId); // @audit: performs same check as above
284:    }
285:
...
293:    function mint(address _to, address _hToken, uint256 _amount, uint24 _fromChain) internal {
294:        if (!isGlobalAddress[_hToken]) revert UnrecognizedToken(); // @audit: 2nd sload
295:        ERC20hTokenRoot(_hToken).mint(_to, _amount, _fromChain);
296:    }
```
```diff
diff --git a/src/ulysses-omnichain/RootPort.sol b/src/ulysses-omnichain/RootPort.sol
index 80d2bea..187503e 100644
--- a/src/ulysses-omnichain/RootPort.sol
+++ b/src/ulysses-omnichain/RootPort.sol
@@ -280,7 +280,7 @@ contract RootPort is Ownable, IRootPort {
         if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();

         if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);
-        if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);
+        if (_deposit > 0) ERC20hTokenRoot(_hToken).mint(_recipient, _deposit, _fromChainId);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L114-L118

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L237-L246

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L87-L88

### Cache `govToken` and pass cached value into `getProposalThresholdAmount` to save 2 SLOADs
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
114:        require(
115:            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount() // @audit: 1st sload for `govToken` and public function reads `govToken` again
116:                || isWhitelisted(msg.sender),
117:            "GovernorBravo::propose: proposer votes below proposal threshold"
118:        );

237:                require(
238:                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()) // @audit: 1st sload and public function reads `govToken` again
239:                        && msg.sender == whitelistGuardian,
240:                    "GovernorBravo::cancel: whitelisted proposer"
241:                );
242:            } else {
243:                require(
244:                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()), @audit: 1st sload and public function reads `govToken` again
245:                    "GovernorBravo::cancel: proposer above threshold"
246:                );

87:    function getProposalThresholdAmount() public view returns (uint256) {
88:        return govToken.totalSupply() * proposalThreshold / DIVISIONER; // @audit: 2nd sload
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..a91168c 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -85,7 +85,11 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
     }

     function getProposalThresholdAmount() public view returns (uint256) {
-        return govToken.totalSupply() * proposalThreshold / DIVISIONER;
+        return _getProposalThresholdAmount(govToken);
+    }
+
+    function _getProposalThresholdAmount(GovTokenInterface _govToken) internal view returns (uint256) {
+        return _govToken.totalSupply() * proposalThreshold / DIVISIONER;
     }

     function getQuorumVotesAmount() public view returns (uint256) {
@@ -111,11 +115,14 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         // Reject proposals before initiating as Governor
         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");
         // Allow addresses above proposal threshold and whitelisted addresses to propose
-        require(
-            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
-                || isWhitelisted(msg.sender),
-            "GovernorBravo::propose: proposer votes below proposal threshold"
-        );
+        { // @audit: used to avoid `stack too deep` error
+            GovTokenInterface _govToken = govToken;
+            require(
+                _govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > _getProposalThresholdAmount(_govToken)
+                    || isWhitelisted(msg.sender),
+                "GovernorBravo::propose: proposer votes below proposal threshold"
+            );
+        }
         require(
             targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,
             "GovernorBravo::propose: proposal function information arity mismatch"
@@ -233,15 +240,16 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         // admin is Emergency DAO and can cancel any proposal
         if (msg.sender != proposal.proposer && msg.sender != admin) {
             // Whitelisted proposers can't be canceled for falling below proposal threshold
+            GovTokenInterface _govToken = govToken;
             if (isWhitelisted(proposal.proposer)) {
                 require(
-                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
+                    (_govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < _getProposalThresholdAmount(_govToken))
                         && msg.sender == whitelistGuardian,
                     "GovernorBravo::cancel: whitelisted proposer"
                 );
             } else {
                 require(
-                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
+                    (_govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < _getProposalThresholdAmount(_govToken)),
                     "GovernorBravo::cancel: proposer above threshold"
                 );
             }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L180-L185

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L190-L202

### Cache `timelock` and pass cached value into `queueOrRevertInternal` to save 2 SLOADs per iteration
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
180:        uint256 eta = add256(block.timestamp, timelock.delay()); // @audit: 1st sload
181:        for (uint256 i = 0; i < proposal.targets.length; i++) {
182:            queueOrRevertInternal( // @audit: function reads `timelock` twice`
183:                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], eta
184:            );
185:        }

190:    function queueOrRevertInternal(
...
197:        require(
198:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))), // @audit: 2nd sload + on every iteration
199:            "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
200:        );
201:        timelock.queueTransaction(target, value, signature, data, eta); // @audit: 3rd sload + on every iteration
202:    }
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..17d1a79 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -177,10 +177,11 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
             "GovernorBravo::queue: proposal can only be queued if it is succeeded"
         );
         Proposal storage proposal = proposals[proposalId];
-        uint256 eta = add256(block.timestamp, timelock.delay());
+        TimelockInterface _timelock = timelock;
+        uint256 eta = add256(block.timestamp, _timelock.delay());
         for (uint256 i = 0; i < proposal.targets.length; i++) {
             queueOrRevertInternal(
-                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], eta
+                _timelock, proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], eta
             );
         }
         proposal.eta = eta;
@@ -188,6 +189,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
     }

     function queueOrRevertInternal(
+        TimelockInterface _timelock,
         address target,
         uint256 value,
         string memory signature,
@@ -195,10 +197,10 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         uint256 eta
     ) internal {
         require(
-            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
+            !_timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
             "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
         );
-        timelock.queueTransaction(target, value, signature, data, eta);
+        _timelock.queueTransaction(target, value, signature, data, eta);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L359-L373

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L520-L531

*Gas Savings for `RootBridgeAgent.callOutAndBridgeMultiple`, obtained via protocol's tests: Avg 209 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  618480  |  628408  | 618201  |    4     |
| After  |  618271  |  628199  | 617992  |    4     |

### Cache `settlementNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
359:        bytes memory data = abi.encodePacked(
360:            bytes1(0x02),
361:            _recipient,
362:            uint8(hTokens.length),
363:            settlementNonce, // @audit: 1st sload
364:            hTokens,
365:            tokens,
366:            _amounts,
367:            _deposits,
368:            _data,
369:            _manageGasOut(_toChain)
370:        );
371:
372:        //Create Settlement Balance
373:        _createMultipleSettlement(_owner, _recipient, hTokens, tokens, _amounts, _deposits, data, _toChain); // @audit: updates `settlementNonce`

520:    function _createMultipleSettlement(
...
530:        // Update State
531:        getSettlement[_getAndIncrementSettlementNonce()] = Settlement({ // @audit: 2nd sload
```
```diff
diff --git a/./src/ulysses-omnichain/RootBridgeAgent.sol b/./src/ulysses-omnichain/RootBridgeAgentNew.sol
index 34f4286..417e45e 100644
--- a/./src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/./src/ulysses-omnichain/RootBridgeAgentNew.sol
@@ -356,11 +356,12 @@ contract RootBridgeAgent is IRootBridgeAgent {
         }

         //Prepare data for call with settlement of multiple assets
-        bytes memory data = abi.encodePacked(
+        uint32 _settlementNonce = settlementNonce;
+        _data = abi.encodePacked( // @audit: re-using `_data` memory array to fix `stack too deep` error
             bytes1(0x02),
             _recipient,
             uint8(hTokens.length),
-            settlementNonce,
+            _settlementNonce,
             hTokens,
             tokens,
             _amounts,
@@ -370,10 +371,23 @@ contract RootBridgeAgent is IRootBridgeAgent {
         );

         //Create Settlement Balance
-        _createMultipleSettlement(_owner, _recipient, hTokens, tokens, _amounts, _deposits, data, _toChain);
+        // Update State
+        getSettlement[_settlementNonce++] = Settlement({
+            owner: _owner,
+            recipient: _recipient,
+            hTokens: hTokens,
+            tokens: tokens,
+            amounts: _amounts,
+            deposits: _deposits,
+            callData: _data,
+            toChain: _toChain,
+            status: SettlementStatus.Success,
+            gasToBridgeOut: userFeeInfo.gasToBridgeOut
+        });
+        settlementNonce = _settlementNonce;

         //Perform Call to destination Branch Chain.
-        _performCall(data, _toChain);
+        _performCall(_data, _toChain);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L305-L324

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L483-L504

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L520-L531

*Gas Savings for `RootBridgeAgent.callOutAndBridge`, obtained via protocol's tests: Avg 172 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  484688  |  549370  | 489608  |    11    |
| After  |  484516  |  549155  | 489409  |    11    |

### Cache `settlementNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
305:        //Prepare data for call
306:        bytes memory data = abi.encodePacked(
307:            bytes1(0x01),
308:            _recipient,
309:            settlementNonce, // @audit: 1st sload
310:            localAddress,
311:            underlyingAddress,
312:            _amount,
313:            _deposit,
314:            _data,
315:            _manageGasOut(_toChain)
316:        );
317:
318:        //Update State to reflect bridgeOut
319:        _updateStateOnBridgeOut(
320:            msg.sender, _globalAddress, localAddress, underlyingAddress, _amount, _deposit, _toChain
321:        );
322:
323:        //Create Settlement
324:        _createSettlement(_owner, _recipient, localAddress, underlyingAddress, _amount, _deposit, data, _toChain); // @audit: invokes function that updates `settlementNonce`

483:    function _createSettlement(
...
503:        //Call createSettlement
504:        _createMultipleSettlement(_owner, _recipient, hTokens, tokens, amounts, deposits, _callData, _toChain); // @audit: updates `settlementNonce`

520:    function _createMultipleSettlement(
...
530:        // Update State
531:        getSettlement[_getAndIncrementSettlementNonce()] = Settlement({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..3c88ffa 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -303,10 +303,11 @@ contract RootBridgeAgent is IRootBridgeAgent {
         }

         //Prepare data for call
-        bytes memory data = abi.encodePacked(
+        uint32 _settlementNonce = settlementNonce;
+        _data = abi.encodePacked( // @audit: re-using `_data` memory array to fix `stack too deep` error
             bytes1(0x01),
             _recipient,
-            settlementNonce,
+            _settlementNonce,
             localAddress,
             underlyingAddress,
             _amount,
@@ -321,10 +322,33 @@ contract RootBridgeAgent is IRootBridgeAgent {
         );

         //Create Settlement
-        _createSettlement(_owner, _recipient, localAddress, underlyingAddress, _amount, _deposit, data, _toChain);
+        //Cast to Dynamic
+        address[] memory hTokens = new address[](1);
+        hTokens[0] = localAddress;
+        address[] memory tokens = new address[](1);
+        tokens[0] = underlyingAddress;
+        uint256[] memory amounts = new uint256[](1);
+        amounts[0] = _amount;
+        uint256[] memory deposits = new uint256[](1);
+        deposits[0] = _deposit;
+
+        // Update State
+        getSettlement[_settlementNonce++] = Settlement({
+            owner: _owner,
+            recipient: _recipient,
+            hTokens: hTokens,
+            tokens: tokens,
+            amounts: amounts,
+            deposits: deposits,
+            callData: _data,
+            toChain: _toChain,
+            status: SettlementStatus.Success,
+            gasToBridgeOut: userFeeInfo.gasToBridgeOut
+        });
+        settlementNonce = _settlementNonce;

         //Perform Call to clear hToken balance on destination branch chain and perform call.
-        _performCall(data, _toChain);
+        _performCall(_data, _toChain);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L802-L817

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1233-L1234

*Gas Savings for `RootBridgeAgent.anyExecute`, obtained via protocol's tests: Avg 110 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  218159  |  1528750 | 554930  |    187   |
| After  |  218049  |  1528750 | 554929  |    187   |

### Avoid re-reading `initialGas` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
802:        delete(initialGas); // @audit: `initialGas` set to 0
...
814:        if (minExecCost > availableGas) {
815:            _forceRevert(); // @audit: reads `initialGas` and reverts if 0. `initialGas` will be 0 at this point
816:            return;
817:        }

1233:    function _forceRevert() internal {
1234:        if (initialGas == 0) revert GasErrorOrRepeatedTx(); // @audit: reads `initialGas`
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..bb0d334 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -812,8 +812,7 @@ contract RootBridgeAgent is IRootBridgeAgent {

         //Check if sufficient balance
         if (minExecCost > availableGas) {
-            _forceRevert();
-            return;
+            revert GasErrorOrRepeatedTx();
         }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L244-L246

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L550-L558

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L743-L746

*Gas Savings for `RootBridgeAgent.retrySettlement`, obtained via protocol's tests: Avg 73 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  273852  |  273852  | 273852  |    1     |
| After  |  273779  |  273779  | 273779  |    1     |

### Refactor functions to take in cached `initialGas` value to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
244:    function retrySettlement(uint32 _settlementNonce, uint128 _remoteExecutionGas) external payable {
245:        //Update User Gas available.
246:        if (initialGas == 0) { // @audit: 1st sload

550:    function _retrySettlement(uint32 _settlementNonce) internal returns (bool) {
551:        //Get Settlement
552:        Settlement memory settlement = getSettlement[_settlementNonce];
553:
554:        //Check if Settlement hasn't been redeemed.
555:        if (settlement.owner == address(0)) return false;
556:
557:        //abi encodePacked
558:        bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain)); // @audit: invokes function that reads `initialGas`

743:    function _manageGasOut(uint24 _toChain) internal returns (uint128) {
744:        uint256 amountOut;
745:        address gasToken;
746:        uint256 _initialGas = initialGas; // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..94476a3 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -243,12 +243,13 @@ contract RootBridgeAgent is IRootBridgeAgent {
     /// @inheritdoc IRootBridgeAgent
     function retrySettlement(uint32 _settlementNonce, uint128 _remoteExecutionGas) external payable {
         //Update User Gas available.
-        if (initialGas == 0) {
+        uint256 _initialGas = initialGas;
+        if (_initialGas == 0) {
             userFeeInfo.depositedGas = uint128(msg.value);
             userFeeInfo.gasToBridgeOut = _remoteExecutionGas;
         }
         //Clear Settlement with updated gas.
-        _retrySettlement(_settlementNonce);
+        _retrySettlement(_initialGas, _settlementNonce);
     }

     /// @inheritdoc IRootBridgeAgent
@@ -275,7 +276,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
     function callOut(address _recipient, bytes memory _data, uint24 _toChain) external payable lock requiresRouter {
         //Encode Data for call.
         bytes memory data =
-            abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));
+            abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(initialGas, _toChain));

         //Perform Call to clear hToken balance on destination branch chain.
         _performCall(data, _toChain);
@@ -312,7 +313,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
             _amount,
             _deposit,
             _data,
-            _manageGasOut(_toChain)
+            _manageGasOut(initialGas, _toChain)
         );

         //Update State to reflect bridgeOut
@@ -366,7 +367,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
             _amounts,
             _deposits,
             _data,
-            _manageGasOut(_toChain)
+            _manageGasOut(initialGas, _toChain)
         );

         //Create Settlement Balance
@@ -547,7 +548,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
      *    @param _settlementNonce Identifier for token settlement.
      *
      */
-    function _retrySettlement(uint32 _settlementNonce) internal returns (bool) {
+    function _retrySettlement(uint256 _initialGas, uint32 _settlementNonce) internal returns (bool) {
         //Get Settlement
         Settlement memory settlement = getSettlement[_settlementNonce];

@@ -555,7 +556,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
         if (settlement.owner == address(0)) return false;

         //abi encodePacked
-        bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain));
+        bytes memory newGas = abi.encodePacked(_manageGasOut(_initialGas, settlement.toChain));

         //overwrite last 16bytes of callData
         for (uint256 i = 0; i < newGas.length;) {
@@ -740,10 +741,9 @@ contract RootBridgeAgent is IRootBridgeAgent {
      * @notice Manages gas costs of bridging from Root to a given Branch.
      * @param _toChain destination chain.
      */
-    function _manageGasOut(uint24 _toChain) internal returns (uint128) {
+    function _manageGasOut(uint256 _initialGas, uint24 _toChain) internal returns (uint128) {
         uint256 amountOut;
         address gasToken;
-        uint256 _initialGas = initialGas;

         if (_toChain == localChainId) {
             //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L425-L429

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L441-L443

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L831-L836

### Cache `depositNonce` and increment cached value to save 1 SLOAD and 1 SSTORE
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
425:        bytes memory packedData = abi.encodePacked(
426:            bytes1(0x07), depositNonce++, _settlementNonce, msg.value.toUint128(), _gasToBoostSettlement // @audit: 1st sload + sstore
427:        );
428:        //Update State and Perform Call
429:        _sendRetrieveOrRetry(packedData); // @audit: invokes function that updates `depositNonce`

441:    function _sendRetrieveOrRetry(bytes memory _data) internal {
442:        //Deposit Gas for call.
443:        _createGasDeposit(msg.sender, msg.value.toUint128()); // @audit: function that updates `depositNonce`

831:    function _createGasDeposit(address _user, uint128 _gasToBridgeOut) internal {
832:        //Deposit Gas to Port
833:        _depositGas(_gasToBridgeOut);
834:
835:        // Update State
836:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload + sstore
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..1a8cdb0 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -422,11 +422,29 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         requiresFallbackGas
     {
         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData = abi.encodePacked(
-            bytes1(0x07), depositNonce++, _settlementNonce, msg.value.toUint128(), _gasToBoostSettlement
+            bytes1(0x07), _depositNonce++, _settlementNonce, msg.value.toUint128(), _gasToBoostSettlement
         );
         //Update State and Perform Call
-        _sendRetrieveOrRetry(packedData);
+
+        //Deposit Gas to Port
+        _depositGas(msg.value.toUint128());
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: msg.sender,
+            hTokens: new address[](0),
+            tokens: new address[](0),
+            amounts: new uint256[](0),
+            deposits: new uint256[](0),
+            status: DepositStatus.Success,
+            depositedGas: msg.value.toUint128()
+        });
+        depositNonce = _depositNonce;
+
+        //Perform Call
+        _performCall(packedData);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L226-L234

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L757-L759

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L831-L836

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
226:        bytes memory packedData = abi.encodePacked(
227:            bytes1(0x04), msg.sender, depositNonce, _params, msg.value.toUint128(), _remoteExecutionGas // @audit: 1st sload
228:        );
229:
230:        //Wrap the gas allocated for omnichain execution.
231:        wrappedNativeToken.deposit{value: msg.value}();
232:
233:        //Perform Signed Call without deposit
234:        _noDepositCall(msg.sender, packedData, msg.value.toUint128()); // @audit: invokes function that updates `depositNonce`

757:    function _noDepositCall(address _depositor, bytes memory _data, uint128 _gasToBridgeOut) internal {
758:        //Deposit Gas for call.
759:        _createGasDeposit(_depositor, _gasToBridgeOut); // @audit: updates `depositNonce`

831:    function _createGasDeposit(address _user, uint128 _gasToBridgeOut) internal {
832:        //Deposit Gas to Port
833:        _depositGas(_gasToBridgeOut);
834:
835:        // Update State
836:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..1dcf9ff 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -223,15 +223,34 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         requiresFallbackGas
     {
         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData = abi.encodePacked(
-            bytes1(0x04), msg.sender, depositNonce, _params, msg.value.toUint128(), _remoteExecutionGas
+            bytes1(0x04), msg.sender, _depositNonce, _params, msg.value.toUint128(), _remoteExecutionGas
         );

         //Wrap the gas allocated for omnichain execution.
         wrappedNativeToken.deposit{value: msg.value}();

         //Perform Signed Call without deposit
-        _noDepositCall(msg.sender, packedData, msg.value.toUint128());
+
+        //Deposit Gas to Port
+        _depositGas(msg.value.toUint128());
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: msg.sender,
+            hTokens: new address[](0),
+            tokens: new address[](0),
+            amounts: new uint256[](0),
+            deposits: new uint256[](0),
+            status: DepositStatus.Success,
+            depositedGas: msg.value.toUint128()
+        });
+        depositNonce = _depositNonce;
+
+        //Perform Call
+        _performCall(packedData);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L480-L484

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L757-L759

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L831-L836

*Gas Savings for `BranchBridgeAgent.performSystemCallOut`, obtained via protocol's tests: Avg 219 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  215613  |  252733  | 229141  |    53    |
| After  |  215394  |  252514  | 228922  |    53    |

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
480:        bytes memory packedData =
481:            abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas); // @audit: 1st sload
482:
483:        //Perform Call
484:        _noDepositCall(_depositor, packedData, gasToBridgeOut); // @audit: invokes function that updates `depositNonce`

757:    function _noDepositCall(address _depositor, bytes memory _data, uint128 _gasToBridgeOut) internal {
758:        //Deposit Gas for call.
759:        _createGasDeposit(_depositor, _gasToBridgeOut); // @audit: updates `depositNonce`

831:    function _createGasDeposit(address _user, uint128 _gasToBridgeOut) internal {
832:        //Deposit Gas to Port
833:        _depositGas(_gasToBridgeOut);
834:
835:        // Update State
836:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..fa98223 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -477,11 +477,28 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         _requiresFallbackGas(gasToBridgeOut);

         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData =
-            abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas);
+            abi.encodePacked(bytes1(0x00), _depositNonce, _params, gasToBridgeOut, _remoteExecutionGas);

         //Perform Call
-        _noDepositCall(_depositor, packedData, gasToBridgeOut);
+        //Deposit Gas to Port
+        _depositGas(gasToBridgeOut);
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: _depositor,
+            hTokens: new address[](0),
+            tokens: new address[](0),
+            amounts: new uint256[](0),
+            deposits: new uint256[](0),
+            status: DepositStatus.Success,
+            depositedGas: gasToBridgeOut
+        });
+        depositNonce = _depositNonce;
+
+        //Perform Call
+        _performCall(packedData);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L656-L660

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L757-L759

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L831-L836

*Gas Savings for `BranchBridgeAgent.performCallOut`, obtained via protocol's tests: Avg 248 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  1416971 |  1484243 | 1448019 |    13    |
| After  |  1416697 |  1484024 | 1447771 |    13    |

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
656:        bytes memory packedData =
657:            abi.encodePacked(bytes1(0x01), depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas); // @audit: 1st sload
658:
659:        //Perform Call
660:        _noDepositCall(_depositor, packedData, _gasToBridgeOut); // @audit: invokes function that updates `depositNonce`

757:    function _noDepositCall(address _depositor, bytes memory _data, uint128 _gasToBridgeOut) internal {
758:        //Deposit Gas for call.
759:        _createGasDeposit(_depositor, _gasToBridgeOut); // @audit: updates `depositNonce`

831:    function _createGasDeposit(address _user, uint128 _gasToBridgeOut) internal {
832:        //Deposit Gas to Port
833:        _depositGas(_gasToBridgeOut);
834:
835:        // Update State
836:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..7cb124b 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -653,11 +653,28 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         internal
     {
         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData =
-            abi.encodePacked(bytes1(0x01), depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas);
+            abi.encodePacked(bytes1(0x01), _depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas);

         //Perform Call
-        _noDepositCall(_depositor, packedData, _gasToBridgeOut);
+        //Deposit Gas to Port
+        _depositGas(_gasToBridgeOut);
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: _depositor,
+            hTokens: new address[](0),
+            tokens: new address[](0),
+            amounts: new uint256[](0),
+            deposits: new uint256[](0),
+            status: DepositStatus.Success,
+            depositedGas: _gasToBridgeOut
+        });
+        depositNonce = _depositNonce;
+
+        //Perform Call
+        _performCall(packedData);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L245-L263

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L776-L786

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L857-L882

*Gas Savings for `BranchBridgeAgent.callOutSignedAndBridge`, obtained via protocol's tests: Avg 206 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  1701908 |  1701908 | 1701908 |    2     |
| After  |  1701702 |  1701702 | 1701702 |    2     |

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
245:        bytes memory packedData = abi.encodePacked(
246:            bytes1(0x05),
247:            msg.sender,
248:            depositNonce, // @audit: 1st sload
249:            _dParams.hToken,
250:            _dParams.token,
251:            _dParams.amount,
252:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
253:            _dParams.toChain,
254:            _params,
255:            msg.value.toUint128(),
256:            _remoteExecutionGas
257:        );
258:
259:        //Wrap the gas allocated for omnichain execution.
260:        wrappedNativeToken.deposit{value: msg.value}();
261:
262:        //Create Deposit and Send Cross-Chain request
263:        _depositAndCall( // @audit: invokes function that updates `depositNonce`

776:    function _depositAndCall(
777:        address _depositor,
778:        bytes memory _data,
779:        address _hToken,
780:        address _token,
781:        uint256 _amount,
782:        uint256 _deposit,
783:        uint128 _gasToBridgeOut
784:    ) internal {
785:        //Deposit and Store Info
786:        _createDepositSingle(_depositor, _hToken, _token, _amount, _deposit, _gasToBridgeOut); // @audit: updates `depositNonce`

857:    function _createDepositSingle(
858:        address _user,
859:        address _hToken,
860:        address _token,
861:        uint256 _amount,
862:        uint256 _deposit,
863:        uint128 _gasToBridgeOut
864:    ) internal {
...
881:        // Update State
882:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..7737b11 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -242,10 +242,11 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         requiresFallbackGas
     {
         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData = abi.encodePacked(
             bytes1(0x05),
             msg.sender,
-            depositNonce,
+            _depositNonce,
             _dParams.hToken,
             _dParams.token,
             _dParams.amount,
@@ -260,15 +261,37 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         wrappedNativeToken.deposit{value: msg.value}();

         //Create Deposit and Send Cross-Chain request
-        _depositAndCall(
-            msg.sender,
-            packedData,
-            _dParams.hToken,
-            _dParams.token,
-            _dParams.amount,
-            _dParams.deposit,
-            msg.value.toUint128()
-        );
+        //Deposit / Lock Tokens into Port
+        IPort(localPortAddress).bridgeOut(msg.sender, _dParams.hToken, _dParams.token, _dParams.amount, _dParams.deposit);
+
+        //Deposit Gas to Port
+        _depositGas(msg.value.toUint128());
+
+        // Cast to dynamic memory array
+        address[] memory hTokens = new address[](1);
+        hTokens[0] = _dParams.hToken;
+        address[] memory tokens = new address[](1);
+        tokens[0] = _dParams.token;
+        uint256[] memory amounts = new uint256[](1);
+        amounts[0] = _dParams.amount;
+        uint256[] memory deposits = new uint256[](1);
+        deposits[0] = _dParams.deposit;
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: msg.sender,
+            hTokens: hTokens,
+            tokens: tokens,
+            amounts: amounts,
+            deposits: deposits,
+            status: DepositStatus.Success,
+            depositedGas: msg.value.toUint128()
+        });
+        depositNonce = _depositNonce;
+
+
+        //Perform Call
+        _performCall(packedData);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L681-L695

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L776-L786

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L857-L882

*Gas Savings for `BaseBranchRouter.callOutAndBridge`, obtained via protocol's tests: Avg 296 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  288381  |  305445  | 256327  |    12    |
| After  |  288112  |  304340  | 256031  |    12    |

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
681:        bytes memory packedData = abi.encodePacked(
682:            bytes1(0x02),
683:            depositNonce, // @audit: 1st sload
684:            _dParams.hToken,
685:            _dParams.token,
686:            _dParams.amount,
687:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
688:            _dParams.toChain,
689:            _params,
690:            _gasToBridgeOut,
691:            _remoteExecutionGas
692:        );
693:
694:        //Create Deposit and Send Cross-Chain request
695:        _depositAndCall( // @audit: invokes function that updates `depositNonce`

776:    function _depositAndCall(
777:        address _depositor,
778:        bytes memory _data,
779:        address _hToken,
780:        address _token,
781:        uint256 _amount,
782:        uint256 _deposit,
783:        uint128 _gasToBridgeOut
784:    ) internal {
785:        //Deposit and Store Info
786:        _createDepositSingle(_depositor, _hToken, _token, _amount, _deposit, _gasToBridgeOut); // @audit: updates `depositNonce`

857:    function _createDepositSingle(
858:        address _user,
859:        address _hToken,
860:        address _token,
861:        uint256 _amount,
862:        uint256 _deposit,
863:        uint128 _gasToBridgeOut
864:    ) internal {
...
881:        // Update State
882:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..73c3997 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -678,9 +678,10 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         uint128 _remoteExecutionGas
     ) internal {
         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData = abi.encodePacked(
             bytes1(0x02),
-            depositNonce,
+            _depositNonce,
             _dParams.hToken,
             _dParams.token,
             _dParams.amount,
@@ -692,9 +693,36 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         );

         //Create Deposit and Send Cross-Chain request
-        _depositAndCall(
-            _depositor, packedData, _dParams.hToken, _dParams.token, _dParams.amount, _dParams.deposit, _gasToBridgeOut
-        );
+        //Deposit / Lock Tokens into Port
+        IPort(localPortAddress).bridgeOut(_depositor, _dParams.hToken, _dParams.token, _dParams.amount, _dParams.deposit);
+
+        //Deposit Gas to Port
+        _depositGas(_gasToBridgeOut);
+
+        // Cast to dynamic memory array
+        address[] memory hTokens = new address[](1);
+        hTokens[0] = _dParams.hToken;
+        address[] memory tokens = new address[](1);
+        tokens[0] = _dParams.token;
+        uint256[] memory amounts = new uint256[](1);
+        amounts[0] = _dParams.amount;
+        uint256[] memory deposits = new uint256[](1);
+        deposits[0] = _dParams.deposit;
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: _depositor,
+            hTokens: hTokens,
+            tokens: tokens,
+            amounts: amounts,
+            deposits: deposits,
+            status: DepositStatus.Success,
+            depositedGas: _gasToBridgeOut
+        });
+        depositNonce = _depositNonce;
+
+        //Perform Call
+        _performCall(packedData);
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L288-L307

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L803-L819

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L903-L918

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
288:        bytes memory packedData = abi.encodePacked(
289:            bytes1(0x06),
290:            msg.sender,
291:            uint8(_dParams.hTokens.length),
292:            depositNonce, // @audit: 1st sload
293:            _dParams.hTokens,
294:            _dParams.tokens,
295:            _dParams.amounts,
296:            _deposits,
297:            _dParams.toChain,
298:            _params,
299:            msg.value.toUint128(),
300:            _remoteExecutionGas
301:        );
302:
303:        //Wrap the gas allocated for omnichain execution.
304:        wrappedNativeToken.deposit{value: msg.value}();
305:
306:        //Create Deposit and Send Cross-Chain request
307:        _depositAndCallMultiple( // @audit: invokes function that updates `depositNonce`

803:    function _depositAndCallMultiple(
804:        address _depositor,
805:        bytes memory _data,
806:        address[] memory _hTokens,
807:        address[] memory _tokens,
808:        uint256[] memory _amounts,
809:        uint256[] memory _deposits,
810:        uint128 _gasToBridgeOut
811:    ) internal {
812:        //Validate Input
813:        if (
814:            _hTokens.length != _tokens.length || _tokens.length != _amounts.length
815:                || _amounts.length != _deposits.length
816:        ) revert InvalidInput();
817:
818:        //Deposit and Store Info
819:        _createDepositMultiple(_depositor, _hTokens, _tokens, _amounts, _deposits, _gasToBridgeOut); // @audit: updates `depositNonce`

903:    function _createDepositMultiple(
904:        address _user,
905:        address[] memory _hTokens,
906:        address[] memory _tokens,
907:        uint256[] memory _amounts,
908:        uint256[] memory _deposits,
909:        uint128 _gasToBridgeOut
910:    ) internal {
911:        //Deposit / Lock Tokens into Port
912:        IPort(localPortAddress).bridgeOutMultiple(_user, _hTokens, _tokens, _amounts, _deposits);
913:
914:        //Deposit Gas to Port
915:        _depositGas(_gasToBridgeOut);
916:
917:        // Update State
918:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..4a2783d 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -285,11 +285,12 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         }

         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData = abi.encodePacked(
             bytes1(0x06),
             msg.sender,
             uint8(_dParams.hTokens.length),
-            depositNonce,
+            _depositNonce,
             _dParams.hTokens,
             _dParams.tokens,
             _dParams.amounts,
@@ -304,15 +305,34 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         wrappedNativeToken.deposit{value: msg.value}();

         //Create Deposit and Send Cross-Chain request
-        _depositAndCallMultiple(
-            msg.sender,
-            packedData,
-            _dParams.hTokens,
-            _dParams.tokens,
-            _dParams.amounts,
-            _dParams.deposits,
-            msg.value.toUint128()
-        );
+        //Validate Input
+        if (
+            _dParams.hTokens.length != _dParams.tokens.length || _dParams.tokens.length != _dParams.amounts.length
+                || _dParams.amounts.length != _dParams.deposits.length
+        ) revert InvalidInput();
+
+        //Deposit / Lock Tokens into Port
+        IPort(localPortAddress).bridgeOutMultiple(msg.sender, _dParams.hTokens, _dParams.tokens, _dParams.amounts, _dParams.deposits);
+
+        //Deposit Gas to Port
+        _depositGas(msg.value.toUint128());
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: msg.sender,
+            hTokens: _dParams.hTokens,
+            tokens: _dParams.tokens,
+            amounts: _dParams.amounts,
+            deposits: _dParams.deposits,
+            status: DepositStatus.Success,
+            depositedGas: msg.value.toUint128()
+        });
+        depositNonce = _depositNonce;
+
+
+        //Perform Call
+        _performCall(packedData);
+
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L724-L739

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L803-L819

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L903-L918

*Gas Savings for `BaseBranchRouter.callOutAndBridgeMultiple`, obtained via protocol's tests: Avg 174 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  431637  |  431637  | 431637  |    1     |
| After  |  431463  |  431463  | 431463  |    1     |

### Cache `depositNonce` to save 1 SLOAD
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
724:        bytes memory packedData = abi.encodePacked(
725:            bytes1(0x03),
726:            uint8(_dParams.hTokens.length),
727:            depositNonce, // @audit: 1st sload
728:            _dParams.hTokens,
729:            _dParams.tokens,
730:            _dParams.amounts,
731:            deposits,
732:            _dParams.toChain,
733:            _params,
734:            _gasToBridgeOut,
735:            _remoteExecutionGas
736:        );
737:
738:        //Create Deposit and Send Cross-Chain request
739:        _depositAndCallMultiple( // @audit: invokes function that updates `depositNonce`

803:    function _depositAndCallMultiple(
804:        address _depositor,
805:        bytes memory _data,
806:        address[] memory _hTokens,
807:        address[] memory _tokens,
808:        uint256[] memory _amounts,
809:        uint256[] memory _deposits,
810:        uint128 _gasToBridgeOut
811:    ) internal {
812:        //Validate Input
813:        if (
814:            _hTokens.length != _tokens.length || _tokens.length != _amounts.length
815:                || _amounts.length != _deposits.length
816:        ) revert InvalidInput();
817:
818:        //Deposit and Store Info
819:        _createDepositMultiple(_depositor, _hTokens, _tokens, _amounts, _deposits, _gasToBridgeOut); // @audit: updates `depositNonce`

903:    function _createDepositMultiple(
904:        address _user,
905:        address[] memory _hTokens,
906:        address[] memory _tokens,
907:        uint256[] memory _amounts,
908:        uint256[] memory _deposits,
909:        uint128 _gasToBridgeOut
910:    ) internal {
911:        //Deposit / Lock Tokens into Port
912:        IPort(localPortAddress).bridgeOutMultiple(_user, _hTokens, _tokens, _amounts, _deposits);
913:
914:        //Deposit Gas to Port
915:        _depositGas(_gasToBridgeOut);
916:
917:        // Update State
918:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: 2nd sload
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..83b92f1 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -721,10 +721,11 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         }

         //Encode Data for cross-chain call.
+        uint32 _depositNonce = depositNonce;
         bytes memory packedData = abi.encodePacked(
             bytes1(0x03),
             uint8(_dParams.hTokens.length),
-            depositNonce,
+            _depositNonce,
             _dParams.hTokens,
             _dParams.tokens,
             _dParams.amounts,
@@ -736,15 +737,32 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         );

         //Create Deposit and Send Cross-Chain request
-        _depositAndCallMultiple(
-            _depositor,
-            packedData,
-            _dParams.hTokens,
-            _dParams.tokens,
-            _dParams.amounts,
-            _dParams.deposits,
-            _gasToBridgeOut
-        );
+        //Validate Input
+        if (
+            _dParams.hTokens.length != _dParams.tokens.length || _dParams.tokens.length != _dParams.amounts.length
+                || _dParams.amounts.length != _dParams.deposits.length
+        ) revert InvalidInput();
+
+        //Deposit / Lock Tokens into Port
+        IPort(localPortAddress).bridgeOutMultiple(_depositor, _dParams.hTokens, _dParams.tokens, _dParams.amounts, _dParams.deposits);
+
+        //Deposit Gas to Port
+        _depositGas(_gasToBridgeOut);
+
+        // Update State
+        getDeposit[_depositNonce++] = Deposit({
+            owner: _depositor,
+            hTokens: _dParams.hTokens,
+            tokens: _dParams.tokens,
+            amounts: _dParams.amounts,
+            deposits: _dParams.deposits,
+            status: DepositStatus.Success,
+            depositedGas: _gasToBridgeOut
+        });
+        depositNonce = _depositNonce;
+
+        //Perform Call
+        _performCall(packedData);
     }
```

## Refactor code to avoid redundant external calls
External calls are expensive as they use the `CALL/STATICCALL` opcode (~100 gas). The instances below illustrate functions that invoke other public/internal functions which perform external calls that were already performed (either in the calling function or in other invoked public/internal functions). In the diffs below we inlined the logic from the public/internal functions and re-used the return values from the first function calls to avoid performing redundant external calls.

Total Instances: `4`

Estimated Gas Saved: `2623`

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L232-L241

### Re-use return value from `_pool.slot0()` to save 2 External Calls
```solidity
File: src/talos/libraries/PoolVariables.sol
232:        (uint160 sqrtPriceX96, int24 currentTick,,,,,) = _pool.slot0(); // @audit: 1st external call
233:
234:        (cache.tickLower, cache.tickUpper) = baseTicks(currentTick, baseThreshold, _tickSpacing);
235:
236:        // Calc liquidity for base ticks
237:        cache.liquidity =
238:            liquidityForAmounts(_pool, cache.amount0Desired, cache.amount1Desired, cache.tickLower, cache.tickUpper); // @audit: 2nd external call (`pool.slot0()`)
239:
240:        // Get exact amounts for base ticks
241:        (cache.amount0, cache.amount1) = amountsForLiquidity(_pool, cache.liquidity, cache.tickLower, cache.tickUpper); // @audit: 3rd external call (`pool.slot0()`)
```
```diff
diff --git a/src/talos/libraries/PoolVariables.sol b/src/talos/libraries/PoolVariables.sol
index db83965..88c0c04 100644
--- a/src/talos/libraries/PoolVariables.sol
+++ b/src/talos/libraries/PoolVariables.sol
@@ -234,11 +234,18 @@ library PoolVariables {
         (cache.tickLower, cache.tickUpper) = baseTicks(currentTick, baseThreshold, _tickSpacing);

         // Calc liquidity for base ticks
-        cache.liquidity =
-            liquidityForAmounts(_pool, cache.amount0Desired, cache.amount1Desired, cache.tickLower, cache.tickUpper);
+        cache.liquidity = LiquidityAmounts.getLiquidityForAmounts(
+            sqrtPriceX96,
+            TickMath.getSqrtRatioAtTick(cache.tickLower),
+            TickMath.getSqrtRatioAtTick(cache.tickUpper),
+            cache.amount0Desired,
+            cache.amount1Desired
+        );

         // Get exact amounts for base ticks
-        (cache.amount0, cache.amount1) = amountsForLiquidity(_pool, cache.liquidity, cache.tickLower, cache.tickUpper);
+        (cache.amount0, cache.amount1) = LiquidityAmounts.getAmountsForLiquidity(
+            sqrtPriceX96, TickMath.getSqrtRatioAtTick(cache.tickLower), TickMath.getSqrtRatioAtTick(cache.tickUpper), cache.liquidity
+        );

         // Get imbalanced token
         zeroForOne = amountsDirection(cache.amount0Desired, cache.amount1Desired, cache.amount0, cache.amount1);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L119-L126

### Re-use return value from `pool.slot0()` to save 3 External Calls
```solidity
File: src/talos/libraries/PoolVariables.sol
119:        (uint160 sqrtPriceX96, int24 currentTick,,,,,) = pool.slot0(); // @audit: 1st external call
120:        //Calc base ticks
121:        (cache.tickLower, cache.tickUpper) = baseTicks(currentTick, baseThreshold, tickSpacing);
122:        //Calc amounts of token0 and token1 that can be stored in base range
123:        (cache.amount0, cache.amount1) =
124:            amountsForTicks(pool, cache.amount0Desired, cache.amount1Desired, cache.tickLower, cache.tickUpper); // @audit: 2nd and 3rd external calls (`pool.slot0()`)
125:        //Liquidity that can be stored in base range
126:        cache.liquidity = liquidityForAmounts(pool, cache.amount0, cache.amount1, cache.tickLower, cache.tickUpper); // @audit: 4th external call (`pool.slot0()`
```
```diff
diff --git a/src/talos/libraries/PoolVariables.sol b/src/talos/libraries/PoolVariables.sol
index db83965..3fc3569 100644
--- a/src/talos/libraries/PoolVariables.sol
+++ b/src/talos/libraries/PoolVariables.sol
@@ -120,10 +120,26 @@ library PoolVariables {
         //Calc base ticks
         (cache.tickLower, cache.tickUpper) = baseTicks(currentTick, baseThreshold, tickSpacing);
         //Calc amounts of token0 and token1 that can be stored in base range
-        (cache.amount0, cache.amount1) =
-            amountsForTicks(pool, cache.amount0Desired, cache.amount1Desired, cache.tickLower, cache.tickUpper);
+        uint128 liquidity = LiquidityAmounts.getLiquidityForAmounts(
+            sqrtPriceX96,
+            TickMath.getSqrtRatioAtTick(cache.tickLower),
+            TickMath.getSqrtRatioAtTick(cache.tickUpper),
+            cache.amount0Desired,
+            cache.amount1Desired
+        );
+
+        (cache.amount0, cache.amount1) = LiquidityAmounts.getAmountsForLiquidity(
+            sqrtPriceX96, TickMath.getSqrtRatioAtTick(cache.tickLower), TickMath.getSqrtRatioAtTick(cache.tickUpper), liquidity
+        );
+
         //Liquidity that can be stored in base range
-        cache.liquidity = liquidityForAmounts(pool, cache.amount0, cache.amount1, cache.tickLower, cache.tickUpper);
+        cache.liquidity = LiquidityAmounts.getLiquidityForAmounts(
+            sqrtPriceX96,
+            TickMath.getSqrtRatioAtTick(cache.tickLower),
+            TickMath.getSqrtRatioAtTick(cache.tickUpper),
+            cache.amount0,
+            cache.amount1
+        );
         //Get imbalanced token
         bool zeroGreaterOne = amountsDirection(cache.amount0Desired, cache.amount1Desired, cache.amount0, cache.amount1);
         //Calc new tick(upper or lower) for imbalanced token
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L155-L165

### Re-use return value from `pool.slot0()` to save 1 External Call
```solidity
File: src/talos/libraries/PoolVariables.sol
155:    function amountsForTicks(
156:        IUniswapV3Pool pool,
157:        uint256 amount0Desired,
158:        uint256 amount1Desired,
159:        int24 _tickLower,
160:        int24 _tickUpper
161:    ) internal view returns (uint256 amount0, uint256 amount1) {
162:        uint128 liquidity = liquidityForAmounts(pool, amount0Desired, amount1Desired, _tickLower, _tickUpper); // @audit: 1st call to `pool.slot0()`
163:
164:        (amount0, amount1) = amountsForLiquidity(pool, liquidity, _tickLower, _tickUpper); // @audit 2nd call to `pool.slot0()`
165:    }
```
```diff
diff --git a/src/talos/libraries/PoolVariables.sol b/src/talos/libraries/PoolVariables.sol
index db83965..21aa0a5 100644
--- a/src/talos/libraries/PoolVariables.sol
+++ b/src/talos/libraries/PoolVariables.sol
@@ -159,9 +159,19 @@ library PoolVariables {
         int24 _tickLower,
         int24 _tickUpper
     ) internal view returns (uint256 amount0, uint256 amount1) {
-        uint128 liquidity = liquidityForAmounts(pool, amount0Desired, amount1Desired, _tickLower, _tickUpper);
+        (uint160 sqrtRatioX96,,,,,,) = pool.slot0();

-        (amount0, amount1) = amountsForLiquidity(pool, liquidity, _tickLower, _tickUpper);
+        uint128 liquidity = LiquidityAmounts.getLiquidityForAmounts(
+            sqrtRatioX96,
+            TickMath.getSqrtRatioAtTick(_tickLower),
+            TickMath.getSqrtRatioAtTick(_tickUpper),
+            amount0Desired,
+            amount1Desired
+        );
+
+        (uint256 amount0, uint256 amount1) = LiquidityAmounts.getAmountsForLiquidity(
+            sqrtRatioX96, TickMath.getSqrtRatioAtTick(_tickLower), TickMath.getSqrtRatioAtTick(_tickUpper), liquidity
+        );
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L108-L120

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L129-L133

*Gas Savings for `BaseV2Minter.updatePeriod`, obtained via protocol's tests: Avg 2023 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |    538   |  131809  | 59230   |    11    |
| After  |    538   |  127359  | 57207   |    11    |

### Cache `HERMES(underlying).totalSupply()` and `vault.totalAssets()` to save 4 External Calls
```solidity
File: src/hermes/minters/BaseV2Minter.sol
108:    function circulatingSupply() public view returns (uint256) {
109:        return HERMES(underlying).totalSupply() - vault.totalAssets();
110:    }
...
113:    function weeklyEmission() public view returns (uint256) {
114:        return (circulatingSupply() * tailEmission) / base;
115:    }
...
118:    function calculateGrowth(uint256 _minted) public view returns (uint256) {
119:        return (vault.totalAssets() * _minted) / HERMES(underlying).totalSupply();
120:    }

129:            uint256 newWeeklyEmission = weeklyEmission(); // @audit: performs 2 external calls
130:            weekly += newWeeklyEmission;
131:            uint256 _circulatingSupply = circulatingSupply(); // @audit: performs 2 external calls (4 total)
132:
133:            uint256 _growth = calculateGrowth(newWeeklyEmission); // @audit: performs 2 external calls (6 total)
```
```diff
diff --git a/src/hermes/minters/BaseV2Minter.sol b/src/hermes/minters/BaseV2Minter.sol
index 7d7f013..8272292 100644
--- a/src/hermes/minters/BaseV2Minter.sol
+++ b/src/hermes/minters/BaseV2Minter.sol
@@ -126,11 +126,12 @@ contract BaseV2Minter is Ownable, IBaseV2Minter {
         if (block.timestamp >= _period + week && initializer == address(0)) {
             _period = (block.timestamp / week) * week;
             activePeriod = _period;
-            uint256 newWeeklyEmission = weeklyEmission();
+            uint256 _totalSupply = HERMES(underlying).totalSupply();
+            uint256 _totalAssets = vault.totalAssets();
+            uint256 _circulatingSupply = _totalSupply = _totalAssets;
+            uint256 newWeeklyEmission = (_circulatingSupply * tailEmission) / base;
             weekly += newWeeklyEmission;
-            uint256 _circulatingSupply = circulatingSupply();
-
-            uint256 _growth = calculateGrowth(newWeeklyEmission);
+            uint256 _growth = (_totalAssets * newWeeklyEmission) / _totalSupply;
             uint256 _required = _growth + newWeeklyEmission;
             /// @dev share of newWeeklyEmission emissions sent to DAO.
             uint256 share = (_required * daoShare) / base;
```

## Use calldata instead of memory for function parameters
When you specify a data location as `memory`, that value will be copied into memory. When you specify the location as `calldata`, the value will stay static within calldata. If the value is a large, complex type, using memory may result in extra memory expansion costs.

Total Instances: `7`

Estimated Gas Saved: `8463`

**Note: These are instances missed by in the bot race**.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L76-L80

*Gas Savings for `BaseBranchRouter.callOutAndBridgeMultiple`, obtained via protocol's tests: Avg 1694 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  431637  |  431637  | 431637  |    1     |
| After  |  429943  |  429943  | 429943  |    1     |

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol
76:    function callOutAndBridgeMultiple(
77:        bytes calldata params,
78:        DepositMultipleInput memory dParams,
79:        uint128 remoteExecutionGas
80:    ) external payable lock {
```
```diff
diff --git a/src/ulysses-omnichain/BaseBranchRouter.sol b/src/ulysses-omnichain/BaseBranchRouter.sol
index 4ef1023..cc2242f 100644
--- a/src/ulysses-omnichain/BaseBranchRouter.sol
+++ b/src/ulysses-omnichain/BaseBranchRouter.sol
@@ -75,7 +75,7 @@ contract BaseBranchRouter is IBranchRouter, Ownable {
     /// @inheritdoc IBranchRouter
     function callOutAndBridgeMultiple(
         bytes calldata params,
-        DepositMultipleInput memory dParams,
+        DepositMultipleInput calldata dParams,
         uint128 remoteExecutionGas
     ) external payable lock {
         IBridgeAgent(localBridgeAgentAddress).performCallOutAndBridgeMultiple{value: msg.value}(
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L65

*Gas Savings for `BaseBranchRouter.callOutAndBridge`, obtained via protocol's tests: Avg 310 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  288381  |  305445  | 256327  |    12    |
| After  |  288135  |  304358  | 256017  |    12    |

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol
65:    function callOutAndBridge(bytes calldata params, DepositInput memory dParams, uint128 remoteExecutionGas)
```
```diff
diff --git a/src/ulysses-omnichain/BaseBranchRouter.sol b/src/ulysses-omnichain/BaseBranchRouter.sol
index 4ef1023..4df5def 100644
--- a/src/ulysses-omnichain/BaseBranchRouter.sol
+++ b/src/ulysses-omnichain/BaseBranchRouter.sol
@@ -62,7 +62,7 @@ contract BaseBranchRouter is IBranchRouter, Ownable {
     }

     /// @inheritdoc IBranchRouter
-    function callOutAndBridge(bytes calldata params, DepositInput memory dParams, uint128 remoteExecutionGas)
+    function callOutAndBridge(bytes calldata params, DepositInput calldata dParams, uint128 remoteExecutionGas)
         external
         payable
         lock
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L404

*Gas Savings for `RootBridgeAgent.bridgeInMultiple`, obtained via protocol's tests: Avg 99 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  56698   |  56698   | 56698   |    3     |
| After  |  56599   |  56599   | 56599   |    3     |

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
404:    function bridgeInMultiple(address _recipient, DepositMultipleParams memory _dParams, uint24 _fromChain)
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..95f094e 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -401,7 +401,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
     }

     /// @inheritdoc IRootBridgeAgent
-    function bridgeInMultiple(address _recipient, DepositMultipleParams memory _dParams, uint24 _fromChain)
+    function bridgeInMultiple(address _recipient, DepositMultipleParams calldata _dParams, uint24 _fromChain)
         external
         requiresAgentExecutor
     {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L285-L293:

*Gas Savings for `RootBridgeAgent.callOutAndBridge`, obtained via protocol's tests: Avg 199 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  484688  |  549370  | 489608  |    11    |
| After  |  484489  |  549122  | 489379  |    11    |

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
285:    function callOutAndBridge(
286:        address _owner,
287:        address _recipient,
288:        bytes memory _data,
289:        address _globalAddress,
290:        uint256 _amount,
291:        uint256 _deposit,
292:        uint24 _toChain
293:    ) external payable lock requiresRouter {
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..28608e7 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -285,7 +285,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
     function callOutAndBridge(
         address _owner,
         address _recipient,
-        bytes memory _data,
+        bytes calldata _data,
         address _globalAddress,
         uint256 _amount,
         uint256 _deposit,
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L275

*Gas Savings for `RootBridgeAgent.callOut`, obtained via protocol's tests: Avg 678 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  5355492 |  6153563 | 3778403 |    111   |
| After  |  5354784 |  6152855 | 3777725 |    111   |

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
275:    function callOut(address _recipient, bytes memory _data, uint24 _toChain) external payable lock requiresRouter {
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..4231694 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -272,7 +272,7 @@ contract RootBridgeAgent is IRootBridgeAgent {
     //////////////////////////////////////////////////////////////*/

     /// @inheritdoc IRootBridgeAgent
-    function callOut(address _recipient, bytes memory _data, uint24 _toChain) external payable lock requiresRouter {
+    function callOut(address _recipient, bytes calldata _data, uint24 _toChain) external payable lock requiresRouter {
         //Encode Data for call.
         bytes memory data =
             abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L192

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L509-L512

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L673-L676

*Gas Savings for `BaseBranchRouter.callOutAndBridge`, obtained via protocol's tests: Avg 2778 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  288381  |  305445  |  256327 |    12    |
| After  |  288801  |  304891  |  253549 |    12    |

*Gas Savings for `MockBranchBridgeAgent.performCallOutAndBridge`, obtained via protocol's tests: Avg 2705 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  272785  |  289849  |  240430 |    12    |
| After  |  273205  |  290174  |  237725 |    12    |

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
192:    function callOutAndBridge(bytes calldata _params, DepositInput memory _dParams, uint128 _remoteExecutionGas)  // @audit: `_dParams` can be calldata

509:    function performCallOutAndBridge(
510:        address _depositor,
511:        bytes calldata _params,
512:        DepositInput memory _dParams, // @audit: can be calldata

673:    function _callOutAndBridge( // @audit: invoked by functions above
674:        address _depositor,
675:        bytes calldata _params,
676:        DepositInput memory _dParams, // @audit: can be calldata
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..16c9db2 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -189,7 +189,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
     }

     /// @inheritdoc IBranchBridgeAgent
-    function callOutAndBridge(bytes calldata _params, DepositInput memory _dParams, uint128 _remoteExecutionGas)
+    function callOutAndBridge(bytes calldata _params, DepositInput calldata _dParams, uint128 _remoteExecutionGas)
         external
         payable
         lock
@@ -509,7 +509,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
     function performCallOutAndBridge(
         address _depositor,
         bytes calldata _params,
-        DepositInput memory _dParams,
+        DepositInput calldata _dParams,
         uint128 _gasToBridgeOut,
         uint128 _remoteExecutionGas
     ) external payable lock requiresRouter {
@@ -673,7 +673,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
     function _callOutAndBridge(
         address _depositor,
         bytes calldata _params,
-        DepositInput memory _dParams,
+        DepositInput calldata _dParams,
         uint128 _gasToBridgeOut,
         uint128 _remoteExecutionGas
     ) internal {
```

## Use struct `dot` notation to avoid memory allocation
In the instances below, structs values are being assigned via the following method: `Type({field1: value1, field2: value2});`. This method results in a new struct being created in memory with our specified fields. That memory struct is then written to storage. We can leverage the struct `dot` notation to assign values **directly to storage**, thus avoiding allocating memory space for a temporary struct.

Total Instances: `8`

Estimated Gas Saved: `1416`

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L189-L193

*Gas Savings for `FlywheelGaugeRewards.queueRewardsForCyclePaginated`, obtained via protocol's tests: Avg 299 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  61742   |  132064  |  78774  |    9     |
| After  |  61425   |  131747  |  78475  |    9     |

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
189:            gaugeQueuedRewards[gauge] = QueuedRewards({ // @audit: new `QueuedRewards` struct being created in memory and then copied into storage
190:                priorCycleRewards: queuedRewards.priorCycleRewards + completedRewards,
191:                cycleRewards: uint112(nextRewards),
192:                storedCycle: currentCycle
193:            });
```
```diff
diff --git a/src/rewards/rewards/FlywheelGaugeRewards.sol b/src/rewards/rewards/FlywheelGaugeRewards.sol
index a33e81a..0efcc5f 100644
--- a/src/rewards/rewards/FlywheelGaugeRewards.sol
+++ b/src/rewards/rewards/FlywheelGaugeRewards.sol
@@ -185,12 +185,11 @@ contract FlywheelGaugeRewards is Ownable, IFlywheelGaugeRewards {
             uint112 completedRewards = queuedRewards.storedCycle == lastCycle ? queuedRewards.cycleRewards : 0;
             uint256 nextRewards = gaugeToken.calculateGaugeAllocation(address(gauge), totalQueuedForCycle);
             require(nextRewards <= type(uint112).max); // safe cast
-
-            gaugeQueuedRewards[gauge] = QueuedRewards({
-                priorCycleRewards: queuedRewards.priorCycleRewards + completedRewards,
-                cycleRewards: uint112(nextRewards),
-                storedCycle: currentCycle
-            });
+
+            QueuedRewards storage _gaugeQueuedRewards = gaugeQueuedRewards[gauge];
+            _gaugeQueuedRewards.priorCycleRewards = queuedRewards.priorCycleRewards + completedRewards;
+            _gaugeQueuedRewards.cycleRewards = uint112(nextRewards);
+            _gaugeQueuedRewards.storedCycle = currentCycle;

             emit QueueRewards(address(gauge), currentCycle, nextRewards);
         }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L228-L232

*Gas Savings for `FlywheelGaugeRewards.getAccruedRewards`, obtained via protocol's tests: Avg 101 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  3153    |  27095   | 8741    |    23    |
| After  |  3153    |  26799   | 8640    |    23    |

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
228:        gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({ // @audit: new `QueuedRewards` struct being created in memory and then copied into storage
229:            priorCycleRewards: 0,
230:            cycleRewards: cycleRewardsNext,
231:            storedCycle: queuedRewards.storedCycle
232:        });
```
```diff
diff --git a/src/rewards/rewards/FlywheelGaugeRewards.sol b/src/rewards/rewards/FlywheelGaugeRewards.sol
index a33e81a..464ca7a 100644
--- a/src/rewards/rewards/FlywheelGaugeRewards.sol
+++ b/src/rewards/rewards/FlywheelGaugeRewards.sol
@@ -224,13 +224,13 @@ contract FlywheelGaugeRewards is Ownable, IFlywheelGaugeRewards {
             accruedRewards += cycleRewardsNext;
             cycleRewardsNext = 0;
         }
-
-        gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({
-            priorCycleRewards: 0,
-            cycleRewards: cycleRewardsNext,
-            storedCycle: queuedRewards.storedCycle
-        });
+
+        QueuedRewards storage _gaugeQueuedRewards = gaugeQueuedRewards[ERC20(msg.sender)];
+        _gaugeQueuedRewards.priorCycleRewards = 0;
+        _gaugeQueuedRewards.cycleRewards = cycleRewardsNext;
+        _gaugeQueuedRewards.storedCycle = queuedRewards.storedCycle;

         if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);
     }
 }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L229

*Gas Savings for `UniswapV3Staker.onERC721Received`, obtained via protocol's tests: Avg 166 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  131975  |  247596  | 126285  |    56    |
| After  |  131809  |  247430  | 126119  |    56    |

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol
229:        deposits[tokenId] = Deposit({owner: from, tickLower: tickLower, tickUpper: tickUpper, stakedTimestamp: 0}); // @audit: new `Deposit` struct being created in memory and then copied into storage
```
```diff
diff --git a/src/uni-v3-staker/UniswapV3Staker.sol b/src/uni-v3-staker/UniswapV3Staker.sol
index 5970379..4d021ee 100644
--- a/src/uni-v3-staker/UniswapV3Staker.sol
+++ b/src/uni-v3-staker/UniswapV3Staker.sol
@@ -225,8 +225,12 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {

         (IUniswapV3Pool pool, int24 tickLower, int24 tickUpper, uint128 liquidity) =
             NFTPositionInfo.getPositionInfo(factory, nonfungiblePositionManager, tokenId);
-
-        deposits[tokenId] = Deposit({owner: from, tickLower: tickLower, tickUpper: tickUpper, stakedTimestamp: 0});
+
+        Deposit storage _deposit = deposits[tokenId];
+        _deposit.owner = from;
+        _deposit.tickLower = tickLower;
+        _deposit.tickUpper = tickUpper;
+        _deposit.stakedTimestamp = 0;
         emit DepositTransferred(tokenId, address(0), from);

         // stake the token in the current incentive
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L531-L542

*Gas Savings for `RootBridgeAgent.callOutAndBridge`, obtained via protocol's tests: Avg 99 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  484688  |  549370  | 489608  |    11    |
| After  |  484589  |  549247  | 489495  |    11    |

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
531:        getSettlement[_getAndIncrementSettlementNonce()] = Settlement({ // @audit: new `Settlement` struct being created in memory and then copied into storage
532:            owner: _owner,
533:            recipient: _recipient,
534:            hTokens: _hTokens,
535:            tokens: _tokens,
536:            amounts: _amounts,
537:            deposits: _deposits,
538:            callData: _callData,
539:            toChain: _toChain,
540:            status: SettlementStatus.Success,
541:            gasToBridgeOut: userFeeInfo.gasToBridgeOut
542:        });
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..1651422 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -528,18 +528,17 @@ contract RootBridgeAgent is IRootBridgeAgent {
         uint24 _toChain
     ) internal {
         // Update State
-        getSettlement[_getAndIncrementSettlementNonce()] = Settlement({
-            owner: _owner,
-            recipient: _recipient,
-            hTokens: _hTokens,
-            tokens: _tokens,
-            amounts: _amounts,
-            deposits: _deposits,
-            callData: _callData,
-            toChain: _toChain,
-            status: SettlementStatus.Success,
-            gasToBridgeOut: userFeeInfo.gasToBridgeOut
-        });
+        Settlement storage settlement = getSettlement[_getAndIncrementSettlementNonce()];
+        settlement.owner = _owner;
+        settlement.recipient = _recipient;
+        settlement.hTokens = _hTokens;
+        settlement.tokens = _tokens;
+        settlement.amounts = _amounts;
+        settlement.deposits = _deposits;
+        settlement.callData = _callData;
+        settlement.toChain = _toChain;
+        settlement.status = SettlementStatus.Success;
+        settlement.gasToBridgeOut = userFeeInfo.gasToBridgeOut;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L836-L844

*Gas Savings for `BranchBridgeAgent.performSystemCallOut`, obtained via protocol's tests: Avg 297 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  215613  |  252733  | 229141  |    53    |
| After  |  215316  |  252436  | 228844  |    53    |

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
836:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: new `Deposit` struct being created in memory and then copied into storage
837:            owner: _user,
838:            hTokens: new address[](0),
839:            tokens: new address[](0),
840:            amounts: new uint256[](0),
841:            deposits: new uint256[](0),
842:            status: DepositStatus.Success,
843:            depositedGas: _gasToBridgeOut
844:        });
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..41938bd 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -833,15 +833,14 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         _depositGas(_gasToBridgeOut);

         // Update State
-        getDeposit[_getAndIncrementDepositNonce()] = Deposit({
-            owner: _user,
-            hTokens: new address[](0),
-            tokens: new address[](0),
-            amounts: new uint256[](0),
-            deposits: new uint256[](0),
-            status: DepositStatus.Success,
-            depositedGas: _gasToBridgeOut
-        });
+        Deposit storage _depositStruct = getDeposit[_getAndIncrementDepositNonce()];
+        _depositStruct.owner = _user;
+        _depositStruct.hTokens = new address[](0);
+        _depositStruct.tokens = new address[](0);
+        _depositStruct.amounts = new uint256[](0);
+        _depositStruct.deposits = new uint256[](0);
+        _depositStruct.status = DepositStatus.Success;
+        _depositStruct.depositedGas = _gasToBridgeOut;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L882-L890

*Gas Savings for `BranchBridgeAgent.callOutSignedAndBridge`, obtained via protocol's tests: Avg 211 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  1701908 |  1701908 | 1701908 |    2     |
| After  |  1701697 |  1701697 | 1701697 |    2     |

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
882:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: new `Deposit` struct being created in memory and then copied into storage
883:            owner: _user,
884:            hTokens: hTokens,
885:            tokens: tokens,
886:            amounts: amounts,
887:            deposits: deposits,
888:            status: DepositStatus.Success,
889:            depositedGas: _gasToBridgeOut
890:        });
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..a6a07b5 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -879,15 +879,14 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         deposits[0] = _deposit;

         // Update State
-        getDeposit[_getAndIncrementDepositNonce()] = Deposit({
-            owner: _user,
-            hTokens: hTokens,
-            tokens: tokens,
-            amounts: amounts,
-            deposits: deposits,
-            status: DepositStatus.Success,
-            depositedGas: _gasToBridgeOut
-        });
+        Deposit storage _depositStruct = getDeposit[_getAndIncrementDepositNonce()];
+        _depositStruct.owner = _user;
+        _depositStruct.hTokens = hTokens;
+        _depositStruct.tokens = tokens;
+        _depositStruct.amounts = amounts;
+        _depositStruct.deposits = deposits;
+        _depositStruct.status = DepositStatus.Success;
+        _depositStruct.depositedGas = _gasToBridgeOut;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L918-L926

*Gas Savings for `BaseBranchRouter.callOutAndBridgeMultiple`, obtained via protocol's tests: Avg 195 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  431637  |  431637  | 431637  |    1     |
| After  |  431442  |  431442  | 431442  |    1     |

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
918:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({ // @audit: new `Deposit` struct being created in memory and then copied into storage
919:            owner: _user,
920:            hTokens: _hTokens,
921:            tokens: _tokens,
922:            amounts: _amounts,
923:            deposits: _deposits,
924:            status: DepositStatus.Success,
925:            depositedGas: _gasToBridgeOut
926:        });
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..452e76e 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -915,15 +915,14 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         _depositGas(_gasToBridgeOut);

         // Update State
-        getDeposit[_getAndIncrementDepositNonce()] = Deposit({
-            owner: _user,
-            hTokens: _hTokens,
-            tokens: _tokens,
-            amounts: _amounts,
-            deposits: _deposits,
-            status: DepositStatus.Success,
-            depositedGas: _gasToBridgeOut
-        });
+        Deposit storage _depositStruct = getDeposit[_getAndIncrementDepositNonce()];
+        _depositStruct.owner = _user;
+        _depositStruct.hTokens = _hTokens;
+        _depositStruct.tokens = _tokens;
+        _depositStruct.amounts = _amounts;
+        _depositStruct.deposits = _deposits;
+        _depositStruct.status = DepositStatus.Success;
+        _depositStruct.depositedGas = _gasToBridgeOut;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L416-L424

*Gas Savings for `RootBridgeAgentExecutor.executeSignedWithDepositMultiple`, obtained via protocol's tests: Avg 48 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  637630  |  786912  | 510799  |    3     |
| After  |  637582  |  786864  | 510751  |    3     |

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol
416:        dParams = DepositMultipleParams({ // @audit: new `DepositMultipleParams` struct being in memory and then copied into the already create `dParams` struct. Double memory allocation.
417:            numberOfAssets: numOfAssets,
418:            depositNonce: nonce,
419:            hTokens: hTokens,
420:            tokens: tokens,
421:            amounts: amounts,
422:            deposits: deposits,
423:            toChain: toChain
424:        });
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgentExecutor.sol b/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
index f49aac0..e72e11a 100644
--- a/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
+++ b/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
@@ -413,16 +413,15 @@ contract RootBridgeAgentExecutor is Ownable {
         }

         //Save Deposit Multiple Params
-        dParams = DepositMultipleParams({
-            numberOfAssets: numOfAssets,
-            depositNonce: nonce,
-            hTokens: hTokens,
-            tokens: tokens,
-            amounts: amounts,
-            deposits: deposits,
-            toChain: toChain
-        });
+        dParams.numberOfAssets = numOfAssets;
+        dParams.depositNonce = nonce;
+        dParams.hTokens = hTokens;
+        dParams.tokens = tokens;
+        dParams.amounts = amounts;
+        dParams.deposits = deposits;
+        dParams.toChain = toChain;

         RootBridgeAgent(payable(msg.sender)).bridgeInMultiple(_recipient, dParams, _fromChain);
     }
 }
```

## Re-use memory arrays to avoid extra memory allocation
In the instances below we can avoid creating memory arrays, thus incurring memory allocation costs, by re-using arrays of the same `type`. I.e. instead of creating two `address[]` arrays we can only create one and and strategically modify the value(s) in that one array as we need it. This allows us to use one memory array for multiple purposes.

Total Instances: `3`

Estimated Gas Saved: `494`

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L494-L504

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L531-L542

*Gas Savings for `RootBridgeAgent.callOutAndBridge`, obtained via protocol's tests: Avg 159 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  484589  |  549247  | 489495  |    11    |
| After  |  484430  |  549048  | 489310  |    11    |

**Note: This optimization is dependent on the optimization in [this finding](#use-struct-dot-notation-to-avoid-memory-allocation) and therefore will be benchmarked using the the optimizations in that finding as the baseline**.

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol
494:        address[] memory hTokens = new address[](1);
495:        hTokens[0] = _hToken;
496:        address[] memory tokens = new address[](1);
497:        tokens[0] = _token;
498:        uint256[] memory amounts = new uint256[](1);
499:        amounts[0] = _amount;
500:        uint256[] memory deposits = new uint256[](1);
501:        deposits[0] = _deposit;
502:
503:        //Call createSettlement
504:        _createMultipleSettlement(_owner, _recipient, hTokens, tokens, amounts, deposits, _callData, _toChain);

531:        getSettlement[_getAndIncrementSettlementNonce()] = Settlement({
532:            owner: _owner,
533:            recipient: _recipient,
534:            hTokens: _hTokens,
535:            tokens: _tokens,
536:            amounts: _amounts,
537:            deposits: _deposits,
538:            callData: _callData,
539:            toChain: _toChain,
540:            status: SettlementStatus.Success,
541:            gasToBridgeOut: userFeeInfo.gasToBridgeOut
542:        });
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgent.sol b/src/ulysses-omnichain/RootBridgeAgent.sol
index 34f4286..9d2764f 100644
--- a/src/ulysses-omnichain/RootBridgeAgent.sol
+++ b/src/ulysses-omnichain/RootBridgeAgent.sol
@@ -491,17 +491,30 @@ contract RootBridgeAgent is IRootBridgeAgent {
         uint24 _toChain
     ) internal {
         //Cast to Dynamic
-        address[] memory hTokens = new address[](1);
-        hTokens[0] = _hToken;
-        address[] memory tokens = new address[](1);
-        tokens[0] = _token;
-        uint256[] memory amounts = new uint256[](1);
-        amounts[0] = _amount;
-        uint256[] memory deposits = new uint256[](1);
-        deposits[0] = _deposit;
-
-        //Call createSettlement
-        _createMultipleSettlement(_owner, _recipient, hTokens, tokens, amounts, deposits, _callData, _toChain);
+        address[] memory addressArray = new address[](1);
+        uint256[] memory uintArray = new uint256[](1);
+
+        // Update State
+        Settlement storage settlement = getSettlement[_getAndIncrementSettlementNonce()];
+        settlement.owner = _owner;
+        settlement.recipient = _recipient;
+
+        addressArray[0] = _hToken;
+        settlement.hTokens = addressArray;
+
+        addressArray[0] = _token;
+        settlement.tokens = addressArray;
+
+        uintArray[0] = _amount;
+        settlement.amounts = uintArray;
+
+        uintArray[0] = _deposit;
+        settlement.deposits = uintArray;
+
+        settlement.callData = _callData;
+        settlement.toChain = _toChain;
+        settlement.status = SettlementStatus.Success;
+        settlement.gasToBridgeOut = userFeeInfo.gasToBridgeOut;
     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L836-L844

*Gas Savings for `BranchBridgeAgent.performSystemCallOut`, obtained via protocol's tests: Avg 172 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  215613  |  252733  | 229141  |    53    |
| After  |  215441  |  252561  | 228969  |    53    |

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
836:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({
837:            owner: _user,
838:            hTokens: new address[](0), 
839:            tokens: new address[](0), // @audit: new address array created in memory
840:            amounts: new uint256[](0),
841:            deposits: new uint256[](0), // @audit: new  uint256 array created in memory
842:            status: DepositStatus.Success,
843:            depositedGas: _gasToBridgeOut
844:        });
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..20ec14f 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -833,12 +833,14 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         _depositGas(_gasToBridgeOut);

         // Update State
+        address[] memory addressArray = new address[](0);
+        uint256[] memory uintArray = new uint256[](0);
         getDeposit[_getAndIncrementDepositNonce()] = Deposit({
             owner: _user,
-            hTokens: new address[](0),
-            tokens: new address[](0),
-            amounts: new uint256[](0),
-            deposits: new uint256[](0),
+            hTokens: addressArray,
+            tokens: addressArray,
+            amounts: uintArray,
+            deposits: uintArray,
             status: DepositStatus.Success,
             depositedGas: _gasToBridgeOut
         });
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L872-L890

*Gas Savings for `BranchBridgeAgent.callOutSignedAndBridge`, obtained via protocol's tests: Avg 163 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  1701697 |  1701697 | 1701697 |    2     |
| After  |  1701534 |  1701534 | 1701534 |    2     |

**Note: This optimization is dependent on the optimization in [this finding](#use-struct-dot-notation-to-avoid-memory-allocation) and therefore will be benchmarked using the the optimizations in that finding as the baseline**.

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
872:        address[] memory hTokens = new address[](1);
873:        hTokens[0] = _hToken;
874:        address[] memory tokens = new address[](1); // @audit: 2nd address array created in memory
875:        tokens[0] = _token;
876:        uint256[] memory amounts = new uint256[](1);
877:        amounts[0] = _amount;
878:        uint256[] memory deposits = new uint256[](1); // @audit: second uint256 array created in memory
879:        deposits[0] = _deposit;
880:
881:        // Update State
882:        getDeposit[_getAndIncrementDepositNonce()] = Deposit({
883:            owner: _user,
884:            hTokens: hTokens,
885:            tokens: tokens,
886:            amounts: amounts,
887:            deposits: deposits,
888:            status: DepositStatus.Success,
889:            depositedGas: _gasToBridgeOut
890:        });
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..379b0bc 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -869,25 +869,22 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         _depositGas(_gasToBridgeOut);

         // Cast to dynamic memory array
-        address[] memory hTokens = new address[](1);
-        hTokens[0] = _hToken;
-        address[] memory tokens = new address[](1);
-        tokens[0] = _token;
-        uint256[] memory amounts = new uint256[](1);
-        amounts[0] = _amount;
-        uint256[] memory deposits = new uint256[](1);
-        deposits[0] = _deposit;
+        address[] memory addressArray = new address[](1);
+        uint256[] memory uintArray = new uint256[](1);

         // Update State
-        getDeposit[_getAndIncrementDepositNonce()] = Deposit({
-            owner: _user,
-            hTokens: hTokens,
-            tokens: tokens,
-            amounts: amounts,
-            deposits: deposits,
-            status: DepositStatus.Success,
-            depositedGas: _gasToBridgeOut
-        });
+        Deposit storage _depositStruct = getDeposit[_getAndIncrementDepositNonce()];
+        _depositStruct.owner = _user;
+        addressArray[0] = _hToken;
+        _depositStruct.hTokens = addressArray;
+        addressArray[0] = _token;
+        _depositStruct.tokens = addressArray;
+        uintArray[0] = _amount;
+        _depositStruct.amounts = uintArray;
+        uintArray[0] = _deposit;
+        _depositStruct.deposits = uintArray;
+        _depositStruct.status = DepositStatus.Success;
+        _depositStruct.depositedGas = _gasToBridgeOut;
     }
```

## Usage of `uint`/`int` smaller than 32 bytes (256 bits) incurs overhead
The EVM operates with 32 byte words. Therefore, if you declare state variables less than 32 bytes the EVM will need to perform extra operations to cast your value to the specified size.

**Note: This instance was missed in the bot race**.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L16-L24

*Gas Savings for `MockBranchBridgeAgent.anyFallback`, obtained via protocol's tests: Avg 147 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  42579   |  44579   | 40444   |    6     |
| After  |  42407   |  44407   | 40297   |    6     |

*Note: `getDeposit[_depositNonce].depositedGas` is accessed multiple times in the above function.*

### Increase the `uint` type of `depositedGas` since it is not packed with any other state variables
```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
16:  struct Deposit {
17:      uint128 depositedGas; // @audit: occupies its own slot; NOT packed with other variables
18:      address owner;
19:      DepositStatus status;
20:      address[] hTokens;
21:      address[] tokens;
22:      uint256[] amounts;
23:      uint256[] deposits;
}
```
```diff
diff --git a/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol b/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
index 574d075..6340017 100644
--- a/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
@@ -14,7 +14,7 @@ enum DepositStatus {
 }

 struct Deposit {
-    uint128 depositedGas;
+    uint96 depositedGas;
     address owner;
     DepositStatus status;
     address[] hTokens;
@@ -399,3 +399,4 @@ interface IBranchBridgeAgent is IApp {
     error UnrecognizedCallerNotRouter();
     error UnrecognizedBridgeAgentExecutor();
 }
```

## Multiple accesses of a mapping/array should use a local variable cache
Caching a mapping's value in a storage pointer when the value is accessed multiple times saves ~40 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it.

To achieve this, declare a storage pointer for the variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `myMap[id]`, save its reference via a storage pointer: `MyMap storage _myMap = myMap[id]` and use the pointer instead.

Total Instances: `2`

Estimated Gas Saved: `477`

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L483-L502

*Gas Savings for `UniswapV3Staker.onERC721Received`, obtained via protocol's tests: Med 119 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  131975  |  247596  | 126285  |    56    |
| After  |  131856  |  247477  | 125764  |    56    |

*Note: The `Avg` column for this function changes and so the `Median` column is used for benchmarking.*

### Cache storage pointers for `incentives[incentiveId]` and `deposits[tokenId]`
```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol
483:        if (incentives[incentiveId].totalRewardUnclaimed == 0) revert NonExistentIncentiveError();
...
491:        address tokenOwner = deposits[tokenId].owner;
...
501:        deposits[tokenId].stakedTimestamp = uint40(block.timestamp);
502:        incentives[incentiveId].numberOfStakes++;
```
```diff
diff --git a/src/uni-v3-staker/UniswapV3Staker.sol b/src/uni-v3-staker/UniswapV3Staker.sol
index 5970379..696c9fe 100644
--- a/src/uni-v3-staker/UniswapV3Staker.sol
+++ b/src/uni-v3-staker/UniswapV3Staker.sol
@@ -479,8 +479,9 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {
         IncentiveKey memory key = IncentiveKey({pool: pool, startTime: IncentiveTime.computeStart(block.timestamp)});

         bytes32 incentiveId = IncentiveId.compute(key);
-
-        if (incentives[incentiveId].totalRewardUnclaimed == 0) revert NonExistentIncentiveError();
+
+        Incentive storage incentive = incentives[incentiveId];
+        if (incentive.totalRewardUnclaimed == 0) revert NonExistentIncentiveError();

         if (uint24(tickUpper - tickLower) < poolsMinimumWidth[pool]) revert RangeTooSmallError();
         if (liquidity == 0) revert NoLiquidityError();
@@ -488,7 +489,8 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {
         stakedIncentiveKey[tokenId] = key;

         // If user not attached to gauge, attach
-        address tokenOwner = deposits[tokenId].owner;
+        Deposit storage deposit = deposits[tokenId];
+        address tokenOwner = deposit.owner;
         if (tokenOwner == address(0)) revert TokenNotDeposited();

         UniswapV3Gauge gauge = gauges[pool]; // saves another SLOAD if no tokenId is attached
@@ -498,8 +500,8 @@ contract UniswapV3Staker is IUniswapV3Staker, Multicallable {
             gauge.attachUser(tokenOwner);
         }

-        deposits[tokenId].stakedTimestamp = uint40(block.timestamp);
-        incentives[incentiveId].numberOfStakes++;
+        deposit.stakedTimestamp = uint40(block.timestamp);
+        incentive.numberOfStakes++;

         (, uint160 secondsPerLiquidityInsideX128,) = pool.snapshotCumulativesInside(tickLower, tickUpper);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L327-L411

*Gas Savings for `MockBranchBridgeAgent.retryDeposit`, obtained via protocol's tests: Avg 358 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  34216   |  34216   | 24263   |    3     |
| After  |  33676   |  33676   | 23905   |    3     |

### Cache storage pointer for `getDeposit[_depositNonce]`
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
327:        if (getDeposit[_depositNonce].owner != msg.sender) revert NotDepositOwner();
328:
...
332:        if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {
333:            if (_isSigned) {
334:                packedData = abi.encodePacked(
335:                    bytes1(0x05),
336:                    msg.sender,
337:                    _depositNonce,
338:                    getDeposit[_depositNonce].hTokens[0],
339:                    getDeposit[_depositNonce].tokens[0],
340:                    getDeposit[_depositNonce].amounts[0],
341:                    _normalizeDecimals(
342:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
343:                    ),
344:                    _toChain,
345:                    _params,
346:                    msg.value.toUint128(),
347:                    _remoteExecutionGas
348:                );
349:            } else {
350:                packedData = abi.encodePacked(
351:                    bytes1(0x02),
352:                    _depositNonce,
353:                    getDeposit[_depositNonce].hTokens[0],
354:                    getDeposit[_depositNonce].tokens[0],
355:                    getDeposit[_depositNonce].amounts[0],
356:                    _normalizeDecimals(
357:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
358:                    ),
359:                    _toChain,
360:                    _params,
361:                    msg.value.toUint128(),
362:                    _remoteExecutionGas
363:                );
364:            }
365:        } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {
366:            //Nonce
367:            uint32 nonce = _depositNonce;
368:
369:            if (_isSigned) {
370:                packedData = abi.encodePacked(
371:                    bytes1(0x06),
372:                    msg.sender,
373:                    uint8(getDeposit[_depositNonce].hTokens.length),
374:                    nonce,
375:                    getDeposit[nonce].hTokens,
376:                    getDeposit[nonce].tokens,
377:                    getDeposit[nonce].amounts,
378:                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
379:                    _toChain,
380:                    _params,
381:                    msg.value.toUint128(),
382:                    _remoteExecutionGas
383:                );
384:            } else {
385:                packedData = abi.encodePacked(
386:                    bytes1(0x03),
387:                    uint8(getDeposit[nonce].hTokens.length),
388:                    _depositNonce,
389:                    getDeposit[nonce].hTokens,
390:                    getDeposit[nonce].tokens,
391:                    getDeposit[nonce].amounts,
392:                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
393:                    _toChain,
394:                    _params,
395:                    msg.value.toUint128(),
396:                    _remoteExecutionGas
397:                );
398:            }
399:        }
...
407:        //Ensure success Status
408:        getDeposit[_depositNonce].status = DepositStatus.Success;
409:
410:        //Update Deposited Gas
411:        getDeposit[_depositNonce].depositedGas = msg.value.toUint128();
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..c5caa29 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -324,22 +324,23 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         uint24 _toChain
     ) external payable lock requiresFallbackGas {
         //Check if deposit belongs to message sender
-        if (getDeposit[_depositNonce].owner != msg.sender) revert NotDepositOwner();
+        Deposit storage _depositStruct = getDeposit[_depositNonce];
+        if (_depositStruct.owner != msg.sender) revert NotDepositOwner();

         //Encode Data for cross-chain call.
         bytes memory packedData;

-        if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {
+        if (uint8(_depositStruct.hTokens.length) == 1) {
             if (_isSigned) {
                 packedData = abi.encodePacked(
                     bytes1(0x05),
                     msg.sender,
                     _depositNonce,
-                    getDeposit[_depositNonce].hTokens[0],
-                    getDeposit[_depositNonce].tokens[0],
-                    getDeposit[_depositNonce].amounts[0],
+                    _depositStruct.hTokens[0],
+                    _depositStruct.tokens[0],
+                    _depositStruct.amounts[0],
                     _normalizeDecimals(
-                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
+                        _depositStruct.deposits[0], ERC20(_depositStruct.tokens[0]).decimals()
                     ),
                     _toChain,
                     _params,
@@ -350,11 +351,11 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                 packedData = abi.encodePacked(
                     bytes1(0x02),
                     _depositNonce,
-                    getDeposit[_depositNonce].hTokens[0],
-                    getDeposit[_depositNonce].tokens[0],
-                    getDeposit[_depositNonce].amounts[0],
+                    _depositStruct.hTokens[0],
+                    _depositStruct.tokens[0],
+                    _depositStruct.amounts[0],
                     _normalizeDecimals(
-                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
+                        _depositStruct.deposits[0], ERC20(_depositStruct.tokens[0]).decimals()
                     ),
                     _toChain,
                     _params,
@@ -362,20 +363,18 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                     _remoteExecutionGas
                 );
             }
-        } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {
-            //Nonce
-            uint32 nonce = _depositNonce;
+        } else if (uint8(_depositStruct.hTokens.length) > 1) {

             if (_isSigned) {
                 packedData = abi.encodePacked(
                     bytes1(0x06),
                     msg.sender,
-                    uint8(getDeposit[_depositNonce].hTokens.length),
-                    nonce,
-                    getDeposit[nonce].hTokens,
-                    getDeposit[nonce].tokens,
-                    getDeposit[nonce].amounts,
-                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
+                    uint8(_depositStruct.hTokens.length),
+                    _depositNonce,
+                    _depositStruct.hTokens,
+                    _depositStruct.tokens,
+                    _depositStruct.amounts,
+                    _normalizeDecimalsMultiple(_depositStruct.deposits, _depositStruct.tokens),
                     _toChain,
                     _params,
                     msg.value.toUint128(),
@@ -384,12 +383,12 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
             } else {
                 packedData = abi.encodePacked(
                     bytes1(0x03),
-                    uint8(getDeposit[nonce].hTokens.length),
+                    uint8(_depositStruct.hTokens.length),
                     _depositNonce,
-                    getDeposit[nonce].hTokens,
-                    getDeposit[nonce].tokens,
-                    getDeposit[nonce].amounts,
-                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
+                    _depositStruct.hTokens,
+                    _depositStruct.tokens,
+                    _depositStruct.amounts,
+                    _normalizeDecimalsMultiple(_depositStruct.deposits, _depositStruct.tokens),
                     _toChain,
                     _params,
                     msg.value.toUint128(),
@@ -405,10 +404,10 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
         _depositGas(msg.value.toUint128());

         //Ensure success Status
-        getDeposit[_depositNonce].status = DepositStatus.Success;
+        _depositStruct.status = DepositStatus.Success;

         //Update Deposited Gas
-        getDeposit[_depositNonce].depositedGas = msg.value.toUint128();
+        _depositStruct.depositedGas = msg.value.toUint128();

         //Perform Call
         _performCall(packedData);
```

## Cache calculation to avoid performing computation multiple times
Understanding that each computation done in the EVM consumes gas, it would behoove us to cache computations that are known to be performed multiple times. Doing so will allow us to only incur the gas cost of said computation once, instead of multiple times. If the computation is intensive, or in a loop, this optimization can save a fair amount of gas.

Total Instances: `2`

Estimated Gas Saved: `697`

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L364-L408

*Gas Savings for `RootBridgeAgentExecutor.executeSignedWithDepositMultiple`, obtained via protocol's tests: Avg 348 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  637630  |  786912  | 510799  |    3     |
| After  |  637281  |  786564  | 510451  |    3     |

### Cache `PARAMS_START + i` to avoid performing computation multiple times
```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol
364:        for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {
367:            //Parse Params
368:            hTokens[i] = address(
369:                uint160(
370:                    bytes20(
371:                        bytes32(
372:                            _dParams[
373:                                PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * i) + 12:
374:                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (PARAMS_START + i))
...
379:            tokens[i] = address(
380:                uint160(
381:                    bytes20(
382:                        _dParams[
383:                            PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:
384:                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)
...
390:            amounts[i] = uint256(
391:                bytes32(
392:                    _dParams[
393:                        PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
394:                            PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets)
395:                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
...
400:            deposits[i] = uint256(
401:                bytes32(
402:                    _dParams[
403:                        PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
404:                            PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets)
405:                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
```
```diff
diff --git a/src/ulysses-omnichain/RootBridgeAgentExecutor.sol b/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
--- a/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
+++ b/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
@@ -362,6 +362,7 @@ contract RootBridgeAgentExecutor is Ownable {
         uint256[] memory deposits = new uint256[](numOfAssets);

         for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {
+            uint256 commonEnd = PARAMS_START + i;
             //Parse Params
             hTokens[i] = address(
                 uint160(
@@ -369,7 +370,7 @@ contract RootBridgeAgentExecutor is Ownable {
                         bytes32(
                             _dParams[
                                 PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * i) + 12:
-                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (PARAMS_START + i))
+                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (commonEnd))
                             ]
                         )
                     )
@@ -381,7 +382,7 @@ contract RootBridgeAgentExecutor is Ownable {
                     bytes20(
                         _dParams[
                             PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:
-                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)
+                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(commonEnd + numOfAssets)
                         ]
                     )
                 )
@@ -392,7 +393,7 @@ contract RootBridgeAgentExecutor is Ownable {
                     _dParams[
                         PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
                             PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets)
-                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
+                                + PARAMS_ENTRY_SIZE * uint16(commonEnd)
                     ]
                 )
             );
@@ -402,7 +403,7 @@ contract RootBridgeAgentExecutor is Ownable {
                     _dParams[
                         PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
                             PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets)
-                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
+                                + PARAMS_ENTRY_SIZE * uint16(commonEnd)
                     ]
                 )
             );
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L580-L621

*Gas Savings for `MockBranchBridgeAgent.clearTokens`, obtained via protocol's tests: Avg 349 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  122192  |  122192  | 122192  |    1     |
| After  |  121843  |  121843  | 121843  |    1     |

### Cache `PARAMS_START + i` to avoid performing computation multiple times
```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol
580:        for (uint256 i = 0; i < numOfAssets;) {
581:            //Parse Params
582:            _hTokens[i] = address(
583:                uint160(
584:                    bytes20(
585:                        bytes32(
586:                            _sParams[
587:                                PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * i) + 12:
588:                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (PARAMS_START + i))
...
594:            _tokens[i] = address(
595:                uint160(
596:                    bytes20(
597:                        _sParams[
598:                            PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:
599:                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)
...
604:            _amounts[i] = uint256(
605:                bytes32(
606:                    _sParams[
607:                        PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
608:                            PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets)
609:                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
...
613:            _deposits[i] = uint256(
614:                bytes32(
615:                    _sParams[
616:                        PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
617:                            PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets)
618:                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
```
```diff
diff --git a/src/ulysses-omnichain/BranchBridgeAgent.sol b/src/ulysses-omnichain/BranchBridgeAgent.sol
index 929c621..ab0f041 100644
--- a/src/ulysses-omnichain/BranchBridgeAgent.sol
+++ b/src/ulysses-omnichain/BranchBridgeAgent.sol
@@ -578,6 +578,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {

         //Transfer token to recipient
         for (uint256 i = 0; i < numOfAssets;) {
+            uint256 commonEnd = PARAMS_START + i;
             //Parse Params
             _hTokens[i] = address(
                 uint160(
@@ -585,7 +586,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                         bytes32(
                             _sParams[
                                 PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * i) + 12:
-                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (PARAMS_START + i))
+                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (commonEnd))
                             ]
                         )
                     )
@@ -596,7 +597,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                     bytes20(
                         _sParams[
                             PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:
-                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)
+                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(commonEnd + numOfAssets)
                         ]
                     )
                 )
@@ -606,7 +607,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                     _sParams[
                         PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
                             PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets)
-                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
+                                + PARAMS_ENTRY_SIZE * uint16(commonEnd)
                     ]
                 )
             );
@@ -615,7 +616,7 @@ contract BranchBridgeAgent is IBranchBridgeAgent {
                     _sParams[
                         PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):
                             PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets)
-                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)
+                                + PARAMS_ENTRY_SIZE * uint16(commonEnd)
                     ]
                 )
             );
```

## Using `SafeMath-like` functions with compiler version `^0.8.0` wastes gas
As of solidity version `^0.8.0` the compiler will throw an error when an underflow/overflow occurs during arithmetic. In the instance below, the two functions `add256` and `sub256` perform the same functionality as the `SafeMath` library, which is unnecessary since the compiler version being used is `0.8.18`. Remove these functions to save the gas needed to call the internal function and perform the `require` statement check in the 5 functions listed below.

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L525-L534

*The following functions use the `SafeMath` functions below: `propose`, `cancel`, `queue`, `state`, and `castVoteInternal`*
```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
525:    function add256(uint256 a, uint256 b) internal pure returns (uint256) {
526:        uint256 c = a + b;
527:        require(c >= a, "addition overflow");
528:        return c;
529:    }
530:
531:    function sub256(uint256 a, uint256 b) internal pure returns (uint256) {
532:        require(b <= a, "subtraction underflow");
533:        return a - b;
534:    }
```
```diff
diff --git a/src/governance/GovernorBravoDelegateMaia.sol b/src/governance/GovernorBravoDelegateMaia.sol
index 066b81b..fbb98bf 100644
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -112,7 +112,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");
         // Allow addresses above proposal threshold and whitelisted addresses to propose
         require(
-            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
+            govToken.getPriorVotes(msg.sender, block.number - 1) > getProposalThresholdAmount()
                 || isWhitelisted(msg.sender),
             "GovernorBravo::propose: proposer votes below proposal threshold"
         );
@@ -136,8 +136,8 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
             );
         }

-        uint256 startBlock = add256(block.number, votingDelay);
-        uint256 endBlock = add256(startBlock, votingPeriod);
+        uint256 startBlock = block.number + votingDelay;
+        uint256 endBlock = startBlock + votingPeriod;

         proposalCount++;
         uint256 newProposalID = proposalCount;
@@ -177,7 +177,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
             "GovernorBravo::queue: proposal can only be queued if it is succeeded"
         );
         Proposal storage proposal = proposals[proposalId];
-        uint256 eta = add256(block.timestamp, timelock.delay());
+        uint256 eta = block.timestamp + timelock.delay();
         for (uint256 i = 0; i < proposal.targets.length; i++) {
             queueOrRevertInternal(
                 proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], eta
@@ -235,13 +235,13 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
             // Whitelisted proposers can't be canceled for falling below proposal threshold
             if (isWhitelisted(proposal.proposer)) {
                 require(
-                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
+                    (govToken.getPriorVotes(proposal.proposer, block.number - 1) < getProposalThresholdAmount())
                         && msg.sender == whitelistGuardian,
                     "GovernorBravo::cancel: whitelisted proposer"
                 );
             } else {
                 require(
-                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
+                    (govToken.getPriorVotes(proposal.proposer, block.number - 1) < getProposalThresholdAmount()),
                     "GovernorBravo::cancel: proposer above threshold"
                 );
             }
@@ -311,7 +311,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
             return ProposalState.Succeeded;
         } else if (proposal.executed) {
             return ProposalState.Executed;
-        } else if (block.timestamp >= add256(proposal.eta, timelock.GRACE_PERIOD())) {
+        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
             return ProposalState.Expired;
         } else {
             return ProposalState.Queued;
@@ -367,11 +367,11 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         uint96 votes = govToken.getPriorVotes(voter, proposal.startBlock);

         if (support == 0) {
-            proposal.againstVotes = add256(proposal.againstVotes, votes);
+            proposal.againstVotes = proposal.againstVotes + votes;
         } else if (support == 1) {
-            proposal.forVotes = add256(proposal.forVotes, votes);
+            proposal.forVotes = proposal.forVotes + votes;
         } else if (support == 2) {
-            proposal.abstainVotes = add256(proposal.abstainVotes, votes);
+            proposal.abstainVotes = proposal.abstainVotes + votes;
         }

         receipt.hasVoted = true;
@@ -522,17 +522,6 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
         emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
     }

-    function add256(uint256 a, uint256 b) internal pure returns (uint256) {
-        uint256 c = a + b;
-        require(c >= a, "addition overflow");
-        return c;
-    }
-
-    function sub256(uint256 a, uint256 b) internal pure returns (uint256) {
-        require(b <= a, "subtraction underflow");
-        return a - b;
-    }
-
     function getChainIdInternal() internal view returns (uint256) {
         uint256 chainId;
         assembly {
```

## A mapping is more efficient than an array
Fetching data from an array is more expensive than fetching data from a mapping. Fetching data from an array will require iterating over the array until ou reach your desired data. When using a mapping you only need to know the `key` in order to fetch the data from the exact slot it is stored in. [Source](https://twitter.com/pcaversaccio/status/1464523336730480640)

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L29

*Gas Savings for `MockBaseV2GaugeManager.newEpoch(uint256,uint256)`, obtained via protocol's tests: Avg 281 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  1580    |  4006    | 1654    |    35    |
| After  |  1305    |  3233    | 1373    |    35    |

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol
29:    BaseV2GaugeFactory[] public gaugeFactories;
```
```diff
diff --git a/src/gauges/factories/BaseV2GaugeManager.sol b/src/gauges/factories/BaseV2GaugeManager.sol
index 298b76c..6f8ee73 100644
--- a/src/gauges/factories/BaseV2GaugeManager.sol
+++ b/src/gauges/factories/BaseV2GaugeManager.sol
@@ -25,8 +25,9 @@ contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {
     /// @inheritdoc IBaseV2GaugeManager
     bHermesBoost public immutable bHermesGaugeBoost;

-    /// @inheritdoc IBaseV2GaugeManager
-    BaseV2GaugeFactory[] public gaugeFactories;
+    mapping(uint256 => BaseV2GaugeFactory) gaugeFactoriesMap;
+
+    uint256 gaugeFactoriesCount;

     /// @inheritdoc IBaseV2GaugeManager
     mapping(BaseV2GaugeFactory => uint256) public gaugeFactoryIds;
@@ -47,9 +48,25 @@ contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {
         bHermesGaugeBoost = _bHermes.gaugeBoost();
     }

+    // @audit: @inheritdoc IBaseV2GaugeManager
+    function gaugeFactories(uint256 pos) external view returns (BaseV2GaugeFactory) {
+        return gaugeFactoriesMap[pos];
+    }
+
     /// @inheritdoc IBaseV2GaugeManager
     function getGaugeFactories() external view returns (BaseV2GaugeFactory[] memory) {
-        return gaugeFactories;
+        uint256 length = gaugeFactoriesCount;
+        BaseV2GaugeFactory[] memory _gaugeFactories = new BaseV2GaugeFactory[](length);
+
+        for (uint256 i; i < length; ) {
+            _gaugeFactories[i] = gaugeFactoriesMap[i];
+
+            unchecked {
+                ++i;
+            }
+        }
+
+        return _gaugeFactories;
     }

     /*//////////////////////////////////////////////////////////////
@@ -58,11 +75,9 @@ contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {

     /// @inheritdoc IBaseV2GaugeManager
     function newEpoch() external {
-        BaseV2GaugeFactory[] storage _gaugeFactories = gaugeFactories;
-
-        uint256 length = _gaugeFactories.length;
+        uint256 length = gaugeFactoriesCount;
         for (uint256 i = 0; i < length;) {
-            if (activeGaugeFactories[_gaugeFactories[i]]) _gaugeFactories[i].newEpoch();
+            if (activeGaugeFactories[gaugeFactoriesMap[i]]) gaugeFactoriesMap[i].newEpoch();

             unchecked {
                 i++;
@@ -72,13 +87,11 @@ contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {

     /// @inheritdoc IBaseV2GaugeManager
     function newEpoch(uint256 start, uint256 end) external {
-        BaseV2GaugeFactory[] storage _gaugeFactories = gaugeFactories;
-
-        uint256 length = _gaugeFactories.length;
+        uint256 length = gaugeFactoriesCount;
         if (end > length) end = length;

         for (uint256 i = start; i < end;) {
-            if (activeGaugeFactories[_gaugeFactories[i]]) _gaugeFactories[i].newEpoch();
+            if (activeGaugeFactories[gaugeFactoriesMap[i]]) gaugeFactoriesMap[i].newEpoch();

             unchecked {
                 i++;
@@ -109,9 +122,11 @@ contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {
     /// @inheritdoc IBaseV2GaugeManager
     function addGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {
         if (activeGaugeFactories[gaugeFactory]) revert GaugeFactoryAlreadyExists();
-
-        gaugeFactoryIds[gaugeFactory] = gaugeFactories.length;
-        gaugeFactories.push(gaugeFactory);
+
+        uint256 pos = gaugeFactoriesCount;
+        gaugeFactoryIds[gaugeFactory] = pos;
+        gaugeFactoriesMap[pos] = gaugeFactory;
+        gaugeFactoriesCount = pos + 1;
         activeGaugeFactories[gaugeFactory] = true;

         emit AddedGaugeFactory(address(gaugeFactory));
@@ -119,10 +134,10 @@ contract BaseV2GaugeManager is Ownable, IBaseV2GaugeManager {

     /// @inheritdoc IBaseV2GaugeManager
     function removeGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {
-        if (!activeGaugeFactories[gaugeFactory] || gaugeFactories[gaugeFactoryIds[gaugeFactory]] != gaugeFactory) {
+        if (!activeGaugeFactories[gaugeFactory] || gaugeFactoriesMap[gaugeFactoryIds[gaugeFactory]] != gaugeFactory) {
             revert NotActiveGaugeFactory();
         }
-        delete gaugeFactories[gaugeFactoryIds[gaugeFactory]];
+        delete gaugeFactoriesMap[gaugeFactoryIds[gaugeFactory]];
         delete gaugeFactoryIds[gaugeFactory];
         delete activeGaugeFactories[gaugeFactory];
```

## Remove event to avoid emitting redundant data
In the instance below, both events emit the data `assets[i]`. Since redundant data is being emitted in the `AssetRemoved` event, we should remove that event to save at least one `Glog (375 gas)`. These gas savings can compound as the events are being emitted within a loop.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L98-L99

```solidity
File: src/ulysses-amm/UlyssesToken.sol
98:            emit AssetRemoved(assets[i]); // @audit: 1st emit of `assets[i]`
99:            emit AssetAdded(assets[i], _weights[i]); // @audit: 2nd emit of `assets[i]`
```

## Cache calldata pointers for complex types to avoid offset calculations
The function parameters in the following instances are complex types (Arrays of structs) and thus will result in more complex offset calculations to retrieve specific data from calldata. We can avoid peforming some of these offset calculations by instantiating calldata pointers.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L48-L49

*Gas Savings for `VirtualAccount.call`, obtained via protocol's tests: Avg 142 gas*

|        |    Med   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  6061    |  28591   |  14803  |    18    |
| After  |  5912    |  28442   |  14661  |    18    |

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol
48:        for (uint256 i = 0; i < calls.length; i++) {
49:            (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);
```
```diff
diff --git a/src/ulysses-omnichain/VirtualAccount.sol b/src/ulysses-omnichain/VirtualAccount.sol
index e7f513c..456bcd7 100644
--- a/src/ulysses-omnichain/VirtualAccount.sol
+++ b/src/ulysses-omnichain/VirtualAccount.sol
@@ -46,7 +46,8 @@ contract VirtualAccount is IVirtualAccount {
         blockNumber = block.number;
         returnData = new bytes[](calls.length);
         for (uint256 i = 0; i < calls.length; i++) {
-            (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);
+            Call calldata _call = calls[i];
+            (bool success, bytes memory data) = _call.target.call(_call.callData);
             if (!success) revert CallFailed();
             returnData[i] = data;
         }
```