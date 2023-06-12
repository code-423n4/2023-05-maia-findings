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