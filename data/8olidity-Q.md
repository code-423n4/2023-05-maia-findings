## `_acceptAdmin` has redundant judgment

In the `_acceptadmin()` function, it will check whether the caller is `pendingAdmin` and judge whether `pendingAdmin ≠ address(0)`, but in fact, as long as it is judged that `caller == pengdingadmin`, there is no need to re-judge `msg.sender != address(0)`

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
function _acceptAdmin() external {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(
            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"//@audit 
        );
```