## 1. `Require()` statements that are the same in a single contract file should be refactored to a modifier or function


***There are 2 instances of this issue:***

``` 
File: src/erc-4626/UlyssesERC4626.sol
50:        require(assets != 0, "ZERO_ASSETS");

75:        require(assets != 0, "ZERO_ASSETS");
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L50

```
File: src/ulysses-omnichain/RootPort.sol
129:        require(_setup, "Setup ended.");

145:        require(_setup, "Setup ended.");
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L129