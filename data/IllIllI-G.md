

## Summary

### Gas Optimizations

| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [[G&#x2011;01](#g01-consider-using-bytes32-rather-than-a-string)] | Consider using `bytes32` rather than a `string` | 1 |  - |
| [[G&#x2011;02](#g02-remove-or-replace-unused-state-variables)] | Remove or replace unused state variables | 2 |  - |
| [[G&#x2011;03](#g03-state-variables-can-be-packed-into-fewer-storage-slots)] | State variables can be packed into fewer storage slots | 3 |  6000 |
| [[G&#x2011;04](#g04-structs-can-be-packed-into-fewer-storage-slots)] | Structs can be packed into fewer storage slots | 5 |  10000 |
| [[G&#x2011;05](#g05-using-storage-instead-of-memory-for-structsarrays-saves-gas)] | Using `storage` instead of `memory` for structs/arrays saves gas | 2 |  8400 |
| [[G&#x2011;06](#g06-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage)] | State variables should be cached in stack variables rather than re-reading them from storage | 94 |  9118 |
| [[G&#x2011;07](#g07-multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)] | Multiple accesses of a mapping/array should use a local variable cache | 54 |  2268 |

Total: 161 instances over 7 issues with **35786 gas** saved

Gas totals are estimates based on data from the Ethereum Yellowpaper. The estimates use the lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.


### Invalid Issues

The issues below may be reported by other bots/wardens, but can be ignored since either the rule or the specified instances are invalid

| |Issue|Instances|
|-|:-|:-:|
| [[I&#x2011;01](#i01-do-not-calculate-constant-variables-which-will-save-gas)] | ~~Do not calculate `constant` variables, which will save gas~~ | 3 | 
| [[I&#x2011;02](#i02-use-delete-instead-of-setting-mappingstate-variable-to-zero-to-save-gas)] | ~~Use delete instead of setting mapping/state variable to zero, to save gas~~ | 24 | 
| [[I&#x2011;03](#i03-abiencode-is-less-efficient-than-abiencodepacked)] | ~~`abi.encode()` is less efficient than `abi.encodepacked()`~~ | 15 | 
| [[I&#x2011;04](#i04-contracts-are-not-using-their-oz-upgradeable-counterparts)] | ~~Contracts are not using their OZ Upgradeable counterparts~~ | 8 | 
| [[I&#x2011;05](#i05-change-public-function-visibility-to-external-to-save-gas)] | ~~Change `public` function visibility to `external` to save gas~~ | 5 | 
| [[I&#x2011;06](#i06-solmates-safetransferlib-doesnt-check-whether-the-erc20-contract-exists)] | ~~Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists~~ | 3 | 
| [[I&#x2011;07](#i07-save-gas-with-the-use-of-specific-import-statements)] | ~~Save gas with the use of specific import statements~~ | 2 | 

Total: 60 instances over 7 issues



## Gas Optimizations


### [G&#x2011;01] Consider using `bytes32` rather than a `string`
Using the `bytes` types for fixed-length strings is more efficient than having the EVM have to incur the overhead of string processing. Consider whether the value _needs_ to be a `string`. A good reason to keep it as a `string` would be if the variable is defined in an interface that this project does not own.

*There is one instance of this issue:*

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

9:       string public constant name = "vMaia Governor Bravo";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L9-L9


### [G&#x2011;02] Remove or replace unused state variables
Saves a storage slot. If the variable is assigned a non-zero value, saves Gsset (**20000 gas**). If it's assigned a zero value, saves Gsreset (**2900 gas**). If the variable remains unassigned, there is no gas savings unless the variable is `public`, in which case the compiler-generated non-payable getter deployment cost is saved. If the state variable is overriding an interface's public function, mark the variable as `constant` or `immutable` so that it does not use a storage slot

*There are 2 instances of this issue:*

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

38:       mapping(address => address) public getBridgeAgentManager;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L38

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

22:       address public localBranchPortAddress;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L22


### [G&#x2011;03] State variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

*There are 3 instances of this issue:*

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

/// @audit Variable ordering with 2 slots instead of the current 3:
///           mapping(32):gaugeQueuedRewards, uint112(14):nextCycleQueuedRewards, uint32(4):gaugeCycle, uint32(4):nextCycle, uint32(4):paginationOffset
36:       uint32 public override gaugeCycle;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelGaugeRewards.sol#L36

```solidity
File: src/talos/base/TalosBaseStrategy.sol

/// @audit Variable ordering with 4 slots instead of the current 5:
///           uint256(32):tokenId, uint256(32):protocolFees0, uint256(32):protocolFees1, uint128(16):liquidity, int24(3):tickLower, int24(3):tickUpper, bool(1):initialized
37:       uint256 public override tokenId;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L37

```solidity
File: src/ulysses-omnichain/RootPort.sol

/// @audit Variable ordering with 20 slots instead of the current 21:
///           mapping(32):getUserAccount, mapping(32):isRouterApproved, mapping(32):isChainId, mapping(32):isBridgeAgent, address[](32):bridgeAgents, uint256(32):bridgeAgentsLenght, mapping(32):getBridgeAgentManager, mapping(32):isBridgeAgentFactory, address[](32):bridgeAgentFactories, uint256(32):bridgeAgentFactoriesLenght, mapping(32):isGlobalAddress, mapping(32):getGlobalTokenFromLocal, mapping(32):getLocalTokenFromGlobal, mapping(32):getLocalTokenFromUnder, mapping(32):getUnderlyingTokenFromLocal, mapping(32):getWrappedNativeToken, mapping(32):getGasPoolInfo, address(20):localBranchPortAddress, bool(1):_setup, address(20):coreRootRouterAddress, address(20):coreRootBridgeAgentAddress
34:       address public localBranchPortAddress;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootPort.sol#L34


### [G&#x2011;04] Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 5 instances of this issue:*

```solidity
File: src/governance/GovernorBravoInterfaces.sol

/// @audit Variable ordering with 13 slots instead of the current 14:
///           uint256(32):id, uint256(32):eta, address[](32):targets, uint256[](32):values, string[](32):signatures, bytes[](32):calldatas, uint256(32):startBlock, uint256(32):endBlock, uint256(32):forVotes, uint256(32):againstVotes, uint256(32):abstainVotes, mapping(32):receipts, address(20):proposer, bool(1):canceled, bool(1):executed
105       struct Proposal {
106           /// @notice Unique id for looking up a proposal
107           uint256 id;
108           /// @notice Creator of the proposal
109           address proposer;
110           /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
111           uint256 eta;
112           /// @notice the ordered list of target addresses for calls to be made
113           address[] targets;
114           /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
115           uint256[] values;
116           /// @notice The ordered list of function signatures to be called
117           string[] signatures;
118           /// @notice The ordered list of calldata to be passed to each call
119           bytes[] calldatas;
120           /// @notice The block at which voting begins: holders must delegate their votes prior to this block
121           uint256 startBlock;
122           /// @notice The block at which voting ends: votes must be cast prior to this block
123           uint256 endBlock;
124           /// @notice Current number of votes in favor of this proposal
125           uint256 forVotes;
126           /// @notice Current number of votes in opposition to this proposal
127           uint256 againstVotes;
128           /// @notice Current number of votes for abstaining for this proposal
129           uint256 abstainVotes;
130           /// @notice Flag marking whether the proposal has been canceled
131           bool canceled;
132           /// @notice Flag marking whether the proposal has been executed
133           bool executed;
134           /// @notice Receipts of ballots for the entire set of voters
135           mapping(address => Receipt) receipts;
136:      }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoInterfaces.sol#L105-L136

```solidity
File: src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol

/// @audit Variable ordering with 4 slots instead of the current 5:
///           uint256(32):amount, uint256(32):deposit, address(20):hToken, uint24(3):toChain, address(20):token
26    struct DepositInput {
27        //Deposit Info
28        address hToken; //Input Local hTokens Address.
29        address token; //Input Native / underlying Token Address.
30        uint256 amount; //Amount of Local hTokens deposited for interaction.
31        uint256 deposit; //Amount of native tokens deposited for interaction.
32        uint24 toChain; //Destination chain for interaction.
33:   }

/// @audit Variable ordering with 5 slots instead of the current 6:
///           address[](32):hTokens, address[](32):tokens, uint256[](32):amounts, uint256[](32):deposits, uint128(16):depositedGas, uint32(4):depositNonce, uint24(3):toChain, uint8(1):numberOfAssets
55    struct DepositMultipleParams {
56        //Deposit Info
57        uint8 numberOfAssets; //Number of assets to deposit.
58        uint32 depositNonce; //Deposit nonce.
59        address[] hTokens; //Input Local hTokens Address.
60        address[] tokens; //Input Native / underlying Token Address.
61        uint256[] amounts; //Amount of Local hTokens deposited for interaction.
62        uint256[] deposits; //Amount of native tokens deposited for interaction.
63        uint24 toChain; //Destination chain for interaction.
64        uint128 depositedGas; //BRanch chain gas token amount sent with request.
65:   }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33

```solidity
File: src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol

/// @audit Variable ordering with 4 slots instead of the current 5:
///           uint256(32):amount, uint256(32):deposit, address(20):hToken, uint32(4):depositNonce, uint24(3):toChain, address(20):token
63    struct DepositParams {
64        //Deposit Info
65        uint32 depositNonce; //Deposit nonce.
66        address hToken; //Input Local hTokens Address.
67        address token; //Input Native / underlying Token Address.
68        uint256 amount; //Amount of Local hTokens deposited for interaction.
69        uint256 deposit; //Amount of native tokens deposited for interaction.
70        uint24 toChain; //Destination chain for interaction.
71:   }

/// @audit Variable ordering with 5 slots instead of the current 6:
///           address[](32):hTokens, address[](32):tokens, uint256[](32):amounts, uint256[](32):deposits, uint32(4):depositNonce, uint24(3):toChain, uint8(1):numberOfAssets
73    struct DepositMultipleParams {
74        //Deposit Info
75        uint8 numberOfAssets; //Number of assets to deposit.
76        uint32 depositNonce; //Deposit nonce.
77        address[] hTokens; //Input Local hTokens Address.
78        address[] tokens; //Input Native / underlying Token Address.
79        uint256[] amounts; //Amount of Local hTokens deposited for interaction.
80        uint256[] deposits; //Amount of native tokens deposited for interaction.
81        uint24 toChain; //Destination chain for interaction.
82:   }

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71


### [G&#x2011;05] Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 2 instances of this issue:*

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

179:              QueuedRewards memory queuedRewards = gaugeQueuedRewards[gauge];

204:          QueuedRewards memory queuedRewards = gaugeQueuedRewards[ERC20(msg.sender)];

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelGaugeRewards.sol#L179


### [G&#x2011;06] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 94 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/gauges/BaseV2Gauge.sol

/// @audit multiRewardsDepot on line 133
135:          multiRewardsDepot.addAsset(flyWheelRewards, bribeFlywheel.rewardToken());

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/gauges/BaseV2Gauge.sol#L135

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

/// @audit whitelistGuardian on line 464
466:          emit WhitelistGuardianSet(oldGuardian, whitelistGuardian);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L466

```solidity
File: src/governance/GovernorBravoDelegator.sol

/// @audit implementation on line 48
50:           emit NewImplementation(oldImplementation, implementation);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegator.sol#L50

```solidity
File: src/hermes/minters/BaseV2Minter.sol

/// @audit dao on line 145
145:              if (dao != address(0)) underlying.safeTransfer(dao, share);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/minters/BaseV2Minter.sol#L145

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

/// @audit partners on line 59
60:           partners.push(newPartnerManager);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/factories/PartnerManagerFactory.sol#L60

```solidity
File: src/maia/PartnerUtilityManager.sol

/// @audit partnerVault on line 75
76:               IBaseVault(partnerVault).applyWeight();

/// @audit partnerVault on line 85
86:               IBaseVault(partnerVault).applyBoost();

/// @audit partnerVault on line 95
96:               IBaseVault(partnerVault).applyGovernance();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/PartnerUtilityManager.sol#L76

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

/// @audit partnerVault on line 74
65:               partnerVault

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/maia/tokens/ERC4626PartnerManager.sol#L65

```solidity
File: src/rewards/base/FlywheelCore.sol

/// @audit flywheelRewards on line 126
128:              rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance);

/// @audit flywheelBooster on line 164
165:                  ? flywheelBooster.boostedTotalSupply(strategy)

/// @audit flywheelBooster on line 196
197:              ? flywheelBooster.boostedBalanceOf(strategy, user)

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/base/FlywheelCore.sol#L128

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

/// @audit _assets on line 60
62:           delete _isAsset[_assets[rewardsContract]];

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/depots/MultiRewardsDepot.sol#L62

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

/// @audit nextCycleQueuedRewards on line 134
137:          uint112 queued = nextCycleQueuedRewards;

/// @audit paginationOffset on line 122
125:          uint32 offset = paginationOffset;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelGaugeRewards.sol#L137

```solidity
File: src/talos/base/TalosBaseStrategy.sol

/// @audit tokenId on line 155
160:          emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares);

/// @audit tokenId on line 190
203:                  tokenId: _tokenId,

/// @audit tokenId on line 255
265:                      tokenId: _tokenId,

/// @audit tokenId on line 265
283:                  tokenId: _tokenId,

/// @audit tokenId on line 299
305:          emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

/// @audit tokenId on line 305
307:          afterRerange(tokenId); // tokenId changed in doRerange

/// @audit tokenId on line 312
318:          emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

/// @audit tokenId on line 318
320:          afterRerange(tokenId); // tokenId changed in doRerange

/// @audit tokenId on line 355
365:                  tokenId: _tokenId,

/// @audit liquidity on line 261
266:                      liquidity: liquidityToDecrease,

/// @audit liquidity on line 350
356:                  liquidity: _liquidity,

/// @audit tickLower on line 120
/// @audit tickLower on line 140
140:                  tickLower: tickLower,

/// @audit tickUpper on line 121
/// @audit tickUpper on line 141
141:                  tickUpper: tickUpper,

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L160

```solidity
File: src/ulysses-amm/UlyssesPool.sol

/// @audit bandwidthStateList on line 169
176:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit bandwidthStateList on line 249
253:              for (uint256 i = 1; i < bandwidthStateList.length;) {

/// @audit bandwidthStateList on line 260
278:              for (uint256 i = 1; i < bandwidthStateList.length;) {

/// @audit bandwidthStateList on line 278
280:                      if (i == bandwidthStateList.length - 1) {

/// @audit bandwidthStateList on line 284
296:          for (uint256 i = 1; i < bandwidthStateList.length; i++) {

/// @audit totalWeights on line 176
181:          uint256 oldTotalWeights = totalWeights;

/// @audit totalWeights on line 183
212:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit totalWeights on line 233
238:          uint256 oldTotalWeights = totalWeights;

/// @audit totalWeights on line 241
243:          if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights) {

/// @audit totalWeights on line 243
297:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit bandwidthStateList[i].bandwidth on line 133
135:              assets += bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i].weight on line 176
212:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit bandwidthStateList[i].bandwidth on line 178
190:              uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i].bandwidth on line 192
194:                  newBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i].bandwidth on line 194
214:              newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

/// @audit bandwidthStateList[i].weight on line 233
284:                              leftOverBandwidth.mulDiv(bandwidthStateList[i].weight, weightsWithoutPool).toUint248();

/// @audit bandwidthStateList[i].weight on line 284
297:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit bandwidthStateList[i].bandwidth on line 283
299:              newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesPool.sol#L176

```solidity
File: src/ulysses-amm/UlyssesToken.sol

/// @audit assets on line 89
95:           for (uint256 i = 0; i < assets.length; i++) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesToken.sol#L95

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

/// @audit localChainId on line 62
66:           address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);

/// @audit rootPortAddress on line 49
54:           IRootPort(rootPortAddress).mintToLocalBranch(_recipient, globalToken, _deposit);

/// @audit rootPortAddress on line 62
66:           address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);

/// @audit rootPortAddress on line 66
70:           IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchPort.sol#L66

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

/// @audit localPortAddress on line 83
93:           if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L93

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

/// @audit localBridgeAgentAddress on line 39
40:           bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BaseBranchRouter.sol#L40

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

/// @audit bridgeAgentExecutorAddress on line 1154
1177:             try BranchBridgeAgentExecutor(bridgeAgentExecutorAddress).executeWithSettlement(

/// @audit bridgeAgentExecutorAddress on line 1177
1201:             try BranchBridgeAgentExecutor(bridgeAgentExecutorAddress).executeWithSettlementMultiple(

/// @audit getDeposit[_depositNonce].hTokens on line 338
353:                      getDeposit[_depositNonce].hTokens[0],

/// @audit getDeposit[_depositNonce].hTokens on line 353
365:          } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {

/// @audit getDeposit[_depositNonce].hTokens on line 365
373:                      uint8(getDeposit[_depositNonce].hTokens.length),

/// @audit getDeposit[_depositNonce].tokens on line 339
342:                          getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

/// @audit getDeposit[_depositNonce].tokens on line 342
354:                      getDeposit[_depositNonce].tokens[0],

/// @audit getDeposit[_depositNonce].tokens on line 354
357:                          getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

/// @audit getDeposit[_depositNonce].amounts on line 340
355:                      getDeposit[_depositNonce].amounts[0],

/// @audit getDeposit[_depositNonce].deposits on line 342
357:                          getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

/// @audit getDeposit[nonce].hTokens on line 375
387:                      uint8(getDeposit[nonce].hTokens.length),

/// @audit getDeposit[nonce].hTokens on line 387
389:                      getDeposit[nonce].hTokens,

/// @audit getDeposit[nonce].tokens on line 376
378:                      _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),

/// @audit getDeposit[nonce].tokens on line 378
390:                      getDeposit[nonce].tokens,

/// @audit getDeposit[nonce].tokens on line 390
392:                      _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),

/// @audit getDeposit[nonce].amounts on line 377
391:                      getDeposit[nonce].amounts,

/// @audit getDeposit[nonce].deposits on line 378
392:                      _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L1177

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

/// @audit localPortAddress on line 133
143:          if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {

/// @audit localPortAddress on line 164
165:              IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);

/// @audit localPortAddress on line 165
167:              IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);

/// @audit localPortAddress on line 178
179:          IPort(localPortAddress).toggleBridgeAgent(_branchBridgeAgent);

/// @audit localPortAddress on line 190
191:              IPort(localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio);

/// @audit localPortAddress on line 191
193:              IPort(localPortAddress).toggleStrategyToken(_underlyingToken);

/// @audit localPortAddress on line 212
214:              IPort(localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

/// @audit localPortAddress on line 214
217:              IPort(localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

/// @audit localPortAddress on line 217
220:              IPort(localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreBranchRouter.sol#L143

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

/// @audit bridgeAgentAddress on line 120
123:          IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(

/// @audit bridgeAgentAddress on line 148
155:          IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/MulticallRootRouter.sol#L123

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

/// @audit initialGas on line 875
1161:             if (initialGas > 0) {

/// @audit initialGas on line 1161
1171:         if (initialGas > 0) {

/// @audit userFeeInfo on line 247
248:              userFeeInfo.gasToBridgeOut = _remoteExecutionGas;

/// @audit userFeeInfo on line 751
754:              return uint128(userFeeInfo.gasToBridgeOut);

/// @audit userFeeInfo on line 754
758:              if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();

/// @audit userFeeInfo on line 758
759:              (amountOut, gasToken) = _gasSwapOut(userFeeInfo.gasToBridgeOut, _toChain);

/// @audit userFeeInfo on line 908
/// @audit userFeeInfo on line 1162
1162:                 _payExecutionGas(userFeeInfo.depositedGas, userFeeInfo.gasToBridgeOut, _initialGas, fromChainId);

/// @audit userFeeInfo on line 1162
/// @audit userFeeInfo on line 1172
1172:             _payExecutionGas(userFeeInfo.depositedGas, userFeeInfo.gasToBridgeOut, _initialGas, fromChainId);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgent.sol#L1161

```solidity
File: src/ulysses-omnichain/RootPort.sol

/// @audit coreRootRouterAddress on line 435
442:          IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootPort.sol#L442

</details>

### [G&#x2011;07] Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There are 54 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-20/ERC20Boost.sol

/// @audit _userGauges[<etc>] on line 204
213:                  require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.

/// @audit _userGauges[<etc>] on line 232
239:              require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Boost.sol#L213

```solidity
File: src/erc-20/ERC20Gauges.sol

/// @audit _userGauges[user] on line 210
211:          if (added && _userGauges[user].length() > maxGauges && !canContractExceedMaxGauges[user]) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Gauges.sol#L211

```solidity
File: src/erc-20/ERC20MultiVotes.sol

/// @audit _delegates[user] on line 324
339:                      require(_delegates[user].remove(delegatee)); // Remove from set. Should never fail.

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L339

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

/// @audit pools[<etc>] on line 111
124:              pools[poolIds[i]].transferOwnership(owner);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/factories/UlyssesFactory.sol#L124

```solidity
File: src/ulysses-amm/UlyssesPool.sol

/// @audit bandwidthStateList[i] on line 131
133:              assets += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

/// @audit bandwidthStateList[i] on line 133
135:              assets += bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i] on line 176
178:              oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

/// @audit bandwidthStateList[i] on line 178
190:              uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i] on line 190
192:                  bandwidthStateList[i].bandwidth = oldBandwidth.mulDivUp(oldTotalWeights, newTotalWeights).toUint248();

/// @audit bandwidthStateList[i] on line 192
194:                  newBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i] on line 194
212:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit bandwidthStateList[i] on line 212
214:              newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

/// @audit bandwidthStateList[i] on line 233
235:              oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

/// @audit bandwidthStateList[i] on line 235
255:                      uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i] on line 255
257:                          bandwidthStateList[i].bandwidth =

/// @audit bandwidthStateList[i] on line 257
260:                          leftOverBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;

/// @audit bandwidthStateList[i] on line 260
281:                          bandwidthStateList[i].bandwidth += leftOverBandwidth.toUint248();

/// @audit bandwidthStateList[i] on line 281
283:                          bandwidthStateList[i].bandwidth +=

/// @audit bandwidthStateList[i] on line 283
284:                              leftOverBandwidth.mulDiv(bandwidthStateList[i].weight, weightsWithoutPool).toUint248();

/// @audit bandwidthStateList[i] on line 284
297:              uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

/// @audit bandwidthStateList[i] on line 297
299:              newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesPool.sol#L133

```solidity
File: src/ulysses-amm/UlyssesToken.sol

/// @audit assets[i] on line 112
116:                  assets[i].safeTransfer(msg.sender, assetBalance - newAssetBalance);

/// @audit assets[i] on line 116
118:                  assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesToken.sol#L116

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

/// @audit getDeposit[_depositNonce] on line 327
332:          if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {

/// @audit getDeposit[_depositNonce] on line 332
338:                      getDeposit[_depositNonce].hTokens[0],

/// @audit getDeposit[_depositNonce] on line 338
339:                      getDeposit[_depositNonce].tokens[0],

/// @audit getDeposit[_depositNonce] on line 339
340:                      getDeposit[_depositNonce].amounts[0],

/// @audit getDeposit[_depositNonce] on line 340
/// @audit getDeposit[_depositNonce] on line 342
342:                          getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

/// @audit getDeposit[_depositNonce] on line 342
353:                      getDeposit[_depositNonce].hTokens[0],

/// @audit getDeposit[_depositNonce] on line 353
354:                      getDeposit[_depositNonce].tokens[0],

/// @audit getDeposit[_depositNonce] on line 354
355:                      getDeposit[_depositNonce].amounts[0],

/// @audit getDeposit[_depositNonce] on line 355
/// @audit getDeposit[_depositNonce] on line 357
357:                          getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

/// @audit getDeposit[_depositNonce] on line 357
365:          } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {

/// @audit getDeposit[_depositNonce] on line 365
373:                      uint8(getDeposit[_depositNonce].hTokens.length),

/// @audit getDeposit[nonce] on line 375
376:                      getDeposit[nonce].tokens,

/// @audit getDeposit[nonce] on line 376
377:                      getDeposit[nonce].amounts,

/// @audit getDeposit[nonce] on line 377
/// @audit getDeposit[nonce] on line 378
378:                      _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),

/// @audit getDeposit[nonce] on line 378
387:                      uint8(getDeposit[nonce].hTokens.length),

/// @audit getDeposit[nonce] on line 387
389:                      getDeposit[nonce].hTokens,

/// @audit getDeposit[nonce] on line 389
390:                      getDeposit[nonce].tokens,

/// @audit getDeposit[nonce] on line 390
391:                      getDeposit[nonce].amounts,

/// @audit getDeposit[nonce] on line 391
/// @audit getDeposit[nonce] on line 392
392:                      _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),

/// @audit getDeposit[_depositNonce] on line 373
408:          getDeposit[_depositNonce].status = DepositStatus.Success;

/// @audit getDeposit[_depositNonce] on line 408
411:          getDeposit[_depositNonce].depositedGas = msg.value.toUint128();

/// @audit getDeposit[_depositNonce] on line 1069
1075:         getDeposit[_depositNonce].depositedGas -= minExecCost.toUint128();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L332

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

/// @audit getSettlement[_depositNonce] on line 257
260:          if (getSettlement[_depositNonce].status != SettlementStatus.Failed || depositOwner == address(0)) {

/// @audit getSettlement[_settlementNonce] on line 839
845:          getSettlement[_settlementNonce].gasToBridgeOut -= minExecCost.toUint128();

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgent.sol#L260

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

/// @audit deposits[tokenId] on line 491
501:          deposits[tokenId].stakedTimestamp = uint40(block.timestamp);

/// @audit incentives[incentiveId] on line 483
502:          incentives[incentiveId].numberOfStakes++;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/UniswapV3Staker.sol#L501

</details>





## Invalid Issues

The issues below may be reported by other bots/wardens, but can be ignored since either the rule or the specified instances are invalid


### [I&#x2011;01] ~~Do not calculate `constant` variables, which will save gas~~
The compiler automatically expands simple expressions into their literal values, so manually doing the expansion [doesn't save gas](https://twitter.com/GalloDaSballo/status/1543729080926871557), and this finding is invalid

*There are 3 instances of this issue:*

```solidity
File: src/erc-20/ERC20MultiVotes.sol

360      bytes32 public constant DELEGATION_TYPEHASH =
361:         keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

42       bytes32 public constant DOMAIN_TYPEHASH =
43:          keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

46:      bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L42-L43


### [I&#x2011;02] ~~Use delete instead of setting mapping/state variable to zero, to save gas~~
Using delete instead of assigning zero to state variables does not save any extra gas - they both refund the same amount, so this finding is invalid

*There are 24 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/erc-20/ERC20Boost.sol

249:         getUserBoost[msg.sender] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Boost.sol#L249-L249

```solidity
File: src/erc-20/ERC20MultiVotes.sol

340:                     _delegatesVotesCount[user][delegatee] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L340-L340

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

149:         newProposal.eta = 0;

156:         newProposal.forVotes = 0;

157:         newProposal.againstVotes = 0;

158:         newProposal.abstainVotes = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L149-L149

```solidity
File: src/hermes/minters/BaseV2Minter.sol

165:         weekly = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/hermes/minters/BaseV2Minter.sol#L165-L165

```solidity
File: src/rewards/base/FlywheelCore.sol

98:              rewardsAccrued[user] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/base/FlywheelCore.sol#L98-L98

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

100:         nextCycleQueuedRewards = 0;

101:         paginationOffset = 0;

122:             paginationOffset = 0;

145:             nextCycleQueuedRewards = 0;

146:             paginationOffset = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/rewards/FlywheelGaugeRewards.sol#L100-L100

```solidity
File: src/talos/base/TalosBaseStrategy.sol

362:         liquidity = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L362-L362

```solidity
File: src/talos/libraries/PoolVariables.sol

211:         secondsAgo[1] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/libraries/PoolVariables.sol#L211-L211

```solidity
File: src/ulysses-amm/UlyssesToken.sol

78:          assetId[asset] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-amm/UlyssesToken.sol#L78-L78

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

202:         incentive.totalRewardUnclaimed = 0;

268:             rewards[msg.sender] = 0;

279:         rewards[msg.sender] = 0;

406:                 _userAttachements[owner][key.pool] = 0;

426:         deposit.stakedTimestamp = 0;

454:         stake.secondsPerLiquidityInsideInitialX128 = 0;

455:         stake.liquidityNoOverflow = 0;

456:         if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/UniswapV3Staker.sol#L202-L202

</details>

### [I&#x2011;03] ~~`abi.encode()` is less efficient than `abi.encodepacked()`~~
`abi.encodePacked()` does not always save gas over `abi.encode()` and in fact often costs [more](https://gist.github.com/IllIllI000/2ee970e4f05af4d2a3d89a56b5cc93a5) gas. The [comparison](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison) sometimes linked to itself even shows that when addresses are involved, the packed flavor costs more gas. Furthermore, the test was done on Solidity 0.4.24, and there have been a lot of changes since then. Without more specific tests or explanations, it's not correct to say that the change always saves gas.

*There are 15 instances of this issue:*

```solidity
File: src/erc-20/ERC20MultiVotes.sol

368:                     "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L368-L368

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

198:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

346:             keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L198-L198

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

53:          bytes memory data = abi.encode(_underlyingAddress, address(0), name, symbol);

98:          bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L53-L53

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

48:          bytes memory data = abi.encode(msg.sender, _globalAddress, _toChain, _rootExecutionGas);

72:          bytes memory data = abi.encode(_underlyingAddress, newToken, name, symbol);

105:         bytes memory data = abi.encode(_globalAddress, newToken);

148:         bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreBranchRouter.sol#L48-L48

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

107:         bytes memory data = abi.encode(

158:         bytes memory data = abi.encode(

238:         bytes memory data = abi.encode(_branchBridgeAgentFactory);

259:         bytes memory data = abi.encode(_branchBridgeAgent);

282:         bytes memory data = abi.encode(_underlyingToken, _minimumReservesRatio);

309:         bytes memory data = abi.encode(_portStrategy, _underlyingToken, _dailyManagementLimit, _isUpdateDailyLimit);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/CoreRootRouter.sol#L107-L107


### [I&#x2011;04] ~~Contracts are not using their OZ Upgradeable counterparts~~
The rule is true only when the contract being defined is upgradeable, which isn't the case for these invalid examples

*There are 8 instances of this issue:*

<details>
<summary>see instances</summary>


```solidity
File: src/talos/base/TalosBaseStrategy.sol

13:  import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/base/TalosBaseStrategy.sol#L13-L13

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

9:   import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/boost-aggregator/BoostAggregator.sol#L9-L9

```solidity
File: src/talos/interfaces/IBoostAggregator.sol

6:   import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/IBoostAggregator.sol#L6-L6

```solidity
File: src/talos/interfaces/ITalosBaseStrategy.sol

7:   import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/talos/interfaces/ITalosBaseStrategy.sol#L7-L7

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

9:   import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/VirtualAccount.sol#L9-L9

```solidity
File: src/ulysses-omnichain/interfaces/IVirtualAccount.sol

4:   import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/interfaces/IVirtualAccount.sol#L4-L4

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

12:  import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/UniswapV3Staker.sol#L12-L12

```solidity
File: src/uni-v3-staker/interfaces/IUniswapV3Staker.sol

5:   import {IERC721Receiver} from "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L5-L5

</details>

### [I&#x2011;05] ~~Change `public` function visibility to `external` to save gas~~
Both `public` and `external` functions use the same amount of gas (both deployment and runtime gas), so this finding is invalid

*There are 5 instances of this issue:*

```solidity
File: src/erc-20/ERC20Boost.sol

175:     function decrementGaugeBoost(address gauge, uint256 boost) public {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20Boost.sol#L175-L175

```solidity
File: src/erc-20/ERC20MultiVotes.sol

131:     function delegates(address delegator) public view returns (address[] memory) {

363:     function delegateBySig(address delegatee, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s) public {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/erc-20/ERC20MultiVotes.sol#L131-L131

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

104      function propose(
105          address[] memory targets,
106          uint256[] memory values,
107          string[] memory signatures,
108          bytes[] memory calldatas,
109          string memory description
110:     ) public returns (uint256) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L104-L110

```solidity
File: src/rewards/base/FlywheelCore.sol

84:      function accrue(ERC20 strategy, address user, address secondUser) public returns (uint256, uint256) {

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/rewards/base/FlywheelCore.sol#L84-L84


### [I&#x2011;06] ~~Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists~~
`safeTransferETH()` does not call functions on a contract and instead directly sends funds to the contract, so contract existence checks are not needed for these library calls.

*There are 3 instances of this issue:*

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

165:             SafeTransferLib.safeTransferETH(_recipient, gasRemaining);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L165-L165

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1044:        SafeTransferLib.safeTransferETH(_recipient, gasRemaining - minExecCost);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/BranchBridgeAgent.sol#L1044-L1044

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1263:        SafeTransferLib.safeTransferETH(daoAddress, _accumulatedFees);

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/ulysses-omnichain/RootBridgeAgent.sol#L1263-L1263


### [I&#x2011;07] ~~Save gas with the use of specific import statements~~
Importing whole files rather than specific identifiers [does not waste gas](https://ethereum.stackexchange.com/questions/138876/does-solidity-optimizer-eliminate-unused-internal-functions-of-libraries), so this finding is invalid

*There are 2 instances of this issue:*

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

4:   import "./GovernorBravoInterfaces.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegateMaia.sol#L4-L4

```solidity
File: src/governance/GovernorBravoDelegator.sol

4:   import "./GovernorBravoInterfaces.sol";

```
https://github.com/code-423n4/2023-05-maia/blob/78e49c651fd119b85bb79296620d5cb39efa7cdd/src/governance/GovernorBravoDelegator.sol#L4-L4