
|no    |issue|number of issue|
|------|-----|---------------|
|[G-01]|Structs can be packed into fewer storage slots|6|
|[G-02]|Using > 0 costs more gas than != 0 when used on a uint in a require() statement|2|
|[G-03]| >= costs less gas than >|59|
|[G-04]| Do not calculate constants|2|
|[G-05]|abi.encode() is less efficient than abi.encodePacked()|4|
|[G-06]|Amounts should be checked for 0 before calling a transfer|5|
|[G-07]|A modifier used only once and not being inherited should be inlined to save gas|7|
|[G-08]|Make 3 event parameters indexed when possible|70|
|[G-09]|Use constants instead of type(uintx).max|40|
|[G-10]|Use assembly to hash instead of solidity|6|
|[G-11]|internal functions not called by the contract should be removed to save deployment gas|1|
|[G-12]|Loop best practice to save gas|14|
|[G-13]|public functions not called by the contract should be declared external instead|26|
|[G-14]|Gas savings can be achieved by changing the model for assigning value to the structure (260 gas)|8|
|[G-15]|Using private rather than public for constants, saves gas|16|
|[G-16]|Use assembly for math (add, sub, mul, div)|6|
|[G-17]|Using delete statement can save gas|5|
|[G-18]|Access mappings directly rather than using accessor functions|9|
|[G-19]| Use hardcode address instead address(this)|40|
|[G-20]| Multiple accesses of a mapping/array should use a local variable cache|38|
|[G-21]|Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement|6|
|[G-22]|Avoid contract existence checks by using low level calls|109|
|[G-23]| Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|3|



## [G-01]    Structs can be packed into fewer storage slots

Structs in Solidity can be packed into fewer storage slots to save gas during contract execution.
When you define a struct in Solidity, the EVM allocates a fixed amount of storage space for each struct variable, regardless of the actual size of the struct. This means that if your struct contains multiple smaller variables, such as integers or booleans, it may be possible to pack these variables into fewer storage slots, and thus reduce the overall amount of storage space needed.



```solidity

105    struct Proposal {
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
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#105-L136


```solidity

26    struct DepositInput {
    //Deposit Info
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33


```solidity
44   struct DepositParams {
    //Deposit Info
    uint32 depositNonce; //Deposit nonce.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
    uint128 depositedGas; //BRanch chain gas token amount sent with request.
}

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L44-L53


```solidity

31    struct Settlement {
    uint24 toChain; //Destination chain for interaction.
    uint128 gasToBridgeOut; //Gas owed to user
    address owner; //Owner of the settlement
    address recipient; //Recipient of the settlement.
    SettlementStatus status; //Status of the settlement
    address[] hTokens; //Input Local hTokens Addresses.
    address[] tokens; //Input Native / underlying Token Addresses.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    bytes callData; //Call data for settlement
}
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L31-L42



```solidity
63    struct DepositParams {
    //Deposit Info
    uint32 depositNonce; //Deposit nonce.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71

```solidity
73  struct DepositMultipleParams {
    //Deposit Info
    uint8 numberOfAssets; //Number of assets to deposit.
    uint32 depositNonce; //Deposit nonce.
    address[] hTokens; //Input Local hTokens Address.
    address[] tokens; //Input Native / underlying Token Address.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L73-L82


## [G-02]   Using > 0 costs more gas than != 0 when used on a uint in a require() statement

In Solidity, using != 0 instead of > 0 in a require() statement can potentially save gas when checking a uint variable.
When checking a uint variable in a require() statement, you can use either the > 0 or != 0 comparison operator to check if the variable is non-zero. However, using != 0 is generally more gas-efficient, particularly when the variable is a uint.
This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

```solidity

52    require(_weights[i] > 0);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52


```solidity

47       require(_weight > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47

## [G-03]   >= costs less gas than >



In Solidity, using the >= operator instead of the > operator can potentially save gas during contract execution.
When comparing two integers in Solidity, the > operator requires the EVM to perform an additional operation to check if the two integers are equal. This is because the > operator only returns true if the left operand is strictly greater than the right operand, and false otherwise. In contrast, the >= operator returns true if the left operand is greater than or equal to the right operand.

Therefore, if you are comparing two integers and do not need to check for strict inequality, using the >= operator can potentially save gas by avoiding the additional equality check.
The compiler uses opcodes for solidity code that uses >, but only requires for >=, which saves 3 gas

```solidity

52     require(_weights[i] > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52

```solidity
252    if (share > shares) shares = share;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L252



```solidity

211 if (added && _userGauges[user].length() > maxGauges && !canContractExceedMaxGauges[user])
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L211

```solidity
236 if (newUserWeight > getVotes(user)) revert OverWeightError();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L236

```solidity
417 if (weight > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L417


```solidity
441 if (weight > 0) 
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L441




```solidity

70     if (ckpts[mid].fromBlock > blockNumber)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L70

```solidity
165    if (count > 1) revert DelegationError();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L165

```solidity
194    if (newDelegate && delegateCount(delegator) > maxDelegates && !canContractExceedMaxDelegates[delegator])
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L194

```solidity
250    if (pos > 0 && ckpts[pos - 1].fromBlock == block.number)

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250


```solidity

91     if (end > length) end = length;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L91


```solidity


114         require(
            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L114

```solidity
298         require(
            proposalCount >= proposalId && proposalId > initialProposalId, "GovernorBravo::state: invalid proposal id"
        );    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L298


```solidity
390         return (whitelistAccountExpirations[account] > block.timestamp);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L390

```solidity

93       if (_daoShare > max_dao_share) revert DaoShareTooHigh();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L93

```solidity
99       if (_tail_emission > max_tail_emission) revert TailEmissionTooHigh();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L99


```solidity

219     if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L219

```solidity
241     if (amount > maxMint(to)) revert ExceedsMaxDeposit();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L241


```solidity


75     if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75

```solidity
85     if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85

```solidity
95     if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0)

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95

```solidity

127    if (oldRewardBalance > 0) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L127

```solidity
162    if (strategyRewardsAccrued > 0)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L162


```solidity


120     if (currentCycle > nextCycle)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L120

```solidity
234     if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234

```solidity

158    if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L158


```solidity
219    if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L219

```solidity
398    if (amount0 > _protocolFees0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L398

```solidity
401    if (amount1 > _protocolFees1)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L401

```solidity
409    if (amount0 > 0) _token0.transfer(msg.sender, amount0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409

```solidity
410    if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410


```solidity

138     if (balance > assets)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L138


```solidity
153     if (claimed > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L153

```solidity
171     if (index > MAX_DESTINATIONS) revert TooManyDestinations();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L171


```solidity
185     if (newTotalWeights > MAX_TOTAL_WEIGHT) revert InvalidWeight();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L185

```solidity
191     if (oldBandwidth > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L191

```solidity
243     if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L243

```solidity
252     if (oldTotalWeights > newTotalWeights)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L252

```solidity
256     if (oldBandwidth > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L256

```solidity
272     if (oldBandwidth > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L272


```solidity
310     if (_fees.lambda1 > MAX_LAMBDA1) revert InvalidFee();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L310

```solidity
315     if (_fees.sigma1 > DIVISIONER) revert InvalidFee();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L315

```solidity

327     if (_protocolFee > MAX_PROTOCOL_FEE) revert InvalidFee();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L327

```solidity
911     if (updateAmount > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L911

```solidity
1048    if (updateAmount > 0) 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L1048


```solidity


47    require(_weight > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L47

```solidity
115   if (assetBalance > newAssetBalance)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L115


```solidity

474     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474


```solidity
499     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499

```solidity
521     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L521


```solidity
543     if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}(); 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L543


```solidity
623     if (_amounts[i] - _deposits[i] > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L623

```solidity
627     if (_deposits[i] > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L627


```solidity
979     if (_amount - _deposit > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L979

```solidity
983     if (_deposit > 0) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L983

```solidity
1035    if (minExecCost > gasRemaining) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1035


```solidity

1050    if (gasLeft - gasAfterTransfer > TRANSFER_OVERHEAD) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1050


```solidity
1069    if (minExecCost > getDeposit[_depositNonce].depositedGas)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1069

```solidity
1328    if (executionBudget > 0) try anycallConfig.withdraw(executionBudget)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1328


##  [G-04] Do not calculate constants

If you have a value that needs to be used multiple times in your Solidity code, and it doesn't need to be calculated at runtime, you can consider using a constant to store the value instead of calculating it every time it's needed. By doing this, you can save gas costs by avoiding unnecessary computations.

```solidity

24      uint256 internal constant week = 86400 * 7;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L24

```solidity

47      uint24 private constant protocolFee = 2 * 1e5; //20%
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47

## [G-05] abi.encode() is less efficient than abi.encodePacked()





In Solidity, using abi.encodePacked() can be more gas-efficient than using abi.encode() when encoding data for storage or transmission on the blockchain.
The abi.encode() function is used to pack multiple values into a single byte array, which can then be used for storage or transmission. However, abi.encode() also includes additional metadata in the output, such as the data types of the encoded values, which can increase the size of the output and consume more gas.



```solidity
346   keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
51   abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L51

```solidity
689   abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689

```solidity
733   abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L733









## [G-06] Amounts should be checked for 0 before calling a transfer

In Solidity, checking whether an amount is zero before calling a transfer function can potentially save gas during contract execution.

When transferring tokens or ether between accounts in a Solidity contract, it's important to ensure that the amount being transferred is non-zero to avoid unnecessary gas costs. If the amount being transferred is zero, the transfer function will still be called and consume gas, even though no actual transfer is taking place.

```solidity
510    return super.transferFrom(from, to, amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L510

```solidity
293     return super.transfer(to, amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L293

```solidity
305     return super.transferFrom(from, to, amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L305


```solidity
91     return super.transfer(_to, _amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L91

```solidity
96     return super.transferFrom(_from, _to, _amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L96


## [G-07]   A modifier used only once and not being inherited should be inlined to save gas

Inlining a modifier that is used only once and not being inherited can potentially save gas during contract execution.
In Solidity, a modifier is a piece of code that can be attached to a function and is executed before the function body. Modifiers are typically used to enforce certain conditions or restrictions on the function, such as requiring the caller to have a certain permission or meet certain criteria.


```solidity

190      modifier onlyWhitelisted(address from) {
        if (!whitelistedAddresses[from]) revert Unauthorized();
        _;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L190-L193


```solidity

423      modifier lock() {
        require(_unlocked == 1);
        _unlocked = 2;
        _;
        _unlocked = 1;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L423-L429

```solidity

105         modifier onlyGaugeFactory() {
        if (!gaugeManager.activeGaugeFactories(BaseV2GaugeFactory(msg.sender))) {
            revert Unauthorized();
        }
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L105-L110

```solidity

32      modifier onlybHermes() {
        if (msg.sender != bHermes) revert NotbHermes();
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32-L35

```solidity

39       modifier onlybHermes() {
        if (msg.sender != bHermes) revert NotbHermes();
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39-L42

```solidity

75     modifier requiresCoreRouter() {
        if (msg.sender != localCoreRouterAddress) revert UnrecognizedCoreRouter();
        _;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L75-L78

```solidity

74      modifier requiresCoreRouter() {
        if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress) {
            revert UnrecognizedCoreRouter();
        }
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L74-L79







## [G-08] Make 3 event parameters indexed when possible

In Solidity, marking event parameters as indexed can potentially save gas during contract execution, especially when the events are used to filter and search for specific data in the blockchain.
When an event parameter is marked as indexed, its value is hashed and stored separately from the event data in the transaction receipt. This allows the value of the indexed parameter to be efficiently searched and filtered using blockchain explorers or other tools, without having to read and process all the event data in the transaction.
However, marking too many parameters as indexed can also increase the gas cost of the transaction, since each indexed parameter requires additional storage and processing. Therefore, it's important to carefully consider which event parameters to mark as indexed and limit them to only those that are necessary for efficient filtering and searching.



```solidity
222   event UpdateUserBoost(address indexed user, uint256 updatedBoost);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Boost.sol#L222

225   event DecrementUserGaugeBoost(address indexed user, address indexed gauge, uint256 UpdatedBoost);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Boost.sol#L225

```solidity
246    event IncrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L246

```solidity
249    event DecrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L249

```solidity
258    event MaxGaugesUpdate(uint256 oldMaxGauges, uint256 newMaxGauges);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L258

```solidity
147    event MaxDelegatesUpdate(uint256 oldMaxDelegates, uint256 newMaxDelegates);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L147

```solidity
153    event Delegation(address indexed delegator, address indexed delegate, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L153

```solidity
159    event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L159

```solidity
93    event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626.sol#L93

```solidity
64    event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L64

```solidity
159    event Deposit(address indexed caller, address indexed owner, uint256[] assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L159


```solidity
178    event AssetAdded(address asset, uint256 weight);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L178

```solidity
184    event AssetRemoved(address asset);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L184

```solidity
86   event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L86

```solidity
111    event AddedGaugeFactory(address gaugeFactory);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L111


```solidity
114    event RemovedGaugeFactory(address gaugeFactory);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L114

```solidity
117    event ChangedbHermesGaugeOwner(address newOwner);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L117

```solidity
120    event ChangedAdmin(address newAdmin);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L120

```solidity
6     event ProposalCreated(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        string description
    );
24    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

27    event ProposalCanceled(uint256 id);

30    event ProposalQueued(uint256 id, uint256 eta);

33    event ProposalExecuted(uint256 id);

36    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);

39    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);

42    event NewImplementation(address oldImplementation, address newImplementation);

45    event ProposalThresholdSet(uint256 oldProposalThreshold, uint256 newProposalThreshold);

48    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);

51    event NewAdmin(address oldAdmin, address newAdmin);

54    event WhitelistAccountExpirationSet(address account, uint256 expiration);

57    event WhitelistGuardianSet(address oldGuardian, address newGuardian);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L6

```solidity
109    event Mint(address indexed sender, uint256 weekly, uint256 circulatingSupply, uint256 growth, uint256 dao_share);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IBaseV2Minter.sol#L109

```solidity
82    event ForfeitWeight(address indexed user, uint256 amount);

85    event ForfeitBoost(address indexed user, uint256 amount);

88    event ForfeitGovernance(address indexed user, uint256 amount);

91    event ClaimWeight(address indexed user, uint256 amount);

94    event ClaimBoost(address indexed user, uint256 amount);

97    event ClaimGovernance(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L82

```solidity
78    event AccrueRewards(address indexed user, uint256 rewardsDelta, uint256 rewardsIndex);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/interfaces/IERC4626PartnerManager.sol#L78

```solidity
85    event ClaimRewards(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/interfaces/IERC4626PartnerManager.sol#L85

```solidity
42    event NewRewardsCycle(uint32 indexed start, uint256 indexed end, uint256 reward);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L42

```solidity
121    event AccrueRewards(ERC20 indexed strategy, address indexed user, uint256 rewardsDelta, uint256 rewardsIndex);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelCore.sol#L121

```solidity
128    event ClaimRewards(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelCore.sol#L128

```solidity
93    event CycleStart(uint32 indexed cycleStart, uint256 rewardAmount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L93

```solidity
96    event QueueRewards(address indexed gauge, uint32 indexed cycleStart, uint256 rewardAmount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L96

```solidity
165    event CollectFees(uint256 feesFromPool0, uint256 feesFromPool1, uint256 usersFees0, uint256 usersFees1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L165

```solidity
170    event CompoundFees(uint256 amount0, uint256 amount1);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L170

```solidity
187    event RewardPaid(address indexed sender, uint256 fees0, uint256 fees1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L187

```solidity
235    event Rerange(uint256 indexed tokenId, int24 tickLower, int24 tickUpper, uint256 amount0, uint256 amount1);
```

240    event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L187

```solidity
23     event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L23

```solidity
28    event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L28

```solidity
237    event Swap(address indexed caller, uint256 indexed poolId, uint256 assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L237

```solidity
281    event LogCallin(bytes1 selector, bytes data, uint256 fromChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L281

```solidity
282    event LogCallout(bytes1 selector, bytes data, uint256, uint256 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L282


```solidity
293    event LogCalloutFail(bytes1 selector, bytes data, uint256 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L293

```solidity
198    event DebtCreated(address indexed _strategy, address indexed _token, uint256 _amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L198

```solidity
199    event DebtRepaid(address indexed _strategy, address indexed _token, uint256 _amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L199

```solidity
201    event StrategyTokenAdded(address indexed _token, uint256 _minimumReservesRatio);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L201

```solidity
202    event StrategyTokenToggled(address indexed _token);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L202

```solidity

204    event PortStrategyAdded(address indexed _portStrategy, address indexed _token, uint256 _dailyManagementLimit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L204

```solidity
205    event PortStrategyToggled(address indexed _portStrategy, address indexed _token);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L205


```solidity
206    event PortStrategyUpdated(address indexed _portStrategy, address indexed _token, uint256 _dailyManagementLimit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L206

```solidity
378    event LogCallin(bytes1 selector, bytes data, uint24 fromChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L378

```solidity
379    event LogCallout(bytes1 selector, bytes data, uint256, uint24 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L379


```solidity
380    event LogCalloutFail(bytes1 selector, bytes data, uint24 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L380

```solidity
315    event BridgeAgentAdded(address indexed bridgeAgent, address manager);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L315

```solidity
322    event VirtualAccountCreated(address indexed user, address account);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L322

324    event LocalTokenAdded(
        address indexed underlyingAddress, address localAddress, address globalAddress, uint24 chainId
    );
327    event GlobalTokenAdded(address indexed localAddress, address indexed globalAddress, uint24 chainId);    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L327

```solidity
264    event IncentiveCreated(IUniswapV3Pool indexed pool, uint256 startTime, uint256 reward);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L264

```solidity
269    event IncentiveEnded(bytes32 indexed incentiveId, uint256 refund);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L269
```solidity
281    event TokenStaked(uint256 indexed tokenId, bytes32 indexed incentiveId, uint128 liquidity);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L281

```solidity
291    event RewardClaimed(address indexed to, uint256 reward);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L291

```solidity
296    event BribeDepotUpdated(IUniswapV3Pool indexed uniswapV3Pool, address bribeDepot);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L296



## [G-09] Use constants instead of type(uintx).max

In Solidity, using constants instead of type(uintX).max can potentially save gas during contract execution, especially when working with large numbers.
When you need to represent the maximum value of an unsigned integer type in Solidity, you can use the expression type(uintX).max, where X is the number of bits in the integer. For example, type(uint256).max represents the maximum value of a 256-bit unsigned integer.


```solidity
67      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L67

```solidity
84      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L84

```solidity
149     return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L149

```solidity
154     return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L154


```solidity
99   return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L99


```solidity
104  return type(uint256).max;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L104

```solidity
143      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L143

```solidity
165      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L165

```solidity
200      shares = type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L200

```solidity
270      return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L270

```solidity
275      return type(uint256).max;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L275

```solidity
68       if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L68

```solidity
113      return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L113


```solidity
117      return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L117



```solidity
45      rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L45 

```solidity
200     address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L200

```solidity
201        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L201

```solidity
202       address(governance).safeApprove(newPartnerVault, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L202


```solidity
203     address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L203


```solidity
36     _rewardToken.safeApprove(address(_flywheel), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L36

```solidity
133     require(newRewards <= type(uint112).max); 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L133

```solidity
187     require(nextRewards <= type(uint112).max); 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L187


```solidity
130        address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L130

```solidity
131        address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L131

```solidity
249        if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L249


```solidity
285        amount0Max: type(uint128).max,
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L285
```solidity
286        amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L286

```solidity
367        amount0Max: type(uint128).max,
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L367

```solidity
368        amount1Max: type(uint128).max
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L368



```solidity
151      amount0Max: type(uint128).max,
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L151

```solidity
152      amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L152

```solidity
111          amount0Max: type(uint128).max,
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L111

```solidity
112          amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L112


```solidity
40      address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40 

```solidity
70     if (liquidity == type(uint96).max)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L70

```solidity

385    amount0Max: type(uint128).max,
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L385

```solidity
386    amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L386

```solidity
456    if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L456

```solidity
506    if (liquidity >= type(uint96).max)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L506

```solidity
509    liquidityNoOverflow: type(uint96).max,
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L509


## [G-10] Use assembly to hash instead of solidity

In Solidity, using assembly to hash data can potentially save gas during contract execution, especially when working with large amounts of data.

Hashing is a common operation in many blockchain applications, and Solidity provides built-in functions for hashing data, such as keccak256(). However, these functions can be relatively expensive in terms of gas cost, especially when working with large data sets.


```solidity
366  keccak256(
                abi.encodePacked(
                    "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
                )
            ),
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L366-L370


```solidity
198   !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L198

```solidity
346     keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
347    bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L347

```solidity
348    bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L348

```solidity
17    return keccak256(abi.encode(key));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol#L17

## [G-11] internal functions not called by the contract should be removed to save deployment gas


Internal functions in Solidity are only intended to be invoked within the contract or by other internal functions. If an internal function is not called anywhere within the contract, it serves no purpose and contributes unnecessary overhead during deployment. Removing such functions can lead to substantial gas savings.

```solidity


19     function transferRewards(address _asset, address _rewardsContract) internal returns (uint256 balance)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L19

## [G-12] Loop best practice to save gas





```
best practice
-----------------------------------------------------
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

```solidity
59  unchecked {
      i++;
    }
71  unchecked {
      i++;
    }

82  unchecked {
      i++;
    }

173 unchecked {
      i++;
    }

204 unchecked {
      i++;
    }

218 unchecked {
      i++;
    }

237 unchecked {
      i++;
    }

253 unchecked {
      i++;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L59

```solidity
197 unchecked {
      i++;
    }

264 unchecked {
      i++;
    }

288 unchecked {
      i++;
    }

930 unchecked {
      i++;
    }

1001 unchecked {
      i++;
    }

1068 unchecked {
      i++;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L197





## [G-13]   public functions not called by the contract should be declared external instead

when a function is not intended to be called from within the contract itself, declaring it as external can save gas because the function call can be handled by the external function interface, which is generally more efficient than the internal function call mechanism used for public functions.




```solidity

32     function checkpoints(address account, uint32 pos) public view virtual returns (Checkpoint memory)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L32


```solidity
37     function numCheckpoints(address account) public view virtual returns (uint32) {
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L37

```solidity
42     function freeVotes(address account) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L42


```solidity
58     function getPriorVotes(address account, uint256 blockNumber) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L58

```solidity
126    function delegatesVotesCount(address delegator, address delegatee) public view virtual returns (uint256) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L126

```solidity
141   function incrementDelegation(address delegatee, uint256 amount) public virtual
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L141

```solidity
146   function undelegate(address delegatee, uint256 amount) public virtual
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L146


```solidity

47   function mint(uint256 shares, address receiver) public virtual returns (uint256 assets)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L47

```solidity
80   function redeem(uint256 shares, address receiver, address owner) public virtual returns (uint256 assets)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L80

```solidity
148  function maxDeposit(address) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L148

```solidity
153  function maxMint(address) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L153


```solidity
158  function maxWithdraw(address owner) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L158

```solidity
163  function maxRedeem(address owner) public view virtual returns (uint256)

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L163


```solidity

47     function mint(uint256 shares, address receiver) public virtual returns (uint256 assets) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L47

```solidity
75     function convertToAssets(uint256 shares) public view virtual returns (uint256) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L75

```solidity
98     function maxDeposit(address) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L98

```solidity
103    function maxMint(address) public view virtual returns (uint256) 

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L103


```solidity

113         function mint(uint256 shares, address receiver)
        public
        virtual
        nonReentrant
        returns (uint256[] memory assetsAmounts)

156     function redeem(uint256 shares, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256[] memory assetsAmounts)
269     function maxDeposit(address) public view virtual returns (uint256)

```solidity
274     function maxMint(address) public view virtual returns (uint256) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L274

```solidity
279     function maxWithdraw(address owner) public view virtual returns (uint256[] memory) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L279

```solidity
284     function maxRedeem(address owner) public view virtual returns (uint256)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L284 




https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L113-L117

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L156-L160

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L169


```solidity

47     function mint(uint256 shares, address receiver) public virtual nonReentrant returns (uint256 assets)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L47

```solidity
59      function redeem(uint256 shares, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256 assets)

86         function totalAssets() public view virtual returns (uint256);

    function convertToShares(uint256 assets) public view virtual returns (uint256) {
        return assets;
    }

    function convertToAssets(uint256 shares) public view virtual returns (uint256) {
        return shares;
    }

    function previewDeposit(uint256 assets) public view virtual returns (uint256) {
        return assets;
    }

    function previewMint(uint256 shares) public view virtual returns (uint256) {
        return shares;
    }

    function previewRedeem(uint256 shares) public view virtual returns (uint256) {
        return shares;
    }

    /*//////////////////////////////////////////////////////////////
                     DEPOSIT/WITHDRAWAL LIMIT LOGIC
    //////////////////////////////////////////////////////////////*/

    function maxDeposit(address) public view virtual returns (uint256) {
        return type(uint256).max;
    }

    function maxMint(address) public view virtual returns (uint256) {
        return type(uint256).max;
    }

    function maxRedeem(address owner) public view virtual returns (uint256) {
        return balanceOf[owner];
    }
```


https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L59-L63

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L86-L122

```solidity

55    function forfeitMultiple(uint256 amount) public virtual
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L55

```solidity
62    function forfeitMultipleAmounts(uint256 weight, uint256 boost, uint256 _governance) public virtual
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L62

```solidity
96    function claimMultiple(uint256 amount) public virtual
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L96


```solidity
103   function claimMultipleAmounts(uint256 weight, uint256 boost, uint256 _governance) public virtual 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L103

```solidity

85      function updateUnderlyingBalance() public virtual

90      function claimOutstanding() public virtual 

113     function convertToShares(uint256 assets) public view virtual override returns (uint256)

121     function convertToAssets(uint256 shares) public view virtual override returns (uint256)

129     function previewDeposit(uint256 assets) public view virtual override returns (uint256)

136     function previewMint(uint256 shares) public view virtual override returns (uint256) 

143     function previewWithdraw(uint256 assets) public view virtual override returns (uint256) 

151     function previewRedeem(uint256 shares) public view virtual override returns (uint256) 
```


https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L85-L151



## [G-14] Gas savings can be achieved by changing the model for assigning value to the structure (260 gas)


Gas savings can be achieved by changing the model for assigning value to the structure (260 gas) ///////////// for saving of gas explain it
In Solidity, gas savings can be achieved by changing the model for assigning values to a struct. Specifically, using a memory reference for the struct instead of assigning values directly can reduce the gas cost of the operation.

When assigning values directly to a struct, Solidity needs to copy the values from the input parameters to the struct in memory, which can consume a significant amount of gas, especially when working with large structs.

```solidity
131    getUserGaugeBoost[user][msg.sender] =
            GaugeState({userGaugeBoost: userGaugeBoost, totalGaugeBoost: totalSupply.toUint128()});
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L131

```solidity
253   ckpts.push(Checkpoint({fromBlock: block.number.toUint32(), votes: newWeight.toUint224()}));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L253

```solidity
189   gaugeQueuedRewards[gauge] = QueuedRewards({
                priorCycleRewards: queuedRewards.priorCycleRewards + completedRewards,
                cycleRewards: uint112(nextRewards),
                storedCycle: currentCycle
            });

228    gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({
            priorCycleRewards: 0,
            cycleRewards: cycleRewardsNext,
            storedCycle: queuedRewards.storedCycle
        });        
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L189

```solidity
148   INonfungiblePositionManager.CollectParams({
                    tokenId: _tokenId,
                    recipient: address(this),
                    amount0Max: type(uint128).max,
                    amount1Max: type(uint128).max
                })
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L148

```solidity
108   INonfungiblePositionManager.CollectParams({
                tokenId: _tokenId,
                recipient: address(this),
                amount0Max: type(uint128).max,
                amount1Max: type(uint128).max
            })

142    INonfungiblePositionManager.IncreaseLiquidityParams({
                    tokenId: _tokenId,
                    amount0Desired: balance0,
                    amount1Desired: balance1,
                    amount0Min: 0,
                    amount1Min: 0,
                    deadline: block.timestamp
                })

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L108

```solidity
416   dParams = DepositMultipleParams({
            numberOfAssets: numOfAssets,
            depositNonce: nonce,
            hTokens: hTokens,
            tokens: tokens,
            amounts: amounts,
            deposits: deposits,
            toChain: toChain
        });
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L416




## [G-15]    Using private rather than public for constants, saves gas


using private rather than public for constants can potentially save gas costs, especially in contracts that are frequently read from or that have many public constants. However, it's worth noting that the gas savings may be negligible in smaller contracts or contracts with few public constants. Additionally, using private may limit the functionality of the contract, as the variable cannot be accessed by other contracts or external entities.


```solidity

360         bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360-L361

```solidity

9          string public constant name = "vMaia Governor Bravo";

    /// @notice The minimum setable proposal threshold
    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

    /// @notice The maximum setable proposal threshold
    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

    /// @notice The minimum setable voting period
    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

    /// @notice The max setable voting period
    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

    /// @notice The min setable voting delay
    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

    /// @notice The max setable voting delay
    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

    /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed
    uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

    /// @notice The maximum number of actions that can be included in a proposal
    uint256 public constant proposalMaxOperations = 10; // 10 actions

    /// @notice The divisor value used in percentage calculations
    uint256 public constant DIVISIONER = 1 ether;

42        bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9-L36

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-L46

```solidity

8           uint256 public constant FLAG_NONE = 0x0;
    uint256 public constant FLAG_MERGE_CONFIG_FLAGS = 0x1;
    uint256 public constant FLAG_PAY_FEE_ON_DEST = 0x1 << 1;
    uint256 public constant FLAG_ALLOW_FALLBACK = 0x1 << 2;
    uint256 public constant FLAG_ALLOW_FALLBACK_DST = 6;

    // exec flags used internally
    uint256 public constant FLAG_EXEC_START_VALUE = 0x1 << 16;
    uint256 public constant FLAG_EXEC_FALLBACK = 0x1 << 16;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L8-L16



## [G-16] Use assembly for math (add, sub, mul, div)


Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.



```solidity
379    function _add112(uint112 a, uint112 b) private pure returns (uint112) {
        return a + b;
       }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L379

```solidity
73   low = mid + 1;

82   return (a & b) + (a ^ b) / 2;

259  return a + b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L73

```solidity
526   uint256 c = a + b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L526

```solidity
134   uint256 _required = _growth + newWeeklyEmission;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L134




## [G-17]  Using delete statement can save gas

 using the delete keyword can save gas because it reduces the amount of storage space needed to store the data structure and avoids unnecessary gas consumption when modifying it in the future.


```solidity

249    getUserBoost[msg.sender] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L249 

```solidity

340      _delegatesVotesCount[user][delegatee] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L340



```solidity

98       rewardsAccrued[user] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L98



```solidity

211     secondsAgo[1] = 0;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L211

```solidity

78       assetId[asset] = 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L78



## [G-18] Access mappings directly rather than using accessor functions



In Solidity, gas savings can be achieved by accessing mappings directly, rather than using accessor functions.
Mappings are a type of data structure in Solidity that allow you to associate a key with a value. In many cases, you may want to access the values stored in a mapping from within your contract functions.

```solidity
87   return _userGauges[user].values();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L87

```solidity
108  return _userGauges[user].length();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L108


```solidity
144   return _userGauges[user].values();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L144

```solidity
165   return _userGauges[user].length();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L165


```solidity
132   return _delegates[delegator].values();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L132

```solidity
137   return _delegates[delegator].length();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L137

```solidity
164   return balanceOf[owner];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L164

```solidity
285   return balanceOf[owner];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L285

```solidity
121   return balanceOf[owner];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L121







## [G-19] Use hardcode address instead address(this)

Using a hardcoded address instead of address(this) in Solidity can potentially save gas in certain situations.

When you use address(this) in Solidity, the compiler generates code to retrieve the current contract address at runtime. This incurs an additional gas cost compared to using a hardcoded address, because the EVM needs to execute additional instructions to retrieve and push the current contract address onto the stack.

```solidity

37     address(asset).safeTransferFrom(msg.sender, address(this), assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L37

```solidity
51    address(asset).safeTransferFrom(msg.sender, address(this), assets);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L51

```solidity
 
69     assets[i].safeTransferFrom(msg.sender, address(this), assetsAmounts[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L69

```solidity

36     asset.safeTransferFrom(msg.sender, address(this), assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L36

```solidity
52     asset.safeTransferFrom(msg.sender, address(this), assets);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L52

```solidity
70      multiRewardsDepot = new MultiRewardsDepot(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L70

```solidity
115     if (isActive[_bribeFlywheels[i]]) _bribeFlywheels[i].accrue(ERC20(address(this)), user);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L115

```solidity

138     uint256 _balanceOf = underlying.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L138

```solidity
140     HERMES(underlying).mint(address(this), _required - _balanceOf);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140 


```solidity

116     return address(asset).balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L116

```solidity

129     gaugeWeight.mint(address(this), amount);
        gaugeBoost.mint(address(this), amount);
        governance.mint(address(this), amount);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/bHermes.sol#L129-L131

```solidity

72       address(gaugeWeight).safeTransferFrom(msg.sender, address(this), amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L72


```solidity
81       address(gaugeBoost).safeTransferFrom(msg.sender, address(this), amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L81

```solidity
90       address(governance).safeTransferFrom(msg.sender, address(this), amount);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L90 

```solidity

162      return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L162

```solidity
168      return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L168

```solidity
219      if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L219

```solidity
226      address(this), totalSupply * newRate - address(partnerGovernance).balanceOf(address(this))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L226

```solidity
244      ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L244



```solidity

75     if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75

```solidity
85     if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85


```solidity
95     if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95

```solidity
104    address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L104

```solidity
128    uint256 weightAvailable = address(gaugeWeight).balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L128

```solidity
139    uint256 boostAvailable = address(gaugeBoost).balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L139

```solidity
148    uint256 governanceAvailable = address(governance).balanceOf(address(this));

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L148


```solidity

125     address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);
        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L125-L126


```solidity
146     recipient: address(this),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L146

```solidity
196     address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);
        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L196-L197

```solidity
366     recipient: address(this),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L366

```solidity
406     uint256 balance0 = _token0.balanceOf(address(this));
        uint256 balance1 = _token1.balanceOf(address(this));
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L406-L407


```solidity

90      flywheel.accrue(ERC20(address(this)), msg.sender, _to);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L90


```solidity
95      flywheel.accrue(ERC20(address(this)), _from, _to);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L95


```solidity
150     recipient: address(this),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#150

```solidity
177     try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId)

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L177



```solidity

103     return asset.balanceOf(address(this)) - getProtocolFees();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L103


```solidity
108     return balanceOf[owner].min(asset.balanceOf(address(this)));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L108

```solidity
127     uint256 balance = asset.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L127

```solidity
218     asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L218

```solidity
303     asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L303

```solidity
1109    asset.safeTransferFrom(msg.sender, address(this), assets);

```


https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1109

```solidity

84        asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L84

```solidity
112       uint256 assetBalance = assets[i].balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112

```solidity
118       assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L118

```solidity

1020      uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1020


```solidity
1078     IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1078

```solidity
1093      IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1093

```solidity
1103      IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1103

```solidity
1325     uint256 executionBudget = anycallConfig.executionBudget(address(this));

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1325


```solidity

127     uint256 currBalance = ERC20(_token).balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127

```solidity
138     uint256 currBalance = ERC20(_token).balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L138

```solidity
180     IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L180


```solidity
249     _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L249


```solidity
254     _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L254

```solidity
271      address(this),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L271

```solidity
276      _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L276


## [G-20]   Multiple accesses of a mapping/array should use a local variable cache

Using a local variable cache when accessing mappings and arrays multiple times in a function can potentially save gas during contract execution.

In Solidity, mappings and arrays can be used to store and access data in a contract. When you access a mapping or array multiple times in a function, the EVM needs to perform multiple lookups in storage, which can be expensive in terms of gas cost.

To reduce the gas cost of accessing mappings and arrays multiple times, you can use a local variable cache to store the values of the mapping or array in memory. By doing so, you can avoid multiple lookups in storage and instead read the values from memory, which is faster and cheaper.


```solidity

28      mapping(address => mapping(address => GaugeState)) public override getUserGaugeBoost;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L28


```solidity  
31      mapping(address => uint256) public override getUserBoost;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L31

```solidity
33      mapping(address => EnumerableSet.AddressSet) internal _userGauges;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L33


```solidity

45    mapping(address => mapping(address => uint112)) public override getUserGaugeWeight;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L45

```solidity
49    mapping(address => uint112) public override getUserWeight;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L49

```solidity
53    mapping(address => Weight) internal _getGaugeWeight;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L53


```solidity
58    mapping(address => EnumerableSet.AddressSet) internal _userGauges;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L58

```solidity
395   mapping(address => bool) public override canContractExceedMaxGauges;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L395

```solidity

29       mapping(address => Checkpoint[]) private _checkpoints;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L29


```solidity
93       mapping(address => bool) public override canContractExceedMaxDelegates;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L93

```solidity
117      mapping(address => mapping(address => uint256)) private _delegatesVotesCount;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L117


```solidity
120      mapping(address => uint256) public userDelegatedVotes;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#120


```solidity
123      mapping(address => EnumerableSet.AddressSet) private _delegates;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#123


```solidity

35        mapping(FlywheelCore => bool) public override isActive;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L35

```solidity
38        mapping(FlywheelCore => bool) public override added;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L38



```solidity

100     mapping(uint256 => Proposal) public proposals;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/governance/GovernorBravoInterfaces.sol#L100


```solidity

32      mapping(address => uint256) public userClaimedWeight;
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L32

```solidity
34      mapping(address => uint256) public userClaimedBoost;
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L34

```solidity
36      mapping(address => uint256) public userClaimedGovernance;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/hermes/UtilityManager.sol#L36

```solidity 

17        mapping(address => address) private _assets;
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/MultiRewardsDepot.sol#L17

```solidity
20        mapping(address => bool) private _isRewardsContract;
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/MultiRewardsDepot.sol#L20

```solidity
23        mapping(address => bool) private _isAsset;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/depots/MultiRewardsDepot.sol#L23 


```solidity

39       mapping(address => address) public userToRewardsDepot;

    /// @inheritdoc IBoostAggregator
    mapping(uint256 => address) public tokenIdToUser;

    /// @inheritdoc IBoostAggregator
    mapping(uint256 => uint256) public tokenIdRewards;

    /// @inheritdoc IBoostAggregator
    mapping(address => bool) public whitelistedAddresses;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L39-L48

```solidity

55         mapping(uint256 => UlyssesPool) public pools;

58       mapping(uint256 => UlyssesToken) public tokens;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L55-L58

```solidity

34       mapping(uint256 => uint256) public destinations;

       /// @notice destinationIds[address] => destinationId
      mapping(address => uint256) public destinationIds;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L34-L37

```solidity

122         mapping(uint32 => Deposit) public getDeposit;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L122


```solidity
129        mapping(uint32 => bool) public executionHistory;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L129

```solidity

34         mapping(address => IUniswapV3Pool) public gaugePool;

    /// @inheritdoc IUniswapV3Staker
    mapping(IUniswapV3Pool => UniswapV3Gauge) public gauges;

    /// @inheritdoc IUniswapV3Staker
    mapping(IUniswapV3Pool => address) public bribeDepots;

    /// @inheritdoc IUniswapV3Staker
    mapping(IUniswapV3Pool => uint24) public poolsMinimumWidth;

    /// @inheritdoc IUniswapV3Staker
    mapping(bytes32 => Incentive) public override incentives;

    /// @inheritdoc IUniswapV3Staker
    mapping(uint256 => Deposit) public override deposits;

    /// @notice stakes[user][pool] => tokenId of attached position of user per pool
    mapping(address => mapping(IUniswapV3Pool => uint256)) private _userAttachements;

    /// @dev stakes[tokenId][incentiveHash] => Stake
    mapping(uint256 => mapping(bytes32 => Stake)) private _stakes;

    /// @dev stakedIncentives[tokenId] => incentiveIds
    mapping(uint256 => IncentiveKey) private stakedIncentiveKey;

```
https://github.com/code-423n4/2023-05-maia/tree/main/src/uni-v3-staker/UniswapV3Staker.sol#L34-L58


## [G-21] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }.


In Solidity, using the unchecked {} block for subtractions can potentially save gas during contract execution, especially when working with large numbers.
When performing arithmetic operations in Solidity, the EVM automatically checks for overflow and underflow conditions, which can consume a significant amount of gas. However, in some cases, it may be possible to guarantee that an operation will not result in overflow or underflow because of a previous require() or if-statement.


```solidity
532    require(b <= a, "subtraction underflow");
        return a - b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L532-L533

```solidity
140   HERMES(underlying).mint(address(this), _required - _balanceOf);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140

```solidity
166   if (amount0 < amount0Desired) {
            uint256 refund0 = amount0Desired - amount0;
            address(_token0).safeTransfer(msg.sender, refund0);
        }

171    if (amount1 < amount1Desired) {
            uint256 refund1 = amount1Desired - amount1;
            address(_token1).safeTransfer(msg.sender, refund1);
        }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166-L174


```solidity
226    if (amount0 < amount0Desired) {
            uint256 refund0 = amount0Desired - amount0;
            address(_token0).safeTransfer(msg.sender, refund0);
        }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L226



```solidity
231   if (amount1 < amount1Desired) {
            uint256 refund1 = amount1Desired - amount1;
            address(_token1).safeTransfer(msg.sender, refund1);
        }  
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L231



## [G-22] Avoid contract existence checks by using low level calls


In Solidity, gas savings can be achieved by avoiding contract existence checks and using low-level calls instead.
In some cases, you may need to call functions in other contracts from within your Solidity contract. One way to do this is by using the address.call() or address.delegatecall() functions. These functions allow you to call functions in another contract using a low-level call, which can be more gas-efficient than other methods of inter-contract communication.

However, when using address.call() or address.delegatecall(), Solidity will automatically perform an existence check on the target contract before making the call. This check ensures that the target contract exists and is not a contract that is in the process of being destroyed.


```solidity
41    return IFlywheelInstantRewards(flywheelRewards).getAccruedRewards();
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreInstant.sol#L41


```solidity
40   return IFlywheelAcummulatedRewards(flywheelRewards).getAccruedRewards(strategy);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreStrategy.sol#L40


```solidity
82    _boostAggregator.setOwnRewardsDepot(address(FlywheelInstantRewards(_flywheel.flywheelRewards()).rewardsDepot()));
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L82

```solidity
104   IPort(localPortAddress).addBridgeAgent(newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L104

```solidity
99    IPort(localPortAddress).addBridgeAgent(newCoreBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L99

```solidity
139   IPort(localPortAddress).addBridgeAgent(newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L139


```solidity
62    localAnyCallExecutorAddress = IAnycallProxy(localAnyCallAddress).executor();
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L62

```solidity
88    IRootPort(rootPortAddress).addBridgeAgent(msg.sender, newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L88


```solidity
103   IArbPort(localPortAddress).depositToPort(
            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
        );
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L103


```solidity
115   IArbPort(localPortAddress).withdrawFromPort(msg.sender, msg.sender, localAddress, amount);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L115

```solidity
143   IRootBridgeAgent(rootBridgeAgentAddress).anyExecute(_callData);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L143


```solidity
104   BranchBridgeAgent(payable(msg.sender)).clearToken(
            sParams.recipient, sParams.hToken, sParams.token, sParams.amount, sParams.deposit
        );
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L104


```solidity
110    (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L110

```solidity
147   (success, result) = IRouter(_router).anyExecuteSettlementMultiple(    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L147

```solidity
127   uint256 currBalance = ERC20(_token).balanceOf(address(this));

138   uint256 currBalance = ERC20(_token).balanceOf(address(this));

180   IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

212    _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

222   ERC20hTokenBranch(_localAddress).mint(_recipient, _amount);

232    ERC20hTokenBranch(_localAddresses[i]).mint(_recipient, _amounts[i]);

250   ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);

254  _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

272  _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127


```solidity
54   IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(
            msg.sender, packedData, 0, _remoteExecutionGas
        );

65    string memory name = ERC20(_underlyingAddress).name();

66    string memory symbol = ERC20(_underlyingAddress).symbol();   

69    ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(name, symbol);

78     IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(msg.sender, packedData, 0, 0);

102    ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(_name, _symbol);

111    IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _rootExecutionGas, 0);

133    if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {

138     address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(
            _newBranchRouter, _rootBridgeAgent, _rootBridgeAgentFactory
        );

143     if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {
            revert UnrecognizedBridgeAgent();
        }

154     IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _remoteExecutionGas, 0);

164   if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {

165   IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);

167   IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);

178     if (!IPort(localPortAddress).isBridgeAgent(_branchBridgeAgent)) revert UnrecognizedBridgeAgent();

179   IPort(localPortAddress).toggleBridgeAgent(_branchBridgeAgent);

190   if (!IPort(localPortAddress).isStrategyToken(_underlyingToken)) {

191   IPort(localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio);

193   IPort(localPortAddress).toggleStrategyToken(_underlyingToken);

212   if (!IPort(localPortAddress).isPortStrategy(_portStrategy, _underlyingToken)) {

214   IPort(localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

217    IPort(localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

220   IPort(localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L54


```solidity
65    bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

90    if (msg.sender != IPort(rootPortAddress).getBridgeAgentManager(_rootBridgeAgent)) {
            revert UnauthorizedCallerNotManager();
        }

95    if (!IPort(rootPortAddress).isChainId(_toChain)) revert InvalidChainId();

98     if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_toChain) != address(0)) revert InvalidChainId();

101   if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_toChain)) revert UnauthorizedChainId();

104   address rootBridgeAgentFactory = IBridgeAgent(_rootBridgeAgent).factoryAddress();

115   IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

128     IPort(rootPortAddress).syncBranchBridgeAgentWithRoot(_newBranchBridgeAgent, _rootBridgeAgent, _fromChain);
    }

148   if (!IPort(rootPortAddress).isGlobalAddress(_globalAddress)) {

153   if (IPort(rootPortAddress).isGlobalToken(_globalAddress, _toChain)) {

159    _globalAddress, ERC20(_globalAddress).name(), ERC20(_globalAddress).symbol(), _remoteExecutionGas

166   IBridgeAgent(bridgeAgentAddress).callOut(_gasReceiver, packedData, _toChain);

187    IPort(rootPortAddress).isGlobalAddress(_underlyingAddress)

188      || IPort(rootPortAddress).isLocalToken(_underlyingAddress, _fromChain)

189      || IPort(rootPortAddress).isUnderlyingToken(_underlyingAddress, _fromChain)

193    address newToken = address(IFactory(hTokenFactoryAddress).createToken(_name, _symbol));

196    IPort(rootPortAddress).setAddresses(
            newToken, (_fromChain == rootChainId) ? newToken : _localAddress, _underlyingAddress, _fromChain
        );

210    if (IPort(rootPortAddress).isLocalToken(_localAddress, _toChain)) revert TokenAlreadyAdded();

213    IPort(rootPortAddress).setLocalAddress(_globalAddress, _localAddress, _toChain);

233    if (!IPort(rootPortAddress).isBridgeAgentFactory(_rootBridgeAgentFactory)) {

244    IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

265      IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

288     IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);


315     IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L65

```solidity
78     bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

96    (blockNumber, returnData) = IMulticall(multicallAddress).aggregate(calls);

120   ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

123   IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(
            owner, recipient, "", outputToken, amountOut, depositOut, toChain
        );

155   IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(
            owner, recipient, "", outputTokens, amountsOut, depositsOut, toChain
        );   
        
281   IVirtualAccount(userAccount).call(calls);

289   IVirtualAccount(userAccount).call(calls);

292    IVirtualAccount(userAccount).withdrawERC20(outputParams.outputToken, outputParams.amountOut);

295   IVirtualAccount(userAccount).userAddress(),

309   IVirtualAccount(userAccount).call(calls);

312     IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

320   IVirtualAccount(userAccount).userAddress(),

388   IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

396   IVirtualAccount(userAccount).userAddress(),

444   IVirtualAccount(userAccount).withdrawERC20(outputParams.outputToken, outputParams.amountOut);

464    IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L78

```solidity
84   (success, result) = IRouter(_router).anyExecuteResponse(
            bytes1(_data[PARAMS_TKN_START]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId
        );
105    IRouter(_router).anyExecute(bytes1(_data[5]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId);

137   (success, result) = IRouter(_router).anyExecuteDepositSingle(
                _data[112], _data[113:_data.length - PARAMS_GAS_IN], dParams, _fromChainId
            );

177   (success, result) = IRouter(_router).anyExecuteDepositMultiple(
                bytes1(_data[PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE]),
                _data[
                    PARAMS_START + PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE:
                        length - PARAMS_GAS_IN
                ],
                dParams,
                _fromChainId
            );
208        IRouter(_router).anyExecuteSigned(_data[25], _data[26:_data.length - PARAMS_GAS_IN], _account, _fromChainId);

241   (success, result) = IRouter(_router).anyExecuteSignedDepositSingle(
                _data[132], _data[133:_data.length - PARAMS_GAS_IN], dParams, _account, _fromChainId
            );

283   (success, result) = IRouter(_router).anyExecuteSignedDepositMultiple(
                    _data[PARAMS_END_SIGNED_OFFSET
                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE],
                    _data[
                        PARAMS_START + PARAMS_END_SIGNED_OFFSET
                            + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE:
                            _data.length - PARAMS_GAS_IN
                    ],
                    dParams,
                    _account,
                    _fromChainId
                );

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L84


```solidity
153   IBridgeAgent(_coreRootBridgeAgent).syncBranchBridgeAgent(_coreLocalBranchBridgeAgent, localChainId);

295   ERC20hTokenRoot(_hToken).mint(_to, _amount, _fromChain);

301   ERC20hTokenRoot(_hToken).burn(_from, _amount, _fromChain);

332   ERC20hTokenRoot(_hToken).burn(_from, _amount, localChainId);

383   if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_branchChainId) != address(0)) {

386   if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_branchChainId)) {

389    IBridgeAgent(_rootBridgeAgent).syncBranchBridgeAgent(_newBranchBridgeAgent, _branchChainId);

435    IERC20hTokenRootFactory(ICoreRootRouter(coreRootRouterAddress).hTokenFactoryAddress()).createToken(
                _wrappedGasTokenName, _wrappedGasTokenSymbol
            )
440   ERC20hTokenRoot(newGlobalToken).mint(_pledger, _pledgedInitialAmount, _chainId);

442   IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(
            _coreBranchBridgeAgentAddress, _chainId
        );

465    newGasPoolAddress = INonfungiblePositionManager(_nonFungiblePositionManagerAddress)
                .createAndInitializePoolIfNecessary(newGlobalToken, wrappedNativeTokenAddress, _fee, _sqrtPriceX96);
469    newGasPoolAddress = INonfungiblePositionManager(_nonFungiblePositionManagerAddress)
                .createAndInitializePoolIfNecessary(wrappedNativeTokenAddress, newGlobalToken, _fee, _sqrtPriceX96);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L153


```solidity
37   ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L37


```solidity
70   if ((!IRootPort(localPortAddress).isRouterApproved(this, msg.sender)) && (msg.sender != userAddress)) {
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L70



## [G-23] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant


In Solidity, using immutable variables instead of constant variables can result in gas savings, particularly for expressions that involve constant values such as calls to keccak256().

constant variables are evaluated at compile time and their values are hardcoded into the bytecode. This can be useful for small constant values, but can result in larger bytecode and higher gas costs for more complex expressions or larger constant values.



```solidity
360   bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```        
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360

```solidity
42    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)")
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42


```solidity
46    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L46
