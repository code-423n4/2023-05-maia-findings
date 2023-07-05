# **GAS OPTIMIZATIONS**

## [G-01] Unused Imports
The following files were imported but were not used. These files would costs gas during deployment and generally this is a bad coding practice. I would recommend that the unused imports be removed

###  68 Instances

In the following files `Ownable` was imported with the statement `import {Ownable} from "solady/auth/Ownable.sol";` but was not used. (15 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol#L6
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelInstantRewards.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L5 
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L4


In the following files `ERC20` was imported with the statement `import {ERC20} from "solmate/tokens/ERC20.sol";` but was not used. (12 Instances) 
- https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L7
- https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L7
- https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L8
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L7
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L7
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelRewards.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L8
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L9
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L8
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L6
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L8


In the following files `SafeCastLib` was imported with the statement `import {SafeCastLib} from "solady/utils/SafeCastLib.sol";` but was not used. (4 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L4
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol#L5
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L4


In the following files `ERC20hTokenBranch` was imported with the statement `import {ERC20hTokenBranch as ERC20hToken} from "./token/ERC20hTokenBranch.sol";` but was not used. (4 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L16
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L15
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L12 
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L19


In the following files `SafeTransferLib` was imported with the statement `import {SafeTransferLib} from "solady/utils/SafeTransferLib.sol";` but was not used. (3 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L6
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol#L6
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol#L6


In the following files `IAnycallProxy` was imported with the statement `import {IAnycallProxy} from "../interfaces/IAnycallProxy.sol";` but was not used. (2 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L11
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L12


In the following files `IBaseV2Guage` was imported with the statement `import {IBaseV2Gauge} from "@gauges/interfaces/IBaseV2Gauge.sol"` but was not used. (2 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L12
- https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L14


In the following files `IBranchRouter` was imported with the statement `import {IBranchRouter as IRouter} from "./interfaces/IBranchRouter.sol";` but was not used. (2 Instances)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L17
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L16


In the following file `Errors` was imported with the statement `import {Errors} from "./interfaces/Errors.sol";` but was not used. (1 Instance) 
- https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L14


In the following file `BaseV2Guage` was imported with the statement `import {BaseV2Gauge} from "@gauges/BaseV2Gauge.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L8


In the following file `bHermesVotes` was imported with the statement `import {bHermesVotes as vMaiaVotes} from "@hermes/tokens/bHermesVotes.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol#L11


In the following file `IRewardsDepot` was imported with the statement `import {RewardsDepot, IRewardsDepot} from "./RewardsDepot.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L4


In the following file `FlywheelCore` was imported with the statement `import {FlywheelCore} from "../base/FlywheelCore.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L9


In the following file `TalosStrategyStaked` was imported with the statement `import {DeployStaked, TalosStrategyStaked} from "../TalosStrategyStaked.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol#L14


In the following file `TalosStrategyVanilla` was imported with the statement `import {DeployVanilla, TalosStrategyVanilla} from "../TalosStrategyVanilla.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L9


In the following file `TalosManager` was imported with the statement `import {TalosManager} from "../TalosManager.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L10


In the following file `IUniswapV3Staker` was imported with the statement `import {IUniswapV3Staker} from "@v3-staker/interfaces/IUniswapV3Staker.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L12


In the following file `TalosOptimizer` was imported with the statement `import {OptimizerFactory, TalosOptimizer} from "../factories/OptimizerFactory.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategyFactory.sol#L8


In the following file `PositionKey` was imported with the statement `import {PositionKey} from "@uniswap/v3-periphery/contracts/libraries/PositionKey.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L12


In the following file `MultiRewardsDepot` was imported with the statement `import {MultiRewardsDepot} from "@rewards/depots/MultiRewardsDepot.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L10


In the following file `CoreBranchRouter` was imported with the statement `import {CoreBranchRouter} from "../CoreBranchRouter.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L8


In the following file `IRootBridgeAgent` was imported with the statement `import {IRootBridgeAgent} from "../interfaces/IRootBridgeAgent.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L11


In the following file `AnycallFlags` was imported with the statement `import {AnycallFlags} from "./lib/AnycallFlags.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L11


In the following file `IAnycallConfig` was imported with the statement `import {IAnycallConfig} from "./interfaces/IAnycallConfig.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L13


In the following file `IAnycallExecutor` was imported with the statement `import {IAnycallExecutor} from "./interfaces/IAnycallExecutor.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L14


In the following file `ArbitrumBranchBridgeAgent` was imported with the statement `import {ArbitrumBranchBridgeAgent} from "./ArbitrumBranchBridgeAgent.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L13


In the following file `IERC20hTokenBranchFactory` was imported with the statement `import {IERC20hTokenBranchFactory as IFactory} from "./interfaces/IERC20hTokenBranchFactory.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L8 (IERC20hTokenBranchFactory)


In the following file `ICoreBranchRouter` was imported with the statement `import {ICoreBranchRouter} from "./interfaces/ICoreBranchRouter.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L12


In the following file `IVirtualAccount`, `Call` were imported with the statement `import {IVirtualAccount, Call} from "./interfaces/IVirtualAccount.sol";` but were not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L14


In the following file `ERC20hTokenRoot` was imported with the statement `import {ERC20hTokenRoot} from "./token/ERC20hTokenRoot.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L17


In the following file `IRootRouter` was imported with the statement `import {IRootRouter as IRouter} from "./interfaces/IRootRouter.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L23


In the following file `IRootBridgeAgentFactory` was imported with the statement `import {IRootBridgeAgentFactory} from "./interfaces/IRootBridgeAgentFactory.sol";` but was not used. (1 Instance)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L13



# [G-02] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses OR/AND logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

f(x) is a low gas cost operation

g(y) is a high gas cost operation 

Sort operations with different gas costs as follows 

f(x) || g(y)

f(x) && g(y)

### 5 Instances
- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L508
``` solidity
 require(
        msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"
);
```
in `msg.sender == pendingAdmin` SLOAD (cold access) operation is performed it would be better if `msg.sender != address(0)` was witten first in the require statement so that in scenarios where it results to false the EVM would not need to read from storage thereby saving 2100 gas units.

The code should be refactored to:
``` solidity
require(
             msg.sender != address(0) && msg.sender == pendingAdmin, "GovernorBravo:_acceptAdmin: pending admin only"
);
```

- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L114-#L118
``` solidity
require(
            (!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))
                || (msg.sender != userAddress),
            "GovernorBravo::propose: proposer votes below proposal threshold"
);
```
in the `(!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))` of the require statement the EVM would perform more operations than it would perform in the `(msg.sender != userAddress)`. It would be better if the EVM performs `(msg.sender != userAddress)` operations before `(!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))` operations so that in senarios where `(msg.sender != userAddress)` results to true the gas consuming operations of `(!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))` would be avoided.

The code should be refactored to:
``` solidity
require(
        (msg.sender != userAddress) || (!IRootPort(localPortAddress).isRouterApproved(this, msg.sender)),
            "GovernorBravo::propose: proposer votes below proposal threshold"
);
```

- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L165
``` solidity
if (destinationIds[address(destination)] != 0 || destinationId == id) 
    revert InvalidPool();
```
In the `destinationIds[address(destination)]` of the if statement the EVM would perform an SLOAD (cold access) operation which cost 2100 gas units and is more gas consuming than the `destinationId == id` which just compares an immutable and a stack variable. It would be better if `destinationId == id` operations is performed before `destinationIds[address(destination)]` so that in scenarios where `destinationId == id` results to true the EVM would not need to read the storage for `destinationIds[address(destination)]` thereby saving 2100 gas.

The code should be refactored to: 
``` solidity
if (destinationId == id || destinationIds[address(destination)] != 0) 
    revert InvalidPool();
```
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L260
``` solidity
if ((isNotRestake || block.timestamp < endTime) || owner != msg.sender)

```
In the `(isNotRestake || block.timestamp < endTime)` of the if statement the EVM would perform SLOAD (cold access), EQ and ISZERO operations which cost 2106 gas units and is more gas consuming than the `owner != msg.sender` which just compares a stack variable and an address literal. It would be better if `owner != msg.sender` operations is performed before `(isNotRestake || block.timestamp < endTime)` so that in scenarios where `owner != msg.sender` results to true the EVM would not need to perform operations for `(isNotRestake || block.timestamp < endTime)` thereby saving 2106 gas.

The code should be refactored to:
``` solidity
if (owner != msg.sender || (isNotRestake || block.timestamp < endTime))

```

- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L70
``` solidity
 if ((!IRootPort(localPortAddress).isRouterApproved(this, msg.sender)) && (msg.sender != userAddress)) {
            revert UnauthorizedCaller();
}
```
in the `(!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))` of the if statement the EVM would perform more operations than it would perform in the `(msg.sender != userAddress)`. It would be better if the EVM performs `(msg.sender != userAddress)` operations before `(!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))` operations so that in senarios where `(msg.sender != userAddress)` results to false the gas consuming operations of `(!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))` would be avoided.

The code should be refactored to:
``` solidity
 if ((msg.sender != userAddress) && (!IRootPort(localPortAddress).isRouterApproved(this, msg.sender))) {
            revert UnauthorizedCaller();
}
```

## [G-03]  Making constant variables private will save gas during deployment.

When constants are marked internal extra getter functions are created increasing the deployment cost.  One can still read these variables through the source code. In order to eliminate the extra deployment gas cost introduced by these internal constant variables there is a number of mitigating steps we can use depending on how they are to be used. If they need to be accessed by an external contract a separate single getter function can be used to return all constants as a tuple or if they are to be inherited they can be made and if they are not needed by external contracts or inherited then they should be made private.

### 3 Instances (Gas Saved: 124153)
- https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360 (gas saved 29232)

``` solidity
 bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
should be refactored to:
``` solidity
 bytes32 private constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
Test Results
```
|  Methods                                                                                                       │
·············|··························|··············|·············|·············|···············|··············
|  Contract  ·  Method                  ·  Min         ·  Max        ·  Avg        ·  # calls      ·  usd (avg)  │
·············|··························|··············|·············|·············|···············|··············
|  GasTest   ·  createPrivateConstants  ·           -  ·          -  ·      66028  ·            1  ·          -  │
·············|··························|··············|·············|·············|···············|··············
|  GasTest   ·  createPublicConstants   ·           -  ·          -  ·      95251  ·            1  ·          -  │
·············|··························|··············|·············|·············|···············|··············
|  Deployments                          ·                                          ·  % of limit   ·             │
········································|··············|·············|·············|···············|··············
```

- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9-#L37 (gas saved: 51270)

``` solidity
    string public constant name = "vMaia Governor Bravo";

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

````
should be refactored to: 
``` solidity
    string internal constant name = "vMaia Governor Bravo";

    /// @notice The minimum setable proposal threshold
    uint256 internal constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

    /// @notice The maximum setable proposal threshold
    uint256 internal constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

    /// @notice The minimum setable voting period
    uint256 internal constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

    /// @notice The max setable voting period
    uint256 internal constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

    /// @notice The min setable voting delay
    uint256 internal constant MIN_VOTING_DELAY = 40320; // About 1 weeks

    /// @notice The max setable voting delay
    uint256 internal constant MAX_VOTING_DELAY = 80640; // About 2 weeks

    /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed
    uint256 internal constant quorumVotes = 0.35 ether; // 35% of GovToken

    /// @notice The maximum number of actions that can be included in a proposal
    uint256 internal constant proposalMaxOperations = 10; // 10 actions

    /// @notice The divisor value used in percentage calculations
    uint256 internal constant DIVISIONER = 1 ether;

    function getConstants() public returns(
        string memory, 
        uint256,
        uint256,
        uint256,
        uint256,
        uint256,
        uint256,
        uint256,
        uint256,
        uint256
    ){
        return(name, MIN_PROPOSAL_THRESHOLD, MAX_PROPOSAL_THRESHOLD, MIN_VOTING_PERIOD, MAX_VOTING_PERIOD, MIN_VOTING_DELAY, MAX_VOTING_DELAY, quorumVotes, proposalMaxOperations, DIVISIONER)
    }
```
Test Results
```
·······································|····························|·············|······························
|  Methods                                                                                                       │
·············|··························|··············|·············|·············|···············|··············
|  Contract  ·  Method                  ·  Min         ·  Max        ·  Avg        ·  # calls      ·  usd (avg)  │
·············|··························|··············|·············|·············|···············|··············
|  GasTest   ·  createInternaConstants  ·           -  ·          -  ·     178878  ·            1  ·          -  │
·············|··························|··············|·············|·············|···············|··············
|  GasTest   ·  createPublicConstants   ·           -  ·          -  ·     230148  ·            1  ·          -  │
·············|··························|··············|·············|·············|···············|··············
|  Deployments    
```

- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42-#L46 (Gas saved: 43651)

```
 bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
Should be refactored to: 
```
 bytes32 private constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)");

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 private constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

```
Test Result: 
```
········································|····························|·············|······························
|  Methods                                                                                                       │
·············|··························|··············|·············|·············|···············|··············
|  Contract  ·  Method                  ·  Min         ·  Max        ·  Avg        ·  # calls      ·  usd (avg)  │
·············|··························|··············|·············|·············|···············|··············
|  GasTest   ·  createPrivateConstants  ·           -  ·          -  ·      66028  ·            1  ·          -  │
·············|··························|··············|·············|·············|···············|··············
|  GasTest   ·  createPublicConstants   ·           -  ·          -  ·     109679  ·            1  ·          -  │
·············|··························|··············|·············|·············|···············|··············
|  Deployments                          ·                                          ·  % of limit   ·             │
```

## [G-04]  Move lesser gas costing require checks to the top
Require() or revert() statements that check input arguments or cost lesser gas should be at the top of the function.
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.

### Instances

- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L63-#L78

- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L123
- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L363
- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L42-#L45
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L45-#L47
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L102-#L103
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L302-#L303
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L130-#L131
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L145-#L149
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L647-#L648


## [G-05] State variables should be cached
State variables should be cached in stack variables rather than re-reading them from storage. The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. 
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L314-#L415
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L257-#L260
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L751-#L759
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1162
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1172
- https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L436&#L442
- https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L508


### [G-06] Non efficient zero initialization
- https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L55

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas. In the code below there is no need setting the amount variable in the else block to zero because the default value of a uint256 variable is zero 
``` solidity
function getAccruedRewards(ERC20 strategy) external override onlyFlywheel returns (uint256 amount) {
        uint32 timestamp = block.timestamp.toUint32();

        // if cycle has ended, reset cycle and transfer all available
        if (timestamp >= endCycle) {
            amount = getNextCycleRewards(strategy);

            // reset for next cycle
            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
            endCycle = newEndCycle;

            emit NewRewardsCycle(timestamp, newEndCycle, amount);
        } else {
            amount = 0;
        }
    }
```
This code could be refactored to:
``` solidity
function getAccruedRewards(ERC20 strategy) external override onlyFlywheel returns (uint256 amount) {
        uint32 timestamp = block.timestamp.toUint32();

        // if cycle has ended, reset cycle and transfer all available
        if (timestamp >= endCycle) {
            amount = getNextCycleRewards(strategy);

            // reset for next cycle
            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;
            endCycle = newEndCycle;

            emit NewRewardsCycle(timestamp, newEndCycle, amount);
        } else {
            amount;
        }
    }

```
Please note that in the automated findings report this instance was not included.