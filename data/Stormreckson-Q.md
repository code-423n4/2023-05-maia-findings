1-wrong event emitted in the TalosBaseStrategy.sol contract have a rebalance() function that incorrectly emits the Rerange event instead of the Rebalance event.
It is crucial to ensure that events are properly emitted in a contract to maintain transparency, interoperability, and user confidence in the system. 
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L311-L321

2- Emitting the Snapshot event with uninitialized values of `cache.amount0Desired` and `cache.amount1Desired` may produce unexpected or misleading data when listening to the emitted event.
To ensure accurate and expected behavior, it is important to initialize the `PoolVariables.Info` struct before utilizing its members in calculations.
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/libraries/PoolVariables.sol#L227-L230