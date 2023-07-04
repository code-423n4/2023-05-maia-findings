# Gas Optimization

# Summary

| Number | Optimization Details                                                              | Context |
| :----: | :-------------------------------------------------------------------------------- | :-----: |
| [G-01] | Avoid contract existence checks by using low level calls                          |   40    |
| [G-02] |  Add `unchecked{}` for substractions where the operands can't underflow because of previous require or if-statement                                                                                 |   28    |
| [G-03] |  Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant                                                                                |    3    |
| [G-04] | Use assembly to hash instead of solidity                                          |    5    |
| [G-05] |  State variables should be cached in stack variables rather than `re-reading` them from storage                                                                                 |   87    |
| [G-06] | When possible, use assembly instead of `unchecked{++i}`                           |   38    |
| [G-07] | can make the variable `outside` the loop to save gas                              |   24    |
| [G-08] | Before some `functions`, we should check some variables for possible gas save     |   10    |
| [G-09] | A `modifier` used only once and not being inherited should be inlined to save gas |    7    |
| [G-10] | `struct Order:` can be more tighly packed                                         |    6    |
| [G-11] | Use constants instead of `type(uintx).max`                                        |   30    |
| [G-12] | Use hardcode address instead `address(this)`                                      |   84    |
| [G-13] | `abi.encode()` is less efficient than abi.encodePacked()                          |    9    |
| [G-14] | Using `> 0` costs more gas than `!= 0`                                            |   44    |
| [G-15] | Do not calculate `constant`                                                       |    7    |
| [G-16] |  instead of calculating a state var with `keccak256()` every time the contract is made pre calculate them before and only give the result to a contract                                                                                |    3    |
| [G-17] | Ternary operation is cheaper than `if-else` statement                             |    1    |
| [G-18] | Use assembly to `hash` instead of solidity                                        |    6    |


## [G-01] Avoid contract existence checks by using low level calls

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

total gas save : `40 *100 = 4000`

```solidity
File: ulysses-omnichain/ArbitrumCoreBranchRouter.sol

//@audit   performCallOut()
59        IBridgeAgent(localBridgeAgentAddress).performCallOut(msg.sender, packedData, 0, 0);


//@audit    createBridgeAgent()
88        address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(


//@audit    isBridgeAgent()
93        if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {


//@audit        performSystemCallOut()
104        IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, 0, 0);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

```solidity
File: ulysses-amm/UlyssesToken.sol

//@audit    decimals()
46        require(ERC20(asset).decimals() == 18);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L46

```solidity
File: ulysses-omnichain/BranchBridgeAgentExecutor.sol

//@audit        anyExecuteNoSettlement()
76        (success, result) = IRouter(_router).anyExecuteNoSettlement(_data[25:_data.length - PARAMS_GAS_OUT]);


//@audit  anyExecuteSettlement()
110            (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);


//@audit  anyExecuteSettlementMultiple()
147            (success, result) = IRouter(_router).anyExecuteSettlementMultiple(

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

```solidity
File: ulysses-omnichain/BaseBranchRouter.sol

//@audit   bridgeAgentExecutorAddress()
40        bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress();


//@audit   performCallOut()
59        IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(


//@audit   performCallOutAndBridge()
70        IBridgeAgent(localBridgeAgentAddress).performCallOutAndBridge{value: msg.value}(


//@audit   performCallOutAndBridgeMultiple()
81        IBridgeAgent(localBridgeAgentAddress).performCallOutAndBridgeMultiple{value: msg.value}(


//@audit   retrySettlement()
88        IBridgeAgent(localBridgeAgentAddress).retrySettlement{value: msg.value}(_settlementNonce, _gasToBoostSettlement);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol

```solidity
File: ulysses-omnichain/CoreBranchRouter.sol

//@audit       performCallOut()
54        IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(


//@audit       createToken()
69        ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(name, symbol);


//@audit       createToken()
102        ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(_name, _symbol);


//@audit       performSystemCallOut()
111        IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _rootExecutionGas, 0);


//@audit       createBridgeAgent()
138        address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

```solidity
File: ulysses-omnichain/CoreRootRouter.sol


//@audit     callOut()
265        IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);


//@audit     callOut()
288        IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);


//@audit     callOut()
315        IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol

```solidity
File: ulysses-omnichain/MulticallRootRouter.sol

//@audit        callOutAndBridge()
123        IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(

//@audit        callOutAndBridgeMultiple()
155        IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L123

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

//@audit      slot0()
674        (uint160 sqrtPriceX96,,,,,,) = IUniswapV3Pool(poolAddress).slot0();

//@audit      slot0()
684        try IUniswapV3Pool(poolAddress).swap(

//@audit      slot0()
717            (uint160 sqrtPriceX96,,,,,,) = IUniswapV3Pool(poolAddress).slot0();


//@audit      swap()
728        (int256 amount0, int256 amount1) = IUniswapV3Pool(poolAddress).swap(

//@audit     anyCall()
782            IAnycallProxy(localAnyCallAddress).anyCall(


//@audit     context()
856        (from, fromChainId,) = IAnycallExecutor(localAnyCallExecutorAddress).context();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

//@audit    anyCall()
1008        IAnycallProxy(localAnyCallAddress).anyCall(


//@audit     deposit()
1093        IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));


//@audit    context()
1110        (from, fromChainId,) = IAnycallExecutor(localAnyCallExecutorAddress).context();


//@audit    config()
1324        IAnycallConfig anycallConfig = IAnycallConfig(IAnycallProxy(localAnyCallAddress).config());

//@audit    context()
1388        (address from,,) = IAnycallExecutor(localAnyCallExecutorAddress).context();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

```solidity
File: gauges/UniswapV3Gauge.sol

//@audit  createIncentiveFromGauge()
54        IUniswapV3Staker(uniswapV3Staker).createIncentiveFromGauge(amount);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L54

```solidity
File: ulysses-omnichain/factories/RootBridgeAgentFactory.sol

//@audit   executor()
62        localAnyCallExecutorAddress = IAnycallProxy(localAnyCallAddress).executor();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L62

```solidity
File: gauges/factories/UniswapV3GaugeFactory.sol

//@audit   setMinimumWidth()
100        UniswapV3Gauge(gauge).setMinimumWidth(minimumWidth);

//@audit       strategy()
101        uniswapV3Staker.updateGauges(IUniswapV3Pool(UniswapV3Gauge(gauge).strategy()));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L100

```solidity
File: talos/TalosManager.sol

//@audit    slot0()
68        (, int24 currentTick,,,,,) = position.pool().slot0();

//@audit    slot0()
80        (, int24 currentTick,,,,,) = position.pool().slot0();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L68

## [G-02] Add `unchecked{}` for substractions where the operands can't underflow because of previous require or if-statement

This will stop the check for overflow and underflow so it will save gas.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L115-L119

```solidity
File: ulysses-amm/UlyssesToken.sol

115       if (assetBalance > newAssetBalance) {
116                assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);
117            } else {
118                assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);
119            }
```

```diff
diff --git a/UlyssesToken.org.sol b/UlyssesToken.sol
index 047c23f..0bebf7e 100644
--- a/UlyssesToken.org.sol
+++ b/UlyssesToken.sol
@@ -2,8 +2,12 @@
             uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);

             if (assetBalance > newAssetBalance) {
+                unchecked{
                 assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);
+                 }
             } else {
+                unchecked{
                 assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);
+                }
             }
         }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L110

```solidity
File: ulysses-omnichain/BranchBridgeAgentExecutor.sol

110            (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);



147            (success, result) = IRouter(_router).anyExecuteSettlementMultiple(
148                _data[
149                    PARAMS_END_SIGNED_OFFSET + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * PARAMS_TKN_SET_SIZE):
150                        _data.length - PARAMS_GAS_OUT
150                ],
```

```diff
diff --git a/BranchBridgeAgentExecutor.org.sol b/BranchBridgeAgentExecutor.sol
index 6da1c96..2caf94b 100644
--- a/BranchBridgeAgentExecutor.org.sol
+++ b/BranchBridgeAgentExecutor.sol
@@ -1,6 +1,8 @@
         if (_data.length - PARAMS_GAS_OUT > 129) {
             //Execute remote request
+            unchecked{
             (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);
+                }
         } else {
             success = true;
         }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L139-L141

```solidity
File: hermes/minters/BaseV2Minter.sol

140                HERMES(underlying).mint(address(this), _required - _balanceOf);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L124

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

124            IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(_depositor, _localAddress, _amount - _deposit);


146                    _depositor, _localAddresses[i], _amounts[i] - _deposits[i]

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L276

```solidity
File: ulysses-omnichain/BranchPort.sol

249            _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);


250            ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);


276                _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);


277                ERC20hTokenBranch(_localAddresses[i]).burn(_amounts[i] - _deposits[i]);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L138

```solidity
File: ulysses-omnichain/RootBridgeAgentExecutor.sol

138                _data[112], _data[113:_data.length - PARAMS_GAS_IN], dParams, _fromChainId


181                        length - PARAMS_GAS_IN


242                _data[132], _data[133:_data.length - PARAMS_GAS_IN], dParams, _account, _fromChainId


289                            _data.length - PARAMS_GAS_IN

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L533

```solidity
File: governance/GovernorBravoDelegateMaia.sol

533        return a - b;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L139

```solidity
File: ulysses-amm/UlyssesPool.sol

139            return balance - assets;


194                newBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;

218            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

260                        leftOverBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;


275                leftOverBandwidth += oldBandwidth - poolState.bandwidth;

303            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L454

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

454            _globalAddress.safeTransferFrom(_sender, localPortAddress, _amount - _deposit);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

```solidity
File: talos/base/TalosBaseStrategy.sol

167            uint256 refund0 = amount0Desired - amount0;


172            uint256 refund1 = amount1Desired - amount1;


227            uint256 refund0 = amount0Desired - amount0;


232            uint256 refund1 = amount1Desired - amount1;


412        protocolFees0 = _protocolFees0 - amount0;

413        protocolFees1 = _protocolFees1 - amount1;

```

## [G-03] Expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to bytes4 should also only be done once.

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L362

```solidity
File: erc-20/ERC20MultiVotes.sol

360    bytes32 public constant DELEGATION_TYPEHASH =
361        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

```solidity
File: governance/GovernorBravoDelegateMaia.sol

42    bytes32 public constant DOMAIN_TYPEHASH =
43        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");


46    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

## [G-04] Use `assembly` to hash instead of solidity

for example:

```solidity
function solidityHash(uint256 a, uint256 b) public view {
    //unoptimized
    keccak256(abi.encodePacked(a, b));
}
Gas: 313



function assemblyHash(uint256 a, uint256 b) public view {
    //optimized
    assembly {
        mstore(0x00, a)
        mstore(0x20, b)
        let hashedVal := keccak256(0x00, 0x40)
    }
}
Gas: 231

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L365-L374

```solidity
File: erc-20/ERC20MultiVotes.sol

365        address signer = ecrecover(
366            keccak256(
367                abi.encodePacked(
368                    "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
369                )
370            ),
371            v,
372            r,
373            s
374        );
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol#L17

```solidity
File: uni-v3-staker/libraries/IncentiveId.sol

17        return keccak256(abi.encode(key));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
File: governance/GovernorBravoDelegateMaia.sol

346           keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

347        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));


348        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

## [G-05] State variables should be cached in stack variables rather than `re-reading` them from storage

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol

```solidity
File: gauges/BaseV2Gauge.sol

//@audit  WEEK
68        epoch = (block.timestamp / WEEK) * WEEK;

84        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;


86         if (epoch < _newEpoch) {

146        if (!isActive[bribeFlywheel]) revert FlywheelNotActive();

```

```diff
diff --git a/BaseV2Gauge.org.sol b/BaseV2Gauge.sol
index 0cd357b..a994bc3 100644
--- a/BaseV2Gauge.org.sol
+++ b/BaseV2Gauge.sol
@@ -1,7 +1,9 @@
     function newEpoch() external {
-        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
+        uint256 _week = WEEK;
+        uint256 _epoch = epoch;
+        uint256 _newEpoch = (block.timestamp / _week) * _week;

-        if (epoch < _newEpoch) {
+        if (_epoch < _newEpoch) {
             epoch = _newEpoch;

             uint256 accruedRewards = flywheelGaugeRewards.getAccruedRewards();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L41

```solidity
File: rewards/depots/SingleRewardsDepot.sol

41        if (msg.sender != address(rewardsContract)) revert FlywheelRewardsError();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L60

```solidity
File: rewards/depots/MultiRewardsDepot.sol

//@audit  _isRewardsContract    _isAsset
48        if (_isAsset[asset] || _isRewardsContract[rewardsContract]) revert ErrorAddingAsset();


//@audit  _isRewardsContract
58        if (!_isRewardsContract[rewardsContract]) revert ErrorRemovingAsset();


//@audit  _assets
60        emit AssetRemoved(rewardsContract, _assets[rewardsContract]);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L67

```solidity
File: ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

67        hTokensLenght++;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L45

```solidity
File: ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

45        hTokensLenght++;

67        hTokensLenght++;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50

```solidity
File:

//@audit  cache state variable to local cache   rewardsCycleLength
50            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol

```solidity
File: maia/PartnerUtilityManager.sol


//@audit   partnerVault
75        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
76            IBaseVault(partnerVault).applyWeight();
77        }


85        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
86            IBaseVault(partnerVault).applyBoost();
87        }


95        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
96            IBaseVault(partnerVault).applyGovernance();
97        }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L217

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

//@audit   bHermesRate
217        if (newRate < bHermesRate) revert InvalidRate();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

```solidity
File: src/talos/base/TalosBaseStrategy.sol

//@audit  initialized
108        if (initialized) revert AlreadyInitialized();


//@audit  tickLower,tokenId,tickUpper
305        emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);


//@audit  tickLower,tokenId,tickUpper
318        emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L77

```solidity
File: src/erc-20/ERC20Gauges.sol

//@audit   gaugeCycleLength
77            return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L20

```solidity
File: src/uni-v3-staker/libraries/IncentiveTime.sol

//@audit     INCENTIVES_DURATION,  INCENTIVES_OFFSET
20        return uint96(((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) * INCENTIVES_DURATION + INCENTIVES_OFFSET);


//@audit     INCENTIVES_DURATION,  INCENTIVES_OFFSET
27            (((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) + 1) * INCENTIVES_DURATION + INCENTIVES_OFFSET

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L133-L136

```solidity
File: ulysses-omnichain/BranchBridgeAgentExecutor.sol


///@audit   PARAMS_START_SIGNED,   PARAMS_TKN_START
133               PARAMS_START_SIGNED:
134                    PARAMS_START_SIGNED + PARAMS_TKN_START
135                        + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))
136            ],


///@audit   PARAMS_START_SIGNED,   PARAMS_TKN_START
142            _data.length - PARAMS_GAS_OUT
143                > PARAMS_START_SIGNED + PARAMS_TKN_START
144                    + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

```solidity
File: src/hermes/minters/BaseV2Minter.sol

//@audit   max_dao_share
93        if (_daoShare > max_dao_share) revert DaoShareTooHigh();


//@audit   week
82        activePeriod = (block.timestamp / week) * week;

126        if (block.timestamp >= _period + week && initializer == address(0)) {
127            _period = (block.timestamp / week) * week;


//@audit   dao
145            if (dao != address(0)) underlying.safeTransfer(dao, share);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L118-L119

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

///@audit DIVISIONER
118        if (pendingRewards > DIVISIONER) {
119            uint256 newProtocolRewards = (pendingRewards * protocolFee) / DIVISIONER;

154        if (_protocolFee > DIVISIONER) revert FeeTooHigh();

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

//@audit gaugeCycleLength
60        gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

79        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

114        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L186-L190

```solidity
File: ulysses-omnichain/CoreRootRouter.sol

//@audit  rootPortAddress
90        if (msg.sender != IPort(rootPortAddress).getBridgeAgentManager(_rootBridgeAgent)) {

95        if (!IPort(rootPortAddress).isChainId(_toChain)) revert InvalidChainId();



//@audit  rootPortAddress
186     if (
187            IPort(rootPortAddress).isGlobalAddress(_underlyingAddress)
188                || IPort(rootPortAddress).isLocalToken(_underlyingAddress, _fromChain)
189                || IPort(rootPortAddress).isUnderlyingToken(_underlyingAddress, _fromChain)
190        ) revert TokenAlreadyAdded();

//@audit  rootPortAddress
210        if (IPort(rootPortAddress).isLocalToken(_localAddress, _toChain)) revert TokenAlreadyAdded();

213        IPort(rootPortAddress).setLocalAddress(_globalAddress, _localAddress, _toChain);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol

```solidity
File: ulysses-omnichain/RootPort.sol

//@audit localChainId
494            getUnderlyingTokenFromLocal[_ecoTokenGlobalAddress][localChainId] != address(0)
495                || getLocalTokenFromUnder[_ecoTokenGlobalAddress][localChainId] != address(0)


499       getGlobalTokenFromLocal[_ecoTokenGlobalAddress][localChainId] = _ecoTokenGlobalAddress;
500        getLocalTokenFromGlobal[_ecoTokenGlobalAddress][localChainId] = _ecoTokenGlobalAddress;


//@audit wrappedNativeTokenAddress
463        if (newGlobalToken < wrappedNativeTokenAddress) {

466                .createAndInitializePoolIfNecessary(newGlobalToken, wrappedNativeTokenAddress, _fee, _sqrtPriceX96);

470                .createAndInitializePoolIfNecessary(wrappedNativeTokenAddress, newGlobalToken, _fee, _sqrtPriceX96);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

//@audit PARAMS_START_SIGNED
1274            _depositNonce = uint32(bytes4(data[PARAMS_START_SIGNED:PARAMS_START_SIGNED + PARAMS_TKN_START]));


1290                bytes4(data[PARAMS_START_SIGNED + PARAMS_START:PARAMS_START_SIGNED + PARAMS_TKN_START + PARAMS_START])


//@audit   PARAMS_ENTRY_SIZE
587                                PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * i) + 12:
588                                    PARAMS_TKN_START + (PARAMS_ENTRY_SIZE * (PARAMS_START + i))

//@audit   PARAMS_ENTRY_SIZE
598                            PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:
599                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)
```

## [G-06] When possible, use assembly instead of `unchecked{++i}`

You can also use unchecked{++i;} for even more gas savings but this will not check to see if i overflows. For best gas savings, use inline assembly, however, this limits the functionality you can achieve.

For Example:

```solidity
//loop with unchecked{++i}
function uncheckedPlusPlusI() public pure {
    uint256 j = 0;
    for (uint256 i; i < 10; ) {
        j++;
        unchecked {
            ++i;
        }
    }
}
Gas: 1329



//loop with inline assembly
function inlineAssemblyLoop() public pure {
    assembly {
        let j := 0
        for {
            let i := 0
        } lt(i, 10) {
            i := add(i, 0x01)
        } {
            j := add(j, 0x01)
        }
    }
}
Gas: 709

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L117-L119

```solidity
File: gauges/BaseV2Gauge.sol

117            unchecked {
118                i++;
119            }
```

```diff
diff --git a/BaseV2Gauge.org.sol b/BaseV2Gauge.sol
index 306e1f2..79bd4b3 100644
--- a/BaseV2Gauge.org.sol
+++ b/BaseV2Gauge.sol
@@ -4,7 +4,13 @@
         for (uint256 i = 0; i < length;) {
             if (isActive[_bribeFlywheels[i]]) _bribeFlywheels[i].accrue(ERC20(address(this)), user);

-            unchecked {
-                i++;
-            }
-        }

+        assembly {
+           unchecked {
+                i := add(i, 1)
+             }
+        }


```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L80

```solidity
File: gauges/factories/BaseV2GaugeFactory.sol

80            unchecked {
81                i++;
82            }


96           unchecked {
97                i++;
98            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L53

```solidity
File: erc-20/ERC20Boost.sol

53            unchecked {
54                values[i] = _gauges.at(offset + i); // will revert if out of bounds
55                i++;
56            }


99            unchecked {
100                values[i] = _userGauges[user].at(offset + i); // will revert if out of bounds
101                i++;
102            }

165            unchecked {
167                i++;
168            }

223            unchecked {
224                i++;
225            }

244            unchecked {
245                i++;
246            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L115

```solidity
File: erc-20/ERC20Gauges.sol

115            unchecked {
116                values[i] = _gauges.at(offset + i); // will revert if out of bounds
117                i++;
118            }


156            unchecked {
157                values[i] = _userGauges[user].at(offset + i); // will revert if out of bounds
158                i++;
159            }


265            unchecked {
266                i++;
267            }

345            unchecked {
346                i++;
347            }


547                unchecked {
548                    i++;
549                }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L81-L83

```solidity
File: ulysses-amm/UlyssesRouter.sol

81            unchecked {
82                ++i;
83            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol

```solidity
File: gauges/factories/BaseV2GaugeManager.sol

67            unchecked {
68                i++;
69            }

83            unchecked {
84                i++;
85            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

```solidity
File: ulysses-amm/factories/UlyssesFactory.sol

102           unchecked {
103                ++i;
104            }


113                unchecked {
114                    ++j;
115                }


118          unchecked {
119                ++i;
120            }


126           unchecked {
127                ++i;
128            }



152            unchecked {
153                ++i;
154            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

104            unchecked {
105                ++i;
106           }



150            unchecked {
151                ++i;
152            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol

```solidity
File: src/ulysses-omnichain/BranchPort.sol

234            unchecked {
235                ++i;
236            }


279            unchecked {
280                i++;
281            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol

```solidity
File:  ulysses-omnichain/MulticallRootRouter.sol

149            unchecked {
150                ++i;
151            }



314               unchecked {
315                    ++i;
316                }


390                unchecked {
391                    ++i;
392                }


466                unchecked {
467                    ++i;
468                }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol

```solidity
File: src/ulysses-amm/UlyssesPool.sol

197           unchecked {
                ++i;

            }

264               unchecked {
                    ++i;
                }


288                unchecked {
                    ++i;
                }


930            unchecked {
                 ++i;
             }


1001            unchecked {
                 ++i;
            }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

353            unchecked {
                ++i;
            }



422            unchecked {
                ++i;
            }


563           unchecked {
                ++i;
            }


609            unchecked {
                ++i;
            }
```

```solidity
File:
```

## [G-07] can make the variable `outside` the loop to save gas

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol

```solidity
File: erc-20/ERC20Boost.sol

157            address gauge = gaugeList[i];

208            address gauge = gaugeList[offset + i];

237            address gauge = gaugeList[i];

```

```diff
diff --git a/ERC20Boost.org.sol b/ERC20Boost.sol
index ac82d24..e140e0c 100644
--- a/ERC20Boost.org.sol
+++ b/ERC20Boost.sol
@@ -1,6 +1,7 @@
         uint256 length = gaugeList.length;
+        address gauge;
         for (uint256 i = 0; i < length;) {
-            address gauge = gaugeList[i];
+            gauge = gaugeList[i];

             if (!_deprecatedGauges.contains(gauge)) {
                 uint256 gaugeBoost = getUserGaugeBoost[user][gauge].userGaugeBoost;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol

```solidity
File: erc-20/ERC20MultiVotes.sol

329            address delegatee = delegateList[i];

330            uint256 delegateVotes = _delegatesVotesCount[user][delegatee];

332            uint256 votesToFree = FixedPointMathLib.min(delegateVotes, userUnusedVotes(delegatee));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol

```solidity
File: erc-20/ERC20Gauges.sol

260            address gauge = gaugeList[i];

261            uint112 weight = weights[i];

339            address gauge = gaugeList[i];

340            uint112 weight = weights[i];

537            address gauge = gaugeList[i];

538            uint112 userGaugeWeight = getUserGaugeWeight[user][gauge];
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

```solidity
File: ulysses-amm/UlyssesToken.sol

112            uint256 assetBalance = assets[i].balanceOf(address(this));

113            uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol

```solidity
File: ulysses-amm/UlyssesPool.sol

131            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

176            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

190            uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

212            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);


233            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

255                    uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

297            uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

908            uint256 updateAmount = bandwidthUpdateAmounts[i];

963            uint256 updateAmount = bandwidthUpdateAmounts[i];

1045            uint256 updateAmount = bandwidthUpdateAmounts[i];

```

## [G-08] Before some `functions`, we should check some variables for possible gas save

Before transfer, we should check for amount being 0 so the function doesn't run when its not gonna do anything:

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L73-L75

```solidity
File: ulysses-amm/UlyssesRouter.sol

74        address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount);

```

```diff
diff --git a/UlyssesRouter.org.sol b/UlyssesRouter.sol
index 0b2cb56..971100f 100644
--- a/UlyssesRouter.org.sol
+++ b/UlyssesRouter.sol
@@ -1,4 +1,5 @@
     function swap(uint256 amount, uint256 minOutput, Route[] calldata routes) external returns (uint256) {
+        require(amount != 0, "Amount must be greater than zero");
         address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount);

         uint256 length = routes.length;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L104

```solidity
File: maia/PartnerUtilityManager.sol

104        address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);

```

```diff
diff --git a/PartnerUtilityManager.org.sol b/PartnerUtilityManager.sol
index b61b0c5..fc29087 100644
--- a/PartnerUtilityManager.org.sol
+++ b/PartnerUtilityManager.sol
@@ -1,4 +1,5 @@
     function forfeitPartnerGovernance(uint256 amount) public {
+        require(amount != 0, "Amount must be greater than zero");
         userClaimedPartnerGovernance[msg.sender] -= amount;
         /// @dev partnerGovernance is kept in this contract and not sent to vaults to avoid governance attacks.
         address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

```solidity
File: talos/base/TalosBaseStrategy.sol

//@audit    amount0Desired
196        address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);

//@audit    amount0Desired
197        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L176

```solidity
File: talos/boost-aggregator/BoostAggregator.sol

//@audit  amount
176        address(hermesGaugeBoost).safeTransfer(to, amount);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L52

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

//@audit _deposit
52        _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L311

```solidity
File: ulysses-omnichain/RootPort.sol

///@audit   _amount
311        _hToken.safeTransferFrom(_from, address(this), _amount);


320        _hToken.safeTransfer(_to, _amount);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1109

```solidity
File: ulysses-amm/UlyssesPool.sol

//@audit  assets
1109        asset.safeTransferFrom(msg.sender, address(this), assets);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L930

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

//@audit    _gasToBridgeOut
930        address(wrappedNativeToken).safeTransfer(localPortAddress, _gasToBridgeOut);
```

## [G-09] A `modifier` used only once and not being inherited should be inlined to save gas

When a modifier is used only once and is not inherited by any other contracts, inlining it can reduce gas costs. Inlining means that the modifier's code is directly inserted into the function where it is applied, rather than creating a separate function call for the modifier.
By inlining the modifier, you avoid the overhead of the additional function call, which results in lower gas consumption. This optimization is especially useful when the modifier's code is short or simple.

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L190-L193

```solidity
file:    src/talos/boost-aggregator/BoostAggregator.sol

190      modifier onlyWhitelisted(address from) {
191        if (!whitelistedAddresses[from]) revert Unauthorized();
192        _;
193    }
```

```diff
diff --git a/BoostAggregator.org.sol b/BoostAggregator.sol
index af2049d..5f137da 100644
--- a/BoostAggregator.org.sol
+++ b/BoostAggregator.sol
@@ -4,6 +4,8 @@
         onlyWhitelisted(from)
         returns (bytes4)
     {

+        if (!whitelistedAddresses[from]) revert Unauthorized();

        // update tokenIdRewards prior to staking
        tokenIdRewards[tokenId] = uniswapV3Staker.tokenIdRewards(tokenId);
        // map tokenId to user
        tokenIdToUser[tokenId] = from;
        // stake NFT to Uniswap V3 Staker
        nonfungiblePositionManager.safeTransferFrom(address(this), address(uniswapV3Staker), tokenId);

        return this.onERC721Received.selector;
    }



-       modifier onlyWhitelisted(address from) {
-        if (!whitelistedAddresses[from]) revert Unauthorized();
-        _;
     }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L423-L429

```solidity
file:    src/ulysses-omnichain/BranchPort.sol

423      modifier lock() {
424             require(_unlocked == 1);
425        _unlocked = 2;
426        _;
427        _unlocked = 1;
428    }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L105-L110

```solidity
file:      src/gauges/factories/BribesFactory.sol

105         modifier onlyGaugeFactory() {
106             if (!gaugeManager.activeGaugeFactories(BaseV2GaugeFactory(msg.sender))) {
107                revert Unauthorized();
108          }
109          _;
110     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32-L35

```solidity
file:   src/hermes/tokens/bHermesBoost.sol

32      modifier onlybHermes() {
33         if (msg.sender != bHermes) revert NotbHermes();
34          _;
35     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39-L42

```solidity
file:    src/hermes/tokens/bHermesGauges.sol

39       modifier onlybHermes() {
40          if (msg.sender != bHermes) revert NotbHermes();
41          _;
42     }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L75-L78

```solidity
file: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

75      modifier requiresCoreRouter() {
76         if (msg.sender != localCoreRouterAddress) revert UnrecognizedCoreRouter();
77          _;
78     }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L74-L79

```solidity
file:   src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

74      modifier requiresCoreRouter() {
75          if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress) {
76             revert UnrecognizedCoreRouter();
77         }
78           _;
79     }
```

## [G-10] `struct Order:` can be more tighly packed

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

total save gas = `6 * 20000 = 60000`

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

```solidity
File: ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

55  struct DepositMultipleParams {
56        //Deposit Info
57        uint8 numberOfAssets; //Number of assets to deposit.
58        uint32 depositNonce; //Deposit nonce.
59        address[] hTokens; //Input Local hTokens Address.
60        address[] tokens; //Input Native / underlying Token Address.
61        uint256[] amounts; //Amount of Local hTokens deposited for interaction.
62        uint256[] deposits; //Amount of native tokens deposited for interaction.
63       uint24 toChain; //Destination chain for interaction.
64        uint128 depositedGas; //BRanch chain gas token amount sent with request.
65    }

```

The total number of storage slots used is 6. the new layout saves one storage slot. Thus, the reordered structure layout reduces the number of storage slots used to 5.

save `1` storage slot

```diff

  struct DepositMultipleParams {
        //Deposit Info
         uint8 numberOfAssets; //Number of assets to deposit.
         uint32 depositNonce; //Deposit nonce.
+        uint24 toChain; //Destination chain for interaction.
+        uint128 depositedGas; //BRanch chain gas token amount sent with request.
         address[] hTokens; //Input Local hTokens Address.
         address[] tokens; //Input Native / underlying Token Address.
         uint256[] amounts; //Amount of Local hTokens deposited for interaction.
         uint256[] deposits; //Amount of native tokens deposited for interaction.
-        uint24 toChain; //Destination chain for interaction.
-        uint128 depositedGas; //BRanch chain gas token amount sent with request.
    }

```

```solidity
File: ulysses-omnichain/interfaces/IBranchBridgeAgent.sol


76    struct SettlementMultipleParams {
77        uint8 numberOfAssets; //Number of assets to deposit.
78        address recipient;
79        uint32 settlementNonce;
80        address[] hTokens;
81        address[] tokens;
82        uint256[] amounts;
83        uint256[] deposits;
84    }
```

When an array of address type is stored in Solidity, each element of the array (i.e., each address) occupies a full 32-byte storage slot.
so we save one storage slot

save `1` storage slot

```diff

    struct SettlementMultipleParams {
         uint8 numberOfAssets; //Number of assets to deposit.
+        uint32 settlementNonce;
         address recipient;
-        uint32 settlementNonce;
         address[] hTokens;
         address[] tokens;
         uint256[] amounts;
         uint256[] deposits;
    }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol

```solidity
File: ulysses-omnichain/interfaces/IRootBridgeAgent.sol

63    struct DepositParams {
64        //Deposit Info
65        uint32 depositNonce; //Deposit nonce.
66        address hToken; //Input Local hTokens Address.
67        address token; //Input Native / underlying Token Address.
68        uint256 amount; //Amount of Local hTokens deposited for interaction.
69        uint256 deposit; //Amount of native tokens deposited for interaction.
70        uint24 toChain; //Destination chain for interaction.
71    }
```

save `1` storage slot

```diff

    struct DepositParams {
         //Deposit Info
+        uint24 toChain; //Destination chain for interaction.
         uint32 depositNonce; //Deposit nonce.
         address hToken; //Input Local hTokens Address.
         address token; //Input Native / underlying Token Address.
         uint256 amount; //Amount of Local hTokens deposited for interaction.
         uint256 deposit; //Amount of native tokens deposited for interaction.
-        uint24 toChain; //Destination chain for interaction.
    }
```

```solidity
File: ulysses-omnichain/interfaces/IRootBridgeAgent.sol

73    struct DepositMultipleParams {
74        //Deposit Info
75        uint8 numberOfAssets; //Number of assets to deposit.
76        uint32 depositNonce; //Deposit nonce.
77        address[] hTokens; //Input Local hTokens Address.
78        address[] tokens; //Input Native / underlying Token Address.
79        uint256[] amounts; //Amount of Local hTokens deposited for interaction.
80        uint256[] deposits; //Amount of native tokens deposited for interaction.
81        uint24 toChain; //Destination chain for interaction.
82    }
```

save `1` storage slot

```diff

    struct DepositMultipleParams {
       //Deposit Info
         uint8 numberOfAssets; //Number of assets to deposit.
+        uint24 toChain; //Destination chain for interaction.
         uint32 depositNonce; //Deposit nonce.
         address[] hTokens; //Input Local hTokens Address.
         address[] tokens; //Input Native / underlying Token Address.
         uint256[] amounts; //Amount of Local hTokens deposited for interaction.
         uint256[] deposits; //Amount of native tokens deposited for interaction.
-        uint24 toChain; //Destination chain for interaction.
   }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136

```solidity
File: governance/GovernorBravoInterfaces.sol

   struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
        uint256 eta;
        /// @notice the ordered list of target addresses for calls to be made
        address[] targets;
        /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
        uint256[] values;
        /// @notice The ordered list of function signatures to be called
        string[] signatures;
        /// @notice The ordered list of calldata to be passed to each call
        bytes[] calldatas;
        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
        uint256 startBlock;
        /// @notice The block at which voting ends: votes must be cast prior to this block
        uint256 endBlock;
        /// @notice Current number of votes in favor of this proposal
        uint256 forVotes;
        /// @notice Current number of votes in opposition to this proposal
        uint256 againstVotes;
        /// @notice Current number of votes for abstaining for this proposal
        uint256 abstainVotes;
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
    }
```

save `2` storage slots

```diff
   struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
+       /// @notice Flag marking whether the proposal has been canceled
+       bool canceled;
+       /// @notice Flag marking whether the proposal has been executed
+       bool executed;
        /// @notice Creator of the proposal
        address proposer;
        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
        uint256 eta;
        /// @notice the ordered list of target addresses for calls to be made
        address[] targets;
        /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
        uint256[] values;
        /// @notice The ordered list of function signatures to be called
        string[] signatures;
        /// @notice The ordered list of calldata to be passed to each call
        bytes[] calldatas;
        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
        uint256 startBlock;
        /// @notice The block at which voting ends: votes must be cast prior to this block
        uint256 endBlock;
        /// @notice Current number of votes in favor of this proposal
        uint256 forVotes;
        /// @notice Current number of votes in opposition to this proposal
        uint256 againstVotes;
        /// @notice Current number of votes for abstaining for this proposal
        uint256 abstainVotes;
-        /// @notice Flag marking whether the proposal has been canceled
-        bool canceled;
-      /// @notice Flag marking whether the proposal has been executed
-        bool executed;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
    }
```

## [G-11] Use constants instead of `type(uintx).max`

type(uint128).max or type(uint112).max, etc. it uses more gas in the distribution process and also for each transaction than constant usage.

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol

```solidity
File: talos/TalosStrategyVanilla.sol

111                amount0Max: type(uint128).max,

112                amount1Max: type(uint128).max,

```

```diff

diff --git a/TalosStrategyVanilla.org.sol b/TalosStrategyVanilla.sol
index 1f9ffbb..f7528cf 100644
--- a/TalosStrategyVanilla.org.sol
+++ b/TalosStrategyVanilla.sol
@@ -1,7 +1,10 @@
+     uint128 constant MAX_UINT128 = type(uint128).max;
+
+
    (uint256 collect0, uint256 collect1) = nonfungiblePositionManager.collect(
             INonfungiblePositionManager.CollectParams({
                 tokenId: _tokenId,
                 recipient: address(this),
-                amount0Max: type(uint128).max,
-                amount1Max: type(uint128).max
+                amount0Max: MAX_UINT128,
+                amount1Max: MAX_UINT128
             })

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol

```solidity
File: talos/TalosStrategyStaked.sol

151                amount0Max: type(uint128).max,

152                amount1Max: type(uint128).max,

```

```diff

diff --git a/TalosStrategyVanilla.org.sol b/TalosStrategyVanilla.sol
index 1f9ffbb..f7528cf 100644
--- a/TalosStrategyVanilla.org.sol
+++ b/TalosStrategyVanilla.sol
@@ -1,7 +1,10 @@
+     uint128 constant MAX_UINT128 = type(uint128).max;
+
+
         (uint256 collect0, uint256 collect1) = nonfungiblePositionManager.collect(
              INonfungiblePositionManager.CollectParams({
                tokenId: _tokenId,
                recipient: address(this),
-                amount0Max: type(uint128).max,
-                amount1Max: type(uint128).max
+                amount0Max: MAX_UINT128,
+                amount1Max: MAX_UINT128
             })

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol


133            require(newRewards <= type(uint112).max); // safe cast

187            require(nextRewards <= type(uint112).max); // safe cast
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

70        if (liquidity == type(uint96).max) {

385                        amount0Max: type(uint128).max,

386                        amount1Max: type(uint128).max

456        if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

506        if (liquidity >= type(uint96).max) {

509                liquidityNoOverflow: type(uint96).max,

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40

```solidity
File: ulysses-amm/UlyssesRouter.sol

40            address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L36

```solidity
File: rewards/base/BaseFlywheelRewards.sol

36        _rewardToken.safeApprove(address(_flywheel), type(uint256).max);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol

```solidity
File: erc-4626/ERC4626DepositOnly.sol

99        return type(uint256).max;

104        return type(uint256).max;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol

```solidity
File: erc-4626/UlyssesERC4626.sol

68            if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

113        return type(uint256).max;

117        return type(uint256).max;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L200-L204

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

200        address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);

201        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);

202        address(governance).safeApprove(newPartnerVault, type(uint256).max);

203        address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
```

```diff
diff --git a/ERC4626PartnerManager.org.sol b/ERC4626PartnerManager.sol
index 285a7e1..55dc8d4 100644
--- a/ERC4626PartnerManager.org.sol
+++ b/ERC4626PartnerManager.sol
@@ -1,8 +1,11 @@
+        uint256 constant MAX_UINT256 = type(uint256).max;
+
+
         address(partnerGovernance).safeApprove(oldPartnerVault, 0);

-        address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
-        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
-        address(governance).safeApprove(newPartnerVault, type(uint256).max);
-        address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
+        address(gaugeWeight).safeApprove(newPartnerVault, MAX_UINT256;
+        address(gaugeBoost).safeApprove(newPartnerVault, MAX_UINT256;
+        address(governance).safeApprove(newPartnerVault, MAX_UINT256;
+        address(partnerGovernance).safeApprove(newPartnerVault, MAX_UINT256;

         partnerVault = newPartnerVault;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

```solidity
File: talos/base/TalosBaseStrategy.sol

130        address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

131        address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

249            if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares;

285                amount0Max: type(uint128).max,

286                amount0Max: type(uint128).max,

367                amount0Max: type(uint128).max,

368                amount0Max: type(uint128).max,

```

## [G-12] Use hardcode address instead `address(this)`

It can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol

```solidity
File: maia/PartnerUtilityManager.sol

75        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {

85        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {

95        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {

104        address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);

128        uint256 weightAvailable = address(gaugeWeight).balanceOf(address(this));

139        uint256 boostAvailable = address(gaugeBoost).balanceOf(address(this));

148        uint256 governanceAvailable = address(governance).balanceOf(address(this));

```

```diff
+        address public myAddress = 0x1234567890123456789012345678901234567890;


-        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
+        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(myAddress) > 0) {

-        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
+        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(myAddress) > 0) {

-        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
+        if (partnerVault != address(0) && address(governance).balanceOf(myAddress) > 0) {

-        address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);
+        address(partnerGovernance).safeTransferFrom(msg.sender, myAddress, amount);

-        uint256 weightAvailable = address(gaugeWeight).balanceOf(address(this));
+        uint256 weightAvailable = address(gaugeWeight).balanceOf(myAddress);

-        uint256 boostAvailable = address(gaugeBoost).balanceOf(address(this));
+        uint256 boostAvailable = address(gaugeBoost).balanceOf(myAddress);

-        uint256 governanceAvailable = address(governance).balanceOf(address(this));
+        uint256 governanceAvailable = address(governance).balanceOf(myAddress);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol

```solidity
File: erc-4626/ERC4626DepositOnly.sol

37        address(asset).safeTransferFrom(msg.sender, address(this), assets);

51        address(asset).safeTransferFrom(msg.sender, address(this), assets);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol

```solidity
File: erc-4626/UlyssesERC4626.sol


36        asset.safeTransferFrom(msg.sender, address(this), assets);

52        asset.safeTransferFrom(msg.sender, address(this), assets);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol

```solidity
File: gauges/BaseV2Gauge.sol

70        multiRewardsDepot = new MultiRewardsDepot(address(this));

115            if (isActive[_bribeFlywheels[i]]) _bribeFlywheels[i].accrue(ERC20(address(this)), user);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol

```solidity
File: hermes/UtilityManager.sol

72        address(gaugeWeight).safeTransferFrom(msg.sender, address(this), amount);

81        address(gaugeBoost).safeTransferFrom(msg.sender, address(this), amount);

90        address(governance).safeTransferFrom(msg.sender, address(this), amount);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol

```solidity
File: maia/tokens/ERC4626PartnerManager.sol

162        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

168        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

208        emit MigratePartnerVault(address(this), newPartnerVault);

219        if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) {

226            address(this), totalSupply * newRate - address(partnerGovernance).balanceOf(address(this))

244        ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

```solidity
File: talos/base/TalosBaseStrategy.sol


125        address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);

126        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);

146                recipient: address(this),

196        address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);

197        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);

366                recipient: address(this),

406        uint256 balance0 = _token0.balanceOf(address(this));

407        uint256 balance1 = _token1.balanceOf(address(this));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol

```solidity
File: talos/libraries/PoolActions.sol

47            address(this),

84                recipient: address(this),

98        balance0 = token0.balanceOf(address(this));

99        balance1 = token1.balanceOf(address(this));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

```solidity
File: ulysses-amm/UlyssesToken.sol

84        asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));

112            uint256 assetBalance = assets[i].balanceOf(address(this));

118                assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol

```solidity
File: talos/boost-aggregator/BoostAggregator.sol

90        nonfungiblePositionManager.safeTransferFrom(address(this), address(uniswapV3Staker), tokenId);

168        address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));

175        hermesGaugeBoost.updateUserBoost(address(this));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol

```solidity
File: talos/TalosStrategyVanilla.sol

110                recipient: address(this),

130        uint256 balance0 = token0.balanceOf(address(this)) - protocolFees0;

131        uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol


52        _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);

120                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

140                    address(this),

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol

```solidity
File: talos/TalosStrategyStaked.sol

90        flywheel.accrue(ERC20(address(this)), msg.sender, _to);

95        flywheel.accrue(ERC20(address(this)), _from, _to);

150                    recipient: address(this),

177        try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

```solidity
File: rewards/rewards/FlywheelGaugeRewards.sol

88        uint256 balanceBefore = rewardToken.balanceOf(address(this));

90        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);

130            uint256 balanceBefore = rewardToken.balanceOf(address(this));

132            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol

```solidity
File: ulysses-omnichain/BranchPort.sol

127        uint256 currBalance = ERC20(_token).balanceOf(address(this));

138        uint256 currBalance = ERC20(_token).balanceOf(address(this));

180        IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

249            _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);

254                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

271                    address(this),

276                _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol

```solidity
File: ulysses-amm/UlyssesPool.sol

103        return asset.balanceOf(address(this)) - getProtocolFees();

108        return balanceOf[owner].min(asset.balanceOf(address(this)));

127        uint256 balance = asset.balanceOf(address(this));

218            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

303            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

1109        asset.safeTransferFrom(msg.sender, address(this), assets);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

224        bridgeAgentExecutorAddress = DeployRootBridgeAgentExecutor.deploy(address(this));

668        IPort(localPortAddress).bridgeToRoot(address(this), gasTokenGlobalAddress, _amount, _amount, _fromChain);

685            address(this),

729            address(this),

766        IPort(localPortAddress).burn(address(this), gasToken, amountOut, _toChain);

851        IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));

1237        uint256 executionBudget = anycallConfig.executionBudget(address(this));

1328        if (msg.sender != IPort(localPortAddress).getBridgeAgentManager(address(this))) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol

1020        uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));

1078        IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);

1093        IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));

1103        IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);

1325        uint256 executionBudget = anycallConfig.executionBudget(address(this));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol

```solidity
File: hermes/bHermes.sol

116        return address(asset).balanceOf(address(this));

129        gaugeWeight.mint(address(this), amount);

130        gaugeBoost.mint(address(this), amount);

131        governance.mint(address(this), amount);
```

## [G-13] `abi.encode()` is less efficient than abi.encodePacked()

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol#L17

```solidity
File: uni-v3-staker/libraries/IncentiveId.sol

17        return keccak256(abi.encode(key));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L51

```solidity
File: talos/libraries/PoolActions.sol

51            abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

```solidity
File: governance/GovernorBravoDelegateMaia.sol

198            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

346            keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
347        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

348        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol

689            abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

733            abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))

```

## [G-14] Using `> 0` costs more gas than `!= 0`

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47

```solidity
File: ulysses-amm/UlyssesToken.sol

47        require(_weight > 0);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139

```solidity
File: talos/TalosStrategyVanilla.sol

139        if (_liquidity > 0) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111

```solidity
File: ulysses-amm/factories/UlyssesFactory.sol

111                if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

```solidity
File: ulysses-omnichain/ArbitrumBranchPort.sol

118        if (_deposit > 0) {

123        if (_amount - _deposit > 0) {

137            if (_deposits[i] > 0) {

144            if (_amounts[i] - _deposits[i] > 0) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol

```solidity
File: ulysses-omnichain/BranchPort.sol


248        if (_amount - _deposit > 0) {

252        if (_deposit > 0) {

268            if (_deposits[i] > 0) {

275            if (_amounts[i] - _deposits[i] > 0) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol

```solidity
File: uni-v3-staker/UniswapV3Staker.sol

199        if (incentive.numberOfStakes > 0) revert EndIncentiveWhileStakesArePresent();

271        if (reward > 0) hermes.safeTransfer(to, reward);

281        if (reward > 0) hermes.safeTransfer(to, reward);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol

```solidity
File: ulysses-omnichain/RootPort.sol

282        if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);

283        if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol

```solidity
File: ulysses-amm/UlyssesPool.sol


153        if (claimed > 0) {

191            if (oldBandwidth > 0) {

256                    if (oldBandwidth > 0) {

272            if (oldBandwidth > 0) {

282                    } else if (leftOverBandwidth > 0) {

911            if (updateAmount > 0) {

1048            if (updateAmount > 0) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

```solidity
File: ulysses-omnichain/RootBridgeAgent.sol


347            if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();

451        if (_amount - _deposit > 0) {

457        if (_deposit > 0) {

651        address(data.tokenIn).safeTransfer(msg.sender, uint256(amount0 > 0 ? amount0 : amount1));

750            if (_initialGas > 0) {

757        if (_initialGas > 0) {


1161            if (initialGas > 0) {

1171        if (initialGas > 0) {

1240        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

```solidity
File: ulysses-omnichain/BranchBridgeAgent.sol


499        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

543        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

627            if (_deposits[i] > 0) {

983        if (_deposit > 0) {

1328        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol

```solidity
File: rewards/base/FlywheelCore.sol

127        if (oldRewardBalance > 0) {

162        if (strategyRewardsAccrued > 0) {

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

```solidity
File: talos/base/TalosBaseStrategy.sol

409        if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410        if (amount1 > 0) _token1.transfer(msg.sender, amount1);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol

```solidity
File: erc-20/ERC20Gauges.sol

417        if (weight > 0) {

441        if (weight > 0) {

```

## [G-15] Do not calculate `constant`

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/lib/AnycallFlags.sol

```solidity
File: ulysses-omnichain/lib/AnycallFlags.sol


10    uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;

11    uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;

12    uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;

13    uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;

14    uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L24

```solidity
File: hermes/minters/BaseV2Minter.sol

24    uint256 internal constant week = 86400 * 7;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47

```solidity
File: talos/TalosStrategyVanilla.sol

47    uint24 private constant protocolFee = 2 * 1e5; //20%

```

## [G-16] instead of calculating a state var with `keccak256()` every time the contract is made pre calculate them before and only give the result to a contract

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46

```solidity
File: governance/GovernorBravoDelegateMaia.sol

42    bytes32 public constant DOMAIN_TYPEHASH =
43        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");


46      bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity
File: erc-20/ERC20MultiVotes.sol

360    bytes32 public constant DELEGATION_TYPEHASH =
361        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```

## [G-17] Ternary operation is cheaper than `if-else` statement

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L138

```solidity
File: ulysses-amm/UlyssesPool.sol

138        if (balance > assets) {
138            return balance - assets;
140        } else {
141            return 0;
142        }
```

```diff
diff --git a/UlyssesPool.org.sol b/UlyssesPool.sol
index 18872b1..f446801 100644
--- a/UlyssesPool.org.sol
+++ b/UlyssesPool.sol
@@ -3,9 +3,6 @@
             assets += bandwidthStateList[i].bandwidth;
         }

-        if (balance > assets) {
-            return balance - assets;
-        } else {
-            return 0;
-        }
+        return (balance > assets) ? (balance - assets) : 0;

     }

```

## [G-18] Use assembly to `hash` instead of solidity

For Example:

```solidity


function solidityHash(uint256 a, uint256 b) public view {
    //unoptimized
    keccak256(abi.encodePacked(a, b));
}

function assemblyHash(uint256 a, uint256 b) public view {
    //optimized
    assembly {
        mstore(0x00, a)
        mstore(0x20, b)
        let hashedVal := keccak256(0x00, 0x40)
    }
}
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L366-L369

```solidity
File: erc-20/ERC20MultiVotes.sol

366            keccak256(
367                abi.encodePacked(
368                    "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
369                )
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol#L17

```solidity
File: uni-v3-staker/libraries/IncentiveId.sol

17        return keccak256(abi.encode(key));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

```solidity
File: governance/GovernorBravoDelegateMaia.sol

198            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

346            keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

347        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

348        bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));

```

