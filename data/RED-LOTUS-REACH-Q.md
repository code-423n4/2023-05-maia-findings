# QA Report

### Issues Template

| Letter | Name | Description |
| --- | --- | --- |
| L | Low risk | Potential risk |
| G | Gas | Gas Optimization |

| Total Found Issues | 7 |
| --- | --- |

### Low Risk Template

| Count | Explanation | Instances |
| --- | --- | --- |
| [L-01] | Potential Misuse of transferOwnership | 1 |
| [L-02] | Potential Price Manipulation via Flash Loans | 1 |
| [L-03] | Potential Denial of Service Due to large number of gauges | 1 |
| [L-04] | User will never enter the if statement when they execute the _checkTimeLimit function multiple times | 1 |
| [L-05] |  Incomplete Information Emitted in Events  | 1 |
| [L-06] | Asset Loss When Redeeming Small Amounts | 1 |
| [L-07] | Lack of OnlyOwner Modifier in claimProtocolFees Function | 1 |

| Total Low Risk Issues | 8 |
| --- | --- |

### Gas Template

| Count | Explanation | Instances |
| --- | --- | --- |
|  |  |  |

| Total Gas Optimizations |  |
| --- | --- |

## [L-01] Potential Misuse of `transferOwnership`

### Impact

The `renounceOwnership` function is overridden to prevent accidental renunciation of ownership, but the `transferOwnership` function could still be potentially misused to transfer ownership to the zero address or any other unintended address. This could lead to the smart contract becoming ownerless or falling into the wrong hands, leading to a loss of control over the contract’s functionalities, including its funds if any.

### Proof of Concept

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L112-L115](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L112-L115)

In the code snippet provided, we can see that the `renounceOwnership` function is overridden to disable its functionality:

```solidity
/// @notice Function being overrriden to prevent mistakenly renouncing ownership.
function renounceOwnership() public payable override onlyOwner {
    revert("Cannot renounce ownership");
}
```

However, the `transferOwnership` would still allow the contract owner to transfer ownership to an arbitrary address, including the zero address (address(0)).

### Recommended Mitigation

To prevent potential misuse, you should add explicit checks in the transferOwnership function to prevent transferring ownership to the zero address or any other invalid address as per your business logic. The revised function could look like this:

```solidity

function transferOwnership(address newOwner) public override onlyOwner {
    require(newOwner != address(0), "New owner cannot be the zero address");
    // Add other necessary checks as per your use case
    super.transferOwnership(newOwner);
}
```

## [L-02] Potential Price Manipulation via Flash Loans

### Impact

The current implementation uses the spot price of assets directly from the Uniswap V3 pool. This is risky because the pool price can be temporarily manipulated with a flash loan attack, which can result in transactions that are unfair or unfavorable for users. A flash loan attack can happen when someone borrows a large amount of assets and then uses these assets to manipulate the price in a liquidity pool, all within a single transaction.

### Proof of Concept

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L674-L696](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L674-L696)

```solidity

(uint160 sqrtPriceX96,,,,,,) = IUniswapV3Pool(poolAddress).slot0();

... some code here

try IUniswapV3Pool(poolAddress).swap(
    address(this),
    zeroForOneOnInflow,
    int256(_amount),
    sqrtPriceLimitX96,
    abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))
) returns (int256 amount0, int256 amount1)
```

In this code, the spot price `sqrtPriceX96` is taken directly from the Uniswap V3 pool. This price is then used to determine the amounts for the swap.

### Recommended Mitigation

To mitigate this risk, it is recommended to use a Time Weighted Average Price (TWAP) instead of the spot price for swaps. TWAP is more resistant to price manipulation as it averages the price over a certain period, making it harder for an attacker to significantly influence the price within a single transaction. Uniswap V3 provides a function to calculate the TWAP, which you can use in your smart contract.

## [L-03] Potential Denial of Service Due to large number of gauges

### Impact

The current implementation of the gauges function in the `ERC20Gauges` contract, and its usage in the `FlywheelGaugeRewards` contract, could potentially lead to a Denial of Service (DoS) if there are too many gauges. The gauges function returns all gauge addresses in an array, which is copied to memory. If there are a large number of gauges, it could consume excessive gas and potentially exceed the block gas limit, rendering the function uncallable.

### Proof of Concept

 [FlywheelGaugeRewards](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L96) [ERC20Gauges](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#LL107C1-L109C6) [Enumerable Set](https://github.com/code-423n4/2023-05-maia/blob/main/lib/EnumerableSet.sol#L134-L144)

```solidity
// FlywheelGaugeRewards.sol
address[] memory gauges = gaugeToken.gauges(); // @audit this is dangerous, since it copies all the gauges._values (addresses) into memory

// ERC20Gauges.sol
function _values(Set storage set) private view returns (bytes32[] memory) {
    return set._values;
}
```

In this code, the gauges function, which is called in `FlywheelGaugeRewards`, retrieves all the gauges from the `ERC20Gauges` contract. This can be problematic if there are many gauges, as it could consume too much memory and gas. 

### Recommended Mitigation

To mitigate this risk, consider using a pattern such as pagination to retrieve a limited number of gauges at a time. This would avoid excessive gas consumption and potential DoS even if there are a large number of gauges. Here is an example of how pagination can be implemented:

```solidity
function getGauges(uint256 startIndex, uint256 endIndex) public view returns (address[] memory) {
    require(endIndex >= startIndex, "End index must be greater than or equal to start index");
    uint256 length = endIndex.sub(startIndex);
    address[] memory gauges = new address[](length);
    for (uint256 i = startIndex; i < endIndex; i++) {
        gauges[i - startIndex] = _values[i];
    }
    return gauges;
}
```

## [L-04] User will never enter the if statement when they execute the `_checkTimeLimit` function multiple times

### Impact

The protocol allows users to check if a port strategy has reached its daily management limit:

```jsx
/**
 * @notice Internal function to check if a Port Strategy has reached its daily management limit.
 *   @param _token address being managed.
 *   @param _amount of token being requested.
 */
function _checkTimeLimit(address _token, uint256 _amount) internal {
    if (block.timestamp - lastManaged[msg.sender][_token] >= 1 days) {
        strategyDailyLimitRemaining[msg.sender][_token] = strategyDailyLimitAmount[msg.sender][_token];
    }
    strategyDailyLimitRemaining[msg.sender][_token] -= _amount;
    lastManaged[msg.sender][_token] = block.timestamp;
}
```

The `_checkTimeLimit` function checks whether `msg.sender's` `_token` has been `lastManaged` in a time frame of more than or equal to 1 day. 

If the user checks the time limit of a token multiple times, in a time frame of less than 24 hours since the *last* time it was called,  `strategyDailyLimitRemaining` will never update.  That is because  `_amount` is deducted from `strategyDailyLimitRemaining`, meaning it decreases the daily limit upon repeated executions of the function, eventually preventing further calls as further transactions revert, without any explicit warning.

### Proof of Concept

[https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L194-L196](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L194-L196)

### Recommended Mitigation

Use `lastManaged[msg.sender][_token] = block.timestamp;` within the `if` statement.

## [L-05] Incomplete Information Emitted in Events

### Impact

The currently designed smart contract events do not emit the previous values of the state changes, which could lead to a lack of visibility and traceability when changes are made. This can make it difficult for other developers or auditors to follow the contract’s history and could potentially hinder troubleshooting or understanding of the contract’s behavior over time. 

### Proof of Concept

Below are examples of events that do not emit the previous values of the state changes:

```solidity

emit UpdateUserBoost(user, userGaugeBoost);
emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
event IncrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);
event DecrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);
```

In the above examples, only the new values resulting from the state changes are recorded. Previous state data is not included in the emitted event, which limits the amount of historical information available for these changes.

### Recommended Mitigation

It is recommended to modify the events in a way that they also record and emit the previous values during a state change. This would help maintain a more complete history of state changes and improve the contract’s traceability. Here’s an example of how you can include the previous value in an event:

```solidity
event UpdateUserBoost(address indexed user, uint256 oldValue, uint256 newValue);
```

In this event, `oldValue` is the previous value before the state change, and `newValue` is the value after the state change.

## [L-06] Asset Loss When Redeeming Small Amounts

### Impact

When dealing with redeeming shares using `redeem` within `TalosBaseStrategy`, it has been observed to result in unexpected and significant asset loss relative to the amount deposited. The asset loss seems more pronounced with small amounts and may lead to potential economic and accounting exploits or financial losses for users. 

### Proof of Concept

[https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L238-L29](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L238-L291)

Two test cases have been written to illustrate this issue. In the first case, if shares are redeemed in smaller amounts across multiple transactions, the user seems to get a lower return. In the second case, if all shares are redeemed at once, the user receives an expected return.

```solidity
function testWithdrawSmallAmount() public {
    uint256 amount0Desired = 10;

    (uint256 totalShares,uint256 amount0_, uint256 amount1_) = deposit(amount0Desired, amount0Desired, user1);
    assertEq(amount0_,10);
    assertEq(amount1_,6);

    uint256 sharesRedeemed = totalShares/5;
    assertEq(sharesRedeemed*5, totalShares);

    for(int i = 0; i < 5; i++){
        (uint256 amount0, uint256 amount1) =  withdraw(sharesRedeemed, user1);
        assertEq(amount0,1);
        assertEq(amount1,1);
    }
function testWithdrawAllSmall() public {
    uint256 amount0Desired = 10;

    (uint256 totalShares,uint256 amount0_,uint256 amount1_) = deposit(amount0Desired, amount0Desired, user1);
    assertEq(amount0_,10);
    assertEq(amount1_,6);

    (uint256 amount0, uint256 amount1) =  withdraw(totalShares, user1);
    assertEq(amount0,9);
    assertEq(amount1,5);
}

```

In `testWithdrawSmallAmount()`, redeeming 1/5 of the shares 5 times yields 1 of each token every time, totaling 5 of each. In contrast, in `testWithdrawAllSmall()`, redeeming all shares at once yields 9 of one token and 5 of another, revealing an apparent loss compared to the expected amounts. That is a 55% loss of assets.

### Recommended Mitigation

Ensure that users do not withdraw only a small amount of shares.

## [L-07] Lack of OnlyOwner Modifier in claimProtocolFees Function

### Impact

The `claimProtocolFees` function, located within the admin logic section of the contract, lacks the `onlyOwner` modifier. This omission exposes a potential vulnerability and creates a vector for unauthorized access and manipulation of the protocol fees. Although it may not directly result in a loss of funds, the absence of the `onlyOwner` modifier can lead to unauthorized changes in the contract’s state and disrupt the intended distribution and utilization of the protocol fees. 

### Proof of Concept

While no specific exploit scenario or attacker contract is presented, the lack of the `onlyOwner` modifier within the `claimProtocolFees` function enables any address to call the function and transfer the protocol fees to the owner. As a result, an unauthorized party could manipulate the state of the contract and potentially disrupt the balance and intended distribution of the protocol fees. This introduces a vector of attack and compromises the control and governance mechanisms in place. 

### Recommended Mitigation

To address this vulnerability and enhance the security of the contract’s admin logic, it is strongly recommended to add the `onlyOwner` modifier to the `claimProtocolFees` function. By doing so, only the contract owner will have the authority to execute the function and transfer the protocol fees.