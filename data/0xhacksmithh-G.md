### [Gas-01] By `emiting` one step above, Instead `Caching` `state variable` to `memory`
According to my testing ~3200 gas can be save without casting storage to memory instead emiting one step above.
```solidity
    function setMaxGauges(uint256 newMax) external onlyOwner {
-        uint256 oldMax = maxGauges;
-        maxGauges = newMax; 

-        emit MaxGaugesUpdate(oldMax, newMax);

+        emit MaxGaugesUpdate(maxGauges, newMax);
+        maxGauges = newMax; 


    }
```

```solidity
    function setMaxDelegates(uint256 newMax) external onlyOwner {
-        uint256 oldMax = maxDelegates;
-        maxDelegates = newMax;

-        emit MaxDelegatesUpdate(oldMax, newMax); 

+        emit MaxDelegatesUpdate(maxDelegates, newMax);
+        maxDelegates = newMax;

    }
```

*Instances(Multiple Instances)*

```
File : erc-20/ERC20Gauges.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L456-L459
```
```
File : erc-20/ERC20MultiVotes.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L97-L100
```

### [Gas-02] Use nested `if` and avoid multiple check combinations
i.e instead of using `if(cond1 && cond2){}`

Use as follows
```solidity
if(cond1){
    if(cond2){
        .......
        .......}
}
```

```solidity
        if (added && _userGauges[user].length() > maxGauges && !canContractExceedMaxGauges[user]) { 
            revert MaxGaugeError();
        }
```
```solidity
if (canExceedMax && account.code.length == 0) revert Errors.NonContractError();
```


```
File : erc-20/ERC20Boost.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L211-L213
```
```
File : erc-20/ERC20MultiVotes.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L105
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L250
```

### [Gas-03] Way of `Struct` update to storage could be optimizable.
```solidity
        getUserGaugeBoost[user][msg.sender] =
            GaugeState({userGaugeBoost: userGaugeBoost, totalGaugeBoost: totalSupply.toUint128()});
```


```
File : erc-20/ERC20Boost.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L131-L132
```

### [Gas-04] Creation of `memory` variable should occur outside of loop, and get overriden in each iteration.
```solidity
+ address delegatee;
+ uint256 delegateVotes
  for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {
-           address delegatee = delegateList[i];  // @audit
-           uint256 delegateVotes = _delegatesVotesCount[user][delegatee]; 

+           delegatee = delegateList[i];  // @audit
+           delegateVotes = _delegatesVotesCount[user][delegatee];
            // Minimum of votes delegated to delegatee and unused votes of delegatee
            uint256 votesToFree = FixedPointMathLib.min(delegateVotes, userUnusedVotes(delegatee));                 // Skip if votesToFree is zero
            if (votesToFree != 0) {
                totalFreed += votesToFree;

                if (delegateVotes == votesToFree) {
                    // If all votes are freed, remove delegatee from list
                    require(_delegates[user].remove(delegatee)); // Remove from set. Should never fail.
                    _delegatesVotesCount[user][delegatee] = 0;
                } else {
```


```
File : erc-20/ERC20MultiVotes.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L329-L330
```


### [Gas-05] `gaugeCycleLength` could be cached to memory
```solidity
function _getGaugeCycleEnd() internal view returns (uint32) {
        uint32 nowPlusOneCycle = block.timestamp.toUint32() + gaugeCycleLength; 
        unchecked {
            return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // cannot divide by zero and always <= nowPlusOneCycle so no overflow
        }
```

```
File : erc-20/ERC20Gauges.sol
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L75
```