
## Summary

### Low Risk Issues

| |Issue|Instances|
|-|:-|:-:|
| [[L&#x2011;01](#l01-initialization-can-be-front-run)] | Initialization can be front-run | 10 | 
| [[L&#x2011;02](#l02-tokens-may-be-minted-to-address0x0)] | Tokens may be minted to `address(0x0)` | 10 | 
| [[L&#x2011;03](#l03-decimals-is-not-a-part-of-the-erc-20-standard)] | `decimals()` is not a part of the ERC-20 standard | 20 | 
| [[L&#x2011;04](#l04-name-is-not-a-part-of-the-erc-20-standard)] | `name()` is not a part of the ERC-20 standard | 4 | 
| [[L&#x2011;05](#l05-symbol-is-not-a-part-of-the-erc-20-standard)] | `symbol()` is not a part of the ERC-20 standard | 4 | 
| [[L&#x2011;06](#l06-file-allows-a-version-of-solidity-that-is-susceptible-to-an-assembly-optimizer-bug)] | File allows a version of solidity that is susceptible to an assembly optimizer bug | 1 | 
| [[L&#x2011;07](#l07-solidity-version-0820-may-not-work-on-other-chains-due-to-push0)] | Solidity version 0.8.20 may not work on other chains due to `PUSH0` | 153 | 

Total: 202 instances over 7 issues

### Non-critical Issues

| |Issue|Instances|
|-|:-|:-:|
| [[N&#x2011;01](#n01-duplicate-import-statements)] | Duplicate import statements | 7 | 
| [[N&#x2011;02](#n02-override-function-arguments-that-are-unused-should-have-the-variable-name-removed-or-commented-out-to-avoid-compiler-warnings)] | `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings | 5 | 
| [[N&#x2011;03](#n03-non-assembly-method-available)] | Non-assembly method available | 1 | 
| [[N&#x2011;04](#n04-inconsistent-method-of-specifying-a-floating-pragma)] | Inconsistent method of specifying a floating pragma | 11 | 
| [[N&#x2011;05](#n05-using--without-specifying-an-upper-bound-is-unsafe)] | Using `>`/`>=` without specifying an upper bound is unsafe | 11 | 
| [[N&#x2011;06](#n06-natspec-param-is-listed-multiple-times)] | NatSpec `@param` is listed multiple times | 2 | 
| [[N&#x2011;07](#n07-avoid-the-use-of-sensitive-terms)] | Avoid the use of sensitive terms | 33 | 
| [[N&#x2011;08](#n08-large-assembly-blocks-should-have-extensive-comments)] | Large assembly blocks should have extensive comments | 2 | 
| [[N&#x2011;09](#n09-visibility-should-be-set-explicitly-rather-than-defaulting-to-internal)] | Visibility should be set explicitly rather than defaulting to `internal` | 2 | 
| [[N&#x2011;10](#n10-control-structures-do-not-follow-the-solidity-style-guide)] | Control structures do not follow the Solidity Style Guide | 13 | 
| [[N&#x2011;11](#n11-expressions-for-constant-values-such-as-a-call-to-keccak256-should-use-immutable-rather-than-constant)] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 3 | 
| [[N&#x2011;12](#n12-numeric-values-having-to-do-with-time-should-use-time-units-for-readability)] | Numeric values having to do with time should use time units for readability | 11 | 
| [[N&#x2011;13](#n13-consider-using-delete-rather-than-assigning-zerofalse-to-clear-values)] | Consider using `delete` rather than assigning zero/false to clear values | 19 | 
| [[N&#x2011;14](#n14-contracts-should-have-full-test-coverage)] | Contracts should have full test coverage | 1 | 
| [[N&#x2011;15](#n15-internal-functions-not-called-by-the-contract-should-be-removed)] | `internal` functions not called by the contract should be removed | 1 | 

Total: 122 instances over 15 issues



## Low Risk Issues


### [L&#x2011;01] Initialization can be front-run
The `initialize()` functions below are not called by another contract atomically after the contract is deployed, so it's possible for a malicious user to call `initialize()` which, if it's noticed in time, would require the project to re-deploy the contract in order to properly initialize. Consider creating a factory contract, which will `new` and `initialize()` each contract atomically.

*There are 10 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

56       function initialize(
57           address timelock_,
58           address govToken_,
59           uint256 votingPeriod_,
60           uint256 votingDelay_,
61           uint256 proposalThreshold_
62:      ) public virtual {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L56-L62

```solidity
File: src/hermes/minters/BaseV2Minter.sol

78:      function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/minters/BaseV2Minter.sol#L78-L78

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

37:      function initialize(address _localBridgeAgentAddress) external onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BaseBranchRouter.sol#L37-L37

```solidity
File: src/ulysses-omnichain/BranchPort.sol

99:      function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchPort.sol#L99-L99

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

63:      function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L63-L63

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

74:      function initialize(address _bridgeAgentAddress) external onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/MulticallRootRouter.sol#L74-L74

```solidity
File: src/ulysses-omnichain/RootPort.sol

128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootPort.sol#L128-L128

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:      function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L54-L54

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:      function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L35-L35

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

40:      function initialize(address _coreRouter) external onlyOwner {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L40-L40

</details>

### [L&#x2011;02] Tokens may be minted to `address(0x0)`
Neither the listed functions, nor `_mint()` prevent minting to `address(0x0)`

*There are 10 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-4626/ERC4626.sol

47       function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) {
48           assets = previewMint(shares); // No need to check for rounding error, previewMint rounds up.
49   
50           // Need to transfer before minting or ERC777s could reenter.
51           address(asset).safeTransferFrom(msg.sender, address(this), assets);
52   
53           _mint(receiver, shares);
54   
55           emit Deposit(msg.sender, receiver, assets, shares);
56   
57           afterDeposit(assets, shares);
58:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626.sol#L47-L58

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

47       function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) {
48           assets = previewMint(shares); // No need to check for rounding error, previewMint rounds up.
49   
50           // Need to transfer before minting or ERC777s could reenter.
51           address(asset).safeTransferFrom(msg.sender, address(this), assets);
52   
53           _mint(receiver, shares);
54   
55           emit Deposit(msg.sender, receiver, assets, shares);
56   
57           afterDeposit(assets, shares);
58:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626DepositOnly.sol#L47-L58

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

113      function mint(uint256 shares, address receiver)
114          public
115          virtual
116          nonReentrant
117          returns (uint256[] memory assetsAmounts)
118      {
119          assetsAmounts = previewMint(shares); // No need to check for rounding error, previewMint rounds up.
120  
121          // Need to transfer before minting or ERC777s could reenter.
122          receiveAssets(assetsAmounts);
123  
124          _mint(receiver, shares);
125  
126          emit Deposit(msg.sender, receiver, assetsAmounts, shares);
127  
128          afterDeposit(assetsAmounts, shares);
129:     }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626MultiToken.sol#L113-L129

```solidity
File: src/hermes/tokens/HERMES.sol

62       function mint(address account, uint256 amount) external onlyOwner {
63           _mint(account, amount);
64:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/HERMES.sol#L62-L64

```solidity
File: src/hermes/tokens/bHermesBoost.sol

28       function mint(address to, uint256 amount) external onlybHermes {
29           _mint(to, amount);
30:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/bHermesBoost.sol#L28-L30

```solidity
File: src/hermes/tokens/bHermesGauges.sol

35       function mint(address to, uint256 amount) external onlybHermes {
36           _mint(to, amount);
37:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/bHermesGauges.sol#L35-L37

```solidity
File: src/hermes/tokens/bHermesVotes.sol

26       function mint(address to, uint256 amount) external onlybHermes {
27           _mint(to, amount);
28:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/bHermesVotes.sol#L26-L28

```solidity
File: src/maia/tokens/Maia.sol

55       function mint(address account, uint256 amount) external onlyOwner {
56           _mint(account, amount);
57:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/tokens/Maia.sol#L55-L57

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol

23       function mint(address account, uint256 amount) external override onlyOwner returns (bool) {
24           _mint(account, amount);
25           return true;
26:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L23-L26

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

72       function mint(address to, uint256 amount, uint256 chainId) external requiresPort returns (bool) {
73           getTokenBalance[chainId] += amount;
74           _mint(to, amount);
75           return true;
76:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L72-L76

</details>

### [L&#x2011;03] `decimals()` is not a part of the ERC-20 standard
The `decimals()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*There are 20 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-4626/ERC4626.sol

23:      constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626.sol#L23-L23

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

23:      constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626DepositOnly.sol#L23-L23

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

51:              require(ERC20(_assets[i]).decimals() == 18);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626MultiToken.sol#L51-L51

```solidity
File: src/erc-4626/UlyssesERC4626.sol

27:          if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/UlyssesERC4626.sol#L27-L27

```solidity
File: src/ulysses-amm/UlyssesToken.sol

46:          require(ERC20(asset).decimals() == 18);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesToken.sol#L46-L46

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

104:             msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L104-L104

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

72:          underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));

82:              _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

120:                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

141:                     _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchPort.sol#L72-L72

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

252:             _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),

284:             _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

357:                         getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

342:                         getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

687:             _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),

720:             deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

1355:            deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals());

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L252-L252

```solidity
File: src/ulysses-omnichain/BranchPort.sol

212:             _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

254:                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

272:                     _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchPort.sol#L212-L212

</details>

### [L&#x2011;04] `name()` is not a part of the ERC-20 standard
The `name()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*There are 4 instances of this issue:*

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

49:          string memory name = ERC20(_underlyingAddress).name();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L49-L49

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

65:          string memory name = ERC20(_underlyingAddress).name();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreBranchRouter.sol#L65-L65

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

159:             _globalAddress, ERC20(_globalAddress).name(), ERC20(_globalAddress).symbol(), _remoteExecutionGas

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L159-L159

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

39:              ERC20(_wrappedNativeTokenAddress).name(),

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L39-L39


### [L&#x2011;05] `symbol()` is not a part of the ERC-20 standard
The `symbol()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*There are 4 instances of this issue:*

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

50:          string memory symbol = ERC20(_underlyingAddress).symbol();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L50-L50

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

66:          string memory symbol = ERC20(_underlyingAddress).symbol();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreBranchRouter.sol#L66-L66

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

159:             _globalAddress, ERC20(_globalAddress).name(), ERC20(_globalAddress).symbol(), _remoteExecutionGas

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L159-L159

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

40:              ERC20(_wrappedNativeTokenAddress).symbol(),

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L40-L40


### [L&#x2011;06] File allows a version of solidity that is susceptible to an assembly optimizer bug
In solidity versions 0.8.13 and 0.8.14, there is an [optimizer bug](https://github.com/ethereum/solidity-blog/blob/499ab8abc19391be7b7b34f88953a067029a5b45/_posts/2022-06-15-inline-assembly-memory-side-effects-bug.md) where, if the use of a variable is in a separate `assembly` block from the block in which it was stored, the `mstore` operation is optimized out, leading to uninitialized memory. The code currently does not have such a pattern of execution, but it does use `mstore`s in `assembly` blocks, so it is a risk for future changes. The affected solidity versions should be avoided if at all possible.

*There is one instance of this issue:*

```solidity
File: src/ulysses-amm/UlyssesPool.sol

380          assembly {
381              // Store bandwidth state slot in memory
382              mstore(0x00, bandwidthStateList.slot)
383              // Hash the bandwidth state slot to get the bandwidth state list start
384              let bandwidthStateListStart := keccak256(0x00, 0x20)
385  
386              // Total difference from target bandwidth of all bandwidth states
387              let totalDiff
388              // Total difference from target bandwidth of all bandwidth states
389              let transfered
390              // Total amount to be distributed according to each bandwidth weights
391              let transferedChange
392  
393              for { let i := 1 } lt(i, length) { i := add(i, 1) } {
394                  // Load bandwidth and weight from storage
395                  // Each bandwidth state occupies two storage slots
396                  let slot := sload(add(bandwidthStateListStart, mul(i, 2)))
397                  // Bandwidth is the first 248 bits of the slot
398                  let bandwidth := and(slot, 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff)
399                  // Weight is the last 8 bits of the slot
400                  let weight := shr(248, slot)
401  
402                  // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
403                  if mul(weight, gt(_totalSupply, div(not(0), weight))) {
404                      // Store the function selector of `MulDivFailed()`.
405                      mstore(0x00, 0xad251c27)
406                      // Revert with (offset, size).
407                      revert(0x1c, 0x04)
408                  }
409  
410                  // Calculate the target bandwidth
411                  let targetBandwidth := div(mul(_totalSupply, weight), _totalWeights)
412  
413                  // Calculate the difference from the target bandwidth
414                  switch positiveTransfer
415                  // If the transfer is positive, calculate deficit from target bandwidth
416                  case true {
417                      // If there is a deficit, store the difference
418                      if gt(targetBandwidth, bandwidth) {
419                          // Calculate the difference
420                          let diff := sub(targetBandwidth, bandwidth)
421                          // Add the difference to the total difference
422                          totalDiff := add(totalDiff, diff)
423                          // Store the difference in the diffs array
424                          mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)
425                      }
426                  }
427                  // If the transfer is negative, calculate surplus from target bandwidth
428                  default {
429                      // If there is a surplus, store the difference
430                      if gt(bandwidth, targetBandwidth) {
431                          // Calculate the difference
432                          let diff := sub(bandwidth, targetBandwidth)
433                          // Add the difference to the total difference
434                          totalDiff := add(totalDiff, diff)
435                          // Store the difference in the diffs array
436                          mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)
437                      }
438                  }
439              }
440  
441              // Calculate the amount to be distributed according deficit/surplus
442              // and/or the amount to be distributed according to each bandwidth weights
443              switch gt(amount, totalDiff)
444              // If the amount is greater than the total deficit/surplus
445              case true {
446                  // Total deficit/surplus is distributed
447                  transfered := totalDiff
448                  // Set rest to be distributed according to each bandwidth weights
449                  transferedChange := sub(amount, totalDiff)
450              }
451              // If the amount is less than the total deficit/surplus
452              default {
453                  // Amount will be distributed according to deficit/surplus
454                  transfered := amount
455              }
456  
457              for { let i := 1 } lt(i, length) { i := add(i, 1) } {
458                  // Increase/decrease amount of bandwidth for each bandwidth state
459                  let bandwidthUpdate
460  
461                  // If there is a deficit/surplus, calculate the amount to be distributed
462                  if gt(transfered, 0) {
463                      // Load the difference from the diffs array
464                      let diff := mload(add(diffs, add(mul(i, 0x20), 0x20)))
465  
466                      // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
467                      if mul(diff, gt(transfered, div(not(0), diff))) {
468                          // Store the function selector of `MulDivFailed()`.
469                          mstore(0x00, 0xad251c27)
470                          // Revert with (offset, size).
471                          revert(0x1c, 0x04)
472                      }
473  
474                      // Calculate the amount to be distributed according to deficit/surplus
475                      switch roundUp
476                      // If round up then do mulDivUp(transfered, diff, totalDiff)
477                      case true {
478                          bandwidthUpdate :=
479                              add(
480                                  iszero(iszero(mod(mul(transfered, diff), totalDiff))), div(mul(transfered, diff), totalDiff)
481                              )
482                      }
483                      // If round down then do mulDiv(transfered, diff, totalDiff)
484                      default { bandwidthUpdate := div(mul(transfered, diff), totalDiff) }
485                  }
486  
487                  // If there is a rest, calculate the amount to be distributed according to each bandwidth weights
488                  if gt(transferedChange, 0) {
489                      // Load weight from storage
490                      let weight := shr(248, sload(add(bandwidthStateListStart, mul(i, 2))))
491  
492                      // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
493                      if mul(weight, gt(transferedChange, div(not(0), weight))) {
494                          // Store the function selector of `MulDivFailed()`.
495                          mstore(0x00, 0xad251c27)
496                          // Revert with (offset, size).
497                          revert(0x1c, 0x04)
498                      }
499  
500                      // Calculate the amount to be distributed according to each bandwidth weights
501                      switch roundUp
502                      // If round up then do mulDivUp(transferedChange, weight, _totalWeights)
503                      case true {
504                          bandwidthUpdate :=
505                              add(
506                                  bandwidthUpdate,
507                                  add(
508                                      iszero(iszero(mod(mul(transferedChange, weight), _totalWeights))),
509                                      div(mul(transferedChange, weight), _totalWeights)
510                                  )
511                              )
512                      }
513                      // If round down then do mulDiv(transferedChange, weight, _totalWeights)
514                      default {
515                          bandwidthUpdate := add(bandwidthUpdate, div(mul(transferedChange, weight), _totalWeights))
516                      }
517                  }
518  
519                  // If there is an update in bandwidth
520                  if gt(bandwidthUpdate, 0) {
521                      // Store the amount to be updated in the bandwidthUpdateAmounts array
522                      mstore(add(bandwidthUpdateAmounts, add(mul(i, 0x20), 0x20)), bandwidthUpdate)
523                  }
524              }
525:         }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesPool.sol#L380-L525


### [L&#x2011;07] Solidity version 0.8.20 may not work on other chains due to `PUSH0`
The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

*There are 153 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-20/ERC20Boost.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Boost.sol#L3-L3

```solidity
File: src/erc-20/ERC20Gauges.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Gauges.sol#L3-L3

```solidity
File: src/erc-20/ERC20MultiVotes.sol

4:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L4-L4

```solidity
File: src/erc-20/interfaces/Errors.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/interfaces/Errors.sol#L2-L2

```solidity
File: src/erc-20/interfaces/IERC20Boost.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/interfaces/IERC20Boost.sol#L2-L2

```solidity
File: src/erc-20/interfaces/IERC20Gauges.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/interfaces/IERC20Gauges.sol#L3-L3

```solidity
File: src/erc-20/interfaces/IERC20MultiVotes.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/interfaces/IERC20MultiVotes.sol#L3-L3

```solidity
File: src/erc-4626/ERC4626.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626.sol#L2-L2

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626DepositOnly.sol#L2-L2

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626MultiToken.sol#L2-L2

```solidity
File: src/erc-4626/UlyssesERC4626.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/UlyssesERC4626.sol#L2-L2

```solidity
File: src/erc-4626/interfaces/IERC4626.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/interfaces/IERC4626.sol#L2-L2

```solidity
File: src/erc-4626/interfaces/IERC4626DepositOnly.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L2-L2

```solidity
File: src/erc-4626/interfaces/IERC4626MultiToken.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/interfaces/IERC4626MultiToken.sol#L2-L2

```solidity
File: src/erc-4626/interfaces/IUlyssesERC4626.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/interfaces/IUlyssesERC4626.sol#L2-L2

```solidity
File: src/gauges/BaseV2Gauge.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/BaseV2Gauge.sol#L2-L2

```solidity
File: src/gauges/UniswapV3Gauge.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/UniswapV3Gauge.sol#L2-L2

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/factories/BaseV2GaugeFactory.sol#L3-L3

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/factories/BaseV2GaugeManager.sol#L3-L3

```solidity
File: src/gauges/factories/BribesFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/factories/BribesFactory.sol#L2-L2

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/factories/UniswapV3GaugeFactory.sol#L3-L3

```solidity
File: src/gauges/interfaces/IBaseV2Gauge.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IBaseV2Gauge.sol#L2-L2

```solidity
File: src/gauges/interfaces/IBaseV2GaugeFactory.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IBaseV2GaugeFactory.sol#L3-L3

```solidity
File: src/gauges/interfaces/IBaseV2GaugeManager.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IBaseV2GaugeManager.sol#L3-L3

```solidity
File: src/gauges/interfaces/IBribesFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IBribesFactory.sol#L2-L2

```solidity
File: src/gauges/interfaces/IUniswapV3Gauge.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IUniswapV3Gauge.sol#L2-L2

```solidity
File: src/gauges/interfaces/IUniswapV3GaugeFactory.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IUniswapV3GaugeFactory.sol#L3-L3

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

2:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L2-L2

```solidity
File: src/governance/GovernorBravoDelegator.sol

2:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegator.sol#L2-L2

```solidity
File: src/governance/GovernorBravoInterfaces.sol

2:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoInterfaces.sol#L2-L2

```solidity
File: src/hermes/UtilityManager.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/UtilityManager.sol#L3-L3

```solidity
File: src/hermes/bHermes.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/bHermes.sol#L2-L2

```solidity
File: src/hermes/interfaces/IBaseV2Minter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/interfaces/IBaseV2Minter.sol#L2-L2

```solidity
File: src/hermes/interfaces/IUtilityManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/interfaces/IUtilityManager.sol#L2-L2

```solidity
File: src/hermes/interfaces/IbHermesUnderlying.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/interfaces/IbHermesUnderlying.sol#L2-L2

```solidity
File: src/hermes/minters/BaseV2Minter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/minters/BaseV2Minter.sol#L2-L2

```solidity
File: src/hermes/tokens/HERMES.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/HERMES.sol#L2-L2

```solidity
File: src/hermes/tokens/bHermesBoost.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/bHermesBoost.sol#L2-L2

```solidity
File: src/hermes/tokens/bHermesGauges.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/bHermesGauges.sol#L2-L2

```solidity
File: src/hermes/tokens/bHermesVotes.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/tokens/bHermesVotes.sol#L2-L2

```solidity
File: src/maia/PartnerUtilityManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/PartnerUtilityManager.sol#L2-L2

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/factories/PartnerManagerFactory.sol#L2-L2

```solidity
File: src/maia/interfaces/IBaseVault.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/interfaces/IBaseVault.sol#L3-L3

```solidity
File: src/maia/interfaces/IERC4626PartnerManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/interfaces/IERC4626PartnerManager.sol#L2-L2

```solidity
File: src/maia/interfaces/IPartnerManagerFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/interfaces/IPartnerManagerFactory.sol#L2-L2

```solidity
File: src/maia/interfaces/IPartnerUtilityManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/interfaces/IPartnerUtilityManager.sol#L2-L2

```solidity
File: src/maia/libraries/DateTimeLib.sol

2:   pragma solidity ^0.8.4;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/libraries/DateTimeLib.sol#L2-L2

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/tokens/ERC4626PartnerManager.sol#L2-L2

```solidity
File: src/maia/tokens/Maia.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/tokens/Maia.sol#L2-L2

```solidity
File: src/maia/vMaia.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/vMaia.sol#L3-L3

```solidity
File: src/rewards/FlywheelCoreInstant.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/FlywheelCoreInstant.sol#L2-L2

```solidity
File: src/rewards/FlywheelCoreStrategy.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/FlywheelCoreStrategy.sol#L2-L2

```solidity
File: src/rewards/base/BaseFlywheelRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/base/BaseFlywheelRewards.sol#L3-L3

```solidity
File: src/rewards/base/FlywheelCore.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/base/FlywheelCore.sol#L3-L3

```solidity
File: src/rewards/booster/FlywheelBoosterGaugeWeight.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3-L3

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/depots/MultiRewardsDepot.sol#L2-L2

```solidity
File: src/rewards/depots/RewardsDepot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/depots/RewardsDepot.sol#L2-L2

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/depots/SingleRewardsDepot.sol#L2-L2

```solidity
File: src/rewards/interfaces/IFlywheelAcummulatedRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L3-L3

```solidity
File: src/rewards/interfaces/IFlywheelBooster.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelBooster.sol#L3-L3

```solidity
File: src/rewards/interfaces/IFlywheelBribeRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelBribeRewards.sol#L3-L3

```solidity
File: src/rewards/interfaces/IFlywheelCore.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelCore.sol#L3-L3

```solidity
File: src/rewards/interfaces/IFlywheelGaugeRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L3-L3

```solidity
File: src/rewards/interfaces/IFlywheelInstantRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelInstantRewards.sol#L3-L3

```solidity
File: src/rewards/interfaces/IFlywheelRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelRewards.sol#L3-L3

```solidity
File: src/rewards/interfaces/IMultiRewardsDepot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IMultiRewardsDepot.sol#L2-L2

```solidity
File: src/rewards/interfaces/IRewardsDepot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IRewardsDepot.sol#L2-L2

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L3-L3

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelBribeRewards.sol#L3-L3

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelGaugeRewards.sol#L3-L3

```solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelInstantRewards.sol#L3-L3

```solidity
File: src/talos/TalosManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosManager.sol#L2-L2

```solidity
File: src/talos/TalosOptimizer.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosOptimizer.sol#L3-L3

```solidity
File: src/talos/TalosStrategyStaked.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyStaked.sol#L2-L2

```solidity
File: src/talos/TalosStrategyVanilla.sol

3:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyVanilla.sol#L3-L3

```solidity
File: src/talos/base/TalosBaseStrategy.sol

3:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L3-L3

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/boost-aggregator/BoostAggregator.sol#L2-L2

```solidity
File: src/talos/factories/BoostAggregatorFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/factories/BoostAggregatorFactory.sol#L2-L2

```solidity
File: src/talos/factories/OptimizerFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/factories/OptimizerFactory.sol#L2-L2

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/factories/TalosBaseStrategyFactory.sol#L2-L2

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/factories/TalosStrategyStakedFactory.sol#L2-L2

```solidity
File: src/talos/factories/TalosStrategyVanillaFactory.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/factories/TalosStrategyVanillaFactory.sol#L3-L3

```solidity
File: src/talos/interfaces/AutomationCompatibleInterface.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/AutomationCompatibleInterface.sol#L2-L2

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IBoostAggregator.sol#L2-L2

```solidity
File: src/talos/interfaces/IBoostAggregatorFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IBoostAggregatorFactory.sol#L2-L2

```solidity
File: src/talos/interfaces/IOptimizerFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IOptimizerFactory.sol#L2-L2

```solidity
File: src/talos/interfaces/ITalosBaseStrategy.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosBaseStrategy.sol#L3-L3

```solidity
File: src/talos/interfaces/ITalosBaseStrategyFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosBaseStrategyFactory.sol#L2-L2

```solidity
File: src/talos/interfaces/ITalosManager.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosManager.sol#L2-L2

```solidity
File: src/talos/interfaces/ITalosOptimizer.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosOptimizer.sol#L3-L3

```solidity
File: src/talos/interfaces/ITalosStrategyStaked.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosStrategyStaked.sol#L2-L2

```solidity
File: src/talos/interfaces/ITalosStrategyStakedFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosStrategyStakedFactory.sol#L2-L2

```solidity
File: src/talos/libraries/PoolActions.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/libraries/PoolActions.sol#L3-L3

```solidity
File: src/talos/libraries/PoolVariables.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/libraries/PoolVariables.sol#L3-L3

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

2:   pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/strategies/TalosStrategySimple.sol#L2-L2

```solidity
File: src/ulysses-amm/UlyssesPool.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesPool.sol#L2-L2

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesRouter.sol#L2-L2

```solidity
File: src/ulysses-amm/UlyssesToken.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesToken.sol#L2-L2

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/factories/UlyssesFactory.sol#L2-L2

```solidity
File: src/ulysses-amm/interfaces/IUlyssesFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/interfaces/IUlyssesFactory.sol#L2-L2

```solidity
File: src/ulysses-amm/interfaces/IUlyssesPool.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/interfaces/IUlyssesPool.sol#L2-L2

```solidity
File: src/ulysses-amm/interfaces/IUlyssesRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/interfaces/IUlyssesRouter.sol#L2-L2

```solidity
File: src/ulysses-amm/interfaces/IUlyssesToken.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/interfaces/IUlyssesToken.sol#L2-L2

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2-L2

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3-L3

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BaseBranchRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L2-L2

```solidity
File: src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2-L2

```solidity
File: src/ulysses-omnichain/BranchPort.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchPort.sol#L3-L3

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreBranchRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/MulticallRootRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgent.sol#L2-L2

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2-L2

```solidity
File: src/ulysses-omnichain/RootPort.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootPort.sol#L3-L3

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/VirtualAccount.sol#L3-L3

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallConfig.sol

3:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IAnycallConfig.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallExecutor.sol

3:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IAnycallProxy.sol

3:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IAnycallProxy.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IApp.sol

3:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IApp.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IArbitrumBranchPort.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IBranchBridgeAgentFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IBranchPort.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IBranchPort.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IBranchRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IBranchRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/ICoreBranchRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IERC20hTokenBranch.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IERC20hTokenRoot.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IMulticall2.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IMulticall2.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IPortStrategy.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IRootBridgeAgentFactory.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IRootPort.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IRootPort.sol#L3-L3

```solidity
File: src/ulysses-omnichain/interfaces/IRootRouter.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IRootRouter.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IVirtualAccount.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L2-L2

```solidity
File: src/ulysses-omnichain/interfaces/IWETH9.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IWETH9.sol#L2-L2

```solidity
File: src/ulysses-omnichain/lib/AnycallFlags.sol

3:   pragma solidity ^0.8.10;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/lib/AnycallFlags.sol#L3-L3

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenBranch.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L2-L2

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2-L2

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/UniswapV3Staker.sol#L3-L3

```solidity
File: src/uni-v3-staker/interfaces/IUniswapV3Staker.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L3-L3

```solidity
File: src/uni-v3-staker/libraries/IncentiveId.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/libraries/IncentiveId.sol#L2-L2

```solidity
File: src/uni-v3-staker/libraries/IncentiveTime.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/libraries/IncentiveTime.sol#L2-L2

```solidity
File: src/uni-v3-staker/libraries/NFTPositionInfo.sol

3:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L3-L3

```solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

2:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/libraries/RewardMath.sol#L2-L2

</details>
## Non-critical Issues


### [N&#x2011;01] Duplicate import statements


*There are 7 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/talos/TalosManager.sol

11:   import {ITalosManager, AutomationCompatibleInterface} from "./interfaces/ITalosManager.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosManager.sol#L11

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

21    import {
22        Deposit,
23        DepositStatus,
24        DepositInput,
25        DepositMultipleInput,
26        DepositParams,
27        DepositMultipleParams,
28        SettlementParams,
29        SettlementMultipleParams
30:   } from "./interfaces/IBranchBridgeAgent.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L21-L30

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

16:   import {DepositParams, DepositMultipleParams} from "./interfaces/IRootBridgeAgent.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L16

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

13:   import {DepositParams, DepositMultipleParams, Settlement} from "./interfaces/IRootBridgeAgent.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/MulticallRootRouter.sol#L13

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

9:    import {DepositParams, DepositMultipleParams} from "./interfaces/IRootBridgeAgent.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L9

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

26    import {
27        IRootBridgeAgent,
28        DepositParams,
29        DepositMultipleParams,
30        Settlement,
31        SettlementStatus,
32        SettlementParams,
33        SettlementMultipleParams,
34        UserFeeInfo,
35        SwapCallbackData
36:   } from "./interfaces/IRootBridgeAgent.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgent.sol#L26-L36

```solidity
File: src/ulysses-omnichain/RootPort.sol

17:   import {VirtualAccount, GasPoolInfo} from "./interfaces/IRootPort.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootPort.sol#L17

</details>

### [N&#x2011;02] `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings


*There are 5 instances of this issue:*

```solidity
File: src/maia/vMaia.sol

91:       function claimBoost(uint256 amount) public override {}

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/vMaia.sol#L91

```solidity
File: src/talos/TalosStrategyVanilla.sol

79:       function afterRedeem(uint256 _tokenId) internal override {}

90:       function afterDeposit(uint256 _tokenId) internal override {}

100:      function afterRerange(uint256 _tokenId) internal override {}

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyVanilla.sol#L79

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

149:      function _gasSwapIn(bytes memory gasData) internal override returns (uint256 gasAmount) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L149


### [N&#x2011;03] Non-assembly method available
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`, `assembly { hash := extcodehash() }` => `bytes32 hash = address().codehash`
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

*There is one instance of this issue:*

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

539:              chainId := chainid()

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L539


### [N&#x2011;04] Inconsistent method of specifying a floating pragma
Some files use `>=`, some use `^`. The instances below are examples of the method that has the fewest instances for a specific version. Note that using `>=` without also specifying `<=` will lead to failures to compile, or external project incompatability, when the major version changes and there are breaking-changes, so `^` should be preferred regardless of the instance counts

*There are 11 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626DepositOnly.sol#L2

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626MultiToken.sol#L2

```solidity
File: src/erc-4626/ERC4626.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626.sol#L2

```solidity
File: src/erc-4626/interfaces/IERC4626.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/interfaces/IERC4626.sol#L2

```solidity
File: src/erc-4626/UlyssesERC4626.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/UlyssesERC4626.sol#L2

```solidity
File: src/talos/base/TalosBaseStrategy.sol

3:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L3

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/boost-aggregator/BoostAggregator.sol#L2

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IBoostAggregator.sol#L2

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/strategies/TalosStrategySimple.sol#L2

```solidity
File: src/talos/TalosStrategyStaked.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyStaked.sol#L2

```solidity
File: src/talos/TalosStrategyVanilla.sol

3:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyVanilla.sol#L3

</details>

### [N&#x2011;05] Using `>`/`>=` without specifying an upper bound is unsafe
There _will_ be breaking changes in future versions of solidity, and at that point your code will no longer be compatable. While you may have the specific version to use in a configuration file, others that include your source files may not.

*There are 11 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626DepositOnly.sol#L2

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626MultiToken.sol#L2

```solidity
File: src/erc-4626/ERC4626.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/ERC4626.sol#L2

```solidity
File: src/erc-4626/interfaces/IERC4626.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/interfaces/IERC4626.sol#L2

```solidity
File: src/erc-4626/UlyssesERC4626.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-4626/UlyssesERC4626.sol#L2

```solidity
File: src/talos/base/TalosBaseStrategy.sol

3:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L3

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/boost-aggregator/BoostAggregator.sol#L2

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IBoostAggregator.sol#L2

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/strategies/TalosStrategySimple.sol#L2

```solidity
File: src/talos/TalosStrategyStaked.sol

2:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyStaked.sol#L2

```solidity
File: src/talos/TalosStrategyVanilla.sol

3:    pragma solidity >=0.8.0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/TalosStrategyVanilla.sol#L3

</details>

### [N&#x2011;06] NatSpec `@param` is listed multiple times


*There are 2 instances of this issue:*

```solidity
File: src/rewards/interfaces/IFlywheelCore.sol

/// @audit user
67        /**
68         * @notice accrue rewards for a two users on a strategy
69         *   @param strategy the strategy to accrue a user's rewards on
70         *   @param user the first user to be accrued
71         *   @param user the second user to be accrued
72         *   @return the cumulative amount of rewards accrued to the first user (including prior)
73         *   @return the cumulative amount of rewards accrued to the second user (including prior)
74         */
75:       function accrue(ERC20 strategy, address user, address secondUser) external returns (uint256, uint256);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/interfaces/IFlywheelCore.sol#L67-L75

```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

/// @audit depositor
313       /**
314        * @notice Internal function performs call to AnycallProxy Contract for cross-chain messaging.
315        *   @param depositor address of user depositing assets.
316        *   @param params calldata for omnichain execution.
317        *   @param depositor address of user depositing assets.
318        *   @param gasToBridgeOut gas allocated for the cross-chain call.
319        *   @param remoteExecutionGas gas allocated for omnichain execution.
320        *   @dev DEPOSIT ID: 1 (Call without Deposit)
321        *
322        */
323:      function performCallOut(address depositor, bytes memory params, uint128 gasToBridgeOut, uint128 remoteExecutionGas)

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L313-L323


### [N&#x2011;07] Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There are 33 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/gauges/interfaces/IBaseV2Gauge.sol

58:       /// @notice mapping of whitelisted bribe tokens.

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/interfaces/IBaseV2Gauge.sol#L58

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

389:      function isWhitelisted(address account) public view returns (bool) {

447:      function _setWhitelistAccountExpiration(address account, uint256 expiration) external {

461:      function _setWhitelistGuardian(address account) external {

113:          // Allow addresses above proposal threshold and whitelisted addresses to propose

235:              // Whitelisted proposers can't be canceled for falling below proposal threshold

385:       * @notice View function which returns if an account is whitelisted

387:       * @return If the account is whitelisted

443:       * @notice Admin function for setting the whitelist expiration as a timestamp for an account. Whitelist status allows accounts to propose without meeting threshold

444:       * @param account Account address to set whitelist expiration for

445:       * @param expiration Expiration for account whitelist status as timestamp (if now < expiration, whitelisted)

458:       * @notice Admin function for setting the whitelistGuardian. WhitelistGuardian can cancel proposals from whitelisted addresses

459:       * @param account Account to set whitelistGuardian to (0x0 to remove whitelistGuardian)

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L389

```solidity
File: src/governance/GovernorBravoInterfaces.sol

163:      mapping(address => uint256) public whitelistAccountExpirations;

166:      address public whitelistGuardian;

53:       /// @notice Emitted when whitelist account expiration is set

56:       /// @notice Emitted when the whitelistGuardian is set

162:      /// @notice Stores the expiration of account whitelist status as a timestamp

165:      /// @notice Address which manages whitelisted proposals and whitelist accounts

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoInterfaces.sol#L163

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

38:           /// @dev Anyone can call this, whitelisting is handled in FlywheelCore

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelBribeRewards.sol#L38

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

48:       mapping(address => bool) public whitelistedAddresses;

143:      function addWhitelistedAddress(address user) external onlyOwner {

148:      function removeWhitelistedAddress(address user) external onlyOwner {

78:       ///      whitelisted addresses to retrieve NFTs incorrectly sent to this contract

188:      /// @dev Only whitelisted addresses

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/boost-aggregator/BoostAggregator.sol#L48

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

48:       function whitelistedAddresses(address) external view returns (bool);

84:       function addWhitelistedAddress(address user) external;

90:       function removeWhitelistedAddress(address user) external;

47:       /// @notice mapping of whitelisted addresses

81:        * @notice add whitelisted address to stake using this contract

87:        * @notice remove whitelisted address from staking using this contract

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IBoostAggregator.sol#L48

```solidity
File: src/ulysses-omnichain/BranchPort.sol

56:       /// @notice List of Tokens whitelisted for usage in Port Strategies.

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchPort.sol#L56

```solidity
File: src/ulysses-omnichain/interfaces/IPortStrategy.sol

9:     *         whitelisted by the chain's Branch Port to manage a limited amount

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IPortStrategy.sol#L9

</details>

### [N&#x2011;08] Large assembly blocks should have extensive comments
Assembly blocks are take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code

*There are 2 instances of this issue:*

```solidity
File: src/governance/GovernorBravoDelegator.sol

75            assembly {
76                let free_mem_ptr := mload(0x40)
77                returndatacopy(free_mem_ptr, 0, returndatasize())
78    
79                switch success
80                case 0 { revert(free_mem_ptr, returndatasize()) }
81                default { return(free_mem_ptr, returndatasize()) }
82:           }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegator.sol#L75-L82

```solidity
File: src/maia/libraries/DateTimeLib.sol

43            assembly {
44                epochDay := add(epochDay, 719468)
45                let doe := mod(epochDay, 146097)
46                let yoe := div(sub(sub(add(doe, div(doe, 36524)), div(doe, 1460)), eq(doe, 146096)), 365)
47                let doy := sub(doe, sub(add(mul(365, yoe), shr(2, yoe)), div(yoe, 100)))
48                let mp := div(add(mul(5, doy), 2), 153)
49                month := sub(add(mp, 3), mul(gt(mp, 9), 12))
50:           }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/libraries/DateTimeLib.sol#L43-L50


### [N&#x2011;09] Visibility should be set explicitly rather than defaulting to `internal`


*There are 2 instances of this issue:*

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

16:       address immutable localPortAddress;

19:       address localCoreRouterAddress;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L16


### [N&#x2011;10] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*There are 13 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/hermes/bHermes.sol

143           if (
144:              userBalance - userClaimedWeight[msg.sender] < amount || userBalance - userClaimedBoost[msg.sender] < amount

161           if (
162:              userBalance - userClaimedWeight[from] < amount || userBalance - userClaimedBoost[from] < amount

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/bHermes.sol#L143-L144

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

328           if (
329:              userBalance - userClaimedWeight[from] < amount || userBalance - userClaimedBoost[from] < amount

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/tokens/ERC4626PartnerManager.sol#L328-L329

```solidity
File: src/talos/base/TalosBaseStrategy.sol

108           if (initialized) revert AlreadyInitialized();
109   
110:          {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L108-L110

```solidity
File: src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

141           if (
142:              _data.length - PARAMS_GAS_OUT

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L141-L142

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

813           if (
814:              _hTokens.length != _tokens.length || _tokens.length != _amounts.length

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L813-L814

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

186           if (
187:              IPort(rootPortAddress).isGlobalAddress(_underlyingAddress)

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L186-L187

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

172           if (
173:              length - PARAMS_GAS_IN

277               if (
278:                  _data.length - PARAMS_GAS_IN

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L172-L173

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

55            if (
56:               (_dParams.amount < _dParams.deposit) //Deposit can't be greater than amount.

262           } else if (
263:              msg.sender != depositOwner && msg.sender != address(IPort(localPortAddress).getUserAccount(depositOwner))

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgent.sol#L55-L56

```solidity
File: src/ulysses-omnichain/RootPort.sol

493           if (
494:              getUnderlyingTokenFromLocal[_ecoTokenGlobalAddress][localChainId] != address(0)

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootPort.sol#L493-L494

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

374           if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();
375   
376:          {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/UniswapV3Staker.sol#L374-L376

</details>

### [N&#x2011;11] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it **doesn't save any gas** because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 3 instances of this issue:*

```solidity
File: src/erc-20/ERC20MultiVotes.sol

360       bytes32 public constant DELEGATION_TYPEHASH =
361:          keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

42        bytes32 public constant DOMAIN_TYPEHASH =
43:           keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

46:       bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L42-L43


### [N&#x2011;12] Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There are 11 instances of this issue:*

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

/// @audit 80640
17        /// @notice The minimum setable voting period
18:       uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

/// @audit 161280
20        /// @notice The max setable voting period
21:       uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

/// @audit 40320
23        /// @notice The min setable voting delay
24:       uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

/// @audit 80640
26        /// @notice The max setable voting delay
27:       uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L17-L18

```solidity
File: src/hermes/minters/BaseV2Minter.sol

/// @audit 86400
23        /// @dev allows minting once per week (reset every Thursday 00:00 UTC)
24:       uint256 internal constant week = 86400 * 7;

/// @audit 300
29:       uint256 internal constant max_dao_share = 300;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/minters/BaseV2Minter.sol#L23-L24

```solidity
File: src/maia/libraries/DateTimeLib.sol

/// @audit 86400
35        /// @dev Returns (`month`) from the number of days since 1970-01-01.
36        /// See: https://howardhinnant.github.io/date_algorithms.html
37        /// Note: Inputs outside the supported ranges result in undefined behavior.
38        /// Use {isSupportedDays} to check if the inputs is supported.
39        function getMonth(uint256 timestamp) internal pure returns (uint256 month) {
40:           uint256 epochDay = timestamp / 86400;

/// @audit 86400
57:               uint256 day = timestamp / 86400;

/// @audit 86400
58:               startOfDay = day * 86400;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/libraries/DateTimeLib.sol#L35-L40

```solidity
File: src/ulysses-amm/UlyssesPool.sol

/// @audit 4980e14
/// @audit 6000e14
69        /// @notice The current rebalancing fees
70:       Fees public fees = Fees({lambda1: 20e14, lambda2: 4980e14, sigma1: 6000e14, sigma2: 500e14});

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesPool.sol#L69-L70


### [N&#x2011;13] Consider using `delete` rather than assigning zero/false to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 19 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-20/ERC20Boost.sol

249:          getUserBoost[msg.sender] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Boost.sol#L249

```solidity
File: src/erc-20/ERC20MultiVotes.sol

340:                      _delegatesVotesCount[user][delegatee] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L340

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

149:          newProposal.eta = 0;

156:          newProposal.forVotes = 0;

157:          newProposal.againstVotes = 0;

158:          newProposal.abstainVotes = 0;

159:          newProposal.canceled = false;

160:          newProposal.executed = false;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L149

```solidity
File: src/rewards/base/FlywheelCore.sol

98:               rewardsAccrued[user] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/base/FlywheelCore.sol#L98

```solidity
File: src/talos/libraries/PoolVariables.sol

211:          secondsAgo[1] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/libraries/PoolVariables.sol#L211

```solidity
File: src/ulysses-amm/UlyssesToken.sol

78:           assetId[asset] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesToken.sol#L78

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

202:          incentive.totalRewardUnclaimed = 0;

268:              rewards[msg.sender] = 0;

279:          rewards[msg.sender] = 0;

406:                  _userAttachements[owner][key.pool] = 0;

426:          deposit.stakedTimestamp = 0;

454:          stake.secondsPerLiquidityInsideInitialX128 = 0;

455:          stake.liquidityNoOverflow = 0;

456:          if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/UniswapV3Staker.sol#L202

</details>

### [N&#x2011;14] Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is one instance of this issue:*

```solidity
File: Various Files


```


### [N&#x2011;15] `internal` functions not called by the contract should be removed
All unused code should be removed

*There is one instance of this issue:*

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1109:     function _getContext() internal view returns (address from, uint256 fromChainId) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L1109
