## Summary

| |Issue|Instances|
|-|:-|:-:|
| [Low-1](#Low-1) | Hash Collisions With Multiple Variable Length Arguments | 1 |
| [Low-2](#Low-2) | Loss of precision | 26 |
| [Low-3](#Low-3) | Use `safeTransferOwnership` instead of `transferOwnership` function | 3 |
| [Low-4](#Low-4) | Unbounded loop | 27 |
| [Low-5](#Low-5) | `decimals()` not part of ERC20 standard | 20 |
| [Low-6](#Low-6) | Use `safetransfer` Instead Of `transfer` | 8 |
| [Low-7](#Low-7) | Use `_safeMint` instead of `_mint` | 19 |
| [Low-8](#Low-8) | Division before multiplication causing significant loss of precision | 32 |
| [Low-9](#Low-9) | `safeApprove()` is deprecated | 13 |
| [Low-10](#Low-10) | Use `require` instead of `assert` | 3 |
| [Low-11](#Low-11) | Signatures vulnerable to malleability attacks | 2 |
| [Low-12](#Low-12) | UNSAFE `decimals()` CALL FOR ASSET TOKENS THAT DO NOT IMPLEMENT `decimals()` | 20 |
| [Low-13](#Low-13) | Minting tokens to the zero address should be avoided | 19 |
| [Low-14](#Low-14) | Functions return bool true but cannot return false | 4 |
| [Low-15](#Low-15) | PREVENT DIV BY 0 | 50 |
| [Low-16](#Low-16) | AVOID TRANSFER()/SEND() AS REENTRANCY MITIGATIONS | 8 |
| [Low-17](#Low-17) | ECRECOVER MAY RETURN EMPTY ADDRESS | 2 |
| [Low-18](#Low-18) | Use `safeERC20.safeApprove()` instead of `approve()` | 2 |
| [Low-19](#Low-19) | Inline assembly bypasses several important safety features and checks of Solidity | 28 |
| [Low-20](#Low-20) | Message call with hardcoded gas amount | 15 |
| [Low-21](#Low-21) | Solidity version 0.8.20 may not work on other chains due to `PUSH0` | 143 |
| [NC-1](#NC-1) | Return values of `approve()` not checked | 2 |
| [NC-2](#NC-2) | Constants should be defined rather than using magic numbers | 8 |
| [NC-3](#NC-3) | Using `while` for unbounded loops isn’t recommended | 1 |
| [NC-4](#NC-4) | Functions guaranteed to revert when called by normal users can be marked `payable` | 92 |
| [NC-5](#NC-5) | Floating pragma | 143 |
| [NC-6](#NC-6) | Hardcoded values can’t be changed | 72 |
| [NC-7](#NC-7) | Unspecific compiler version pragma | 154 |
| [NC-8](#NC-8) | Inconsistent spacing in comments | 484 |
| [NC-9](#NC-9) | Consider using `delete` rather than assigning zero to clear values | 17 |
| [NC-10](#NC-10) | `Revert` should be used on some functions instead of `return` | 15 |
| [NC-11](#NC-11) | Compute known value `off-chain` | 1 |
| [NC-12](#NC-12) | Use of `bytes.concat()` instead of `abi.encodePacked()` | 18 |
| [NC-13](#NC-13) | Variables need not be initialized to zero | 56 |
| [NC-14](#NC-14) | Use a single file for all system-wide constants | 86 |
| [NC-15](#NC-15) | Use `encode` instead of `encodePacked` for hashig | 1 |
| [NC-16](#NC-16) | Skip `== true` and `== false` checks | 1 |
| [NC-17](#NC-17) | Not using the named return variables anywhere in the function is confusing | 97 |
| [NC-18](#NC-18) | Use modifier instead of require/if for special `msg.sender` | 9 |
| [NC-19](#NC-19) | Unsigned divisions can be marked as `unchecked` | 9 |
| [NC-20](#NC-20) | Non-external/public function names should begin with an underscore | 61 |
| [NC-21](#NC-21) | Uppercase immutable variables | 123 |
| [NC-22](#NC-22) | Using `>/>=` without specifying an upper bound is unsafe | 11 |
| [NC-23](#NC-23) | Contract name must be in CamelCase. | 5 |
| [NC-24](#NC-24) | block.timestamp used as time proxy | 38 |
| [NC-25](#NC-25) | Empty Catch Block | 4 |
| [NC-26](#NC-26) | Consider using named mappings | 94 |
| [NC-27](#NC-27) | Consider disabling `renounceOwnership()` | 7 |
| [NC-28](#NC-28) | Interfaces should be defined in separate files from their usage | 3 |
| [NC-29](#NC-29) | decimals() is an optional method | 20 |
| [NC-30](#NC-30) | Use replace and pop instead of the delete keyword to removing an item from an array | 26 |
| [NC-31](#NC-31) | Multiples of 10 should use scientific notation | 2 |
| [NC-32](#NC-32) | Use Solidity units when possible | 4 |
| [NC-33](#NC-33) | Best practice is to prevent signature malleability | 2 |
| [NC-34](#NC-34) | Change 52 weeks to 365 days | 1 |
| [NC-35](#NC-35) | Cast to `bytes` or `bytes32` for clearer semantic meaning | 17 |
| [NC-36](#NC-36) | Consider bounding input array length | 27 |
| [NC-37](#NC-37) | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 |


### **[Low-1]** Hash Collisions With Multiple Variable Length Arguments

Using abi.encodePacked() with multiple variable length arguments can, in certain situations, lead to a hash collision. Since abi.encodePacked() packs all elements in order regardless of whether they're part of an array, you can move elements between arrays and, so long as all elements are in the same order, it will return the same encoding. In a signature verification situation, an attacker could exploit this by modifying the position of elements in a previous function call to effectively bypass authorization. See [SWC-133](https://swcregistry.io/docs/SWC-133)
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
348:        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L348



*Instances (1)*


### **[Low-2]** Loss of precision

Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
82:        activePeriod = (block.timestamp / week) * week;
127:            _period = (block.timestamp / week) * week;
136:            uint256 share = (_required * daoShare) / base;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L82


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
119:            uint256 newProtocolRewards = (pendingRewards * protocolFee) / DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L119


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
120:        uint256 earnedProtocolFees0 = (collect0 * _protocolFee) / _GLOBAL_DIVISIONER;
121:        uint256 earnedProtocolFees1 = (collect1 * _protocolFee) / _GLOBAL_DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L120


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
60:        gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
79:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
114:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L60


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
389:        return _decimals == 18 ? _amount : _amount * 1 ether / (10 ** _decimals);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L389


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
312:        if (_fees.lambda1 + _fees.lambda2 != DIVISIONER / 2) revert InvalidFee();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L312


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
677:        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;
720:            uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L677


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
1341:        return _decimals == 18 ? _amount : _amount * (10 ** _decimals) / 1 ether;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1341


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
50:            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
68:        epoch = (block.timestamp / WEEK) * WEEK;
84:        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L68


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
171:                deltaIndex = ((strategyRewardsAccrued * ONE) / supplyTokens).toUint224();
201:        uint256 supplierDelta = (supplierTokens * deltaIndex) / ONE;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L171


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
215:        shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;
261:            uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L215


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
40:        uint256 epochDay = timestamp / 86400;
57:            uint256 day = timestamp / 86400;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L40


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
44:            boostedSecondsInsideX128 = (secondsInsideX128 * 4) / 10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L44


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
97:        int24 compressed = tick / tickSpacing;
251:        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (_strategy.priceImpactPercentage() / 2)) / GLOBAL_DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L97



*Instances (26)*

### **[Low-3]** Use `safeTransferOwnership` instead of `transferOwnership` function

`transferOwnership` function is used to change Ownership from Ownable.sol. Use a 2 structure `transferOwnership` which is safer. `safeTransferOwnership`, use it is more secure due to 2-stage ownership transfer.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
138:        bHermesGaugeWeight.transferOwnership(newOwner);
139:        bHermesGaugeBoost.transferOwnership(newOwner);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L138


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
124:            pools[poolIds[i]].transferOwnership(owner);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L124



*Instances (3)*

### **[Low-4]** Unbounded loop

While looping large collections, it's possible to run out of gas - causing a DOS condition
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
48:        for (uint256 i = 0; i < calls.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L48


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
95:        for (uint256 i = 0; i < assets.length; i++) {
111:        for (uint256 i = 0; i < assets.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L95


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
101:        for (uint256 i = 0; i < _localAddresses.length;) {
136:        for (uint256 i = 0; i < _localAddresses.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L101


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
231:        for (uint256 i = 0; i < _localAddresses.length;) {
267:        for (uint256 i = 0; i < _localAddresses.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L231


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
146:        for (uint256 i = 0; i < outputTokens.length;) {
311:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
387:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
463:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L146


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
181:        for (uint256 i = 0; i < proposal.targets.length; i++) {
215:        for (uint256 i = 0; i < proposal.targets.length; i++) {
251:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L181


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
130:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
232:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
253:            for (uint256 i = 1; i < bandwidthStateList.length;) {
278:            for (uint256 i = 1; i < bandwidthStateList.length;) {
296:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L130


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
342:        for (uint256 i = 0; i < _globalAddresses.length;) {
408:        for (uint256 i = 0; i < _dParams.hTokens.length;) {
561:        for (uint256 i = 0; i < newGas.length;) {
596:        for (uint256 i = 0; i < settlement.hTokens.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L342


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
283:        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
719:        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
951:        for (uint256 i = 0; i < deposit.hTokens.length;) {
1354:        for (uint256 i = 0; i < _deposits.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L283



*Instances (27)*

### **[Low-5]** `decimals()` not part of ERC20 standard


#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
46:        require(ERC20(asset).decimals() == 18);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L46


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
72:        underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));
82:            _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
120:                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
141:                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L72


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
104:            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L104


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
212:            _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
254:                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
272:                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L212


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
252:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
284:            _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());
342:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
687:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
720:            deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());
1355:            deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals());
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L252


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
23:    constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
27:        if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L27


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
23:    constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
51:            require(ERC20(_assets[i]).decimals() == 18);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L51



*Instances (20)*

### **[Low-6]** Use `safetransfer` Instead Of `transfer`


#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
148:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L148


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
91:        return super.transfer(_to, _amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L91


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
270:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L270


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
313:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L313


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
293:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L293


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
409:        if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410:        if (amount1 > 0) _token1.transfer(msg.sender, amount1);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L409


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
498:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L498



*Instances (8)*

### **[Low-7]** Use `_safeMint` instead of `_mint`


#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
63:        _mint(account, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L63


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
56:        _mint(account, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L56


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
24:        _mint(account, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L24


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
29:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L29


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
36:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L36


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
27:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L27


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
74:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L74


```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
132:        super._mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L132


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
39:        _mint(receiver, shares);
53:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L39


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
42:        _mint(receiver, shares);
54:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L42


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
39:        _mint(receiver, shares);
53:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L39


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
245:        super._mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L245


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
105:        _mint(receiver, shares);
124:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L105


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
157:        _mint(receiver, shares);
218:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L157



*Instances (19)*

### **[Low-8]** Division before multiplication causing significant loss of precision

First divides and then multiplies again, there is a significant loss of precision
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
82:        activePeriod = (block.timestamp / week) * week;
127:            _period = (block.timestamp / week) * week;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L82


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
60:        gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
79:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
114:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L60


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
50:            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
68:        epoch = (block.timestamp / WEEK) * WEEK;
84:        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L68


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
77:            return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L77


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
20:        return uint96(((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) * INCENTIVES_DURATION + INCENTIVES_OFFSET);
27:            (((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) + 1) * INCENTIVES_DURATION + INCENTIVES_OFFSET
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L20


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
35:                ((secondsInsideX128 * 4) / 10) + ((((stakedDuration << 128) * boostAmount) / boostTotalSupply) * 6) / 10
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L35



*Instances (12)*

### **[Low-9]** `safeApprove()` is deprecated

[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead. The function may currently work, but if a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to no longer has this function, you'll encounter unnecessary delays in porting and testing replacement contracts.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
45:        rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L45


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol
40:            address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol#L40


```solidity
File:2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol
36:        _rewardToken.safeApprove(address(_flywheel), type(uint256).max);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol#L36


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
195:        address(gaugeWeight).safeApprove(oldPartnerVault, 0);
196:        address(gaugeBoost).safeApprove(oldPartnerVault, 0);
197:        address(governance).safeApprove(oldPartnerVault, 0);
198:        address(partnerGovernance).safeApprove(oldPartnerVault, 0);
200:        address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
201:        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
202:        address(governance).safeApprove(newPartnerVault, type(uint256).max);
203:        address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L195


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
130:        address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);
131:        address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L130



*Instances (13)*


### **[Low-10]** Use `require` instead of `assert`

Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
183:            assert(queuedRewards.storedCycle == 0 || queuedRewards.storedCycle >= lastCycle);
215:        assert(queuedRewards.storedCycle >= cycle);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L183


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
65:        assert(currentTime >= startTime);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L65



*Instances (3)*

### **[Low-11]** Signatures vulnerable to malleability attacks

ecrecover() accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin’s ECDSA library to perform the extra checks necessary in order to prevent this attack.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
349:        address signatory = ecrecover(digest, v, r, s);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L349


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
365:        address signer = ecrecover(
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L365



*Instances (2)*

### **[Low-12]** UNSAFE `decimals()` CALL FOR ASSET TOKENS THAT DO NOT IMPLEMENT `decimals()`

helper functions like [BoringCrypto's](https://github.com/boringcrypto/BoringSolidity/blob/ccb743d4c3363ca37491b87c6c9b24b1f5fa25dc/contracts/libraries/BoringERC20.sol#L52-L55) safeDecimals can be used instead of directly calling decimals()
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
46:        require(ERC20(asset).decimals() == 18);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L46


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
72:        underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));
82:            _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
120:                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
141:                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L72


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
104:            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L104


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
212:            _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
254:                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
272:                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L212


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
252:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
284:            _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());
342:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
687:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
720:            deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());
1355:            deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals());
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L252


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
23:    constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
27:        if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L27


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
23:    constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
51:            require(ERC20(_assets[i]).decimals() == 18);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L51



*Instances (20)*

### **[Low-13]** Minting tokens to the zero address should be avoided

The core function mint is used by users to mint an option position by providing token1 as collateral and borrowing the max amount of liquidity. Address(0) check is missing in both this function and the internal function _mint, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
63:        _mint(account, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L63


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
56:        _mint(account, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L56


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
24:        _mint(account, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L24


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
29:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L29


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
36:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L36


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
27:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L27


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
74:        _mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L74


```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
132:        super._mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L132


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
39:        _mint(receiver, shares);
53:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L39


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
42:        _mint(receiver, shares);
54:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L42


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
39:        _mint(receiver, shares);
53:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L39


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
245:        super._mint(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L245


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
105:        _mint(receiver, shares);
124:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L105


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
157:        _mint(receiver, shares);
218:        _mint(receiver, shares);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L157



*Instances (19)*

### **[Low-14]** Functions return bool true but cannot return false


#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
25:        return true;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L25


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
75:        return true;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L75


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
62:        return true;
583:        return true;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L62



*Instances (4)*

### **[Low-15]** PREVENT DIV BY 0


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
82:        activePeriod = (block.timestamp / week) * week;
114:        return (circulatingSupply() * tailEmission) / base;
119:        return (vault.totalAssets() * _minted) / HERMES(underlying).totalSupply();
127:            _period = (block.timestamp / week) * week;
136:            uint256 share = (_required * daoShare) / base;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L82


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
119:            uint256 newProtocolRewards = (pendingRewards * protocolFee) / DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L119


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
60:        gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
79:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
114:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L60


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
150:        return ((_currBalance + getStrategyTokenDebt[_token]) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L150


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
88:        return govToken.totalSupply() * proposalThreshold / DIVISIONER;
92:        return govToken.totalSupply() * quorumVotes / DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L88


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
677:        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;
720:            uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L865


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
50:            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
68:        epoch = (block.timestamp / WEEK) * WEEK;
84:        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L68


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
171:                deltaIndex = ((strategyRewardsAccrued * ONE) / supplyTokens).toUint224();
201:        uint256 supplierDelta = (supplierTokens * deltaIndex) / ONE;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L171


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
162:        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
168:        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
219:        if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L162


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
215:        shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;
261:            uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L215


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
77:            return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow
180:        return (quantity * weight) / total;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L77


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
20:        return uint96(((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) * INCENTIVES_DURATION + INCENTIVES_OFFSET);
27:            (((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) + 1) * INCENTIVES_DURATION + INCENTIVES_OFFSET
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L20


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
35:                ((secondsInsideX128 * 4) / 10) + ((((stakedDuration << 128) * boostAmount) / boostTotalSupply) * 6) / 10
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L35


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
97:        int24 compressed = tick / tickSpacing;
214:        return int24((tickCumulatives[1] - tickCumulatives[0]) / int56(int32(_twapDuration)));
251:        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (_strategy.priceImpactPercentage() / 2)) / GLOBAL_DIVISIONER;
```

*Instances (32)*

### **[Low-16]** AVOID TRANSFER()/SEND() AS REENTRANCY MITIGATIONS

Although transfer() and send() have been recommended as a security best-practice to prevent reentrancy attacks because they only forward 2300 gas, the gas repricing of opcodes may break deployed contracts. Use call() instead, without hardcoded gas limits along with checks-effects-interactions pattern or reentrancy guards for reentrancy protection.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
148:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L148


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
91:        return super.transfer(_to, _amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L91


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
270:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L270


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
313:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L313


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
293:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L293


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
409:        if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410:        if (amount1 > 0) _token1.transfer(msg.sender, amount1);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L409


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
498:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L498



*Instances (8)*

### **[Low-17]** ECRECOVER MAY RETURN EMPTY ADDRESS

There is a common issue that ecrecover returns empty (0x0) address when the signature is invalid. function should check that before returning the result of ecrecover.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
349:        address signatory = ecrecover(digest, v, r, s);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L349


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
365:        address signer = ecrecover(
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L365



*Instances (2)*

### **[Low-18]** Use `safeERC20.safeApprove()` instead of `approve()`

Note that approve() will fail for certain token implementations that do not return a boolean value . Hence it is recommend to use safeApprove().
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
120:        ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);
148:            ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L120



*Instances (2)*

### **[Low-19]** Inline assembly bypasses several important safety features and checks of Solidity


#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegator.sol
61:        assembly {
75:        assembly {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegator.sol#L61


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
538:        assembly {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L538


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
356:        assembly {
380:        assembly {
553:        assembly {
581:        assembly {
633:        assembly {
698:        assembly {
737:        assembly {
751:            assembly {
760:            assembly {
851:        assembly {
913:                assembly {
924:                assembly {
948:        assembly {
966:            assembly {
977:            assembly {
1007:        assembly {
1032:        assembly {
1050:                assembly {
1062:                assembly {
1074:        assembly {
1098:        assembly {
1112:        assembly {
1152:        assembly {
1175:        assembly {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L356


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
43:        assembly {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L43



*Instances (28)*

### **[Low-20]** Message call with hardcoded gas amount

The transfer() and send() functions forward a fixed amount of 2300 gas. Historically, it has often been recommended to use these functions for value transfers to guard against reentrancy attacks. However, the gas cost of EVM instructions may change significantly during hard forks which may break already deployed contract systems that make fixed assumptions about gas costs. For example. EIP 1884 broke several existing smart contracts due to a cost increase of the SLOAD instruction. See [SWC-134](https://swcregistry.io/docs/SWC-134)
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
140:    function transfer(address to, uint256 amount) public virtual override returns (bool) {
148:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L140


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
89:    function transfer(address _to, uint256 _amount) public override returns (bool) {
91:        return super.transfer(_to, _amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L89


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
263:    function transfer(address to, uint256 amount)
270:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L263


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
312:    function transfer(address to, uint256 amount) public override notAttached(msg.sender, amount) returns (bool) {
313:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L312


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
291:    function transfer(address to, uint256 amount) public virtual override returns (bool) {
293:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L291


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
409:        if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410:        if (amount1 > 0) _token1.transfer(msg.sender, amount1);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L409


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
496:    function transfer(address to, uint256 amount) public virtual override returns (bool) {
498:        return super.transfer(to, amount);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L496


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol
11:    function transfer(address dst, uint256 wad) external;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol#L11



*Instances (15)*

### **[Low-21]** Solidity version 0.8.20 may not work on other chains due to `PUSH0`

The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new PUSH0 op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version)
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L2


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/FlywheelCoreInstant.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/FlywheelCoreInstant.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/FlywheelCoreStrategy.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/FlywheelCoreStrategy.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyVanillaFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyVanillaFactory.sol#L3


```solidity
File:2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/OptimizerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/OptimizerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/TalosOptimizer.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosOptimizer.sol#L3


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegator.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegator.sol#L2


```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/factories/BribesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BribesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/talos/TalosManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosManager.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L3


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/depots/RewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/RewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L3


```solidity
File:2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/UtilityManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/UtilityManager.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/maia/PartnerUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/PartnerUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L3


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
4:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L4


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol#L3


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
2:pragma solidity ^0.8.4;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L2


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/NFTPositionInfo.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L2


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L2


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolActions.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolActions.sol#L3


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/Errors.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/Errors.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelInstantRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelInstantRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/AutomationCompatibleInterface.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/AutomationCompatibleInterface.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IApp.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IApp.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IPortStrategy.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L3


```solidity
File:2023-05-maia-main/lib/v3-periphery/contracts/interfaces/external/IWETH9.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/lib/v3-periphery/contracts/interfaces/external/IWETH9.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IbHermesUnderlying.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IbHermesUnderlying.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelBooster.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelBooster.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IUniswapV3Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IUniswapV3Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosStrategyStaked.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosStrategyStaked.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelBribeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelBribeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesToken.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IMultiRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IMultiRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosStrategyStakedFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosStrategyStakedFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/maia/interfaces/IBaseVault.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IBaseVault.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IMulticall2.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IMulticall2.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IPartnerUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IPartnerUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IBoostAggregatorFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IBoostAggregatorFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesRouter.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IOptimizerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IOptimizerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626DepositOnly.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBribesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBribesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosOptimizer.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosOptimizer.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IUlyssesERC4626.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IERC4626PartnerManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IERC4626PartnerManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategyFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategyFactory.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IPartnerManagerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IPartnerManagerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelCore.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelCore.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L3


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IBaseV2Minter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IBaseV2Minter.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelGaugeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20MultiVotes.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20MultiVotes.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626MultiToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesPool.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20Boost.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20Boost.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20Gauges.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20Gauges.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategy.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategy.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootPort.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L2



*Instances (143)*

# **Gas Optimizer Report**


This is an automated findings report.


### **[NC-1]** Return values of `approve()` not checked

Not all IERC20 implementations `revert()` when there's a failure in `approve()`. The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
120:        ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);
148:            ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L120



*Instances (2)*

### **[NC-2]** Constants should be defined rather than using magic numbers

Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
400:                let weight := shr(248, slot)
490:                    let weight := shr(248, sload(add(bandwidthStateListStart, mul(i, 2))))
559:            weight := shr(248, slot)
669:            sstore(destinationState.slot, or(bandwidth, shl(248, weight)))
702:            let sigma2 := shr(192, feeSlot)
706:            lambda2 := and(shr(64, feeSlot), 0xffffffffffffffff)
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L400


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
47:            let doy := sub(doe, sub(add(mul(365, yoe), shr(2, yoe)), div(yoe, 100)))
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L47


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
13:    uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L13



*Instances (8)*


### **[NC-3]** Using `while` for unbounded loops isn’t recommended

Don’t write loops that are unbounded as this can hit the gas limit, causing your transaction to fail.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
68:        while (low < high) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L68



*Instances (1)*

### **[NC-4]** Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
62:    function mint(address account, uint256 amount) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L62


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
55:    function mint(address account, uint256 amount) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L55


```solidity
File:2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol
32:    function getRewards() external override onlyFlywheelRewards returns (uint256 balance) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol#L32


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
23:    function mint(address account, uint256 amount) external override onlyOwner returns (bool) {
29:    function burn(uint256 value) public override onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L23


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
28:    function mint(address to, uint256 amount) external onlybHermes {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L28


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol
33:    function getAccruedRewards() external override onlyFlywheel returns (uint256) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol#L33


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
35:    function mint(address to, uint256 amount) external onlybHermes {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L35


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
26:    function mint(address to, uint256 amount) external onlybHermes {
35:    function burn(address from, uint256 amount) external onlybHermes {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L26


```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
62:    function setMinimumWidth(uint24 _minimumWidth) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L62


```solidity
File:2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol
38:    function getRewards() external override(RewardsDepot, IMultiRewardsDepot) onlyFlywheelRewards returns (uint256) {
47:    function addAsset(address rewardsContract, address asset) external onlyOwner {
57:    function removeAsset(address rewardsContract) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol#L38


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
40:    function initialize(address _coreRouter) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L40


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
35:    function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L35


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
58:    function addPartner(PartnerManager newPartnerManager) external onlyOwner {
67:    function addVault(IBaseVault newVault) external onlyOwner {
80:    function removePartner(PartnerManager partnerManager) external onlyOwner {
89:    function removeVault(IBaseVault vault) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L58


```solidity
File:2023-05-maia-main/src/talos/TalosOptimizer.sol
62:    function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {
68:    function setTwapDuration(uint32 _twapDuration) external onlyOwner {
74:    function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {
80:    function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {
85:    function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosOptimizer.sol#L62


```solidity
File:2023-05-maia-main/src/gauges/factories/BribesFactory.sol
72:    function addGaugetoFlywheel(address gauge, address bribeToken) external onlyGaugeFactory {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BribesFactory.sol#L72


```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
98:    function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L98


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol
54:    function initialize(address _coreRootBridgeAgent) external override onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L54


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
44:    function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {
60:    function removeAsset(address asset) external nonReentrant onlyOwner {
88:    function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L44


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
94:    function addGauge(address gauge) external onlyActiveGaugeFactory {
100:    function removeGauge(address gauge) external onlyActiveGaugeFactory {
110:    function addGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {
121:    function removeGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {
137:    function changebHermesGaugeOwner(address newOwner) external onlyAdmin {
145:    function changeAdmin(address newAdmin) external onlyAdmin {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L94


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
83:    function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L83


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
86:    function setDao(address _dao) external onlyOwner {
92:    function setDaoShare(uint256 _daoShare) external onlyOwner {
98:    function setTailEmission(uint256 _tail_emission) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L86


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
143:    function addWhitelistedAddress(address user) external onlyOwner {
148:    function removeWhitelistedAddress(address user) external onlyOwner {
153:    function setProtocolFee(uint256 _protocolFee) external onlyOwner {
159:    function withdrawProtocolFees(address to) external onlyOwner {
165:    function withdrawAllGaugeBoost(address to) external onlyOwner {
172:    function withdrawGaugeBoost(address to, uint256 amount) external onlyOwner {
180:    function decrementGaugesBoostIndexed(uint256 boost, uint256 offset, uint256 num) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L143


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol
37:    function initialize(address _localBridgeAgentAddress) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol#L37


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
65:    function renounceOwnership() public payable override onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L65


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
63:    function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L63


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
99:    function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {
113:    function renounceOwnership() public payable override onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L99


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
74:    function initialize(address _bridgeAgentAddress) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L74


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
128:    function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {
158:    function forefeitOwnership(address _owner) external onlyOwner {
165:    function renounceOwnership() public payable override onlyOwner {
399:    function toggleBridgeAgent(address _bridgeAgent) external onlyOwner {
406:    function addBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {
413:    function toggleBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {
484:    function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external onlyOwner {
491:    function addEcosystemToken(address _ecoTokenGlobalAddress) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L128


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
159:    function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {
223:    function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {
308:    function setFees(Fees calldata _fees) external nonReentrant onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L159


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
42:    function getAccruedRewards(ERC20 strategy) external override onlyFlywheel returns (uint256 amount) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L42


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
101:    function attachUser(address user) external onlyStrategy {
106:    function detachUser(address user) external onlyStrategy {
128:    function addBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {
144:    function removeBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L101


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
109:    function createGauge(address strategy, bytes memory data) external onlyOwner {
130:    function removeGauge(BaseV2Gauge gauge) external onlyOwner {
144:    function addBribeToGauge(BaseV2Gauge gauge, address bribeToken) external onlyOwnerOrBribesFactoryOwner {
151:    function removeBribeFromGauge(BaseV2Gauge gauge, address bribeToken) external onlyOwnerOrBribesFactoryOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L109


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
111:    function addStrategyForRewards(ERC20 strategy) external onlyOwner {
125:    function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {
137:    function setBooster(IFlywheelBooster newBooster) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L111


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
188:    function migratePartnerVault(address newPartnerVault) external onlyOwner {
216:    function increaseConversionRate(uint256 newRate) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L188


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
259:    function addGauge(address gauge) external onlyOwner {
273:    function removeGauge(address gauge) external onlyOwner {
285:    function replaceGauge(address oldGauge, address newGauge) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L259


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
96:    function setMaxDelegates(uint256 newMax) external onlyOwner {
104:    function setContractExceedMaxDelegates(address account, bool canExceedMax) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L96


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
298:    function rerange() external virtual override nonReentrant checkDeviation onlyStrategyManager {
311:    function rebalance() external virtual override nonReentrant checkDeviation onlyStrategyManager {
394:    function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L298


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
398:    function addGauge(address gauge) external onlyOwner returns (uint112) {
425:    function removeGauge(address gauge) external onlyOwner {
449:    function replaceGauge(address oldGauge, address newGauge) external onlyOwner {
455:    function setMaxGauges(uint256 newMax) external onlyOwner {
463:    function setContractExceedMaxGauges(address account, bool canExceedMax) external onlyOwner {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L398



*Instances (92)*

### **[NC-5]** Floating pragma

use fixed solidity version
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L2


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/FlywheelCoreInstant.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/FlywheelCoreInstant.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/FlywheelCoreStrategy.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/FlywheelCoreStrategy.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyVanillaFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyVanillaFactory.sol#L3


```solidity
File:2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/OptimizerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/OptimizerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/TalosOptimizer.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosOptimizer.sol#L3


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegator.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegator.sol#L2


```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/factories/BribesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BribesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/talos/TalosManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosManager.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L3


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/depots/RewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/RewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L3


```solidity
File:2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/UtilityManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/UtilityManager.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/maia/PartnerUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/PartnerUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L3


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
4:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L4


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol#L3


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
2:pragma solidity ^0.8.4;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L2


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/NFTPositionInfo.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L2


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L2


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolActions.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolActions.sol#L3


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/Errors.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/Errors.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelInstantRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelInstantRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/AutomationCompatibleInterface.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/AutomationCompatibleInterface.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IApp.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IApp.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IPortStrategy.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L3


```solidity
File:2023-05-maia-main/lib/v3-periphery/contracts/interfaces/external/IWETH9.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/lib/v3-periphery/contracts/interfaces/external/IWETH9.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IbHermesUnderlying.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IbHermesUnderlying.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelBooster.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelBooster.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IUniswapV3Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IUniswapV3Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosStrategyStaked.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosStrategyStaked.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelBribeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelBribeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesToken.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IMultiRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IMultiRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosStrategyStakedFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosStrategyStakedFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/maia/interfaces/IBaseVault.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IBaseVault.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IMulticall2.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IMulticall2.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IPartnerUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IPartnerUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IBoostAggregatorFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IBoostAggregatorFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesRouter.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IOptimizerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IOptimizerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626DepositOnly.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBribesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBribesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosOptimizer.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosOptimizer.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IUlyssesERC4626.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IERC4626PartnerManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IERC4626PartnerManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategyFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategyFactory.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IPartnerManagerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IPartnerManagerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelCore.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelCore.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L3


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IBaseV2Minter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IBaseV2Minter.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelGaugeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20MultiVotes.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20MultiVotes.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626MultiToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesPool.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20Boost.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20Boost.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20Gauges.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20Gauges.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategy.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategy.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootPort.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L2



*Instances (143)*

### **[NC-6]** Hardcoded values can’t be changed

Hardcoded constants in code is risky for auditability/readability/maintainability
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
34:    uint8 internal constant PARAMS_START_SIGNED = 21;
36:    uint8 internal constant PARAMS_END_SIGNED_OFFSET = 26;
38:    uint8 internal constant PARAMS_ENTRY_SIZE = 32;
40:    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;
42:    uint8 internal constant PARAMS_TKN_SET_SIZE = 128;
44:    uint8 internal constant PARAMS_GAS_OUT = 16;
50:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
52:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L34


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
24:    uint256 internal constant week = 86400 * 7;
26:    uint256 internal constant base = 1000;
28:    uint256 internal constant max_tail_emission = 100;
29:    uint256 internal constant max_dao_share = 300;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L24


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
56:    uint256 private constant DIVISIONER = 10000;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L56


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
47:    uint24 private constant protocolFee = 2 * 1e5; //20%
48:    uint24 private constant GLOBAL_DIVISIONER = 1e6;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L47


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
91:    uint256 internal constant DIVISIONER = 1e4;
92:    uint256 internal constant MIN_RESERVE_RATIO = 3e3;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L91


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
35:    uint8 internal constant PARAMS_START_SIGNED = 21;
39:    uint8 internal constant PARAMS_END_SIGNED_OFFSET = 29;
41:    uint8 internal constant PARAMS_ENTRY_SIZE = 32;
43:    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;
45:    uint8 internal constant PARAMS_TKN_SET_SIZE = 104;
47:    uint8 internal constant PARAMS_TKN_SET_SIZE_MULTIPLE = 128;
49:    uint8 internal constant PARAMS_GAS_IN = 32;
51:    uint8 internal constant PARAMS_GAS_OUT = 16;
57:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
59:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L35


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
12:    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken
15:    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken
18:    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks
21:    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks
24:    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks
27:    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks
30:    uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken
33:    uint256 public constant proposalMaxOperations = 10; // 10 actions
36:    uint256 public constant DIVISIONER = 1 ether;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L12


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
43:    uint256 private constant MIN_SWAP_AMOUNT = 1e4;
46:    uint256 private constant MAX_TOTAL_WEIGHT = 256;
49:    uint256 private constant MAX_DESTINATIONS = 15;
52:    uint256 private constant MAX_PROTOCOL_FEE = 1e16;
55:    uint256 private constant MAX_LAMBDA1 = 1e17;
58:    uint256 private constant MIN_SIGMA2 = 1e16;
65:    uint256 private constant DIVISIONER = 1 ether;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L43


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
102:    uint8 internal constant PARAMS_START_SIGNED = 21;
104:    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;
106:    uint8 internal constant PARAMS_GAS_IN = 32;
108:    uint8 internal constant PARAMS_GAS_OUT = 16;
114:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
116:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
180:    uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; // 100_000 for anycall + 55_000 for fallback
181:    uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution
640:    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L102


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
70:    uint8 internal constant PARAMS_START_SIGNED = 21;
72:    uint8 internal constant PARAMS_ENTRY_SIZE = 32;
74:    uint8 internal constant PARAMS_GAS_OUT = 16;
80:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
82:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
137:    uint256 internal constant MIN_FALLBACK_RESERVE = 185_000; // 100_000 for anycall + 85_000 fallback execution overhead
138:    uint256 internal constant MIN_EXECUTION_OVERHEAD = 160_000; // 100_000 for anycall + 35_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Executions
139:    uint256 internal constant TRANSFER_OVERHEAD = 24_000;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L70


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
53:    uint256 internal constant WEEK = 1 weeks;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L53


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
148:    uint256 private constant ONE = 1e18;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L148


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
77:    uint24 internal constant MULTIPLIER = 1e6;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L77


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol
8:    uint256 public constant FLAG_NONE = 0x0;
9:    uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;
10:    uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;
11:    uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;
15:    uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;
16:    uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol#L8


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
13:    uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))
15:    uint256 private constant INCENTIVES_OFFSET = 12 hours;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L13


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
23:    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L23



*Instances (72)*

### **[NC-7]** Unspecific compiler version pragma


#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L2


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/FlywheelCoreInstant.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/FlywheelCoreInstant.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/FlywheelCoreStrategy.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/FlywheelCoreStrategy.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyVanillaFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyVanillaFactory.sol#L3


```solidity
File:2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/OptimizerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/OptimizerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/TalosOptimizer.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosOptimizer.sol#L3


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegator.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegator.sol#L2


```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/factories/BribesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BribesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/talos/TalosManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosManager.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L2


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
3:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L2


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L3


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
2:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/depots/RewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/RewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L3


```solidity
File:2023-05-maia-main/src/talos/strategies/TalosStrategySimple.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/strategies/TalosStrategySimple.sol#L2


```solidity
File:2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/UtilityManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/UtilityManager.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/maia/PartnerUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/PartnerUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L3


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
4:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L4


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
3:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol#L3


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
2:pragma solidity ^0.8.4;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L2


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/NFTPositionInfo.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L2


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/RewardMath.sol#L2


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolActions.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolActions.sol#L3


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L3


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/Errors.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/Errors.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelInstantRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelInstantRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/AutomationCompatibleInterface.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/AutomationCompatibleInterface.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IApp.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IApp.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IPortStrategy.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L3


```solidity
File:2023-05-maia-main/lib/v3-periphery/contracts/interfaces/external/IWETH9.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/lib/v3-periphery/contracts/interfaces/external/IWETH9.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IbHermesUnderlying.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IbHermesUnderlying.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelBooster.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelBooster.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IUniswapV3Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IUniswapV3Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosStrategyStaked.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosStrategyStaked.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IWETH9.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelBribeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelBribeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesToken.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IMultiRewardsDepot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IMultiRewardsDepot.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosStrategyStakedFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosStrategyStakedFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/maia/interfaces/IBaseVault.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IBaseVault.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IMulticall2.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IMulticall2.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IPartnerUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IPartnerUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IBoostAggregatorFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IBoostAggregatorFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesRouter.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IOptimizerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IOptimizerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol
3:pragma solidity ^0.8.10;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626DepositOnly.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBribesFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBribesFactory.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosOptimizer.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosOptimizer.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IUlyssesERC4626.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IERC4626PartnerManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IERC4626PartnerManager.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategyFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategyFactory.sol#L2


```solidity
File:2023-05-maia-main/src/maia/interfaces/IPartnerManagerFactory.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/interfaces/IPartnerManagerFactory.sol#L2


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeFactory.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeFactory.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2Gauge.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2Gauge.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelCore.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelCore.sol#L3


```solidity
File:2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeManager.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L3


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IBaseV2Minter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IBaseV2Minter.sol#L2


```solidity
File:2023-05-maia-main/src/hermes/interfaces/IUtilityManager.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/interfaces/IUtilityManager.sol#L2


```solidity
File:2023-05-maia-main/src/rewards/interfaces/IFlywheelGaugeRewards.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L3


```solidity
File:2023-05-maia-main/src/talos/interfaces/IBoostAggregator.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IBoostAggregator.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchRouter.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20MultiVotes.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20MultiVotes.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626MultiToken.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesPool.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20Boost.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20Boost.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootRouter.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootRouter.sol#L2


```solidity
File:2023-05-maia-main/src/erc-20/interfaces/IERC20Gauges.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/interfaces/IERC20Gauges.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L3


```solidity
File:2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategy.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/ITalosBaseStrategy.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootPort.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootPort.sol#L3


```solidity
File:2023-05-maia-main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
3:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L3


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L2


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
2:pragma solidity ^0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L2



*Instances (154)*

### **[NC-8]** Inconsistent spacing in comments

Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/talos/TalosManager.sol
67:        //Calculate base ticks.
79:        //Calculate base ticks.
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosManager.sol#L67


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
48:        //Get Token Info
52:        //Encode Data
55:        //Pack FuncId
58:        //Send Cross-Chain request (System Response/Request)
82:        //Check if msg.sender is a valid BridgeAgentFactory
87:        //Create Token
92:        //Check BridgeAgent Address
97:        //Encode Data
100:        //Pack FuncId
103:        //Send Cross-Chain request
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L48


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
75:        //Execute remote request
93:        //Clear Token / Execute Settlement
103:        //Bridge In Assets
109:            //Execute remote request
130:        //Bridge In Assets and Save Deposit Params
146:            //Try to execute remote request
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L75


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
135:        //Get Liquidity for Optimizer's balances
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L135


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
150:        //Gas already provided by Root Bridge Agent
157:        //Get gas remaining
161:            //Unwrap Gas
164:            //Transfer gas remaining to recipient
175:        //Cross-chain messaging + Fallback is managed by the Root Bridge Agent
190:        //Cross-chain messaging + Fallback is managed by the Root Bridge Agent
195:        //Cross-chain messaging + Fallback is managed by the Root Bridge Agent
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L150


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
47:        //Encode Call Data
50:        //Pack FuncId
53:        //Send Cross-Chain request (System Response/Request)
64:        //Get Token Info
68:        //Create Token
71:        //Encode Data
74:        //Pack FuncId
77:        //Send Cross-Chain request (System Response/Request)
101:        //Create Token
104:        //Encode Data
107:        //Pack FuncId
110:        //Send Cross-Chain request
132:        //Check if msg.sender is a valid BridgeAgentFactory
137:        //Create Token
142:        //Check BridgeAgent Address
147:        //Encode Data
150:        //Pack FuncId
153:        //Send Cross-Chain request
213:            //Add new Port Strategy if new.
216:            //Or Update daily limit.
219:            //Or Toggle Port Strategy.
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L47


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
94:        //Check if valid chain
97:        //Check if chain already added to bridge agent
100:        //Check if Branch Bridge Agent is allowed by Root Bridge Agent
103:        //Root Bridge Agent Factory Address
106:        //Encode CallData
111:        //Pack funcId into data
114:        //Add new global token to branch chain
152:        //Verify that it does not exist
157:        //Encode CallData
162:        //Pack funcId into data
165:        //Add new global token to branch chain
192:        //Create new global token
195:        //Update Registry
237:        //Encode CallData
240:        //Pack funcId into data
243:        //Add new global token to branch chain
258:        //Encode CallData
261:        //Pack funcId into data
264:        //Add new global token to branch chain
281:        //Encode CallData
284:        //Pack funcId into data
287:        //Add new global token to branch chain
308:        //Encode CallData
311:        //Pack funcId into data
314:        //Add new global token to branch chain
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L94


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
83:        //Try to execute remote request
103:        //Execute remote request
122:        //Read Deposit Params
132:        //Bridge In Assets
136:            //Execute remote request
159:        //Bridge In Assets and Save Deposit Params
176:            //Try to execute remote request
206:        //Execute remote request
226:        //Read Deposit Params
236:        //Bridge In Asset
240:            //Execute remote request
265:        //Bridge In Assets
282:                //Execute remote request
313:        //Execute remote request
315:        //Trigger retry success (no guarantees about settlement success at this point)
326:        //Request assets for decoded request.
365:            //Parse Params
415:        //Save Deposit Multiple Params
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L83


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
95:        //Call desired functions
119:        //Approve Root Port to spend/send output hTokens.
122:        //Move output hTokens from Root to Branch and call 'clearToken'.
145:        //For each output token
147:            //Approve Root Port to spend output hTokens.
154:        //Move output hTokens from Root to Branch and call 'clearTokens'.
280:            //Call desired functions
288:            //Call desired functions
308:            //Call desired functions
356:            //Call desired functions
364:            //Call desired functions
384:            //Call desired functions
432:            //Call desired functions
440:            //Call desired functions
460:            //Call desired functions
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L95


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
456:        //Avoid stack too deep
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L456


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
56:            (_dParams.amount < _dParams.deposit) //Deposit can't be greater than amount.
57:                || (_dParams.amount > 0 && !IPort(_localPortAddress).isLocalToken(_dParams.hToken, _fromChain)) //Check local exists.
58:                || (_dParams.deposit > 0 && !IPort(_localPortAddress).isUnderlyingToken(_dParams.token, _fromChain)) //Check underlying exists.
227:        accumulatedFees = 1; //Avoid paying 20k gas in first `payExecutionGas` making MIN_EXECUTION_OVERHEAD constant.
245:        //Update User Gas available.
250:        //Clear Settlement with updated gas.
256:        //Get deposit owner.
259:        //Update Deposit
276:        //Encode Data for call.
280:        //Perform Call to clear hToken balance on destination branch chain.
294:        //Get destination Local Address from Global Address.
297:        //Get destination Underlying Address from Local Address.
300:        //Check if valid assets
305:        //Prepare data for call
318:        //Update State to reflect bridgeOut
323:        //Create Settlement
326:        //Perform Call to clear hToken balance on destination branch chain and perform call.
343:            //Populate Addresses for Settlement
358:        //Prepare data for call with settlement of multiple assets
372:        //Create Settlement Balance
375:        //Perform Call to destination Branch Chain.
388:        //Check Deposit info from Cross Chain Parameters.
393:        //Get global address
396:        //Check if valid asset
399:        //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
452:            //Move output hTokens from Root to Branch
458:            //Verify there is enough balance to clear native tokens if needed
493:        //Cast to Dynamic
503:        //Call createSettlement
551:        //Get Settlement
554:        //Check if Settlement hasn't been redeemed.
557:        //abi encodePacked
560:        //overwrite last 16bytes of callData
570:        //Update Gas To Bridge Out
573:        //Set Settlement Calldata to send to Branch Chain
576:        //Update Settlement Staus
579:        //Retry call with additional gas
582:        //Retry Success
595:        //Clear Global hTokens To Recipient on Root Chain cancelling Settlement to Branch
597:            //Check if asset
599:                //Move hTokens from Branch to Root + Mint Sufficient hTokens to match new port deposit
624:        //Update Deposit
642:    //Local mapping of valid gas pools
660:        //Get fromChain's Gas Pool Info
664:        //Check if valid addresses
667:        //Move Gas hTokens from Branch to Root / Mint Sufficient hTokens to match new port deposit
670:        //Save Gas Pool for future use
673:        //Get sqrtPriceX96
679:        //Get limit
683:        //Swap imbalanced token as long as we haven't used the entire amountSpecified and haven't reached the price limit
704:        //Get fromChain's Gas Pool Info
708:        //Check if valid addresses
711:        //Save Gas Pool for future use
716:            //Get sqrtPriceX96
722:            //Get limit
727:        //Swap imbalanced token as long as we haven't used the entire amountSpecified and haven't reached the price limit
749:            //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
781:            //Sends message to AnycallProxy
786:            //Execute locally
801:        //reset initial remote execution gas and remote execution fee information
807:        //Get Available Gas
810:        //Get Root Environment Execution Cost
813:        //Check if sufficient balance
819:        //Replenish Gas
822:        //Account for excess gas
832:        //Save gasleft
835:        //Get Branch Environment Execution Cost
838:        //Check if sufficient balance
844:        //Update user deposit reverts if not enough gas
849:        //Unwrap Gas
866:        //Get Initial Gas Checkpoint
874:            //Save initial gas
877:            //Get fromChainId from AnyExecutor Context
880:            //Save fromChainId
883:            //Swap in all deposited Gas
888:            //Save Gas to Swap out to destination chain
891:            //Local Chain initiated call
894:            //Save depositedGas
897:            //Save Gas to Swap out to destination chain
903:            //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
907:        //Store User Fee Info
910:        //Read Bridge Agent Action Flag attached from cross-chain message header.
913:        //DEPOSIT FLAG: 0 (System request / response)
915:            //Get nonce
918:            //Check if tx has already been executed
921:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
925:            //Try to execute remote request
931:                //Interaction failure trigger fallback
935:            //Update tx state as executed
938:            //DEPOSIT FLAG: 1 (Call without Deposit)
940:            //Get Deposit Nonce
943:            //Check if tx has already been executed
946:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
950:            //Try to execute remote request
956:                //No new asset deposit no need to trigger fallback
960:            //Update tx state as executed
963:            //DEPOSIT FLAG: 2 (Call with Deposit)
965:            //Get Deposit Nonce
968:            //Check if tx has already been executed
971:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
975:            //Try to execute remote request
984:            //Update tx state as executed
987:            //DEPOSIT FLAG: 3 (Call with multiple asset Deposit)
989:            //Get deposit nonce
992:            //Check if tx has already been executed
995:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
999:            //Try to execute remote request
1008:            //Update tx state as executed
1011:            //DEPOSIT FLAG: 4 (Call without Deposit + msg.sender)
1013:            //Get deposit nonce associated with request being processed
1016:            //Check if tx has already been executed
1019:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1023:            //Get User Virtual Account
1028:            //Toggle Router Virtual Account use for tx execution
1031:            //Try to execute remote request
1037:                //No new asset deposit no need to trigger fallback
1041:            //Toggle Router Virtual Account use for tx execution
1044:            //Update tx state as executed
1047:            //DEPOSIT FLAG: 5 (Call with Deposit + msg.sender)
1049:            //Get deposit nonce associated with request being processed
1052:            //Check if tx has already been executed
1055:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1059:            //Get User Virtual Account
1064:            //Toggle Router Virtual Account use for tx execution
1067:            //Try to execute remote request
1076:            //Toggle Router Virtual Account use for tx execution
1079:            //Update tx state as executed
1082:            //DEPOSIT FLAG: 6 (Call with multiple asset Deposit + msg.sender)
1084:            //Get nonce
1087:            //Check if tx has already been executed
1090:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1094:            //Get User Virtual Account
1099:            //Toggle Router Virtual Account use for tx execution
1102:            //Try to execute remote request
1111:            //Toggle Router Virtual Account use for tx execution
1114:            //Update tx state as executed
1119:            //Get nonce
1122:            //Check if tx has already been executed
1125:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1129:            //Try to execute remote request
1137:            //Update tx state as executed
1142:            //Get nonce
1145:            //Check if tx has already been executed
1147:                //Toggle Nonce as executed
1150:                //Retry failed fallback
1154:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1158:            //Unrecognized Function Selector
1160:            //Zero out gas after use if remote call
1170:        //Zero out gas after use if remote call
1183:        //Get Initial Gas Checkpoint
1186:        //Get fromChain
1190:        //Save Flag
1193:        //Deposit nonce
1220:        //Deposit Gas
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L56


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
184:        //Wrap the gas allocated for omnichain execution.
187:        //Perform Call without deposit
198:        //Wrap the gas allocated for omnichain execution.
201:        //Perform Call with deposit
211:        //Wrap the gas allocated for omnichain execution.
214:        //Perform Call with multiple deposits
225:        //Encode Data for cross-chain call.
230:        //Wrap the gas allocated for omnichain execution.
233:        //Perform Signed Call without deposit
244:        //Encode Data for cross-chain call.
259:        //Wrap the gas allocated for omnichain execution.
262:        //Create Deposit and Send Cross-Chain request
280:        //Normalize Deposits
287:        //Encode Data for cross-chain call.
303:        //Wrap the gas allocated for omnichain execution.
306:        //Create Deposit and Send Cross-Chain request
326:        //Check if deposit belongs to message sender
329:        //Encode Data for cross-chain call.
366:            //Nonce
401:        //Wrap the gas allocated for omnichain execution.
404:        //Deposit Gas to Port
407:        //Ensure success Status
410:        //Update Deposited Gas
413:        //Perform Call
424:        //Encode Data for cross-chain call.
428:        //Update State and Perform Call
434:        //Encode Data for cross-chain call.
437:        //Update State and Perform Call
442:        //Deposit Gas for call.
445:        //Perform Call
451:        //Update Deposit
469:        //Get remote call execution deposited gas.
473:        //Wrap the gas allocated for omnichain execution.
476:        //Check Fallback Gas
479:        //Encode Data for cross-chain call.
483:        //Perform Call
494:        //Get remote call execution deposited gas.
498:        //Wrap the gas allocated for omnichain execution.
501:        //Check Fallback Gas
504:        //Perform Call
516:        //Get remote call execution deposited gas.
520:        //Wrap the gas allocated for omnichain execution.
523:        //Check Fallback Gas
526:        //Perform Call
538:        //Get remote call execution deposited gas.
542:        //Wrap the gas allocated for omnichain execution.
545:        //Check Fallback Gas
548:        //Perform Call
570:        //Parse Params
579:        //Transfer token to recipient
581:            //Parse Params
622:            //Clear Tokens to destination
655:        //Encode Data for cross-chain call.
659:        //Perform Call
680:        //Encode Data for cross-chain call.
694:        //Create Deposit and Send Cross-Chain request
716:        //Normalize Deposits
723:        //Encode Data for cross-chain call.
738:        //Create Deposit and Send Cross-Chain request
758:        //Deposit Gas for call.
761:        //Perform Call
785:        //Deposit and Store Info
788:        //Perform Call
812:        //Validate Input
818:        //Deposit and Store Info
821:        //Perform Call
832:        //Deposit Gas to Port
865:        //Deposit / Lock Tokens into Port
868:        //Deposit Gas to Port
911:        //Deposit / Lock Tokens into Port
914:        //Deposit Gas to Port
947:        //Get Deposit
950:        //Transfer token to depositor / user
959:        //Delete Failed Deposit Token Info
994:        //Update and return Deposit
1007:        //Sends message to AnycallProxy
1019:        //Gas remaining
1022:        //Unwrap Gas
1025:        //Delete Remote Initiated Action State
1028:        ///Save gas left
1031:        //Get Branch Environment Execution Cost
1034:        //Check if sufficient balance
1040:        //Replenish Gas
1043:        //Transfer gas remaining to recipient
1046:        //Save Gas
1049:        //Check if sufficient balance
1062:        //Save gas
1065:        //Get Branch Environment Execution Cost
1068:        //Check if sufficient balance
1074:        //Update user deposit reverts if not enough gas => user must boost deposit with gas
1077:        //Withdraw Gas
1080:        //Unwrap Gas
1083:        //Replenish Gas
1092:        //Deposit Gas
1100:        //Cast to uint256
1102:        //Move Gas hTokens from Branch to Root / Mint Sufficient hTokens to match new port deposit
1124:        //Get Initial Gas Checkpoint
1127:        //Save Length
1130:        //Save deposited gas
1133:        //Store deposited gas for router interactions
1136:        //Action Recipient
1139:        //Get Action Flag
1142:        //DEPOSIT FLAG: 0 (No settlement)
1144:            //Get Settlement Nonce
1147:            //Check if tx has already been executed
1150:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1161:            //Update tx state as executed
1164:            //DEPOSIT FLAG: 1 (Single Asset Settlement)
1166:            //Get Settlement Nonce
1169:            //Check if tx has already been executed
1172:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1176:            //Try to execute remote request
1185:            //Update tx state as executed
1188:            //DEPOSIT FLAG: 2 (Multiple Settlement)
1190:            //Get deposit nonce
1193:            //Check if tx has already been executed
1196:                //Return true to avoid triggering anyFallback in case of `_forceRevert()` failure
1200:            //Try to execute remote request
1209:            //Update tx state as executed
1212:            //Unrecognized Function Selector
1215:            //Deduct gas costs from deposit and replenish this bridge agent's execution budget.
1222:        //Deduct gas costs from deposit and replenish this bridge agent's execution budget.
1233:        //Get Initial Gas Checkpoint
1236:        //Save Flag
1239:        //Save memory for Deposit Nonce
1244:            //Check nonce calldata slice.
1247:            //Make tokens available to depositor.
1252:            //Deduct gas costs from deposit and replenish this bridge agent's execution budget.
1261:            //Make tokens available to depositor.
1266:            //Deduct gas costs from deposit and replenish this bridge agent's execution budget.
1273:            //Save nonce
1276:            //Make tokens available to depositor.
1281:            //Deduct gas costs from deposit and replenish this bridge agent's execution budget.
1288:            //Save nonce
1293:            //Make tokens available to depositor.
1298:            //Deduct gas costs from deposit and replenish this bridge agent's execution budget.
1303:            //Unrecognized Function Selector
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L184


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolActions.sol
25:    //Any data passed through by the caller via the IUniswapV3PoolActions#swap call
45:        //Swap imbalanced token as long as we haven't used the entire amountSpecified and haven't reached the price limit
101:        //Get exact ticks depending on Optimizer's balances
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolActions.sol#L25


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
52:        //Get current price from the pool
73:        //Get current price from the pool
120:        //Calc base ticks
122:        //Calc amounts of token0 and token1 that can be stored in base range
125:        //Liquidity that can be stored in base range
127:        //Get imbalanced token
129:        //Calc new tick(upper or lower) for imbalanced token
231:        //Calc base ticks
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L52


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
11:    address tokenIn; //Token being sold
15:    uint128 depositedGas; //Gas deposited by user
16:    uint128 gasToBridgeOut; //Gas to be sent to bridge
20:    //zeroForOne when swapping gas from branch chain into root chain gas
22:    uint24 priceImpactPercentage; //Price impact percentage
23:    address poolAddress; //Uniswap V3 Pool Address
27:    Success, //Settlement was successful
28:    Failed //Settlement failed
32:    uint24 toChain; //Destination chain for interaction.
33:    uint128 gasToBridgeOut; //Gas owed to user
34:    address owner; //Owner of the settlement
35:    address recipient; //Recipient of the settlement.
36:    SettlementStatus status; //Status of the settlement
37:    address[] hTokens; //Input Local hTokens Addresses.
38:    address[] tokens; //Input Native / underlying Token Addresses.
39:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
40:    uint256[] deposits; //Amount of native tokens deposited for interaction.
41:    bytes callData; //Call data for settlement
45:    uint32 settlementNonce; //Settlement nonce.
46:    address recipient; //Recipient of the settlement.
47:    address hToken; //Input Local hTokens Address.
48:    address token; //Input Native / underlying Token Address.
49:    uint256 amount; //Amount of Local hTokens deposited for interaction.
50:    uint256 deposit; //Amount of native tokens deposited for interaction.
54:    uint8 numberOfAssets; //Number of assets to deposit.
55:    uint32 settlementNonce; //Settlement nonce.
56:    address recipient; //Recipient of the settlement.
57:    address[] hTokens; //Input Local hTokens Addresses.
58:    address[] tokens; //Input Native / underlying Token Addresses.
59:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
60:    uint256[] deposits; //Amount of native tokens deposited for interaction.
64:    //Deposit Info
65:    uint32 depositNonce; //Deposit nonce.
66:    address hToken; //Input Local hTokens Address.
67:    address token; //Input Native / underlying Token Address.
68:    uint256 amount; //Amount of Local hTokens deposited for interaction.
69:    uint256 deposit; //Amount of native tokens deposited for interaction.
70:    uint24 toChain; //Destination chain for interaction.
74:    //Deposit Info
75:    uint8 numberOfAssets; //Number of assets to deposit.
76:    uint32 depositNonce; //Deposit nonce.
77:    address[] hTokens; //Input Local hTokens Address.
78:    address[] tokens; //Input Native / underlying Token Address.
79:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
80:    uint256[] deposits; //Amount of native tokens deposited for interaction.
81:    uint24 toChain; //Destination chain for interaction.
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L11


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
27:    //Deposit Info
28:    address hToken; //Input Local hTokens Address.
29:    address token; //Input Native / underlying Token Address.
30:    uint256 amount; //Amount of Local hTokens deposited for interaction.
31:    uint256 deposit; //Amount of native tokens deposited for interaction.
32:    uint24 toChain; //Destination chain for interaction.
36:    //Deposit Info
37:    address[] hTokens; //Input Local hTokens Address.
38:    address[] tokens; //Input Native / underlying Token Address.
39:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
40:    uint256[] deposits; //Amount of native tokens deposited for interaction.
41:    uint24 toChain; //Destination chain for interaction.
45:    //Deposit Info
46:    uint32 depositNonce; //Deposit nonce.
47:    address hToken; //Input Local hTokens Address.
48:    address token; //Input Native / underlying Token Address.
49:    uint256 amount; //Amount of Local hTokens deposited for interaction.
50:    uint256 deposit; //Amount of native tokens deposited for interaction.
51:    uint24 toChain; //Destination chain for interaction.
52:    uint128 depositedGas; //BRanch chain gas token amount sent with request.
56:    //Deposit Info
57:    uint8 numberOfAssets; //Number of assets to deposit.
58:    uint32 depositNonce; //Deposit nonce.
59:    address[] hTokens; //Input Local hTokens Address.
60:    address[] tokens; //Input Native / underlying Token Address.
61:    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
62:    uint256[] deposits; //Amount of native tokens deposited for interaction.
63:    uint24 toChain; //Destination chain for interaction.
64:    uint128 depositedGas; //BRanch chain gas token amount sent with request.
77:    uint8 numberOfAssets; //Number of assets to deposit.
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L27



*Instances (484)*

### **[NC-9]** Consider using `delete` rather than assigning zero to clear values

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
78:        assetId[asset] = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L78


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
202:        incentive.totalRewardUnclaimed = 0;
268:            rewards[msg.sender] = 0;
279:        rewards[msg.sender] = 0;
406:                _userAttachements[owner][key.pool] = 0;
426:        deposit.stakedTimestamp = 0;
454:        stake.secondsPerLiquidityInsideInitialX128 = 0;
455:        stake.liquidityNoOverflow = 0;
456:        if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L202


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
149:        newProposal.eta = 0;
156:        newProposal.forVotes = 0;
157:        newProposal.againstVotes = 0;
158:        newProposal.abstainVotes = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L149


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
98:            rewardsAccrued[user] = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L98


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
249:        getUserBoost[msg.sender] = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L249


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
340:                    _delegatesVotesCount[user][delegatee] = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L340


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
211:        secondsAgo[1] = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L211



*Instances (17)*

### **[NC-10]** `Revert` should be used on some functions instead of `return`

Some instances just return without doing anything, consider applying revert statement instead with a descriptive string why it does that. 
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
104:        if (unstakePeriodEnd >= block.timestamp) return;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L104


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
105:        if (liquidity == 0) return; // no fees to collect when liquidity is zero
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L105


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
141:        if (liquidity == 0) return; // can't unstake when liquidity is zero
174:        if (liquidity == 0) return; // can't stake when liquidity is zero
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L141


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
805:        if (_fromChain == localChainId) return;
1288:        if (msg.sender == getBranchBridgeAgent[localChainId]) return;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L805


```solidity
File:2023-05-maia-main/src/hermes/UtilityManager.sol
70:        if (amount == 0) return;
79:        if (amount == 0) return;
88:        if (amount == 0) return;
111:        if (amount == 0) return;
120:        if (amount == 0) return;
129:        if (amount == 0) return;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/UtilityManager.sol#L70


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
318:        if (userFreeVotes >= votes) return;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L318


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
351:        if (_liquidity == 0) return;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L351


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
523:        if (userFreeWeight >= weight) return;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L523



*Instances (15)*

### **[NC-11]** Compute known value `off-chain`

If You know what data to hash, there is no need to consume more computational power to hash it using keccak256 , you’ll end up consuming 2x amount of gas.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
46:    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L46



*Instances (1)*

### **[NC-12]** Use of `bytes.concat()` instead of `abi.encodePacked()`

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
56:        bytes memory packedData = abi.encodePacked(bytes1(0x02), data);
101:        bytes memory packedData = abi.encodePacked(bytes1(0x04), data);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L56


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
51:        bytes memory packedData = abi.encodePacked(bytes1(0x01), data);
75:        bytes memory packedData = abi.encodePacked(bytes1(0x02), data);
108:        bytes memory packedData = abi.encodePacked(bytes1(0x03), data);
151:        bytes memory packedData = abi.encodePacked(bytes1(0x04), data);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L51


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
112:        bytes memory packedData = abi.encodePacked(bytes1(0x02), data);
163:        bytes memory packedData = abi.encodePacked(bytes1(0x01), data);
241:        bytes memory packedData = abi.encodePacked(bytes1(0x03), data);
262:        bytes memory packedData = abi.encodePacked(bytes1(0x04), data);
285:        bytes memory packedData = abi.encodePacked(bytes1(0x05), data);
312:        bytes memory packedData = abi.encodePacked(bytes1(0x06), data);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L112


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
348:        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L348


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
278:            abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));
558:        bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L278


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
435:        bytes memory packedData = abi.encodePacked(bytes1(0x08), _depositNonce, msg.value.toUint128(), uint128(0));
481:            abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas);
657:            abi.encodePacked(bytes1(0x01), depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L435



*Instances (18)*

### **[NC-13]** Variables need not be initialized to zero

The default value for variables is zero, so initializing them to zero is superfluous.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
48:        for (uint256 i = 0; i < calls.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L48


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol
78:        for (uint256 i = 0; i < length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol#L78


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
95:        for (uint256 i = 0; i < assets.length; i++) {
111:        for (uint256 i = 0; i < assets.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L95


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
64:        for (uint256 i = 0; i < length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L64


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
99:        for (uint256 i = 0; i < length;) {
107:        for (uint256 i = 0; i < length;) {
110:            for (uint256 j = 0; j < length;) {
123:        for (uint256 i = 0; i < length;) {
145:        for (uint256 i = 0; i < length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L99


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
101:        for (uint256 i = 0; i < _localAddresses.length;) {
136:        for (uint256 i = 0; i < _localAddresses.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L101


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
176:        for (uint256 i = 0; i < size; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L176


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
231:        for (uint256 i = 0; i < _localAddresses.length;) {
267:        for (uint256 i = 0; i < _localAddresses.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L231


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
364:        for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L364


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
146:        for (uint256 i = 0; i < outputTokens.length;) {
311:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
387:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
463:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L146


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
456:        if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L456


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
181:        for (uint256 i = 0; i < proposal.targets.length; i++) {
215:        for (uint256 i = 0; i < proposal.targets.length; i++) {
251:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L181


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
342:        for (uint256 i = 0; i < _globalAddresses.length;) {
408:        for (uint256 i = 0; i < _dParams.hTokens.length;) {
561:        for (uint256 i = 0; i < newGas.length;) {
596:        for (uint256 i = 0; i < settlement.hTokens.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L342


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
283:        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
580:        for (uint256 i = 0; i < numOfAssets;) {
719:        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
951:        for (uint256 i = 0; i < deposit.hTokens.length;) {
1354:        for (uint256 i = 0; i < _deposits.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L283


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
114:        for (uint256 i = 0; i < length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L114


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
77:        for (uint256 i = 0; i < length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L77


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
50:        for (uint256 i = 0; i < length;) {
68:        for (uint256 i = 0; i < length;) {
79:        for (uint256 i = 0; i < length;) {
170:        for (uint256 i = 0; i < length;) {
201:        for (uint256 i = 0; i < length;) {
216:        for (uint256 i = 0; i < length;) {
235:        for (uint256 i = 0; i < length;) {
250:        for (uint256 i = 0; i < length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L50


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
52:        for (uint256 i = 0; i < num;) {
98:        for (uint256 i = 0; i < num;) {
151:        uint256 userBoost = 0;
156:        for (uint256 i = 0; i < length;) {
207:        for (uint256 i = 0; i < num && i < length;) {
236:        for (uint256 i = 0; i < size;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L52


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
67:        uint256 low = 0;
328:        for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L67


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
114:        for (uint256 i = 0; i < num;) {
155:        for (uint256 i = 0; i < num;) {
259:        for (uint256 i = 0; i < size;) {
338:        for (uint256 i = 0; i < size;) {
536:        for (uint256 i = 0; i < size && (userFreeWeight + totalFreed) < weight;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L114



*Instances (56)*

### **[NC-14]** Use a single file for all system-wide constants

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
32:    uint8 internal constant PARAMS_START = 1;
34:    uint8 internal constant PARAMS_START_SIGNED = 21;
36:    uint8 internal constant PARAMS_END_SIGNED_OFFSET = 26;
38:    uint8 internal constant PARAMS_ENTRY_SIZE = 32;
40:    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;
42:    uint8 internal constant PARAMS_TKN_SET_SIZE = 128;
44:    uint8 internal constant PARAMS_GAS_OUT = 16;
48:    uint8 internal constant PARAMS_TKN_START = 5;
50:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
52:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L32


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
24:    uint256 internal constant week = 86400 * 7;
26:    uint256 internal constant base = 1000;
28:    uint256 internal constant max_tail_emission = 100;
29:    uint256 internal constant max_dao_share = 300;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L24


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
56:    uint256 private constant DIVISIONER = 10000;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L56


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
47:    uint24 private constant protocolFee = 2 * 1e5; //20%
48:    uint24 private constant GLOBAL_DIVISIONER = 1e6;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L47


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
91:    uint256 internal constant DIVISIONER = 1e4;
92:    uint256 internal constant MIN_RESERVE_RATIO = 3e3;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L91


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
33:    uint8 internal constant PARAMS_START = 1;
35:    uint8 internal constant PARAMS_START_SIGNED = 21;
37:    uint8 internal constant PARAMS_END_OFFSET = 9;
39:    uint8 internal constant PARAMS_END_SIGNED_OFFSET = 29;
41:    uint8 internal constant PARAMS_ENTRY_SIZE = 32;
43:    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;
45:    uint8 internal constant PARAMS_TKN_SET_SIZE = 104;
47:    uint8 internal constant PARAMS_TKN_SET_SIZE_MULTIPLE = 128;
49:    uint8 internal constant PARAMS_GAS_IN = 32;
51:    uint8 internal constant PARAMS_GAS_OUT = 16;
55:    uint8 internal constant PARAMS_TKN_START = 5;
57:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
59:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L33


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
9:    string public constant name = "vMaia Governor Bravo";
12:    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken
15:    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken
18:    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks
21:    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks
24:    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks
27:    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks
30:    uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken
33:    uint256 public constant proposalMaxOperations = 10; // 10 actions
36:    uint256 public constant DIVISIONER = 1 ether;
42:    bytes32 public constant DOMAIN_TYPEHASH =
46:    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L9


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
43:    uint256 private constant MIN_SWAP_AMOUNT = 1e4;
46:    uint256 private constant MAX_TOTAL_WEIGHT = 256;
49:    uint256 private constant MAX_DESTINATIONS = 15;
52:    uint256 private constant MAX_PROTOCOL_FEE = 1e16;
55:    uint256 private constant MAX_LAMBDA1 = 1e17;
58:    uint256 private constant MIN_SIGMA2 = 1e16;
65:    uint256 private constant DIVISIONER = 1 ether;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L43


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
100:    uint8 internal constant PARAMS_START = 1;
102:    uint8 internal constant PARAMS_START_SIGNED = 21;
104:    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;
106:    uint8 internal constant PARAMS_GAS_IN = 32;
108:    uint8 internal constant PARAMS_GAS_OUT = 16;
112:    uint8 internal constant PARAMS_TKN_START = 5;
114:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
116:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
180:    uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; // 100_000 for anycall + 55_000 for fallback
181:    uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution
640:    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L100


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
68:    uint8 internal constant PARAMS_START = 1;
70:    uint8 internal constant PARAMS_START_SIGNED = 21;
72:    uint8 internal constant PARAMS_ENTRY_SIZE = 32;
74:    uint8 internal constant PARAMS_GAS_OUT = 16;
78:    uint8 internal constant PARAMS_TKN_START = 5;
80:    uint8 internal constant PARAMS_AMT_OFFSET = 64;
82:    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;
137:    uint256 internal constant MIN_FALLBACK_RESERVE = 185_000; // 100_000 for anycall + 85_000 fallback execution overhead
138:    uint256 internal constant MIN_EXECUTION_OVERHEAD = 160_000; // 100_000 for anycall + 35_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Executions
139:    uint256 internal constant TRANSFER_OVERHEAD = 24_000;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L68


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
53:    uint256 internal constant WEEK = 1 weeks;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L53


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
148:    uint256 private constant ONE = 1e18;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L148


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
360:    bytes32 public constant DELEGATION_TYPEHASH =
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L360


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
77:    uint24 internal constant MULTIPLIER = 1e6;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L77


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol
8:    uint256 public constant FLAG_NONE = 0x0;
9:    uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;
10:    uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;
11:    uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;
12:    uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;
15:    uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;
16:    uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/lib/AnycallFlags.sol#L8


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
13:    uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))
15:    uint256 private constant INCENTIVES_OFFSET = 12 hours;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L13


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
23:    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L23



*Instances (86)*

### **[NC-15]** Use `encode` instead of `encodePacked` for hashig

Use of abi.encodePacked is safe, but unnecessary and not recommended. abi.encodePacked can result in hash collisions when used with two dynamic arguments (string/bytes).
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
348:        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L348



*Instances (1)*

### **[NC-16]** Skip `== true` and `== false` checks

It’s a tad more efficient to skip checking expressions that evaluate to booleans against `true` / `false`.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
366:        require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L366



*Instances (1)*

### **[NC-17]** Not using the named return variables anywhere in the function is confusing

Consider changing the variable to be an unnamed one, since the variable is never assigned, nor is it returned by name. If the optimizer is not turned on, leaving the code as it is will also waste gas for the stack variable.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/talos/TalosManager.sol
98:            return (false, "");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosManager.sol#L98


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
154:            return (false, "unknown selector");
156:        return (true, "");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L154


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
114:        return (circulatingSupply() * tailEmission) / base;
119:        return (vault.totalAssets() * _minted) / HERMES(underlying).totalSupply();
154:        return _period;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L114


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol
108:        return (false, "unknown selector");
119:        return (false, "unknown selector");
130:        return (false, "unknown selector");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol#L108


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
75:        return _createPool(asset, owner);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L75


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
279:            return (false, "unknown selector");
281:        return (true, "");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L279


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
344:            return (false, "unknown selector");
346:        return (true, "");
373:            return (false, "unknown selector");
375:        return (true, "");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L344


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
150:        return ((_currBalance + getStrategyTokenDebt[_token]) * getMinimumTokenReserveRatio[_token]) / DIVISIONER;
389:        return _decimals == 18 ? _amount : _amount * 1 ether / (10 ** _decimals);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L150


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
232:            return (false, "FuncID not recognized!");
235:        return (true, "");
329:            return (false, "FuncID not recognized!");
332:        return (true, "");
405:            return (false, "FuncID not recognized!");
408:        return (true, "");
481:            return (false, "FuncID not recognized!");
484:        return (true, "");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L232


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
175:        return _getLocalToken(_localAddress, _fromChain, _toChain);
195:        return _getUnderlyingTokenFromGlobal(_globalAddress, _fromChain);
210:        return _isGlobalToken(_globalAddress, _fromChain);
229:        return _isLocalToken(_localAddress, _fromChain, _toChain);
234:        return _getLocalToken(_localAddress, _fromChain, _toChain) != address(0);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L175


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
279:        return (p.targets, p.values, p.signatures, p.calldatas);
390:        return (whitelistAccountExpirations[account] > block.timestamp);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L279


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
736:        return (uint256(!zeroForOneOnInflow ? amount1 : amount0), gasTokenGlobalAddress);
904:            return (true, "Not enough gas to bridge out");
922:                return (true, "already executed tx");
947:                return (true, "already executed tx");
972:                return (true, "already executed tx");
996:                return (true, "already executed tx");
1020:                return (true, "already executed tx");
1056:                return (true, "already executed tx");
1091:                return (true, "already executed tx");
1126:                return (true, "already executed tx");
1155:                return (true, "already executed tx");
1165:            return (false, "unknown selector");
1215:        return (true, "");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L736


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
1151:                return (true, "already executed tx");
1173:                return (true, "already executed tx");
1197:                return (true, "already executed tx");
1217:            return (false, "unknown selector");
1255:            return (true, "");
1269:            return (true, "");
1284:            return (true, "");
1301:            return (true, "");
1305:            return (false, "unknown selector");
1341:        return _decimals == 18 ? _amount : _amount * (10 ** _decimals) / 1 ether;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1151


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
66:        return _accrue(ERC20(msg.sender), user);
71:        return _accrue(strategy, user);
87:        if (index == 0) return (0, 0);
90:        return (accrueUser(strategy, user, index), accrueUser(strategy, secondUser, index));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L66


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
162:        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
168:        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L162


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
46:        return _gauges.values();
62:        return _gauges.contains(gauge) && !_deprecatedGauges.contains(gauge);
67:        return _gauges.length();
72:        return _deprecatedGauges.values();
77:        return _deprecatedGauges.length();
87:        return _userGauges[user].values();
92:        return _userGauges[user].contains(gauge);
108:        return _userGauges[user].length();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L46


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
33:        return _checkpoints[account][pos];
38:        return _checkpoints[account].length.toUint32();
60:        return _checkpointsLookup(_checkpoints[account], blockNumber);
82:        return (a & b) + (a ^ b) / 2;
127:        return _delegatesVotesCount[delegator][delegatee];
132:        return _delegates[delegator].values();
137:        return _delegates[delegator].length();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L33


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
71:        return _getGaugeCycleEnd();
77:            return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow
83:        return _getGaugeWeight[gauge].currentWeight;
89:        return _getStoredWeight(_getGaugeWeight[gauge], _getGaugeCycleEnd());
98:        return _totalWeight.currentWeight;
103:        return _getStoredWeight(_totalWeight, _getGaugeCycleEnd());
108:        return _gauges.values();
124:        return _gauges.contains(gauge) && !_deprecatedGauges.contains(gauge);
129:        return _gauges.length();
134:        return _deprecatedGauges.values();
139:        return _deprecatedGauges.length();
144:        return _userGauges[user].values();
149:        return _userGauges[user].contains(gauge);
165:        return _userGauges[user].length();
180:        return (quantity * weight) / total;
191:        return _incrementUserAndGlobalWeights(msg.sender, weight, currentCycle);
269:        return _incrementUserAndGlobalWeights(msg.sender, weightsSum, currentCycle);
281:        return _decrementUserWeights(msg.sender, weight);
351:        return _decrementUserWeights(msg.sender, weightsSum);
399:        return _addGauge(gauge);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L71



*Instances (97)*

### **[NC-19]** Use modifier instead of require/if for special `msg.sender`

If functions are only allowed to be called by the special actor, modifier should be used instead of checking with require statement, if actor is the msg.sender calling the function.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegator.sol
42:        require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegator.sol#L42


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
509:        require(msg.sender == bridgeAgentExecutorAddress, "Unauthorized Caller");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L509


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
64:        require(msg.sender == admin, "GovernorBravo::initialize: admin only");
398:        require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");
414:        require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");
431:        require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");
462:        require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");
475:        require(msg.sender == admin, "GovernorBravo::_initiate: admin only");
489:        require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L64



*Instances (9)*

### **[NC-19]** Unsigned divisions can be marked as `unchecked`

Divisions which do not divide by -1 cannot overflow or overflow so such operations can be unchecked to save gas
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
136:            uint256 share = (_required * daoShare) / base;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L136


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
119:            uint256 newProtocolRewards = (pendingRewards * protocolFee) / DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L119


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
120:        uint256 earnedProtocolFees0 = (collect0 * _protocolFee) / _GLOBAL_DIVISIONER;
121:        uint256 earnedProtocolFees1 = (collect1 * _protocolFee) / _GLOBAL_DIVISIONER;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L120


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
50:            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
84:        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L84


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
201:        uint256 supplierDelta = (supplierTokens * deltaIndex) / ONE;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L201


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
40:        uint256 epochDay = timestamp / 86400;
57:            uint256 day = timestamp / 86400;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L40



*Instances (9)*

### **[NC-20]** Non-external/public function names should begin with an underscore

According to the Solidity Style Guide, Non-external/public variable and function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol
32:    function getNextCycleRewards(ERC20 strategy) internal override returns (uint256) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol#L32


```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
53:    function distribute(uint256 amount) internal override {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L53


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegator.sol
59:    function delegateTo(address callee, bytes memory data) internal {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegator.sol#L59


```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
102:    function beforeWithdraw(uint256, uint256) internal override {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L102


```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
76:    function newGauge(address strategy, bytes memory data) internal override returns (BaseV2Gauge) {
89:    function afterCreateGauge(address strategy, bytes memory) internal override {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L76


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
110:    function updateAssetBalances() internal {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L110


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
72:    function beforeRedeem(uint256 _tokenId, address) internal override {
79:    function afterRedeem(uint256 _tokenId) internal override {}
83:    function beforeDeposit(uint256 _tokenId, address) internal override {
90:    function afterDeposit(uint256 _tokenId) internal override {}
94:    function beforeRerange(uint256 _tokenId) internal override {
100:    function afterRerange(uint256 _tokenId) internal override {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L72


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
101:    function beforeRedeem(uint256 _tokenId, address _owner) internal override {
108:    function afterRedeem(uint256 _tokenId) internal override {
114:    function beforeDeposit(uint256 _tokenId, address _receiver) internal override {
121:    function afterDeposit(uint256 _tokenId) internal override {
127:    function beforeRerange(uint256 _tokenId) internal override {
134:    function afterRerange(uint256 _tokenId) internal override {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L101


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
293:    function mint(address _to, address _hToken, uint256 _amount, uint24 _fromChain) internal {
349:    function addVirtualAccount(address _user) internal returns (VirtualAccount newAccount) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L293


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
361:    function castVoteInternal(address voter, uint256 proposalId, uint8 support) internal returns (uint96) {
525:    function add256(uint256 a, uint256 b) internal pure returns (uint256) {
531:    function sub256(uint256 a, uint256 b) internal pure returns (uint256) {
536:    function getChainIdInternal() internal view returns (uint256) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L361


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
1200:    function beforeDeposit(uint256 assets) internal override returns (uint256 shares) {
1209:    function beforeMint(uint256 shares) internal override returns (uint256 assets) {
1218:    function afterRedeem(uint256 shares) internal override returns (uint256 assets) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L1200


```solidity
File:2023-05-maia-main/src/rewards/depots/RewardsDepot.sol
19:    function transferRewards(address _asset, address _rewardsContract) internal returns (uint256 balance) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/RewardsDepot.sol#L19


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
60:    function getNextCycleRewards(ERC20 strategy) internal virtual returns (uint256);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L60


```solidity
File:2023-05-maia-main/src/talos/strategies/TalosStrategySimple.sol
30:    function doRerange() internal override returns (uint256 amount0, uint256 amount1) {
36:    function doRebalance() internal override returns (uint256 amount0, uint256 amount1) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/strategies/TalosStrategySimple.sol#L30


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
111:    function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L111


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
129:    function beforeDeposit(uint256 assets) internal virtual returns (uint256 shares);
132:    function beforeMint(uint256 shares) internal virtual returns (uint256 assets);
135:    function afterRedeem(uint256 shares) internal virtual returns (uint256 assets);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L129


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
98:    function distribute(uint256 amount) internal virtual;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L98


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
171:    function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
173:    function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L171


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
125:    function afterCreateGauge(address strategy, bytes memory data) internal virtual;
127:    function newGauge(address strategy, bytes memory data) internal virtual returns (BaseV2Gauge gauge);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L125


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
292:    function beforeWithdraw(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}
294:    function afterDeposit(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L292


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
80:    function average(uint256 a, uint256 b) internal pure returns (uint256) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L80


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
373:    function beforeDeposit(uint256 _tokenId, address _receiver) internal virtual;
375:    function afterDeposit(uint256 _tokenId) internal virtual;
377:    function beforeRedeem(uint256 _tokenId, address _owner) internal virtual;
379:    function afterRedeem(uint256 _tokenId) internal virtual;
381:    function beforeRerange(uint256 _tokenId) internal virtual;
383:    function afterRerange(uint256 _tokenId) internal virtual;
385:    function doRerange() internal virtual returns (uint256 amount0, uint256 amount1);
387:    function doRebalance() internal virtual returns (uint256 amount0, uint256 amount1);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L373


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol
16:    function compute(IUniswapV3Staker.IncentiveKey memory key) internal pure returns (bytes32 incentiveId) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveId.sol#L16


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
39:    function getMonth(uint256 timestamp) internal pure returns (uint256 month) {
55:    function isTuesday(uint256 timestamp) internal pure returns (bool result, uint256 startOfDay) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L39


```solidity
File:2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol
17:    function computeStart(uint256 timestamp) internal pure returns (uint96 start) {
23:    function computeEnd(uint256 timestamp) internal pure returns (uint96 end) {
31:    function getEnd(uint96 start) internal pure returns (uint96 end) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/libraries/IncentiveTime.sol#L17


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolActions.sol
38:    function swapToEqualAmounts(ActionParams memory actionParams, int24 baseThreshold) internal {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolActions.sol#L38


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolVariables.sol
88:    function checkRange(int24 tickLower, int24 tickUpper) internal pure {
96:    function floor(int24 tick, int24 tickSpacing) internal pure returns (int24) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolVariables.sol#L88



*Instances (61)*

### **[NC-21]** Uppercase immutable variables

Variable does not follow [Solidity naming best pratices](https://docs.soliditylang.org/en/v0.4.24/style-guide.html#constants)
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol
42:    bHermesGauges private immutable bhermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L42


```solidity
File:2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol
13:    address private immutable asset;
16:    address private immutable rewardsContract;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/SingleRewardsDepot.sol#L13


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
20:    address public immutable bHermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L20


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol
22:    SingleRewardsDepot public immutable rewardsDepot;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelInstantRewards.sol#L22


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
24:    address public immutable bHermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L24


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
18:    address public immutable bHermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L18


```solidity
File:2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol
19:    UniswapV3Staker public immutable uniswapV3Staker;
22:    ERC20 public immutable hermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol#L19


```solidity
File:2023-05-maia-main/src/gauges/UniswapV3Gauge.sol
20:    address public immutable override uniswapV3Staker;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/UniswapV3Gauge.sol#L20


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
20:    address public immutable userAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L20


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
16:    uint256 public immutable localChainId;
19:    address public immutable rootPortAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L16


```solidity
File:2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol
26:    BoostAggregatorFactory public immutable boostAggregatorFactory;
29:    FlywheelCoreInstant public immutable flywheel;
32:    FlywheelInstantRewards public immutable rewards;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosStrategyStakedFactory.sol#L26


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
13:    uint24 public immutable localChainId;
16:    address immutable localPortAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L13


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
18:    ERC20 public immutable override bHermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L18


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
20:    uint24 public immutable rootChainId;
23:    WETH9 public immutable wrappedNativeToken;
26:    address public immutable rootPortAddress;
29:    address public immutable daoAddress;
32:    address public immutable localAnyCallAddress;
35:    address public immutable localAnyCallExecutorAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L20


```solidity
File:2023-05-maia-main/src/gauges/factories/BribesFactory.sol
24:    uint256 public immutable rewardsCycleLength;
26:    FlywheelBoosterGaugeWeight private immutable flywheelGaugeWeightBooster;
41:    BaseV2GaugeManager public immutable gaugeManager;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BribesFactory.sol#L24


```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
35:    UniswapV3Staker public immutable override uniswapV3Staker;
38:    FlywheelGaugeRewards public immutable override flywheelGaugeRewards;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L35


```solidity
File:2023-05-maia-main/src/talos/TalosManager.sol
22:    int24 public immutable ticksFromLowerRebalance;
25:    int24 public immutable ticksFromUpperRebalance;
28:    int24 public immutable ticksFromLowerRerange;
31:    int24 public immutable ticksFromUpperRerange;
34:    ITalosBaseStrategy public immutable strategy;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosManager.sol#L22


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
19:    uint256 public immutable id;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L19


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
23:    bHermesGauges public immutable bHermesGaugeWeight;
26:    bHermesBoost public immutable bHermesGaugeBoost;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L23


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
18:    uint256 public immutable localChainId;
21:    uint256 public immutable rootChainId;
24:    address public immutable rootBridgeAgentFactoryAddress;
27:    WETH9 public immutable wrappedNativeToken;
30:    address public immutable localCoreBranchRouterAddress;
33:    address public immutable localPortAddress;
36:    address public immutable localAnyCallAddress;
39:    address public immutable localAnyCallExecutorAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L18


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
32:    address public immutable override underlying;
34:    ERC4626 public immutable override vault;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L32


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
27:    INonfungiblePositionManager public immutable nonfungiblePositionManager;
30:    UniswapV3Staker public immutable uniswapV3Staker;
33:    bHermesBoost public immutable hermesGaugeBoost;
36:    ERC20 public immutable hermes;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L27


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
46:    BoostAggregator public immutable override boostAggregator;
49:    FlywheelCoreInstant public immutable flywheel;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L46


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
27:    ERC20Gauges public immutable override gaugeToken;
30:    IBaseV2Minter public immutable minter;
33:    address public immutable override rewardToken;
39:    uint32 public immutable override gaugeCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L27


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
40:    WETH9 public immutable wrappedNativeToken;
43:    uint24 public immutable rootChainId;
46:    address public immutable rootPortAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L40


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
51:    uint256 public immutable localChainId;
54:    address public immutable localPortAddress;
57:    address public immutable multicallAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L51


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
28:    uint24 public immutable localChainId;
31:    address public immutable wrappedNativeTokenAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L28


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
91:    IUniswapV3GaugeFactory public immutable uniswapV3GaugeFactory;
94:    IUniswapV3Factory public immutable override factory;
97:    INonfungiblePositionManager public immutable override nonfungiblePositionManager;
100:    uint256 public immutable override maxIncentiveStartLeadTime;
103:    address public immutable minter;
106:    address public immutable hermes;
109:    bHermesBoost public immutable hermesGaugeBoost;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L91


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
25:    UlyssesFactory public immutable factory;
28:    uint256 public immutable id;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L25


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
123:    uint24 public immutable localChainId;
126:    WETH9 public immutable wrappedNativeToken;
129:    address public immutable factoryAddress;
132:    address public immutable daoAddress;
135:    address public immutable localRouterAddress;
138:    address public immutable localPortAddress;
141:    address public immutable localAnyCallAddress;
144:    address public immutable localAnyCallExecutorAddress;
147:    address public immutable bridgeAgentExecutorAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L123


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
89:    uint256 public immutable rootChainId;
92:    uint256 public immutable localChainId;
95:    WETH9 public immutable wrappedNativeToken;
98:    address public immutable rootBridgeAgentAddress;
101:    address public immutable localAnyCallAddress;
104:    address public immutable localAnyCallExecutorAddress;
107:    address public immutable localRouterAddress;
110:    address public immutable localPortAddress;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L89


```solidity
File:2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol
26:    address public immutable override rewardToken;
29:    FlywheelCore public immutable override flywheel;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/BaseFlywheelRewards.sol#L26


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
23:    uint256 public immutable override rewardsCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L23


```solidity
File:2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol
23:    INonfungiblePositionManager public immutable nonfungiblePositionManager;
26:    OptimizerFactory public immutable optimizerFactory;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
21:    ERC20 public immutable asset;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L21


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
22:    address public immutable asset;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L22


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
26:    address public immutable override rewardToken;
29:    bHermesBoost public immutable hermesGaugeBoost;
32:    FlywheelGaugeRewards public immutable override flywheelGaugeRewards;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L26


```solidity
File:2023-05-maia-main/src/hermes/UtilityManager.sol
25:    bHermesGauges public immutable gaugeWeight;
27:    bHermesBoost public immutable gaugeBoost;
29:    ERC20Votes public immutable governance;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/UtilityManager.sol#L25


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
21:    ERC20 public immutable asset;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L21


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
24:    BaseV2GaugeManager public immutable override gaugeManager;
27:    bHermesBoost public immutable override bHermesBoostToken;
30:    BribesFactory public immutable override bribesFactory;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L24


```solidity
File:2023-05-maia-main/src/maia/PartnerUtilityManager.sol
23:    ERC20Votes public immutable partnerGovernance;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/PartnerUtilityManager.sol#L23


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
24:    address public immutable override rewardToken;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L24


```solidity
File:2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol
31:    PartnerManagerFactory public immutable override factory;
34:    bHermes public immutable override bHermesToken;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/ERC4626PartnerManager.sol#L31


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
61:    ERC20 public immutable override token0;
63:    ERC20 public immutable override token1;
65:    int24 public immutable override tickSpacing;
67:    uint24 public immutable override poolFee;
69:    IUniswapV3Pool public immutable override pool;
71:    ITalosOptimizer public immutable override optimizer;
73:    address public immutable strategyManager;
75:    INonfungiblePositionManager public immutable override nonfungiblePositionManager;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L61


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
39:    uint32 public immutable override gaugeCycleLength;
42:    uint32 public immutable override incrementFreezeWindow;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L39



*Instances (123)*

### **[NC-22]** Using `>/>=` without specifying an upper bound is unsafe

There will be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L2


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
3:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L3


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L2


```solidity
File:2023-05-maia-main/src/talos/strategies/TalosStrategySimple.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/strategies/TalosStrategySimple.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L2


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
3:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L3


```solidity
File:2023-05-maia-main/src/erc-4626/interfaces/IERC4626.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/interfaces/IERC4626.sol#L2


```solidity
File:2023-05-maia-main/src/talos/interfaces/IBoostAggregator.sol
2:pragma solidity >=0.8.0;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/interfaces/IBoostAggregator.sol#L2



*Instances (11)*

### **[NC-23]** Contract name must be in CamelCase.

Consider renaming to camelCase
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol
18:contract bHermesBoost is ERC20Boost, IbHermesUnderlying {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesBoost.sol#L18


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol
22:contract bHermesGauges is ERC20Gauges, IbHermesUnderlying {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesGauges.sol#L22


```solidity
File:2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol
16:contract bHermesVotes is ERC20MultiVotes, IbHermesUnderlying {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/bHermesVotes.sol#L16


```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
26:contract vMaia is ERC4626PartnerManager {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L26


```solidity
File:2023-05-maia-main/src/hermes/bHermes.sol
52:contract bHermes is UtilityManager, ERC4626DepositOnly {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/bHermes.sol#L52



*Instances (5)*

### **[NC-24]** block.timestamp used as time proxy

block.timestamp is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/maia/vMaia.sol
59:        currentMonth = DateTimeLib.getMonth(block.timestamp);
104:        if (unstakePeriodEnd >= block.timestamp) return;
106:        uint256 _currentMonth = DateTimeLib.getMonth(block.timestamp);
109:        (bool isTuesday, uint256 _unstakePeriodStart) = DateTimeLib.isTuesday(block.timestamp);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/vMaia.sol#L59


```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
82:        activePeriod = (block.timestamp / week) * week;
126:        if (block.timestamp >= _period + week && initializer == address(0)) {
127:            _period = (block.timestamp / week) * week;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L82


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyVanilla.sol
148:                    deadline: block.timestamp
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyVanilla.sol#L148


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
60:        gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
79:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
114:        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L60


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
194:        if (block.timestamp - lastManaged[msg.sender][_token] >= 1 days) {
198:        lastManaged[msg.sender][_token] = block.timestamp;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L194


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
140:        uint96 startTime = IncentiveTime.computeEnd(block.timestamp);
164:        if (startTime <= block.timestamp) revert IncentiveStartTimeMustBeNowOrInTheFuture();
165:        if (startTime - block.timestamp > maxIncentiveStartLeadTime) {
188:        if (block.timestamp < IncentiveTime.getEnd(key.startTime)) {
296:            IncentiveTime.getEndAndDuration(key.startTime, deposit.stakedTimestamp, block.timestamp);
332:            block.timestamp
369:            IncentiveTime.getEndAndDuration(key.startTime, deposit.stakedTimestamp, block.timestamp);
374:        if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();
435:            block.timestamp
479:        IncentiveKey memory key = IncentiveKey({pool: pool, startTime: IncentiveTime.computeStart(block.timestamp)});
501:        deposits[tokenId].stakedTimestamp = uint40(block.timestamp);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L140


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
180:        uint256 eta = add256(block.timestamp, timelock.delay());
314:        } else if (block.timestamp >= add256(proposal.eta, timelock.GRACE_PERIOD())) {
390:        return (whitelistAccountExpirations[account] > block.timestamp);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L180


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol
43:        uint32 timestamp = block.timestamp.toUint32();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L43


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
68:        epoch = (block.timestamp / WEEK) * WEEK;
84:        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L68


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
364:        require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L364


```solidity
File:2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol
147:                deadline: block.timestamp
208:                deadline: block.timestamp
269:                    deadline: block.timestamp
359:                deadline: block.timestamp
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/base/TalosBaseStrategy.sol#L147


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
75:        uint32 nowPlusOneCycle = block.timestamp.toUint32() + gaugeCycleLength;
205:            if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L75


```solidity
File:2023-05-maia-main/src/talos/libraries/PoolActions.sol
85:                deadline: block.timestamp
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/libraries/PoolActions.sol#L85



*Instances (38)*

### **[NC-25]** Empty Catch Block

Each of the following try/catch instances has an empty catch block when making an external function call. Consider refactoring them to correspondingly emit their anticipated logged error.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
152:            try flywheelGaugeRewards.queueRewardsForCycle() {} catch {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L152


```solidity
File:2023-05-maia-main/src/talos/TalosStrategyStaked.sol
179:        } catch {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/TalosStrategyStaked.sol#L179


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
1240:        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L1240


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
1328:        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328



*Instances (4)*

### **[NC-26]** Consider using named mappings

Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/a/145555) to make it easier to understand the purpose of each mapping.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol
20:    mapping(ERC20 => RewardsDepot) public override rewardsDepots;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelBribeRewards.sol#L20


```solidity
File:2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol
28:    mapping(BoostAggregator => uint256) public boostAggregatorIds;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/BoostAggregatorFactory.sol#L28


```solidity
File:2023-05-maia-main/src/talos/factories/OptimizerFactory.sol
17:    mapping(TalosOptimizer => uint256) public optimizerIds;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/OptimizerFactory.sol#L17


```solidity
File:2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol
17:    mapping(address => address) private _assets;
20:    mapping(address => bool) private _isRewardsContract;
23:    mapping(address => bool) private _isAsset;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol#L17


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol
28:    mapping(uint256 => uint256) public getTokenBalance;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L28


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
27:    mapping(PartnerManager => uint256) public override partnerIds;
30:    mapping(IBaseVault => uint256) public override vaultIds;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L27


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol
15:    mapping(uint256 => UlyssesPool) private pools;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesRouter.sol#L15


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
38:    mapping(address => address) public getBridgeAgentManager;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L38


```solidity
File:2023-05-maia-main/src/gauges/factories/BribesFactory.sol
32:    mapping(FlywheelCore => uint256) public bribeFlywheelIds;
35:    mapping(FlywheelCore => bool) public activeBribeFlywheels;
38:    mapping(address => FlywheelCore) public flywheelTokens;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BribesFactory.sol#L32


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
32:    mapping(BaseV2GaugeFactory => uint256) public gaugeFactoryIds;
35:    mapping(BaseV2GaugeFactory => bool) public activeGaugeFactories;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L32


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
39:    mapping(address => address) public userToRewardsDepot;
42:    mapping(uint256 => address) public tokenIdToUser;
45:    mapping(uint256 => uint256) public tokenIdRewards;
48:    mapping(address => bool) public whitelistedAddresses;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L39


```solidity
File:2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol
55:    mapping(uint256 => UlyssesPool) public pools;
58:    mapping(uint256 => UlyssesToken) public tokens;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/factories/UlyssesFactory.sol#L55


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol
100:    mapping(uint256 => Proposal) public proposals;
103:    mapping(address => uint256) public latestProposalIds;
163:    mapping(address => uint256) public whitelistAccountExpirations;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol#L100


```solidity
File:2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol
42:    mapping(ERC20 => QueuedRewards) public override gaugeQueuedRewards;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/rewards/FlywheelGaugeRewards.sol#L42


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
27:    mapping(address => bool) public isBridgeAgent;
40:    mapping(address => bool) public isBridgeAgentFactory;
54:    mapping(address => bool) public isStrategyToken;
63:    mapping(address => uint256) public getStrategyTokenDebt;
66:    mapping(address => uint256) public getMinimumTokenReserveRatio;
71:    mapping(address => mapping(address => bool)) public isPortStrategy;
80:    mapping(address => mapping(address => uint256)) public getPortStrategyTokenDebt;
83:    mapping(address => mapping(address => uint256)) public lastManaged;
86:    mapping(address => mapping(address => uint256)) public strategyDailyLimitAmount;
89:    mapping(address => mapping(address => uint256)) public strategyDailyLimitRemaining;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L27


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootPort.sol
47:    mapping(address => VirtualAccount) public getUserAccount;
51:    mapping(VirtualAccount => mapping(address => bool)) public isRouterApproved;
58:    mapping(uint256 => bool) public isChainId;
61:    mapping(address => bool) public isBridgeAgent;
70:    mapping(address => address) public getBridgeAgentManager;
77:    mapping(address => bool) public isBridgeAgentFactory;
90:    mapping(address => bool) public isGlobalAddress;
93:    mapping(address => mapping(uint256 => address)) public getGlobalTokenFromLocal;
96:    mapping(address => mapping(uint256 => address)) public getLocalTokenFromGlobal;
99:    mapping(address => mapping(uint256 => address)) public getLocalTokenFromUnder;
102:    mapping(address => mapping(uint256 => address)) public getUnderlyingTokenFromLocal;
109:    mapping(uint256 => address) public getWrappedNativeToken;
112:    mapping(uint256 => GasPoolInfo) public getGasPoolInfo;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootPort.sol#L47


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
34:    mapping(address => IUniswapV3Pool) public gaugePool;
37:    mapping(IUniswapV3Pool => UniswapV3Gauge) public gauges;
40:    mapping(IUniswapV3Pool => address) public bribeDepots;
43:    mapping(IUniswapV3Pool => uint24) public poolsMinimumWidth;
46:    mapping(bytes32 => Incentive) public override incentives;
49:    mapping(uint256 => Deposit) public override deposits;
52:    mapping(address => mapping(IUniswapV3Pool => uint256)) private _userAttachements;
55:    mapping(uint256 => mapping(bytes32 => Stake)) private _stakes;
58:    mapping(uint256 => IncentiveKey) private stakedIncentiveKey;
81:    mapping(address => uint256) public override rewards;
84:    mapping(uint256 => uint256) public tokenIdRewards;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L34


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
34:    mapping(uint256 => uint256) public destinations;
37:    mapping(address => uint256) public destinationIds;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L34


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
154:    mapping(uint256 => address) public getBranchBridgeAgent;
157:    mapping(uint256 => bool) public isBranchBridgeAgentAllowed;
167:    mapping(uint32 => Settlement) public getSettlement;
174:    mapping(uint256 => mapping(uint32 => bool)) public executionHistory;
643:    mapping(address => bool) private approvedGasPool;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L154


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
122:    mapping(uint32 => Deposit) public getDeposit;
129:    mapping(uint32 => bool) public executionHistory;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L122


```solidity
File:2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol
32:    mapping(TalosBaseStrategy => uint256) public strategyIds;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/factories/TalosBaseStrategyFactory.sol#L32


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
35:    mapping(FlywheelCore => bool) public override isActive;
38:    mapping(FlywheelCore => bool) public override added;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L35


```solidity
File:2023-05-maia-main/src/hermes/UtilityManager.sol
32:    mapping(address => uint256) public userClaimedWeight;
34:    mapping(address => uint256) public userClaimedBoost;
36:    mapping(address => uint256) public userClaimedGovernance;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/UtilityManager.sol#L32


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
36:    mapping(BaseV2Gauge => uint256) public override gaugeIds;
39:    mapping(BaseV2Gauge => bool) public override activeGauges;
42:    mapping(address => BaseV2Gauge) public override strategyGauges;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L36


```solidity
File:2023-05-maia-main/src/maia/PartnerUtilityManager.sol
26:    mapping(address => uint256) public userClaimedPartnerGovernance;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/PartnerUtilityManager.sol#L26


```solidity
File:2023-05-maia-main/src/rewards/base/FlywheelCore.sol
30:    mapping(ERC20 => uint256) public override strategyIds;
62:    mapping(address => uint256) public override rewardsAccrued;
151:    mapping(ERC20 => uint256) public strategyIndex;
154:    mapping(ERC20 => mapping(address => uint256)) public userIndex;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/base/FlywheelCore.sol#L30


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
29:    mapping(address => uint256) public assetId;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L29


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
28:    mapping(address => mapping(address => GaugeState)) public override getUserGaugeBoost;
31:    mapping(address => uint256) public override getUserBoost;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L28


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
29:    mapping(address => Checkpoint[]) private _checkpoints;
93:    mapping(address => bool) public override canContractExceedMaxDelegates;
117:    mapping(address => mapping(address => uint256)) private _delegatesVotesCount;
120:    mapping(address => uint256) public userDelegatedVotes;
123:    mapping(address => EnumerableSet.AddressSet) private _delegates;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L29


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Gauges.sol
45:    mapping(address => mapping(address => uint112)) public override getUserGaugeWeight;
49:    mapping(address => uint112) public override getUserWeight;
395:    mapping(address => bool) public override canContractExceedMaxGauges;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Gauges.sol#L45



*Instances (94)*

### **[NC-27]** Consider disabling `renounceOwnership()`

If the plan for your project does not include eventually giving up all ownership control, consider overwriting OpenZeppelin's Ownable's renounceOwnership() function in order to disable it.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/tokens/HERMES.sol
47:contract HERMES is ERC20, Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/tokens/HERMES.sol#L47


```solidity
File:2023-05-maia-main/src/maia/tokens/Maia.sol
40:contract Maia is ERC20, Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/tokens/Maia.sol#L40


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
25:contract BranchBridgeAgentExecutor is Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L25


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol
21:contract BaseBranchRouter is IBranchRouter, Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BaseBranchRouter.sol#L21


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
38:contract CoreRootRouter is IRootRouter, Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L38


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol
26:contract RootBridgeAgentExecutor is Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L26


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
47:contract MulticallRootRouter is IRootRouter, Ownable {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L47



*Instances (7)*

### **[NC-28]** Interfaces should be defined in separate files from their usage

The interfaces below should be defined in separate files, so that it's easier for future projects to import them, and to avoid duplication later on if they need to be used elsewhere in the project
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol
169:interface TimelockInterface {
197:interface GovTokenInterface {
203:interface GovernorAlpha {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoInterfaces.sol#L169

*Instances (3)*

### **[NC-29]** decimals() is an optional method

Quoting [EIP-20](https://eips.ethereum.org/EIPS/eip-20#methods).
```OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.```
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
46:        require(ERC20(asset).decimals() == 18);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L46


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
72:        underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));
82:            _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
120:                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
141:                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L72


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
104:            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L104


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
212:            _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
254:                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
272:                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L212


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
252:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
284:            _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());
342:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357:                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
687:            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
720:            deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());
1355:            deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals());
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L252


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol
23:    constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626DepositOnly.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol
27:        if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/UlyssesERC4626.sol#L27


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626.sol
23:    constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626.sol#L23


```solidity
File:2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol
51:            require(ERC20(_assets[i]).decimals() == 18);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-4626/ERC4626MultiToken.sol#L51



*Instances (20)*

### **[NC-30]** Use replace and pop instead of the delete keyword to removing an item from an array

Instead of setting an value with zero (using the delete keyword), a better approach for removing an asset can be to replace the target item with the last element and pop the last element
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol
62:        delete _isAsset[_assets[rewardsContract]];
63:        delete _isRewardsContract[rewardsContract];
64:        delete _assets[rewardsContract];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/rewards/depots/MultiRewardsDepot.sol#L62


```solidity
File:2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol
82:        delete partners[partnerIds[partnerManager]];
83:        delete partnerIds[partnerManager];
91:        delete vaults[vaultIds[vault]];
92:        delete vaultIds[vault];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/factories/PartnerManagerFactory.sol#L82


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol
125:        delete gaugeFactories[gaugeFactoryIds[gaugeFactory]];
126:        delete gaugeFactoryIds[gaugeFactory];
127:        delete activeGaugeFactories[gaugeFactory];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeManager.sol#L125


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
149:        delete whitelistedAddresses[user];
161:        delete protocolRewards;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L149


```solidity
File:2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol
251:        delete deposits[tokenId];
457:        delete stakedIncentiveKey[tokenId];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/uni-v3-staker/UniswapV3Staker.sol#L251


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
615:        delete getSettlement[_settlementNonce];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L615


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
960:        delete getDeposit[_depositNonce];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L960


```solidity
File:2023-05-maia-main/src/gauges/BaseV2Gauge.sol
149:        delete isActive[bribeFlywheel];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/BaseV2Gauge.sol#L149


```solidity
File:2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol
132:        delete gauges[gaugeIds[gauge]];
133:        delete gaugeIds[gauge];
134:        delete activeGauges[gauge];
135:        delete strategyGauges[gauge.strategy()];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/BaseV2GaugeFactory.sol#L132


```solidity
File:2023-05-maia-main/src/erc-20/ERC20Boost.sol
140:        delete getUserGaugeBoost[user][msg.sender];
179:            delete getUserGaugeBoost[msg.sender][gauge];
192:        delete getUserGaugeBoost[msg.sender][gauge];
214:                delete getUserGaugeBoost[msg.sender][gauge];
240:            delete getUserGaugeBoost[msg.sender][gauge];
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20Boost.sol#L140



*Instances (26)*

### **[NC-31]** Multiples of 10 should use scientific notation

Using scientific notation for multiples of 10 can improve code readability.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
26:    uint256 internal constant base = 1000;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L26


```solidity
File:2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol
56:    uint256 private constant DIVISIONER = 10000;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/talos/boost-aggregator/BoostAggregator.sol#L56



*Instances (2)*

### **[NC-32]** Use Solidity units when possible

You should consider using [Solidity units](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html) whenever possible to improve readability of the code..
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol
24:    uint256 internal constant week = 86400 * 7;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/hermes/minters/BaseV2Minter.sol#L24


```solidity
File:2023-05-maia-main/src/maia/libraries/DateTimeLib.sol
40:        uint256 epochDay = timestamp / 86400;
57:            uint256 day = timestamp / 86400;
58:            startOfDay = day * 86400;
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/maia/libraries/DateTimeLib.sol#L40



*Instances (4)*

### **[NC-33]** Best practice is to prevent signature malleability

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
349:        address signatory = ecrecover(digest, v, r, s);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L349


```solidity
File:2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol
365:        address signer = ecrecover(
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/erc-20/ERC20MultiVotes.sol#L365



*Instances (2)*

### **[NC-34]** Change 52 weeks to 365 days

Advised it's changed to + 365 days instead of 52 weeks as 52 weeks is a day less than 365 days, if the intention is to wait for a year after block.timestamp.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol
65:            52 weeks,
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/gauges/factories/UniswapV3GaugeFactory.sol#L65



*Instances (1)*

### **[NC-35]** Cast to `bytes` or `bytes32` for clearer semantic meaning

Using a [cast](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739) on a single argument, rather than `abi.encodePacked()` makes the intended operation more clear, leading to less reviewer confusion.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
56:        bytes memory packedData = abi.encodePacked(bytes1(0x02), data);
101:        bytes memory packedData = abi.encodePacked(bytes1(0x04), data);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L56


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol
51:        bytes memory packedData = abi.encodePacked(bytes1(0x01), data);
75:        bytes memory packedData = abi.encodePacked(bytes1(0x02), data);
108:        bytes memory packedData = abi.encodePacked(bytes1(0x03), data);
151:        bytes memory packedData = abi.encodePacked(bytes1(0x04), data);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreBranchRouter.sol#L51


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol
112:        bytes memory packedData = abi.encodePacked(bytes1(0x02), data);
163:        bytes memory packedData = abi.encodePacked(bytes1(0x01), data);
241:        bytes memory packedData = abi.encodePacked(bytes1(0x03), data);
262:        bytes memory packedData = abi.encodePacked(bytes1(0x04), data);
285:        bytes memory packedData = abi.encodePacked(bytes1(0x05), data);
312:        bytes memory packedData = abi.encodePacked(bytes1(0x06), data);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/CoreRootRouter.sol#L112


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
278:            abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));
558:        bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L278


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
435:        bytes memory packedData = abi.encodePacked(bytes1(0x08), _depositNonce, msg.value.toUint128(), uint128(0));
481:            abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas);
657:            abi.encodePacked(bytes1(0x01), depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas);
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L435



*Instances (17)*

### **[NC-36]** Consider bounding input array length

The functions below take in an unbounded array, and make function calls for entries in the array. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol
48:        for (uint256 i = 0; i < calls.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/VirtualAccount.sol#L48


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol
95:        for (uint256 i = 0; i < assets.length; i++) {
111:        for (uint256 i = 0; i < assets.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesToken.sol#L95


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol
101:        for (uint256 i = 0; i < _localAddresses.length;) {
136:        for (uint256 i = 0; i < _localAddresses.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L101


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol
231:        for (uint256 i = 0; i < _localAddresses.length;) {
267:        for (uint256 i = 0; i < _localAddresses.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchPort.sol#L231


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol
146:        for (uint256 i = 0; i < outputTokens.length;) {
311:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
387:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
463:            for (uint256 i = 0; i < outputParams.outputTokens.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/MulticallRootRouter.sol#L146


```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
181:        for (uint256 i = 0; i < proposal.targets.length; i++) {
215:        for (uint256 i = 0; i < proposal.targets.length; i++) {
251:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L181


```solidity
File:2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol
130:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
232:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
253:            for (uint256 i = 1; i < bandwidthStateList.length;) {
278:            for (uint256 i = 1; i < bandwidthStateList.length;) {
296:        for (uint256 i = 1; i < bandwidthStateList.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-amm/UlyssesPool.sol#L130


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol
342:        for (uint256 i = 0; i < _globalAddresses.length;) {
408:        for (uint256 i = 0; i < _dParams.hTokens.length;) {
561:        for (uint256 i = 0; i < newGas.length;) {
596:        for (uint256 i = 0; i < settlement.hTokens.length;) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/RootBridgeAgent.sol#L342


```solidity
File:2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol
283:        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
719:        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
951:        for (uint256 i = 0; i < deposit.hTokens.length;) {
1354:        for (uint256 i = 0; i < _deposits.length; i++) {
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/ulysses-omnichain/BranchBridgeAgent.sol#L283



*Instances (27)*


### **[NC-37]** `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). Unless there is a compelling reason, `abi.encode` should be preferred. If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead
#### <ins>Proof Of Concept</ins>

```solidity
File:2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol
348:        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

#### <ins>Code Snippet</ins>
https://github.com/code-423n4/2023-05-maia-main/src/governance/GovernorBravoDelegateMaia.sol#L348



*Instances (1)*
