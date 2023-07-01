## 1. `> 0` costs more gas than != 0 when used on a uint in a require() statement

***There are 2 instances of this issue:***

``` 
File: src/erc-4626/ERC4626MultiToken.sol
52:            require(_weights[i] > 0);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52

``` 
File: src/ulysses-amm/UlyssesToken.sol
47:        require(_weight > 0);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47

## 2. Try using bit shifting when Dividing by 2 to save gas
`<x> / 2` is the same as `<x> >> 1`.

Found Seven instances using `<x> / 2`.

``` 
File: src/erc-20/ERC20MultiVotes.sol
82:        return (a & b) + (a ^ b) / 2;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol

``` 
File: src/talos/libraries/PoolVariables.sol
247:            ? int256((cache.amount0Desired - cache.amount0) / 2)
248:            : int256((cache.amount1Desired - cache.amount1) / 2);
251:        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (_strategy.priceImpactPercentage() / 2)) / GLOBAL_DIVISIONER;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/libraries/PoolVariables.sol

``` 
File: src/ulysses-amm/UlyssesPool.sol
312:        if (_fees.lambda1 + _fees.lambda2 != DIVISIONER / 2) revert InvalidFee();
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-amm/UlyssesPool.sol

``` 
File: src/ulysses-omnichain/RootBridgeAgent.sol
677:        uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;
720:             uint160 exactSqrtPriceImpact = (sqrtPriceX96 * (priceImpactPercentage / 2)) / GLOBAL_DIVISIONER;
```

https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/RootBridgeAgent.sol