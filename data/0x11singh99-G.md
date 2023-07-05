## [G‑01] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read
Note: view functions also added since they are used in state changing functions

_3 instances - 1 files:_

### Instance#1: _flywheelRewards_ can be cached(saves 100 Gas)

```solidity
File : /src/rewards/base/FlywheelCore.sol

126:  uint256 oldRewardBalance = rewardToken.balanceOf(address(flywheelRewards));
127:        if (oldRewardBalance > 0) {
128:          rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance);
129:       }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L126C7-L129C10

### Instance#2: _flywheelBooster_ instance can be cached

```solidity
File : /src/rewards/base/FlywheelCore.sol
 164:   uint256 supplyTokens = address(flywheelBooster) != address(0)
 165:                ? flywheelBooster.boostedTotalSupply(strategy)
 166:                : strategy.totalSupply();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L164C11-L167C1

Recommended changes :

```diff
    //@audit gas flywheelBooster can be cached
- 164:   uint256 supplyTokens = address(flywheelBooster) != address(0)
- 165:                ? flywheelBooster.boostedTotalSupply(strategy)
- 166:                : strategy.totalSupply();
+ 164:   IFlywheelBooster _flywheelBooster=flywheelBooster;
+ 165:   uint256 supplyTokens = address(_flywheelBooster) != address(0)
+ 166:                ? _flywheelBooster.boostedTotalSupply(strategy)
+ 167:                : strategy.totalSupply();
```

### Instance#3: _flywheelBooster_ instance can be cached

```solidity
File : /src/rewards/base/FlywheelCore.sol

 196:   uint256 supplierTokens = address(flywheelBooster) != address(0)
 197:             ? flywheelBooster.boostedBalanceOf(strategy, user)
 198:              : strategy.balanceOf(user);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L196C8-L198C40

Recommended Changes : implement solution like instance#2 of [G-01]

## [G-02] Use assembly for address(0) comparison

### saves 65 gas each time

_2 instances - 1 files:_

### Instance#1

```solidity
File : /src/rewards/base/FlywheelCore.sol

 164:   uint256 supplyTokens = address(flywheelBooster) != address(0)
 165:                ? flywheelBooster.boostedTotalSupply(strategy)
 166:                : strategy.totalSupply();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L164

Recommended Changes :

```diff
- 164:   uint256 supplyTokens = address(flywheelBooster) != address(0)
- 165:                ? flywheelBooster.boostedTotalSupply(strategy)
- 166:                : strategy.totalSupply();
+ 164:      bool isAddressZero;
+ 165:       assembly {
+ 166:           isAddressZero := eq(address(flywheelBooster), 0)
+ 167:       }
+ 168:     uint256 supplyTokens = isAddressZero
+ 169:               ? strategy.totalSupply()
+ 170:                 : flywheelBooster.boostedTotalSupply(strategy)
```

### Instance#2

```solidity
File : /src/rewards/base/FlywheelCore.sol

196:   uint256 supplierTokens = address(flywheelBooster) != address(0)
197:             ? flywheelBooster.boostedBalanceOf(strategy, user)
198:              : strategy.balanceOf(user);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L196C8-L198C40

Recommended Changes :

```diff
- 196:   uint256 supplierTokens = address(flywheelBooster) != address(0)
- 197:             ? flywheelBooster.boostedBalanceOf(strategy, user)
- 198:              : strategy.balanceOf(user);
+ 196:      bool isAddressZero;
+ 197:       assembly {
+ 198:           isAddressZero := eq(address(flywheelBooster), 0)
+ 199:       }
+ 200:     uint256 supplyTokens = isAddressZero
+ 201:            ? strategy.balanceOf(user)
+ 202:                :flywheelBooster.boostedBalanceOf(strategy, user)
```

## [G-03] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

The Solidity compiler does not optimize the <x> += <y> operation for state variables. This means that every time the state variable is updated, the entire value is copied to memory, the operation is performed, and then the value is copied back to storage. This is expensive and can be avoided by using <x> = <x> + <y> instead.

```solidity

```

## [G-04] Use separate if's for different instead of using || to merge in one and sort them low to high gas consuming conditions to fail early with less gas(if failing)

Saves 16 Gas if first condition is true
Saves 1 Gas if first condition is false

_1 instances - 1 files:_

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol
47:         function addAsset(address rewardsContract, address asset) external onlyOwner {
48:        if (_isAsset[asset] || _isRewardsContract[rewardsContract]) revert ErrorAddingAsset();
49:       _isAsset[asset] = true;
50:     _isRewardsContract[rewardsContract] = true;
             ... }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L48

Recommended Changes:

```diff
       function addAsset(address rewardsContract, address asset) external onlyOwner {
- 48:        if (_isAsset[asset] || _isRewardsContract[rewardsContract]) revert ErrorAddingAsset();
+ 48:       if (_isAsset[asset] ) revert ErrorAddingAsset();
+ 49:       if (_isRewardsContract[rewardsContract]) revert ErrorAddingAsset();
         _isAsset[asset] = true;
        _isRewardsContract[rewardsContract] = true;
       ... }
```
