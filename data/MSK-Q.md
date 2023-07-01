## [L-01] ```queueRewardsForCyclePaginated``` have  ```reentrancies``` that allow manipulation of the order or value of events.
##### Vulnerable Code 
``` solidity 
//External calls
minter.updatePeriod()
newRewards = minter.getRewards()
//Events Emitted After The Call
CycleStart(currentCycle,queued)
_queueRewards(gauges,currentCycle,lastCycle,queued)
```
This may cause issues for offchain components that rely on the values of events e.g. checking for the amount deposited to a bridge.
##### Vulnerable Code Link 
[FlywheelGaugeRewards.sol#L107](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L107)
##### Recommended Pattern 
Apply the ```check-effects-interactions``` pattern.

## [L-02] Avoid Using ```block.timestamp``` 
function ```delegateBySig``` using ```block.timestamp``` which can be manipulated by miners to exlpoit contracts
##### Vulnerable Code Snippet 
``` solidity
require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");
```
##### Vulnerable Code Link
[ERC20MultiVotes.sol#L364](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L364)
##### Recommended Mitigation Steps
Avoid relying on block.timestamp.
## [N-01] Variables Have Similar Names
Variable ```ERC20MultiVotes._delegate(address,address).newDelegatee``` is too similar to ``` ERC20MultiVotes._undelegate(address,address,uint256).newDelegates```
##### Vulnerable Code 
``` solidity 
    function _delegate(address delegator, address newDelegatee) internal virtual {

    uint256 newDelegates = _delegatesVotesCount[delegator][delegatee] - amount;
```
##### Link
[ERC20MultiVotes.sol#L161](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L161)
[ERC20MultiVotes.sol#L222](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L222)
##### Recommended Mitigation Steps 
Prevent variables from having similar names.


