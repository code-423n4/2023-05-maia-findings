## Gas Optimizations List

| Number | Optimization Details                                        | Instances |
| ------ | ------------------------------------------------------ | ------------------ |
| [G-01] | Do not calculate constants                             | 1                  |
| [G-02] | Use constants instead of `type(uintx).max`             | 20                 |
| [G-03] | Check `Require()` / `Revert()` statements first         | 1                  |
| [G-04] | Avoid multiple check combinations with nested `if`     | 26                 |
| [G-05] | Structs can be packed into fewer storage slots          | 1                  |
| [G-06] | Expressions for constant values should use `immutable` | 3                  |
| [G-07] | Use hardcoded address instead of `address(this)`        | 92                 |
| [G-08] | Optimize names to save gas                             | -                  |
| [G-09] | State variables can be cached instead of re-reading them from storage  | 4 |
| [G-10] |  Using `abi.encodePacked()` is more gas efficient than `abi.encode()` | 19 |



### [G-01] Do not calculate constants

The implementation of constant variables (replacements at compile-time) leads to the recomputation of an expression assigned to a constant variable each time the variable is used, wasting gas. Therefore constants should not involve calculations, rather the final value (e.g. `200_000` instead of `2 * 1e5`). For clarity, use comments to show where that value is being derived from.


*There is 1 instance of this issue:*
```solidity
File: TalosStrategyVanilla.sol
47:     uint24 private constant protocolFee = 2 * 1e5; //20%
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47



### [G-02] Use constants instead of `type(uintx).max`

Using `type(uintx).max` such as `type(uint128).max` incurs more gas in distribution and for each transaction. Constants (such as `340282366920938463463374607431768211455` for `type(uint128).max`) should be used instead.


*There are 20 instances of this issue:*

```solidity
File: FlywheelGaugeRewards.sol
133             require(newRewards <= type(uint112).max); // safe cast
187             require(nextRewards <= type(uint112).max); // safe cast
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L133
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L187

```solidity
File: TalosBaseStrategy.sol
130         address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);
131         address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);
249             if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares;
285                 amount0Max: type(uint128).max,
286                 amount1Max: type(uint128).max
367                 amount0Max: type(uint128).max,
368                 amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L130
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L131
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L249
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L285
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L286
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L367
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L368

```solidity
File: TalosStrategyStaked.sol
151                     amount0Max: type(uint128).max,
152                     amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L151
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L152

```solidity
File: TalosStrategyVanilla.sol
111                 amount0Max: type(uint128).max,
112                 amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L111
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla

.sol#L112

```solidity
File: UniswapV3Gauge.sol
45         rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L45

```solidity
File: BaseFlywheelRewards.sol
36         _rewardToken.safeApprove(address(_flywheel), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L36

```solidity
File: UlyssesRouter.sol
40             address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40

```solidity
File: ERC4626PartnerManager.sol
200         address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
201         address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
202         address(governance).safeApprove(newPartnerVault, type(uint256).max);
203         address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L200
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L201
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L202
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L203






### [G-03] Check `Require()` / `Revert()` statements that use less gas first

Checks that cost less gas, such as checking function parameters, should be placed before more expensive checks such as ones that invoke `msg.sender`, because the function will be able to revert before checking more expensive statements.


*There is 1 instance of this issue:*

```diff
--- a/src/governance/GovernorBravoDelegateMaia.sol
+++ b/src/governance/GovernorBravoDelegateMaia.sol
@@ -61,7 +61,6 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE       
         uint256 proposalThreshold_
     ) public virtual {
         require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");
-        require(msg.sender == admin, "GovernorBravo::initialize: admin only");
         require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");
         require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");
         require(
@@ -76,6 +75,7 @@ contract GovernorBravoDelegate is GovernorBravoDelegateStorageV2, GovernorBravoE
             proposalThreshold_ >= MIN_PROPOSAL_THRESHOLD && proposalThreshold_ <= MAX_PROPOSAL_THRESHOLD,
             "GovernorBravo::initialize: invalid proposal threshold"
         );
+        require(msg.sender == admin, "GovernorBravo::initialize: admin only");

         timelock = TimelockInterface(timelock_);
         govToken = GovTokenInterface(govToken_);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L63-L78







### [G-04] Avoid multiple check combinations by using nested `if` statements

Using nested `if` statements is cheaper than using multiple check combinations with `&&`.


*There are 26 instances of this issue:*



```solidity
File: GovernorBravoDelegateMaia.sol
  234         if (msg.sender != proposal.proposer && msg.sender != admin);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L234

```solidity
File: FlywheelGaugeRewards.sol
  210         if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L210

```solidity
File: TalosBaseStrategy.sol
  151         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
  212         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
  273             if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
  335         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L151
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L212
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L273
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L335

```solidity
File: PoolVariables.sol
  98         if (tick < 0 && tick % tickSpacing != 0) compressed--;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L98

```solidity
File: ERC20Gauges.sol
  211         if (added && _userGauges) > maxGauges && !canContractExceedMaxGauges[user]);
  464         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L211
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L464

```solidity
File: ERC20MultiVotes.sol
  105         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts
  194         if (newDelegate && delegateCount(delegator) > maxDelegates && !canContractExceedMaxDelegates[delegator]);
  250         if (pos > 0 && ckpts[pos - 1].fromBlock == block.number);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L105
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L194
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250

```solidity
File: BaseV2GaugeFactory.sol
  161         if (msg.sender != bribesFactory.owner() && msg.sender != owner());
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L161

```solidity
File: BranchBridgeAgent.sol
  474         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
  499         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
  521         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
  543         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L521
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L543

```solidity
File: RootBridgeAgent.sol
  301         if (localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0));
  347             if (hTokens);
  648         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L301
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L347
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L648

```solidity
File: VirtualAccount.sol
  70         if ((!IRootPort(localPortAddress).isRouterApproved(this, msg.sender)) && (msg.sender != userAddress));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L70

```solidity
File: ERC20hTokenRootFactory.sol
  75         if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L75

```solidity
File: UlyssesFactory.sol
  111                 if (j != i && weightspoolIds[j], weights[i][j]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111

```solidity
File: PartnerUtilityManager.sol
  75         if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0);
  85         if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0);
  95         if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95










### [G-05] Structs can be packed into fewer storage slots


*There is 1 instance of this issue:*
The `Proposal` struct can be packed as suggested below:

```solidity
File: GovernorBravoInterfaces.sol
105:     struct Proposal {
106:         /// @notice Unique id for looking up a proposal
107:         uint256 id;
108:         /// @notice Creator of the proposal
109:         address proposer;
110:         /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
111:         uint256 eta;
112:         /// @notice the ordered list of target addresses for calls to be made
113:         address[] targets;
114:         /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
115:         uint256[] values;
116:         /// @notice The ordered list of function signatures to be called
117:         string[] signatures;
118:         /// @notice The ordered list of calldata to be passed to each call
119:         bytes[] calldatas;
120:         /// @notice The block at which voting begins: holders must delegate their votes prior to this block
121:         uint256 startBlock;
122:         /// @notice The block at which voting ends: votes must be cast prior to this block
123:         uint256 endBlock;
124:         /// @notice Current number of votes in favor of this proposal
125:         uint256 forVotes;
126:         /// @notice Current number of votes in opposition to this proposal
127:         uint256 againstVotes;
128:         /// @notice Current number of votes for abstaining for this proposal
129:         uint256 abstainVotes;
130:         /// @notice Flag marking whether the proposal has been canceled
131:         bool canceled;
132:         /// @notice Flag marking whether the proposal has been executed
133:         bool executed;
134:         /// @notice Receipts of ballots for the entire set of voters
135:         mapping(address => Receipt) receipts;
136:     }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136

```diff
105:     struct Proposal {
106:         /// @notice Unique id for looking up a proposal
107:         uint256 id;
108:         /// @notice Creator of the proposal
109:         address proposer;
110:         /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
111:         uint256 eta;
112:         /// @notice the ordered list of target addresses for calls to be made
113:         address[] targets;
114:         /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
115:         uint256[] values;
116:         /// @notice The ordered list of function signatures to be called
117:         string[] signatures;
118:         /// @notice The ordered list of calldata to be passed to each call
119:         bytes[] calldatas;
120:         /// @notice The block at which voting begins: holders must delegate their votes prior to this block
121:         uint256 startBlock;
122:         /// @notice The block at which voting ends: votes must be cast prior to this block
123:         uint256 endBlock;
-124:         /// @notice Current number of votes in favor of this proposal
-125:         uint256 forVotes;
-126:         /// @notice Current number of votes in opposition to this proposal
-127:         uint256 againstVotes;
-128:         /// @notice Current number of votes for abstaining for this proposal
-129:         uint256 abstainVotes;
+124:         /// @notice Current number of votes in favor of this proposal
+125:         uint64 forVotes;
+126:         /// @notice Current number of votes in opposition to this proposal
+127:         uint64 againstVotes;
+128:         /// @notice Current number of votes for abstaining for this proposal
+129:         uint64 abstainVotes;
130:         /// @notice Flag marking whether the proposal has been canceled
131:         bool canceled;
132:         /// @notice Flag marking whether the proposal has been executed
133:         bool executed;
134:         /// @notice Receipts of ballots for the entire set of voters
135:         mapping(address => Receipt) receipts;
136:     }
```







### [G-06] Expressions for constant values such as calling `keccak256()` should use `immutable` rather than `constant`

Constant values for function calls result in increased gas costs as `immutable`s are evaluated at deployment time, in contrast to `constants` which are executed at runtime for each invocation.


*There are 3 instances of this issue:*


```solidity
File: GovernorBravoDelegateMaia.sol
42:     bytes32 public constant DOMAIN_TYPEHASH =
43:         keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-L43
```solidity
File: GovernorBravoDelegateMaia.sol
46:     bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46
```solidity
File: ERC20MultiVotes.sol
360:     bytes32 public constant DELEGATION_TYPEHASH =
361:         keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361



### [G-07] Use hardcode address instead of `address(this)`

Instead of address(this), it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's script.sol and solmate ```LibRlp.sol``` contracts can do this.

References:
- https://book.getfoundry.sh/reference/forge-std/compute-create-address
- https://twitter.com/transmissions11/status/1518507047943245824


*There are 92 instances of this issue:*

```solidity
File: GovernorBravoDelegateMaia.sol
  346             keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
File: TalosStrategyStakedFactory.sol
  51             address(this);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol#L51

```solidity
File: FlywheelGaugeRewards.sol
  88         uint256 balanceBefore = rewardToken.balanceOf(address(this));
  90         require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);
  130             uint256 balanceBefore = rewardToken.balanceOf(address(this));
  132             require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L88-L90  
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L130-L132

```solidity
File: TalosBaseStrategy.sol
  125         address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);
  126         address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);
  146                 recipient: address(this),
  196         address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);
  197         address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);
  366                 recipient: address(this),
  406         uint256 balance0 = _token0.balanceOf(address(this));
  407         uint256 balance1 = _token1.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L125-L126  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L146  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L196  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L366  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L406  
```solidity
File: BoostAggregator.sol
  90         nonfungiblePositionManager.safeTransferFrom(address(this), address(uniswapV3Staker), tokenId);
  168         address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));
  175         hermesGaugeBoost.updateUserBoost(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L90  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L168  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L175  

```solidity
File: PoolActions.sol
  47             address(this),
  84                 recipient: address(this),
  98         balance0 = token0.balanceOf(address(this));
  99         balance1 = token1.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L47  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L84  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L98-L99  

```solidity
File: PoolVariables.sol
  227         cache.amount0Desired = _token0.balanceOf(address(this));
  228         cache.amount1Desired = _token1.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L227-L228  

```solidity
File: TalosStrategyStaked.sol
  90         flywheel.accrue(ERC20(address(this)), msg.sender, _to);
  95         flywheel.accrue(ERC20(address(this)), _from, _to);
  150                     recipient: address(this),
  177         try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId) {
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L90  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L95  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L150  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L177  

```solidity
File: TalosStrategyVanilla.sol
  110                 recipient: address(this),
  130         uint256 balance0 = token0.balance

Of(address(this)) - protocolFees0;
  131         uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L110  
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L130-L131  

```solidity
File: BribesFactory.sol
  86             address(this)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L86  

```solidity
File: UniswapV3GaugeFactory.sol
  83                 address(this)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L83  

```solidity
File: BaseV2Gauge.sol
  70         multiRewardsDepot = new MultiRewardsDepot(address(this));
  115             if (isActive[_bribeFlywheels[i]]) _bribeFlywheels[i].accrue(ERC20(address(this)), user);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L70  
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L115  

```solidity
File: ArbitrumBranchBridgeAgent.sol
  158         uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L158  

```solidity
File: ArbitrumBranchPort.sol
  52         _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);
  120                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
  140                     address(this),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L52  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L120  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L140  

```solidity
File: ArbitrumCoreBranchRouter.sol
  104         IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, 0, 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L104  

```solidity
File: BranchBridgeAgent.sol
  1020         uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));
  1078         IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);
  1093         IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));
  1103         IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);
  1325         uint256 executionBudget = anycallConfig.executionBudget(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1020  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1078  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1093  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1103  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1325  

```solidity
File: BranchPort.sol
  127         uint256 currBalance = ERC20(_token).balanceOf(address(this));
  138         uint256 currBalance = ERC20(_token).balanceOf(address(this));
  180         IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);
  249             _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);
  254                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
  271                     address(this),
  276                 _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L138  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L180  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L249  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L254  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L271  
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L276  





### [G-08] Optimize names to save gas

By prioritizing the most frequently called functions based on their Method ID, gas consumption can be reduced during runtime. When a function is positioned earlier in the order (with a lower Method ID), the gas cost decreases by 22 for each preceding function. Prioritizing frequently called functions allows the caller to save on gas.

**All in-scope contracts** are within context for this issue.

Source:
- https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92




### [G-09] State variables can be cached instead of re-reading them from storage

State variables used multiple times can be cached to save 100 gas by replacing `Gwarmaccess` with a much cheaper stack read.

*There are 4 instances of this issue:*

```solidity
File: ERC20Gauges.sol
74:     function _getGaugeCycleEnd() internal view returns (uint32) {
75:         uint32 nowPlusOneCycle = block.timestamp.toUint32() + gaugeCycleLength; // @audit cache `gaugeCycleLength`
76:         unchecked {
77:             return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // @audit cache `gaugeCycleLength` // cannot divide by zero and always <= nowPlusOneCycle so no overflow
78:         }
79:     }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L74

```solidity
File: FlywheelGaugeRewards.sol
79:         uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength; // @audit
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L79

```solidity
File: FlywheelGaugeRewards.sol
114:         uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength; // @audit
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L114

### [G-10] Using `abi.encodePacked()` is more gas efficient than `abi.encode()`

*There are 19 instances of this issue:*

```solidity
File: GovernorBravoDelegateMaia.sol
  198             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
  346             keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
  347         bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L198
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346-L347

```solidity
File: PoolActions.sol
  51             abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L51

```solidity
File: ERC20MultiVotes.sol
  368                     "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L368

```solidity
File: ArbitrumCoreBranchRouter.sol
  53         bytes memory data = abi.encode(_underlyingAddress, address(0), name, symbol);
  98         bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L53
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L98

```solidity
File: CoreBranchRouter.sol
  48         bytes memory data = abi.encode(msg.sender, _globalAddress, _toChain, _rootExecutionGas);
  72         bytes memory data = abi.encode(_underlyingAddress, newToken, name, symbol);
  105         bytes memory data = abi.encode(_globalAddress, newToken);
  148         bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L48
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L72
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L105
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L148

```solidity
File: CoreRootRouter.sol
  107         bytes memory data = abi.encode(
  158         bytes memory data = abi.encode(
  238         bytes memory data = abi.encode(_branchBridgeAgentFactory);
  259         bytes memory data = abi.encode(_branchBridgeAgent);
  282         bytes memory data = abi.encode(_underlyingToken, _minimumReservesRatio);
  309         bytes memory data = abi.encode(_portStrategy, _underlyingToken, _dailyManagementLimit, _isUpdateDailyLimit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L107
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L158
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L238
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L259
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L282
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L309

```solidity
File: RootBridgeAgent.sol
  689             abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))
  733             abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L733