###  Gas Optimizations List
 --------------------------------------------------
- [x] **Gas 01** → ] State variables can be packed into fewer storage slots
- [x] **Gas 02** → Structs can be packed into fewer storage slots
- [x] **Gas 03** → Remove or replace unused modifier
- [x] **Gas 04** → Use elementary types or a user-defined type instead of a ``struct`` that has only one member
- [x] **Gas 05** → High gas gain can be achieved in for loops 
- [x] **Gas 06** → State variables should be cached in stack variables rather than re-reading them from storage
- [x] **Gas 07** → Use nested if and, avoid multiple check combinations
- [x] **Gas 08** → Reduce Gas Costs by Emitting Events Outside of For Loops
- [x] **Gas 09** → if () / require() statements that check input arguments should be at the top of the function
- [x] **Gas 10** → Use ``require`` instead of ``assert``
- [x] **Gas 11** → Use ``double require`` instead of using ``&&``
- [x] **Gas 12** → Ternary operation is cheaper than if-else statement

### [G-01] State variables can be packed into fewer storage slots

|Title | Total Gas Saved | Instance|
|:--:|:-------:|:--:|
|State variable packed | 4.2k | 2 |

 **(from 3 slot to 2 slot) 1 slot win: 1 * 2.1k  =  2.1k gas saved**

```solidity
src\rewards\rewards\FlywheelGaugeRewards.sol:

  22:     /*//////////////////////////////////////////////////////////////
  23:                         REWARDS CONTRACT STATE
  24:     //////////////////////////////////////////////////////////////*/
  25: 
  26:     /// @inheritdoc IFlywheelGaugeRewards
  27:     ERC20Gauges public immutable override gaugeToken;
  28: 
  29:     /// @notice the minter contract, is a rewardsStream to collect rewards from
  30:     IBaseV2Minter public immutable minter;
  31: 
  32:     /// @inheritdoc IFlywheelGaugeRewards
  33:     address public immutable override rewardToken;
  34: 
  35:     /// @inheritdoc IFlywheelGaugeRewards
  36:     uint32 public override gaugeCycle;
  37: 
  38:     /// @inheritdoc IFlywheelGaugeRewards
  39:     uint32 public immutable override gaugeCycleLength;
  40: 
  41:     /// @inheritdoc IFlywheelGaugeRewards
  42:     mapping(ERC20 => QueuedRewards) public override gaugeQueuedRewards;
  43: 
  44:     /// @notice the start of the next cycle being partially queued
  45:     uint32 internal nextCycle;
  46: 
  47:     // rewards that made it into a partial queue but didn't get completed
  48:     uint112 internal nextCycleQueuedRewards;
  49: 
  50:     // the offset during pagination of the queue
  51:     uint32 internal paginationOffset;

```

```diff
src\rewards\rewards\FlywheelGaugeRewards.sol:

  22:     /*//////////////////////////////////////////////////////////////
  23:                         REWARDS CONTRACT STATE
  24:     //////////////////////////////////////////////////////////////*/
  25: 
  26:     /// @inheritdoc IFlywheelGaugeRewards
  27:     ERC20Gauges public immutable override gaugeToken;
  28: 
  29:     /// @notice the minter contract, is a rewardsStream to collect rewards from
  30:     IBaseV2Minter public immutable minter;
  31: 
  32:     /// @inheritdoc IFlywheelGaugeRewards
  33:     address public immutable override rewardToken;
  34: 
- 35:     /// @inheritdoc IFlywheelGaugeRewards
- 36:     uint32 public override gaugeCycle;
  37: 
  38:     /// @inheritdoc IFlywheelGaugeRewards
  39:     uint32 public immutable override gaugeCycleLength;
  40: 
  41:     /// @inheritdoc IFlywheelGaugeRewards
  42:     mapping(ERC20 => QueuedRewards) public override gaugeQueuedRewards;
  43: 
+ 35:     /// @inheritdoc IFlywheelGaugeRewards
+ 36:     uint32 public override gaugeCycle;

  44:     /// @notice the start of the next cycle being partially queued
  45:     uint32 internal nextCycle;
  46: 
  47:     // rewards that made it into a partial queue but didn't get completed
  48:     uint112 internal nextCycleQueuedRewards;
  49: 
  50:     // the offset during pagination of the queue
  51:     uint32 internal paginationOffset;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L22-L51

** (from 5 slot to 4 slot)  1 slot win: 1 * 2.1k  =  2.1k gas saved**

```solidity
src\talos\base\TalosBaseStrategy.sol:

  32:     /*//////////////////////////////////////////////////////////////
  33:                         TALOS BASE STRATEGY STATE
  34:     //////////////////////////////////////////////////////////////*/
  35: 
  36:     /// @inheritdoc ITalosBaseStrategy
  37:     uint256 public override tokenId;
  38:     /// @inheritdoc ITalosBaseStrategy
  39:     uint128 public override liquidity;
  40: 
  41:     /// @inheritdoc ITalosBaseStrategy
  42:     uint256 public protocolFees0;
  43:     /// @inheritdoc ITalosBaseStrategy
  44:     uint256 public protocolFees1;
  45: 
  46:     /// @notice Current tick lower of Optimizer pool position
  47:     /// @inheritdoc ITalosBaseStrategy
  48:     int24 public override tickLower;
  49:     /// @notice Current tick higher of Optimizer pool position
  50:     /// @inheritdoc ITalosBaseStrategy
  51:     int24 public override tickUpper;
  52: 
  53:     /// @inheritdoc ITalosBaseStrategy
  54:     bool public initialized;

```

```diff
src\talos\base\TalosBaseStrategy.sol:

  32:     /*//////////////////////////////////////////////////////////////
  33:                         TALOS BASE STRATEGY STATE
  34:     //////////////////////////////////////////////////////////////*/
  35: 
  36:     /// @inheritdoc ITalosBaseStrategy
  37:     uint256 public override tokenId;
- 38:     /// @inheritdoc ITalosBaseStrategy
- 39:     uint128 public override liquidity;
  40: 
  41:     /// @inheritdoc ITalosBaseStrategy
  42:     uint256 public protocolFees0;
  43:     /// @inheritdoc ITalosBaseStrategy
  44:     uint256 public protocolFees1;

+ 38:     /// @inheritdoc ITalosBaseStrategy
+ 39:     uint128 public override liquidity;
  45: 
  46:     /// @notice Current tick lower of Optimizer pool position
  47:     /// @inheritdoc ITalosBaseStrategy
  48:     int24 public override tickLower;
  49:     /// @notice Current tick higher of Optimizer pool position
  50:     /// @inheritdoc ITalosBaseStrategy
  51:     int24 public override tickUpper;
  52: 
  53:     /// @inheritdoc ITalosBaseStrategy
  54:     bool public initialized;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L32-L54


### [G-02] Structs can be packed into fewer storage slots

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas.

Below are the details of packaging structs. 

|Title | Total Gas Saved | Instance|
|:--:|:-------:|:--:|
|Struct packed | 6k | 3 |


1. The ``DepositInput`` structure can be packaged as suggested below  (from 5 slots to 4 slots)
**   1 slot win: 1 * 2k  =  2k gas saved**

```solidity
src\ulysses-omnichain\interfaces\IBranchBridgeAgent.sol:

  26: struct DepositInput {
  27:     //Deposit Info
  28:     address hToken; //Input Local hTokens Address.
  29:     address token; //Input Native / underlying Token Address.
  30:     uint256 amount; //Amount of Local hTokens deposited for interaction.
  31:     uint256 deposit; //Amount of native tokens deposited for interaction.
  32:     uint24 toChain; //Destination chain for interaction.
  33: }

```

```diff
src\ulysses-omnichain\interfaces\IBranchBridgeAgent.sol:

  26: struct DepositInput {
  27:     //Deposit Info
+ 32:     uint24 toChain; //Destination chain for interaction.
  28:     address hToken; //Input Local hTokens Address.
  29:     address token; //Input Native / underlying Token Address.
  30:     uint256 amount; //Amount of Local hTokens deposited for interaction.
  31:     uint256 deposit; //Amount of native tokens deposited for interaction.
- 32:     uint24 toChain; //Destination chain for interaction.
  33: }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26-L33

2. The ``DepositMultipleParams`` structure can be packaged as suggested below  (from 6 slots to 5 slots)

**  1 slot win: 1 * 2.1k  =  2.1k gas saved**

```solidity
src\ulysses-omnichain\interfaces\IBranchBridgeAgent.sol:

  55: struct DepositMultipleParams {
  56:     //Deposit Info
  57:     uint8 numberOfAssets; //Number of assets to deposit.
  58:     uint32 depositNonce; //Deposit nonce.
  59:     address[] hTokens; //Input Local hTokens Address.
  60:     address[] tokens; //Input Native / underlying Token Address.
  61:     uint256[] amounts; //Amount of Local hTokens deposited for interaction.
  62:     uint256[] deposits; //Amount of native tokens deposited for interaction.
  63:     uint24 toChain; //Destination chain for interaction.
  64:     uint128 depositedGas; //BRanch chain gas token amount sent with request.
  65: }

```

```diff
src\ulysses-omnichain\interfaces\IBranchBridgeAgent.sol:

  55: struct DepositMultipleParams {
  56:     //Deposit Info
  57:     uint8 numberOfAssets; //Number of assets to deposit.
  58:     uint32 depositNonce; //Deposit nonce.
+ 63:     uint24 toChain; //Destination chain for interaction.
+ 64:     uint128 depositedGas; //BRanch chain gas token amount sent with request.
  59:     address[] hTokens; //Input Local hTokens Address.
  60:     address[] tokens; //Input Native / underlying Token Address.
  61:     uint256[] amounts; //Amount of Local hTokens deposited for interaction.
  62:     uint256[] deposits; //Amount of native tokens deposited for interaction.
- 63:     uint24 toChain; //Destination chain for interaction.
- 64:     uint128 depositedGas; //BRanch chain gas token amount sent with request.
  65: }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L55-L65

3. The ``DepositParams`` structure can be packaged as suggested below  (from 5 slots to 4 slots)

**  1 slot win: 1 * 2.1k  =  2.1k gas saved**


```solidity
src\ulysses-omnichain\interfaces\IRootBridgeAgent.sol:

  63: struct DepositParams {
  64:     //Deposit Info
  65:     uint32 depositNonce; //Deposit nonce.
  66:     address hToken; //Input Local hTokens Address.
  67:     address token; //Input Native / underlying Token Address.
  68:     uint256 amount; //Amount of Local hTokens deposited for interaction.
  69:     uint256 deposit; //Amount of native tokens deposited for interaction.
  70:     uint24 toChain; //Destination chain for interaction.
  71: }

```

```diff
src\ulysses-omnichain\interfaces\IRootBridgeAgent.sol:

  63: struct DepositParams {
  64:     //Deposit Info
  65:     uint32 depositNonce; //Deposit nonce.
+ 70:     uint24 toChain; //Destination chain for interaction.
  66:     address hToken; //Input Local hTokens Address.
  67:     address token; //Input Native / underlying Token Address.
  68:     uint256 amount; //Amount of Local hTokens deposited for interaction.
  69:     uint256 deposit; //Amount of native tokens deposited for interaction.
- 70:     uint24 toChain; //Destination chain for interaction.
  71: }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L63-L71


### [G-03] Remove or replace unused modifier

Removing unused modifiers will reduce the bytecode size, saving gas during contract deployment and execution.

```solidity
src\rewards\base\BaseFlywheelRewards.sol:
  43:     modifier onlyFlywheel() {
  44:         if (msg.sender != address(flywheel)) revert FlywheelError();
  45:         _;
  46:     }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L43-L46


### [G-04] Use elementary types or a user-defined type instead of a ``struct`` that has only one member

There are 3 instances of the subject.

```solidity
src\talos\interfaces\ITalosBaseStrategy.sol:

  29:     struct SwapCallbackData {
  30:         bool zeroForOne;
  31:     }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L29-L31


```solidity
src\talos\libraries\PoolActions.sol:
  26:     struct SwapCallbackData {
  27:         bool zeroForOne;
  28:     }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L25-L28


```solidity
src\ulysses-omnichain\interfaces\IRootBridgeAgent.sol:

  10: struct SwapCallbackData {
  11:     address tokenIn; //Token being sold
  12: }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L10-L12


### [G-05] High gas gain can be achieved in for loops

There are 3 instances of the subject.

```solidity
src\erc-20\ERC20Gauges.sol:

  536:         for (uint256 i = 0; i < size && (userFreeWeight + totalFreed) < weight;) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L536


```solidity
src\erc-20\ERC20MultiVotes.sol:

  328:         for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L328


```solidity
src\erc-20\ERC20Boost.sol:

  207:         for (uint256 i = 0; i < num && i < length;) { 

```

**Recommendation Code:**
The recommendation code is made for one example only.

```diff
src\erc-20\ERC20Boost.sol:

  206:         uint256 length = gaugeList.length;
- 207:         for (uint256 i = 0; i < num && i < length;) {
+              // this line checked num and length
+              uint256 lengthCheck = num >= length ? num : length;
+ 207:         for (uint256 i = 0; i < lengthCheck;) {
  208:             address gauge = gaugeList[offset + i];

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L206-L208



### [G-06] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.



```solidity
src\maia\PartnerUtilityManager.sol:
  73  
  74:         /// @dev Vault applies outstanding weight.
  75:         if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
  76:             IBaseVault(partnerVault).applyWeight();
  77:         }
  78      }

```

```diff
src\maia\PartnerUtilityManager.sol:
  73  
  74:         /// @dev Vault applies outstanding weight.
- 75:         if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
- 76:             IBaseVault(partnerVault).applyWeight();
+             address partnerVault_ = partnerVault
+ 75:         if (partnerVault_ != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
+ 76:             IBaseVault(partnerVault_).applyWeight();
  77:         }
  78      }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L73-L78


```solidity
src\maia\PartnerUtilityManager.sol:
  83  
  84:         /// @dev Vault applies outstanding boost.
  85:         if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
  86:             IBaseVault(partnerVault).applyBoost();
  87:         }

```

```diff
src\maia\PartnerUtilityManager.sol:
  83  
  84:         /// @dev Vault applies outstanding boost.
- 85:         if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
- 86:             IBaseVault(partnerVault).applyBoost();
+             address partnerVault_ = partnerVault
+ 85:         if (partnerVault_ != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
+ 86:             IBaseVault(partnerVault_).applyBoost();
  87:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L84-L87


```solidity
src\maia\PartnerUtilityManager.sol:
  93  
  94:         /// @dev Vault applies outstanding governance.
  95:         if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
  96:             IBaseVault(partnerVault).applyGovernance();
  97:         }
  98      }

```


```diff
src\maia\PartnerUtilityManager.sol:
  93  
- 94:         /// @dev Vault applies outstanding governance.
- 95:         if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {
+             address partnerVault_ = partnerVault
+ 96:             IBaseVault(partnerVault).applyGovernance();
+ 95:         if (partnerVault_ != address(0) && address(governance).balanceOf(address(this)) > 0) {
  96:             IBaseVault(partnerVault_).applyGovernance();
  97:         }
  98      }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L94-L98




```solidity
src\ulysses-omnichain\ArbitrumBranchPort.sol:

  58:     function withdrawFromPort(address _depositor, address _recipient, address _globalAddress, uint256 _deposit)
  59:         external
  60:         requiresBridgeAgent
  61:     {
  62:         if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {
  63:             revert UnknownToken();
  64:         }
  65: 
  66:         address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);
  67: 
  68:         if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();
  69: 
  70:         IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);
  71: 
  72:         underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));
  73:     }

```

```diff
src\ulysses-omnichain\ArbitrumBranchPort.sol:

  58:     function withdrawFromPort(address _depositor, address _recipient, address _globalAddress, uint256 _deposit)
  59:         external
  60:         requiresBridgeAgent
  61:     {
+             address rootPortAddress_ = rootPortAddress;
+             uint24  localChainId_ = localChainId;
- 62:         if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {
+ 62:         if (!IRootPort(rootPortAddress_).isGlobalToken(_globalAddress, localChainId_)) {
  63:             revert UnknownToken();
  64:         }
  65: 
- 66:         address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);
+ 66:         address underlyingAddress = IRootPort(rootPortAddress_).getUnderlyingTokenFromLocal(_globalAddress, localChainId_);
  67: 
  68:         if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();
  69: 
- 70:         IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);
+ 70:         IRootPort(rootPortAddress_).burnFromLocalBranch(_depositor, _globalAddress, _deposit);
  71: 
  72:         underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));
  73:     }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L58-L73


```solidity
src\ulysses-omnichain\RootBridgeAgent.sol:

  743:     function _manageGasOut(uint24 _toChain) internal returns (uint128) {
  744:         uint256 amountOut;
  745:         address gasToken;
  746:         uint256 _initialGas = initialGas;
  747: 
  748:         if (_toChain == localChainId) {
  749:             //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
  750:             if (_initialGas > 0) {
  751:                 address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], userFeeInfo.gasToBridgeOut);
  752:             }
  753: 
  754:             return uint128(userFeeInfo.gasToBridgeOut);
  755:         }
  756: 
  757:         if (_initialGas > 0) {
  758:             if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
  759:             (amountOut, gasToken) = _gasSwapOut(userFeeInfo.gasToBridgeOut, _toChain);
  760:         } else {
  761:             if (msg.value <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
  762:             wrappedNativeToken.deposit{value: msg.value}();
  763:             (amountOut, gasToken) = _gasSwapOut(msg.value, _toChain);
  764:         }
  765: 
  766:         IPort(localPortAddress).burn(address(this), gasToken, amountOut, _toChain);
  767:         return amountOut.toUint128();
  768:     }

```

```diff
src\ulysses-omnichain\RootBridgeAgent.sol:

  743:     function _manageGasOut(uint24 _toChain) internal returns (uint128) {
  744:         uint256 amountOut;
  745:         address gasToken;
  746:         uint256 _initialGas = initialGas;
+              uint128 _gasToBridgeOut = userFeeInfo.gasToBridgeOut;
  747: 
  748:         if (_toChain == localChainId) {
  749:             //Transfer gasToBridgeOut Local Branch Bridge Agent if remote initiated call.
  750:             if (_initialGas > 0) {
- 751:                 address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], userFeeInfo.gasToBridgeOut);
+ 751:                 address(wrappedNativeToken).safeTransfer(getBranchBridgeAgent[localChainId], _gasToBridgeOut);
  752:             }
  753: 
- 754:             return uint128(userFeeInfo.gasToBridgeOut);
+ 754:             return _gasToBridgeOut;
  755:         }
  756: 
  757:         if (_initialGas > 0) {
- 758:             if (userFeeInfo.gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
+ 758:             if (_gasToBridgeOut <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
- 759:             (amountOut, gasToken) = _gasSwapOut(userFeeInfo.gasToBridgeOut, _toChain);
+ 759:             (amountOut, gasToken) = _gasSwapOut(_gasToBridgeOut, _toChain);
  760:         } else {
  761:             if (msg.value <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGasForFees();
  762:             wrappedNativeToken.deposit{value: msg.value}();
  763:             (amountOut, gasToken) = _gasSwapOut(msg.value, _toChain);
  764:         }
  765: 
  766:         IPort(localPortAddress).burn(address(this), gasToken, amountOut, _toChain);
  767:         return amountOut.toUint128();
  768:     }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L743-L768


### [G-07] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.



```solidity
src\erc-20\ERC20Gauges.sol:

  464:         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L464


```solidity
src\erc-20\ERC20MultiVotes.sol:

  105:         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts
  
  250:         if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L105
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250


```solidity
src\gauges\factories\BaseV2GaugeFactory.sol:

  161:         if (msg.sender != bribesFactory.owner() && msg.sender != owner()) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L161



```solidity
src\governance\GovernorBravoDelegateMaia.sol:

  234:         if (msg.sender != proposal.proposer && msg.sender != admin) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L234


```solidity
src\maia\PartnerUtilityManager.sol:
  
  75:         if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {
  
  85:         if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {
  
  95:         if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L85
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L95


```solidity
src\rewards\rewards\FlywheelGaugeRewards.sol:
  
  210:         if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L210


```solidity
src\talos\base\TalosBaseStrategy.sol:

  151:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
  
  212:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
  
  273:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
  
  335:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L151
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L212
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L273
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L335


```solidity
src\talos\libraries\PoolVariables.sol:
  
  98:         if (tick < 0 && tick % tickSpacing != 0) compressed--;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L98


```solidity
src\ulysses-amm\factories\UlyssesFactory.sol:
  
  111:         if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L111


```solidity
src\ulysses-omnichain\RootBridgeAgent.sol:

  648:         if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L648



```solidity
src\ulysses-omnichain\BranchBridgeAgent.sol:
  
  474:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
  
  499:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
  
  521:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();
  
  543:         if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L474
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L499
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L521
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L543


```solidity
src\ulysses-omnichain\factories\ERC20hTokenRootFactory.sol:
  
  75:         if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L75


```solidity
src\uni-v3-staker\UniswapV3Staker.sol:
  
  402:        if (hermesGaugeBoost.isUserGauge(owner, address(gauge)) && _userAttachements[owner][key.pool] == tokenId) {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L402



### [G-08] Reduce Gas Costs by Emitting Events Outside of For Loops

Placing events inside loops results in the event being emitted at each iteration, which can significantly increase gas consumption. By moving events outside the loop and emitting them globally, unnecessary gas expenses can be minimized, leading to more efficient and cost-effective contract execution.

6 results - 5 files:

```solidity
src\erc-20\ERC20Boost.sol:

  207:         for (uint256 i = 0; i < num && i < length;) {
  208:             address gauge = gaugeList[offset + i];
  209: 
  210:             GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
  211: 
  212:             if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
  213:                 require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
  214:                 delete getUserGaugeBoost[msg.sender][gauge];
  215: 
  216:                 emit Detach(msg.sender, gauge);
  217:             } else {
  218:                 gaugeState.userGaugeBoost -= boost.toUint128();
  219: 
  220:                 emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
  221:             }
  222: 
  223:             unchecked {
  224:                 i++;
  225:             }
  226:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L207-L226


```solidity
src\erc-20\ERC20Boost.sol:

  236:         for (uint256 i = 0; i < size;) {
  237:             address gauge = gaugeList[i];
  238: 
  239:             require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
  240:             delete getUserGaugeBoost[msg.sender][gauge];
  241: 
  242:             emit Detach(msg.sender, gauge);
  243: 
  244:             unchecked {
  245:                 i++;
  246:             }
  247:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L236-L247


```solidity
src\erc-20\ERC20MultiVotes.sol:

  328:         for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {
  329:             address delegatee = delegateList[i];
  330:             uint256 delegateVotes = _delegatesVotesCount[user][delegatee];
  331:             // Minimum of votes delegated to delegatee and unused votes of delegatee
  332:             uint256 votesToFree = FixedPointMathLib.min(delegateVotes, userUnusedVotes(delegatee));
  333:             // Skip if votesToFree is zero
  334:             if (votesToFree != 0) {
  335:                 totalFreed += votesToFree;
  336: 
  337:                 if (delegateVotes == votesToFree) {
  338:                     // If all votes are freed, remove delegatee from list
  339:                     require(_delegates[user].remove(delegatee)); // Remove from set. Should never fail.
  340:                     _delegatesVotesCount[user][delegatee] = 0;
  341:                 } else {
  342:                     // If not all votes are freed, update the votes count
  343:                     _delegatesVotesCount[user][delegatee] -= votesToFree;
  344:                 }
  345: 
  346:                 _writeCheckpoint(delegatee, _subtract, votesToFree);
  347:                 emit Undelegation(user, delegatee, votesToFree);
  348:             }
  349:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L328-L349



```solidity
src\erc-4626\ERC4626MultiToken.sol:

  50:         for (uint256 i = 0; i < length;) {
  51:             require(ERC20(_assets[i]).decimals() == 18);
  52:             require(_weights[i] > 0);
  53: 
  54:             _totalWeights += _weights[i];
  55:             assetId[_assets[i]] = i + 1;
  56: 
  57:             emit AssetAdded(_assets[i], _weights[i]);
  58: 
  59:             unchecked {
  60:                 i++;
  61:             }
  62:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L50-L62



```solidity
src\rewards\rewards\FlywheelGaugeRewards.sol:

  176:         for (uint256 i = 0; i < size; i++) {
  177:             ERC20 gauge = ERC20(gauges[i]);
  178: 
  179:             QueuedRewards memory queuedRewards = gaugeQueuedRewards[gauge];
  180: 
  181:             // Cycle queue already started
  182:             require(queuedRewards.storedCycle < currentCycle);
  183:             assert(queuedRewards.storedCycle == 0 || queuedRewards.storedCycle >= lastCycle);
  184: 
  185:             uint112 completedRewards = queuedRewards.storedCycle == lastCycle ? queuedRewards.cycleRewards : 0;
  186:             uint256 nextRewards = gaugeToken.calculateGaugeAllocation(address(gauge), totalQueuedForCycle);
  187:             require(nextRewards <= type(uint112).max); // safe cast
  188: 
  189:             gaugeQueuedRewards[gauge] = QueuedRewards({
  190:                 priorCycleRewards: queuedRewards.priorCycleRewards + completedRewards,
  191:                 cycleRewards: uint112(nextRewards),
  192:                 storedCycle: currentCycle
  193:             });
  194: 
  195:             emit QueueRewards(address(gauge), currentCycle, nextRewards);
  196:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L176-L196


```solidity
src\ulysses-amm\UlyssesToken.sol:

   95:         for (uint256 i = 0; i < assets.length; i++) {
   96:             newTotalWeights += _weights[i];
   97: 
   98:             emit AssetRemoved(assets[i]);
   99:             emit AssetAdded(assets[i], _weights[i]);
  100:         }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L95-L100


### [G-09] if () / require() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

There are 2 instances of the subject.

```solidity
src\erc-4626\UlyssesERC4626.sol:

  24:     constructor(address _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, 18) {
  25:         asset = _asset;
  26: 
  27:         if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
  28:     }

```

```diff
src\erc-4626\UlyssesERC4626.sol:

  24:     constructor(address _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, 18) {
+ 27:         if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
  25:         asset = _asset;
  26: 
- 27:         if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
  28:     }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L24-L28

```solidity
src\ulysses-amm\UlyssesPool.sol:

  159:     function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {
  160:         if (weight == 0) revert InvalidWeight();
  161: 
  162:         UlyssesPool destination = factory.pools(poolId);
  163:         uint256 destinationId = destination.id();
  164: 
  165:         if (destinationIds[address(destination)] != 0 || destinationId == id) revert InvalidPool();
  166: 
  167:         if (destinationId == 0) revert NotUlyssesLP();

```


```diff
src\ulysses-amm\UlyssesPool.sol:

  159:     function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {
  160:         if (weight == 0) revert InvalidWeight();
  161: 
  162:         UlyssesPool destination = factory.pools(poolId);
  163:         uint256 destinationId = destination.id();
  164: 
+ 167:         if (destinationId == 0) revert NotUlyssesLP();

  165:         if (destinationIds[address(destination)] != 0 || destinationId == id) revert InvalidPool();
  166: 
- 167:         if (destinationId == 0) revert NotUlyssesLP();

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L167



### [G-10] Use ``require`` instead of ``assert``

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

There are 3 instances of the subject.

```solidity
src\rewards\rewards\FlywheelGaugeRewards.sol:

  183:        assert(queuedRewards.storedCycle == 0 || queuedRewards.storedCycle >= lastCycle);
  
  215:        assert(queuedRewards.storedCycle >= cycle);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L183
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L215


```solidity
src\uni-v3-staker\libraries\RewardMath.sol:
  
  65:         assert(currentTime >= startTime);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/RewardMath.sol#L65


### [G-11] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

<details>
<summary><i>There are 12 instances of this issue:</i></summary>

```solidity
src\governance\GovernorBravoDelegateMaia.sol:

   67:         require(
   68:             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   69:             "GovernorBravo::initialize: invalid voting period"
   70:         );


   71:         require(
   72:             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   73:             "GovernorBravo::initialize: invalid voting delay"
   74:         );


   75:         require(
   76:             proposalThreshold_ >= MIN_PROPOSAL_THRESHOLD && proposalThreshold_ <= MAX_PROPOSAL_THRESHOLD,
   77:             "GovernorBravo::initialize: invalid proposal threshold"
   78:         );


  119:         require(
  120:             targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,
  121:             "GovernorBravo::propose: proposal function information arity mismatch"
  122:         );


  237:                 require(
  238:                     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())
  239:                         && msg.sender == whitelistGuardian,
  240:                     "GovernorBravo::cancel: whitelisted proposer"
  241:                 );


  298:         require(
  299:             proposalCount >= proposalId && proposalId > initialProposalId, "GovernorBravo::state: invalid proposal id"
  300:         );



  399:         require(
  400:             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
  401:             "GovernorBravo::_setVotingDelay: invalid voting delay"
  402:         );



  415:         require(
  416:             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
  417:             "GovernorBravo::_setVotingPeriod: invalid voting period"
  418:         );


  432:         require(
  433:             newProposalThreshold >= MIN_PROPOSAL_THRESHOLD && newProposalThreshold <= MAX_PROPOSAL_THRESHOLD,
  434:             "GovernorBravo::_setProposalThreshold: invalid proposal threshold"
  435:         );



  507:         require(
  508:             msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"
  509:         );

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L67-L78


```solidity
src\talos\base\TalosBaseStrategy.sol:

  408:         require(balance0 >= amount0 && balance1 >= amount1);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L408


### [G-12] Ternary operation is cheaper than if-else statement

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.


```solidity
src\ulysses-amm\UlyssesPool.sol:
  138:         if (balance > assets) {
  139:             return balance - assets;
  140:         } else {
  141:             return 0;
  142:         }

```

```diff
src\ulysses-amm\UlyssesPool.sol:

- 138:         if (balance > assets) {
- 139:             return balance - assets;
- 140:         } else {
- 141:             return 0;
- 142:         }

+              return (balance > assets) ? (balance - assets) : 0;

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L138-L142