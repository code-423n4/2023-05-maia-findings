# VULN 1 

## [GAS] Use != 0 instead of > 0 for unsigned integer comparison
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 47 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        require(_weight > 0);


Found in line 153 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        if (claimed > 0) {


Found in line 191 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            if (oldBandwidth > 0) {


Found in line 256 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                    if (oldBandwidth > 0) {


Found in line 272 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            if (oldBandwidth > 0) {


Found in line 282 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                    } else if (leftOverBandwidth > 0) {


Found in line 911 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            if (updateAmount > 0) {


Found in line 1048 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            if (updateAmount > 0) {


Found in line 111 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

                if (j != i && weights[i][j] > 0) pools[poolIds[i]].addNewBandwidth(poolIds[j], weights[i][j]);


Found in line 139 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        if (_liquidity > 0) {


Found in line 409 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        if (amount0 > 0) _token0.transfer(msg.sender, amount0);


Found in line 410 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        if (amount1 > 0) _token1.transfer(msg.sender, amount1);


Found in line 98 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

        if (tick < 0 && tick % tickSpacing != 0) compressed--;


Found in line 471 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));


Found in line 474 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();


Found in line 496 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));


Found in line 499 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();


Found in line 518 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));


Found in line 521 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();


Found in line 540 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            (remoteCallDepositedGas > 0 ? (_gasToBridgeOut, true) : (msg.value.toUint128(), false));


Found in line 543 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (!isRemote && gasToBridgeOut > 0) wrappedNativeToken.deposit{value: msg.value}();


Found in line 623 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            if (_amounts[i] - _deposits[i] > 0) {


Found in line 627 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            if (_deposits[i] > 0) {


Found in line 979 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (_amount - _deposit > 0) {


Found in line 983 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (_deposit > 0) {


Found in line 1328 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}


Found in line 57 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

                || (_dParams.amount > 0 && !IPort(_localPortAddress).isLocalToken(_dParams.hToken, _fromChain)) //Check local exists.


Found in line 58 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

                || (_dParams.deposit > 0 && !IPort(_localPortAddress).isUnderlyingToken(_dParams.token, _fromChain)) //Check underlying exists.


Found in line 301 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0)) {


Found in line 347 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();


Found in line 451 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (_amount - _deposit > 0) {


Found in line 457 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (_deposit > 0) {


Found in line 651 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        address(data.tokenIn).safeTransfer(msg.sender, uint256(amount0 > 0 ? amount0 : amount1));


Found in line 750 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (_initialGas > 0) {


Found in line 757 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (_initialGas > 0) {


Found in line 1161 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (initialGas > 0) {


Found in line 1171 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (initialGas > 0) {


Found in line 1240 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}


Found in line 160 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

        if (gasRemaining > 0) {


Found in line 118 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        if (_deposit > 0) {


Found in line 123 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        if (_amount - _deposit > 0) {


Found in line 137 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

            if (_deposits[i] > 0) {


Found in line 144 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

            if (_amounts[i] - _deposits[i] > 0) {


Found in line 248 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        if (_amount - _deposit > 0) {


Found in line 252 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        if (_deposit > 0) {


Found in line 268 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

            if (_deposits[i] > 0) {


Found in line 275 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

            if (_amounts[i] - _deposits[i] > 0) {


Found in line 282 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        if (_amount - _deposit > 0) _hToken.safeTransfer(_recipient, _amount - _deposit);


Found in line 283 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        if (_deposit > 0) mint(_recipient, _hToken, _deposit, _fromChainId);


Found in line 234 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);


Found in line 127 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

        if (oldRewardBalance > 0) {


Found in line 162 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

        if (strategyRewardsAccrued > 0) {


Found in line 75 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {


Found in line 85 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {


Found in line 95 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {


Found in line 52 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

            require(_weights[i] > 0);


Found in line 199 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (incentive.numberOfStakes > 0) revert EndIncentiveWhileStakesArePresent();


Found in line 271 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (reward > 0) hermes.safeTransfer(to, reward);


Found in line 281 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (reward > 0) hermes.safeTransfer(to, reward);


Found in line 31 at 2023-05-maia/src/uni-v3-staker/libraries/RewardMath.sol:

        if (boostTotalSupply > 0) {


Found in line 417 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        if (weight > 0) {


Found in line 441 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        if (weight > 0) {


Found in line 250 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {

------------------------------------------------------------------------ 

### Mitigation 

Use != 0 instead of > 0 for unsigned integer comparison.










# VULN 2 

## [GAS] Use shift Right/Left instead of division/multiplication if possible
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 312 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        if (_fees.lambda1 + _fees.lambda2 != DIVISIONER / 2) revert InvalidFee();


Found in line 247 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

            ? int256((cache.amount0Desired - cache.amount0) / 2)


Found in line 248 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

            : int256((cache.amount1Desired - cache.amount1) / 2); // always positive. "overflow" safe convertion cuz we are dividing by 2


Found in line 251 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (_strategy.priceImpactPercentage() / 2)) / GLOBAL_DIVISIONER;


Found in line 137 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 internal constant MIN_FALLBACK_RESERVE = 185_000; // 100_000 for anycall + 85_000 fallback execution overhead


Found in line 138 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 internal constant MIN_EXECUTION_OVERHEAD = 160_000; // 100_000 for anycall + 35_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Executions


Found in line 180 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; // 100_000 for anycall + 55_000 for fallback


Found in line 181 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution


Found in line 677 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;


Found in line 720 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;


Found in line 35 at 2023-05-maia/src/uni-v3-staker/libraries/RewardMath.sol:

                ((secondsInsideX128 * 4) / 10) + ((((stakedDuration << 128) * boostAmount) / boostTotalSupply) * 6) / 10


Found in line 44 at 2023-05-maia/src/uni-v3-staker/libraries/RewardMath.sol:

            boostedSecondsInsideX128 = (secondsInsideX128 * 4) / 10;


Found in line 82 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        return (a & b) + (a ^ b) / 2;

------------------------------------------------------------------------ 

### Mitigation 

Use shift Right/Left instead of division/multiplication if possible.










# VULN 3 

## [GAS] ++i/i++ should be unchecked{++i}/unchecked{i++} and ++i costs less gas than i++
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 95 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        for (uint256 i = 0; i < assets.length; i++) {


Found in line 111 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        for (uint256 i = 0; i < assets.length; i++) {


Found in line 130 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < bandwidthStateList.length; i++) {


Found in line 175 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < index; i++) {


Found in line 211 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i <= index; i++) {


Found in line 232 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < bandwidthStateList.length; i++) {


Found in line 296 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < bandwidthStateList.length; i++) {


Found in line 283 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {


Found in line 719 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {


Found in line 938 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        return depositNonce++;


Found in line 1354 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _deposits.length; i++) {


Found in line 633 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        return settlementNonce++;


Found in line 109 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        bridgeAgentFactoriesLenght++;


Found in line 280 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

                i++;


Found in line 293 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        bridgeAgentsLenght++;


Found in line 311 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        bridgeAgentFactoriesLenght++;


Found in line 334 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        strategyTokensLenght++;


Found in line 355 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        portStrategiesLenght++;


Found in line 135 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        bridgeAgentFactoriesLenght++;


Found in line 370 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        bridgeAgentsLenght++;


Found in line 407 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        bridgeAgentFactories[bridgeAgentsLenght++] = _bridgeAgentFactory;


Found in line 48 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 67 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

        hTokensLenght++;


Found in line 45 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

        hTokensLenght++;


Found in line 67 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

        hTokensLenght++;


Found in line 176 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        for (uint256 i = 0; i < size; i++) {


Found in line 118 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

                i++;


Found in line 68 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

                i++;


Found in line 84 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

                i++;


Found in line 81 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

                i++;


Found in line 97 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

                i++;


Found in line 60 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 72 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 83 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 174 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 205 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 219 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 238 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 254 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

                i++;


Found in line 502 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        incentives[incentiveId].numberOfStakes++;


Found in line 142 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        proposalCount++;


Found in line 181 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 215 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 251 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 117 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                i++;


Found in line 158 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                i++;


Found in line 266 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                i++;


Found in line 346 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                i++;


Found in line 548 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                    i++;


Found in line 328 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {


Found in line 55 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

                i++;


Found in line 101 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

                i++;


Found in line 166 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

                i++;


Found in line 224 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

                i++;


Found in line 245 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

                i++;

------------------------------------------------------------------------ 

### Mitigation 

++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for and while-loops. Moreover ++i costs less gas than i++, especially when its used in for-loops (--i/i-- too).










# VULN 4 

## [GAS] Don’t initialize variables with default value
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 78 at 2023-05-maia/src/ulysses-amm/UlyssesRouter.sol:

        for (uint256 i = 0; i < length;) {


Found in line 95 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        for (uint256 i = 0; i < assets.length; i++) {


Found in line 111 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        for (uint256 i = 0; i < assets.length; i++) {


Found in line 99 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        for (uint256 i = 0; i < length;) {


Found in line 107 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        for (uint256 i = 0; i < length;) {


Found in line 110 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

            for (uint256 j = 0; j < length;) {


Found in line 123 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        for (uint256 i = 0; i < length;) {


Found in line 145 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        for (uint256 i = 0; i < length;) {


Found in line 146 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        for (uint256 i = 0; i < outputTokens.length;) {


Found in line 311 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            for (uint256 i = 0; i < outputParams.outputTokens.length;) {


Found in line 387 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            for (uint256 i = 0; i < outputParams.outputTokens.length;) {


Found in line 463 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            for (uint256 i = 0; i < outputParams.outputTokens.length;) {


Found in line 283 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {


Found in line 580 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < numOfAssets;) {


Found in line 719 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {


Found in line 951 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < deposit.hTokens.length;) {


Found in line 1354 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _deposits.length; i++) {


Found in line 364 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {


Found in line 342 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < _globalAddresses.length;) {


Found in line 408 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length;) {


Found in line 561 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < newGas.length;) {


Found in line 596 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < settlement.hTokens.length;) {


Found in line 101 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 136 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 231 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 267 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 48 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 176 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        for (uint256 i = 0; i < size; i++) {


Found in line 114 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

        for (uint256 i = 0; i < length;) {


Found in line 64 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

        for (uint256 i = 0; i < length;) {


Found in line 77 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

        for (uint256 i = 0; i < length;) {


Found in line 50 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 68 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 79 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 170 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 201 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 216 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 235 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 250 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        for (uint256 i = 0; i < length;) {


Found in line 181 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 215 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 251 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 114 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        for (uint256 i = 0; i < num;) {


Found in line 155 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        for (uint256 i = 0; i < num;) {


Found in line 259 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        for (uint256 i = 0; i < size;) {


Found in line 338 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        for (uint256 i = 0; i < size;) {


Found in line 536 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        for (uint256 i = 0; i < size && (userFreeWeight + totalFreed) < weight;) {


Found in line 67 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        uint256 low = 0;


Found in line 328 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {


Found in line 52 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        for (uint256 i = 0; i < num;) {


Found in line 98 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        for (uint256 i = 0; i < num;) {


Found in line 151 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        uint256 userBoost = 0;


Found in line 156 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        for (uint256 i = 0; i < length;) {


Found in line 207 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        for (uint256 i = 0; i < num && i < length;) {


Found in line 236 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        for (uint256 i = 0; i < size;) {

------------------------------------------------------------------------ 

### Mitigation 

In such cases, initializing the variables with default values would be unnecessary and can be considered a waste of gas. Additionally, initializing variables with default values can sometimes lead to unnecessary storage operations, which can increase gas costs. For example, if you have a large array of variables, initializing them all with default values can result in a lot of unnecessary storage writes, which can increase the gas costs of your contract.










# VULN 5 

## [GAS] Use Custom Errors
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 61 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        require(_owner != address(0), "Owner cannot be 0");


Found in line 66 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        revert("Cannot renounce ownership");


Found in line 65 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(_localPortAddress != address(0), "Local Port Address cannot be 0");


Found in line 66 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(_multicallAddress != address(0), "Multicall Address cannot be 0");


Found in line 75 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(_bridgeAgentAddress != address(0), "Bridge Agent Address cannot be 0");


Found in line 509 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(msg.sender == bridgeAgentExecutorAddress, "Unauthorized Caller");


Found in line 151 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_rootBridgeAgentAddress != address(0), "Root Bridge Agent Address cannot be the zero address.");


Found in line 152 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localAnyCallAddress != address(0), "AnyCall Address cannot be the zero address.");


Found in line 153 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localAnyCallExecutorAddress != address(0), "AnyCall Executor Address cannot be the zero address.");


Found in line 154 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localRouterAddress != address(0), "Local Router Address cannot be the zero address.");


Found in line 155 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localPortAddress != address(0), "Local Port Address cannot be the zero address.");


Found in line 210 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(address(_wrappedNativeToken) != address(0), "Wrapped native token cannot be zero address");


Found in line 211 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_daoAddress != address(0), "DAO cannot be zero address");


Found in line 212 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localAnyCallAddress != address(0), "Anycall Address cannot be zero address");


Found in line 213 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localAnyCallExecutorAddress != address(0), "Anycall Executor Address cannot be zero address");


Found in line 214 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localPortAddress != address(0), "Port Address cannot be zero address");


Found in line 215 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localRouterAddress != address(0), "Router Address cannot be zero address");


Found in line 38 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

        require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");


Found in line 34 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 95 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(_owner != address(0), "Owner is zero address");


Found in line 100 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(coreBranchRouterAddress == address(0), "Contract already initialized");


Found in line 101 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(!isBridgeAgentFactory[_bridgeAgentFactory], "Contract already initialized");


Found in line 103 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(_coreBranchRouter != address(0), "CoreBranchRouter is zero address");


Found in line 104 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(_bridgeAgentFactory != address(0), "BridgeAgentFactory is zero address");


Found in line 114 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        revert("Cannot renounce ownership");


Found in line 302 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(coreBranchRouterAddress != address(0), "CoreRouter address is zero");


Found in line 303 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(_newCoreRouter != address(0), "New CoreRouter address is zero");


Found in line 115 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_wrappedNativeToken != address(0), "Invalid wrapped native token address.");


Found in line 129 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_setup, "Setup ended.");


Found in line 130 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_bridgeAgentFactory != address(0), "Bridge Agent Factory cannot be 0 address.");


Found in line 131 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_coreRootRouter != address(0), "Core Root Router cannot be 0 address.");


Found in line 145 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_setup, "Setup ended.");


Found in line 146 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(isBridgeAgent[_coreRootBridgeAgent], "Core Bridge Agent doesn't exist.");


Found in line 147 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");


Found in line 148 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");


Found in line 149 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");


Found in line 159 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_owner != address(0), "Owner cannot be 0 address.");


Found in line 166 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        revert("Cannot renounce ownership");


Found in line 45 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 46 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

        require(_factoryAddress != address(0), "Factory Address cannot be 0");


Found in line 34 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 41 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

        require(_coreRouter != address(0), "CoreRouter address cannot be 0");


Found in line 64 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_rootBridgeAgentFactoryAddress != address(0), "Root Bridge Agent Factory Address cannot be 0");


Found in line 65 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(address(_wrappedNativeToken) != address(0), "Wrapped Native Token cannot be 0");


Found in line 66 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localAnyCallAddress != address(0), "Anycall Address cannot be 0");


Found in line 67 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localAnyCallExecutorAddress != address(0), "Anyexec Address cannot be 0");


Found in line 68 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localCoreBranchRouterAddress != address(0), "Core Branch Router Address cannot be 0");


Found in line 69 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localPortAddress != address(0), "Port Address cannot be 0");


Found in line 70 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_owner != address(0), "Owner cannot be 0");


Found in line 84 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0");


Found in line 28 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

        require(_localPortAddress != address(0), "Port address cannot be 0");


Found in line 36 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

        require(_coreRouter != address(0), "CoreRouter address cannot be 0");


Found in line 55 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        require(address(_wrappedNativeToken) != address(0), "Wrapped Native Token cannot be 0");


Found in line 56 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 57 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        require(_daoAddress != address(0), "DAO Address cannot be 0");


Found in line 116 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

        require(strategyIndex[strategy] == 0, "strategy");


Found in line 40 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        require(shares != 0, "ZERO_SHARES");


Found in line 50 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        require(assets != 0, "ZERO_ASSETS");


Found in line 75 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        require(assets != 0, "ZERO_ASSETS");


Found in line 34 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

        require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");


Found in line 100 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        require((shares = previewDeposit(assetsAmounts)) != 0, "ZERO_SHARES");


Found in line 34 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");


Found in line 88 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");


Found in line 42 at 2023-05-maia/src/governance/GovernorBravoDelegator.sol:

        require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");


Found in line 63 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");


Found in line 64 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::initialize: admin only");


Found in line 65 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");


Found in line 66 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");


Found in line 112 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");


Found in line 123 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(targets.length != 0, "GovernorBravo::propose: must provide actions");


Found in line 124 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");


Found in line 146 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");


Found in line 228 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(state(proposalId) != ProposalState.Executed, "GovernorBravo::cancel: cannot cancel executed proposal");


Found in line 350 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");


Found in line 362 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");


Found in line 363 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");


Found in line 366 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");


Found in line 398 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");


Found in line 414 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");


Found in line 431 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");


Found in line 462 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::_setWhitelistGuardian: admin only");


Found in line 475 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::_initiate: admin only");


Found in line 476 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");


Found in line 489 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo:_setPendingAdmin: admin only");


Found in line 527 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(c >= a, "addition overflow");


Found in line 532 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(b <= a, "subtraction underflow");


Found in line 364 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");


Found in line 375 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");

------------------------------------------------------------------------ 

### Mitigation 

Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.










# VULN 6 

## [GAS] Use calldata instead of memory for function arguments that do not get mutated
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 88 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {


Found in line 62 at 2023-05-maia/src/ulysses-amm/interfaces/IUlyssesToken.sol:

    function setWeights(uint256[] memory _weights) external;


Found in line 38 at 2023-05-maia/src/talos/libraries/PoolActions.sol:

    function swapToEqualAmounts(ActionParams memory actionParams, int24 baseThreshold) internal {


Found in line 91 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function _multicall(IMulticall.Call[] memory calls)


Found in line 192 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function callOutAndBridge(bytes calldata _params, DepositInput memory _dParams, uint128 _remoteExecutionGas)


Found in line 238 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function callOutSignedAndBridge(bytes calldata _params, DepositInput memory _dParams, uint128 _remoteExecutionGas)


Found in line 441 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _sendRetrieveOrRetry(bytes memory _data) internal {


Found in line 757 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _noDepositCall(address _depositor, bytes memory _data, uint128 _gasToBridgeOut) internal {


Found in line 1006 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _performCall(bytes memory _calldata) internal virtual {


Found in line 1099 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _gasSwapIn(bytes memory gasData) internal virtual returns (uint256 gasAmount) {


Found in line 1349 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _normalizeDecimalsMultiple(uint256[] memory _deposits, address[] memory _tokens)


Found in line 325 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function _bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain) internal {


Found in line 50 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function checkParams(address _localPortAddress, DepositParams memory _dParams, uint24 _fromChain)


Found in line 275 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function callOut(address _recipient, bytes memory _data, uint24 _toChain) external payable lock requiresRouter {


Found in line 384 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain)


Found in line 404 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function bridgeInMultiple(address _recipient, DepositMultipleParams memory _dParams, uint24 _fromChain)


Found in line 775 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _performCall(bytes memory _calldata, uint256 _toChain) internal {


Found in line 65 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function callOutAndBridge(bytes calldata params, DepositInput memory dParams, uint128 remoteExecutionGas)


Found in line 112 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function anyExecuteSettlement(bytes calldata, SettlementParams memory)


Found in line 123 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function anyExecuteSettlementMultiple(bytes calldata, SettlementMultipleParams memory)


Found in line 379 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteDepositSingle(bytes1, bytes memory, DepositParams memory, uint24)


Found in line 390 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteDepositMultiple(bytes1, bytes calldata, DepositMultipleParams memory, uint24)


Found in line 400 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteSigned(bytes1, bytes memory, address, uint24)


Found in line 411 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteSignedDepositSingle(bytes1, bytes memory, DepositParams memory, address, uint24)


Found in line 422 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteSignedDepositMultiple(bytes1, bytes memory, DepositMultipleParams memory, address, uint24)


Found in line 142 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

    function _performCall(bytes memory _callData) internal override {


Found in line 149 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

    function _gasSwapIn(bytes memory gasData) internal override returns (uint256 gasAmount) {


Found in line 96 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

    function bridgeInMultiple(address _recipient, address[] memory _localAddresses, uint256[] memory _amounts)


Found in line 226 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    function bridgeInMultiple(address _recipient, address[] memory _localAddresses, uint256[] memory _amounts)


Found in line 52 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol:

    function callOutAndBridge(bytes calldata params, DepositInput memory dParams, uint128 rootExecutionGas)


Found in line 107 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol:

    function anyExecuteSettlement(bytes calldata data, SettlementParams memory sParams)


Found in line 117 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol:

    function anyExecuteSettlementMultiple(bytes calldata data, SettlementMultipleParams memory sParams)


Found in line 213 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function callOut(address _recipient, bytes memory _calldata, uint24 _toChain) external payable;


Found in line 268 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain) external;


Found in line 290 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function bridgeInMultiple(address _recipient, DepositMultipleParams memory _dParams, uint24 _fromChain) external;


Found in line 22 at 2023-05-maia/src/ulysses-omnichain/interfaces/IERC20hTokenBranchFactory.sol:

    function createToken(string memory _name, string memory _symbol) external returns (ERC20hTokenBranch newToken);


Found in line 94 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchPort.sol:

    function bridgeInMultiple(address _recipient, address[] memory _localAddresses, uint256[] memory _amounts)


Found in line 26 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

    function anyExecuteResponse(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)


Found in line 38 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

    function anyExecute(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)


Found in line 80 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

    function anyExecuteSigned(bytes1 funcId, bytes memory encodedData, address userAccount, uint24 fromChainId)


Found in line 22 at 2023-05-maia/src/ulysses-omnichain/interfaces/IERC20hTokenRootFactory.sol:

    function createToken(string memory _name, string memory _symbol) external returns (ERC20hTokenRoot newToken);


Found in line 178 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function callOutAndBridge(bytes calldata params, DepositInput memory dParams, uint128 remoteExecutionGas)


Found in line 213 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function callOutSignedAndBridge(bytes calldata params, DepositInput memory dParams, uint128 remoteExecutionGas)


Found in line 323 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function performCallOut(address depositor, bytes memory params, uint128 gasToBridgeOut, uint128 remoteExecutionGas)


Found in line 20 at 2023-05-maia/src/ulysses-omnichain/interfaces/IMulticall2.sol:

    function aggregate(Call[] memory calls) external returns (uint256 blockNumber, bytes[] memory returnData);


Found in line 54 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

    function createToken(string memory _name, string memory _symbol)


Found in line 60 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

    function createToken(string memory _name, string memory _symbol)


Found in line 169 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    function _queueRewards(address[] memory gauges, uint32 currentCycle, uint32 lastCycle, uint256 totalQueuedForCycle)


Found in line 97 at 2023-05-maia/src/gauges/interfaces/IBaseV2GaugeFactory.sol:

    function createGauge(address strategy, bytes memory data) external;


Found in line 76 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    function newGauge(address strategy, bytes memory data) internal override returns (BaseV2Gauge) {


Found in line 89 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    function afterCreateGauge(address strategy, bytes memory) internal override {


Found in line 109 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function createGauge(address strategy, bytes memory data) external onlyOwner {


Found in line 125 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function afterCreateGauge(address strategy, bytes memory data) internal virtual;


Found in line 127 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function newGauge(address strategy, bytes memory data) internal virtual returns (BaseV2Gauge gauge);


Found in line 66 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

    function receiveAssets(uint256[] memory assetsAmounts) private {


Found in line 77 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

    function sendAssets(uint256[] memory assetsAmounts, address receiver) private {


Found in line 292 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

    function beforeWithdraw(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}


Found in line 294 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

    function afterDeposit(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}


Found in line 157 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function createIncentive(IncentiveKey memory key, uint256 reward) external {


Found in line 187 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function endIncentive(IncentiveKey memory key) external returns (uint256 refund) {


Found in line 243 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function withdrawToken(uint256 tokenId, address to, bytes memory data) external {


Found in line 287 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function getRewardInfo(IncentiveKey memory key, uint256 tokenId)


Found in line 361 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function unstakeToken(IncentiveKey memory key, uint256 tokenId) external {


Found in line 365 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function _unstakeToken(IncentiveKey memory key, uint256 tokenId, bool isNotRestake) private {


Found in line 176 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    function createIncentive(IncentiveKey memory key, uint256 reward) external;


Found in line 185 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    function endIncentive(IncentiveKey memory key) external returns (uint256 refund);


Found in line 195 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    function withdrawToken(uint256 tokenId, address to, bytes memory data) external;


Found in line 217 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    function getRewardInfo(IncentiveKey memory key, uint256 tokenId)


Found in line 232 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    function unstakeToken(IncentiveKey memory key, uint256 tokenId) external;


Found in line 16 at 2023-05-maia/src/uni-v3-staker/libraries/IncentiveId.sol:

    function compute(IUniswapV3Staker.IncentiveKey memory key) internal pure returns (bytes32 incentiveId) {


Found in line 59 at 2023-05-maia/src/governance/GovernorBravoDelegator.sol:

    function delegateTo(address callee, bytes memory data) internal {


Found in line 178 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    function incrementGauges(address[] memory gaugeList, uint112[] memory weights)


Found in line 196 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    function decrementGauges(address[] memory gaugeList, uint112[] memory weights)

------------------------------------------------------------------------ 

### Mitigation 

Mark data types as calldata instead of memory where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as calldata. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies memory storage.










# VULN 7 

## [GAS] Cache array length outside of loop
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 95 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        for (uint256 i = 0; i < assets.length; i++) {


Found in line 111 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        for (uint256 i = 0; i < assets.length; i++) {


Found in line 130 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < bandwidthStateList.length; i++) {


Found in line 232 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < bandwidthStateList.length; i++) {


Found in line 253 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            for (uint256 i = 1; i < bandwidthStateList.length;) {


Found in line 278 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            for (uint256 i = 1; i < bandwidthStateList.length;) {


Found in line 296 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        for (uint256 i = 1; i < bandwidthStateList.length; i++) {


Found in line 146 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        for (uint256 i = 0; i < outputTokens.length;) {


Found in line 311 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            for (uint256 i = 0; i < outputParams.outputTokens.length;) {


Found in line 387 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            for (uint256 i = 0; i < outputParams.outputTokens.length;) {


Found in line 463 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            for (uint256 i = 0; i < outputParams.outputTokens.length;) {


Found in line 283 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {


Found in line 719 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length; i++) {


Found in line 951 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < deposit.hTokens.length;) {


Found in line 1354 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        for (uint256 i = 0; i < _deposits.length; i++) {


Found in line 342 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < _globalAddresses.length;) {


Found in line 408 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < _dParams.hTokens.length;) {


Found in line 561 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < newGas.length;) {


Found in line 596 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        for (uint256 i = 0; i < settlement.hTokens.length;) {


Found in line 101 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 136 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 231 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 267 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        for (uint256 i = 0; i < _localAddresses.length;) {


Found in line 48 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

        for (uint256 i = 0; i < calls.length; i++) {


Found in line 181 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 215 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {


Found in line 251 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        for (uint256 i = 0; i < proposal.targets.length; i++) {

------------------------------------------------------------------------ 

### Mitigation 

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).










# VULN 8 

## [GAS] Use assembly to check for address(0)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 33 at 2023-05-maia/src/ulysses-amm/UlyssesRouter.sol:

        if (address(ulysses) == address(0)) {


Found in line 36 at 2023-05-maia/src/ulysses-amm/UlyssesRouter.sol:

            if (address(ulysses) == address(0)) revert UnrecognizedUlyssesLP();


Found in line 84 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        if (address(asset) == address(0)) revert InvalidAsset();


Found in line 148 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

            if (destination == address(0)) revert InvalidPoolId();


Found in line 52 at 2023-05-maia/src/talos/factories/BoostAggregatorFactory.sol:

        if (owner == address(0)) revert InvalidOwner();


Found in line 253 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        if (receiver == address(0)) revert ReceiverIsZeroAddress();


Found in line 260 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (getSettlement[_depositNonce].status != SettlementStatus.Failed || depositOwner == address(0)) {


Found in line 301 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (localAddress == address(0) || (underlyingAddress == address(0) && _deposit > 0)) {


Found in line 347 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (hTokens[i] == address(0) || (tokens[i] == address(0) && _deposits[i] > 0)) revert InvalidInputParams();


Found in line 397 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (globalAddress == address(0)) revert InvalidInputParams();


Found in line 453 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (_localAddress == address(0)) revert UnrecognizedLocalAddress();


Found in line 459 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (_underlyingAddress == address(0)) revert UnrecognizedUnderlyingAddress();


Found in line 555 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (settlement.owner == address(0)) return false;


Found in line 665 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();


Found in line 709 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();


Found in line 778 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (callee == address(0)) revert UnrecognizedBridgeAgent();


Found in line 50 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        if (globalToken == address(0)) revert UnknownUnderlyingToken();


Found in line 68 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        if (underlyingAddress == address(0)) revert UnknownUnderlyingToken();


Found in line 100 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(coreBranchRouterAddress == address(0), "Contract already initialized");


Found in line 342 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        if (address(account) == address(0)) account = addVirtualAccount(_user);


Found in line 126 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

        if (block.timestamp >= _period + week && initializer == address(0)) {


Found in line 73 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

        if (address(flywheelTokens[bribeToken]) == address(0)) createBribeFlywheel(bribeToken);


Found in line 144 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (address(pool) == address(0)) revert IncentiveCallerMustBeRegisteredGauge();


Found in line 169 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (address(gauges[key.pool]) == address(0)) {


Found in line 244 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (to == address(0)) revert InvalidRecipient();


Found in line 492 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (tokenOwner == address(0)) revert TokenNotDeposited();


Found in line 529 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (uniswapV3Gauge == address(0)) revert InvalidGauge();


Found in line 63 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");


Found in line 411 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        if (gauge == address(0) || !(newAdd || previouslyDeprecated)) revert InvalidGaugeError();


Found in line 191 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        if (delegatee == address(0) || free < amount || amount == 0) revert DelegationError();


Found in line 267 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

        if (gauge == address(0) || !(newAdd || previouslyDeprecated)) revert InvalidGauge();

------------------------------------------------------------------------ 

### Mitigation 

Using assembly to check for the zero address can result in significant gas savings compared to using a Solidity expression, especially if the check is performed frequently or in a loop. However, it's important to note that using assembly can make the code less readable and harder to maintain, so it should be used judiciously and with caution.










# VULN 9 

## [GAS] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 159 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {


Found in line 223 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {


Found in line 60 at 2023-05-maia/src/ulysses-amm/interfaces/IUlyssesFactory.sol:

    function createPools(ERC20[] calldata assets, uint8[][] calldata weights, address owner)


Found in line 89 at 2023-05-maia/src/ulysses-amm/interfaces/IUlyssesPool.sol:

        uint8 weight;


Found in line 147 at 2023-05-maia/src/ulysses-amm/interfaces/IUlyssesPool.sol:

    function addNewBandwidth(uint256 poolId, uint8 weight) external returns (uint256 index);


Found in line 154 at 2023-05-maia/src/ulysses-amm/interfaces/IUlyssesPool.sol:

    function setWeight(uint256 poolId, uint8 weight) external;


Found in line 91 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

    function createPools(ERC20[] calldata assets, uint8[][] calldata weights, address owner)


Found in line 47 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    uint24 private constant protocolFee = 2 * 1e5; //20%


Found in line 48 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    uint24 private constant GLOBAL_DIVISIONER = 1e6;


Found in line 116 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        uint24 _protocolFee = protocolFee;


Found in line 117 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        uint24 _GLOBAL_DIVISIONER = GLOBAL_DIVISIONER;


Found in line 18 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    uint32 public override twapDuration;


Found in line 24 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    uint24 public override priceImpactPercentage;


Found in line 35 at 2023-05-maia/src/talos/TalosOptimizer.sol:

        uint32 _twapDuration,


Found in line 38 at 2023-05-maia/src/talos/TalosOptimizer.sol:

        uint24 _priceImpactPercentage,


Found in line 68 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setTwapDuration(uint32 _twapDuration) external onlyOwner {


Found in line 85 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {


Found in line 81 at 2023-05-maia/src/talos/interfaces/ITalosBaseStrategy.sol:

    function poolFee() external view returns (uint24);


Found in line 16 at 2023-05-maia/src/talos/interfaces/ITalosOptimizer.sol:

    function twapDuration() external view returns (uint32);


Found in line 26 at 2023-05-maia/src/talos/interfaces/ITalosOptimizer.sol:

    function priceImpactPercentage() external view returns (uint24);


Found in line 42 at 2023-05-maia/src/talos/interfaces/ITalosOptimizer.sol:

    function setTwapDuration(uint32 _twapDuration) external;


Found in line 60 at 2023-05-maia/src/talos/interfaces/ITalosOptimizer.sol:

    function setPriceImpact(uint24 _priceImpactPercentage) external;


Found in line 39 at 2023-05-maia/src/talos/interfaces/IOptimizerFactory.sol:

        uint32 _twapDuration,


Found in line 42 at 2023-05-maia/src/talos/interfaces/IOptimizerFactory.sol:

        uint24 _priceImpactPercentage,


Found in line 37 at 2023-05-maia/src/talos/factories/OptimizerFactory.sol:

        uint32 _twapDuration,


Found in line 40 at 2023-05-maia/src/talos/factories/OptimizerFactory.sol:

        uint24 _priceImpactPercentage,


Found in line 67 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    uint24 public immutable override poolFee;


Found in line 77 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    uint24 internal constant MULTIPLIER = 1e6;


Found in line 23 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)


Found in line 199 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

    function checkDeviation(IUniswapV3Pool pool, int24 maxTwapDeviation, uint32 twapDuration) public view {


Found in line 207 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

    function getTwap(IUniswapV3Pool pool, uint32 twapDuration) private view returns (int24) {


Found in line 208 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

        uint32 _twapDuration = twapDuration;


Found in line 209 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

        uint32[] memory secondsAgo = new uint32[](2);


Found in line 59 at 2023-05-maia/src/talos/libraries/PoolActions.sol:

        uint24 poolFee


Found in line 117 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        uint24 toChain


Found in line 143 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        uint24 toChain


Found in line 166 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function anyExecuteResponse(bytes1, bytes calldata, uint24)


Found in line 185 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function anyExecute(bytes1 funcId, bytes calldata encodedData, uint24)


Found in line 201 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (IMulticall.Call[] memory callData, OutputParams memory outputParams, uint24 toChain) =


Found in line 202 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (IMulticall.Call[], OutputParams, uint24));


Found in line 217 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (IMulticall.Call[] memory callData, OutputMultipleParams memory outputParams, uint24 toChain) =


Found in line 218 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (IMulticall.Call[], OutputMultipleParams, uint24));


Found in line 239 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function anyExecuteDepositSingle(bytes1, bytes calldata, DepositParams calldata, uint24)


Found in line 250 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function anyExecuteDepositMultiple(bytes1, bytes calldata, DepositMultipleParams calldata, uint24)


Found in line 268 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function anyExecuteSigned(bytes1 funcId, bytes calldata encodedData, address userAccount, uint24)


Found in line 285 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (Call[] memory calls, OutputParams memory outputParams, uint24 toChain) =


Found in line 286 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (Call[], OutputParams, uint24));


Found in line 305 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (Call[] memory calls, OutputMultipleParams memory outputParams, uint24 toChain) =


Found in line 306 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (Call[], OutputMultipleParams, uint24));


Found in line 350 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        uint24


Found in line 361 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (Call[] memory calls, OutputParams memory outputParams, uint24 toChain) =


Found in line 362 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (Call[], OutputParams, uint24));


Found in line 381 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (Call[] memory calls, OutputMultipleParams memory outputParams, uint24 toChain) =


Found in line 382 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (Call[], OutputMultipleParams, uint24));


Found in line 426 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        uint24


Found in line 437 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (Call[] memory calls, OutputParams memory outputParams, uint24 toChain) =


Found in line 438 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (Call[], OutputParams, uint24));


Found in line 457 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

            (Call[] memory calls, OutputMultipleParams memory outputParams, uint24 toChain) =


Found in line 458 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

                abi.decode(encodedData, (Call[], OutputMultipleParams, uint24));


Found in line 43 at 2023-05-maia/src/ulysses-omnichain/CoreBranchRouter.sol:

        uint24 _toChain,


Found in line 32 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_START = 1;


Found in line 34 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_START_SIGNED = 21;


Found in line 36 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_END_SIGNED_OFFSET = 26;


Found in line 38 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_ENTRY_SIZE = 32;


Found in line 40 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;


Found in line 42 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_TKN_SET_SIZE = 128;


Found in line 44 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_GAS_OUT = 16;


Found in line 48 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_TKN_START = 5;


Found in line 50 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_AMT_OFFSET = 64;


Found in line 52 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;


Found in line 95 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

            settlementNonce: uint32(bytes4(_data[PARAMS_START_SIGNED:25])),


Found in line 135 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

                        + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))


Found in line 144 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

                    + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * uint16(PARAMS_TKN_SET_SIZE))


Found in line 149 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol:

                    PARAMS_END_SIGNED_OFFSET + (uint8(bytes1(_data[PARAMS_START_SIGNED])) * PARAMS_TKN_SET_SIZE):


Found in line 68 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_START = 1;


Found in line 70 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_START_SIGNED = 21;


Found in line 72 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_ENTRY_SIZE = 32;


Found in line 74 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_GAS_OUT = 16;


Found in line 78 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_TKN_START = 5;


Found in line 80 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_AMT_OFFSET = 64;


Found in line 82 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;


Found in line 119 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint32 public depositNonce;


Found in line 122 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    mapping(uint32 => Deposit) public getDeposit;


Found in line 129 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    mapping(uint32 => bool) public executionHistory;


Found in line 174 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function getDepositEntry(uint32 _depositNonce) external view returns (Deposit memory) {


Found in line 291 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            uint8(_dParams.hTokens.length),


Found in line 321 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint32 _depositNonce,


Found in line 324 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint24 _toChain


Found in line 332 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {


Found in line 365 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {


Found in line 367 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            uint32 nonce = _depositNonce;


Found in line 373 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                    uint8(getDeposit[_depositNonce].hTokens.length),


Found in line 387 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                    uint8(getDeposit[nonce].hTokens.length),


Found in line 418 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function retrySettlement(uint32 _settlementNonce, uint128 _gasToBoostSettlement)


Found in line 433 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function retrieveDeposit(uint32 _depositNonce) external payable lock requiresFallbackGas {


Found in line 450 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function redeemDeposit(uint32 _depositNonce) external lock {


Found in line 571 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint8 numOfAssets = uint8(bytes1(_sParams[0]));


Found in line 572 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint32 nonce = uint32(bytes4(_sParams[PARAMS_START:PARAMS_TKN_START]));


Found in line 598 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                            PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:


Found in line 599 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)


Found in line 607 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                        PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):


Found in line 608 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                            PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets)


Found in line 609 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)


Found in line 616 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                        PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):


Found in line 617 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                            PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets)


Found in line 618 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)


Found in line 726 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            uint8(_dParams.hTokens.length),


Found in line 937 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _getAndIncrementDepositNonce() internal returns (uint32) {


Found in line 946 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _redeemDeposit(uint32 _depositNonce) internal {


Found in line 993 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _clearDeposit(uint32 _depositNonce) internal {


Found in line 1061 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _payFallbackGas(uint32 _depositNonce, uint256 _initialGas) internal virtual {


Found in line 1145 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1167 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1191 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[22:26]));


Found in line 1240 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint32 _depositNonce;


Found in line 1245 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            _depositNonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));


Found in line 1259 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            _depositNonce = uint32(bytes4(data[PARAMS_START + PARAMS_START:PARAMS_TKN_START + PARAMS_START]));


Found in line 1274 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            _depositNonce = uint32(bytes4(data[PARAMS_START_SIGNED:PARAMS_START_SIGNED + PARAMS_TKN_START]));


Found in line 1289 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

            _depositNonce = uint32(


Found in line 1340 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    function _normalizeDecimals(uint256 _amount, uint8 _decimals) internal pure returns (uint256) {


Found in line 33 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_START = 1;


Found in line 35 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_START_SIGNED = 21;


Found in line 37 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_END_OFFSET = 9;


Found in line 39 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_END_SIGNED_OFFSET = 29;


Found in line 41 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_ENTRY_SIZE = 32;


Found in line 43 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;


Found in line 45 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_TKN_SET_SIZE = 104;


Found in line 47 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_TKN_SET_SIZE_MULTIPLE = 128;


Found in line 49 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_GAS_IN = 32;


Found in line 51 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_GAS_OUT = 16;


Found in line 55 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_TKN_START = 5;


Found in line 57 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_AMT_OFFSET = 64;


Found in line 59 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;


Found in line 78 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeSystemRequest(address _router, bytes calldata _data, uint24 _fromChainId)


Found in line 98 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeNoDeposit(address _router, bytes calldata _data, uint24 _fromChainId)


Found in line 117 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeWithDeposit(address _router, bytes calldata _data, uint24 _fromChainId)


Found in line 124 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

            depositNonce: uint32(bytes4(_data[PARAMS_START:PARAMS_TKN_START])),


Found in line 129 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

            toChain: uint24(bytes3(_data[109:112]))


Found in line 154 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeWithDepositMultiple(address _router, bytes calldata _data, uint24 _fromChainId)


Found in line 164 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                    PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE


Found in line 169 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        uint8 numOfAssets = uint8(bytes1(_data[PARAMS_START]));


Found in line 174 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                > PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE


Found in line 178 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                bytes1(_data[PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE]),


Found in line 180 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                    PARAMS_START + PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE:


Found in line 201 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeSignedNoDeposit(address _account, address _router, bytes calldata _data, uint24 _fromChainId)


Found in line 221 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeSignedWithDeposit(address _account, address _router, bytes calldata _data, uint24 _fromChainId)


Found in line 228 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

            depositNonce: uint32(bytes4(_data[PARAMS_START_SIGNED:25])),


Found in line 233 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

            toChain: uint24(bytes3(_data[129:132]))


Found in line 263 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        uint24 _fromChainId


Found in line 271 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE


Found in line 280 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE


Found in line 285 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE],


Found in line 288 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                            + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE:


Found in line 308 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function executeRetrySettlement(uint32 _settlementNonce)


Found in line 325 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function _bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain) internal {


Found in line 350 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

    function _bridgeInMultiple(address _recipient, bytes calldata _dParams, uint24 _fromChain)


Found in line 355 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        uint8 numOfAssets = uint8(bytes1(_dParams[0]));


Found in line 356 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        uint32 nonce = uint32(bytes4(_dParams[PARAMS_START:5]));


Found in line 357 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        uint24 toChain = uint24(bytes3(_dParams[_dParams.length - 3:_dParams.length]));


Found in line 364 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

        for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {


Found in line 383 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                            PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(i + numOfAssets) + 12:


Found in line 384 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                                PARAMS_TKN_START + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i + numOfAssets)


Found in line 393 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                        PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):


Found in line 394 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                            PARAMS_TKN_START + PARAMS_AMT_OFFSET * uint16(numOfAssets)


Found in line 395 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)


Found in line 403 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                        PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets) + (PARAMS_ENTRY_SIZE * uint16(i)):


Found in line 404 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                            PARAMS_TKN_START + PARAMS_DEPOSIT_OFFSET * uint16(numOfAssets)


Found in line 405 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol:

                                + PARAMS_ENTRY_SIZE * uint16(PARAMS_START + i)


Found in line 50 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function checkParams(address _localPortAddress, DepositParams memory _dParams, uint24 _fromChain)


Found in line 70 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _localChainId,


Found in line 100 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_START = 1;


Found in line 102 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_START_SIGNED = 21;


Found in line 104 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_ADDRESS_SIZE = 20;


Found in line 106 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_GAS_IN = 32;


Found in line 108 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_GAS_OUT = 16;


Found in line 112 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_TKN_START = 5;


Found in line 114 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_AMT_OFFSET = 64;


Found in line 116 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint8 internal constant PARAMS_DEPOSIT_OFFSET = 96;


Found in line 123 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint24 public immutable localChainId;


Found in line 164 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint32 public settlementNonce;


Found in line 167 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    mapping(uint32 => Settlement) public getSettlement;


Found in line 174 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    mapping(uint256 => mapping(uint32 => bool)) public executionHistory;


Found in line 203 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _localChainId,


Found in line 235 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function getSettlementEntry(uint32 _settlementNonce) external view returns (Settlement memory) {


Found in line 244 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function retrySettlement(uint32 _settlementNonce, uint128 _remoteExecutionGas) external payable {


Found in line 255 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function redeemSettlement(uint32 _depositNonce) external lock {


Found in line 275 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function callOut(address _recipient, bytes memory _data, uint24 _toChain) external payable lock requiresRouter {


Found in line 292 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _toChain


Found in line 338 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _toChain


Found in line 362 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint8(hTokens.length),


Found in line 384 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain)


Found in line 404 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function bridgeInMultiple(address _recipient, DepositMultipleParams memory _dParams, uint24 _fromChain)


Found in line 449 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _toChain


Found in line 491 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _toChain


Found in line 528 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 _toChain


Found in line 550 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _retrySettlement(uint32 _settlementNonce) internal returns (bool) {


Found in line 591 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _redeemSettlement(uint32 _settlementNonce) internal {


Found in line 623 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _reopenSettlemment(uint32 _settlementNonce) internal {


Found in line 632 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _getAndIncrementSettlementNonce() internal returns (uint32) {


Found in line 640 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)


Found in line 659 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _gasSwapIn(uint256 _amount, uint24 _fromChain) internal returns (uint256) {


Found in line 661 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        (bool zeroForOneOnInflow, uint24 priceImpactPercentage, address gasTokenGlobalAddress, address poolAddress) =


Found in line 703 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _gasSwapOut(uint256 _amount, uint24 _toChain) internal returns (uint256, address) {


Found in line 705 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        (bool zeroForOneOnInflow, uint24 priceImpactPercentage, address gasTokenGlobalAddress, address poolAddress) =


Found in line 743 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _manageGasOut(uint24 _toChain) internal returns (uint128) {


Found in line 798 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _payExecutionGas(uint128 _depositedGas, uint128 _gasToBridgeOut, uint256 _initialGas, uint24 _fromChain)


Found in line 831 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function _payFallbackGas(uint32 _settlementNonce, uint256 _initialGas) internal virtual {


Found in line 869 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 fromChainId;


Found in line 916 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));


Found in line 941 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));


Found in line 966 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START:PARAMS_TKN_START]));


Found in line 990 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[2:6]));


Found in line 1014 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1050 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1085 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1120 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[1:5]));


Found in line 1130 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeRetrySettlement(uint32(bytes4(data[5:9])))


Found in line 1143 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            uint32 nonce = uint32(bytes4(data[1:5]));


Found in line 1146 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            if (!executionHistory[fromChainId][uint32(bytes4(data[1:5]))]) {


Found in line 1188 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint24 fromChainId = _fromChainId.toUint24();


Found in line 1194 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint32 _settlementNonce;


Found in line 1198 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1203 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));


Found in line 1208 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            _settlementNonce = uint32(bytes4(data[22:26]));


Found in line 1254 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    function syncBranchBridgeAgent(address _newBranchBridgeAgent, uint24 _branchChainId) external requiresPort {


Found in line 49 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function getDepositEntry(uint32 _depositNonce) external view returns (Deposit memory) {


Found in line 87 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function retrySettlement(uint32 _settlementNonce, uint128 _gasToBoostSettlement) external payable lock {


Found in line 92 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function redeemDeposit(uint32 _depositNonce) external lock {


Found in line 43 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    uint24 public immutable rootChainId;


Found in line 56 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    constructor(uint24 _rootChainId, address _wrappedNativeToken, address _rootPortAddress) {


Found in line 86 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        uint24 _toChain,


Found in line 125 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function _syncBranchBridgeAgent(address _newBranchBridgeAgent, address _rootBridgeAgent, uint24 _fromChain)


Found in line 143 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function _addGlobalToken(uint128 _remoteExecutionGas, address _globalAddress, address _gasReceiver, uint24 _toChain)


Found in line 183 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        uint24 _fromChain


Found in line 208 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function _setLocalToken(address _globalAddress, address _localAddress, uint24 _toChain) internal {


Found in line 231 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        uint24 _toChain


Found in line 253 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function removeBranchBridgeAgent(address _branchBridgeAgent, address _gasReceiver, uint24 _toChain)


Found in line 279 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        uint24 _toChain


Found in line 306 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        uint24 _toChain


Found in line 323 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteResponse(bytes1 _funcId, bytes calldata _encodedData, uint24 fromChainId)


Found in line 350 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecute(bytes1 _funcId, bytes calldata _encodedData, uint24 _fromChainId)


Found in line 359 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

            (address gasReceiver, address globalAddress, uint24 toChain, uint128 remoteExecutionGas) =


Found in line 360 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

                abi.decode(_encodedData, (address, address, uint24, uint128));


Found in line 379 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteDepositSingle(bytes1, bytes memory, DepositParams memory, uint24)


Found in line 390 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteDepositMultiple(bytes1, bytes calldata, DepositMultipleParams memory, uint24)


Found in line 400 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteSigned(bytes1, bytes memory, address, uint24)


Found in line 411 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteSignedDepositSingle(bytes1, bytes memory, DepositParams memory, address, uint24)


Found in line 422 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function anyExecuteSignedDepositMultiple(bytes1, bytes memory, DepositMultipleParams memory, address, uint24)


Found in line 174 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

    function _payFallbackGas(uint32, uint256) internal override {


Found in line 22 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

    uint24 public localChainId;


Found in line 33 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

    constructor(uint24 _localChainId, address _rootPortAddress, address _owner) BranchPort(_owner) {


Found in line 388 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    function _denormalizeDecimals(uint256 _amount, uint8 _decimals) internal pure returns (uint256) {


Found in line 28 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    uint24 public immutable localChainId;


Found in line 114 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    constructor(uint24 _localChainId, address _wrappedNativeToken) {


Found in line 174 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function getLocalToken(address _localAddress, uint24 _fromChain, uint24 _toChain) external view returns (address) {


Found in line 184 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function _getLocalToken(address _localAddress, uint256 _fromChain, uint24 _toChain)


Found in line 194 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function getUnderlyingTokenFromGlobal(address _globalAddress, uint24 _fromChain) external view returns (address) {


Found in line 203 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function _getUnderlyingTokenFromGlobal(address _globalAddress, uint24 _fromChain) internal view returns (address) {


Found in line 209 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function isGlobalToken(address _globalAddress, uint24 _fromChain) external view returns (bool) {


Found in line 218 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function _isGlobalToken(address _globalAddress, uint24 _fromChain) internal view returns (bool) {


Found in line 223 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function isLocalToken(address _localAddress, uint24 _fromChain) external view returns (bool) {


Found in line 228 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function isLocalToken(address _localAddress, uint24 _fromChain, uint24 _toChain) external view returns (bool) {


Found in line 233 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function _isLocalToken(address _localAddress, uint24 _fromChain, uint24 _toChain) internal view returns (bool) {


Found in line 238 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function isUnderlyingToken(address _underlyingToken, uint24 _fromChain) external view returns (bool) {


Found in line 247 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function setAddresses(address _globalAddress, address _localAddress, address _underlyingAddress, uint24 _fromChain)


Found in line 261 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function setLocalAddress(address _globalAddress, address _localAddress, uint24 _fromChain)


Found in line 276 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function bridgeToRoot(address _recipient, address _hToken, uint256 _amount, uint256 _deposit, uint24 _fromChainId)


Found in line 293 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function mint(address _to, address _hToken, uint256 _amount, uint24 _fromChain) internal {


Found in line 299 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function burn(address _from, address _hToken, uint256 _amount, uint24 _fromChain) external requiresBridgeAgent {


Found in line 381 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        uint24 _branchChainId


Found in line 424 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        uint24 _chainId,


Found in line 427 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        uint24 _fee,


Found in line 428 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        uint24 _priceImpactPercentage,


Found in line 457 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        uint24 chainId = _chainId;


Found in line 484 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external onlyOwner {


Found in line 16 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    uint24 priceImpactPercentage;


Found in line 77 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function getLocalToken(address _localAddress, uint24 _fromChain, uint24 _toChain) external view returns (address);


Found in line 91 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function getUnderlyingTokenFromGlobal(address _globalAddress, uint24 _fromChain) external view returns (address);


Found in line 98 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function isGlobalToken(address _globalAddress, uint24 _fromChain) external view returns (bool);


Found in line 105 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function isLocalToken(address _localAddress, uint24 _fromChain) external view returns (bool);


Found in line 108 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function isLocalToken(address _localAddress, uint24 _fromChain, uint24 _toChain) external view returns (bool);


Found in line 115 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function isUnderlyingToken(address _underlyingToken, uint24 _fromChain) external view returns (bool);


Found in line 126 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

            uint24 priceImpactPercentage,


Found in line 144 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function burn(address _from, address _hToken, uint256 _amount, uint24 _fromChain) external;


Found in line 154 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function bridgeToRoot(address _recipient, address _hToken, uint256 _amount, uint256 _deposit, uint24 _fromChainId)


Found in line 199 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function setAddresses(address _globalAddress, address _localAddress, address _underlyingAddress, uint24 _fromChain)


Found in line 208 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function setLocalAddress(address _globalAddress, address _localAddress, uint24 _fromChain) external;


Found in line 237 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function syncBranchBridgeAgentWithRoot(address _newBranchBridgeAgent, address _rootBridgeAgent, uint24 _fromChain)


Found in line 284 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

        uint24 _chainId,


Found in line 287 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

        uint24 _fee,


Found in line 288 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

        uint24 _priceImpactPercentage,


Found in line 300 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external;


Found in line 317 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    event BridgeAgentSynced(address indexed bridgeAgent, address indexed rootBridgeAgent, uint24 indexed fromChain);


Found in line 319 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    event NewChainAdded(uint24 indexed chainId);


Found in line 320 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    event GasPoolInfoSet(uint24 indexed chainId, GasPoolInfo gasPoolInfo);


Found in line 325 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

        address indexed underlyingAddress, address localAddress, address globalAddress, uint24 chainId


Found in line 327 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootPort.sol:

    event GlobalTokenAdded(address indexed localAddress, address indexed globalAddress, uint24 chainId);


Found in line 76 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol:

    function retrySettlement(uint32 _settlementNonce, uint128 _gasToBoostSettlement) external payable;


Found in line 83 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol:

    function redeemDeposit(uint32 _depositNonce) external;


Found in line 90 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol:

    function getDepositEntry(uint32 _depositNonce) external view returns (Deposit memory);


Found in line 22 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint24 priceImpactPercentage; //Price impact percentage


Found in line 32 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 45 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint32 settlementNonce; //Settlement nonce.


Found in line 54 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint8 numberOfAssets; //Number of assets to deposit.


Found in line 55 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint32 settlementNonce; //Settlement nonce.


Found in line 65 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint32 depositNonce; //Deposit nonce.


Found in line 70 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 75 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint8 numberOfAssets; //Number of assets to deposit.


Found in line 76 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint32 depositNonce; //Deposit nonce.


Found in line 81 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 213 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function callOut(address _recipient, bytes memory _calldata, uint24 _toChain) external payable;


Found in line 233 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

        uint24 _toChain


Found in line 255 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

        uint24 _toChain


Found in line 268 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain) external;


Found in line 290 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function bridgeInMultiple(address _recipient, DepositMultipleParams memory _dParams, uint24 _fromChain) external;


Found in line 301 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function settlementNonce() external view returns (uint32 nonce);


Found in line 308 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function redeemSettlement(uint32 _depositNonce) external;


Found in line 316 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function retrySettlement(uint32 _settlementNonce, uint128 _remoteExecutionGas) external payable;


Found in line 323 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function getSettlementEntry(uint32 _settlementNonce) external view returns (Settlement memory);


Found in line 330 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    function syncBranchBridgeAgent(address _newBranchBridgeAgent, uint24 _branchChainId) external;


Found in line 378 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    event LogCallin(bytes1 selector, bytes data, uint24 fromChainId);


Found in line 379 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    event LogCallout(bytes1 selector, bytes data, uint256, uint24 toChainId);


Found in line 380 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol:

    event LogCalloutFail(bytes1 selector, bytes data, uint24 toChainId);


Found in line 26 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

    function anyExecuteResponse(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)


Found in line 38 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

    function anyExecute(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)


Found in line 55 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

        uint24 fromChainId


Found in line 70 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

        uint24 fromChainId


Found in line 80 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

    function anyExecuteSigned(bytes1 funcId, bytes memory encodedData, address userAccount, uint24 fromChainId)


Found in line 98 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

        uint24 fromChainId


Found in line 114 at 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol:

        uint24 fromChainId


Found in line 32 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 41 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 46 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint32 depositNonce; //Deposit nonce.


Found in line 51 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 57 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint8 numberOfAssets; //Number of assets to deposit.


Found in line 58 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint32 depositNonce; //Deposit nonce.


Found in line 63 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint24 toChain; //Destination chain for interaction.


Found in line 68 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint32 settlementNonce;


Found in line 77 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint8 numberOfAssets; //Number of assets to deposit.


Found in line 79 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    uint32 settlementNonce;


Found in line 155 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function getDepositEntry(uint32 _depositNonce) external view returns (Deposit memory);


Found in line 241 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

        uint32 _depositNonce,


Found in line 244 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

        uint24 _toChain


Found in line 254 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function retrySettlement(uint32 _settlementNonce, uint128 _gasToBoostSettlement) external payable;


Found in line 262 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function retrieveDeposit(uint32 _depositNonce) external payable;


Found in line 269 at 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol:

    function redeemDeposit(uint32 _depositNonce) external;


Found in line 13 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

    uint24 public immutable localChainId;


Found in line 27 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

    constructor(uint24 _localChainId, address _localPortAddress) {


Found in line 20 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    uint24 public immutable rootChainId;


Found in line 49 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        uint24 _rootChainId,


Found in line 42 at 2023-05-maia/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol:

    event NewRewardsCycle(uint32 indexed start, uint256 indexed end, uint256 reward);


Found in line 39 at 2023-05-maia/src/rewards/interfaces/IFlywheelGaugeRewards.sol:

        uint32 storedCycle;


Found in line 53 at 2023-05-maia/src/rewards/interfaces/IFlywheelGaugeRewards.sol:

    function gaugeCycle() external view returns (uint32);


Found in line 56 at 2023-05-maia/src/rewards/interfaces/IFlywheelGaugeRewards.sol:

    function gaugeCycleLength() external view returns (uint32);


Found in line 62 at 2023-05-maia/src/rewards/interfaces/IFlywheelGaugeRewards.sol:

        returns (uint112 priorCycleRewards, uint112 cycleRewards, uint32 storedCycle);


Found in line 93 at 2023-05-maia/src/rewards/interfaces/IFlywheelGaugeRewards.sol:

    event CycleStart(uint32 indexed cycleStart, uint256 rewardAmount);


Found in line 96 at 2023-05-maia/src/rewards/interfaces/IFlywheelGaugeRewards.sol:

    event QueueRewards(address indexed gauge, uint32 indexed cycleStart, uint256 rewardAmount);


Found in line 43 at 2023-05-maia/src/rewards/rewards/FlywheelAcummulatedRewards.sol:

        uint32 timestamp = block.timestamp.toUint32();


Found in line 36 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    uint32 public override gaugeCycle;


Found in line 39 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    uint32 public immutable override gaugeCycleLength;


Found in line 45 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    uint32 internal nextCycle;


Found in line 51 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    uint32 internal paginationOffset;


Found in line 79 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;


Found in line 80 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 lastCycle = gaugeCycle;


Found in line 114 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;


Found in line 115 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 lastCycle = gaugeCycle;


Found in line 125 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 offset = paginationOffset;


Found in line 169 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

    function _queueRewards(address[] memory gauges, uint32 currentCycle, uint32 lastCycle, uint256 totalQueuedForCycle)


Found in line 206 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint32 cycle = gaugeCycle;


Found in line 55 at 2023-05-maia/src/hermes/bHermes.sol:

    constructor(ERC20 _hermes, address _owner, uint32 _gaugeCycleLength, uint32 _incrementFreezeWindow)


Found in line 26 at 2023-05-maia/src/hermes/tokens/bHermesGauges.sol:

    constructor(address _owner, uint32 _rewardsCycleLength, uint32 _incrementFreezeWindow)


Found in line 23 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

    uint24 public override minimumWidth;


Found in line 37 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

        uint24 _minimumWidth,


Found in line 62 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

    function setMinimumWidth(uint24 _minimumWidth) external onlyOwner {


Found in line 61 at 2023-05-maia/src/gauges/interfaces/IUniswapV3Gauge.sol:

    function minimumWidth() external view returns (uint24);


Found in line 68 at 2023-05-maia/src/gauges/interfaces/IUniswapV3Gauge.sol:

    event NewMinimumWidth(uint24 minimumWidth);


Found in line 76 at 2023-05-maia/src/gauges/interfaces/IUniswapV3Gauge.sol:

    function setMinimumWidth(uint24 _minimumWidth) external;


Found in line 60 at 2023-05-maia/src/gauges/interfaces/IUniswapV3GaugeFactory.sol:

    function setMinimumWidth(address gauge, uint24 minimumWidth) external;


Found in line 77 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

        uint24 minimumWidth = abi.decode(data, (uint24));


Found in line 98 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {


Found in line 43 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    mapping(IUniswapV3Pool => uint24) public poolsMinimumWidth;


Found in line 485 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        if (uint24(tickUpper - tickLower) < poolsMinimumWidth[pool]) revert RangeTooSmallError();


Found in line 554 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        uint24 minimumWidth = gauges[uniswapV3Pool].minimumWidth();


Found in line 114 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    function poolsMinimumWidth(IUniswapV3Pool) external view returns (uint24);


Found in line 301 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

    event PoolMinimumWidthUpdated(IUniswapV3Pool indexed uniswapV3Pool, uint24 indexed poolMinimumWidth);


Found in line 28 at 2023-05-maia/src/uni-v3-staker/libraries/NFTPositionInfo.sol:

        uint24 fee;


Found in line 24 at 2023-05-maia/src/governance/GovernorBravoInterfaces.sol:

    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);


Found in line 143 at 2023-05-maia/src/governance/GovernorBravoInterfaces.sol:

        uint8 support;


Found in line 46 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");


Found in line 326 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    function castVote(uint256 proposalId, uint8 support) external {


Found in line 336 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    function castVoteWithReason(uint256 proposalId, uint8 support, string calldata reason) external {


Found in line 344 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    function castVoteBySig(uint256 proposalId, uint8 support, uint8 v, bytes32 r, bytes32 s) external {


Found in line 361 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    function castVoteInternal(address voter, uint256 proposalId, uint8 support) internal returns (uint96) {


Found in line 28 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    constructor(uint32 _gaugeCycleLength, uint32 _incrementFreezeWindow) {


Found in line 39 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    uint32 public immutable override gaugeCycleLength;


Found in line 42 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    uint32 public immutable override incrementFreezeWindow;


Found in line 70 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function getGaugeCycleEnd() external view returns (uint32) {


Found in line 74 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function _getGaugeCycleEnd() internal view returns (uint32) {


Found in line 75 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 nowPlusOneCycle = block.timestamp.toUint32() + gaugeCycleLength;


Found in line 92 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function _getStoredWeight(Weight storage gaugeWeight, uint32 currentCycle) internal view returns (uint112) {


Found in line 176 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 189 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 202 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function _incrementGaugeWeight(address user, address gauge, uint112 weight, uint32 cycle) internal {


Found in line 229 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function _incrementUserAndGlobalWeights(address user, uint112 weight, uint32 cycle)


Found in line 256 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 274 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 292 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function _decrementGaugeWeight(address user, address gauge, uint112 weight, uint32 cycle) internal {


Found in line 334 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 367 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 cycle


Found in line 413 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 437 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 525 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        uint32 currentCycle = _getGaugeCycleEnd();


Found in line 32 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function checkpoints(address account, uint32 pos) public view virtual returns (Checkpoint memory) {


Found in line 37 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function numCheckpoints(address account) public view virtual returns (uint32) {


Found in line 363 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function delegateBySig(address delegatee, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s) public {


Found in line 20 at 2023-05-maia/src/erc-20/interfaces/IERC20MultiVotes.sol:

        uint32 fromBlock;


Found in line 30 at 2023-05-maia/src/erc-20/interfaces/IERC20MultiVotes.sol:

    function checkpoints(address account, uint32 pos) external view returns (Checkpoint memory);


Found in line 35 at 2023-05-maia/src/erc-20/interfaces/IERC20MultiVotes.sol:

    function numCheckpoints(address account) external view returns (uint32);


Found in line 41 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

        uint32 currentCycle;


Found in line 55 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    function gaugeCycleLength() external view returns (uint32);


Found in line 60 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    function incrementFreezeWindow() external view returns (uint32);


Found in line 74 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    function getGaugeCycleEnd() external view returns (uint32);


Found in line 246 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    event IncrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);


Found in line 249 at 2023-05-maia/src/erc-20/interfaces/IERC20Gauges.sol:

    event DecrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);

------------------------------------------------------------------------ 

### Mitigation 

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.










# VULN 10 

## [GAS] Public Functions to external
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 39 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    function totalAssets() public view override returns (uint256 _totalAssets) {


Found in line 102 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function totalAssets() public view override returns (uint256) {


Found in line 107 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function maxRedeem(address owner) public view override returns (uint256) {


Found in line 65 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

    function renounceOwnership() public payable override onlyOwner {


Found in line 89 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

    function transfer(address _to, uint256 _amount) public override returns (bool) {


Found in line 94 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

    function transferFrom(address _from, address _to, uint256 _amount) public override returns (bool) {


Found in line 113 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    function renounceOwnership() public payable override onlyOwner {


Found in line 165 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function renounceOwnership() public payable override onlyOwner {


Found in line 29 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenBranch.sol:

    function burn(uint256 value) public override onlyOwner {


Found in line 115 at 2023-05-maia/src/hermes/bHermes.sol:

    function totalAssets() public view virtual override returns (uint256) {


Found in line 140 at 2023-05-maia/src/hermes/bHermes.sol:

    function transfer(address to, uint256 amount) public virtual override returns (bool) {


Found in line 158 at 2023-05-maia/src/hermes/bHermes.sol:

    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {


Found in line 91 at 2023-05-maia/src/maia/vMaia.sol:

    function claimBoost(uint256 amount) public override {}


Found in line 52 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function forfeitMultiple(uint256 amount) public virtual override {


Found in line 71 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function forfeitWeight(uint256 amount) public virtual override {


Found in line 81 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function forfeitBoost(uint256 amount) public virtual override {


Found in line 91 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function forfeitGovernance(uint256 amount) public virtual override {


Found in line 108 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function claimMultiple(uint256 amount) public virtual override {


Found in line 127 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function claimWeight(uint256 amount) public virtual override checkWeight(amount) {


Found in line 138 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function claimBoost(uint256 amount) public virtual override checkBoost(amount) {


Found in line 147 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    function claimGovernance(uint256 amount) public virtual override checkGovernance(amount) {


Found in line 105 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function totalAssets() public view override returns (uint256) {


Found in line 113 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function convertToShares(uint256 assets) public view virtual override returns (uint256) {


Found in line 121 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function convertToAssets(uint256 shares) public view virtual override returns (uint256) {


Found in line 129 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function previewDeposit(uint256 assets) public view virtual override returns (uint256) {


Found in line 136 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function previewMint(uint256 shares) public view virtual override returns (uint256) {


Found in line 143 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function previewWithdraw(uint256 assets) public view virtual override returns (uint256) {


Found in line 151 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function previewRedeem(uint256 shares) public view virtual override returns (uint256) {


Found in line 161 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function maxDeposit(address) public view virtual override returns (uint256) {


Found in line 167 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function maxMint(address) public view virtual override returns (uint256) {


Found in line 173 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function maxWithdraw(address user) public view virtual override returns (uint256) {


Found in line 179 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function maxRedeem(address user) public view virtual override returns (uint256) {


Found in line 169 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function userUnusedVotes(address user) public view override returns (uint256) {


Found in line 496 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function transfer(address to, uint256 amount) public virtual override returns (bool) {


Found in line 508 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {


Found in line 291 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function transfer(address to, uint256 amount) public virtual override returns (bool) {


Found in line 303 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {


Found in line 312 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

    function transfer(address to, uint256 amount) public override notAttached(msg.sender, amount) returns (bool) {

------------------------------------------------------------------------ 

### Mitigation 

The following functions could be set external to save gas and improve code quality. External call cost is less expensive than of public functions.










# VULN 11 

## [GAS] <x> += <y> Costs More Gas Than <x> = <x> + <y> For State Variables
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 52 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        totalWeights += _weight;


Found in line 96 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

            newTotalWeights += _weights[i];


Found in line 133 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            assets += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);


Found in line 135 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            assets += bandwidthStateList[i].bandwidth;


Found in line 178 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);


Found in line 194 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                newBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;


Found in line 214 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);


Found in line 235 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            oldRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);


Found in line 260 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                        leftOverBandwidth += oldBandwidth - bandwidthStateList[i].bandwidth;


Found in line 269 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            poolState.bandwidth += leftOverBandwidth.toUint248();


Found in line 275 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                leftOverBandwidth += oldBandwidth - poolState.bandwidth;


Found in line 281 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                        bandwidthStateList[i].bandwidth += leftOverBandwidth.toUint248();


Found in line 283 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

                        bandwidthStateList[i].bandwidth +=


Found in line 299 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            newRebalancingFee += _calculateRebalancingFee(bandwidthStateList[i].bandwidth, targetBandwidth, false);


Found in line 122 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        protocolFees0 += earnedProtocolFees0;


Found in line 123 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        protocolFees1 += earnedProtocolFees1;


Found in line 151 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

            liquidity += liquidityDifference;


Found in line 157 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

            protocolFees0 += collect0;


Found in line 158 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

            protocolFees1 += collect1;


Found in line 121 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

            protocolRewards += newProtocolRewards;


Found in line 216 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        liquidity += liquidityDifference;


Found in line 823 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        accumulatedFees += availableGas - minExecCost;


Found in line 163 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        getStrategyTokenDebt[_token] += _amount;


Found in line 164 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        getPortStrategyTokenDebt[msg.sender][_token] += _amount;


Found in line 73 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

        getTokenBalance[chainId] += amount;


Found in line 93 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        totalQueuedForCycle += nextCycleQueuedRewards;


Found in line 134 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            nextCycleQueuedRewards += uint112(newRewards); // in case a previous incomplete cycle had rewards, add on


Found in line 224 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            accruedRewards += cycleRewardsNext;


Found in line 175 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

            rewardsIndex += deltaIndex;


Found in line 112 at 2023-05-maia/src/hermes/UtilityManager.sol:

        userClaimedWeight[msg.sender] += amount;


Found in line 121 at 2023-05-maia/src/hermes/UtilityManager.sol:

        userClaimedBoost[msg.sender] += amount;


Found in line 130 at 2023-05-maia/src/hermes/UtilityManager.sol:

        userClaimedGovernance[msg.sender] += amount;


Found in line 130 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

            weekly += newWeeklyEmission;


Found in line 137 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

            _required += share;


Found in line 159 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        userClaimedPartnerGovernance[msg.sender] += amount;


Found in line 54 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

            _totalWeights += _weights[i];


Found in line 149 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        incentives[incentiveId].totalRewardUnclaimed += reward;


Found in line 175 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        incentives[incentiveId].totalRewardUnclaimed += reward;


Found in line 442 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

            incentive.totalSecondsClaimedX128 += secondsInsideX128;


Found in line 446 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

            rewards[owner] += reward;


Found in line 450 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

            tokenIdRewards[tokenId] += reward;


Found in line 215 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        getUserGaugeWeight[user][gauge] += weight;


Found in line 262 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

            weightsSum += weight;


Found in line 341 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

            weightsSum += weight;


Found in line 342 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

            if (!_deprecatedGauges.contains(gauge)) globalWeightsSum += weight;


Found in line 542 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                    totalFreed += userGaugeWeight;


Found in line 544 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

                userFreed += userGaugeWeight;


Found in line 199 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        _delegatesVotesCount[delegator][delegatee] += amount;


Found in line 200 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        userDelegatedVotes[delegator] += amount;


Found in line 335 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

                totalFreed += votesToFree;

------------------------------------------------------------------------ 

### Mitigation 

When you use the += operator on a state variable, the EVM has to perform three operations: load the current value of the state variable, add the new value to it, and then store the result back in the state variable. On the other hand, when you use the = operator and then add the values separately, the EVM only needs to perform two operations: load the current value of the state variable and add the new value to it. Better use <x> = <x> + <y> For State Variables.










# VULN 12 

## [GAS] Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 33 and 34 at 2023-05-maia/src/hermes/UtilityManager.sol:

    /// @inheritdoc IUtilityManager

    mapping(address => uint256) public userClaimedBoost;

------------------------------------------------------------------------ 

### Mitigation 

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.










# VULN 13 

## [GAS] Use hardcode address instead address(this)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 74 at 2023-05-maia/src/ulysses-amm/UlyssesRouter.sol:

        address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount);


Found in line 84 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        asset.safeTransfer(msg.sender, asset.balanceOf(address(this)));


Found in line 112 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

            uint256 assetBalance = assets[i].balanceOf(address(this));


Found in line 118 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

                assets[i].safeTransferFrom(msg.sender, address(this), newAssetBalance - assetBalance);


Found in line 103 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        return asset.balanceOf(address(this)) - getProtocolFees();


Found in line 108 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        return balanceOf[owner].min(asset.balanceOf(address(this)));


Found in line 127 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        uint256 balance = asset.balanceOf(address(this));


Found in line 218 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);


Found in line 303 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

            asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);


Found in line 1109 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        asset.safeTransferFrom(msg.sender, address(this), assets);


Found in line 87 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

            UlyssesPoolDeployer.deployPool(_poolId, address(asset), "Ulysses Pool", "ULP", owner, address(this));


Found in line 100 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

            poolIds[i] = _createPool(assets[i], address(this));


Found in line 110 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

                recipient: address(this),


Found in line 130 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        uint256 balance0 = token0.balanceOf(address(this)) - protocolFees0;


Found in line 131 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

        uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1;


Found in line 90 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

        flywheel.accrue(ERC20(address(this)), msg.sender, _to);


Found in line 95 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

        flywheel.accrue(ERC20(address(this)), _from, _to);


Found in line 150 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

                    recipient: address(this),


Found in line 177 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

        try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId) {


Found in line 90 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

        nonfungiblePositionManager.safeTransferFrom(address(this), address(uniswapV3Staker), tokenId);


Found in line 168 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

        address(hermesGaugeBoost).safeTransfer(to, hermesGaugeBoost.balanceOf(address(this)));


Found in line 175 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

        hermesGaugeBoost.updateUserBoost(address(this));


Found in line 51 at 2023-05-maia/src/talos/factories/TalosStrategyStakedFactory.sol:

            address(this)


Found in line 125 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);


Found in line 126 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);


Found in line 146 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

                recipient: address(this),


Found in line 196 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);


Found in line 197 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);


Found in line 366 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

                recipient: address(this),


Found in line 406 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        uint256 balance0 = _token0.balanceOf(address(this));


Found in line 407 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        uint256 balance1 = _token1.balanceOf(address(this));


Found in line 227 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

        cache.amount0Desired = _token0.balanceOf(address(this));


Found in line 228 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

        cache.amount1Desired = _token1.balanceOf(address(this));


Found in line 47 at 2023-05-maia/src/talos/libraries/PoolActions.sol:

            address(this),


Found in line 84 at 2023-05-maia/src/talos/libraries/PoolActions.sol:

                recipient: address(this),


Found in line 98 at 2023-05-maia/src/talos/libraries/PoolActions.sol:

        balance0 = token0.balanceOf(address(this));


Found in line 99 at 2023-05-maia/src/talos/libraries/PoolActions.sol:

        balance1 = token1.balanceOf(address(this));


Found in line 111 at 2023-05-maia/src/ulysses-omnichain/CoreBranchRouter.sol:

        IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _rootExecutionGas, 0);


Found in line 154 at 2023-05-maia/src/ulysses-omnichain/CoreBranchRouter.sol:

        IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _remoteExecutionGas, 0);


Found in line 1020 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));


Found in line 1078 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);


Found in line 1093 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));


Found in line 1103 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);


Found in line 1325 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        uint256 executionBudget = anycallConfig.executionBudget(address(this));


Found in line 104 at 2023-05-maia/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol:

        IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, 0, 0);


Found in line 224 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        bridgeAgentExecutorAddress = DeployRootBridgeAgentExecutor.deploy(address(this));


Found in line 668 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        IPort(localPortAddress).bridgeToRoot(address(this), gasTokenGlobalAddress, _amount, _amount, _fromChain);


Found in line 685 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            address(this),


Found in line 729 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

            address(this),


Found in line 766 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        IPort(localPortAddress).burn(address(this), gasToken, amountOut, _toChain);


Found in line 851 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));


Found in line 1237 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        uint256 executionBudget = anycallConfig.executionBudget(address(this));


Found in line 1328 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (msg.sender != IPort(localPortAddress).getBridgeAgentManager(address(this))) {


Found in line 158 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

        uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));


Found in line 52 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        _underlyingAddress.safeTransferFrom(_depositor, address(this), _deposit);


Found in line 120 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())


Found in line 140 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

                    address(this),


Found in line 127 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        uint256 currBalance = ERC20(_token).balanceOf(address(this));


Found in line 138 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        uint256 currBalance = ERC20(_token).balanceOf(address(this));


Found in line 180 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);


Found in line 249 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

            _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);


Found in line 254 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

                _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())


Found in line 271 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

                    address(this),


Found in line 276 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

                _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);


Found in line 311 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        _hToken.safeTransferFrom(_from, address(this), _amount);


Found in line 350 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        newAccount = new VirtualAccount(_user, address(this));


Found in line 37 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

        ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);


Found in line 61 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

            address(this),


Found in line 20 at 2023-05-maia/src/rewards/depots/RewardsDepot.sol:

        balance = _asset.balanceOf(address(this));


Found in line 88 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        uint256 balanceBefore = rewardToken.balanceOf(address(this));


Found in line 90 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);


Found in line 130 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            uint256 balanceBefore = rewardToken.balanceOf(address(this));


Found in line 132 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);


Found in line 72 at 2023-05-maia/src/hermes/UtilityManager.sol:

        address(gaugeWeight).safeTransferFrom(msg.sender, address(this), amount);


Found in line 81 at 2023-05-maia/src/hermes/UtilityManager.sol:

        address(gaugeBoost).safeTransferFrom(msg.sender, address(this), amount);


Found in line 90 at 2023-05-maia/src/hermes/UtilityManager.sol:

        address(governance).safeTransferFrom(msg.sender, address(this), amount);


Found in line 116 at 2023-05-maia/src/hermes/bHermes.sol:

        return address(asset).balanceOf(address(this));


Found in line 129 at 2023-05-maia/src/hermes/bHermes.sol:

        gaugeWeight.mint(address(this), amount);


Found in line 130 at 2023-05-maia/src/hermes/bHermes.sol:

        gaugeBoost.mint(address(this), amount);


Found in line 131 at 2023-05-maia/src/hermes/bHermes.sol:

        governance.mint(address(this), amount);


Found in line 138 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

            uint256 _balanceOf = underlying.balanceOf(address(this));


Found in line 140 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

                HERMES(underlying).mint(address(this), _required - _balanceOf);


Found in line 75 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0) {


Found in line 85 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) {


Found in line 95 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) {


Found in line 104 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);


Found in line 128 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        uint256 weightAvailable = address(gaugeWeight).balanceOf(address(this));


Found in line 139 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        uint256 boostAvailable = address(gaugeBoost).balanceOf(address(this));


Found in line 148 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

        uint256 governanceAvailable = address(governance).balanceOf(address(this));


Found in line 162 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;


Found in line 168 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;


Found in line 208 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        emit MigratePartnerVault(address(this), newPartnerVault);


Found in line 219 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) {


Found in line 226 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

            address(this), totalSupply * newRate - address(partnerGovernance).balanceOf(address(this))


Found in line 244 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

        ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate);


Found in line 70 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

        multiRewardsDepot = new MultiRewardsDepot(address(this));


Found in line 115 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

            if (isActive[_bribeFlywheels[i]]) _bribeFlywheels[i].accrue(ERC20(address(this)), user);


Found in line 86 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

            address(this)


Found in line 83 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

                address(this)


Found in line 36 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        asset.safeTransferFrom(msg.sender, address(this), assets);


Found in line 52 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        asset.safeTransferFrom(msg.sender, address(this), assets);


Found in line 37 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

        address(asset).safeTransferFrom(msg.sender, address(this), assets);


Found in line 51 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

        address(asset).safeTransferFrom(msg.sender, address(this), assets);


Found in line 69 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

            assets[i].safeTransferFrom(msg.sender, address(this), assetsAmounts[i]);


Found in line 37 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        address(asset).safeTransferFrom(msg.sender, address(this), assets);


Found in line 51 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        address(asset).safeTransferFrom(msg.sender, address(this), assets);


Found in line 151 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        hermes.safeTransferFrom(msg.sender, address(this), reward);


Found in line 177 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        hermes.safeTransferFrom(msg.sender, address(this), reward);


Found in line 254 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        nonfungiblePositionManager.safeTransferFrom(address(this), to, tokenId, data);


Found in line 346 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

            keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

------------------------------------------------------------------------ 

### Mitigation 

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address. Foundry’s script.sol and solmate’s LibRlp.sol contracts can help achieve this.










# VULN 14 

## [GAS] Functions guaranteed to revert when called by normal users can be marked payable
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 44 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {


Found in line 60 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    function removeAsset(address asset) external nonReentrant onlyOwner {


Found in line 88 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {


Found in line 159 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {


Found in line 223 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {


Found in line 308 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    function setFees(Fees calldata _fees) external nonReentrant onlyOwner {


Found in line 62 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {


Found in line 68 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setTwapDuration(uint32 _twapDuration) external onlyOwner {


Found in line 74 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {


Found in line 80 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {


Found in line 85 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {


Found in line 143 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function addWhitelistedAddress(address user) external onlyOwner {


Found in line 148 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function removeWhitelistedAddress(address user) external onlyOwner {


Found in line 153 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function setProtocolFee(uint256 _protocolFee) external onlyOwner {


Found in line 159 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function withdrawProtocolFees(address to) external onlyOwner {


Found in line 165 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function withdrawAllGaugeBoost(address to) external onlyOwner {


Found in line 172 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function withdrawGaugeBoost(address to, uint256 amount) external onlyOwner {


Found in line 180 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function decrementGaugesBoostIndexed(uint256 boost, uint256 offset, uint256 num) external onlyOwner {


Found in line 394 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {


Found in line 74 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    function initialize(address _bridgeAgentAddress) external onlyOwner {


Found in line 37 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

    function initialize(address _localBridgeAgentAddress) external onlyOwner {


Found in line 63 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

    function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {


Found in line 99 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {


Found in line 128 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {


Found in line 158 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function forefeitOwnership(address _owner) external onlyOwner {


Found in line 399 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function toggleBridgeAgent(address _bridgeAgent) external onlyOwner {


Found in line 406 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function addBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {


Found in line 413 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function toggleBridgeAgentFactory(address _bridgeAgentFactory) external onlyOwner {


Found in line 484 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function setGasPoolInfo(uint24 _chainId, GasPoolInfo calldata _gasPoolInfo) external onlyOwner {


Found in line 491 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

    function addEcosystemToken(address _ecoTokenGlobalAddress) external onlyOwner {


Found in line 23 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenBranch.sol:

    function mint(address account, uint256 amount) external override onlyOwner returns (bool) {


Found in line 54 at 2023-05-maia/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol:

    function initialize(address _coreRootBridgeAgent) external override onlyOwner {


Found in line 40 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

    function initialize(address _coreRouter) external onlyOwner {


Found in line 83 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {


Found in line 35 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

    function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {


Found in line 47 at 2023-05-maia/src/rewards/depots/MultiRewardsDepot.sol:

    function addAsset(address rewardsContract, address asset) external onlyOwner {


Found in line 57 at 2023-05-maia/src/rewards/depots/MultiRewardsDepot.sol:

    function removeAsset(address rewardsContract) external onlyOwner {


Found in line 111 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

    function addStrategyForRewards(ERC20 strategy) external onlyOwner {


Found in line 125 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

    function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {


Found in line 137 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

    function setBooster(IFlywheelBooster newBooster) external onlyOwner {


Found in line 62 at 2023-05-maia/src/hermes/tokens/HERMES.sol:

    function mint(address account, uint256 amount) external onlyOwner {


Found in line 86 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    function setDao(address _dao) external onlyOwner {


Found in line 92 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    function setDaoShare(uint256 _daoShare) external onlyOwner {


Found in line 98 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    function setTailEmission(uint256 _tail_emission) external onlyOwner {


Found in line 58 at 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol:

    function addPartner(PartnerManager newPartnerManager) external onlyOwner {


Found in line 67 at 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol:

    function addVault(IBaseVault newVault) external onlyOwner {


Found in line 80 at 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol:

    function removePartner(PartnerManager partnerManager) external onlyOwner {


Found in line 89 at 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol:

    function removeVault(IBaseVault vault) external onlyOwner {


Found in line 188 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function migratePartnerVault(address newPartnerVault) external onlyOwner {


Found in line 216 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    function increaseConversionRate(uint256 newRate) external onlyOwner {


Found in line 55 at 2023-05-maia/src/maia/tokens/Maia.sol:

    function mint(address account, uint256 amount) external onlyOwner {


Found in line 128 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

    function addBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {


Found in line 144 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

    function removeBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {


Found in line 62 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

    function setMinimumWidth(uint24 _minimumWidth) external onlyOwner {


Found in line 110 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

    function addGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {


Found in line 121 at 2023-05-maia/src/gauges/factories/BaseV2GaugeManager.sol:

    function removeGaugeFactory(BaseV2GaugeFactory gaugeFactory) external onlyOwner {


Found in line 98 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {


Found in line 109 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function createGauge(address strategy, bytes memory data) external onlyOwner {


Found in line 130 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function removeGauge(BaseV2Gauge gauge) external onlyOwner {


Found in line 144 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function addBribeToGauge(BaseV2Gauge gauge, address bribeToken) external onlyOwnerOrBribesFactoryOwner {


Found in line 151 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    function removeBribeFromGauge(BaseV2Gauge gauge, address bribeToken) external onlyOwnerOrBribesFactoryOwner {


Found in line 398 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function addGauge(address gauge) external onlyOwner returns (uint112) {


Found in line 425 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function removeGauge(address gauge) external onlyOwner {


Found in line 449 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function replaceGauge(address oldGauge, address newGauge) external onlyOwner {


Found in line 455 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function setMaxGauges(uint256 newMax) external onlyOwner {


Found in line 463 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

    function setContractExceedMaxGauges(address account, bool canExceedMax) external onlyOwner {


Found in line 96 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function setMaxDelegates(uint256 newMax) external onlyOwner {


Found in line 104 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

    function setContractExceedMaxDelegates(address account, bool canExceedMax) external onlyOwner {


Found in line 259 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

    function addGauge(address gauge) external onlyOwner {


Found in line 273 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

    function removeGauge(address gauge) external onlyOwner {


Found in line 285 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

    function replaceGauge(address oldGauge, address newGauge) external onlyOwner {

------------------------------------------------------------------------ 

### Mitigation 

If a function modifier or require such as onlyOwner/onlyX is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.










# VULN 15 

## [GAS] Do not calculate constants
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 47 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    uint24 private constant protocolFee = 2 * 1e5; //20%


Found in line 23 at 2023-05-maia/src/talos/libraries/PoolVariables.sol:

    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)


Found in line 137 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 internal constant MIN_FALLBACK_RESERVE = 185_000; // 100_000 for anycall + 85_000 fallback execution overhead


Found in line 138 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 internal constant MIN_EXECUTION_OVERHEAD = 160_000; // 100_000 for anycall + 35_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Executions


Found in line 180 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint256 internal constant MIN_FALLBACK_RESERVE = 155_000; // 100_000 for anycall + 55_000 for fallback


Found in line 181 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint256 internal constant MIN_EXECUTION_OVERHEAD = 155_000; // 100_000 for anycall + 30_000 Pre 1st Gas Checkpoint Execution + 25_000 Post last Gas Checkpoint Execution


Found in line 227 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        accumulatedFees = 1; //Avoid paying 20k gas in first `payExecutionGas` making MIN_EXECUTION_OVERHEAD constant.


Found in line 640 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    uint24 private constant GLOBAL_DIVISIONER = 1e6; // for basis point (0.0001%)


Found in line 24 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    uint256 internal constant week = 86400 * 7;


Found in line 13 at 2023-05-maia/src/uni-v3-staker/libraries/IncentiveTime.sol:

    uint256 private constant INCENTIVES_DURATION = 1 weeks; // Incentives are 1 week long and start at THURSDAY 12:00:00 UTC (00:00:00 UTC + 12 hours (INCENTIVE_OFFSET))


Found in line 12 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken


Found in line 15 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken


Found in line 18 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks


Found in line 21 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks


Found in line 24 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks


Found in line 27 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks


Found in line 30 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken


Found in line 33 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    uint256 public constant proposalMaxOperations = 10; // 10 actions

------------------------------------------------------------------------ 

### Mitigation 

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.










# VULN 16 

## [GAS] Using private rather than public for constants, saves gas
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 19 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    uint256 public immutable id;


Found in line 28 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    uint256 public immutable id;


Found in line 51 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

    uint256 public immutable localChainId;


Found in line 89 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 public immutable rootChainId;


Found in line 92 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    uint256 public immutable localChainId;


Found in line 16 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

    uint256 public immutable localChainId;


Found in line 18 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    uint256 public immutable localChainId;


Found in line 21 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    uint256 public immutable rootChainId;


Found in line 23 at 2023-05-maia/src/rewards/rewards/FlywheelAcummulatedRewards.sol:

    uint256 public immutable override rewardsCycleLength;


Found in line 24 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

    uint256 public immutable rewardsCycleLength;


Found in line 100 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    uint256 public immutable override maxIncentiveStartLeadTime;

------------------------------------------------------------------------ 

### Mitigation 

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.










# VULN 17 

## [GAS] >= costs less gas than >
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 70 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    /// @notice Mapping returns true if Port Startegy is allowed to manage a given Strategy Token. Strategy => Token => bool.


Found in line 79 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    /// @notice Mapping returns the amount of Strategy Token debt a given Port Startegy has.  Strategy => Token => uint256.


Found in line 82 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    /// @notice Mapping returns the last time a given Port Strategy managed a given Strategy Token. Strategy => Token => uint256.


Found in line 85 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

    /// @notice Mapping returns the time limit a given Port Strategy must wait before managing a Strategy Token. Strategy => Token => uint256.


Found in line 129 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        return currBalance > minReserves ? currBalance - minReserves : 0;


Found in line 140 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        return currBalance < minReserves ? minReserves - currBalance : 0;

Found in line 390 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        return (whitelistAccountExpirations[account] > block.timestamp);


Found in line 93 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

        return gaugeWeight.currentCycle < currentCycle ? gaugeWeight.currentWeight : gaugeWeight.storedWeight;

------------------------------------------------------------------------ 

### Mitigation 

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.










# VULN 18 

## [GAS] Change public state variable visibility to private
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 33 at 2023-05-maia/src/rewards/base/FlywheelCore.sol:

    address public override flywheelRewards;


Found in line 39 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    address public override dao;


Found in line 41 at 2023-05-maia/src/gauges/BaseV2Gauge.sol:

    address public override strategy;

------------------------------------------------------------------------ 

### Mitigation 

It is preferred to change the visibility of the state variables to private, this will save significant gas.










# VULN 19 

## [GAS] With assembly, .call (bool success) transfer can be done gas-optimized
------------------------------------------------------------------------ 

### Proof of concept 

Found in 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol (function anyExecuteSignedDepositSingle():

        bytes1 funcId,

        bytes calldata encodedData,

        DepositParams calldata,

        address userAccount,

        uint24

    ) external payable override requiresExecutor lock returns (bool success, bytes memory result) {

        /// FUNC ID: 1 (multicallNoOutput)

        if (funcId == 0x01) {

            Call[] memory calls = abi.decode(encodedData, (Call[]));



            //Call desired functions



Found in 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol (function anyExecuteSignedDepositMultiple():

        bytes1 funcId,

        bytes memory encodedData,

        DepositMultipleParams calldata,

        address userAccount,

        uint24

    ) external payable requiresExecutor lock returns (bool success, bytes memory result) {

        /// FUNC ID: 1 (multicallNoOutput)

        if (funcId == 0x01) {

            Call[] memory calls = abi.decode(encodedData, (Call[]));



            //Call desired functions



Found in 2023-05-maia/src/ulysses-omnichain/CoreBranchRouter.sol (function anyExecuteNoSettlement(bytes calldata _data)):

    function anyExecuteNoSettlement(bytes calldata _data)

        external

        virtual

        override

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    {

        /// _receiveAddGlobalToken

        if (_data[0] == 0x01) {

            (address globalAddress, string memory name, string memory symbol, uint128 gasToBridgeOut) =

                abi.decode(_data[1:], (address, string, string, uint128));



Found in 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol (function executeNoSettlement(address _router, bytes calldata _data)):

     * @dev SETTLEMENT FLAG: 0 (No settlement)

     */

    function executeNoSettlement(address _router, bytes calldata _data)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Execute remote request

        (success, result) = IRouter(_router).anyExecuteNoSettlement(_data[25:_data.length - PARAMS_GAS_OUT]);

    }





Found in 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol (function executeWithSettlement(address _recipient, address _router, bytes calldata _data)):

     * @dev SETTLEMENT FLAG: 1 (Single Settlement)

     */

    function executeWithSettlement(address _recipient, address _router, bytes calldata _data)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Clear Token / Execute Settlement

        SettlementParams memory sParams = SettlementParams({

            settlementNonce: uint32(bytes4(_data[PARAMS_START_SIGNED:25])),

            recipient: _recipient,



Found in 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol (function executeWithSettlementMultiple(address _recipient, address _router, bytes calldata _data)):

     * @dev SETTLEMENT FLAG: 2 (Multiple Settlements)

     */

    function executeWithSettlementMultiple(address _recipient, address _router, bytes calldata _data)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Bridge In Assets and Save Deposit Params

        SettlementMultipleParams memory sParams = BranchBridgeAgent(payable(msg.sender)).clearTokens(

            _data[

                PARAMS_START_SIGNED:



Found in 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol (function anyExecute(bytes calldata data)):

    /// @inheritdoc IApp

    function anyExecute(bytes calldata data)

        external

        virtual

        requiresExecutor

        returns (bool success, bytes memory result)

    {

        //Get Initial Gas Checkpoint

        uint256 initialGas = gasleft();



        //Save Length



Found in 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol (function anyFallback(bytes calldata data)):

    /// @inheritdoc IApp

    function anyFallback(bytes calldata data)

        external

        virtual

        requiresExecutor

        returns (bool success, bytes memory result)

    {

        //Get Initial Gas Checkpoint

        uint256 initialGas = gasleft();



        //Save Flag



Found in 2023-05-maia/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol (function anyExecuteNoSettlement(bytes calldata _data)):

    ///@inheritdoc CoreBranchRouter

    function anyExecuteNoSettlement(bytes calldata _data)

        external

        override

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    {

        if (_data[0] == 0x02) {

            (

                address newBranchRouter,

                address branchBridgeAgentFactory,



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeSystemRequest(address _router, bytes calldata _data, uint24 _fromChainId)):

     * @dev DEPOSIT FLAG: 0 (System request / response)

     */

    function executeSystemRequest(address _router, bytes calldata _data, uint24 _fromChainId)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Try to execute remote request

        (success, result) = IRouter(_router).anyExecuteResponse(

            bytes1(_data[PARAMS_TKN_START]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId

        );



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeNoDeposit(address _router, bytes calldata _data, uint24 _fromChainId)):

     * @dev DEPOSIT FLAG: 1 (Call without Deposit)

     */

    function executeNoDeposit(address _router, bytes calldata _data, uint24 _fromChainId)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Execute remote request

        (success, result) =

            IRouter(_router).anyExecute(bytes1(_data[5]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId);

    }



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeWithDeposit(address _router, bytes calldata _data, uint24 _fromChainId)):

     * @dev DEPOSIT FLAG: 2 (Call with Deposit)

     */

    function executeWithDeposit(address _router, bytes calldata _data, uint24 _fromChainId)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Read Deposit Params

        DepositParams memory dParams = DepositParams({

            depositNonce: uint32(bytes4(_data[PARAMS_START:PARAMS_TKN_START])),

            hToken: address(uint160(bytes20(_data[PARAMS_TKN_START:25]))),



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeWithDepositMultiple(address _router, bytes calldata _data, uint24 _fromChainId)):

     * @dev DEPOSIT FLAG: 3 (Call with multiple asset Deposit)

     */

    function executeWithDepositMultiple(address _router, bytes calldata _data, uint24 _fromChainId)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Bridge In Assets and Save Deposit Params

        DepositMultipleParams memory dParams = _bridgeInMultiple(

            _router,

            _data[



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeSignedNoDeposit(address _account, address _router, bytes calldata _data, uint24 _fromChainId)):

     * @dev DEPOSIT FLAG: 4 (Call without Deposit + msg.sender)

     */

    function executeSignedNoDeposit(address _account, address _router, bytes calldata _data, uint24 _fromChainId)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Execute remote request

        (success, result) =

            IRouter(_router).anyExecuteSigned(_data[25], _data[26:_data.length - PARAMS_GAS_IN], _account, _fromChainId);

    }



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeSignedWithDeposit(address _account, address _router, bytes calldata _data, uint24 _fromChainId)):

     * @dev DEPOSIT FLAG: 5 (Call with Deposit + msg.sender)

     */

    function executeSignedWithDeposit(address _account, address _router, bytes calldata _data, uint24 _fromChainId)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Read Deposit Params

        DepositParams memory dParams = DepositParams({

            depositNonce: uint32(bytes4(_data[PARAMS_START_SIGNED:25])),

            hToken: address(uint160(bytes20(_data[25:45]))),



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeSignedWithDepositMultiple():

    function executeSignedWithDepositMultiple(

        address _account,

        address _router,

        bytes calldata _data,

        uint24 _fromChainId

    ) external onlyOwner returns (bool success, bytes memory result) {

        //Bridge In Assets

        DepositMultipleParams memory dParams = _bridgeInMultiple(

            _account,

            _data[

                PARAMS_START_SIGNED:



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgentExecutor.sol (function executeRetrySettlement(uint32 _settlementNonce)):

     * @dev DEPOSIT FLAG: 7 (Retry Settlement)

     */

    function executeRetrySettlement(uint32 _settlementNonce)

        external

        onlyOwner

        returns (bool success, bytes memory result)

    {

        //Execute remote request

        RootBridgeAgent(payable(msg.sender)).retrySettlement(_settlementNonce, 0);

        //Trigger retry success (no guarantees about settlement success at this point)

        (success, result) = (true, "");



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol (function anyExecute(bytes calldata data)):

    /// @inheritdoc IApp

    function anyExecute(bytes calldata data)

        external

        virtual

        requiresExecutor

        returns (bool success, bytes memory result)

    {

        //Get Initial Gas Checkpoint

        uint256 _initialGas = gasleft();



        uint24 fromChainId;



Found in 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol (function anyFallback(bytes calldata data)):

    /// @inheritdoc IApp

    function anyFallback(bytes calldata data)

        external

        virtual

        requiresExecutor

        returns (bool success, bytes memory result)

    {

        //Get Initial Gas Checkpoint

        uint256 _initialGas = gasleft();



        //Get fromChain



Found in 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol (function anyExecuteNoSettlement(bytes calldata)):

    /// @inheritdoc IBranchRouter

    function anyExecuteNoSettlement(bytes calldata)

        external

        virtual

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    {

        /// Unrecognized Function Selector

        return (false, "unknown selector");

    }





Found in 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol (function anyExecuteSettlement(bytes calldata, SettlementParams memory)):

    /// @inheritdoc IBranchRouter

    function anyExecuteSettlement(bytes calldata, SettlementParams memory)

        external

        virtual

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    {

        /// Unrecognized Function Selector

        return (false, "unknown selector");

    }





Found in 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol (function anyExecuteSettlementMultiple(bytes calldata, SettlementMultipleParams memory)):

    /// @inheritdoc IBranchRouter

    function anyExecuteSettlementMultiple(bytes calldata, SettlementMultipleParams memory)

        external

        virtual

        requiresAgentExecutor

        returns (bool success, bytes memory result)

    {

        /// Unrecognized Function Selector

        return (false, "unknown selector");

    }





Found in 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol (function call(Call[] calldata calls)):

        returns (uint256 blockNumber, bytes[] memory returnData)

    {

        blockNumber = block.number;

        returnData = new bytes[](calls.length);

        for (uint256 i = 0; i < calls.length; i++) {

            (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

            if (!success) revert CallFailed();

            returnData[i] = data;

        }

    }





Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IApp.sol (function anyExecute(bytes calldata _data) external returns (bool success, bytes memory result);):

     * @notice anyExecute is the function that will be called on the destination chain to execute interaction (required).

     *     @param _data interaction arguments to exec on the destination chain.

     *     @return success whether the interaction was successful.

     *     @return result the result of the interaction.

     */

    function anyExecute(bytes calldata _data) external returns (bool success, bytes memory result);



    /**

     * @notice anyFallback is the function that will be called on the originating chain if the cross chain interaction fails (optional, advised).

     *     @param _data interaction arguments to exec on the destination chain.

     *     @return success whether the interaction was successful.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IApp.sol (function anyFallback(bytes calldata _data) external returns (bool success, bytes memory result);):

     * @notice anyFallback is the function that will be called on the originating chain if the cross chain interaction fails (optional, advised).

     *     @param _data interaction arguments to exec on the destination chain.

     *     @return success whether the interaction was successful.

     *     @return result the result of the interaction.

     */

    function anyFallback(bytes calldata _data) external returns (bool success, bytes memory result);

}



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol (function anyExecuteNoSettlement(bytes calldata data) external returns (bool success, bytes memory result);):



    /**

     * @notice Function responsible of executing a branch router response.

     *     @param data data received from messaging layer.

     */

    function anyExecuteNoSettlement(bytes calldata data) external returns (bool success, bytes memory result);



    /**

     * @dev Function responsible of executing a crosschain request without any deposit.

     *     @param data data received from messaging layer.

     *     @param sParams SettlementParams struct.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol (function anyExecuteSettlement(bytes calldata data, SettlementParams memory sParams)):

     *     @param data data received from messaging layer.

     *     @param sParams SettlementParams struct.

     */

    function anyExecuteSettlement(bytes calldata data, SettlementParams memory sParams)

        external

        returns (bool success, bytes memory result);



    /**

     * @dev Function responsible of executing a crosschain request which contains cross-chain deposit information attached.

     *     @param data data received from messaging layer.

     *     @param sParams SettlementParams struct containing deposit information.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IBranchRouter.sol (function anyExecuteSettlementMultiple(bytes calldata data, SettlementMultipleParams memory sParams)):

     *     @param sParams SettlementParams struct containing deposit information.

     *

     */

    function anyExecuteSettlementMultiple(bytes calldata data, SettlementMultipleParams memory sParams)

        external

        returns (bool success, bytes memory result);



    /*///////////////////////////////////////////////////////////////

                             ERRORS

    //////////////////////////////////////////////////////////////*/





Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecuteResponse(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)):

     *

     */

    function anyExecuteResponse(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)

        external

        payable

        returns (bool success, bytes memory result);



    /**

     *     @notice Function responsible of executing a crosschain request without any deposit.

     *     @param funcId 1 byte Router function identifier.

     *     @param encodedData data received from messaging layer.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecute(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)):

     *

     */

    function anyExecute(bytes1 funcId, bytes memory encodedData, uint24 fromChainId)

        external

        payable

        returns (bool success, bytes memory result);



    /**

     *   @notice Function responsible of executing a crosschain request which contains cross-chain deposit information attached.

     *   @param funcId 1 byte Router function identifier.

     *   @param encodedData execution data received from messaging layer.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecuteDepositSingle():

    function anyExecuteDepositSingle(

        bytes1 funcId,

        bytes memory encodedData,

        DepositParams memory dParams,

        uint24 fromChainId

    ) external payable returns (bool success, bytes memory result);



    /**

     *   @notice Function responsible of executing a crosschain request which contains cross-chain deposit information for multiple assets attached.

     *   @param funcId 1 byte Router function identifier.

     *   @param encodedData execution data received from messaging layer.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecuteDepositMultiple():

    function anyExecuteDepositMultiple(

        bytes1 funcId,

        bytes memory encodedData,

        DepositMultipleParams memory dParams,

        uint24 fromChainId

    ) external payable returns (bool success, bytes memory result);



    /**

     * @notice Function responsible of executing a crosschain request with msg.sender without any deposit.

     * @param funcId 1 byte Router function identifier.

     * @param encodedData execution data received from messaging layer.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecuteSigned(bytes1 funcId, bytes memory encodedData, address userAccount, uint24 fromChainId)):

     * @param fromChainId chain where the request originated from.

     */

    function anyExecuteSigned(bytes1 funcId, bytes memory encodedData, address userAccount, uint24 fromChainId)

        external

        payable

        returns (bool success, bytes memory result);



    /**

     * @notice Function responsible of executing a crosschain request which contains cross-chain deposit information and msg.sender attached.

     * @param funcId 1 byte Router function identifier.

     * @param encodedData execution data received from messaging layer.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecuteSignedDepositSingle():

        bytes1 funcId,

        bytes memory encodedData,

        DepositParams memory dParams,

        address userAccount,

        uint24 fromChainId

    ) external payable returns (bool success, bytes memory result);



    /**

     * @notice Function responsible of executing a crosschain request which contains cross-chain deposit information for multiple assets and msg.sender attached.

     * @param funcId 1 byte Router function identifier.

     * @param encodedData execution data received from messaging layer.



Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IRootRouter.sol (function anyExecuteSignedDepositMultiple():

        bytes1 funcId,

        bytes memory encodedData,

        DepositMultipleParams memory dParams,

        address userAccount,

        uint24 fromChainId

    ) external payable returns (bool success, bytes memory result);



    /*///////////////////////////////////////////////////////////////

                             ERRORS

    //////////////////////////////////////////////////////////////*/





Found in 2023-05-maia/src/ulysses-omnichain/interfaces/IAnycallExecutor.sol (function execute():

        address _from,

        uint256 _fromChainID,

        uint256 _nonce,

        uint256 _flags,

        bytes calldata _extdata

    ) external returns (bool success, bytes memory result);

}


Found in 2023-05-maia/src/governance/GovernorBravoDelegator.sol (function delegateTo(address callee, bytes memory data) internal {):

     * @dev It returns to the external caller whatever the implementation returns or forwards reverts

     * @param callee The contract to delegatecall

     * @param data The raw data to delegatecall

     */

    function delegateTo(address callee, bytes memory data) internal {

        (bool success, bytes memory returnData) = callee.delegatecall(data);

        assembly {

            if eq(success, 0) { revert(add(returnData, 0x20), returndatasize()) }

        }

    }





Found in 2023-05-maia/src/governance/GovernorBravoDelegator.sol (function delegateTo(address callee, bytes memory data) internal {):

     * It returns to the external caller whatever the implementation returns

     * or forwards reverts.

     */

    fallback() external payable {

        // delegate all other functions to current implementation

        (bool success,) = implementation.delegatecall(msg.data);



        assembly {

            let free_mem_ptr := mload(0x40)

            returndatacopy(free_mem_ptr, 0, returndatasize())




------------------------------------------------------------------------ 

### Mitigation 

return data (bool success,) has to be stored due to EVM architecture, but in a usage like below, ‘out’ and ‘outsize’ values are given (0,0), this storage disappears and gas optimization is provided.










# VULN 20 

## [GAS] Empty blocks should be removed or emit something
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 64 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    ) TalosStrategySimple(_pool, _optimizer, _nonfungiblePositionManager, _strategyManager, _owner) {}


Found in line 79 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    function afterRedeem(uint256 _tokenId) internal override {}


Found in line 90 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    function afterDeposit(uint256 _tokenId) internal override {}


Found in line 100 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    function afterRerange(uint256 _tokenId) internal override {}


Found in line 96 at 2023-05-maia/src/talos/TalosManager.sol:

        try strategy.pool().checkDeviation(optimizer.maxTwapDeviation(), optimizer.twapDuration()) {}


Found in line 179 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

        } catch {}


Found in line 26 at 2023-05-maia/src/talos/factories/TalosStrategyVanillaFactory.sol:

    {}


Found in line 24 at 2023-05-maia/src/talos/strategies/TalosStrategySimple.sol:

    ) TalosBaseStrategy(_pool, _strategy, _nonfungiblePositionManager, _strategyManager, _owner) {}


Found in line 284 at 2023-05-maia/src/ulysses-omnichain/CoreBranchRouter.sol:

    fallback() external payable {}


Found in line 1328 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}


Found in line 1419 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    fallback() external payable {}


Found in line 40 at 2023-05-maia/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol:

    {}


Found in line 1240 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        if (executionBudget > 0) try anycallConfig.withdraw(executionBudget) {} catch {}


Found in line 1334 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    fallback() external payable {}


Found in line 90 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

    {}


Found in line 181 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

    function _replenishGas(uint256) internal override {}


Found in line 52 at 2023-05-maia/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol:

    {}


Found in line 37 at 2023-05-maia/src/rewards/FlywheelCoreStrategy.sol:

    ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}


Found in line 38 at 2023-05-maia/src/rewards/FlywheelCoreInstant.sol:

    ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}


Found in line 29 at 2023-05-maia/src/rewards/rewards/FlywheelBribeRewards.sol:

    {}


Found in line 62 at 2023-05-maia/src/hermes/bHermes.sol:

    {}


Found in line 152 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

            try flywheelGaugeRewards.queueRewardsForCycle() {} catch {}


Found in line 91 at 2023-05-maia/src/maia/vMaia.sol:

    function claimBoost(uint256 amount) public override {}


Found in line 111 at 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol:

    function afterDeposit(uint256 assets, uint256 shares) internal virtual {}


Found in line 292 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

    function beforeWithdraw(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}


Found in line 294 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

    function afterDeposit(uint256[] memory assetsAmounts, uint256 shares) internal virtual {}


Found in line 171 at 2023-05-maia/src/erc-4626/ERC4626.sol:

    function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}


Found in line 173 at 2023-05-maia/src/erc-4626/ERC4626.sol:

    function afterDeposit(uint256 assets, uint256 shares) internal virtual {}

------------------------------------------------------------------------ 

### Mitigation 

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}).










# VULN 21 

## [GAS] Use double require instead of using &&
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 408 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        require(balance0 >= amount0 && balance1 >= amount1);


------------------------------------------------------------------------ 

### Mitigation 

Using double require instead of operator && can save more gas. When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.










# VULN 22 

## [GAS] bytes constants are more eficient than string constans
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 9 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

    string public constant name = "vMaia Governor Bravo";

------------------------------------------------------------------------ 

### Mitigation 

If the data can fit in 32 bytes, the bytes32 data type can be used instead of bytes or strings, as it is less robust in terms of robustness.










# VULN 23 

## [GAS] Use a more recent version of solidity
------------------------------------------------------------------------ 

### Proof of concept 

Found in 2023-05-maia/src/talos/TalosStrategyVanilla.sol at line 3: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/talos/TalosStrategyStaked.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/talos/interfaces/IBoostAggregator.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/talos/base/TalosBaseStrategy.sol at line 3: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/talos/strategies/TalosStrategySimple.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/erc-4626/UlyssesERC4626.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/erc-4626/ERC4626DepositOnly.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/erc-4626/ERC4626.sol at line 2: pragma solidity >=0.8.0;

Found in 2023-05-maia/src/erc-4626/interfaces/IERC4626.sol at line 2: pragma solidity >=0.8.0;
------------------------------------------------------------------------ 

### Mitigation 

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath * Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining * Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads * Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings * Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value * In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller. * In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.










# VULN 24 

## [GAS] Use assembly to write address storage values
------------------------------------------------------------------------ 

### Proof of concept 

Found in lines 21 to 28 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

    constructor(
        uint256 _id,
        address[] memory _assets,
        uint256[] memory _weights,
        string memory _name,
        string memory _symbol,
        address _owner
    ) ERC4626MultiToken(_assets, _weights, _name, _symbol) {


Found in lines 80 to 87 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

    constructor(
        uint256 _id,
        address _asset,
        string memory _name,
        string memory _symbol,
        address _owner,
        address _factory
    ) UlyssesERC4626(_asset, _name, _symbol) {


Found in lines 58 to 64 at 2023-05-maia/src/talos/TalosStrategyVanilla.sol:

    constructor(
        IUniswapV3Pool _pool,
        ITalosOptimizer _optimizer,
        INonfungiblePositionManager _nonfungiblePositionManager,
        address _strategyManager,
        address _owner
    ) TalosStrategySimple(_pool, _optimizer, _nonfungiblePositionManager, _strategyManager, _owner) {}


Found in lines 44 to 50 at 2023-05-maia/src/talos/TalosManager.sol:

    constructor(
        address _strategy,
        int24 _ticksFromLowerRebalance,
        int24 _ticksFromUpperRebalance,
        int24 _ticksFromLowerRerange,
        int24 _ticksFromUpperRerange
    ) {


Found in lines 34 to 41 at 2023-05-maia/src/talos/TalosOptimizer.sol:

    constructor(
        uint32 _twapDuration,
        int24 _maxTwapDeviation,
        int24 _tickRangeMultiplier,
        uint24 _priceImpactPercentage,
        uint256 _maxTotalSupply,
        address _owner
    ) {


Found in lines 63 to 70 at 2023-05-maia/src/talos/TalosStrategyStaked.sol:

    constructor(
        IUniswapV3Pool _pool,
        ITalosOptimizer _optimizer,
        BoostAggregator _boostAggregator,
        address _strategyManager,
        FlywheelCoreInstant _flywheel,
        address _owner
    )


Found in lines 79 to 85 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    constructor(
        IUniswapV3Pool _pool,
        ITalosOptimizer _optimizer,
        INonfungiblePositionManager _nonfungiblePositionManager,
        address _strategyManager,
        address _owner
    ) ERC20("TALOS LP", "TLP", 18) {


Found in lines 18 to 24 at 2023-05-maia/src/talos/strategies/TalosStrategySimple.sol:

    constructor(
        IUniswapV3Pool _pool,
        ITalosOptimizer _strategy,
        INonfungiblePositionManager _nonfungiblePositionManager,
        address _strategyManager,
        address _owner
    ) TalosBaseStrategy(_pool, _strategy, _nonfungiblePositionManager, _strategyManager, _owner) {}


Found in lines 141 to 150 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

    constructor(
        WETH9 _wrappedNativeToken,
        uint256 _rootChainId,
        uint256 _localChainId,
        address _rootBridgeAgentAddress,
        address _localAnyCallAddress,
        address _localAnyCallExecutorAddress,
        address _localRouterAddress,
        address _localPortAddress
    ) {


Found in lines 201 to 209 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

    constructor(
        WETH9 _wrappedNativeToken,
        uint24 _localChainId,
        address _daoAddress,
        address _localAnyCallAddress,
        address _localAnyCallExecutorAddress,
        address _localPortAddress,
        address _localRouterAddress
    ) {


Found in lines 71 to 79 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:

    constructor(
        WETH9 _wrappedNativeToken,
        uint256 _localChainId,
        address _rootBridgeAgentAddress,
        address _localAnyCallAddress,
        address _localAnyCallExecutorAddress,
        address _localRouterAddress,
        address _localPortAddress
    )


Found in lines 38 to 44 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

    constructor(
        uint256 _localChainId,
        address _factoryAddress,
        address _rootPortAddress,
        string memory _name,
        string memory _symbol
    ) ERC20(string(string.concat("Hermes ", _name)), string(string.concat("h-", _symbol)), 18) {


Found in lines 31 to 40 at 2023-05-maia/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol:

    constructor(
        uint256 _rootChainId,
        address _rootBridgeAgentFactoryAddress,
        WETH9 _wrappedNativeToken,
        address _localAnyCallAddress,
        address _localAnyCallExecutorAddress,
        address _localCoreBranchRouterAddress,
        address _localPortAddress,
        address _owner
    )


Found in lines 53 to 63 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

    constructor(
        uint256 _localChainId,
        uint256 _rootChainId,
        address _rootBridgeAgentFactoryAddress,
        WETH9 _wrappedNativeToken,
        address _localAnyCallAddress,
        address _localAnyCallExecutorAddress,
        address _localCoreBranchRouterAddress,
        address _localPortAddress,
        address _owner
    ) {


Found in lines 48 to 54 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

    constructor(
        uint24 _rootChainId,
        WETH9 _wrappedNativeToken,
        address _localAnyCallAddress,
        address _rootPortAddress,
        address _daoAddress
    ) {


Found in lines 32 to 37 at 2023-05-maia/src/rewards/FlywheelCoreStrategy.sol:

    constructor(
        address _rewardToken,
        IFlywheelRewards _flywheelRewards,
        IFlywheelBooster _flywheelBooster,
        address _owner
    ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}


Found in lines 33 to 38 at 2023-05-maia/src/rewards/FlywheelCoreInstant.sol:

    constructor(
        address _rewardToken,
        IFlywheelRewards _flywheelRewards,
        IFlywheelBooster _flywheelBooster,
        address _owner
    ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}


Found in lines 53 to 54 at 2023-05-maia/src/hermes/minters/BaseV2Minter.sol:

    constructor(
        address _vault, // the B(3,3) system that will be locked into


Found in lines 48 to 57 at 2023-05-maia/src/maia/vMaia.sol:

    constructor(
        PartnerManagerFactory _factory,
        uint256 _bHermesRate,
        ERC20 _partnerAsset,
        string memory _name,
        string memory _symbol,
        address _bhermes,
        address _partnerVault,
        address _owner
    ) ERC4626PartnerManager(_factory, _bHermesRate, _partnerAsset, _name, _symbol, _bhermes, _partnerVault, _owner) {


Found in lines 36 to 42 at 2023-05-maia/src/maia/PartnerUtilityManager.sol:

    constructor(
        address _gaugeWeight,
        address _gaugeBoost,
        address _governance,
        address _partnerGovernance,
        address _partnerVault
    ) UtilityManager(_gaugeWeight, _gaugeBoost, _governance) {


Found in lines 50 to 59 at 2023-05-maia/src/maia/tokens/ERC4626PartnerManager.sol:

    constructor(
        PartnerManagerFactory _factory,
        uint256 _bHermesRate,
        ERC20 _partnerAsset,
        string memory _name,
        string memory _symbol,
        address _bhermes,
        address _partnerVault,
        address _owner
    )


Found in lines 33 to 39 at 2023-05-maia/src/gauges/UniswapV3Gauge.sol:

    constructor(
        FlywheelGaugeRewards _flywheelGaugeRewards,
        address _uniswapV3Staker,
        address _uniswapV3Pool,
        uint24 _minimumWidth,
        address _owner
    ) BaseV2Gauge(_flywheelGaugeRewards, _uniswapV3Pool, _owner) {


Found in lines 50 to 55 at 2023-05-maia/src/gauges/factories/BribesFactory.sol:

    constructor(
        BaseV2GaugeManager _gaugeManager,
        FlywheelBoosterGaugeWeight _flywheelGaugeWeightBooster,
        uint256 _rewardsCycleLength,
        address _owner
    ) {


Found in lines 50 to 58 at 2023-05-maia/src/gauges/factories/UniswapV3GaugeFactory.sol:

    constructor(
        BaseV2GaugeManager _gaugeManager,
        bHermesBoost _bHermesBoost,
        IUniswapV3Factory _factory,
        INonfungiblePositionManager _nonfungiblePositionManager,
        FlywheelGaugeRewards _flywheelGaugeRewards,
        BribesFactory _bribesFactory,
        address _owner
    ) BaseV2GaugeFactory(_gaugeManager, _bHermesBoost, _bribesFactory, _owner) {


Found in lines 51 to 56 at 2023-05-maia/src/gauges/factories/BaseV2GaugeFactory.sol:

    constructor(
        BaseV2GaugeManager _gaugeManager,
        bHermesBoost _bHermesBoost,
        BribesFactory _bribesFactory,
        address _owner
    ) {


Found in lines 114 to 122 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    constructor(
        IUniswapV3Factory _factory,
        INonfungiblePositionManager _nonfungiblePositionManager,
        IUniswapV3GaugeFactory _uniswapV3GaugeFactory,
        bHermesBoost _hermesGaugeBoost,
        uint256 _maxIncentiveStartLeadTime,
        address _minter,
        address _hermes
    ) {


Found in lines 8 to 16 at 2023-05-maia/src/governance/GovernorBravoDelegator.sol:

    constructor(
        address timelock_,
        address govToken_,
        address admin_,
        address implementation_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThreshold_
    ) public {

------------------------------------------------------------------------ 

### Mitigation 

Use assembly to write address storage values. Here are a few reasons: * Reduced opcode usage: When using assembly, you can directly manipulate storage values using lower-level instructions like sstore (storage store) instead of relying on higher-level Solidity storage assignments. These direct operations typically result in fewer opcode executions, reducing gas costs. * Avoiding unnecessary checks: Solidity storage assignments often involve additional checks and operations, such as enforcing security modifiers or triggering events. By using assembly, you can bypass these additional checks and perform the necessary storage operations directly, resulting in gas savings. * Optimized packing: Assembly provides greater flexibility in packing and unpacking data structures. By carefully arranging and manipulating the storage layout in assembly, you can achieve more efficient storage utilization and minimize wasted storage space. * Fine-grained control: Assembly allows for precise control over gas-consuming operations. You can optimize gas usage by selecting specific instructions and minimizing unnecessary operations or data copying.










# VULN 25 

## [GAS] Use ERC721A instead ERC721
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 27 at 2023-05-maia/src/talos/interfaces/ITalosBaseStrategy.sol:

interface ITalosBaseStrategy is IERC721Receiver {


Found in line 21 at 2023-05-maia/src/talos/interfaces/IBoostAggregator.sol:

interface IBoostAggregator is IERC721Receiver {


Found in line 79 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

    function onERC721Received(address, address from, uint256 tokenId, bytes calldata)


Found in line 92 at 2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol:

        return this.onERC721Received.selector;


Found in line 328 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

    function onERC721Received(address, address, uint256, bytes calldata) external pure override returns (bytes4) {


Found in line 329 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        return this.onERC721Received.selector;


Found in line 36 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

    function withdrawERC721(address _token, uint256 _tokenId) external requiresApprovedCaller {


Found in line 37 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

        ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);


Found in line 60 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

    function onERC721Received(address, address, uint256, bytes calldata) external pure override returns (bytes4) {


Found in line 61 at 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol:

        return this.onERC721Received.selector;


Found in line 17 at 2023-05-maia/src/ulysses-omnichain/interfaces/IVirtualAccount.sol:

interface IVirtualAccount is IERC721Receiver {


Found in line 42 at 2023-05-maia/src/ulysses-omnichain/interfaces/IVirtualAccount.sol:

    function withdrawERC721(address _token, uint256 _tokenId) external;


Found in line 218 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

    function onERC721Received(address, address from, uint256 tokenId, bytes calldata)


Found in line 235 at 2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol:

        return this.onERC721Received.selector;


Found in line 52 at 2023-05-maia/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol:

interface IUniswapV3Staker is IERC721Receiver {

------------------------------------------------------------------------ 

### Mitigation 

ERC721A standard, ERC721A is an improvement standard for ERC721 tokens. It was proposed by the Azuki team and used for developing their NFT collection. Compared with ERC721, ERC721A is a more gas-efficient standard to mint a lot of of NFTs simultaneously. It allows developers to mint multiple NFTs at the same gas price. This has been a great improvement due to Ethereum’s sky-rocketing gas fee. Reference: https://nextrope.com/erc721-vs-erc721a-2/










# VULN 26 

## [GAS] require() or revert() statements that check input arguments should be at the top of the function (Also restructured some if’s)
------------------------------------------------------------------------ 

### Proof of concept 

Found in line 30 at 2023-05-maia/src/ulysses-amm/UlyssesToken.sol:

        require(_id != 0);


Found in line 88 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        require(_owner != address(0));


Found in line 91 at 2023-05-maia/src/ulysses-amm/UlyssesPool.sol:

        require(_id != 0);


Found in line 61 at 2023-05-maia/src/ulysses-amm/factories/UlyssesFactory.sol:

        require(_owner != address(0), "Owner cannot be 0");


Found in line 408 at 2023-05-maia/src/talos/base/TalosBaseStrategy.sol:

        require(balance0 >= amount0 && balance1 >= amount1);


Found in line 65 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(_localPortAddress != address(0), "Local Port Address cannot be 0");


Found in line 66 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(_multicallAddress != address(0), "Multicall Address cannot be 0");


Found in line 172 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        revert();


Found in line 245 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        revert();


Found in line 495 at 2023-05-maia/src/ulysses-omnichain/MulticallRootRouter.sol:

        require(_unlocked == 1);


Found in line 151 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_rootBridgeAgentAddress != address(0), "Root Bridge Agent Address cannot be the zero address.");


Found in line 152 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localAnyCallAddress != address(0), "AnyCall Address cannot be the zero address.");


Found in line 153 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localAnyCallExecutorAddress != address(0), "AnyCall Executor Address cannot be the zero address.");


Found in line 154 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localRouterAddress != address(0), "Local Router Address cannot be the zero address.");


Found in line 155 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_localPortAddress != address(0), "Local Port Address cannot be the zero address.");


Found in line 1367 at 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol:

        require(_unlocked == 1);


Found in line 210 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(address(_wrappedNativeToken) != address(0), "Wrapped native token cannot be zero address");


Found in line 211 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_daoAddress != address(0), "DAO cannot be zero address");


Found in line 212 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localAnyCallAddress != address(0), "Anycall Address cannot be zero address");


Found in line 213 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localAnyCallExecutorAddress != address(0), "Anycall Executor Address cannot be zero address");


Found in line 214 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localPortAddress != address(0), "Port Address cannot be zero address");


Found in line 215 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_localRouterAddress != address(0), "Router Address cannot be zero address");


Found in line 1274 at 2023-05-maia/src/ulysses-omnichain/RootBridgeAgent.sol:

        require(_unlocked == 1);


Found in line 147 at 2023-05-maia/src/ulysses-omnichain/BaseBranchRouter.sol:

        require(_unlocked == 1);


Found in line 386 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        revert();


Found in line 396 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        revert();


Found in line 407 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        revert();


Found in line 418 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        revert();


Found in line 428 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        revert();


Found in line 439 at 2023-05-maia/src/ulysses-omnichain/CoreRootRouter.sol:

        require(_unlocked == 1);


Found in line 34 at 2023-05-maia/src/ulysses-omnichain/ArbitrumBranchPort.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 95 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(_owner != address(0), "Owner is zero address");


Found in line 424 at 2023-05-maia/src/ulysses-omnichain/BranchPort.sol:

        require(_unlocked == 1);


Found in line 115 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_wrappedNativeToken != address(0), "Invalid wrapped native token address.");


Found in line 146 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(isBridgeAgent[_coreRootBridgeAgent], "Core Bridge Agent doesn't exist.");


Found in line 147 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0 address.");


Found in line 148 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_coreLocalBranchBridgeAgent != address(0), "Core Local Branch Bridge Agent cannot be 0 address.");


Found in line 149 at 2023-05-maia/src/ulysses-omnichain/RootPort.sol:

        require(_localBranchPortAddress != address(0), "Local Branch Port Address cannot be 0 address.");


Found in line 45 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 46 at 2023-05-maia/src/ulysses-omnichain/token/ERC20hTokenRoot.sol:

        require(_factoryAddress != address(0), "Factory Address cannot be 0");


Found in line 87 at 2023-05-maia/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol:

        require(


Found in line 34 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 64 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_rootBridgeAgentFactoryAddress != address(0), "Root Bridge Agent Factory Address cannot be 0");


Found in line 65 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(address(_wrappedNativeToken) != address(0), "Wrapped Native Token cannot be 0");


Found in line 66 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localAnyCallAddress != address(0), "Anycall Address cannot be 0");


Found in line 67 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localAnyCallExecutorAddress != address(0), "Anyexec Address cannot be 0");


Found in line 68 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localCoreBranchRouterAddress != address(0), "Core Branch Router Address cannot be 0");


Found in line 69 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_localPortAddress != address(0), "Port Address cannot be 0");


Found in line 70 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(_owner != address(0), "Owner cannot be 0");


Found in line 121 at 2023-05-maia/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:

        require(


Found in line 28 at 2023-05-maia/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:

        require(_localPortAddress != address(0), "Port address cannot be 0");


Found in line 55 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        require(address(_wrappedNativeToken) != address(0), "Wrapped Native Token cannot be 0");


Found in line 56 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        require(_rootPortAddress != address(0), "Root Port Address cannot be 0");


Found in line 57 at 2023-05-maia/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol:

        require(_daoAddress != address(0), "DAO Address cannot be 0");


Found in line 90 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

        require(rewardToken.balanceOf(address(this)) - balanceBefore >= totalQueuedForCycle);


Found in line 132 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);


Found in line 133 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            require(newRewards <= type(uint112).max); // safe cast


Found in line 182 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            require(queuedRewards.storedCycle < currentCycle);


Found in line 187 at 2023-05-maia/src/rewards/rewards/FlywheelGaugeRewards.sol:

            require(nextRewards <= type(uint112).max); // safe cas


Found in line 40 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        require(shares != 0, "ZERO_SHARES");


Found in line 75 at 2023-05-maia/src/erc-4626/UlyssesERC4626.sol:

        require(assets != 0, "ZERO_ASSETS");


Found in line 51 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

            require(ERC20(_assets[i]).decimals() == 18);


Found in line 52 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

            require(_weights[i] > 0);


Found in line 100 at 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol:

        require((shares = previewDeposit(assetsAmounts)) != 0, "ZERO_SHARES");


Found in line 88 at 2023-05-maia/src/erc-4626/ERC4626.sol:

        require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");


Found in line 80 at 2023-05-maia/src/governance/GovernorBravoDelegator.sol:

            case 0 { revert(free_mem_ptr, returndatasize()) }


Found in line 63 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(address(timelock) == address(0), "GovernorBravo::initialize: can only initialize once");


Found in line 64 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(msg.sender == admin, "GovernorBravo::initialize: admin only");


Found in line 65 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(timelock_ != address(0), "GovernorBravo::initialize: invalid timelock address");


Found in line 66 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(govToken_ != address(0), "GovernorBravo::initialize: invalid govToken address");


Found in line 67 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(


Found in line 71 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(


Found in line 75 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(


Found in line 112 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");


Found in line 114 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(


Found in line 119 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(


Found in line 129 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

            require(


Found in line 133 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

            require(


Found in line 146 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");


Found in line 197 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(


Found in line 237 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

                require(


Found in line 243 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

                require(


Found in line 350 at 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol:

        require(signatory != address(0), "GovernorBravo::castVoteBySig: invalid signature");


Found in line 302 at 2023-05-maia/src/erc-20/ERC20Gauges.sol:

            require(_userGauges[user].remove(gauge));


Found in line 225 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

            require(_delegates[delegator].remove(delegatee));


Found in line 339 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

                    require(_delegates[user].remove(delegatee)); // Remove from set. Should never fail.


Found in line 375 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");


Found in line 376 at 2023-05-maia/src/erc-20/ERC20MultiVotes.sol:

        require(signer != address(0));


Found in line 213 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

                require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.


Found in line 239 at 2023-05-maia/src/erc-20/ERC20Boost.sol:

            require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.

------------------------------------------------------------------------ 

### Mitigation 

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.
