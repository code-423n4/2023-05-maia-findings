
# VULN 1 

## [LOW] Keccak Constant values should used to immutable rather than constant
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 46 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

------------------------------------------------------------------------ 

### Mitigation 

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.










# VULN 2 

## [LOW] Division before multiplication causing significant loss of precision
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 50 at 2023-05-maia/src/rewards/rewards/FlywheelAcummulatedRewards.sol:

            uint256 newEndCycle = ((timestamp + rewardsCycleLength) / rewardsCycleLength) * rewardsCycleLength;


Found in line 60 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        gaugeCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;


Found in line 79 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;


Found in line 114 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;


Found in line 82 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

        activePeriod = (block.timestamp / week) * week;


Found in line 127 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

            _period = (block.timestamp / week) * week;


Found in line 68 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

        epoch = (block.timestamp / WEEK) * WEEK;


Found in line 84 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

        uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;

------------------------------------------------------------------------ 

### Mitigation 

Multiply first before dividing to keep the precision.










# VULN 3 

## [LOW] Keccak Constant values should used to immutable rather than constant
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 46 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");

------------------------------------------------------------------------ 

### Mitigation 

By using immutable variables, the Keccak256 hash is computed at contract deployment time rather than at compilation time. This means that the value can be updated if the algorithm changes in a future compiler version, without breaking backward compatibility. Additionally, it provides better gas optimization, as the Keccak256 hash is computed only once at contract deployment instead of every time the variable is accessed during execution.










# VULN 4 

## [LOW] Use .call instead of .transfer to send ether
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 91 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

        return super.transfer(_to, _amount);


Found in line 409 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        if (amount0 > 0) _token0.transfer(msg.sender, amount0);


Found in line 410 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        if (amount1 > 0) _token1.transfer(msg.sender, amount1);


Found in line 148 at 2023-05-maia/src/hermes/bHermes.sol:

        return super.transfer(to, amount);


Found in line 270 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        return super.transfer(to, amount);


Found in line 498 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        return super.transfer(to, amount);


Found in line 293 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        return super.transfer(to, amount);


Found in line 313 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        return super.transfer(to, amount);

------------------------------------------------------------------------ 

### Mitigation 

.transfer will relay 2300 gas and .call will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using .transfer will fail, causing integration issues.Replace .transfer with .call. Note that the result of .call need to be checked.










# VULN 5 

## [LOW] Use the safe variant and ERC721.mint
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 157 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        _mint(receiver, shares);


Found in line 218 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        _mint(receiver, shares);


Found in line 74 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

        _mint(to, amount);


Found in line 24 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenBranch.sol:

        _mint(account, amount);


Found in line 128 at 2023-05-maia/src/hermes/bHermes.sol:

    function _mint(address to, uint256 amount) internal virtual override {


Found in line 132 at 2023-05-maia/src/hermes/bHermes.sol:

        super._mint(to, amount);


Found in line 27 at 2023-05-maia/src/hermes/tokens/bHermesVotes.sol:

        _mint(to, amount);


Found in line 29 at 2023-05-maia/src/hermes/tokens/bHermesBoost.sol:

        _mint(to, amount);


Found in line 63 at 2023-05-maia/src/hermes/tokens/HERMES.sol:

        _mint(account, amount);


Found in line 36 at 2023-05-maia/src/hermes/tokens/bHermesGauges.sol:

        _mint(to, amount);


Found in line 240 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function _mint(address to, uint256 amount) internal virtual override {


Found in line 245 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        super._mint(to, amount);


Found in line 56 at 2023-05-maia/src/maia/tokens/Maia.sol:

        _mint(account, amount);


Found in line 42 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        _mint(receiver, shares);


Found in line 54 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        _mint(receiver, shares);


Found in line 39 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

        _mint(receiver, shares);


Found in line 53 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

        _mint(receiver, shares);


Found in line 105 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        _mint(receiver, shares);


Found in line 124 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        _mint(receiver, shares);


Found in line 39 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        _mint(receiver, shares);


Found in line 53 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        _mint(receiver, shares);

------------------------------------------------------------------------ 

### Mitigation 

.mint won’t check if the recipient is able to receive the NFT. If an incorrect address is passed, it will result in a silent failure and loss of asset. OpenZeppelin recommendation is to use the safe variant of _mint. Replace _mint() with _safeMint().










# VULN 6 

## [LOW] Open TODOs
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 74 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

    /// TODO: @inheritdoc IERC4626DepositOnly


Found in line 9 at 2023-05-maia/src/uni-v3-staker/libraries/NFTPositionInfo.sol:

// TODO: The INIT_CODE_HASH needs to be updated to the values that are live on the chain of it's deployment.

------------------------------------------------------------------------ 

### Mitigation 

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.










# VULN 7 

## [LOW] Use safeTransferOwnership instead of transferOwnership function
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 124 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

            pools[poolIds[i]].transferOwnership(owner);


Found in line 138 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

        bHermesGaugeWeight.transferOwnership(newOwner);


Found in line 139 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

        bHermesGaugeBoost.transferOwnership(newOwner);

------------------------------------------------------------------------ 

### Mitigation 

Use a 2 structure transferOwnership which is safer. safeTransferOwnership, use it is more secure due to 2-stage ownership transfer. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol










# VULN 8 

## [LOW] safeApprove of openZeppelin is deprecated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 40 at 2023-05-maia/src/ulysses-amm/UlyssesRouter.sol:

            address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);


Found in line 130 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);


Found in line 131 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);


Found in line 36 at 2023-05-maia/src/rewards/base/BaseFlywheelRewards.sol:

        _rewardToken.safeApprove(address(_flywheel), type(uint256).max);


Found in line 195 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(gaugeWeight).safeApprove(oldPartnerVault, 0);


Found in line 196 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(gaugeBoost).safeApprove(oldPartnerVault, 0);


Found in line 197 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(governance).safeApprove(oldPartnerVault, 0);


Found in line 198 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(partnerGovernance).safeApprove(oldPartnerVault, 0);


Found in line 200 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);


Found in line 201 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);


Found in line 202 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(governance).safeApprove(newPartnerVault, type(uint256).max);


Found in line 203 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);


Found in line 45 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

        rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);

------------------------------------------------------------------------ 

### Mitigation 

Deprecated, its usage is discouraged. https://docs.openzeppelin.com/contracts/3.x/api/token/erc20. Whenever possible, use safeIncreaseAllowance and safeDecreaseAllowance instead. https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#SafeERC20-safeIncreaseAllowance-contract-IERC20-address-uint256- & https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#SafeERC20-safeDecreaseAllowance-contract-IERC20-address-uint256-










# VULN 9 

## [LOW] Immutables should be in uppercase
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 19 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    uint256 public immutable id;


Found in line 25 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    UlyssesFactory public immutable factory;


Found in line 28 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    uint256 public immutable id;


Found in line 22 at 2023-05-maia/src/talos/TalosManager.sol:

    int24 public immutable ticksFromLowerRebalance;


Found in line 25 at 2023-05-maia/src/talos/TalosManager.sol:

    int24 public immutable ticksFromUpperRebalance;


Found in line 28 at 2023-05-maia/src/talos/TalosManager.sol:

    int24 public immutable ticksFromLowerRerange;


Found in line 31 at 2023-05-maia/src/talos/TalosManager.sol:

    int24 public immutable ticksFromUpperRerange;


Found in line 34 at 2023-05-maia/src/talos/TalosManager.sol:

    ITalosBaseStrategy public immutable strategy;


Found in line 46 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

    BoostAggregator public immutable override boostAggregator;


Found in line 49 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

    FlywheelCoreInstant public immutable flywheel;


Found in line 27 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    INonfungiblePositionManager public immutable nonfungiblePositionManager;


Found in line 30 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    UniswapV3Staker public immutable uniswapV3Staker;


Found in line 33 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    bHermesBoost public immutable hermesGaugeBoost;


Found in line 36 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    ERC20 public immutable hermes;


Found in line 26 at 2023-05-maia/src/talos/factories/TalosStrategyStakedFactory.sol:

    BoostAggregatorFactory public immutable boostAggregatorFactory;


Found in line 29 at 2023-05-maia/src/talos/factories/TalosStrategyStakedFactory.sol:

    FlywheelCoreInstant public immutable flywheel;


Found in line 32 at 2023-05-maia/src/talos/factories/TalosStrategyStakedFactory.sol:

    FlywheelInstantRewards public immutable rewards;


Found in line 23 at 2023-05-maia/src/talos/factories/TalosBaseStrategyFactory.sol:

    INonfungiblePositionManager public immutable nonfungiblePositionManager;


Found in line 26 at 2023-05-maia/src/talos/factories/TalosBaseStrategyFactory.sol:

    OptimizerFactory public immutable optimizerFactory;


Found in line 19 at 2023-05-maia/src/talos/factories/BoostAggregatorFactory.sol:

    UniswapV3Staker public immutable uniswapV3Staker;


Found in line 22 at 2023-05-maia/src/talos/factories/BoostAggregatorFactory.sol:

    ERC20 public immutable hermes;


Found in line 61 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    ERC20 public immutable override token0;


Found in line 63 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    ERC20 public immutable override token1;


Found in line 65 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    int24 public immutable override tickSpacing;


Found in line 67 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    uint24 public immutable override poolFee;


Found in line 69 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    IUniswapV3Pool public immutable override pool;


Found in line 71 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    ITalosOptimizer public immutable override optimizer;


Found in line 73 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    address public immutable strategyManager;


Found in line 75 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    INonfungiblePositionManager public immutable override nonfungiblePositionManager;


Found in line 51 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    uint256 public immutable localChainId;


Found in line 54 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    address public immutable localPortAddress;


Found in line 57 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    address public immutable multicallAddress;


Found in line 89 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 public immutable rootChainId;


Found in line 92 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 public immutable localChainId;


Found in line 95 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    WETH9 public immutable wrappedNativeToken;


Found in line 98 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    address public immutable rootBridgeAgentAddress;


Found in line 101 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    address public immutable localAnyCallAddress;


Found in line 104 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    address public immutable localAnyCallExecutorAddress;


Found in line 107 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    address public immutable localRouterAddress;


Found in line 110 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    address public immutable localPortAddress;


Found in line 123 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint24 public immutable localChainId;


Found in line 126 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    WETH9 public immutable wrappedNativeToken;


Found in line 129 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable factoryAddress;


Found in line 132 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable daoAddress;


Found in line 135 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable localRouterAddress;


Found in line 138 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable localPortAddress;


Found in line 141 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable localAnyCallAddress;


Found in line 144 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable localAnyCallExecutorAddress;


Found in line 147 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    address public immutable bridgeAgentExecutorAddress;


Found in line 40 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    WETH9 public immutable wrappedNativeToken;


Found in line 43 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    uint24 public immutable rootChainId;


Found in line 46 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    address public immutable rootPortAddress;


Found in line 28 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    uint24 public immutable localChainId;


Found in line 31 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    address public immutable wrappedNativeTokenAddress;


Found in line 20 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

    address public immutable userAddress;


Found in line 16 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

    uint256 public immutable localChainId;


Found in line 19 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

    address public immutable rootPortAddress;


Found in line 18 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    uint256 public immutable localChainId;


Found in line 21 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    uint256 public immutable rootChainId;


Found in line 24 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    address public immutable rootBridgeAgentFactoryAddress;


Found in line 27 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    WETH9 public immutable wrappedNativeToken;


Found in line 30 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    address public immutable localCoreBranchRouterAddress;


Found in line 33 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    address public immutable localPortAddress;


Found in line 36 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    address public immutable localAnyCallAddress;


Found in line 39 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    address public immutable localAnyCallExecutorAddress;


Found in line 13 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

    uint24 public immutable localChainId;


Found in line 16 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

    address immutable localPortAddress;


Found in line 20 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    uint24 public immutable rootChainId;


Found in line 23 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    WETH9 public immutable wrappedNativeToken;


Found in line 26 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    address public immutable rootPortAddress;


Found in line 29 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    address public immutable daoAddress;


Found in line 32 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    address public immutable localAnyCallAddress;


Found in line 35 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    address public immutable localAnyCallExecutorAddress;


Found in line 13 at 2023-05-maia/src/rewards/depots/SingleRewardsDepot.sol:

    address private immutable asset;


Found in line 16 at 2023-05-maia/src/rewards/depots/SingleRewardsDepot.sol:

    address private immutable rewardsContract;


Found in line 22 at 2023-05-maia/src/rewards/rewards/FlywheelInstantRewards.sol:

    SingleRewardsDepot public immutable rewardsDepot;


Found in line 23 at 2023-05-maia/src/rewards/rewards/FlywheelAcummulatedRewards.sol:

    uint256 public immutable override rewardsCycleLength;


Found in line 27 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    ERC20Gauges public immutable override gaugeToken;


Found in line 30 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    IBaseV2Minter public immutable minter;


Found in line 33 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    address public immutable override rewardToken;


Found in line 39 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    uint32 public immutable override gaugeCycleLength;


Found in line 26 at 2023-05-maia/src/rewards/base/BaseFlywheelRewards.sol:

    address public immutable override rewardToken;


Found in line 29 at 2023-05-maia/src/rewards/base/BaseFlywheelRewards.sol:

    FlywheelCore public immutable override flywheel;


Found in line 24 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

    address public immutable override rewardToken;


Found in line 42 at 2023-05-maia/src/rewards/booster/FlywheelBoosterGaugeWeight.sol:

    bHermesGauges private immutable bhermes;


Found in line 25 at 2023-05-maia/src/hermes/UtilityManager.sol:

    bHermesGauges public immutable gaugeWeight;


Found in line 27 at 2023-05-maia/src/hermes/UtilityManager.sol:

    bHermesBoost public immutable gaugeBoost;


Found in line 29 at 2023-05-maia/src/hermes/UtilityManager.sol:

    ERC20Votes public immutable governance;


Found in line 18 at 2023-05-maia/src/hermes/tokens/bHermesVotes.sol:

    address public immutable bHermes;


Found in line 20 at 2023-05-maia/src/hermes/tokens/bHermesBoost.sol:

    address public immutable bHermes;


Found in line 24 at 2023-05-maia/src/hermes/tokens/bHermesGauges.sol:

    address public immutable bHermes;


Found in line 32 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    address public immutable override underlying;


Found in line 34 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    ERC4626 public immutable override vault;


Found in line 23 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    ERC20Votes public immutable partnerGovernance;


Found in line 18 at 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol:

    ERC20 public immutable override bHermes;


Found in line 31 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    PartnerManagerFactory public immutable override factory;


Found in line 34 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    bHermes public immutable override bHermesToken;


Found in line 26 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

    address public immutable override rewardToken;


Found in line 29 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

    bHermesBoost public immutable hermesGaugeBoost;


Found in line 32 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

    FlywheelGaugeRewards public immutable override flywheelGaugeRewards;


Found in line 20 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

    address public immutable override uniswapV3Staker;


Found in line 23 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

    bHermesGauges public immutable bHermesGaugeWeight;


Found in line 26 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

    bHermesBoost public immutable bHermesGaugeBoost;


Found in line 24 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

    uint256 public immutable rewardsCycleLength;


Found in line 26 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

    FlywheelBoosterGaugeWeight private immutable flywheelGaugeWeightBooster;


Found in line 41 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

    BaseV2GaugeManager public immutable gaugeManager;


Found in line 35 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    UniswapV3Staker public immutable override uniswapV3Staker;


Found in line 38 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    FlywheelGaugeRewards public immutable override flywheelGaugeRewards;


Found in line 24 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    BaseV2GaugeManager public immutable override gaugeManager;


Found in line 27 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    bHermesBoost public immutable override bHermesBoostToken;


Found in line 30 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    BribesFactory public immutable override bribesFactory;


Found in line 22 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

    address public immutable asset;


Found in line 21 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

    ERC20 public immutable asset;


Found in line 21 at 2023-05-maia/src/erc-4626/ERC4626.sol:

    ERC20 public immutable asset;


Found in line 91 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    IUniswapV3GaugeFactory public immutable uniswapV3GaugeFactory;


Found in line 94 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    IUniswapV3Factory public immutable override factory;


Found in line 97 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    INonfungiblePositionManager public immutable override nonfungiblePositionManager;


Found in line 100 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    uint256 public immutable override maxIncentiveStartLeadTime;


Found in line 103 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    address public immutable minter;


Found in line 106 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    address public immutable hermes;


Found in line 109 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    bHermesBoost public immutable hermesGaugeBoost;


Found in line 39 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    uint32 public immutable override gaugeCycleLength;


Found in line 42 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    uint32 public immutable override incrementFreezeWindow;

------------------------------------------------------------------------ 

### Mitigation 

Immutables should be in uppercase, it helps to distinguish immutables from other types of variables and provides better code readability.
