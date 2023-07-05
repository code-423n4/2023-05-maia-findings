1 . Target :https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

-Uninitialized Array in createPools Function

Issue: The poolIds array is not initialized before assigning values to its elements in the createPools function.

Solution: Before assigning values to the poolIds array, initialize it with the appropriate length. Add the following line before the loop:

uint256[] memory poolIds = new uint256[](length);


-Memory Array Declaration in createPools Function

Issue: The poolIds array is declared as a memory array, but it should be a dynamic storage array to store the pool IDs.

Solution: Change the declaration of poolIds from memory to storage in the function signature:
function createPools(
    ERC20[] calldata assets,
    uint8[][] calldata weights,
    address owner
) external returns (uint256[] memory poolIds)


function createPools(
    ERC20[] calldata assets,
    uint8[][] calldata weights,
    address owner
) external returns (uint256[] memory poolIds)


- Logic Error in createPools Function
Issue: The second loop in the createPools function has a logic error. The inner loop should iterate over the weights[i] array, not the length variable.

Solution: Replace length with weights[i].length in the inner loop condition:

for (uint256 j = 0; j < weights[i].length; j++)


-Missing Increment Statement in createPools Function
Issue: The ++i increment statement is missing at the end of the loop body in the createPools function.

Solution: Add ++i at the end of the loop to increment i

for (uint256 i = 0; i < length; i++) {
    // Existing code...

    ++i;
}


-Incorrect Array Initialization in createToken Function
Issue: The destinations array is initialized with a fixed size of length, but the length of the poolIds array may vary.

Solution: Initialize the destinations array with the same length as the poolIds array to avoid potential out-of-bounds errors. 

Replace the line:
address[] memory destinations = new address[](length);

with
address[] memory destinations = new address[](poolIds.length);


2 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

-The error in the following lines:
if (globalToken == address(0)) revert UnknownUnderlyingToken();


The UnknownUnderlyingToken() is not a valid exception or revert reason. we should use revert("...") to provide a custom error message. For example:

if (globalToken == address(0)) revert("Unknown underlying token");


-The error in the following lines
revert UnknownToken();


Similar to the previous error, UnknownToken() is not a valid exception or revert reason. we should use revert("...") to provide a custom error message. For example:

revert("Unknown token");


3 . Target : https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

- Incorrect Gauge Cycle Calculation
Location: constructor and queueRewardsForCycle functions

Description: The gaugeCycle is being calculated incorrectly during the contract deployment and updating cycle in the queueRewardsForCycle function. The calculation uses block.timestamp.toUint32() to get the current timestamp as a uint32, but this can cause issues when the timestamp overflows. The correct way to calculate the gaugeCycle should use the block.timestamp directly and perform integer division.

Impact: Incorrect gauge cycle calculation may lead to incorrect cycle tracking and rewards distribution.

Suggested Fix:
// Replace the gaugeCycle calculation in constructor and queueRewardsForCycle functions

// Correctly calculate gaugeCycle using integer division
gaugeCycle = block.timestamp / gaugeCycleLength;


-Incorrect Pagination Handling
Location: queueRewardsForCyclePaginated function

Description: The pagination logic in the queueRewardsForCyclePaginated function is not working correctly. The function should only update the paginationOffset when the entire cycle is not completed. However, it is currently updating the offset even when the cycle is complete.

Impact: The rewards may be queued incorrectly and lead to misallocation.

Suggested Fix:
// Replace the paginationOffset update in queueRewardsForCyclePaginated function

// Move this line inside the else block
paginationOffset = offset + numRewards.toUint32();


- Redundant Code
Location: queueRewardsForCyclePaginated function

Description: The variable queued is defined and assigned the value of nextCycleQueuedRewards, but it is not used anywhere in the function. It can be removed to improve code clarity.

Impact: Redundant code has no functional impact but may confuse readers and developers.

Suggested Fix:
// Remove the unused variable
uint112 queued = nextCycleQueuedRewards;

-Overflow Risk
Location: _queueRewards function

Description: The contract uses a for loop to iterate over the gauges array, which could potentially cause an out-of-gas error if the array is too large. Additionally, there is no check for array bounds, which can lead to accessing invalid memory if the array is not properly initialized.

Impact: The contract may be vulnerable to out-of-gas errors and invalid memory access.

Suggested Fix:
Update the for loop to use a while loop, and add a check for the array bounds.
// Replace the for loop in _queueRewards function

uint256 i = 0;
while (i < gauges.length) {
    ERC20 gauge = ERC20(gauges[i]);

    // Add a check for array bounds
    require(gauge != address(0), "Invalid gauge address");

    // ... existing loop logic ...

    i++;
}



