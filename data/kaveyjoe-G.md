1 . Target  : https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol


-  In the amountsForLiquidity and liquidityForAmounts functions, there are repetitive calculations for the same values. we can store and reuse the computed values instead of recalculating them.

- Floor and checkRange, can be inlined directly into the calling functions to eliminate the overhead of function calls.

-The amount0Desired and amount1Desired values are read multiple times in the getSwapToEqualAmountsParams function. we can store these values in local variables instead of reading them repeatedly from storage.


2 Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol

-The contract currently imports SafeCastLib from an external library. Instead of using this library, you can directly use inline type casting provided by Solidity. For example, replace block.timestamp.toUint32() with uint32(block.timestamp).

-  In some functions, there are multiple storage reads for the same variable. we can optimize gas by reducing the number of storage reads and reusing the value when possible, in the _incrementGaugeWeight function, we can store _getGaugeWeight[gauge] in a local variable and use it instead of accessing it multiple times.

-In the gauges(uint256 offset, uint256 num) function, we can avoid copying the array by directly returning a slice of the _gauges set using the at function.

- In the _getGaugeCycleEnd function, the expression (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength can be simplified to nowPlusOneCycle - (nowPlusOneCycle % gaugeCycleLength).

3 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol

- In functions like init(), deposit(), and redeem(), use local variables instead of repeatedly accessing storage variables (e.g., tokenId, token0, token1, nonfungiblePositionManager). This can be done by declaring local variables and assigning them the values of the storage variables once.

-Reduce external function calls: Minimize the number of external function calls, especially within loops, as each call incurs additional gas costs, in functions like deposit() and redeem(), you can store the values of address(_token0) and address(_token1) in local variables instead of accessing them multiple times.

-In the redeem() function, we can avoid an additional storage load by moving the assignment uint256 _tokenId = tokenId; before the beforeRedeem() function call.
Similarly, in the rerange() and rebalance() functions, we can move the assignment uint256 _tokenId = tokenId; before the respective beforeRerange() function calls.

-  In the deposit() and redeem() functions, move the nonReentrant modifier before the override modifier.

4 . Target :https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol

-Replace the average function with a simpler implementation:

function average(uint256 a, uint256 b) internal pure returns (uint256) {
    return (a + b) / 2;
}

This implementation performs the same calculation without the need for bitwise operations.

- In the _checkpointsLookup function, change the loop condition to low <= high instead of low < high to avoid unnecessary iterations.

 - using a mapping instead of EnumerableSet.AddressSet for _delegates. If the number of delegates is typically small and the order doesn't matter, a mapping can be more gas-efficient.

5 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol

- In functions like numUserGauges or numGauges, we can use the length() function of EnumerableSet instead of iterating through the set.

-In the updateUserBoost function, you can store the value of getUserGaugeBoost[user][gauge].userGaugeBoost in a local variable before the loop to avoid repeated storage access.

- In functions  decrementGaugesBoostIndexed, process multiple gauge decrements in a single loop iteration instead of individual operations.

6 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol

- In the convertToShares, convertToAssets, previewMint, and previewWithdraw functions, we can consider using fixed-size arrays instead of dynamic arrays to avoid unnecessary iterations.

- In the receiveAssets and sendAssets functions, batch external transfers of assets to minimize the number of external calls. Instead of transferring each asset individually, we can group the transfers and perform them in a single external call.

7 . Target :https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol

- In the constructor, the string.concat function is used to concatenate strings. String concatenation in Solidity can be expensive. Consider using string interpolation (string(abi.encodePacked(_name, " - Burned Hermes: Aggregated Gov + Yield + Boost"))) or precomputing the concatenated string before deploying the contract to reduce gas costs.

8 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol

-In the claimRewards function, we can avoid updating the rewardsAccrued state variable if the accrued value is zero. This can help reduce unnecessary state changes and save gas.

9 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

-  If we have multiple similar operations, try to batch them together into a single operation, if you have multiple timelock.executeTransaction calls, we can combine them into a single call by modifying the function signature and parameters.

- In the GovernorBravoDelegate contract, the BALLOT_TYPEHASH is currently defined as keccak256("Ballot(uint256 proposalId,uint8 support)"). we can consider changing the string type to bytes32 for more gas efficiency.

10 . Target :https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol

- In function like createIncentiveFromGauge and createIncentive, we can minimize unnecessary storage writes, instead of updating incentives[incentiveId].totalRewardUnclaimed, we could directly transfer the reward to the contract without recording the unclaimed rewards until a user claims them.

11 . Target :https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol

- In the function _isGlobalToken, we can store the result of getLocalTokenFromGlobal[_globalAddress][_fromChain] in a local variable instead of accessing it twice.

-The mappings getGlobalTokenFromLocal and getLocalTokenFromGlobal could potentially be merged into a single mapping.

12 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol

- in the _multicall function, we can aggregate multiple calls into a single call to the IMulticall contract, instead of making individual calls for each function.

13 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol

- In functions like executeSystemRequest, executeNoDeposit, etc., where you pass _data as a calldata parameter, avoid unnecessary data copying. Instead of slicing the _data array with _data[6:_data.length - PARAMS_GAS_IN], we can pass the _data array directly to the function being called, 
     _router.anyExecuteResponse(bytes1(_data[PARAMS_TKN_START]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId). This avoids creating a new array and reduces gas usage.


- in _bridgeInMultiple, w can consider avoiding multiple calls to bytes1, bytes20, bytes32, and uint16 functions by directly accessing the required data from the _data array. 

14 . Target :https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol

- There are multiple modifiers (requiresBridgeAgent, requiresBridgeAgentFactory, requiresCoreRouter) that check for certain conditions before executing functions. It's essential to optimize the order of these modifiers to minimize gas usage. Place modifiers that are likely to fail first to avoid unnecessary execution of subsequent modifiers.

-  In the _checkTimeLimit function, avoid redundant storage reads by saving the strategyDailyLimitRemaining[msg.sender][_token] value in a local variable.

- The isBridgeAgent, isBridgeAgentFactory, isStrategyToken, and isPortStrategy mappings could use a single bool variable to reduce gas consumption.

15  . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol

-The onlyOwner modifier can be placed before the payable modifier. This way, the ownership check is performed before processing any payable value, potentially saving some gas.

-Instead of using custom revert reasons like revert UnauthorizedCallerNotManager(), revert InvalidChainId(), etc., you can use predefined revert reasons. For example, you can replace revert UnauthorizedCallerNotManager() with revert("Unauthorized caller"). Using predefined revert reasons can help reduce the contract size and gas costs.

- In the _addGlobalToken and _addLocalToken functions, we encode and pack data that is then passed to IBridgeAgent(bridgeAgentAddress).callOut. If the called function does not require the data to be encoded or packed, we can directly pass the relevant arguments to the function, which can save gas costs.

16 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

- in the addLocalToken function, instead of calling ERC20(_underlyingAddress).name() and ERC20(_underlyingAddress).symbol(), we can assign name and symbol variables directly from the _underlyingAddress contract.

- In the addGlobalToken function, consider using transfer instead of send to transfer Ether. The transfer function will automatically revert the transaction if the transfer fails, preventing potential issues with the contract's behavior.

17 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

-we can combine nextCycle and paginationOffset into a single struct to reduce storage costs.

- In the _queueRewards function, there is a revert statement inside a loop. Since revert consumes more gas than require, we can replace it with require to optimize gas usage.

- The safeTransfer function in the contract to  performs additional checks during token transfers. If we are certain that the transfers won't fail, we can consider using the regular transfer function to save gas

18 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol

- In the Proposal struct, consider using a single dynamic array for targets, values, signatures, and calldatas instead of multiple arrays. This can reduce gas costs by avoiding separate storage allocations for each array.

-Use Enum Instead of String, Consider using an enum type instead of a string for the ProposalState. Enums are more efficient in terms of gas consumption compared to strings.

19 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

- If an external function call is simple and has a low gas cost, consider inlining the function call instead of using the _performCall function. This can reduce the overhead of cross-contract calls.

-In the _payExecutionGas function, the gas remaining is checked before transferring it. If the gas remaining is always positive, the gas check can be removed.

20. Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

- In function bridgeInMultiple and bridgeOutMultiple, multiple operations are performed in a loop. Instead of executing each operation individually, we can batch them together to reduce the number of iterations and gas costs.

