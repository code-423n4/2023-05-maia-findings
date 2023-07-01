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
## [L-03] Reentrancy in ```RootBridgeAgent.callOutAndBridgeMultiple```
##### Code Snippet 
``` solidity
//External Calls sending Eth
bytes memory data = abi.encodePacked(
            bytes1(0x02),
            _recipient,
            uint8(hTokens.length),
            settlementNonce,
            hTokens,
            tokens,
            _amounts,
            _deposits,
            _data,
            _manageGasOut(_toChain)
        );
// State variables written after the call(s)
 _createMultipleSettlement(_owner, _recipient, hTokens, tokens, _amounts, _deposits, data, _toChain);
```
The reentrancy is benign because it's exploitation would have the same effect as two consecutive calls.
##### Code Link 
[RootBridgeAgent.sol#L359-L370](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L359-L370)
##### Recommended Mitigation Steps
Apply the ```check-effects-interactions``` pattern.
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
## [N-02] ```ERC20Gauges._bur```n is never used and should be removed
##### Code 
``` solidity
function _burn(address from, uint256 amount) internal virtual override {
        _decrementWeightUntilFree(from, amount);
        super._burn(from, amount);
    }
```
dead_code is not used in the contract, and make the code's review more difficult.
##### Code Link
[ERC20Gauges.sol#L485](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L485)
##### Recommended Mitigation Step
Remove unused code.
## [N-03] ```RootBridgeAgentExecutor.PARAMS_AMT_OFFSET```  is never used in ```RootBridgeAgentExecutor```
Unused state variable.
##### Code
``` solidity
uint8 internal constant PARAMS_AMT_OFFSET = 64;
```
##### Code Link
[RootBridgeAgentExecutor.sol#L57](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L57)
##### Recommended Mitigation Steps
Remove unused state variables.
