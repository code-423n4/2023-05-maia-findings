# Gas Optimization Report

## 1. Split `performUpkeep` into two functions
*[TalosManager.sol#L112](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L112)*

### Description
According to the documentation, the `checkUpkeep()` and `performUpkeep()` functions are intended to be called by keeper bots. The keeper bot will first call `checkUpkeep()` to determine if any upkeep is required. If so, it will call `performUpkeep()`. The problem with this strategy is that there is no consideration of whether only a `rebalance()` or a `rerange()` upkeep task is needed. The functions `getRebalance()` and `getRerange()` are called twice, once in `checkUpkeep()` to determine if any upkeep is needed and again at `performUpkeep()` to determine which upkeep is required. There is an opportunity to distinguish between `rebalance()` and `rerange()` and save gas by eliminating duplicate calls.

### Remediation
The remediation can be seen as a three-step process.

1. In `checkUpkeep()`, [TalosManager.sol#L91](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L91), break the `upkeepNeeded` flag into two distinct flags:

    - `bool rebalanceNeeded` associated with `getRebalance()`
    - `bool rerangeNeeded` associated with `getRerange()`

2. Break `performUpkeep()`, [TalosManager.sol#L112](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L112), into two separate functions:

    - `performRebalance()` which calls the `strategy.rebalance()` function.
    - `performRerange()` which calls the `strategy.rerange()` function.

3. Move the logic to determine what upkeep is needed to the keeper bot. If `rebalanceNeeded` is true, call `performRebalance().` If `rerangeNeeded` is true, call `performRerange().`

This refactor saves gas in the `TalosManager` contract by eliminating the duplicate checks on `getRebalance()` and `getRerange().`
