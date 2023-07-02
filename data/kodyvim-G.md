# QA report
# `_payFallbackGas` should refund excess or account for excess.
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1061
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L831
Due to gas price fluctuation there might be excess `depositedGas` remaining within `_payFallbackGas` this excess could be accumulated or returned to the user.
since the checks in `_requiresFallbackGas` allows for excess.
```solidity
function _requiresFallbackGas(uint256 _depositedGas) internal view virtual {
        if (_depositedGas <= MIN_FALLBACK_RESERVE * tx.gasprice) revert InsufficientGas();
    }
```
and also the check in `_payFallbackGas` specifies that the `depositedGas` should be greater than `minExecCost`, there could be excess within these transaction.
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1069
```solidity
//Check if sufficient balance
        if (minExecCost > getDeposit[_depositNonce].depositedGas) {
            _forceRevert();
            return;
        }

        //Update user deposit reverts if not enough gas => user must boost deposit with gas
        getDeposit[_depositNonce].depositedGas -= minExecCost.toUint128();
```
## Recommendation:
Excess could be refunded or accounted for.

# Missing `checkdeviation` on init could expose initializer to unlimited slippage.
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L102
`checkDeviation` modifier checks if price has not moved a lot recently. most functions on `talosBaseStrategy.sol` has this modifier but this is missing on the `init` function. this could expose initializer to unlimited slippage.
## Recommendation:
Add `checkDeviation` modifier to `init`

# Redundant functionality should be removed
This functions has access control but they can never be called since no handle is provided within required contracts.
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L301
 `setCoreRouter` is never called from [coreRouter](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L19)
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L137
`setBooster` is never called from [owner contract](https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L18)
Recommendation
if the function are not required the should be removed

# operation can be refactored.
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1197C9-L1207C10
```solidity
if (flag == 0x00) {
            _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
            _reopenSettlemment(_settlementNonce);

            /// SETTLEMENT FLAG: 1 (single asset settlement)
        } else if (flag == 0x01) {
            _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
            _reopenSettlemment(_settlementNonce);

            /// SETTLEMENT FLAG: 2 (multiple asset settlement)
        }
```
can be refactored to
```solidity
if (flag  == 0x00 || flag == 0x01) {
	    _settlementNonce = uint32(bytes4(data[PARAMS_START_SIGNED:25]));
            _reopenSettlemment(_settlementNonce);
}
```