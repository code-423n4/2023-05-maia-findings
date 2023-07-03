## QA Summary<a name="QA Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#low1-add-to-blacklist-function) | Add to `blacklist` function | 53 |
| [LOW&#x2011;2](#low2-ierc20-approve-is-deprecated) | IERC20 `approve()` Is Deprecated | 2 |
| [LOW&#x2011;3](#low3-consider-the-case-where-totalsupply-is-0) | Consider the case where `totalsupply` is 0 | 9 |
| [LOW&#x2011;4](#low4-contract-will-stop-functioning-in-the-year-2106) | Contract will stop functioning in the year 2106 | 1 |
| [LOW&#x2011;5](#low5-decimals-not-part-of-erc20-standard) | `decimals()` not part of ERC20 standard | 25 |
| [LOW&#x2011;6](#low6-external-call-recipient-may-consume-all-transaction-gas) | External call recipient may consume all transaction gas | 82 |
| [LOW&#x2011;7](#low7-init-functions-are-susceptible-to-frontrunning) | Init functions are susceptible to front-running | 3 |
| [LOW&#x2011;8](#low8-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug) | Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug | 6 |
| [LOW&#x2011;9](#low9-minting-tokens-to-the-zero-address-should-be-avoided) | Minting tokens to the zero address should be avoided | 8 |
| [LOW&#x2011;10](#low10-the-contract-should-approve0-first) | The Contract Should `approve(0)` First | 2 |
| [LOW&#x2011;11](#low11-missing-contractexistence-checks-before-lowlevel-calls) | Missing Contract-existence Checks Before Low-level Calls | 30 |
| [LOW&#x2011;12](#low12-missing-reentrancy-guard-to-withdraw-function) | Missing ReEntrancy Guard to `withdraw` function | 1 |
| [LOW&#x2011;13](#low13-contracts-are-not-using-their-oz-upgradeable-counterparts) | Contracts are not using their OZ Upgradeable counterparts | 8 |
| [LOW&#x2011;14](#low14-solidity-version-0820-may-not-work-on-other-chains-due-to-push0) | Solidity version 0.8.20 may not work on other chains due to `PUSH0` | 145 |
| [LOW&#x2011;15](#low15-storage-write-removal-bug-on-conditional-early-termination) | Storage Write Removal Bug On Conditional Early Termination | 1 |
| [LOW&#x2011;16](#low16-upgrade-openzeppelin-contract-dependency) | Upgrade OpenZeppelin Contract Dependency | 1 |

Total: 377 contexts over 16 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#nc1-avoid-the-use-of-sensitive-terms) | Avoid the use of sensitive terms | 61 |
| [NC&#x2011;2](#nc2-no-need-for--true-or--false-checks) | No need for `== true` or `== false` checks | 1 |
| [NC&#x2011;3](#nc3-compliance-with-solidity-style-rules-in-constant-expressions) | Compliance with Solidity Style rules in Constant expressions | 5 |
| [NC&#x2011;4](#nc4-consider-adding-a-denylist) | Consider adding a deny-list | 9 |
| [NC&#x2011;5](#nc5-consistent-usage-of-require-vs-custom-error) | Consistent usage of require vs custom error | 263 |
| [NC&#x2011;6](#nc6-duplicated-requirerevert-checks-should-be-refactored-to-a-modifier-or-function) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 15 |
| [NC&#x2011;7](#nc7-events-are-missing-sender-information) | Events are missing sender information | 22 |
| [NC&#x2011;8](#nc8-use-delete-to-clear-variables) | Use `delete` to Clear Variables | 25 |
| [NC&#x2011;9](#nc9-immutables-can-be-named-using-the-same-convention) | Immutables can be named using the same convention | 44 |
| [NC&#x2011;10](#nc10-no-need-to-initialize-uints-to-zero) | No need to initialize uints to zero | 3 |
| [NC&#x2011;11](#nc11-memorysafe-annotation-preferred-over-comment-variant) | Memory-safe annotation preferred over comment variant | 554 |
| [NC&#x2011;12](#nc12-missing-event-for-critical-parameter-change) | Missing event for critical parameter change | 15 |
| [NC&#x2011;13](#nc13-nonassembly-method-available) | Non-assembly Method Available | 1 |
| [NC&#x2011;14](#nc14-nonexternalpublic-function-names-should-begin-with-an-underscore) | Non-`external`/`public` function names should begin with an underscore | 64 |
| [NC&#x2011;15](#nc15-omissions-in-events) | Omissions in Events | 4 |
| [NC&#x2011;16](#nc16-operations-such-as-the-changing-of-the-owner-should-be-behind-a-timelock) | Operations such as the changing of the owner should be behind a timelock | 3 |
| [NC&#x2011;17](#nc17-using--without-specifying-an-upper-bound-is-unsafe) | Using `>`/`>=` without specifying an upper bound is unsafe | 11 |
| [NC&#x2011;18](#nc18-private-variables-dont-respect-naming-convention) | Private variables dont respect naming convention | 5 |
| [NC&#x2011;19](#nc19-public-functions-not-called-by-the-contract-should-be-declared-external-instead) | Public Functions Not Called By The Contract Should Be Declared External Instead | 15 |
| [NC&#x2011;20](#nc20-empty-blocks-should-be-removed-or-emit-something) | Empty blocks should be removed or emit something | 24 |
| [NC&#x2011;21](#nc21-redundant-cast) | Redundant Cast | 4 |
| [NC&#x2011;22](#nc22-remove-unused-contract-variables) | Remove unused contract variables | 7 |
| [NC&#x2011;23](#nc23-remove-unused-error-definition) | Remove unused `error` definition | 7 |
| [NC&#x2011;24](#nc24-using-underscore-at-the-end-of-variable-name) | Using underscore at the end of variable name | 7 |
| [NC&#x2011;25](#nc25-use-bytesconcat) | Use `bytes.concat()` | 31 |
| [NC&#x2011;26](#nc26-use--instead-of-0x00-for-empty-bytes-to-avoid-triggering-extra-computation-on-transfers) | Use `` instead of `"0x00"` for empty bytes to avoid triggering extra computation on transfers | 9 |
| [NC&#x2011;27](#nc27-use-inheritdoc-rather-than-using-a-nonstandard-annotation) | Use @inheritdoc rather than using a non-standard annotation | 34 |
| [NC&#x2011;28](#nc28-use-named-function-calls) | Use named function calls | 1 |
| [NC&#x2011;29](#nc29-cast-to-bytes-or-bytes32-for-clearer-semantic-meaning) | Cast to `bytes` or `bytes32` for clearer semantic meaning | 13 |

Total: 1257 contexts over 29 issues

## Low Risk Issues

### <a href="#qa-summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Add to `blacklist` function

As stated in the project:
```js
- Is it an NFT?: true
```

NFT thefts have increased recently, so with the addition of hacked NFTs to the platform, NFTs can be converted into liquidity. To prevent this, I recommend adding the blacklist function.

Marketplaces such as Opensea have a blacklist feature that will not list NFTs that have been reported theft, NFT projects such as Manifold have blacklist functions in their smart contracts.

Here is the project example; Manifold

Manifold Contract https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```solidity
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```



#### <ins>Recommended Mitigation Steps</ins>
Add to Blacklist function and modifier.



### <a href="#qa-summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> IERC20 `approve()` Is Deprecated

`approve()` is subject to a known front-running attack. It is deprecated in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead.

https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#IERC20-approve-address-uint256-

#### <ins>Proof Of Concept</ins>

```solidity
File: MulticallRootRouter.sol

120: ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L120

```solidity
File: MulticallRootRouter.sol

148: ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L148



#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeIncreaseAllowance()` / `safeDecreaseAllowance()` instead.






### <a href="#qa-summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Consider the case where `totalsupply` is 0

Consider the case where `totalsupply` is 0. When `totalsupply` is 0, it should return 0 directly, because there will be an error of dividing by 0.

#### <ins>Impact</ins>

This would cause the affected functions to revert and as a result can lead to potential loss.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BaseV2Minter.sol

119: return (vault.totalAssets() * _minted) / HERMES(underlying).totalSupply();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L119

```solidity
File: ERC4626PartnerManager.sol

162: return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
168: return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L162

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L168



```solidity
File: ERC4626PartnerManager.sol

219: if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L219

```solidity
File: FlywheelCore.sol

171: deltaIndex = ((strategyRewardsAccrued * ONE) / supplyTokens).toUint224();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L171

```solidity
File: TalosBaseStrategy.sol

261: uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L261

```solidity
File: RewardMath.sol

35: ((secondsInsideX128 * 4) / 10) + ((((stakedDuration << 128) * boostAmount) / boostTotalSupply) * 6) / 10

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/RewardMath.sol#L35



</details>


#### <ins>Recommended Mitigation Steps</ins>
Add check for zero value and return 0.

```solidity
if ( totalSupply() == 0) return 0;
```



### <a href="#qa-summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Contract will stop functioning in the year 2106

Limiting the timestamp variable to fit in a `uint32` will cause the call to start reverting in year 2106 for the following calls.

#### <ins>Proof Of Concept</ins>


```solidity
File: FlywheelAcummulatedRewards.sol

43: uint32 timestamp = block.timestamp.toUint32();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L43










### <a href="#qa-summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> `decimals()` not part of ERC20 standard

`decimals()` is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC4626.sol

23: constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L23

```solidity
File: ERC4626DepositOnly.sol

23: constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L23

```solidity
File: ERC4626MultiToken.sol

51: require(ERC20(_assets[i]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L51

```solidity
File: UlyssesERC4626.sol

27: if (ERC20(_asset).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L27

```solidity
File: UlyssesToken.sol

46: require(ERC20(asset).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L46

```solidity
File: ArbitrumBranchBridgeAgent.sol

104: msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L104

```solidity
File: ArbitrumBranchPort.sol

72: underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L72

```solidity
File: ArbitrumBranchPort.sol

82: _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L82

```solidity
File: ArbitrumBranchPort.sol

120: _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L120

```solidity
File: ArbitrumBranchPort.sol

141: _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L141

```solidity
File: BranchBridgeAgent.sol

252: _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L252

```solidity
File: BranchBridgeAgent.sol

687: _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L687

```solidity
File: BranchBridgeAgent.sol

284: _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L284

```solidity
File: BranchBridgeAgent.sol

342: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L342

```solidity
File: BranchBridgeAgent.sol

357: getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L357

```solidity
File: BranchBridgeAgent.sol

720: deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L720

```solidity
File: BranchBridgeAgent.sol

1355: deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1355

```solidity
File: BranchPort.sol

212: _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L212

```solidity
File: BranchPort.sol

254: _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L254

```solidity
File: BranchPort.sol

272: _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L272



</details>




### <a href="#qa-summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> External call recipient may consume all transaction gas

There is no limit specified on the amount of gas used, so the recipient can use up all of the transaction's gas, causing it to revert. Use `addr.call{gas: <amount>}("")` or [this](https://github.com/nomad-xyz/ExcessivelySafeCall) library instead.

#### <ins>Proof Of Concept</ins>

```solidity
File: MulticallRootRouter.sol

281: IVirtualAccount(userAccount).call(calls);
289: IVirtualAccount(userAccount).call(calls);
309: IVirtualAccount(userAccount).call(calls);
357: IVirtualAccount(userAccount).call(calls);
365: IVirtualAccount(userAccount).call(calls);
385: IVirtualAccount(userAccount).call(calls);
433: IVirtualAccount(userAccount).call(calls);
441: IVirtualAccount(userAccount).call(calls);
461: IVirtualAccount(userAccount).call(calls);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L281

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L289

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L357

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L365

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L385

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L433

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L441

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L461



```solidity
File: VirtualAccount.sol

49: (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/VirtualAccount.sol#L49




### <a href="#qa-summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Init functions are susceptible to front-running

The `initialize()` functions below are not called by another contract atomically after the contract is deployed, so it's possible for a malicious user to call `initialize()` which, if it's noticed in time, would require the project to re-deploy the contract in order to properly initialize. Consider creating a factory contract, which will `new` and `initialize()` each contract atomically.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: BaseV2Minter.sol

function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L78

```solidity
File: TalosBaseStrategy.sol

function init(uint256 amount0Desired, uint256 amount1Desired, address receiver)
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L102


```solidity
File: RootPort.sol

function initializeCore(
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L140



</details>






### <a href="#qa-summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Low Level Calls With Solidity Version Before 0.8.14 Can Result In Optimiser Bug

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

#### <ins>Proof Of Concept</ins>

POC can be found in the above medium reference url.

Functions that execute low level calls in contracts with solidity version under 0.8.14

<details>

```solidity
File: UlyssesPool.sol

356: assembly {
            // Revert if the list is empty
            if eq(length, 1) {
                // Store the function selector of `NotInitialized()`.
                mstore(0x00, 0x87138d5c)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }

            // Revert if the amount is too small
            if lt(amount, MIN_SWAP_AMOUNT) {
                // Store the function selector of `AmountTooSmall()`.
                mstore(0x00, 0xc2f5625a)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }
        }
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L356

```solidity
File: UlyssesPool.sol

553: assembly {
            // Load bandwidth and weight from storage
            let slot := sload(destinationState.slot)
            // Bandwidth is the first 248 bits of the slot
            bandwidth := and(slot, 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff)
            // Weight is the last 8 bits of the slot
            weight := shr(248, slot)

            // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
            if mul(weight, gt(_totalSupply, div(not(0), weight))) {
                // Store the function selector of `MulDivFailed()`.
                mstore(0x00, 0xad251c27)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }

            // Get the target bandwidth
            targetBandwidth := div(mul(_totalSupply, weight), _totalWeights)
        }
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L553

```solidity
File: UlyssesPool.sol

698: assembly {
            // Load the rebalancing fee slot to get the fee parameters
            let feeSlot := sload(fees.slot)
            // Get sigma2 from the first 8 bytes of the fee slot
            let sigma2 := shr(192, feeSlot)
            // Get sigma1 from the next 8 bytes of the fee slot
            let sigma1 := and(shr(128, feeSlot), 0xffffffffffffffff)
            // Get lambda2 from the next 8 bytes of the fee slot
            lambda2 := and(shr(64, feeSlot), 0xffffffffffffffff)
            // Get lambda1 from the last 8 bytes of the fee slot
            lambda1 := and(feeSlot, 0xffffffffffffffff)

            // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
            if mul(sigma1, gt(targetBandwidth, div(not(0), sigma1))) {
                // Store the function selector of `MulDivFailed()`.
                mstore(0x00, 0xad251c27)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }

            // Calculate the upper bound for the first fee
            upperBound1 := div(mul(targetBandwidth, sigma1), DIVISIONER)

            // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
            if mul(sigma2, gt(targetBandwidth, div(not(0), sigma2))) {
                // Store the function selector of `MulDivFailed()`.
                mstore(0x00, 0xad251c27)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }

            // Calculate the upper bound for the second fee
            upperBound2 := div(mul(targetBandwidth, sigma2), DIVISIONER)
        }
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L698

```solidity
File: UlyssesPool.sol

851: assembly {
            // Calculate the height of the trapezium
            // The height is calculated as `upperBound - bandwidth`
            let height := sub(upperBound, bandwidth)

            // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
            if mul(feeTier, gt(height, div(not(0), feeTier))) {
                // Store the function selector of `MulDivFailed()`.
                mstore(0x00, 0xad251c27)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }

            // Calculate the width of the trapezium, rounded up
            // The width is calculated as `feeTier * height / maxWidth + offset`
            let width :=
                add(add(iszero(iszero(mod(mul(height, feeTier), maxWidth))), div(mul(height, feeTier), maxWidth)), offset)

            // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
            if mul(height, gt(width, div(not(0), height))) {
                // Store the function selector of `MulDivFailed()`.
                mstore(0x00, 0xad251c27)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }

            // Calculate the fee for this tier
            switch roundDown
            // If round down then do mulDiv(transfered, diff, totalDiff)
            case true { fee := div(mul(width, height), DIVISIONER) }
            // If round up then do mulDivUp(transfered, diff, totalDiff)
            default {
                fee := add(iszero(iszero(mod(mul(width, height), DIVISIONER))), div(mul(width, height), DIVISIONER))
            }
        }
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L851


```solidity
File: UlyssesPool.sol

1098: assembly {
            // Revert if poolId is invalid
            if iszero(index) {
                // Store the function selector of `NotUlyssesLP()`.
                mstore(0x00, 0x3c930918)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }
        }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L1098

```solidity
File: UlyssesPool.sol

1152: assembly {
            // Revert if msg.sender is invalid
            if iszero(index) {
                // Store the function selector of `NotUlyssesLP()`.
                mstore(0x00, 0x3c930918)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }
            // Revert if the amount is zero
            if iszero(assets) {
                // Store the function selector of `AmountTooSmall()`.
                mstore(0x00, 0xc2f5625a)
                // Revert with (offset, size).
                revert(0x1c, 0x04)
            }
        }
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L1152



</details>



#### <ins>Recommended Mitigation Steps</ins>

Consider upgrading to at least solidity v0.8.15.



### <a href="#qa-summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Minting tokens to the zero address should be avoided

The core function `mint` is used by users to mint an option position by providing token1 as collateral and borrowing the max amount of liquidity. `Address(0)` check is missing in both this function and the internal function `_mint`, which is triggered to mint the tokens to the to address. Consider applying a check in the function to ensure tokens aren't minted to the zero address.


#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: bHermesBoost.sol


function mint(address to, uint256 amount) external onlybHermes {
        _mint(to, amount);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesBoost.sol#L28

```solidity
File: bHermesGauges.sol


function mint(address to, uint256 amount) external onlybHermes {
        _mint(to, amount);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesGauges.sol#L35

```solidity
File: bHermesVotes.sol


function mint(address to, uint256 amount) external onlybHermes {
        _mint(to, amount);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesVotes.sol#L26

```solidity
File: HERMES.sol


function mint(address account, uint256 amount) external onlyOwner {
        _mint(account, amount);
    }
}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/HERMES.sol#L62

```solidity
File: Maia.sol


function mint(address account, uint256 amount) external onlyOwner {
        _mint(account, amount);
    }
}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/Maia.sol#L55

```solidity
File: RootPort.sol


function mintToLocalBranch(address _to, address _hToken, uint256 _amount) external requiresLocalBranchPort {
        mint(_to, _hToken, _amount, localChainId);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L324

```solidity
File: ERC20hTokenBranch.sol


function mint(address account, uint256 amount) external override onlyOwner returns (bool) {
        _mint(account, amount);
        return true;
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L23

```solidity
File: ERC20hTokenRoot.sol


function mint(address to, uint256 amount, uint256 chainId) external requiresPort returns (bool) {
        getTokenBalance[chainId] += amount;
        _mint(to, amount);
        return true;
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L72



</details>





### <a href="#qa-summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> The Contract Should `approve(0)` First

Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

#### <ins>Proof Of Concept</ins>


```solidity
File: MulticallRootRouter.sol

120: ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L120

```solidity
File: MulticallRootRouter.sol

148: ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L148



#### <ins>Recommended Mitigation Steps</ins>

Approve with a zero amount first before setting the actual amount.




### <a href="#qa-summary">[LOW&#x2011;12]</a><a name="LOW&#x2011;12"> Missing ReEntrancy Guard to `withdraw` function

The following contracts have no Re-Entrancy protection in their withdrawal function

If the mint was initiated by a contract, then the contract is checked for its ability to receive ERC721 tokens. Without reentrancy guard, `onERC721Received` will allow an attacker controlled contract to call the `mint` again, which may not be desirable to some parties, like allowing minting more than allowed. 

Reference: https://www.paradigm.xyz/2021/08/the-dangers-of-surprising-code

#### <ins>Proof Of Concept</ins>

If withdraw is `msg.sender` contract, it can do re-entrancy by overriding onERC721Received function, it doesn't seem to be a serious problem since it conforms to check-effect-interaction pattern, but this is a clear re-entry due to access to other functions and pre-emit processing. is the entracy

```solidity
reentrancy.sol:
 function onERC721Received(
    address,
    address,
    uint256,
    bytes memory
  ) public virtual override returns (bytes4) {
    //...do something
    }
    return this.onERC721Received.selector;
  }
```

Affected lines: 

```solidity
File: VirtualAccount.sol

36: function withdrawERC721(address _token, uint256 _tokenId) external requiresApprovedCaller {
        ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/VirtualAccount.sol#L36




#### <ins>Recommended Mitigation Steps</ins>

Use Openzeppelin or Solmate Re-Entrancy pattern Here is a example of a re-entrancy guard

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract ReEntrancyGuard {
    bool internal locked;

    modifier noReentrant() {
        require(!locked, "No re-entrancy");
        locked = true;
        _;
        locked = false;
    }
}
```






### <a href="#qa-summary">[LOW&#x2011;13]</a><a name="LOW&#x2011;13"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

<details>

```solidity
File: TalosBaseStrategy.sol

13: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L13

```solidity
File: BoostAggregator.sol

9: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L9

```solidity
File: IBoostAggregator.sol

6: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L6

```solidity
File: ITalosBaseStrategy.sol

7: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosBaseStrategy.sol#L7

```solidity
File: VirtualAccount.sol

9: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/VirtualAccount.sol#L9

```solidity
File: IVirtualAccount.sol

4: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L4

```solidity
File: UniswapV3Staker.sol

12: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L12

```solidity
File: IUniswapV3Staker.sol

5: import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L5



</details>

#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.
See https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/tree/master/contracts for list of available upgradeable contracts





### <a href="#qa-summary">[LOW&#x2011;14]</a><a name="LOW&#x2011;14"> Solidity version 0.8.20 may not work on other chains due to `PUSH0`

The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20Boost.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L3

```solidity
File: ERC20Gauges.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L3

```solidity
File: ERC20MultiVotes.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L4

```solidity
File: Errors.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/interfaces/Errors.sol#L2

```solidity
File: IERC20Boost.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/interfaces/IERC20Boost.sol#L2

```solidity
File: IERC20Gauges.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/interfaces/IERC20Gauges.sol#L3

```solidity
File: IERC20MultiVotes.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L3

```solidity
File: IERC4626DepositOnly.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L2

```solidity
File: IERC4626MultiToken.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L2

```solidity
File: IUlyssesERC4626.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L2

```solidity
File: BaseV2Gauge.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L2

```solidity
File: UniswapV3Gauge.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/UniswapV3Gauge.sol#L2

```solidity
File: BaseV2GaugeFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L3

```solidity
File: BaseV2GaugeManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeManager.sol#L3

```solidity
File: BribesFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BribesFactory.sol#L2

```solidity
File: UniswapV3GaugeFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L3

```solidity
File: IBaseV2Gauge.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IBaseV2Gauge.sol#L2

```solidity
File: IBaseV2GaugeFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IBaseV2GaugeFactory.sol#L3

```solidity
File: IBaseV2GaugeManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L3

```solidity
File: IBribesFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IBribesFactory.sol#L2

```solidity
File: IUniswapV3Gauge.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IUniswapV3Gauge.sol#L2

```solidity
File: IUniswapV3GaugeFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IUniswapV3GaugeFactory.sol#L3

```solidity
File: GovernorBravoDelegateMaia.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L2

```solidity
File: GovernorBravoDelegator.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L2

```solidity
File: GovernorBravoInterfaces.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L2

```solidity
File: bHermes.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/bHermes.sol#L2

```solidity
File: UtilityManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L3

```solidity
File: IBaseV2Minter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/interfaces/IBaseV2Minter.sol#L2

```solidity
File: IbHermesUnderlying.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/interfaces/IbHermesUnderlying.sol#L2

```solidity
File: IUtilityManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/interfaces/IUtilityManager.sol#L2

```solidity
File: BaseV2Minter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L2

```solidity
File: bHermesBoost.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesBoost.sol#L2

```solidity
File: bHermesGauges.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesGauges.sol#L2

```solidity
File: bHermesVotes.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesVotes.sol#L2

```solidity
File: HERMES.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/HERMES.sol#L2

```solidity
File: PartnerUtilityManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L2

```solidity
File: vMaia.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/vMaia.sol#L3

```solidity
File: PartnerManagerFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/factories/PartnerManagerFactory.sol#L2

```solidity
File: IBaseVault.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/interfaces/IBaseVault.sol#L3

```solidity
File: IERC4626PartnerManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/interfaces/IERC4626PartnerManager.sol#L2

```solidity
File: IPartnerManagerFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/interfaces/IPartnerManagerFactory.sol#L2

```solidity
File: IPartnerUtilityManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/interfaces/IPartnerUtilityManager.sol#L2

```solidity
File: DateTimeLib.sol

pragma solidity ^0.8.4;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/libraries/DateTimeLib.sol#L2

```solidity
File: ERC4626PartnerManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L2

```solidity
File: Maia.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/Maia.sol#L2

```solidity
File: GovernorBravoDelegator.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L2

```solidity
File: GovernorBravoInterfaces.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L2

```solidity
File: FlywheelCoreInstant.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreInstant.sol#L2

```solidity
File: FlywheelCoreStrategy.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreStrategy.sol#L2

```solidity
File: BaseFlywheelRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/BaseFlywheelRewards.sol#L3

```solidity
File: FlywheelCore.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L3

```solidity
File: FlywheelBoosterGaugeWeight.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3

```solidity
File: MultiRewardsDepot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/MultiRewardsDepot.sol#L2

```solidity
File: RewardsDepot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/RewardsDepot.sol#L2

```solidity
File: SingleRewardsDepot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/SingleRewardsDepot.sol#L2

```solidity
File: IFlywheelAcummulatedRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L3

```solidity
File: IFlywheelBooster.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelBooster.sol#L3

```solidity
File: IFlywheelBribeRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelBribeRewards.sol#L3

```solidity
File: IFlywheelCore.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelCore.sol#L3

```solidity
File: IFlywheelGaugeRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L3

```solidity
File: IFlywheelInstantRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelInstantRewards.sol#L3

```solidity
File: IFlywheelRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelRewards.sol#L3

```solidity
File: IMultiRewardsDepot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IMultiRewardsDepot.sol#L2

```solidity
File: IRewardsDepot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IRewardsDepot.sol#L2

```solidity
File: FlywheelAcummulatedRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L3

```solidity
File: FlywheelBribeRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelBribeRewards.sol#L3

```solidity
File: FlywheelGaugeRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L3

```solidity
File: FlywheelInstantRewards.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelInstantRewards.sol#L3

```solidity
File: TalosManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L2

```solidity
File: TalosOptimizer.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosOptimizer.sol#L3

```solidity
File: BoostAggregatorFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/BoostAggregatorFactory.sol#L2

```solidity
File: OptimizerFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/OptimizerFactory.sol#L2

```solidity
File: TalosBaseStrategyFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosBaseStrategyFactory.sol#L2

```solidity
File: TalosStrategyStakedFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyStakedFactory.sol#L2

```solidity
File: TalosStrategyVanillaFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L3

```solidity
File: AutomationCompatibleInterface.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/AutomationCompatibleInterface.sol#L2

```solidity
File: IBoostAggregatorFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregatorFactory.sol#L2

```solidity
File: IOptimizerFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IOptimizerFactory.sol#L2

```solidity
File: ITalosBaseStrategy.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosBaseStrategy.sol#L3

```solidity
File: ITalosBaseStrategyFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosBaseStrategyFactory.sol#L2

```solidity
File: ITalosManager.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosManager.sol#L2

```solidity
File: ITalosOptimizer.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosOptimizer.sol#L3

```solidity
File: ITalosStrategyStaked.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosStrategyStaked.sol#L2

```solidity
File: ITalosStrategyStakedFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/ITalosStrategyStakedFactory.sol#L2

```solidity
File: PoolActions.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolActions.sol#L3

```solidity
File: PoolVariables.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L3

```solidity
File: UlyssesPool.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L2

```solidity
File: UlyssesRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesRouter.sol#L2

```solidity
File: UlyssesToken.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L2

```solidity
File: UlyssesFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L2

```solidity
File: IUlyssesFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/interfaces/IUlyssesFactory.sol#L2

```solidity
File: IUlyssesPool.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L2

```solidity
File: IUlyssesRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/interfaces/IUlyssesRouter.sol#L2

```solidity
File: IUlyssesToken.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/interfaces/IUlyssesToken.sol#L2

```solidity
File: ArbitrumBranchBridgeAgent.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2

```solidity
File: ArbitrumBranchPort.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3

```solidity
File: ArbitrumCoreBranchRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2

```solidity
File: BaseBranchRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BaseBranchRouter.sol#L2

```solidity
File: BranchBridgeAgent.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L2

```solidity
File: BranchBridgeAgentExecutor.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2

```solidity
File: BranchPort.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L3

```solidity
File: CoreBranchRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L2

```solidity
File: CoreRootRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L2

```solidity
File: MulticallRootRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L2

```solidity
File: RootBridgeAgent.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L2

```solidity
File: RootBridgeAgentExecutor.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2

```solidity
File: RootPort.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L3

```solidity
File: VirtualAccount.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/VirtualAccount.sol#L3

```solidity
File: ArbitrumBranchBridgeAgentFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2

```solidity
File: BranchBridgeAgentFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2

```solidity
File: ERC20hTokenBranchFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2

```solidity
File: ERC20hTokenRootFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2

```solidity
File: RootBridgeAgentFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2

```solidity
File: IAnycallConfig.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IAnycallConfig.sol#L3

```solidity
File: IAnycallExecutor.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol#L3

```solidity
File: IAnycallProxy.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IAnycallProxy.sol#L3

```solidity
File: IApp.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IApp.sol#L3

```solidity
File: IArbitrumBranchPort.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol#L3

```solidity
File: IBranchBridgeAgent.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L2

```solidity
File: IBranchBridgeAgentFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol#L2

```solidity
File: IBranchPort.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L3

```solidity
File: IBranchRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IBranchRouter.sol#L2

```solidity
File: ICoreBranchRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol#L2

```solidity
File: IERC20hTokenBranch.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L2

```solidity
File: IERC20hTokenBranchFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol#L2

```solidity
File: IERC20hTokenRoot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L2

```solidity
File: IERC20hTokenRootFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol#L2

```solidity
File: IMulticall2.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IMulticall2.sol#L2

```solidity
File: IPortStrategy.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L3

```solidity
File: IRootBridgeAgent.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L2

```solidity
File: IRootBridgeAgentFactory.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol#L2

```solidity
File: IRootPort.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L3

```solidity
File: IRootRouter.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IRootRouter.sol#L2

```solidity
File: IVirtualAccount.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L2

```solidity
File: IWETH9.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IWETH9.sol#L2

```solidity
File: AnycallFlags.sol

pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L3

```solidity
File: ERC20hTokenBranch.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L2

```solidity
File: ERC20hTokenRoot.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2

```solidity
File: UniswapV3Staker.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L3

```solidity
File: IUniswapV3Staker.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L3

```solidity
File: IncentiveId.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveId.sol#L2

```solidity
File: IncentiveTime.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L2

```solidity
File: NFTPositionInfo.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L3

```solidity
File: RewardMath.sol

pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/RewardMath.sol#L2



</details>




### <a href="#qa-summary">[LOW&#x2011;15]</a><a name="LOW&#x2011;15"> Storage Write Removal Bug On Conditional Early Termination

See the following for more info:

https://twitter.com/solidity_lang/status/1567953562151579650?s=20&t=fXIo4hRjOiMXl2dqpD5Oyw

https://blog.soliditylang.org/2022/09/08/storage-write-removal-before-conditional-termination/

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

536: function getChainIdInternal() internal view returns (uint256) {
        uint256 chainId;
        assembly {
            chainId := chainid()
        }
        return chainId;
    }
}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L536




#### <ins>Recommended Mitigation Steps</ins>
Upgrade pragma to at least version 0.8.17






### <a href="#qa-summary">[LOW&#x2011;16]</a><a name="LOW&#x2011;16"> Upgrade OpenZeppelin Contract Dependency

An outdated OZ version is used (which has known vulnerabilities, see: https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).
Release: https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.9.0

#### <ins>Proof Of Concept</ins>

Require dependencies to be at least version of 4.9.0 to include critical vulnerability patches.

```solidity
File: package.json

    "@openzeppelin/contracts": "4.6.0"
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/../lib/v3-periphery/package.json#L32



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.9.0 via `>=4.9.0`





## Non Critical Issues


### <a href="#qa-summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Avoid the use of sensitive terms

Use <a href="https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/">alternative variants</a>, e.g. allowlist/denylist instead of whitelist/blacklist

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: IBaseV2Gauge.sol

58: /// @notice mapping of whitelisted bribe tokens.

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IBaseV2Gauge.sol#L58

```solidity
File: GovernorBravoDelegateMaia.sol

113: // Allow addresses above proposal threshold and whitelisted addresses to propose
116: || isWhitelisted(msg.sender),
235: // Whitelisted proposers can't be canceled for falling below proposal threshold
236: if (isWhitelisted(proposal.proposer)) {
239: && msg.sender == whitelistGuardian,
240: "GovernorBravo::cancel: whitelisted proposer"
385: * @notice View function which returns if an account is whitelisted
387: * @return If the account is whitelisted
389: function isWhitelisted(address account) public view returns (bool) {
390: return (whitelistAccountExpirations[account] > block.timestamp);
443: * @notice Admin function for setting the whitelist expiration as a timestamp for an account. Whitelist status allows accounts to propose without meeting threshold
444: * @param account Account address to set whitelist expiration for
445: * @param expiration Expiration for account whitelist status as timestamp (if now < expiration, whitelisted)
447: function _setWhitelistAccountExpiration(address account, uint256 expiration) external {
449: msg.sender == admin || msg.sender == whitelistGuardian,
450: "GovernorBravo::_setWhitelistAccountExpiration: admin only"
452: whitelistAccountExpirations[account] = expiration;
454: emit WhitelistAccountExpirationSet(account, expiration);
458: * @notice Admin function for setting the whitelistGuardian. WhitelistGuardian can cancel proposals from whitelisted addresses
459: * @param account Account to set whitelistGuardian to (0x0 to remove whitelistGuardian)
461: function _setWhitelistGuardian(address account) external {
462: require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");
463: address oldGuardian = whitelistGuardian;
464: whitelistGuardian = account;
466: emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L113

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L116

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L235

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L236

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L239

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L240

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L385

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L387

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L389

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L390

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L443

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L444

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L445

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L447

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L449

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L450

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L452

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L454

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L458

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L459

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L461

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L462

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L463

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L464

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L466



```solidity
File: GovernorBravoInterfaces.sol

53: /// @notice Emitted when whitelist account expiration is set
54: event WhitelistAccountExpirationSet(address account, uint256 expiration);
56: /// @notice Emitted when the whitelistGuardian is set
57: event WhitelistGuardianSet(address oldGuardian, address newGuardian);
162: /// @notice Stores the expiration of account whitelist status as a timestamp
163: mapping(address => uint256) public whitelistAccountExpirations;
165: /// @notice Address which manages whitelisted proposals and whitelist accounts
166: address public whitelistGuardian;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L53

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L54

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L56

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L57

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L162

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L163

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L166



```solidity
File: GovernorBravoInterfaces.sol

53: /// @notice Emitted when whitelist account expiration is set
54: event WhitelistAccountExpirationSet(address account, uint256 expiration);
56: /// @notice Emitted when the whitelistGuardian is set
57: event WhitelistGuardianSet(address oldGuardian, address newGuardian);
162: /// @notice Stores the expiration of account whitelist status as a timestamp
163: mapping(address => uint256) public whitelistAccountExpirations;
165: /// @notice Address which manages whitelisted proposals and whitelist accounts
166: address public whitelistGuardian;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L53

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L54

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L56

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L57

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L162

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L163

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L166



```solidity
File: FlywheelBribeRewards.sol

38: /// @dev Anyone can call this, whitelisting is handled in FlywheelCore

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelBribeRewards.sol#L38

```solidity
File: BoostAggregator.sol

48: mapping(address => bool) public whitelistedAddresses;
78: ///      whitelisted addresses to retrieve NFTs incorrectly sent to this contract
82: onlyWhitelisted(from)
143: function addWhitelistedAddress(address user) external onlyOwner {
144: whitelistedAddresses[user] = true;
148: function removeWhitelistedAddress(address user) external onlyOwner {
149: delete whitelistedAddresses[user];
188: /// @dev Only whitelisted addresses
190: modifier onlyWhitelisted(address from) {
191: if (!whitelistedAddresses[from]) revert Unauthorized();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L48

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L78

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L82

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L143

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L144

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L148

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L149

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L188

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L190

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L191



```solidity
File: IBoostAggregator.sol

47: /// @notice mapping of whitelisted addresses
48: function whitelistedAddresses(address) external view returns (bool);
81: * @notice add whitelisted address to stake using this contract
84: function addWhitelistedAddress(address user) external;
87: * @notice remove whitelisted address from staking using this contract
90: function removeWhitelistedAddress(address user) external;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L47

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L48

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L81

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L84

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L87

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L90



```solidity
File: BranchPort.sol

56: /// @notice List of Tokens whitelisted for usage in Port Strategies.

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L56

```solidity
File: IPortStrategy.sol

9: *         whitelisted by the chain's Branch Port to manage a limited amount

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L9



</details>



### <a href="#qa-summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> No need for `== true` or `== false` checks

There is no need to verify that `== true` or `== false` when the variable checked upon is a boolean as well.

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

366: require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L366




#### <ins>Recommended Mitigation Steps</ins>

Instead simply check for `variable` or `!variable`






### <a href="#qa-summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Consider adding a deny-list

Doing so will significantly increase centralization, but will help to prevent hackers from using stolen tokens

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: bHermesBoost.sol

18: contract bHermesBoost is ERC20Boost, IbHermesUnderlying

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesBoost.sol#L18

```solidity
File: bHermesGauges.sol

22: contract bHermesGauges is ERC20Gauges, IbHermesUnderlying

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesGauges.sol#L22

```solidity
File: bHermesVotes.sol

16: contract bHermesVotes is ERC20MultiVotes, IbHermesUnderlying

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/bHermesVotes.sol#L16

```solidity
File: HERMES.sol

47: contract HERMES is ERC20, Ownable

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/tokens/HERMES.sol#L47

```solidity
File: Maia.sol

40: contract Maia is ERC20, Ownable

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/Maia.sol#L40

```solidity
File: ERC20hTokenBranchFactory.sol

11: contract ERC20hTokenBranchFactory is Ownable, IERC20hTokenBranchFactory

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L11

```solidity
File: ERC20hTokenRootFactory.sol

12: contract ERC20hTokenRootFactory is Ownable, IERC20hTokenRootFactory

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L12

```solidity
File: ERC20hTokenBranch.sol

11: contract ERC20hTokenBranch is ERC20, Ownable, IERC20hTokenBranch

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L11

```solidity
File: ERC20hTokenRoot.sol

12: contract ERC20hTokenRoot is ERC20, IERC20hTokenRoot

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L12



</details>






### <a href="#qa-summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Consistent usage of require vs custom error

Consider using the same approach throughout the codebase to improve the consistency of the code.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20Boost.sol

118: revert InvalidGauge();
267: revert InvalidGauge();
279: revert InvalidGauge();
122: if (!_userGauges[user].add(msg.sender)) revert GaugeAlreadyAttached();
267: if (gauge == address(0) || !(newAdd || previouslyDeprecated)) revert InvalidGauge();
279: if (!_deprecatedGauges.add(gauge)) revert InvalidGauge();
342: if (freeGaugeBoost(user) < amount) revert AttachedBoost();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L118

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L267

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L279

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L122

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L267

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L279

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L342



```solidity
File: ERC20Gauges.sol

29: if (_incrementFreezeWindow >= _gaugeCycleLength) revert IncrementFreezeError();
203: if (!_gauges.contains(gauge) || _deprecatedGauges.contains(gauge)) revert InvalidGaugeError();
205: if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError();
212: revert MaxGaugeError();
236: if (newUserWeight > getVotes(user)) revert OverWeightError();
251: if (weights.length != size) revert SizeMismatchError();
328: if (weights.length != size) revert SizeMismatchError();
293: if (!_gauges.contains(gauge)) revert InvalidGaugeError();
251: if (weights.length != size) revert SizeMismatchError();
328: if (weights.length != size) revert SizeMismatchError();
411: if (gauge == address(0) || !(newAdd || previouslyDeprecated)) revert InvalidGaugeError();
435: if (!_deprecatedGauges.add(gauge)) revert InvalidGaugeError();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L29

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L203

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L205

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L236

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L251

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L328

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L293

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L251

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L328

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L411

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Gauges.sol#L435



```solidity
File: ERC20MultiVotes.sol

59: if (blockNumber >= block.number) revert BlockError();
165: if (count > 1) revert DelegationError();
191: if (delegatee == address(0) || free < amount || amount == 0) revert DelegationError();
165: revert DelegationError();
191: revert DelegationError();
196: revert DelegationError();
220: if (userUnusedVotes(delegatee) < amount) revert UndelegationVoteError();
351: if ((userFreeVotes + totalFreed) < votes) revert UndelegationVoteError();
364: require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");
375: require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L59

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L191

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L191

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L196

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L220

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L351

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L364

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L375



```solidity
File: ERC4626MultiToken.sol

48: if (length != _assets.length || length == 0) revert InvalidLength();
100: require((shares = previewDeposit(assetsAmounts)) != 0, "ZERO_SHARES");
172: if (assetsAmounts[i] == 0) revert ZeroAssets();
198: if (length != assets.length) revert InvalidLength();
248: if (length != assets.length) revert InvalidLength();
198: if (length != assets.length) revert InvalidLength();
248: if (length != assets.length) revert InvalidLength();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L48

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L100

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L172

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L198

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L248

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L198

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L248



```solidity
File: UlyssesERC4626.sol

27: if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
40: require(shares != 0, "ZERO_SHARES");
50: require(assets != 0, "ZERO_ASSETS");
75: require(assets != 0, "ZERO_ASSETS");
50: require(assets != 0, "ZERO_ASSETS");
75: require(assets != 0, "ZERO_ASSETS");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L27

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L40

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L50

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L75

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L50

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L75



```solidity
File: FlywheelGaugeRewards.sol

83: if (currentCycle <= lastCycle) revert CycleError();
118: if (currentCycle <= lastCycle) revert CycleError();
83: if (currentCycle <= lastCycle) revert CycleError();
118: if (currentCycle <= lastCycle) revert CycleError();
174: if (size == 0) revert EmptyGaugesError();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L83

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L118

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L83

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L118

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L174



```solidity
File: TalosBaseStrategy.sol

108: if (initialized) revert AlreadyInitialized();
151: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
212: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
273: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
335: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
158: if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
219: if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
151: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
212: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
273: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
335: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
158: if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
219: if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
252: if (shares == 0) revert RedeemingZeroShares();
253: if (receiver == address(0)) revert ReceiverIsZeroAddress();
151: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
212: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
273: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
335: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
334: if (msg.sender != address(pool)) revert CallerIsNotPool();
151: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
212: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
273: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
335: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
399: revert Token0AmountIsBiggerThanProtocolFees();
402: revert Token1AmountIsBiggerThanProtocolFees();
431: if (msg.sender != strategyManager) revert NotStrategyManager();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L108

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L151

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L335

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L158

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L219

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L151

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L335

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L158

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L219

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L252

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L253

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L151

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L335

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L334

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L151

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L335

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L399

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L402

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L431



```solidity
File: UlyssesPool.sol

160: if (weight == 0) revert InvalidWeight();
224: if (weight == 0) revert InvalidWeight();
165: if (destinationIds[address(destination)] != 0 || destinationId == id) revert InvalidPool();
167: if (destinationId == 0) revert NotUlyssesLP();
171: if (index > MAX_DESTINATIONS) revert TooManyDestinations();
185: if (newTotalWeights > MAX_TOTAL_WEIGHT) revert InvalidWeight();
160: if (weight == 0) revert InvalidWeight();
224: if (weight == 0) revert InvalidWeight();
228: if (poolIndex == 0) revert NotUlyssesLP();
160: revert InvalidWeight();
185: revert InvalidWeight();
224: revert InvalidWeight();
244: revert InvalidWeight();
310: if (_fees.lambda1 > MAX_LAMBDA1) revert InvalidFee();
312: if (_fees.lambda1 + _fees.lambda2 != DIVISIONER / 2) revert InvalidFee();
315: if (_fees.sigma1 > DIVISIONER) revert InvalidFee();
317: if (_fees.sigma1 <= _fees.sigma2 || _fees.sigma2 < MIN_SIGMA2) revert InvalidFee();
324: if (msg.sender != factory.owner()) revert Unauthorized();
327: if (_protocolFee > MAX_PROTOCOL_FEE) revert InvalidFee();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L160

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L224

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L165

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L167

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L171

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L185

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L160

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L224

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L228

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L160

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L185

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L224

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L244

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L310

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L312

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L315

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L317

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L324

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L327



```solidity
File: UlyssesToken.sol

45: if (assetId[asset] != 0) revert AssetAlreadyAdded();
66: if (newAssetsLength == 0) revert CannotRemoveLastAsset();
89: if (_weights.length != assets.length) revert InvalidWeightsLength();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L45

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L66

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L89



```solidity
File: UlyssesFactory.sol

61: require(_owner != address(0), "Owner cannot be 0");
84: if (address(asset) == address(0)) revert InvalidAsset();
97: if (length != weights.length) revert ParameterLengthError();
108: if (length != weights[i].length) revert ParameterLengthError();
148: if (destination == address(0)) revert InvalidPoolId();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L61

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L84

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L97

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L108

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L148



```solidity
File: ArbitrumBranchPort.sol

34: require(_rootPortAddress != address(0), "Root Port Address cannot be 0");
50: if (globalToken == address(0)) revert UnknownUnderlyingToken();
63: revert UnknownToken();
68: if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L34

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L50

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L63

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L68



```solidity
File: BaseBranchRouter.sol

38: require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");
139: if (msg.sender != bridgeAgentExecutorAddress) revert UnrecognizedBridgeAgentExecutor();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BaseBranchRouter.sol#L38

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BaseBranchRouter.sol#L139



```solidity
File: BranchBridgeAgent.sol

151: require(_rootBridgeAgentAddress != address(0), "Root Bridge Agent Address cannot be the zero address.");
152: require(_localAnyCallAddress != address(0), "AnyCall Address cannot be the zero address.");
153: require(_localAnyCallExecutorAddress != address(0), "AnyCall Executor Address cannot be the zero address.");
154: require(_localRouterAddress != address(0), "Local Router Address cannot be the zero address.");
155: require(_localPortAddress != address(0), "Local Port Address cannot be the zero address.");
327: if (getDeposit[_depositNonce].owner != msg.sender) revert NotDepositOwner();
453: revert DepositRedeemUnavailable();
816: ) revert InvalidInput();
1375: if (msg.sender != bridgeAgentExecutorAddress) revert UnrecognizedBridgeAgentExecutor();
1387: if (msg.sender != localAnyCallExecutorAddress) revert AnycallUnauthorizedCaller();
1389: if (from != rootBridgeAgentAddress) revert AnycallUnauthorizedCaller();
1400: if (msg.sender != localRouterAddress) revert UnrecognizedCallerNotRouter();
1411: if (msg.value <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGas();
1416: if (_depositedGas <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGas();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L151

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L152

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L153

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L154

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L155

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L327

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L453

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L816

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1375

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1387

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1389

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1400

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1411

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1416



```solidity
File: BranchPort.sol

95: require(_owner != address(0), "Owner is zero address");
100: require(coreBranchRouterAddress == address(0), "Contract already initialized");
101: require(!isBridgeAgentFactory[_bridgeAgentFactory], "Contract already initialized");
103: require(_coreBranchRouter != address(0), "CoreBranchRouter is zero address");
104: require(_bridgeAgentFactory != address(0), "BridgeAgentFactory is zero address");
159: if (_amount > _excessReserves(_token)) revert InsufficientReserves();
173: if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
353: if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
174: if (!isPortStrategy[_strategy][_token]) revert UnrecognizedPortStrategy();
302: require(coreBranchRouterAddress != address(0), "CoreRouter address is zero");
303: require(_newCoreRouter != address(0), "New CoreRouter address is zero");
332: if (_minimumReservesRatio >= DIVISIONER) revert InvalidMinimumReservesRatio();
173: if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
353: if (!isStrategyToken[_token]) revert UnrecognizedStrategyToken();
398: if (msg.sender != coreBranchRouterAddress) revert UnrecognizedCore();
404: if (!isBridgeAgent[msg.sender]) revert UnrecognizedBridgeAgent();
410: if (!isBridgeAgentFactory[msg.sender]) revert UnrecognizedBridgeAgentFactory();
416: if (!isPortStrategy[msg.sender][_token]) revert UnrecognizedPortStrategy();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L95

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L100

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L101

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L103

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L104

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L159

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L173

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L353

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L174

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L302

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L303

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L332

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L173

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L353

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L398

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L404

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L410

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L416



```solidity
File: CoreRootRouter.sol

91: revert UnauthorizedCallerNotManager();
95: if (!IPort(rootPortAddress).isChainId(_toChain)) revert InvalidChainId();
98: if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_toChain) != address(0)) revert InvalidChainId();
101: if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_toChain)) revert UnauthorizedChainId();
146: if (_toChain == rootChainId) revert InvalidChainId();
149: revert UnrecognizedGlobalToken();
154: revert TokenAlreadyAdded();
190: revert TokenAlreadyAdded();
210: revert TokenAlreadyAdded();
190: ) revert TokenAlreadyAdded();
210: ) revert TokenAlreadyAdded();
210: if (IPort(rootPortAddress).isLocalToken(_localAddress, _toChain)) revert TokenAlreadyAdded();
234: revert UnrecognizedBridgeAgentFactory();
453: if (msg.sender != bridgeAgentExecutorAddress) revert UnrecognizedBridgeAgentExecutor();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L91

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L95

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L98

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L101

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L146

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L154

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L190

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L210

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L190

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L210

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L210

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L234

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L453



```solidity
File: RootBridgeAgent.sol

210: require(address(_wrappedNativeToken) != address(0), "Wrapped native token cannot be zero address");
211: require(_daoAddress != address(0), "DAO cannot be zero address");
212: require(_localAnyCallAddress != address(0), "Anycall Address cannot be zero address");
213: require(_localAnyCallExecutorAddress != address(0), "Anycall Executor Address cannot be zero address");
214: require(_localPortAddress != address(0), "Port Address cannot be zero address");
215: require(_localRouterAddress != address(0), "Router Address cannot be zero address");
261: revert SettlementRedeemUnavailable();
265: revert NotSettlementOwner();
302: revert InvalidInputParams();
347: revert InvalidInputParams();
390: revert InvalidInputParams();
397: revert InvalidInputParams();
347: if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();
302: revert InvalidInputParams();
347: revert InvalidInputParams();
390: revert InvalidInputParams();
397: revert InvalidInputParams();
397: if (globalAddress == address(0)) revert InvalidInputParams();
453: if (_localAddress == address(0)) revert UnrecognizedLocalAddress();
459: if (_underlyingAddress == address(0)) revert UnrecognizedUnderlyingAddress();
461: revert InsufficientBalanceForSettlement();
647: if (!approvedGasPool[msg.sender]) revert CallerIsNotPool();
648: if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
665: if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();
709: if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();
665: if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();
709: if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();
758: if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
761: if (msg.value <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
778: if (callee == address(0)) revert UnrecognizedBridgeAgent();
1234: if (initialGas == 0) revert GasErrorOrRepeatedTx();
1249: if (getBranchBridgeAgent[_branchChainId] != address(0)) revert AlreadyAddedBridgeAgent();
1260: if (msg.sender != daoAddress) revert NotDao();
1290: if (msg.sender != localAnyCallExecutorAddress) revert AnycallUnauthorizedCaller();
1292: if (getBranchBridgeAgent[fromChainId] != from) revert AnycallUnauthorizedCaller();
1303: if (msg.sender != localRouterAddress) revert UnrecognizedCallerNotRouter();
1308: if (msg.sender != bridgeAgentExecutorAddress) revert UnrecognizedExecutor();
1308: revert UnrecognizedExecutor();
1315: revert UnrecognizedExecutor();
1322: if (msg.sender != localPortAddress) revert UnrecognizedPort();
1329: revert UnrecognizedBridgeAgentManager();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L210

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L211

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L212

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L213

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L214

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L215

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L261

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L265

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L302

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L390

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L397

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L302

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L390

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L397

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L397

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L453

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L459

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L461

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L647

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L648

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L665

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L709

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L665

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L709

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L758

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L761

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L778

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1234

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1249

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1260

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1290

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1292

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1303

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1308

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1308

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1315

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1322

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1329



```solidity
File: RootPort.sol

115: require(_wrappedNativeToken != address(0), "Invalid wrapped native token address.");
129: require(_setup, "Setup ended.");
145: require(_setup, "Setup ended.");
130: require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");
131: require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");
129: require(_setup, "Setup ended.");
145: require(_setup, "Setup ended.");
146: require(isBridgeAgent[_coreRootBridgeAgent], "Core Bridge Agent doesn't exist.");
147: require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");
148: require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");
149: require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");
159: require(_owner != address(0), "Owner cannot be 0 address.");
280: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
280: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
280: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
280: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
280: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
280: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
294: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
300: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
309: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
318: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
330: if (!isGlobalAddress[_hToken]) revert UnrecognizedToken();
367: if (isBridgeAgent[_bridgeAgent]) revert AlreadyAddedBridgeAgent();
367: revert AlreadyAddedBridgeAgent();
384: revert AlreadyAddedBridgeAgent();
387: revert BridgeAgentNotAllowed();
492: if (isGlobalAddress[_ecoTokenGlobalAddress]) revert AlreadyAddedEcosystemToken();
492: ) revert AlreadyAddedEcosystemToken();
496: ) revert AlreadyAddedEcosystemToken();
511: if (!isBridgeAgentFactory[msg.sender]) revert UnrecognizedBridgeAgentFactory();
517: if (!isBridgeAgent[msg.sender]) revert UnrecognizedBridgeAgent();
523: if (!(msg.sender == coreRootRouterAddress)) revert UnrecognizedCoreRootRouter();
529: if (!(msg.sender == localBranchPortAddress)) revert UnrecognizedLocalBranchPort();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L115

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L129

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L145

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L130

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L131

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L129

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L145

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L146

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L147

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L148

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L149

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L159

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L294

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L300

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L318

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L294

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L300

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L318

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L294

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L300

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L318

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L294

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L300

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L318

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L294

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L300

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L318

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L280

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L294

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L300

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L309

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L318

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L330

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L367

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L367

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L384

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L387

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L492

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L492

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L496

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L511

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L517

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L523

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L529



```solidity
File: ERC20hTokenBranchFactory.sol

28: require(_localPortAddress != address(0), "Port address cannot be 0");
36: require(_coreRouter != address(0), "CoreRouter address cannot be 0");
76: if (msg.sender != localCoreRouterAddress) revert UnrecognizedCoreRouter();
82: if (msg.sender != localPortAddress) revert UnrecognizedPort();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L28

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L36

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L76

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L82



```solidity
File: ERC20hTokenRootFactory.sol

34: require(_rootPortAddress != address(0), "Root Port Address cannot be 0");
41: require(_coreRouter != address(0), "CoreRouter address cannot be 0");
76: revert UnrecognizedCoreRouter();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L34

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L41

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L76



```solidity
File: ERC20hTokenRoot.sol

45: require(_rootPortAddress != address(0), "Root Port Address cannot be 0");
46: require(_factoryAddress != address(0), "Factory Address cannot be 0");
58: if (msg.sender != rootPortAddress) revert UnrecognizedPort();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L45

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L46

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L58





</details>








#### <a href="#qa-summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20Boost.sol

191: require(_userGauges[msg.sender].remove(gauge));
213: require(_userGauges[msg.sender].remove(gauge));
239: require(_userGauges[msg.sender].remove(gauge));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L191

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L213

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L239



```solidity
File: UlyssesERC4626.sol

50: require(assets != 0, "ZERO_ASSETS");
75: require(assets != 0, "ZERO_ASSETS");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L50

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L75



```solidity
File: CoreRootRouter.sol

386: revert();
396: revert();
407: revert();
418: revert();
428: revert();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L386

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L396

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L407

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L418

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L428



```solidity
File: MulticallRootRouter.sol

172: revert();
245: revert();
255: revert();

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L172

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L245

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L255



```solidity
File: RootPort.sol

129: require(_setup, "Setup ended.");
145: require(_setup, "Setup ended.");

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L129

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L145





</details>




### <a href="#qa-summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> `else`-block not required

One level of nesting can be removed by not having an `else` block when the `if`-block returns, and `if (foo) { return 1; } else { return 2; }` becomes `if (foo) { return 1; } return 2;`

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

314: } else if (block.timestamp >= add256(proposal.eta, timelock.GRACE_PERIOD())) {
            return ProposalState.Expired;
        } else {
            return ProposalState.Queued;
        }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L314

```solidity
File: UlyssesPool.sol

138: if (balance > assets) {
            return balance - assets;
        } else {
            return 0;
        }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L138






### <a href="#qa-summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Events are missing sender information

The following functions are missing critical parameters when emitting an event.
When dealing with source address which uses the value of `msg.sender`, the `msg.sender` value must be specified in every transaction, a contract or web page listening to events cannot react to users, emit does not serve the purpose. Basically, this event cannot be used.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20Boost.sol


function attach(address user) external {
        if (!_gauges.contains(msg.sender) || _deprecatedGauges.contains(msg.sender)) {
            revert InvalidGauge();
        }

        
        if (!_userGauges[user].add(msg.sender)) revert GaugeAlreadyAttached();

        uint128 userGaugeBoost = balanceOf[user].toUint128();

        if (getUserBoost[user] < userGaugeBoost) {
            getUserBoost[user] = userGaugeBoost;
            emit UpdateUserBoost(user, userGaugeBoost);
        }

        getUserGaugeBoost[user][msg.sender] =
            GaugeState({userGaugeBoost: userGaugeBoost, totalGaugeBoost: totalSupply.toUint128()});

        emit Attach(user, msg.sender, userGaugeBoost);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L128

```solidity
File: GovernorBravoDelegateMaia.sol


function cancel(uint256 proposalId) external {
        require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");

        Proposal storage proposal = proposals[proposalId];

        
        
        if (msg.sender != proposal.proposer && msg.sender != admin) {
            
            if (isWhitelisted(proposal.proposer)) {
                require(
                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
                        && msg.sender == whitelistGuardian,
                    "GovernorBravo::cancel: whitelisted proposer"
                );
            } else {
                require(
                    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
                    "GovernorBravo::cancel: proposer above threshold"
                );
            }
        }

        proposal.canceled = true;
        for (uint256 i = 0; i < proposal.targets.length; i++) {
            timelock.cancelTransaction(
                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta
            );
        }

        emit ProposalCanceled(proposalId);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L257

```solidity
File: GovernorBravoDelegateMaia.sol


function _setVotingDelay(uint256 newVotingDelay) external {
        require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            "GovernorBravo::_setVotingDelay: invalid voting delay"
        );
        uint256 oldVotingDelay = votingDelay;
        votingDelay = newVotingDelay;

        emit VotingDelaySet(oldVotingDelay, votingDelay);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L406

```solidity
File: GovernorBravoDelegateMaia.sol


function _setVotingPeriod(uint256 newVotingPeriod) external {
        require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            "GovernorBravo::_setVotingPeriod: invalid voting period"
        );
        uint256 oldVotingPeriod = votingPeriod;
        votingPeriod = newVotingPeriod;

        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L422

```solidity
File: GovernorBravoDelegateMaia.sol


function _setProposalThreshold(uint256 newProposalThreshold) external {
        require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");
        require(
            newProposalThreshold >= MIN_PROPOSAL_THRESHOLD && newProposalThreshold <= MAX_PROPOSAL_THRESHOLD,
            "GovernorBravo::_setProposalThreshold: invalid proposal threshold"
        );
        uint256 oldProposalThreshold = proposalThreshold;
        proposalThreshold = newProposalThreshold;

        emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L439

```solidity
File: GovernorBravoDelegateMaia.sol


function _setWhitelistAccountExpiration(address account, uint256 expiration) external {
        require(
            msg.sender == admin || msg.sender == whitelistGuardian,
            "GovernorBravo::_setWhitelistAccountExpiration: admin only"
        );
        whitelistAccountExpirations[account] = expiration;

        emit WhitelistAccountExpirationSet(account, expiration);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L454

```solidity
File: GovernorBravoDelegateMaia.sol


function _setWhitelistGuardian(address account) external {
        require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");
        address oldGuardian = whitelistGuardian;
        whitelistGuardian = account;

        emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L466

```solidity
File: GovernorBravoDelegateMaia.sol


function _setPendingAdmin(address newPendingAdmin) external {
        
        require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");

        
        address oldPendingAdmin = pendingAdmin;

        
        pendingAdmin = newPendingAdmin;

        
        emit NewPendingAdmin(oldPendingAdmin, newPendingAdmin);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L498

```solidity
File: GovernorBravoDelegateMaia.sol


function _acceptAdmin() external {
        
        require(
            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"
        );

        
        address oldAdmin = admin;
        address oldPendingAdmin = pendingAdmin;

        
        admin = pendingAdmin;

        
        pendingAdmin = address(0);

        emit NewAdmin(oldAdmin, admin);
        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L521-L522

```solidity
File: GovernorBravoDelegator.sol


function _setImplementation(address implementation_) public {
        require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");
        require(
            implementation_ != address(0), "GovernorBravoDelegator::_setImplementation: invalid implementation address"
        );

        address oldImplementation = implementation;
        implementation = implementation_;

        emit NewImplementation(oldImplementation, implementation);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L50

```solidity
File: GovernorBravoDelegator.sol


function _setImplementation(address implementation_) public {
        require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");
        require(
            implementation_ != address(0), "GovernorBravoDelegator::_setImplementation: invalid implementation address"
        );

        address oldImplementation = implementation;
        implementation = implementation_;

        emit NewImplementation(oldImplementation, implementation);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L49

```solidity
File: UlyssesToken.sol


function removeAsset(address asset) external nonReentrant onlyOwner {
        
        uint256 assetIndex = assetId[asset] - 1;

        uint256 newAssetsLength = assets.length - 1;

        if (newAssetsLength == 0) revert CannotRemoveLastAsset();

        totalWeights -= weights[assetIndex];

        address lastAsset = assets[newAssetsLength];

        assetId[lastAsset] = assetIndex;
        assets[assetIndex] = lastAsset;
        weights[assetIndex] = weights[newAssetsLength];

        assets.pop();
        weights.pop();
        assetId[asset] = 0;

        emit AssetRemoved(asset);

        updateAssetBalances();

        asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L80

```solidity
File: RootBridgeAgent.sol


function anyExecute(bytes calldata data)
        external
        virtual
        requiresExecutor
        returns (bool success, bytes memory result)
    {
        
        uint256 _initialGas = gasleft();

        uint24 fromChainId;

        UserFeeInfo memory _userFeeInfo;

        if (localAnyCallExecutorAddress == msg.sender) {
            
            initialGas = _initialGas;

            
            (, uint256 _fromChainId) = _getContext();

            
            fromChainId = _fromChainId.toUint24();

            
            _userFeeInfo.depositedGas = _gasSwapIn(
                uint256(uint128(bytes16(data[data.length - PARAMS_GAS_IN:data.length - PARAMS_GAS_OUT]))), fromChainId
            ).toUint128();

            
            _userFeeInfo.gasToBridgeOut = uint128(bytes16(data[data.length - PARAMS_GAS_OUT:data.length]));
        } else {
            
            fromChainId = localChainId;

            
            _userFeeInfo.depositedGas = uint128(bytes16(data[data.length - 32:data.length - 16]));

            
            _userFeeInfo.gasToBridgeOut = _userFeeInfo.depositedGas;
        }

        if (_userFeeInfo.depositedGas < _userFeeInfo.gasToBridgeOut) {
            _forceRevert();
            
            return (true, "Not enough gas to bridge out");
        }

        
        userFeeInfo = _userFeeInfo;

        
        bytes1 flag = data[0];

        
        if (flag == 0x00) {
            
            uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeSystemRequest(
                localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                
                (success, result) = (false, reason);
            }

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x01) {
            
            uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeNoDeposit(
                localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                
                (success, result) = (true, reason);
            }

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x02) {
            
            uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeWithDeposit(
                localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                result = reason;
            }

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x03) {
            
            uint32 nonce = uint32(bytes4(data[2:6]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeWithDepositMultiple(
                localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                result = reason;
            }

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x04) {
            
            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            VirtualAccount userAccount = IPort(localPortAddress).fetchVirtualAccount(
                address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
            );

            
            IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress);

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeSignedNoDeposit(
                address(userAccount), localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                
                (success, result) = (true, reason);
            }

            
            IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress);

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x05) {
            
            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            VirtualAccount userAccount = IPort(localPortAddress).fetchVirtualAccount(
                address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
            );

            
            IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress);

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeSignedWithDeposit(
                address(userAccount), localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                result = reason;
            }

            
            IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress);

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x06) {
            
            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            VirtualAccount userAccount = IPort(localPortAddress).fetchVirtualAccount(
                address(uint160(bytes20(data[PARAMS_START:PARAMS_START_SIGNED])))
            );

            
            IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress);

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeSignedWithDepositMultiple(
                address(userAccount), localRouterAddress, data, fromChainId
            ) returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                result = reason;
            }

            
            IPort(localPortAddress).toggleVirtualAccountApproved(userAccount, localRouterAddress);

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x07) {
            
            uint32 nonce = uint32(bytes4(data[1:5]));

            
            if (executionHistory[fromChainId][nonce]) {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeRetrySettlement(uint32(bytes4(data[5:9])))
            returns (bool, bytes memory res) {
                (success, result) = (true, res);
            } catch (bytes memory reason) {
                result = reason;
            }

            
            executionHistory[fromChainId][nonce] = true;

            
        } else if (flag == 0x08) {
            
            uint32 nonce = uint32(bytes4(data[1:5]));

            
            if (!executionHistory[fromChainId][uint32(bytes4(data[1:5]))]) {
                
                executionHistory[fromChainId][nonce] = true;

                
                (success, result) = (false, "");
            } else {
                _forceRevert();
                
                return (true, "already executed tx");
            }

            
        } else {
            
            if (initialGas > 0) {
                _payExecutionGas(userFeeInfo.depositedGas, userFeeInfo.gasToBridgeOut, _initialGas, fromChainId);
            }

            return (false, "unknown selector");
        }

        emit LogCallin(flag, data, fromChainId);

        
        if (initialGas > 0) {
            _payExecutionGas(userFeeInfo.depositedGas, userFeeInfo.gasToBridgeOut, _initialGas, fromChainId);
        }
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1168

```solidity
File: UniswapV3Staker.sol


function createIncentiveFromGauge(uint256 reward) external {
        if (reward <= 0) revert IncentiveRewardMustBePositive();

        uint96 startTime = IncentiveTime.computeEnd(block.timestamp);

        IUniswapV3Pool pool = gaugePool[msg.sender];

        if (address(pool) == address(0)) revert IncentiveCallerMustBeRegisteredGauge();

        IncentiveKey memory key = IncentiveKey({startTime: startTime, pool: pool});
        bytes32 incentiveId = IncentiveId.compute(key);

        incentives[incentiveId].totalRewardUnclaimed += reward;

        hermes.safeTransferFrom(msg.sender, address(this), reward);

        emit IncentiveCreated(pool, startTime, reward);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L153

```solidity
File: UniswapV3Staker.sol


function createIncentive(IncentiveKey memory key, uint256 reward) external {
        if (reward <= 0) revert IncentiveRewardMustBePositive();

        uint96 startTime = IncentiveTime.computeStart(key.startTime);

        if (startTime != key.startTime) revert IncentiveStartTimeNotAtEndOfAnEpoch();

        if (startTime <= block.timestamp) revert IncentiveStartTimeMustBeNowOrInTheFuture();
        if (startTime - block.timestamp > maxIncentiveStartLeadTime) {
            revert IncentiveStartTimeTooFarIntoFuture();
        }

        if (address(gauges[key.pool]) == address(0)) {
            revert IncentiveCannotBeCreatedForPoolWithNoGauge();
        }

        bytes32 incentiveId = IncentiveId.compute(key);

        incentives[incentiveId].totalRewardUnclaimed += reward;

        hermes.safeTransferFrom(msg.sender, address(this), reward);

        emit IncentiveCreated(key.pool, startTime, reward);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L179

```solidity
File: UniswapV3Staker.sol


function onERC721Received(address, address from, uint256 tokenId, bytes calldata)
        external
        override
        returns (bytes4)
    {
        INonfungiblePositionManager _nonfungiblePositionManager = nonfungiblePositionManager;
        if (msg.sender != address(_nonfungiblePositionManager)) revert TokenNotUniswapV3NFT();

        (IUniswapV3Pool pool, int24 tickLower, int24 tickUpper, uint128 liquidity) =
            NFTPositionInfo.getPositionInfo(factory, nonfungiblePositionManager, tokenId);

        deposits[tokenId] = Deposit({owner: from, tickLower: tickLower, tickUpper: tickUpper, stakedTimestamp: 0});
        emit DepositTransferred(tokenId, address(0), from);

        
        _stakeToken(tokenId, pool, tickLower, tickUpper, liquidity);

        return this.onERC721Received.selector;
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L230

```solidity
File: UniswapV3Staker.sol


function claimReward(address to, uint256 amountRequested) external returns (uint256 reward) {
        reward = rewards[msg.sender];
        if (amountRequested != 0 && amountRequested < reward) {
            reward = amountRequested;
            rewards[msg.sender] -= reward;
        } else {
            rewards[msg.sender] = 0;
        }

        if (reward > 0) hermes.safeTransfer(to, reward);

        emit RewardClaimed(to, reward);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L273

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L283



```solidity
File: UniswapV3Staker.sol


function _unstakeToken(IncentiveKey memory key, uint256 tokenId, bool isNotRestake) private {
        Deposit storage deposit = deposits[tokenId];

        (uint96 endTime, uint256 stakedDuration) =
            IncentiveTime.getEndAndDuration(key.startTime, deposit.stakedTimestamp, block.timestamp);

        address owner = deposit.owner;

        
        if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();

        {
            
            address bribeAddress = bribeDepots[key.pool];

            if (bribeAddress != address(0)) {
                nonfungiblePositionManager.collect(
                    INonfungiblePositionManager.CollectParams({
                        tokenId: tokenId,
                        recipient: bribeAddress,
                        amount0Max: type(uint128).max,
                        amount1Max: type(uint128).max
                    })
                );
            }
        }

        bytes32 incentiveId = IncentiveId.compute(key);
        uint160 secondsInsideX128;
        uint128 liquidity;
        {
            uint128 boostAmount;
            uint128 boostTotalSupply;

            UniswapV3Gauge gauge = gauges[key.pool]; 

            
            if (hermesGaugeBoost.isUserGauge(owner, address(gauge)) && _userAttachements[owner][key.pool] == tokenId) {
                
                (boostAmount, boostTotalSupply) = hermesGaugeBoost.getUserGaugeBoost(owner, address(gauge));
                gauge.detachUser(owner);
                _userAttachements[owner][key.pool] = 0;
            }

            uint160 secondsPerLiquidityInsideInitialX128;
            (secondsPerLiquidityInsideInitialX128, liquidity) = stakes(tokenId, incentiveId);
            if (liquidity == 0) revert TokenNotStaked();

            (, uint160 secondsPerLiquidityInsideX128,) =
                key.pool.snapshotCumulativesInside(deposit.tickLower, deposit.tickUpper);

            secondsInsideX128 = RewardMath.computeBoostedSecondsInsideX128(
                stakedDuration,
                liquidity,
                uint128(boostAmount),
                uint128(boostTotalSupply),
                secondsPerLiquidityInsideInitialX128,
                secondsPerLiquidityInsideX128
            );
        }

        deposit.stakedTimestamp = 0;
        Incentive storage incentive = incentives[incentiveId];
        incentive.numberOfStakes--;
        uint256 reward = RewardMath.computeBoostedRewardAmount(
            incentive.totalRewardUnclaimed,
            incentive.totalSecondsClaimedX128,
            key.startTime,
            endTime,
            secondsInsideX128,
            block.timestamp
        );

        unchecked {
            
            
            
            incentive.totalSecondsClaimedX128 += secondsInsideX128;
            
            incentive.totalRewardUnclaimed -= reward;
            
            rewards[owner] += reward;

            
            
            tokenIdRewards[tokenId] += reward;
        }

        Stake storage stake = _stakes[tokenId][incentiveId];
        stake.secondsPerLiquidityInsideInitialX128 = 0;
        stake.liquidityNoOverflow = 0;
        if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;
        delete stakedIncentiveKey[tokenId];
        emit TokenUnstaked(tokenId, incentiveId);
    }

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L458



</details>


#### <ins>Recommended Mitigation Steps</ins>
Add `msg.sender` parameter in event-emit






### <a href="#qa-summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20Boost.sol

249: getUserBoost[msg.sender] = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L249

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

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L406

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L426

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L454

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L455





</details>



### <a href="#qa-summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Immutables can be named using the same convention

Immutables should be in uppercase per naming convention.
As shown below, some immutables are named using capital letters and underscores while some are not. For a better code quality, please consider naming these immutables using the same naming convention.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: UtilityManager.sol

25: bHermesGauges public immutable gaugeWeight;
27: bHermesBoost public immutable gaugeBoost;
29: ERC20Votes public immutable governance;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L25

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L27

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L29



```solidity
File: ERC4626PartnerManager.sol

31: PartnerManagerFactory public immutable override factory;
34: bHermes public immutable override bHermesToken;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L31

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/tokens/ERC4626PartnerManager.sol#L34



```solidity
File: BaseFlywheelRewards.sol

26: address public immutable override rewardToken;
29: FlywheelCore public immutable override flywheel;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/BaseFlywheelRewards.sol#L26

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/BaseFlywheelRewards.sol#L29



```solidity
File: SingleRewardsDepot.sol

13: address private immutable asset;
16: address private immutable rewardsContract;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/SingleRewardsDepot.sol#L13

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/SingleRewardsDepot.sol#L16



```solidity
File: FlywheelGaugeRewards.sol

27: ERC20Gauges public immutable override gaugeToken;
30: IBaseV2Minter public immutable minter;
33: address public immutable override rewardToken;
39: uint32 public immutable override gaugeCycleLength;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L27

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L30

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L33

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L39



```solidity
File: TalosManager.sol

22: int24 public immutable ticksFromLowerRebalance;
25: int24 public immutable ticksFromUpperRebalance;
28: int24 public immutable ticksFromLowerRerange;
31: int24 public immutable ticksFromUpperRerange;
34: ITalosBaseStrategy public immutable strategy;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L22

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L25

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L28

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L31

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L34



```solidity
File: TalosStrategyStaked.sol

46: BoostAggregator public immutable override boostAggregator;
49: FlywheelCoreInstant public immutable flywheel;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L46

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L49



```solidity
File: TalosBaseStrategy.sol

61: ERC20 public immutable override token0;
63: ERC20 public immutable override token1;
65: int24 public immutable override tickSpacing;
67: uint24 public immutable override poolFee;
69: IUniswapV3Pool public immutable override pool;
71: ITalosOptimizer public immutable override optimizer;
73: address public immutable strategyManager;
75: INonfungiblePositionManager public immutable override nonfungiblePositionManager;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L61

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L63

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L65

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L67

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L69

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L71

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L73

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L75



```solidity
File: BoostAggregator.sol

27: INonfungiblePositionManager public immutable nonfungiblePositionManager;
30: UniswapV3Staker public immutable uniswapV3Staker;
33: bHermesBoost public immutable hermesGaugeBoost;
36: ERC20 public immutable hermes;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L27

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L30

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L33

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L36



```solidity
File: BoostAggregatorFactory.sol

19: UniswapV3Staker public immutable uniswapV3Staker;
22: ERC20 public immutable hermes;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/BoostAggregatorFactory.sol#L19

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/BoostAggregatorFactory.sol#L22



```solidity
File: TalosStrategyStakedFactory.sol

26: BoostAggregatorFactory public immutable boostAggregatorFactory;
29: FlywheelCoreInstant public immutable flywheel;
32: FlywheelInstantRewards public immutable rewards;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyStakedFactory.sol#L26

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyStakedFactory.sol#L29

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyStakedFactory.sol#L32



```solidity
File: UniswapV3Staker.sol

91: IUniswapV3GaugeFactory public immutable uniswapV3GaugeFactory;
94: IUniswapV3Factory public immutable override factory;
97: INonfungiblePositionManager public immutable override nonfungiblePositionManager;
100: uint256 public immutable override maxIncentiveStartLeadTime;
103: address public immutable minter;
106: address public immutable hermes;
109: bHermesBoost public immutable hermesGaugeBoost;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L91

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L94

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L97

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L100

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L103

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L106

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L109





</details>






### <a href="#qa-summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
File: ERC20Boost.sol

151: uint256 userBoost = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20Boost.sol#L151

```solidity
File: ERC20MultiVotes.sol

67: uint256 low = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L67

```solidity
File: UniswapV3Staker.sol

456: if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L456










### <a href="#qa-summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Memory-safe annotation preferred over comment variant

The memory-safe annotation (`assembly ("memory-safe") { ... }`), available starting in Solidity version 0.8.13 is preferred over the comment variant, which will be removed in a future breaking [release](https://docs.soliditylang.org/en/v0.8.13/assembly.html#memory-safety). The comment variant is only meant for externalized library code that needs to work in earlier versions (e.g. `SafeTransferLib` needs to be able to be used in many different versions).

#### <ins>Proof Of Concept</ins>


```solidity
File: DateTimeLib.sol

42: /// @solidity memory-safe-assembly

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/libraries/DateTimeLib.sol#L42

```solidity
File: UlyssesPool.sol

// There are over 200 instances of this in this file

355: /// @solidity memory-safe-assembly
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol




### <a href="#qa-summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: UniswapV3GaugeFactory.sol

98: function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L98

```solidity
File: BaseV2Minter.sol

86: function setDao(address _dao) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L86

```solidity
File: BaseV2Minter.sol

92: function setDaoShare(uint256 _daoShare) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L92

```solidity
File: BaseV2Minter.sol

98: function setTailEmission(uint256 _tail_emission) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L98

```solidity
File: TalosOptimizer.sol

62: function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosOptimizer.sol#L62

```solidity
File: TalosOptimizer.sol

68: function setTwapDuration(uint32 _twapDuration) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosOptimizer.sol#L68

```solidity
File: TalosOptimizer.sol

74: function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosOptimizer.sol#L74

```solidity
File: TalosOptimizer.sol

80: function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosOptimizer.sol#L80

```solidity
File: TalosOptimizer.sol

85: function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosOptimizer.sol#L85

```solidity
File: BoostAggregator.sol

100: function setOwnRewardsDepot(address rewardsDepot) external {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L100

```solidity
File: BoostAggregator.sol

153: function setProtocolFee(uint256 _protocolFee) external onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L153

```solidity
File: UlyssesPool.sol

223: function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L223

```solidity
File: UlyssesPool.sol

308: function setFees(Fees calldata _fees) external nonReentrant onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L308

```solidity
File: UlyssesPool.sol

323: function setProtocolFee(uint256 _protocolFee) external nonReentrant {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L323

```solidity
File: BranchPort.sol

301: function setCoreRouter(address _newCoreRouter) external requiresCoreRouter {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L301



</details>





### <a href="#qa-summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Non-assembly Method Available

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

539: chainId := chainid()
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L539






### <a href="#qa-summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Non-`external`/`public` function names should begin with an underscore

According to the Solidity Style Guide, Non-`external`/`public` function names should begin with an <a href="https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables">underscore</a>

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20MultiVotes.sol

80: function average(uint256 a, uint256 b) internal pure returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L80

```solidity
File: ERC4626.sol

171: function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L171

```solidity
File: ERC4626.sol

173: function afterDeposit(uint256 assets, uint256 shares) internal virtual {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L173

```solidity
File: ERC4626DepositOnly.sol

111: function afterDeposit(uint256 assets, uint256 shares) internal virtual {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L111

```solidity
File: ERC4626MultiToken.sol

292: function beforeWithdraw(uint256[] memory assetsAmounts, uint256 shares) internal virtual {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L292

```solidity
File: ERC4626MultiToken.sol

294: function afterDeposit(uint256[] memory assetsAmounts, uint256 shares) internal virtual {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L294

```solidity
File: BaseV2Gauge.sol

98: function distribute(uint256 amount) internal virtual;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L98

```solidity
File: UniswapV3Gauge.sol

53: function distribute(uint256 amount) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/UniswapV3Gauge.sol#L53

```solidity
File: BaseV2GaugeFactory.sol

125: function afterCreateGauge(address strategy, bytes memory data) internal virtual;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/BaseV2GaugeFactory.sol#L125

```solidity
File: UniswapV3GaugeFactory.sol

76: function newGauge(address strategy, bytes memory data) internal override returns (BaseV2Gauge) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L76

```solidity
File: UniswapV3GaugeFactory.sol

89: function afterCreateGauge(address strategy, bytes memory) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L89

```solidity
File: GovernorBravoDelegateMaia.sol

190: function queueOrRevertInternal(
        address target,
        uint256 value,
        string memory signature,
        bytes memory data,
        uint256 eta
    ) internal {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L190

```solidity
File: GovernorBravoDelegateMaia.sol

361: function castVoteInternal(address voter, uint256 proposalId, uint8 support) internal returns (uint96) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L361

```solidity
File: GovernorBravoDelegateMaia.sol

525: function add256(uint256 a, uint256 b) internal pure returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L525

```solidity
File: GovernorBravoDelegateMaia.sol

531: function sub256(uint256 a, uint256 b) internal pure returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L531

```solidity
File: GovernorBravoDelegateMaia.sol

536: function getChainIdInternal() internal view returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L536

```solidity
File: GovernorBravoDelegator.sol

59: function delegateTo(address callee, bytes memory data) internal {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L59

```solidity
File: vMaia.sol

102: function beforeWithdraw(uint256, uint256) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/vMaia.sol#L102

```solidity
File: DateTimeLib.sol

39: function getMonth(uint256 timestamp) internal pure returns (uint256 month) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/libraries/DateTimeLib.sol#L39

```solidity
File: DateTimeLib.sol

55: function isTuesday(uint256 timestamp) internal pure returns (bool result, uint256 startOfDay) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/libraries/DateTimeLib.sol#L55

```solidity
File: GovernorBravoDelegator.sol

58: function delegateTo(address callee, bytes memory data) internal {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L58

```solidity
File: FlywheelBribeRewards.sol

32: function getNextCycleRewards(ERC20 strategy) internal override returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelBribeRewards.sol#L32

```solidity
File: TalosStrategyStaked.sol

101: function beforeRedeem(uint256 _tokenId, address _owner) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L101

```solidity
File: TalosStrategyStaked.sol

108: function afterRedeem(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L108

```solidity
File: TalosStrategyStaked.sol

114: function beforeDeposit(uint256 _tokenId, address _receiver) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L114

```solidity
File: TalosStrategyStaked.sol

121: function afterDeposit(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L121

```solidity
File: TalosStrategyStaked.sol

127: function beforeRerange(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L127

```solidity
File: TalosStrategyStaked.sol

134: function afterRerange(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L134

```solidity
File: TalosStrategyVanilla.sol

72: function beforeRedeem(uint256 _tokenId, address) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L72

```solidity
File: TalosStrategyVanilla.sol

79: function afterRedeem(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L79

```solidity
File: TalosStrategyVanilla.sol

83: function beforeDeposit(uint256 _tokenId, address) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L83

```solidity
File: TalosStrategyVanilla.sol

90: function afterDeposit(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L90

```solidity
File: TalosStrategyVanilla.sol

94: function beforeRerange(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L94

```solidity
File: TalosStrategyVanilla.sol

100: function afterRerange(uint256 _tokenId) internal override {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L100

```solidity
File: TalosBaseStrategy.sol

373: function beforeDeposit(uint256 _tokenId, address _receiver) internal virtual;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L373

```solidity
File: TalosStrategyStakedFactory.sol

62: function createTalosV3Strategy(
        IUniswapV3Pool pool,
        ITalosOptimizer optimizer,
        address strategyManager,
        bytes memory data
    ) internal override returns (TalosBaseStrategy strategy) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyStakedFactory.sol#L62

```solidity
File: TalosStrategyVanillaFactory.sol

33: function createTalosV3Strategy(
        IUniswapV3Pool pool,
        ITalosOptimizer optimizer,
        address strategyManager,
        bytes memory
    ) internal override returns (TalosBaseStrategy) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L33

```solidity
File: PoolActions.sol

38: function swapToEqualAmounts(ActionParams memory actionParams, int24 baseThreshold) internal {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolActions.sol#L38

```solidity
File: PoolActions.sol

56: function rerange(
        INonfungiblePositionManager nonfungiblePositionManager,
        ActionParams memory actionParams,
        uint24 poolFee
    )
        internal
        returns (int24 tickLower, int24 tickUpper, uint256 amount0, uint256 amount1, uint256 tokenId, uint128 liquidity)
    {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolActions.sol#L56

```solidity
File: PoolVariables.sol

47: function amountsForLiquidity(IUniswapV3Pool pool, uint128 liquidity, int24 _tickLower, int24 _tickUpper)
        internal
        view
        returns (uint256, uint256)
    {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L47

```solidity
File: PoolVariables.sol

66: function liquidityForAmounts(
        IUniswapV3Pool pool,
        uint256 amount0,
        uint256 amount1,
        int24 _tickLower,
        int24 _tickUpper
    ) internal view returns (uint128) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L66

```solidity
File: PoolVariables.sol

88: function checkRange(int24 tickLower, int24 tickUpper) internal pure {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L88

```solidity
File: PoolVariables.sol

96: function floor(int24 tick, int24 tickSpacing) internal pure returns (int24) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L96

```solidity
File: PoolVariables.sol

110: function getPositionTicks(
        IUniswapV3Pool pool,
        uint256 amount0Desired,
        uint256 amount1Desired,
        int24 baseThreshold,
        int24 tickSpacing
    ) internal view returns (int24 tickLower, int24 tickUpper) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L110

```solidity
File: PoolVariables.sol

155: function amountsForTicks(
        IUniswapV3Pool pool,
        uint256 amount0Desired,
        uint256 amount1Desired,
        int24 _tickLower,
        int24 _tickUpper
    ) internal view returns (uint256 amount0, uint256 amount1) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L155

```solidity
File: PoolVariables.sol

185: function amountsDirection(uint256 amount0Desired, uint256 amount1Desired, uint256 amount0, uint256 amount1)
        internal
        pure
        returns (bool zeroGreaterOne)
    {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L185

```solidity
File: PoolVariables.sol

217: function getSwapToEqualAmountsParams(
        IUniswapV3Pool _pool,
        ITalosOptimizer _strategy,
        int24 _tickSpacing,
        int24 baseThreshold,
        ERC20 _token0,
        ERC20 _token1
    ) internal returns (bool zeroForOne, int256 amountSpecified, uint160 sqrtPriceLimitX96) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L217

```solidity
File: TalosStrategySimple.sol

30: function doRerange() internal override returns (uint256 amount0, uint256 amount1) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/strategies/TalosStrategySimple.sol#L30

```solidity
File: TalosStrategySimple.sol

36: function doRebalance() internal override returns (uint256 amount0, uint256 amount1) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/strategies/TalosStrategySimple.sol#L36

```solidity
File: UlyssesPool.sol

1200: function beforeDeposit(uint256 assets) internal override returns (uint256 shares) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L1200

```solidity
File: UlyssesPool.sol

1209: function beforeMint(uint256 shares) internal override returns (uint256 assets) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L1209

```solidity
File: UlyssesPool.sol

1218: function afterRedeem(uint256 shares) internal override returns (uint256 assets) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L1218

```solidity
File: UlyssesToken.sol

110: function updateAssetBalances() internal {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L110

```solidity
File: RootBridgeAgent.sol

50: function checkParams(address _localPortAddress, DepositParams memory _dParams, uint24 _fromChain)
        internal
        view
        returns (bool)
    {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L50

```solidity
File: RootPort.sol

293: function mint(address _to, address _hToken, uint256 _amount, uint24 _fromChain) internal {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L293

```solidity
File: RootPort.sol

349: function addVirtualAccount(address _user) internal returns (VirtualAccount newAccount) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L349

```solidity
File: IncentiveId.sol

16: function compute(IUniswapV3Staker.IncentiveKey memory key) internal pure returns (bytes32 incentiveId) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveId.sol#L16

```solidity
File: IncentiveTime.sol

17: function computeStart(uint256 timestamp) internal pure returns (uint96 start) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L17

```solidity
File: IncentiveTime.sol

23: function computeEnd(uint256 timestamp) internal pure returns (uint96 end) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L23

```solidity
File: IncentiveTime.sol

31: function getEnd(uint96 start) internal pure returns (uint96 end) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L31

```solidity
File: IncentiveTime.sol

35: function getEndAndDuration(uint96 start, uint40 stakedTimestamp, uint256 timestamp)
        internal
        pure
        returns (uint96 end, uint256 stakedDuration)
    {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L35

```solidity
File: NFTPositionInfo.sol

21: function getPositionInfo(
        IUniswapV3Factory factory,
        INonfungiblePositionManager nonfungiblePositionManager,
        uint256 tokenId
    ) internal view returns (IUniswapV3Pool pool, int24 tickLower, int24 tickUpper, uint128 liquidity) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L21

```solidity
File: RewardMath.sol

20: function computeBoostedSecondsInsideX128(
        uint256 stakedDuration,
        uint128 liquidity,
        uint128 boostAmount,
        uint128 boostTotalSupply,
        uint160 secondsPerLiquidityInsideInitialX128,
        uint160 secondsPerLiquidityInsideX128
    ) internal pure returns (uint160 boostedSecondsInsideX128) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/RewardMath.sol#L20

```solidity
File: RewardMath.sol

56: function computeBoostedRewardAmount(
        uint256 totalRewardUnclaimed,
        uint160 totalSecondsClaimedX128,
        uint256 startTime,
        uint256 endTime,
        uint160 secondsInsideX128,
        uint256 currentTime
    ) internal pure returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/RewardMath.sol#L56



</details>




### <a href="#qa-summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters
The following events should also add the previous original value in addition to the new value.

#### <ins>Proof Of Concept</ins>


```solidity
File: IUniswapV3Gauge.sol

68: event NewMinimumWidth(uint24 minimumWidth);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/interfaces/IUniswapV3Gauge.sol#L68

```solidity
File: IFlywheelCore.sol

140: event FlywheelRewardsUpdate(address indexed newFlywheelRewards);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelCore.sol#L140

```solidity
File: IFlywheelCore.sol

146: event FlywheelBoosterUpdate(address indexed newBooster);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/interfaces/IFlywheelCore.sol#L146

```solidity
File: IRootPort.sol

319: event NewChainAdded(uint24 indexed chainId);
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L319




#### <ins>Recommended Mitigation Steps</ins>
The events should include the new value and old value where possible.






### <a href="#qa-summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Operations such as the changing of the owner should be behind a timelock

From the point of view of a user, the changing of the owner of a contract is a high risk operation that may have outcomes ranging from an attacker gaining control over the protocol, to the function no longer functioning due to a typo in the destination address. To give users plenty of warning so that they can validate any ownership changes, changes of ownership should be behind a timelock.

#### <ins>Proof Of Concept</ins>


```solidity
File: UlyssesFactory.sol

65: function renounceOwnership(

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L65

```solidity
File: BranchPort.sol

113: function renounceOwnership(

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L113

```solidity
File: RootPort.sol

165: function renounceOwnership(

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L165






### <a href="#qa-summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Using `>`/`>=` without specifying an upper bound is unsafe

There will be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC4626.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626.sol#L2

```solidity
File: ERC4626DepositOnly.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626DepositOnly.sol#L2

```solidity
File: ERC4626MultiToken.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/ERC4626MultiToken.sol#L2

```solidity
File: UlyssesERC4626.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/UlyssesERC4626.sol#L2

```solidity
File: IERC4626.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-4626/interfaces/IERC4626.sol#L2

```solidity
File: TalosStrategyStaked.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L2

```solidity
File: TalosStrategyVanilla.sol

3: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L3

```solidity
File: TalosBaseStrategy.sol

3: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L3

```solidity
File: BoostAggregator.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L2

```solidity
File: IBoostAggregator.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/interfaces/IBoostAggregator.sol#L2

```solidity
File: TalosStrategySimple.sol

2: pragma solidity >=0.8.0;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/strategies/TalosStrategySimple.sol#L2



</details>



### <a href="#qa-summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> Private variables dont respect naming convention

All private variables should start with `_` but following do not respect the naming convetion.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: FlywheelCore.sol

148: uint256 private constant ONE = 1e18;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L148

```solidity
File: TalosStrategyStaked.sol

52: bool private stakeFlag = false;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L52

```solidity
File: TalosStrategyVanilla.sol

47: uint24 private constant protocolFee = 2 * 1e5; //20%

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L47

```solidity
File: BoostAggregator.sol

56: uint256 private constant DIVISIONER = 10000;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L56

```solidity
File: UlyssesPool.sol

65: uint256 private constant DIVISIONER = 1 ether;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L65



</details>





### <a href="#qa-summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: bHermes.sol

function claimOutstanding() public virtual {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/bHermes.sol#L96

```solidity
File: bHermes.sol

function totalAssets() public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/bHermes.sol#L115

```solidity
File: bHermes.sol

function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/bHermes.sol#L158

```solidity
File: vMaia.sol

function claimBoost(uint256 amount) public override {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/vMaia.sol#L91

```solidity
File: TalosStrategyStaked.sol

function createTalosV3Strategy(
        IUniswapV3Pool pool,
        ITalosOptimizer optimizer,
        BoostAggregator boostAggregator,
        address strategyManager,
        FlywheelCoreInstant flywheel,
        address owner
    ) public returns (TalosBaseStrategy) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L20

```solidity
File: TalosStrategyStaked.sol

function transferFrom(address _from, address _to, uint256 _amount) public override returns (bool) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L94

```solidity
File: TalosStrategyVanilla.sol

function createTalosV3Vanilla(
        IUniswapV3Pool pool,
        ITalosOptimizer optimizer,
        INonfungiblePositionManager nonfungiblePositionManager,
        address strategyManager,
        address owner
    ) public returns (TalosBaseStrategy) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L22

```solidity
File: PoolVariables.sol

function checkDeviation(IUniswapV3Pool pool, int24 maxTwapDeviation, uint32 twapDuration) public view {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L199

```solidity
File: UlyssesPool.sol

function totalAssets() public view override returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L102

```solidity
File: UlyssesPool.sol

function maxRedeem(address owner) public view override returns (uint256) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L107

```solidity
File: UlyssesToken.sol

function totalAssets() public view override returns (uint256 _totalAssets) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesToken.sol#L39

```solidity
File: UlyssesFactory.sol

function deployPool(
        uint256 id,
        address asset,
        string calldata name,
        string calldata symbol,
        address owner,
        address factory
    ) public returns (UlyssesPool) {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L25

```solidity
File: UlyssesFactory.sol

function renounceOwnership() public payable override onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/factories/UlyssesFactory.sol#L65

```solidity
File: BranchPort.sol

function renounceOwnership() public payable override onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchPort.sol#L113

```solidity
File: RootPort.sol

function renounceOwnership() public payable override onlyOwner {
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L165



</details>



### <a href="#qa-summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Empty blocks should be removed or emit something

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: bHermes.sol

62: {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/bHermes.sol#L62

```solidity
File: BaseV2Minter.sol

152: try flywheelGaugeRewards.queueRewardsForCycle() {} catch {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L152

```solidity
File: vMaia.sol

91: function claimBoost(uint256 amount) public override {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/vMaia.sol#L91

```solidity
File: FlywheelCoreInstant.sol

38: ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreInstant.sol#L38

```solidity
File: FlywheelCoreStrategy.sol

37: ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreStrategy.sol#L37

```solidity
File: FlywheelBribeRewards.sol

29: {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelBribeRewards.sol#L29

```solidity
File: TalosManager.sol

96: try strategy.pool().checkDeviation(optimizer.maxTwapDeviation(), optimizer.twapDuration()) {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosManager.sol#L96

```solidity
File: TalosStrategyStaked.sol

179: } catch {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L179

```solidity
File: TalosStrategyVanilla.sol

64: ) TalosStrategySimple(_pool, _optimizer, _nonfungiblePositionManager, _strategyManager, _owner) {}
79: function afterRedeem(uint256 _tokenId) internal override {}
90: function afterDeposit(uint256 _tokenId) internal override {}
100: function afterRerange(uint256 _tokenId) internal override {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L64

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L79

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L90

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L100



```solidity
File: TalosStrategyVanillaFactory.sol

26: {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L26

```solidity
File: TalosStrategySimple.sol

24: ) TalosBaseStrategy(_pool, _strategy, _nonfungiblePositionManager, _strategyManager, _owner) {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/strategies/TalosStrategySimple.sol#L24

```solidity
File: ArbitrumBranchBridgeAgent.sol

90: {}
181: {}
181: function _replenishGas(uint256) internal override {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L90

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L181

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L181



```solidity
File: ArbitrumCoreBranchRouter.sol

40: {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L40

```solidity
File: BranchBridgeAgent.sol

1328: if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
1419: fallback() external payable {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419



```solidity
File: CoreBranchRouter.sol

284: fallback() external payable {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L284

```solidity
File: RootBridgeAgent.sol

1240: if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}
1334: fallback() external payable {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1240

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1334



```solidity
File: ArbitrumBranchBridgeAgentFactory.sol

52: {}

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L52



</details>




### <a href="#qa-summary">[NC&#x2011;21]</a><a name="NC&#x2011;21"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


```solidity
File: FlywheelCore.sol

128: address(newFlywheelRewards)
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/base/FlywheelCore.sol#L128

```solidity
File: TalosBaseStrategy.sol

339: int256(amount0)
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L339

```solidity
File: TalosBaseStrategy.sol

340: int256(amount1)
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/base/TalosBaseStrategy.sol#L340

```solidity
File: RootPort.sol

160: address(_owner)
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootPort.sol#L160






### <a href="#qa-summary">[NC&#x2011;22]</a><a name="NC&#x2011;22"> Remove unused contract variables

Note that there may be cases where a variable appears to be used, but this is only because there are multiple definitions of the varible in different files. In such cases, the variable definition should be moved into a separate file. The instances below are the unused variables.

#### <ins>Proof Of Concept</ins>


```solidity
File: AnycallFlags.sol

FLAG_MERGE_CONFIG_FLAGS
FLAG_EXEC_FALLBACK
FLAG_PAY_FEE_ON_DEST
FLAG_NONE
FLAG_EXEC_START_VALUE

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/lib/AnycallFlags.sol






### <a href="#qa-summary">[NC&#x2011;23]</a><a name="NC&#x2011;23"> Remove unused `error` definition

Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.

#### <ins>Proof Of Concept</ins>


```solidity
File: IRootPort.sol

error UnrecognizedStrategyManager
error FeeError
error AlreadyExecutedTransaction
error InvalidChain
error UnrecognizedGlobalAddress
error UnrecognizedAddressInDestination
error UnknowHTokenFactory

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/interfaces/IRootPort.sol








### <a href="#qa-summary">[NC&#x2011;24]</a><a name="NC&#x2011;24"> Using underscore at the end of variable name

The use of underscore at the end of the variable name is uncommon and also suggests that the variable name was not completely changed. Consider refactoring `variableName_` to `variableName`.

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

82: votingPeriod_;
83: votingDelay_;
84: proposalThreshold_;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L82-L84

```solidity
File: GovernorBravoDelegator.sol

34: admin_;
48: implementation_;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L34

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L48



```solidity
File: GovernorBravoDelegator.sol

33: admin_;
47: implementation_;

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L33

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L47








### <a href="#qa-summary">[NC&#x2011;25]</a><a name="NC&#x2011;25"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: ERC20MultiVotes.sol

367: abi.encodePacked(
                    "/x19/x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
                )
            ),
            v,
            r,
            s
        )

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L367

```solidity
File: GovernorBravoDelegateMaia.sol

348: bytes32 digest = keccak256(abi.encodePacked("/x19/x01", domainSeparator, structHash))

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L348

```solidity
File: ArbitrumCoreBranchRouter.sol

56: bytes memory packedData = abi.encodePacked(bytes1(0x02), data)
101: bytes memory packedData = abi.encodePacked(bytes1(0x04), data)

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L56

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L101



```solidity
File: BranchBridgeAgent.sol

226: bytes memory packedData = abi.encodePacked(
            bytes1(0x04), msg.sender, depositNonce, _params, msg.value.toUint128(), _remoteExecutionGas
        )
245: bytes memory packedData = abi.encodePacked(
            bytes1(0x05),
            msg.sender,
            depositNonce,
            _dParams.hToken,
            _dParams.token,
            _dParams.amount,
            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
            _dParams.toChain,
            _params,
            msg.value.toUint128(),
            _remoteExecutionGas
        )
288: bytes memory packedData = abi.encodePacked(
            bytes1(0x06),
            msg.sender,
            uint8(_dParams.hTokens.length),
            depositNonce,
            _dParams.hTokens,
            _dParams.tokens,
            _dParams.amounts,
            _deposits,
            _dParams.toChain,
            _params,
            msg.value.toUint128(),
            _remoteExecutionGas
        )
334: packedData = abi.encodePacked(
                    bytes1(0x05),
                    msg.sender,
                    _depositNonce,
                    getDeposit[_depositNonce].hTokens[0],
                    getDeposit[_depositNonce].tokens[0],
                    getDeposit[_depositNonce].amounts[0],
                    _normalizeDecimals(
                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
                    ),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                )
350: packedData = abi.encodePacked(
                    bytes1(0x02),
                    _depositNonce,
                    getDeposit[_depositNonce].hTokens[0],
                    getDeposit[_depositNonce].tokens[0],
                    getDeposit[_depositNonce].amounts[0],
                    _normalizeDecimals(
                        getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
                    ),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                )
370: packedData = abi.encodePacked(
                    bytes1(0x06),
                    msg.sender,
                    uint8(getDeposit[_depositNonce].hTokens.length),
                    nonce,
                    getDeposit[nonce].hTokens,
                    getDeposit[nonce].tokens,
                    getDeposit[nonce].amounts,
                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                )
385: packedData = abi.encodePacked(
                    bytes1(0x03),
                    uint8(getDeposit[nonce].hTokens.length),
                    _depositNonce,
                    getDeposit[nonce].hTokens,
                    getDeposit[nonce].tokens,
                    getDeposit[nonce].amounts,
                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                )
425: bytes memory packedData = abi.encodePacked(
            bytes1(0x07), depositNonce++, _settlementNonce, msg.value.toUint128(), _gasToBoostSettlement
        )
435: bytes memory packedData = abi.encodePacked(bytes1(0x08), _depositNonce, msg.value.toUint128(), uint128(0))
481: abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas)
657: abi.encodePacked(bytes1(0x01), depositNonce, _params, _gasToBridgeOut, _remoteExecutionGas)
681: bytes memory packedData = abi.encodePacked(
            bytes1(0x02),
            depositNonce,
            _dParams.hToken,
            _dParams.token,
            _dParams.amount,
            _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
            _dParams.toChain,
            _params,
            _gasToBridgeOut,
            _remoteExecutionGas
        )
724: bytes memory packedData = abi.encodePacked(
            bytes1(0x03),
            uint8(_dParams.hTokens.length),
            depositNonce,
            _dParams.hTokens,
            _dParams.tokens,
            _dParams.amounts,
            deposits,
            _dParams.toChain,
            _params,
            _gasToBridgeOut,
            _remoteExecutionGas
        )

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L226

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L245

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L288

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L334

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L350

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L370

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L385

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L425

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L435

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L481

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L657

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L681

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L724



```solidity
File: CoreBranchRouter.sol

51: bytes memory packedData = abi.encodePacked(bytes1(0x01), data)
75: bytes memory packedData = abi.encodePacked(bytes1(0x02), data)
108: bytes memory packedData = abi.encodePacked(bytes1(0x03), data)
151: bytes memory packedData = abi.encodePacked(bytes1(0x04), data)

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L51

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L75

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L108

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L151



```solidity
File: CoreRootRouter.sol

112: bytes memory packedData = abi.encodePacked(bytes1(0x02), data)
163: bytes memory packedData = abi.encodePacked(bytes1(0x01), data)
241: bytes memory packedData = abi.encodePacked(bytes1(0x03), data)
262: bytes memory packedData = abi.encodePacked(bytes1(0x04), data)
285: bytes memory packedData = abi.encodePacked(bytes1(0x05), data)
312: bytes memory packedData = abi.encodePacked(bytes1(0x06), data)

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L112

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L163

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L241

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L262

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L285

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L312



```solidity
File: RootBridgeAgent.sol

278: abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain))
306: bytes memory data = abi.encodePacked(
            bytes1(0x01),
            _recipient,
            settlementNonce,
            localAddress,
            underlyingAddress,
            _amount,
            _deposit,
            _data,
            _manageGasOut(_toChain)
        )
359: bytes memory data = abi.encodePacked(
            bytes1(0x02),
            _recipient,
            uint8(hTokens.length),
            settlementNonce,
            hTokens,
            tokens,
            _amounts,
            _deposits,
            _data,
            _manageGasOut(_toChain)
        )
558: bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain))

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L278

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L306

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L359

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L558





</details>


#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#qa-summary">[NC&#x2011;26]</a><a name="NC&#x2011;26"> Use `""` instead of `"0x00"` for empty bytes to avoid triggering extra computation on transfers

When tokens with hooks on transfer take in `bytes` arguments, there is often a length check so that 0 length bytes inputs are skipped. By using `"0x00"`, a non-0 `bytes` value is created which is semantically empty but would fail the 0-length check. This would increase the gas cost of processing such transfers.



#### <ins>Proof Of Concept</ins>


<details>

```solidity
File: UlyssesPool.sol

384: let bandwidthStateListStart := keccak256(0x00, 0x20)

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L384

```solidity
File: BranchBridgeAgent.sol

481: abi.encodePacked(bytes1(0x00), depositNonce, _params, gasToBridgeOut, _remoteExecutionGas);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L481

```solidity
File: BranchBridgeAgent.sol

1143: if (flag == 0x00) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1143

```solidity
File: BranchBridgeAgent.sol

1243: if ((flag == 0x00) || (flag == 0x01) || (flag == 0x02)) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1243

```solidity
File: RootBridgeAgent.sol

278: abi.encodePacked(bytes1(0x00), _recipient, settlementNonce++, _data, _manageGasOut(_toChain));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L278

```solidity
File: RootBridgeAgent.sol

914: if (flag == 0x00) {
1197: if (flag == 0x00) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L914

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1197





</details>

#### <ins>Recommended Mitigation Steps</ins>

Consider changing the value to `""` instead, unless there is some other reason to retain the bytes value as-is.



### <a href="#qa-summary">[NC&#x2011;27]</a><a name="NC&#x2011;27"> Use @inheritdoc rather than using a non-standard annotation

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ERC20MultiVotes.sol

63: /// @dev Lookup a value in a list of (sorted) checkpoints.
    function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 blockNumber) private view returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L63

```solidity
File: BaseV2Gauge.sol

129: /// @dev Can't add existing flywheel (active or not)
        if (added[bribeFlywheel]) revert FlywheelAlreadyAdded();

        address flyWheelRewards = address(bribeFlywheel.flywheelRewards());
        FlywheelBribeRewards(flyWheelRewards).setRewardsDepot(multiRewardsDepot);

        multiRewardsDepot.addAsset(flyWheelRewards, bribeFlywheel.rewardToken());
        bribeFlywheels.push(bribeFlywheel);
        isActive[bribeFlywheel] = true;
        added[bribeFlywheel] = true;

        emit AddedBribeFlywheel(bribeFlywheel);
    }

    /// @inheritdoc IBaseV2Gauge
    function removeBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/gauges/BaseV2Gauge.sol#L129

```solidity
File: GovernorBravoDelegator.sol

66: /**
     * @dev Delegates execution to an implementation contract.
     * It returns to the external caller whatever the implementation returns
     * or forwards reverts.
     */
    fallback() external payable {
        // delegate all other functions to current implementation
        (bool success,) = implementation.delegatecall(msg.data);

        assembly {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegator.sol#L66

```solidity
File: BaseV2Minter.sol

15: /// @title Base V2 Minter - Mints HERMES tokens for the B(3,3) system
contract BaseV2Minter is Ownable, IBaseV2Minter {
    using SafeTransferLib for address;

    /*//////////////////////////////////////////////////////////////
                         MINTER STATE
    //////////////////////////////////////////////////////////////*/

    /// @dev allows minting once per week (reset every Thursday 00:00 UTC)
    uint256 internal constant week = 86400 * 7;
    /// @dev 2% per week target emission
    uint256 internal constant base = 1000;

    uint256 internal constant max_tail_emission = 100;
    uint256 internal constant max_dao_share = 300;

    /// @inheritdoc IBaseV2Minter
    address public immutable override underlying;
    /// @inheritdoc IBaseV2Minter
    ERC4626 public immutable override vault;

    /// @inheritdoc IBaseV2Minter
    FlywheelGaugeRewards public override flywheelGaugeRewards;
    /// @inheritdoc IBaseV2Minter
    address public override dao;

    /// @inheritdoc IBaseV2Minter
    uint256 public override daoShare = 100;
    uint256 public override tailEmission = 20;
    /// @inheritdoc IBaseV2Minter

    /// @inheritdoc IBaseV2Minter
    uint256 public override weekly;
    /// @inheritdoc IBaseV2Minter
    uint256 public override activePeriod;

    address internal initializer;

    constructor(
        address _vault, // the B(3,3) system that will be locked into
        address _dao,
        address _owner
    ) {
        _initializeOwner(_owner);
        initializer = msg.sender;
        dao = _dao;
        underlying = address(ERC4626(_vault).asset());
        vault = ERC4626(_vault);
    }

    /*//////////////////////////////////////////////////////////////
                         FALLBACK LOGIC
    //////////////////////////////////////////////////////////////*/

    fallback() external {
        updatePeriod();
    }

    /*//////////////////////////////////////////////////////////////
                         ADMIN LOGIC
    //////////////////////////////////////////////////////////////*/

    /// @inheritdoc IBaseV2Minter
    function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {
87: /// @dev DAO can be set to address(0) to disable DAO rewards.
        dao = _dao;
    }

    /// @inheritdoc IBaseV2Minter
    function setDaoShare(uint256 _daoShare) external onlyOwner {
135: /// @dev share of newWeeklyEmission emissions sent to DAO.
            uint256 share = (_required * daoShare) / base;
            _required += share;
            uint256 _balanceOf = underlying.balanceOf(address(this));
            if (_balanceOf < _required) {
                HERMES(underlying).mint(address(this), _required - _balanceOf);
            }

            underlying.safeTransfer(address(vault), _growth);

            if (dao != address(0)) underlying.safeTransfer(dao, share);

            emit Mint(msg.sender, newWeeklyEmission, _circulatingSupply, _growth, share);

            /// @dev queue rewards for the cycle, anyone can call if fails
            ///      queueRewardsForCycle will call this function but won't enter
            ///      here because activePeriod was updated
            try flywheelGaugeRewards.queueRewardsForCycle() {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L15

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L87

https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/minters/BaseV2Minter.sol#L135



```solidity
File: PartnerUtilityManager.sol

74: /// @dev Vault applies outstanding weight.
        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
            IBaseVault(partnerVault).applyWeight();
        }
    }

    /// @inheritdoc IUtilityManager
    function forfeitBoost(uint256 amount) public virtual override {
84: /// @dev Vault applies outstanding boost.
        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
            IBaseVault(partnerVault).applyBoost();
        }
    }

    /// @inheritdoc IUtilityManager
    function forfeitGovernance(uint256 amount) public virtual override {
94: /// @dev Vault applies outstanding governance.
        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
            IBaseVault(partnerVault).applyGovernance();
        }
    }

    /// @inheritdoc IPartnerUtilityManager
    function forfeitPartnerGovernance(uint256 amount) public {
103: /// @dev partnerGovernance is kept in this contract and not sent to vaults to avoid governance attacks.
        address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);
    }

    /// @inheritdoc IUtilityManager
    function claimMultiple(uint256 amount) public virtual override {
129: /// @dev Must transfer weight amount to this manager address.
        if (weightAvailable < amount) {
            IBaseVault(partnerVault).clearWeight(amount - weightAvailable);
        }

        super.claimWeight(amount);
    }

    /// @inheritdoc IUtilityManager
    function claimBoost(uint256 amount) public virtual override checkBoost(amount) {
140: /// @dev Must transfer boost amount to this manager address.
        if (boostAvailable < amount) IBaseVault(partnerVault).clearBoost(amount - boostAvailable);

        super.claimBoost(amount);
    }

    /// @inheritdoc IUtilityManager
    function claimGovernance(uint256 amount) public virtual override checkGovernance(amount) {
149: /// @dev Must transfer governance amount to this manager address.
        if (governanceAvailable < amount) {
            IBaseVault(partnerVault).clearGovernance(amount - governanceAvailable);
        }

        super.claimGovernance(amount);
    }

    /// @inheritdoc IPartnerUtilityManager
    function claimPartnerGovernance(uint256 amount) public checkPartnerGovernance(amount) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L74

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L84

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L94

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L103

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L129

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L140

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/PartnerUtilityManager.sol#L149



```solidity
File: DateTimeLib.sol

42: /// @solidity memory-safe-assembly
        assembly {
            epochDay := add(epochDay, 719468)
            let doe := mod(epochDay, 146097)
            let yoe := div(sub(sub(add(doe, div(doe, 36524)), div(doe, 1460)), eq(doe, 146096)), 365)
            let doy := sub(doe, sub(add(mul(365, yoe), shr(2, yoe)), div(yoe, 100)))
            let mp := div(add(mul(5, doy), 2), 153)
            month := sub(add(mp, 3), mul(gt(mp, 9), 12))
        }
    }

    /// @dev Returns the weekday from the unix timestamp.
    /// Monday: 1, Tuesday: 2, ....., Sunday: 7.
    function isTuesday(uint256 timestamp) internal pure returns (bool result, uint256 startOfDay) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/maia/libraries/DateTimeLib.sol#L42

```solidity
File: GovernorBravoDelegator.sol

65: /**
     * @dev Delegates execution to an implementation contract.
     * It returns to the external caller whatever the implementation returns
     * or forwards reverts.
     */
    fallback() external payable {
        // delegate all other functions to current implementation
        (bool success,) = implementation.delegatecall(msg.data);

        assembly {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/out-of-scope/governance/GovernorBravoDelegator.sol#L65

```solidity
File: FlywheelBoosterGaugeWeight.sol

58: /// @inheritdoc IFlywheelBooster
    /// @dev User's gauge weight allocated to the strategy
    function boostedBalanceOf(ERC20 strategy, address user) external view returns (uint256) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L58

```solidity
File: FlywheelGaugeRewards.sol

73: /// @dev Update minter cycle and queue rewars if needed.
        /// This will make this call fail if it is a new epoch, because the minter calls this function, the first call would fail with "CycleError()".
        /// Should be called through Minter to kickoff new epoch.
        minter.updatePeriod();

        // next cycle is always the next even divisor of the cycle length above current block timestamp.
        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
        uint32 lastCycle = gaugeCycle;

        // ensure new cycle has begun
        if (currentCycle <= lastCycle) revert CycleError();

        gaugeCycle = currentCycle;

        // queue the rewards stream and sanity check the tokens were received
        uint256 balanceBefore = rewardToken.balanceOf(address(this));
        totalQueuedForCycle = minter.getRewards();
        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);

        // include uncompleted cycle
        totalQueuedForCycle += nextCycleQueuedRewards;

        // iterate over all gauges and update the rewards allocations
        address[] memory gauges = gaugeToken.gauges();

        _queueRewards(gauges, currentCycle, lastCycle, totalQueuedForCycle);

        nextCycleQueuedRewards = 0;
        paginationOffset = 0;

        emit CycleStart(currentCycle, totalQueuedForCycle);
    }

    /// @inheritdoc IFlywheelGaugeRewards
    function queueRewardsForCyclePaginated(uint256 numRewards) external {
108: /// @dev Update minter cycle and queue rewars if needed.
        /// This will make this call fail if it is a new epoch, because the minter calls this function, the first call would fail with "CycleError()".
        /// Should be called through Minter to kickoff new epoch.
        minter.updatePeriod();

        // next cycle is always the next even divisor of the cycle length above current block timestamp.
        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
        uint32 lastCycle = gaugeCycle;

        // ensure new cycle has begun
        if (currentCycle <= lastCycle) revert CycleError();

        if (currentCycle > nextCycle) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L73

https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L108



```solidity
File: BoostAggregator.sol

120: /// @dev protocol rewards stay in stake contract
            protocolRewards += newProtocolRewards;
            pendingRewards -= newProtocolRewards;

            address rewardsDepot = userToRewardsDepot[user];
            if (rewardsDepot != address(0)) {
                // claim rewards to user's rewardsDepot
                uniswapV3Staker.claimReward(rewardsDepot, pendingRewards);
            } else {
                // claim rewards to user
                uniswapV3Staker.claimReward(user, pendingRewards);
            }
        }

        // withdraw rewards from Uniswap V3 Staker
        uniswapV3Staker.withdrawToken(tokenId, user, "");
    }

    /*//////////////////////////////////////////////////////////////
                            ADMIN LOGIC
    //////////////////////////////////////////////////////////////*/

    /// @inheritdoc IBoostAggregator
    function addWhitelistedAddress(address user) external onlyOwner {
166: /// @dev May run out of gas.
        hermesGaugeBoost.decrementAllGaugesAllBoost();
        address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));
    }

    /// @inheritdoc IBoostAggregator
    function withdrawGaugeBoost(address to, uint256 amount) external onlyOwner {
173: /// @dev May run out of gas.
        hermesGaugeBoost.decrementAllGaugesBoost(amount);
        hermesGaugeBoost.updateUserBoost(address(this));
        address(hermesGaugeBoost).safeTransfer(to, amount);
    }

    /// @inheritdoc IBoostAggregator
    function decrementGaugesBoostIndexed(uint256 boost, uint256 offset, uint256 num) external onlyOwner {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L120

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L166

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/boost-aggregator/BoostAggregator.sol#L173



```solidity
File: PoolVariables.sol

94: /// @dev Rounds tick down towards negative infinity so that it's a multiple
    /// of `tickSpacing`.
    function floor(int24 tick, int24 tickSpacing) internal pure returns (int24) {
167: /// @dev Calc base ticks depending on base threshold and tickspacing
    function baseTicks(int24 currentTick, int24 baseThreshold, int24 tickSpacing)
        private
        pure
        returns (int24 tickLower, int24 tickUpper)
    {
206: /// @dev Fetches time-weighted average price in ticks from Uniswap pool for specified duration
    function getTwap(IUniswapV3Pool pool, uint32 twapDuration) private view returns (int24) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L94

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L167

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol#L206



```solidity
File: UlyssesPool.sol

113: /**
         * @dev bandwidthStateList first element has always 0 bandwidth
         *      so this line will never fail and return 0 instead
         */
        return bandwidthStateList[destinations[destinationId]].bandwidth;
    }

    /// @inheritdoc IUlyssesPool
    function getBandwidthStateList() external view returns (BandwidthState[] memory) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol#L113

```solidity
File: MulticallRootRouter.sol

164: /// @inheritdoc IRootRouter
    /// @dev This function will revert when called.
    function anyExecuteResponse(bytes1, bytes calldata, uint24)
        external
        payable
        override
        returns (bool, bytes memory)
    {
175: /**
     *  @inheritdoc IRootRouter
     *  @dev FuncIDs
     *
     *  FUNC ID      | FUNC NAME
     *  0x01         |  multicallNoOutput
     *  0x02         |  multicallSingleOutput
     *  0x03         |  multicallMultipleOutput
     *
     */
    function anyExecute(bytes1 funcId, bytes calldata encodedData, uint24)
        external
        payable
        override
        lock
        requiresExecutor
        returns (bool, bytes memory)
    {
258: /**
     *  @inheritdoc IRootRouter
     *  @dev FuncIDs
     *
     *  FUNC ID      | FUNC NAME
     *  0x01         |  multicallNoOutput
     *  0x02         |  multicallSingleOutput
     *  0x03         |  multicallMultipleOutput
     *
     */
    function anyExecuteSigned(bytes1 funcId, bytes calldata encodedData, address userAccount, uint24)
        external
        payable
        override
        lock
        requiresExecutor
        returns (bool, bytes memory)
    {
327: /// UNRECOGNIZED FUNC ID
        } else {
            return (false, "FuncID not recognized!");
        }

        return (true, "");
    }

    /**
     *  @inheritdoc IRootRouter
     *  @dev FuncIDs
     *
     *  FUNC ID      | FUNC NAME
     *  0x01         |  multicallNoOutput
     *  0x02         |  multicallSingleOutput
     *  0x03         |  multicallMultipleOutput
     *
     */
    function anyExecuteSignedDepositSingle(
        bytes1 funcId,
        bytes calldata encodedData,
        DepositParams calldata,
        address userAccount,
        uint24
    ) external payable override requiresExecutor lock returns (bool success, bytes memory result) {
403: /// UNRECOGNIZED FUNC ID
        } else {
            return (false, "FuncID not recognized!");
        }

        return (true, "");
    }

    /**
     *  @inheritdoc IRootRouter
     *  @dev FuncIDs
     *
     *  FUNC ID      | FUNC NAME
     *  0x01         |  multicallNoOutput
     *  0x02         |  multicallSingleOutput
     *  0x03         |  multicallMultipleOutput
     *
     */
    function anyExecuteSignedDepositMultiple(
        bytes1 funcId,
        bytes memory encodedData,
        DepositMultipleParams calldata,
        address userAccount,
        uint24
    ) external payable requiresExecutor lock returns (bool success, bytes memory result) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L164

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L175

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L258

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L327

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/MulticallRootRouter.sol#L403



```solidity
File: UniswapV3Staker.sol

215: /// @dev Upon receiving a Uniswap V3 ERC721, create the token deposit and
    ///      _stakes in current incentive setting owner to `from`.
    /// @inheritdoc IERC721Receiver
    function onERC721Received(address, address from, uint256 tokenId, bytes calldata)
        external
        override
        returns (bytes4)
    {
475: /// @dev Stakes a deposited token without doing an already staked in another position check
    function _stakeToken(uint256 tokenId, IUniswapV3Pool pool, int24 tickLower, int24 tickUpper, uint128 liquidity)
        private
    {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L215

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L475



```solidity
File: IncentiveTime.sol

18: /// @dev The start of the incentive is the start of the week (Thursday 12:00:00 UTC) that the timestamp falls in
        /// Remove Offset, rounds down to nearest week, adds offset back
        return uint96(((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) * INCENTIVES_DURATION + INCENTIVES_OFFSET);
    }

    function computeEnd(uint256 timestamp) internal pure returns (uint96 end) {
24: /// @dev The end of the incentive is the end of the week (Thursday 12:00:00 UTC) that the timestamp falls in
        /// Remove Offset, rounds up to nearest week, adds offset back
        return uint96(
            (((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) + 1) * INCENTIVES_DURATION + INCENTIVES_OFFSET
        );
    }

    function getEnd(uint96 start) internal pure returns (uint96 end) {

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L18

https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L24





</details>



### <a href="#qa-summary">[NC&#x2011;28]</a><a name="NC&#x2011;28"> Use named function calls

Code base has an extensive use of named function calls, but it somehow missed one instance where this would be appropriate.

It should use named function calls on function call, as such:
```solidity
    library.exampleFunction{value: _data.amount.value}({
        _id: _data.id,
        _amount: _data.amount.value,
        _token: _data.token,
        _example: "",
        _metadata: _data.metadata
    });
```

#### <ins>Proof Of Concept</ins>


```solidity
File: GovernorBravoDelegateMaia.sol

216: timelock.executeTransaction{value: proposal.values[i]}(
                proposal.targets[i], proposal.values[i], proposal.signatures[i], proposal.calldatas[i], proposal.eta
            );

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoDelegateMaia.sol#L216










### <a href="#qa-summary">[NC&#x2011;29]</a><a name="NC&#x2011;29"> Cast to `bytes` or `bytes32` for clearer semantic meaning

Using a <a href="https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739">cast</a> on a single argument, rather than abi.encodePacked() makes the intended operation more clear, leading to less reviewer confusion.

#### <ins>Proof Of Concept</ins>

<details>

```solidity
File: ArbitrumCoreBranchRouter.sol

56: bytes memory packedData = abi.encodePacked(bytes1(0x02), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L56

```solidity
File: ArbitrumCoreBranchRouter.sol

101: bytes memory packedData = abi.encodePacked(bytes1(0x04), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L101

```solidity
File: CoreBranchRouter.sol

51: bytes memory packedData = abi.encodePacked(bytes1(0x01), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L51

```solidity
File: CoreBranchRouter.sol

75: bytes memory packedData = abi.encodePacked(bytes1(0x02), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L75

```solidity
File: CoreBranchRouter.sol

108: bytes memory packedData = abi.encodePacked(bytes1(0x03), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L108

```solidity
File: CoreBranchRouter.sol

151: bytes memory packedData = abi.encodePacked(bytes1(0x04), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L151

```solidity
File: CoreRootRouter.sol

112: bytes memory packedData = abi.encodePacked(bytes1(0x02), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L112

```solidity
File: CoreRootRouter.sol

163: bytes memory packedData = abi.encodePacked(bytes1(0x01), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L163

```solidity
File: CoreRootRouter.sol

241: bytes memory packedData = abi.encodePacked(bytes1(0x03), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L241

```solidity
File: CoreRootRouter.sol

262: bytes memory packedData = abi.encodePacked(bytes1(0x04), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L262

```solidity
File: CoreRootRouter.sol

285: bytes memory packedData = abi.encodePacked(bytes1(0x05), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L285

```solidity
File: CoreRootRouter.sol

312: bytes memory packedData = abi.encodePacked(bytes1(0x06), data);

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L312

```solidity
File: RootBridgeAgent.sol

558: bytes memory newGas = abi.encodePacked(_manageGasOut(settlement.toChain));

```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol#L558



</details>



