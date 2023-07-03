## GAS Summary<a name="GAS Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#gas1-abiencode-is-less-efficient-than-abiencodepacked) | `abi.encode()` is less efficient than `abi.encodepacked()` | 20 | 2000 |
| [GAS&#x2011;2](#gas2-consider-activating-viair-for-deploying) | Consider activating `via-ir` for deploying | 1 | 250 |
| [GAS&#x2011;3](#gas3-use-assembly-in-place-of-abidecode-to-extract-calldata-values-more-efficiently) | Use assembly in place of `abi.decode` to extract calldata values more efficiently | 2 | 224 |
| [GAS&#x2011;4](#gas4-use-assembly-to-emit-events) | Use assembly to emit events | 146 | 5548 |
| [GAS&#x2011;5](#gas5-using-fixed-bytes-is-cheaper-than-using-string) | Using fixed bytes is cheaper than using `string` | 1 | 13 |
| [GAS&#x2011;6](#gas6-using-delete-statement-can-save-gas) | Using `delete` statement can save gas | 28 | 224 |
| [GAS&#x2011;7](#gas7-using--0-costs-more-gas-than-!-0-when-used-on-a-uint-in-a-require-statement) | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 2 | 12 |
| [GAS&#x2011;8](#gas8-multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache) | Multiple accesses of a mapping/array should use a local variable cache | 439 | 35120 |
| [GAS&#x2011;9](#gas9-optimize-names-to-save-gas) | Optimize names to save gas | 80 | 1760 |
| [GAS&#x2011;10](#gas10-using-private-rather-than-public-for-constants-saves-gas) | Using `private` rather than `public` for constants, saves gas | 20 | 68000 |
| [GAS&#x2011;11](#gas11-require-should-be-used-instead-of-assert) | `require()` Should Be Used Instead Of `assert()` | 3 | 24 |
| [GAS&#x2011;12](#gas12-the-result-of-a-function-call-should-be-cached-rather-than-recalling-the-function) | The result of a function call should be cached rather than re-calling the function | 394 | 19700 |
| [GAS&#x2011;13](#gas13-shorten-the-array-rather-than-copying-to-a-new-one) | Shorten the array rather than copying to a new one | 23 | 1150 |
| [GAS&#x2011;14](#gas14-splitting-require-statements-that-use-&&-saves-gas) | Splitting `require()` Statements That Use `&&` Saves Gas | 1 | 9 |
| [GAS&#x2011;15](#gas15-state-variables-can-be-packed-into-fewer-storage-slots) | State variables can be packed into fewer storage slots | 1 | 2000 |
| [GAS&#x2011;16](#gas16-help-the-optimizer-by-saving-a-storage-variable’s-reference-instead-of-repeatedly-fetching-it) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 23 | 1242 |
| [GAS&#x2011;17](#gas14-use-v490-openzeppelin-contracts) | Use v4.9.0 OpenZeppelin contracts | 1 | 28 |
| [GAS&#x2011;18](#gas34-use-short-circuiting-rules-to-your-advantage) | Use Short Circuiting rules to your advantage | 1 | 1000 |


Total: 1186 contexts over 18 issues

## Gas Optimizations

### <a href="#gas-summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20MultiVotes.sol

368: "/x19/x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L368

```solidity
File: GovernorBravoDelegateMaia.sol

198: !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L198

```solidity
File: GovernorBravoDelegateMaia.sol

346: keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
File: GovernorBravoDelegateMaia.sol

347: bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L347

```solidity
File: PoolActions.sol

51: abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolActions.sol#L51

```solidity
File: ArbitrumCoreBranchRouter.sol

53: bytes memory data = abi.encode(_underlyingAddress, address(0), name, symbol);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L53

```solidity
File: ArbitrumCoreBranchRouter.sol

98: bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L98

```solidity
File: CoreBranchRouter.sol

48: bytes memory data = abi.encode(msg.sender, _globalAddress, _toChain, _rootExecutionGas);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L48

```solidity
File: CoreBranchRouter.sol

72: bytes memory data = abi.encode(_underlyingAddress, newToken, name, symbol);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L72

```solidity
File: CoreBranchRouter.sol

105: bytes memory data = abi.encode(_globalAddress, newToken);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L105

```solidity
File: CoreBranchRouter.sol

148: bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L148

```solidity
File: CoreRootRouter.sol

107: bytes memory data = abi.encode(
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L107

```solidity
File: CoreRootRouter.sol

158: bytes memory data = abi.encode(
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L158

```solidity
File: CoreRootRouter.sol

238: bytes memory data = abi.encode(_branchBridgeAgentFactory);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L238

```solidity
File: CoreRootRouter.sol

259: bytes memory data = abi.encode(_branchBridgeAgent);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L259

```solidity
File: CoreRootRouter.sol

282: bytes memory data = abi.encode(_underlyingToken, _minimumReservesRatio);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L282

```solidity
File: CoreRootRouter.sol

309: bytes memory data = abi.encode(_portStrategy, _underlyingToken, _dailyManagementLimit, _isUpdateDailyLimit);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L309

```solidity
File: RootBridgeAgent.sol

689: abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689

```solidity
File: RootBridgeAgent.sol

733: abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L733

```solidity
File: IncentiveId.sol

17: return keccak256(abi.encode(key));
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveId.sol#L17



</details>



### <a href="#gas-summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Consider activating `via-ir` for deploying

The IR-based code generator was introduced with an aim to not only allow code generation to be more transparent and auditable but also to enable more powerful optimization passes that span across functions.

You can enable it on the command line using `--via-ir` or with the option `{"viaIR": true}`.

This will take longer to compile, but you can just simple test it before deploying and if you got a better benchmark then you can add --via-ir to your deploy command

More on: https://docs.soliditylang.org/en/v0.8.17/ir-breaking-changes.html





### <a href="#gas-summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Use assembly in place of `abi.decode` to extract calldata values more efficiently

Instead of using `abi.decode`, we can use assembly to decode our desired calldata values directly. This will allow us to avoid decoding calldata values that we will not use.

For example, for a generic `abi.decode` call:
```solidity
(bytes32 varA, bytes32 varB, uint256 varC, uint256 varD) = abi.decode(metadata, (bytes32, bytes32, uint256, uint256));
```

We can use the following assembly call to extract the relevant metadata:

```solidity
        bytes32 varA;
        bytes32 varB;
        uint256 varC;
        uint256 varD;
        { // used to discard `data` variable and avoid extra stack manipulation
            bytes calldata data = metadata;
            assembly {
                varA := calldataload(add(data.offset, 0x00))
                varB := calldataload(add(data.offset, 0x20))
                varC := calldataload(add(data.offset, 0x40))
                varD := calldataload(add(data.offset, 0x60))
            }
        }
```

#### <ins>Proof Of Concept</ins>

```solidity
File: ArbitrumCoreBranchRouter.sol

143: (address underlyingToken, uint256 minimumReservesRatio) = abi.decode(_data[1:], (address, uint256));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L143

```solidity
File: CoreBranchRouter.sol

268: (address underlyingToken, uint256 minimumReservesRatio) = abi.decode(_data[1:], (address, uint256));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L268






### <a href="#gas-summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Use assembly to emit events

We can use assembly to emit events efficiently by utilizing `scratch space` and the `free memory pointer`. This will allow us to potentially avoid memory expansion costs.
Note: In order to do this optimization safely, we will need to cache and restore the free memory pointer.

For example, for a generic `emit` event for `eventSentAmountExample`:
```solidity
// uint256 id, uint256 value, uint256 amount
emit eventSentAmountExample(id, value, amount);
```

We can use the following assembly emit events:

```solidity
        assembly {
            let memptr := mload(0x40)
            mstore(0x00, calldataload(0x44))
            mstore(0x20, calldataload(0xa4))
            mstore(0x40, amount)
            log1(
                0x00,
                0x60,
                // keccak256("eventSentAmountExample(uint256,uint256,uint256)")
                0xa622cf392588fbf2cd020ff96b2f4ebd9c76d7a4bc7f3e6b2f18012312e76bc3
            )
            mstore(0x40, memptr)
        }
```

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20Boost.sol

128: emit UpdateUserBoost(user, userGaugeBoost);
134: emit Attach(user, msg.sender, userGaugeBoost);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L128

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L134



```solidity
File: ERC20Boost.sol

142: emit Detach(user, msg.sender);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L142

```solidity
File: ERC20Boost.sol

171: emit UpdateUserBoost(user, userBoost);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L171

```solidity
File: ERC20Boost.sol

181: emit Detach(msg.sender, gauge);
185: emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L181

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L185



```solidity
File: ERC20Boost.sol

194: emit Detach(msg.sender, gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L194

```solidity
File: ERC20Boost.sol

216: emit Detach(msg.sender, gauge);
220: emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L216

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L220



```solidity
File: ERC20Boost.sol

242: emit Detach(msg.sender, gauge);
251: emit UpdateUserBoost(msg.sender, 0);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L242

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L251



```solidity
File: ERC20Boost.sol

269: emit AddGauge(gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L269

```solidity
File: ERC20Boost.sol

281: emit RemoveGauge(gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L281

```solidity
File: ERC20Gauges.sol

219: emit IncrementGaugeWeight(user, gauge, weight, cycle);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L219

```solidity
File: ERC20Gauges.sol

307: emit DecrementGaugeWeight(user, gauge, weight, cycle);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L307

```solidity
File: ERC20Gauges.sol

421: emit AddGauge(gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L421

```solidity
File: ERC20Gauges.sol

445: emit RemoveGauge(gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L445

```solidity
File: ERC20Gauges.sol

459: emit MaxGaugesUpdate(oldMax, newMax);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L459

```solidity
File: ERC20Gauges.sol

468: emit CanContractExceedMaxGaugesUpdate(account, canExceedMax);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L468

```solidity
File: ERC20MultiVotes.sol

100: emit MaxDelegatesUpdate(oldMax, newMax);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L100

```solidity
File: ERC20MultiVotes.sol

109: emit CanContractExceedMaxDelegatesUpdate(account, canExceedMax);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L109

```solidity
File: ERC20MultiVotes.sol

178: emit DelegateChanged(delegator, oldDelegatee, newDelegatee);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L178

```solidity
File: ERC20MultiVotes.sol

202: emit Delegation(delegator, delegatee, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L202

```solidity
File: ERC20MultiVotes.sol

231: emit Undelegation(delegator, delegatee, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L231

```solidity
File: ERC20MultiVotes.sol

255: emit DelegateVotesChanged(delegatee, oldWeight, newWeight);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L255

```solidity
File: ERC20MultiVotes.sol

347: emit Undelegation(user, delegatee, votesToFree);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L347

```solidity
File: ERC4626.sol

41: emit Deposit(msg.sender, receiver, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L41

```solidity
File: ERC4626.sol

55: emit Deposit(msg.sender, receiver, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L55

```solidity
File: ERC4626.sol

74: emit Withdraw(msg.sender, receiver, owner, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L74

```solidity
File: ERC4626.sol

94: emit Withdraw(msg.sender, receiver, owner, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L94

```solidity
File: ERC4626DepositOnly.sol

41: emit Deposit(msg.sender, receiver, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L41

```solidity
File: ERC4626DepositOnly.sol

55: emit Deposit(msg.sender, receiver, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L55

```solidity
File: ERC4626MultiToken.sol

57: emit AssetAdded(_assets[i], _weights[i]);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L57

```solidity
File: ERC4626MultiToken.sol

107: emit Deposit(msg.sender, receiver, assetsAmounts, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L107

```solidity
File: ERC4626MultiToken.sol

126: emit Deposit(msg.sender, receiver, assetsAmounts, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L126

```solidity
File: ERC4626MultiToken.sol

150: emit Withdraw(msg.sender, receiver, owner, assetsAmounts, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L150

```solidity
File: ERC4626MultiToken.sol

182: emit Withdraw(msg.sender, receiver, owner, assetsAmounts, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L182

```solidity
File: UlyssesERC4626.sol

44: emit Deposit(msg.sender, receiver, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L44

```solidity
File: UlyssesERC4626.sol

56: emit Deposit(msg.sender, receiver, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L56

```solidity
File: UlyssesERC4626.sol

77: emit Withdraw(msg.sender, receiver, owner, assets, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L77

```solidity
File: BaseV2Gauge.sol

93: emit Distribute(accruedRewards, _newEpoch);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L93

```solidity
File: BaseV2Gauge.sol

140: emit AddedBribeFlywheel(bribeFlywheel);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L140

```solidity
File: BaseV2Gauge.sol

151: emit RemoveBribeFlywheel(bribeFlywheel);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L151

```solidity
File: UniswapV3Gauge.sol

65: emit NewMinimumWidth(_minimumWidth);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/UniswapV3Gauge.sol#L65

```solidity
File: BaseV2GaugeManager.sol

117: emit AddedGaugeFactory(address(gaugeFactory));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L117

```solidity
File: BaseV2GaugeManager.sol

129: emit RemovedGaugeFactory(address(gaugeFactory));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L129

```solidity
File: BaseV2GaugeManager.sol

141: emit ChangedbHermesGaugeOwner(newOwner);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L141

```solidity
File: BaseV2GaugeManager.sol

148: emit ChangedAdmin(newAdmin);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L148

```solidity
File: BribesFactory.sol

98: emit BribeFlywheelCreated(bribeToken, flywheel);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BribesFactory.sol#L98

```solidity
File: GovernorBravoDelegateMaia.sol

187: emit ProposalQueued(proposalId, eta);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L187

```solidity
File: GovernorBravoDelegateMaia.sol

220: emit ProposalExecuted(proposalId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L220

```solidity
File: GovernorBravoDelegateMaia.sol

257: emit ProposalCanceled(proposalId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L257

```solidity
File: GovernorBravoDelegateMaia.sol

327: emit VoteCast(msg.sender, proposalId, support, castVoteInternal(msg.sender, proposalId, support), "");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L327

```solidity
File: GovernorBravoDelegateMaia.sol

337: emit VoteCast(msg.sender, proposalId, support, castVoteInternal(msg.sender, proposalId, support), reason);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L337

```solidity
File: GovernorBravoDelegateMaia.sol

351: emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), "");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L351

```solidity
File: GovernorBravoDelegateMaia.sol

406: emit VotingDelaySet(oldVotingDelay, votingDelay);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L406

```solidity
File: GovernorBravoDelegateMaia.sol

422: emit VotingPeriodSet(oldVotingPeriod, votingPeriod);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L422

```solidity
File: GovernorBravoDelegateMaia.sol

439: emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L439

```solidity
File: GovernorBravoDelegateMaia.sol

454: emit WhitelistAccountExpirationSet(account, expiration);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L454

```solidity
File: GovernorBravoDelegateMaia.sol

466: emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L466

```solidity
File: GovernorBravoDelegateMaia.sol

498: emit NewPendingAdmin(oldPendingAdmin, newPendingAdmin);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L498

```solidity
File: GovernorBravoDelegateMaia.sol

521: emit NewAdmin(oldAdmin, admin);
522: emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L521-L522

```solidity
File: GovernorBravoDelegator.sol

50: emit NewImplementation(oldImplementation, implementation);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L50

```solidity
File: UtilityManager.sol

74: emit ForfeitWeight(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L74

```solidity
File: UtilityManager.sol

83: emit ForfeitBoost(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L83

```solidity
File: UtilityManager.sol

92: emit ForfeitGovernance(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L92

```solidity
File: UtilityManager.sol

115: emit ClaimWeight(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L115

```solidity
File: UtilityManager.sol

124: emit ClaimBoost(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L124

```solidity
File: UtilityManager.sol

133: emit ClaimGovernance(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L133

```solidity
File: BaseV2Minter.sol

147: emit Mint(msg.sender, newWeeklyEmission, _circulatingSupply, _growth, share);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L147

```solidity
File: PartnerManagerFactory.sol

63: emit AddedPartner(newPartnerManager, id);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/factories/PartnerManagerFactory.sol#L63

```solidity
File: PartnerManagerFactory.sol

72: emit AddedVault(newVault, id);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/factories/PartnerManagerFactory.sol#L72

```solidity
File: PartnerManagerFactory.sol

85: emit RemovedPartner(partnerManager);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/factories/PartnerManagerFactory.sol#L85

```solidity
File: PartnerManagerFactory.sol

94: emit RemovedVault(vault);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/factories/PartnerManagerFactory.sol#L94

```solidity
File: ERC4626PartnerManager.sol

208: emit MigratePartnerVault(address(this), newPartnerVault);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L208

```solidity
File: GovernorBravoDelegator.sol

49: emit NewImplementation(oldImplementation, implementation);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L49

```solidity
File: FlywheelCore.sol

102: emit ClaimRewards(user, accrued);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L102

```solidity
File: FlywheelCore.sol

121: emit AddStrategy(address(strategy));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L121

```solidity
File: FlywheelCore.sol

133: emit FlywheelRewardsUpdate(address(newFlywheelRewards));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L133

```solidity
File: FlywheelCore.sol

140: emit FlywheelBoosterUpdate(address(newBooster));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L140

```solidity
File: FlywheelCore.sol

206: emit AccrueRewards(strategy, user, supplierDelta, index);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L206

```solidity
File: MultiRewardsDepot.sol

53: emit AssetAdded(rewardsContract, asset);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/MultiRewardsDepot.sol#L53

```solidity
File: MultiRewardsDepot.sol

60: emit AssetRemoved(rewardsContract, _assets[rewardsContract]);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/MultiRewardsDepot.sol#L60

```solidity
File: FlywheelAcummulatedRewards.sol

53: emit NewRewardsCycle(timestamp, newEndCycle, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L53

```solidity
File: FlywheelBribeRewards.sol

41: emit AddRewardsDepot(msg.sender, rewardsDepot);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelBribeRewards.sol#L41

```solidity
File: FlywheelGaugeRewards.sol

103: emit CycleStart(currentCycle, totalQueuedForCycle);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L103

```solidity
File: FlywheelGaugeRewards.sol

147: emit CycleStart(currentCycle, queued);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L147

```solidity
File: FlywheelGaugeRewards.sol

195: emit QueueRewards(address(gauge), currentCycle, nextRewards);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L195

```solidity
File: TalosStrategyVanilla.sol

124: emit CollectFees(earnedProtocolFees0, earnedProtocolFees1, collect0, collect1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L124

```solidity
File: TalosStrategyVanilla.sol

133: emit Snapshot(balance0, balance1);
152: emit CompoundFees(amount0, amount1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L133

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L152



```solidity
File: TalosBaseStrategy.sol

160: emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L160

```solidity
File: TalosBaseStrategy.sol

221: emit Deposit(msg.sender, receiver, amount0, amount1, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L221

```solidity
File: TalosBaseStrategy.sol

279: emit Redeem(msg.sender, receiver, _owner, amount0, amount1, shares);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L279

```solidity
File: TalosBaseStrategy.sol

305: emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L305

```solidity
File: TalosBaseStrategy.sol

318: emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L318

```solidity
File: TalosBaseStrategy.sol

414: emit RewardPaid(msg.sender, amount0, amount1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L414

```solidity
File: PoolActions.sol

71: emit Snapshot(balance0, balance1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolActions.sol#L71

```solidity
File: PoolVariables.sol

229: emit Snapshot(cache.amount0Desired, cache.amount1Desired);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L229

```solidity
File: UlyssesPool.sol

1140: emit Swap(msg.sender, poolId, assets);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L1140

```solidity
File: UlyssesToken.sol

54: emit AssetAdded(asset, _weight);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L54

```solidity
File: UlyssesToken.sol

80: emit AssetRemoved(asset);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L80

```solidity
File: UlyssesToken.sol

98: emit AssetRemoved(assets[i]);
99: emit AssetAdded(assets[i], _weights[i]);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L98-L99

```solidity
File: BranchBridgeAgent.sol

1214: emit LogCallin(flag, data, rootChainId);
1214: emit LogCallin(flag, data, rootChainId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1214

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1214



```solidity
File: BranchBridgeAgent.sol

1250: emit LogCalloutFail(flag, data, rootChainId);
1250: emit LogCalloutFail(flag, data, rootChainId);
1250: emit LogCalloutFail(flag, data, rootChainId);
1250: emit LogCalloutFail(flag, data, rootChainId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1250

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1250

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1250

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1250



```solidity
File: BranchPort.sol

168: emit DebtCreated(msg.sender, _token, _amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L168

```solidity
File: BranchPort.sol

185: emit DebtRepaid(_strategy, _token, amountToWithdraw);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L185

```solidity
File: BranchPort.sol

313: emit BridgeAgentFactoryAdded(_newBridgeAgentFactory);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L313

```solidity
File: BranchPort.sol

320: emit BridgeAgentFactoryToggled(_newBridgeAgentFactory);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L320

```solidity
File: BranchPort.sol

327: emit BridgeAgentToggled(_bridgeAgent);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L327

```solidity
File: BranchPort.sol

338: emit StrategyTokenAdded(_token, _minimumReservesRatio);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L338

```solidity
File: BranchPort.sol

345: emit StrategyTokenToggled(_token);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L345

```solidity
File: BranchPort.sol

359: emit PortStrategyAdded(_portStrategy, _token, _dailyManagementLimit);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L359

```solidity
File: BranchPort.sol

366: emit PortStrategyToggled(_portStrategy, _token);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L366

```solidity
File: BranchPort.sol

376: emit PortStrategyUpdated(_portStrategy, _token, _dailyManagementLimit);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L376

```solidity
File: RootBridgeAgent.sol

1168: emit LogCallin(flag, data, fromChainId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1168

```solidity
File: RootBridgeAgent.sol

1211: emit LogCalloutFail(flag, data, fromChainId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1211

```solidity
File: RootPort.sol

257: emit LocalTokenAdded(_underlyingAddress, _localAddress, _globalAddress, _fromChain);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L257

```solidity
File: RootPort.sol

268: emit GlobalTokenAdded(_localAddress, _globalAddress, _fromChain);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L268

```solidity
File: RootPort.sol

353: emit VirtualAccountCreated(_user, address(newAccount));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L353

```solidity
File: RootPort.sol

374: emit BridgeAgentAdded(_bridgeAgent, _manager);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L374

```solidity
File: RootPort.sol

391: emit BridgeAgentSynced(_newBranchBridgeAgent, _rootBridgeAgent, _branchChainId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L391

```solidity
File: RootPort.sol

402: emit BridgeAgentToggled(_bridgeAgent);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L402

```solidity
File: RootPort.sol

409: emit BridgeAgentFactoryAdded(_bridgeAgentFactory);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L409

```solidity
File: RootPort.sol

416: emit BridgeAgentFactoryToggled(_bridgeAgentFactory);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L416

```solidity
File: RootPort.sol

480: emit NewChainAdded(_chainId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L480

```solidity
File: RootPort.sol

487: emit GasPoolInfoSet(_chainId, _gasPoolInfo);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L487

```solidity
File: RootPort.sol

502: emit EcosystemTokenAdded(_ecoTokenGlobalAddress);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L502

```solidity
File: UniswapV3Staker.sol

153: emit IncentiveCreated(pool, startTime, reward);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L153

```solidity
File: UniswapV3Staker.sol

179: emit IncentiveCreated(key.pool, startTime, reward);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L179

```solidity
File: UniswapV3Staker.sol

208: emit IncentiveEnded(incentiveId, refund);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L208

```solidity
File: UniswapV3Staker.sol

230: emit DepositTransferred(tokenId, address(0), from);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L230

```solidity
File: UniswapV3Staker.sol

252: emit DepositTransferred(tokenId, msg.sender, address(0));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L252

```solidity
File: UniswapV3Staker.sol

273: emit RewardClaimed(to, reward);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L273

```solidity
File: UniswapV3Staker.sol

283: emit RewardClaimed(to, reward);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L283

```solidity
File: UniswapV3Staker.sol

458: emit TokenUnstaked(tokenId, incentiveId);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L458

```solidity
File: UniswapV3Staker.sol

518: emit TokenStaked(tokenId, incentiveId, liquidity);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L518

```solidity
File: UniswapV3Staker.sol

532: emit GaugeUpdated(uniswapV3Pool, uniswapV3Gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L532

```solidity
File: UniswapV3Staker.sol

548: emit BribeDepotUpdated(uniswapV3Pool, newDepot);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L548

```solidity
File: UniswapV3Staker.sol

558: emit PoolMinimumWidthUpdated(uniswapV3Pool, minimumWidth);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L558



</details>









### <a href="#gas-summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Using fixed bytes is cheaper than using `string`

As a rule of thumb, use `bytes` for arbitrary-length raw byte data and string for arbitrary-length `string` (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper.

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

9: string public constant name = "vMaia Governor Bravo";
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L9









### <a href="#gas-summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Using `delete` statement can save gas

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20Boost.sol

151: uint256 userBoost = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L151

```solidity
File: ERC20Boost.sol

249: getUserBoost[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L249

```solidity
File: ERC20MultiVotes.sol

67: uint256 low = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L67

```solidity
File: ERC20MultiVotes.sol

340: _delegatesVotesCount[user][delegatee] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L340

```solidity
File: GovernorBravoDelegateMaia.sol

149: newProposal.eta = 0;
156: newProposal.forVotes = 0;
157: newProposal.againstVotes = 0;
158: newProposal.abstainVotes = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L149

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L156

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L157

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L158



```solidity
File: BaseV2Minter.sol

165: weekly = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L165

```solidity
File: FlywheelCore.sol

98: rewardsAccrued[user] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L98

```solidity
File: FlywheelAcummulatedRewards.sol

55: amount = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L55

```solidity
File: FlywheelGaugeRewards.sol

100: nextCycleQueuedRewards = 0;
101: paginationOffset = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L100-L101

```solidity
File: FlywheelGaugeRewards.sol

122: paginationOffset = 0;
145: nextCycleQueuedRewards = 0;
122: paginationOffset = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L122

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L145

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L122



```solidity
File: FlywheelGaugeRewards.sol

225: cycleRewardsNext = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L225

```solidity
File: TalosBaseStrategy.sol

362: liquidity = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L362

```solidity
File: PoolVariables.sol

211: secondsAgo[1] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L211

```solidity
File: UlyssesToken.sol

78: assetId[asset] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L78

```solidity
File: UniswapV3Staker.sol

202: incentive.totalRewardUnclaimed = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L202

```solidity
File: UniswapV3Staker.sol

268: rewards[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L268

```solidity
File: UniswapV3Staker.sol

279: rewards[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L279

```solidity
File: UniswapV3Staker.sol

406: _userAttachements[owner][key.pool] = 0;
426: deposit.stakedTimestamp = 0;
454: stake.secondsPerLiquidityInsideInitialX128 = 0;
455: stake.liquidityNoOverflow = 0;
456: if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L406

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L426

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L454

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L455

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L456





</details>









### <a href="#gas-summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement

This change saves 6 gas per instance

#### <ins>Proof Of Concept</ins>


```solidity
File: ERC4626MultiToken.sol

52: require(_weights[i] > 0);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L52

```solidity
File: UlyssesToken.sol

47: require(_weight > 0);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L47





### <a href="#gas-summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `someMap[someIndex]`, save its reference like this: `SomeStruct storage someStruct = someMap[someIndex]` and use it.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20Boost.sol

126: if (getUserBoost[user] < userGaugeBoost) {
127: getUserBoost[user] = userGaugeBoost;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L126-L127

```solidity
File: ERC20Gauges.sol

233: newUserWeight = getUserWeight[user] + weight;
239: getUserWeight[user] = newUserWeight;
318: getUserWeight[user] = newUserWeight;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L233

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L239

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L318



```solidity
File: ERC20Gauges.sol

295: uint112 oldWeight = getUserGaugeWeight[user][gauge];
299: getUserGaugeWeight[user][gauge] = oldWeight - weight;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L295

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L299



```solidity
File: ERC20Gauges.sol

317: newUserWeight = getUserWeight[user] - weight;
239: getUserWeight[user] = newUserWeight;
318: getUserWeight[user] = newUserWeight;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L317

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L239

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L318



```solidity
File: ERC20MultiVotes.sol

222: uint256 newDelegates = _delegatesVotesCount[delegator][delegatee] - amount;
228: _delegatesVotesCount[delegator][delegatee] = newDelegates;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L222

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L228



```solidity
File: ERC20MultiVotes.sol

247: uint256 oldWeight = pos == 0 ? 0 : ckpts[pos - 1].votes;
250: if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {
251: ckpts[pos - 1].votes = newWeight.toUint224();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L247

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L250

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L251



```solidity
File: ERC20MultiVotes.sol

330: uint256 delegateVotes = _delegatesVotesCount[user][delegatee];
340: _delegatesVotesCount[user][delegatee] = 0;
343: _delegatesVotesCount[user][delegatee] -= votesToFree;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L340

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L343



```solidity
File: ERC4626.sol

65: uint256 allowed = allowance[owner][msg.sender];
82: uint256 allowed = allowance[owner][msg.sender];
67: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
84: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L65

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L82

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L67

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L84



```solidity
File: ERC4626MultiToken.sol

52: require(_weights[i] > 0);
54: _totalWeights += _weights[i];

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L52

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L54



```solidity
File: ERC4626MultiToken.sol

141: uint256 allowed = allowance[owner][msg.sender];
163: uint256 allowed = allowance[owner][msg.sender];
143: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
165: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L141

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L163

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L143

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L165



```solidity
File: UlyssesERC4626.sol

66: uint256 allowed = allowance[owner][msg.sender];
68: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L66

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L68



```solidity
File: BaseV2Gauge.sol

130: if (added[bribeFlywheel]) revert FlywheelAlreadyAdded();
138: added[bribeFlywheel] = true;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L130

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L138



```solidity
File: BaseV2GaugeFactory.sol

110: if (address(strategyGauges[strategy]) != address(0)) revert GaugeAlreadyExists();
113: strategyGauges[strategy] = gauge;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L110

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L113



```solidity
File: BaseV2GaugeManager.sol

111: if (activeGaugeFactories[gaugeFactory]) revert GaugeFactoryAlreadyExists();
115: activeGaugeFactories[gaugeFactory] = true;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L111

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L115



```solidity
File: BribesFactory.sol

73: if (address(flywheelTokens[bribeToken]) == address(0)) createBribeFlywheel(bribeToken);
75: flywheelTokens[bribeToken].addStrategyForRewards(ERC20(gauge));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BribesFactory.sol#L73

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BribesFactory.sol#L75



```solidity
File: BribesFactory.sol

80: if (address(flywheelTokens[bribeToken]) != address(0)) revert BribeFlywheelAlreadyExists();
89: flywheelTokens[bribeToken] = flywheel;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BribesFactory.sol#L80

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BribesFactory.sol#L89



```solidity
File: FlywheelCore.sol

95: uint256 accrued = rewardsAccrued[user];
98: rewardsAccrued[user] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L95

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L98



```solidity
File: FlywheelCore.sol

116: require(strategyIndex[strategy] == 0, "strategy");
117: strategyIndex[strategy] = ONE;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L116-L117

```solidity
File: FlywheelCore.sol

183: uint256 supplierIndex = userIndex[strategy][user];
186: userIndex[strategy][user] = index;
202: uint256 supplierAccrued = rewardsAccrued[user] + supplierDelta;
204: rewardsAccrued[user] = supplierAccrued;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L183

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L186

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L202

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L204



```solidity
File: FlywheelGaugeRewards.sol

179: QueuedRewards memory queuedRewards = gaugeQueuedRewards[gauge];
189: gaugeQueuedRewards[gauge] = QueuedRewards({

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L179

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L189



```solidity
File: FlywheelGaugeRewards.sol

204: QueuedRewards memory queuedRewards = gaugeQueuedRewards[ERC20(msg.sender)];
228: gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L204

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L228



```solidity
File: TalosBaseStrategy.sol

247: uint256 allowed = allowance[_owner][msg.sender];
249: if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L247

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L249



```solidity
File: UlyssesPool.sol

131: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
176: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
212: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
233: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
297: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
133: assets += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
135: assets += bandwidthStateList[i].bandwidth;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L176

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L233

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L297

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L133

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L135



```solidity
File: UlyssesPool.sol

165: if (destinationIds[address(destination)] != 0 || destinationId == id) revert InvalidPool();
131: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
176: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
212: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
233: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
297: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
178: oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
235: oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
190: uint256 oldBandwidth = bandwidthStateList[i].bandwidth;
255: uint256 oldBandwidth = bandwidthStateList[i].bandwidth;
192: bandwidthStateList[i].bandwidth = oldBandwidth.mulDivUp(oldTotalWeights, newTotalWeights).toUint248();
194: newBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;
207: destinationIds[address(destination)] = index;
131: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
176: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
212: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
233: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
297: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
214: newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
299: newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L176

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L233

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L297

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L178

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L235

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L190

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L255

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L192

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L194

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L207

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L176

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L233

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L297

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L214

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L299



```solidity
File: UlyssesPool.sol

131: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
176: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
212: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
233: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
297: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
178: oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
235: oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
190: uint256 oldBandwidth = bandwidthStateList[i].bandwidth;
255: uint256 oldBandwidth = bandwidthStateList[i].bandwidth;
192: bandwidthStateList[i].bandwidth =
257: bandwidthStateList[i].bandwidth =
260: leftOverBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;
281: bandwidthStateList[i].bandwidth += leftOverBandwidth.toUint248();
281: bandwidthStateList[i].bandwidth +=
283: bandwidthStateList[i].bandwidth +=
284: leftOverBandwidth.mulDiv(bandwidthStateList[i].weight, weightsWithoutPool).toUint248();
131: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
176: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
212: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
233: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
297: uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);
214: newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);
299: newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L176

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L233

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L297

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L178

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L235

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L190

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L255

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L192

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L257

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L260

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L281

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L281

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L283

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L284

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L176

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L233

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L297

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L214

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L299



```solidity
File: UlyssesRouter.sol

32: ulysses = pools[id];
38: pools[id] = ulysses;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesRouter.sol#L32

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesRouter.sol#L38



```solidity
File: UlyssesToken.sol

45: if (assetId[asset] != 0) revert AssetAlreadyAdded();
49: assetId[asset] = assets.length + 1;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L45

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L49



```solidity
File: UlyssesToken.sol

62: uint256 assetIndex = assetId[asset] - 1;
78: assetId[asset] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L62

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L78



```solidity
File: UlyssesToken.sol

112: uint256 assetBalance = assets[i].balanceOf(address(this));
116: assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);
118: assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L112

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L116

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L118



```solidity
File: ArbitrumCoreBranchRouter.sol

118: if (_data[0] == 0x02) {
124: ) = abi.decode(_data[1:], (address, address, address, address, uint128));
131: } else if (_data[0] == 0x03) {
132: (address bridgeAgentFactoryAddress) = abi.decode(_data[1:], (address));
137: } else if (_data[0] == 0x04) {
138: (address branchBridgeAgent) = abi.decode(_data[1:], (address));
142: } else if (_data[0] == 0x05) {
143: (address underlyingToken, uint256 minimumReservesRatio) = abi.decode(_data[1:], (address, uint256));
147: } else if (_data[0] == 0x06) {
149: abi.decode(_data[1:], (address, address, uint256, bool));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L118

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L132

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L137

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L142

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L143

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L147

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L149



```solidity
File: BranchBridgeAgent.sol

327: if (getDeposit[_depositNonce].owner != msg.sender) revert NotDepositOwner();
332: if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {
338: getDeposit[_depositNonce].hTokens[0],
353: getDeposit[_depositNonce].hTokens[0],
339: getDeposit[_depositNonce].tokens[0],
354: getDeposit[_depositNonce].tokens[0],
340: getDeposit[_depositNonce].amounts[0],
355: getDeposit[_depositNonce].amounts[0],
342: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
338: getDeposit[_depositNonce].hTokens[0],
353: getDeposit[_depositNonce].hTokens[0],
339: getDeposit[_depositNonce].tokens[0],
354: getDeposit[_depositNonce].tokens[0],
340: getDeposit[_depositNonce].amounts[0],
355: getDeposit[_depositNonce].amounts[0],
342: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
365: } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {
373: uint8(getDeposit[_depositNonce].hTokens.length),
375: getDeposit[nonce].hTokens,
389: getDeposit[nonce].hTokens,
376: getDeposit[nonce].tokens,
390: getDeposit[nonce].tokens,
377: getDeposit[nonce].amounts,
391: getDeposit[nonce].amounts,
378: _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
392: _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
387: uint8(getDeposit[nonce].hTokens.length),
375: getDeposit[nonce].hTokens,
389: getDeposit[nonce].hTokens,
376: getDeposit[nonce].tokens,
390: getDeposit[nonce].tokens,
377: getDeposit[nonce].amounts,
391: getDeposit[nonce].amounts,
378: _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
392: _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
408: getDeposit[_depositNonce].status = DepositStatus.Success;
411: getDeposit[_depositNonce].depositedGas = msg.value.toUint128();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L327

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L332

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L338

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L353

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L339

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L354

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L340

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L355

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L342

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L357

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L338

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L353

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L339

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L354

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L340

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L355

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L342

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L357

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L365

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L373

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L375

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L389

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L376

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L390

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L377

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L391

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L378

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L392

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L387

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L375

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L389

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L376

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L390

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L377

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L391

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L378

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L392

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L408

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L411



```solidity
File: BranchBridgeAgent.sol

613: _deposits[i] = uint256(
627: if (_deposits[i] > 0) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L613

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627



```solidity
File: BranchBridgeAgent.sol

1069: if (minExecCost > getDeposit[_depositNonce].depositedGas) {
1075: getDeposit[_depositNonce].depositedGas -= minExecCost.toUint128();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1069

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1075



```solidity
File: BranchBridgeAgent.sol

1145: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1167: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1148: if (executionHistory[nonce]) {
1170: if (executionHistory[nonce]) {
1194: if (executionHistory[nonce]) {
1162: executionHistory[nonce] = true;
1186: executionHistory[nonce] = true;
1210: executionHistory[nonce] = true;
1145: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1167: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1148: if (executionHistory[nonce]) {
1170: if (executionHistory[nonce]) {
1194: if (executionHistory[nonce]) {
1162: executionHistory[nonce] = true;
1186: executionHistory[nonce] = true;
1210: executionHistory[nonce] = true;
1148: if (executionHistory[nonce]) {
1170: if (executionHistory[nonce]) {
1194: if (executionHistory[nonce]) {
1162: executionHistory[nonce] = true;
1186: executionHistory[nonce] = true;
1210: executionHistory[nonce] = true;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1145

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1167

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1170

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1194

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1162

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1186

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1210

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1145

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1167

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1170

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1194

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1162

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1186

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1210

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1170

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1194

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1162

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1186

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1210



```solidity
File: BranchBridgeAgentExecutor.sol

135: + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))
144: + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))
135: + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))
144: + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))
149: PARAMS_END_SIGNED_OFFSET + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * PARAMS_TKN_SET_SIZE):

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L135

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L144

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L135

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L144

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L149



```solidity
File: BranchPort.sol

101: require(!isBridgeAgentFactory[_bridgeAgentFactory], "Contract already initialized");
107: isBridgeAgentFactory[_bridgeAgentFactory] = true;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L101

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L107



```solidity
File: BranchPort.sol

194: if (block.timestamp - lastManaged[msg.sender][_token] >= 1 days) {
198: lastManaged[msg.sender][_token] = block.timestamp;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L194

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L198



```solidity
File: CoreBranchRouter.sol

237: if (_data[0] == 0x01) {
239: abi.decode(_data[1:], (address, string, string, uint128));
243: } else if (_data[0] == 0x02) {
250: ) = abi.decode(_data[1:], (address, address, address, address, uint128));
257: } else if (_data[0] == 0x03) {
258: (address bridgeAgentFactoryAddress) = abi.decode(_data[1:], (address));
262: } else if (_data[0] == 0x04) {
263: (address branchBridgeAgent) = abi.decode(_data[1:], (address));
267: } else if (_data[0] == 0x05) {
268: (address underlyingToken, uint256 minimumReservesRatio) = abi.decode(_data[1:], (address, uint256));
272: } else if (_data[0] == 0x06) {
274: abi.decode(_data[1:], (address, address, uint256, bool));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L237

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L239

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L243

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L250

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L257

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L258

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L262

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L263

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L267

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L268

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L272

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L274



```solidity
File: RootBridgeAgent.sol

257: address depositOwner = getSettlement[_depositNonce].owner;
260: if (getSettlement[_depositNonce].status != SettlementStatus.Failed || depositOwner == address(0)) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L257

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L260



```solidity
File: RootBridgeAgent.sol

598: if (settlement.hTokens[i] != address(0)) {
602: IPort(localPortAddress).getGlobalTokenFromLocal(settlement.hTokens[i], settlement.toChain),

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L598

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L602



```solidity
File: RootBridgeAgent.sol

839: if (minExecCost > getSettlement[_settlementNonce].gasToBridgeOut) {
845: getSettlement[_settlementNonce].gasToBridgeOut -= minExecCost.toUint128();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L839

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L845



```solidity
File: RootBridgeAgent.sol

916: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
941: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
966: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
916: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
941: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
966: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
916: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
941: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
966: uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
1014: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1050: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1085: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
1025: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
1061: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
1096: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
1014: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1050: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1085: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
1025: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
1061: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
1096: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
1014: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1050: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1085: uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
1025: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
1061: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
1096: address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
1120: uint32 nonce = uint32(bytes4(data[1:5]));
1143: uint32 nonce = uint32(bytes4(data[1:5]));
919: if (executionHistory[fromChainId][nonce]) {
944: if (executionHistory[fromChainId][nonce]) {
969: if (executionHistory[fromChainId][nonce]) {
993: if (executionHistory[fromChainId][nonce]) {
1017: if (executionHistory[fromChainId][nonce]) {
1053: if (executionHistory[fromChainId][nonce]) {
1088: if (executionHistory[fromChainId][nonce]) {
1123: if (executionHistory[fromChainId][nonce]) {
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;
1120: uint32 nonce = uint32(bytes4(data[1:5]));
1143: uint32 nonce = uint32(bytes4(data[1:5]));
936: executionHistory[fromChainId][nonce] = true;
961: executionHistory[fromChainId][nonce] = true;
985: executionHistory[fromChainId][nonce] = true;
1009: executionHistory[fromChainId][nonce] = true;
1045: executionHistory[fromChainId][nonce] = true;
1080: executionHistory[fromChainId][nonce] = true;
1115: executionHistory[fromChainId][nonce] = true;
1138: executionHistory[fromChainId][nonce] = true;
1148: executionHistory[fromChainId][nonce] = true;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L916

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L941

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L966

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L916

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L941

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L966

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L916

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L941

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L966

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1014

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1050

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1085

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1025

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1061

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1096

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1014

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1050

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1085

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1025

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1061

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1096

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1014

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1050

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1085

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1025

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1061

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1096

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1120

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1143

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L919

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L944

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L969

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L993

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1017

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1053

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1088

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1123

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1120

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1143

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148



```solidity
File: RootBridgeAgent.sol

1198: _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1203: _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1198: _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
1203: _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1198

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1203

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1198

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1203



```solidity
File: RootBridgeAgentExecutor.sol

164: PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
174: PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
169: uint8 numOfAssets = uint8(bytes1(_data[PARAMS_START]));
174: > PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L164

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L174

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L169

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L174



```solidity
File: RootBridgeAgentExecutor.sol

271: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
280: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
285: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
288: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
271: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
280: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
285: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
288: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
288: + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE:

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L271

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L285

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L288

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L271

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L285

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L288

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L288



```solidity
File: RootPort.sol

492: if (isGlobalAddress[_ecoTokenGlobalAddress]) revert AlreadyAddedEcosystemToken();
498: isGlobalAddress[_ecoTokenGlobalAddress] = true;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L492

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L498



```solidity
File: UniswapV3Staker.sol

263: reward = rewards[msg.sender];
278: reward = rewards[msg.sender];
266: rewards[msg.sender] -= reward;
268: rewards[msg.sender] = 0;
279: rewards[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L263

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L278

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L266

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L268

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L279



```solidity
File: UniswapV3Staker.sol

263: reward = rewards[msg.sender];
278: reward = rewards[msg.sender];
268: rewards[msg.sender] = 0;
279: rewards[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L263

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L278

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L268

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L279



```solidity
File: UniswapV3Staker.sol

402: if (hermesGaugeBoost.isUserGauge(owner, address(gauge)) && _userAttachements[owner][key.pool] == tokenId) {
406: _userAttachements[owner][key.pool] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L402

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L406



```solidity
File: UniswapV3Staker.sol

483: if (incentives[incentiveId].totalRewardUnclaimed == 0) revert NonExistentIncentiveError();
491: address tokenOwner = deposits[tokenId].owner;
501: deposits[tokenId].stakedTimestamp = uint40(block.timestamp);
502: incentives[incentiveId].numberOfStakes++;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L483

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L491

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L501

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L502



```solidity
File: UniswapV3Staker.sol

531: if (address(gauges[uniswapV3Pool]) != uniswapV3Gauge) {
534: gauges[uniswapV3Pool] = UniswapV3Gauge(uniswapV3Gauge);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L531

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L534



```solidity
File: UniswapV3Staker.sol

545: if (newDepot != bribeDepots[uniswapV3Pool]) {
546: bribeDepots[uniswapV3Pool] = newDepot;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L545-L546

```solidity
File: UniswapV3Staker.sol

555: if (minimumWidth != poolsMinimumWidth[uniswapV3Pool]) {
556: poolsMinimumWidth[uniswapV3Pool] = minimumWidth;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L555-L556



</details>



### <a href="#gas-summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.




### <a href="#gas-summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

#### <ins>Proof Of Concept</ins>


```solidity
File: ERC20MultiVotes.sol

360: bytes32 public constant DELEGATION_TYPEHASH =

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L360

```solidity
File: GovernorBravoDelegateMaia.sol

9: string public constant name = "vMaia Governor Bravo";
12: uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken
15: uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken
18: uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks
21: uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks
24: uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks
27: uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks
30: uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken
33: uint256 public constant proposalMaxOperations = 10; // 10 actions
36: uint256 public constant DIVISIONER = 1 ether;
42: bytes32 public constant DOMAIN_TYPEHASH =
46: bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L9

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L12

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L15

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L18

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L21

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L24

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L27

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L30

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L33

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L36

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L42

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L46



```solidity
File: AnycallFlags.sol

8: uint256 public constant FLAG_NONE = 0x0;
9: uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;
10: uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;
11: uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;
12: uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;
15: uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;
16: uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L8

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L9

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L10

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L11

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L12

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L15

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L16





#### <ins>Recommended Mitigation Steps</ins>

Set variable to private.



### <a href="#gas-summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```solidity
File: FlywheelGaugeRewards.sol

183: assert(queuedRewards.storedCycle == 0 || queuedRewards.storedCycle >= lastCycle);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L183

```solidity
File: FlywheelGaugeRewards.sol

215: assert(queuedRewards.storedCycle >= cycle);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L215

```solidity
File: RewardMath.sol

65: assert(currentTime >= startTime);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/RewardMath.sol#L65






### <a href="#gas-summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> The result of a function call should be cached rather than re-calling the function

External calls are expensive. Results of external function calls should be cached rather than call them multiple times. Consider caching the following:

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20MultiVotes.sol

251: ckpts[pos - 1].votes = newWeight.toUint224();
253: ckpts.push(Checkpoint({fromBlock: block.number.toUint32(), votes: newWeight.toUint224()}));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L251

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L253



```solidity
File: ERC4626.sol

103: function totalAssets() public view virtual returns (uint256);
109: return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L103

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L109



```solidity
File: ERC4626DepositOnly.sol

65: function totalAssets() public view virtual returns (uint256);
71: return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L65

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L71



```solidity
File: BaseV2GaugeFactory.sol

73: function newEpoch() external {
78: if (activeGauges[_gauges[i]]) _gauges[i].newEpoch();
94: if (activeGauges[_gauges[i]]) _gauges[i].newEpoch();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L73

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L78

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L94



```solidity
File: BaseV2GaugeManager.sol

60: function newEpoch() external {
65: if (activeGaugeFactories[_gaugeFactories[i]]) _gaugeFactories[i].newEpoch();
81: if (activeGaugeFactories[_gaugeFactories[i]]) _gaugeFactories[i].newEpoch();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L60

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L65

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L81



```solidity
File: GovernorBravoDelegateMaia.sol

238: (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
244: (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
244: (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L238

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L244

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L244



```solidity
File: GovernorBravoDelegator.sol

62: if eq(success, 0) { revert(add(returnData, 0x20), returndatasize()) }
77: returndatacopy(free_mem_ptr, 0, returndatasize())
80: case 0 { revert(free_mem_ptr, returndatasize()) }
81: default { return(free_mem_ptr, returndatasize()) }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L62

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L77

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L80

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L81



```solidity
File: GovernorBravoDelegator.sol

61: if eq(success, 0) { revert(add(returnData, 0x20), returndatasize()) }
76: returndatacopy(free_mem_ptr, 0, returndatasize())
79: case 0 { revert(free_mem_ptr, returndatasize()) }
80: default { return(free_mem_ptr, returndatasize()) }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L61

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L76

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L79

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L80



```solidity
File: UlyssesPool.sol

192: oldBandwidth.mulDivUp(oldTotalWeights, newTotalWeights).toUint248();
258: oldBandwidth.mulDivUp(oldTotalWeights, newTotalWeights).toUint248();
273: oldBandwidth.mulDivUp(oldTotalWeights, newTotalWeights).toUint248();
269: poolState.bandwidth += leftOverBandwidth.toUint248();
273: poolState.bandwidth = oldBandwidth.mulDivUp(oldTotalWeights, newTotalWeights).toUint248();
281: bandwidthStateList[i].bandwidth += leftOverBandwidth.toUint248();
284: leftOverBandwidth.mulDiv(bandwidthStateList[i].weight, weightsWithoutPool).toUint248();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L192

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L258

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L269

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L281

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L284



```solidity
File: UlyssesRouter.sol

74: address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount);
92: address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesRouter.sol#L74

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesRouter.sol#L92



```solidity
File: BranchBridgeAgent.sol

227: bytes1(0x04), msg.sender, depositNonce, _params, msg.value.toUint128(), _remoteExecutionGas
234: _noDepositCall(msg.sender, packedData, msg.value.toUint128());

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L227

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L234



```solidity
File: BranchBridgeAgent.sol

188: msg.value.toUint128(),
202: msg.value.toUint128(),
215: msg.value.toUint128(),
227: msg.value.toUint128(),
255: msg.value.toUint128(),
299: msg.value.toUint128(),
346: msg.value.toUint128(),
361: msg.value.toUint128(),
381: msg.value.toUint128(),
395: msg.value.toUint128(),
426: msg.value.toUint128(),
435: msg.value.toUint128(),
471: msg.value.toUint128(),
496: msg.value.toUint128(),
518: msg.value.toUint128(),
540: msg.value.toUint128(),
188: msg.value.toUint128()
202: msg.value.toUint128()
215: msg.value.toUint128()
227: msg.value.toUint128()
234: msg.value.toUint128()
255: msg.value.toUint128()
270: msg.value.toUint128()
299: msg.value.toUint128()
314: msg.value.toUint128()
346: msg.value.toUint128()
361: msg.value.toUint128()
381: msg.value.toUint128()
395: msg.value.toUint128()
405: msg.value.toUint128()
411: msg.value.toUint128()
426: msg.value.toUint128()
435: msg.value.toUint128()
443: msg.value.toUint128()
471: msg.value.toUint128()
496: msg.value.toUint128()
518: msg.value.toUint128()
540: msg.value.toUint128()

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L188

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L202

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L215

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L227

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L255

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L299

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L346

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L361

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L381

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L395

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L426

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L435

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L471

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L496

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L518

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L540

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L188

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L202

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L215

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L227

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L234

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L255

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L270

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L299

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L314

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L346

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L361

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L381

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L395

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L405

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L411

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L426

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L435

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L443

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L471

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L496

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L518

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L540



```solidity
File: BranchBridgeAgent.sol

342: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
188: msg.value.toUint128(),
202: msg.value.toUint128(),
215: msg.value.toUint128(),
227: msg.value.toUint128(),
255: msg.value.toUint128(),
299: msg.value.toUint128(),
346: msg.value.toUint128(),
361: msg.value.toUint128(),
381: msg.value.toUint128(),
395: msg.value.toUint128(),
426: msg.value.toUint128(),
435: msg.value.toUint128(),
471: msg.value.toUint128(),
496: msg.value.toUint128(),
518: msg.value.toUint128(),
540: msg.value.toUint128(),
342: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
357: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
188: msg.value.toUint128(),
202: msg.value.toUint128(),
215: msg.value.toUint128(),
227: msg.value.toUint128(),
255: msg.value.toUint128(),
299: msg.value.toUint128(),
346: msg.value.toUint128(),
361: msg.value.toUint128(),
381: msg.value.toUint128(),
395: msg.value.toUint128(),
426: msg.value.toUint128(),
435: msg.value.toUint128(),
471: msg.value.toUint128(),
496: msg.value.toUint128(),
518: msg.value.toUint128(),
540: msg.value.toUint128(),
188: msg.value.toUint128(),
202: msg.value.toUint128(),
215: msg.value.toUint128(),
227: msg.value.toUint128(),
255: msg.value.toUint128(),
299: msg.value.toUint128(),
346: msg.value.toUint128(),
361: msg.value.toUint128(),
381: msg.value.toUint128(),
395: msg.value.toUint128(),
426: msg.value.toUint128(),
435: msg.value.toUint128(),
471: msg.value.toUint128(),
496: msg.value.toUint128(),
518: msg.value.toUint128(),
540: msg.value.toUint128(),
188: msg.value.toUint128(),
202: msg.value.toUint128(),
215: msg.value.toUint128(),
227: msg.value.toUint128(),
255: msg.value.toUint128(),
299: msg.value.toUint128(),
346: msg.value.toUint128(),
361: msg.value.toUint128(),
381: msg.value.toUint128(),
395: msg.value.toUint128(),
426: msg.value.toUint128(),
435: msg.value.toUint128(),
471: msg.value.toUint128(),
496: msg.value.toUint128(),
518: msg.value.toUint128(),
540: msg.value.toUint128(),
405: _depositGas(msg.value.toUint128());
411: getDeposit[_depositNonce].depositedGas = msg.value.toUint128();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L342-L411

```solidity
File: BranchBridgeAgent.sol

1029: uint256 gasLeft = gasleft();
1063: uint256 gasLeft = gasleft();
1036: _forceRevert();
1051: _forceRevert();
1070: _forceRevert();
1149: _forceRevert();
1171: _forceRevert();
1195: _forceRevert();
1316: _forceRevert();
1047: uint256 gasAfterTransfer = gasleft();
1036: _forceRevert();
1051: _forceRevert();
1070: _forceRevert();
1149: _forceRevert();
1171: _forceRevert();
1195: _forceRevert();
1316: _forceRevert();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1029

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1063

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1036

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1051

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1070

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1171

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1195

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1316

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1047

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1036

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1051

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1070

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1171

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1195

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1316



```solidity
File: BranchBridgeAgent.sol

1036: _forceRevert();
1051: _forceRevert();
1070: _forceRevert();
1149: _forceRevert();
1171: _forceRevert();
1195: _forceRevert();
1316: _forceRevert();
1036: _forceRevert();
1051: _forceRevert();
1070: _forceRevert();
1149: _forceRevert();
1171: _forceRevert();
1195: _forceRevert();
1316: _forceRevert();
1036: _forceRevert();
1051: _forceRevert();
1070: _forceRevert();
1149: _forceRevert();
1171: _forceRevert();
1195: _forceRevert();
1316: _forceRevert();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1036

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1051

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1070

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1171

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1195

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1316

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1036

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1051

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1070

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1171

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1195

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1316

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1036

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1051

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1070

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1171

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1195

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1316



```solidity
File: MulticallRootRouter.sol

295: IVirtualAccount(userAccount).userAddress(),
320: IVirtualAccount(userAccount).userAddress(),
371: IVirtualAccount(userAccount).userAddress(),
396: IVirtualAccount(userAccount).userAddress(),
447: IVirtualAccount(userAccount).userAddress(),
472: IVirtualAccount(userAccount).userAddress(),
295: IVirtualAccount(userAccount).userAddress(),
320: IVirtualAccount(userAccount).userAddress(),
371: IVirtualAccount(userAccount).userAddress(),
396: IVirtualAccount(userAccount).userAddress(),
447: IVirtualAccount(userAccount).userAddress(),
472: IVirtualAccount(userAccount).userAddress(),

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L295

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L320

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L371

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L396

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L447

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L472

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L295

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L320

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L371

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L396

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L447

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L472



```solidity
File: RootBridgeAgent.sol

693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();
693: _forceRevert();
815: _forceRevert();
840: _forceRevert();
902: _forceRevert();
920: _forceRevert();
945: _forceRevert();
970: _forceRevert();
994: _forceRevert();
1018: _forceRevert();
1054: _forceRevert();
1089: _forceRevert();
1124: _forceRevert();
1153: _forceRevert();
1226: _forceRevert();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L693

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L815

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L840

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L902

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L920

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L945

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L970

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L994

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1018

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1054

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1089

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1124

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1226





</details>



### <a href="#gas-summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Shorten the array rather than copying to a new one

Inline-assembly can be used to shorten the array by changing the length slot, so that the entries don't have to be copied to a new, shorter array

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: PoolVariables.sol

209: uint32[] memory secondsAgo = new uint32[](2);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L209

```solidity
File: UlyssesPool.sol

377: uint256[] memory diffs = new uint256[](length);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L377

```solidity
File: UlyssesFactory.sol

144: address[] memory destinations = new address[](length);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L144

```solidity
File: BranchBridgeAgent.sol

281: uint256[] memory _deposits = new uint256[](_dParams.hTokens.length);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L281

```solidity
File: BranchBridgeAgent.sol

574: address[] memory _hTokens = new address[](numOfAssets);
575: address[] memory _tokens = new address[](numOfAssets);
576: uint256[] memory _amounts = new uint256[](numOfAssets);
577: uint256[] memory _deposits = new uint256[](numOfAssets);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L574-L577

```solidity
File: BranchBridgeAgent.sol

717: uint256[] memory deposits = new uint256[](_dParams.hTokens.length);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L717

```solidity
File: BranchBridgeAgent.sol

872: address[] memory hTokens = new address[](1);
874: address[] memory tokens = new address[](1);
876: uint256[] memory amounts = new uint256[](1);
878: uint256[] memory deposits = new uint256[](1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L872

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L874

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L876

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L878



```solidity
File: RootBridgeAgent.sol

340: address[] memory hTokens = new address[](_globalAddresses.length);
341: address[] memory tokens = new address[](_globalAddresses.length);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L340-L341

```solidity
File: RootBridgeAgent.sol

494: address[] memory hTokens = new address[](1);
496: address[] memory tokens = new address[](1);
498: uint256[] memory amounts = new uint256[](1);
500: uint256[] memory deposits = new uint256[](1);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L494

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L496

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L498

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L500



```solidity
File: RootBridgeAgentExecutor.sol

359: address[] memory hTokens = new address[](numOfAssets);
360: address[] memory tokens = new address[](numOfAssets);
361: uint256[] memory amounts = new uint256[](numOfAssets);
362: uint256[] memory deposits = new uint256[](numOfAssets);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L359-L362



</details>



### <a href="#gas-summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
	require(version == 1);
	require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```solidity
File: TalosBaseStrategy.sol

408: require(balance0 >= amount0 && balance1 >= amount1);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L408





### <a href="#gas-summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> State variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables can also be cheaper

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BaseV2Minter.sol

16: contract BaseV2Minter is Ownable, IBaseV2Minter {
    ...
    /// @inheritdoc IBaseV2Minter
    uint256 public override weekly;
    /// @inheritdoc IBaseV2Minter
    uint256 public override activePeriod;
```

Can save one storage slot by changing from `uint256` to `uint128`

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L47-L49


</details>




### <a href="#gas-summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20Boost.sol

100: values[i] = _userGauges[user].at(offset + i);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L100

```solidity
File: ERC20Boost.sol

153: address[] memory gaugeList = _userGauges[user].values();
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L153

```solidity
File: ERC20Boost.sol

160: uint256 gaugeBoost = getUserGaugeBoost[user][gauge].userGaugeBoost;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L160

```solidity
File: ERC20Gauges.sol

157: values[i] = _userGauges[user].at(offset + i);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L157

```solidity
File: ERC20Gauges.sol

532: address[] memory gaugeList = _userGauges[user].values();
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L532

```solidity
File: ERC20Gauges.sol

538: uint112 userGaugeWeight = getUserGaugeWeight[user][gauge];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L538

```solidity
File: ERC20Gauges.sol

553: getUserWeight[user] -= userFreed;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L553

```solidity
File: ERC20MultiVotes.sol

324: address[] memory delegateList = _delegates[user].values();
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L324

```solidity
File: ERC20MultiVotes.sol

330: uint256 delegateVotes = _delegatesVotesCount[user][delegatee];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L330

```solidity
File: ERC20MultiVotes.sol

339: require(_delegates[user].remove(delegatee));
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L339

```solidity
File: ERC20MultiVotes.sol

340: _delegatesVotesCount[user][delegatee] = 0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L340

```solidity
File: ERC20MultiVotes.sol

343: _delegatesVotesCount[user][delegatee] -= votesToFree;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L343

```solidity
File: ERC20MultiVotes.sol

353: userDelegatedVotes[user] -= totalFreed;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L353

```solidity
File: ERC4626MultiToken.sol

163: uint256 allowed = allowance[owner][msg.sender];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L163

```solidity
File: ERC4626MultiToken.sol

165: if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L165

```solidity
File: GovernorBravoDelegateMaia.sol

179: Proposal storage proposal = proposals[proposalId];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L179

```solidity
File: GovernorBravoDelegateMaia.sol

213: Proposal storage proposal = proposals[proposalId];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L213

```solidity
File: GovernorBravoDelegateMaia.sol

230: Proposal storage proposal = proposals[proposalId];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L230

```solidity
File: FlywheelGaugeRewards.sol

179: QueuedRewards memory queuedRewards = gaugeQueuedRewards[gauge];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L179

```solidity
File: UlyssesPool.sol

226: uint256 poolIndex = destinations[poolId];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L226

```solidity
File: UlyssesPool.sol

239: uint256 weightsWithoutPool = oldTotalWeights - bandwidthStateList[poolIndex].weight;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L239

```solidity
File: UlyssesPool.sol

249: BandwidthState storage poolState = bandwidthStateList[poolIndex];
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L249

```solidity
File: UlyssesRouter.sol

92: address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesRouter.sol#L92



</details>










### <a href="#gas-summary">[GAS&#x2011;17]</a><a name="GAS&#x2011;17"> Use v4.9.0 OpenZeppelin contracts

The upcoming v4.9.0 version of OpenZeppelin provides many small gas optimizations.
https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0
#### <ins>Proof Of Concept</ins>

```solidity
File: package.json

    "@openzeppelin/contracts": "4.6.0"
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/../lib/v3-periphery/package.json#L32



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.9.0







### <a href="#gas-summary">[GAS&#x2011;18]</a><a name="GAS&#x2011;18"> Use Short Circuiting rules to your advantage

The operators `||` and `&&` apply the common short-circuiting rules. This means that in the expression `f(x) || g(y)`, if `f(x)` evaluates to true, `g(y) `will not be evaluated even if it may have side-effects.
So setting less costly function to `f(x)` and setting costly function to `g(x)` is efficient.

i.e. 
```solidity
 if (!varA && msg.sender != varB);
```

Can be changed to:
```solidity
 if (msg.sender != varB && !varA);
```

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

234: (msg.sender != proposal.proposer && msg.sender != admin)

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L234





