### Gas Optimizations List

| Number | Optimization Details                                                                                                                        | Instances |
| :----: | :------------------------------------------------------------------------------------------------------------------------------------------ | :-------: |
| [G-01] | State variables should be cached in stack variables rather than re-reading them from storage variables                                      |     7     |
| [G-02] | Use assembly for address(0) comparison                                                                                                      |     4     |
| [G-03] | <x> += <y> / <x> -= <y> costs more gas than <x> = <x> + <y> / <x> = <x> - <y> for state variables                                           |     4     |
| [G-04] | Use separate if's for different checks instead of using \|\| to merge multiple checks into one                                              |     1     |
| [G-05] | Use nested if and, avoid multiple check combinations                                                                                        |     3     |
| [G-06] | Missing `zero-address` check in `constructor`                                                                                               |     4     |
| [G-07] | Write for loops in more gas efficient way                                                                                                   |     1     |
| [G-08] | Use unchecked{} whenever underflow/overflow not possible saves 130 gas each time                                                            |     6     |
| [G-09] | Use separate require's for different checks instead of merging multiple checks into one                                                     |     1     |
| [G-10] | Using storage instead of memory for structs/arrays saves gas                                                                                |     2     |
| [G-11] | State variables can be updated once in final rather than re-writing same variable more than once in one function. Saves unnecessary SSTORE. |     2     |

Total 11 issues

## [G‑01] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read
Note: view functions also added since they are used in state changing functions

_7 instances - 2 files:_

### Instance#1: `flywheelRewards` can be cached(saves 100 Gas)

```solidity
File : /src/rewards/base/FlywheelCore.sol

126:  uint256 oldRewardBalance = rewardToken.balanceOf(address(flywheelRewards));
127:        if (oldRewardBalance > 0) {
128:          rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance);
129:       }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L126C7-L129C10

### Instance#2: `flywheelBooster` instance can be cached

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

### Instance#3: `flywheelBooster` instance can be cached

```solidity
File : /src/rewards/base/FlywheelCore.sol

 196:   uint256 supplierTokens = address(flywheelBooster) != address(0)
 197:             ? flywheelBooster.boostedBalanceOf(strategy, user)
 198:              : strategy.balanceOf(user);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L196C8-L198C40

Recommended Changes : implement solution like instance#2 of [G-01]

### Instance#4: `liquidity` can be cached instead of re-reading from storage(in line 216 for += liquidity will be read again) (saves 100 Gas)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

215:   shares = supply == 0 ? liquidityDifference * MULTIPLIER : (liquidityDifference * supply) / liquidity;
216:         liquidity += liquidityDifference;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L215C9-L216C42

### Instance#5: State var. `liquidity` can be cached instead of re-reading from storage(in line 277 for -= liquidity will be read again )(saves 100 Gas)

```solidity
File: src/talos/base/TalosBaseStrategy.sol
261:    uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);
     ...

277:     liquidity -= liquidityToDecrease;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L261C8-L278C10

### Instance#6: `tokenId` can again be cached after doRerange() change instead of re-reading that from storage(at line 305 and 307) (saves 100 Gas)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

304:         (uint256 amount0, uint256 amount1) = doRerange();
        //@audit gas  tokenId can again be cached after doRerange() change
 305:       emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

307:      afterRerange(tokenId); // tokenId changed in doRerange
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L304C8-L307C63

### Instance#7: `tokenId` can again be cached after doRebalance() change instead of re-reading that from storage 2 times (at line 318 and 320) (saves 100 Gas)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

317:         (uint256 amount0, uint256 amount1) = doRebalance();
        //@audit gas  tokenid can again be cached after do reabalance chenge
 318:       emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

 320:       afterRerange(tokenId); // tokenId changed in doRerange
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L317C8-L320C63

## [G-02] Use assembly for address(0) comparison

### saves 65 gas each time

_4 instances - 2 files:_

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

### Instance#3:

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

         //@audit gas address 0 check using assembly
 125:            if (rewardsDepot != address(0)) {
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L125
Recommended Changes : Compare with address(0) like above recommendation of
instance#2 using assembly

### Instance#4:

```solidity
File: src/talos/base/TalosBaseStrategy.sol

         //@audit gas address 0 check using assembly
253:   if (receiver == address(0)) revert ReceiverIsZeroAddress();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L253

## [G-03] <x> += <y> / <x> -= <y> costs more gas than <x> = <x> + <y> / <x> = <x> - <y> for state variables

The Solidity compiler does not optimize the <x> += <y> / <x> -= <y> operation for state variables. This means that every time the state variable is updated, the entire value is copied to memory, the operation is performed, and then the value is copied back to storage. This is expensive and can be avoided by using <x> = <x> + <y> / <x> = <x> - <y> instead.

_4 instances - 3 files:_

### Instance#1:

```diff
File: src/rewards/rewards/FlywheelGaugeRewards.sol

- 134 :   nextCycleQueuedRewards += uint112(newRewards);
+ 134 :   nextCycleQueuedRewards = nextCycleQueuedRewards+ uint112(newRewards);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L134C12-L134C59

### Instance#2 :

```diff
File: src/talos/boost-aggregator/BoostAggregator.sol

- 121:    protocolRewards += newProtocolRewards;
+ 121:    protocolRewards = protocolRewards+newProtocolRewards;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L121C9-L121C51

### Instance#3 :

```diff
 File: src/talos/base/TalosBaseStrategy.sol
- 216:    liquidity += liquidityDifference;
+ 216:    liquidity = liquidity + liquidityDifference;

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L216C9-L217C1

### Instance#4 :

```diff
 File: src/talos/base/TalosBaseStrategy.sol
- 277:     liquidity -= liquidityToDecrease;
+ 277:      liquidity = liquidity - liquidityToDecrease;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L277

## [G-04] Use separate if's for different checks instead of using || to merge in one and sort them also low to high gas consuming conditions to fail early with less gas(if failing)

_1 instance - 1 file:_

### Instance#1:

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol
47:         function addAsset(address rewardsContract, address asset) external onlyOwner {
48:        if (_isAsset[asset] || _isRewardsContract[rewardsContract]) revert ErrorAddingAsset();
49:       _isAsset[asset] = true;
50:     _isRewardsContract[rewardsContract] = true;
             ... }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L48

Recommended Changes: Conditions in _if_ can be write in 2 separate if's

```diff
       function addAsset(address rewardsContract, address asset) external onlyOwner {
- 48:        if (_isAsset[asset] || _isRewardsContract[rewardsContract]) revert ErrorAddingAsset();
+ 48:       if (_isAsset[asset] ) revert ErrorAddingAsset();
+ 49:       if (_isRewardsContract[rewardsContract]) revert ErrorAddingAsset();
         _isAsset[asset] = true;
        _isRewardsContract[rewardsContract] = true;
       ... }
```

## [G-05] Use nested if and, avoid multiple check combinations

Using nested if is cheaper gas wise than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

_3 instances - 2 files:_

### Instance#1-2:

```solidity
File: src/talos/base/TalosBaseStrategy.sol

151:    if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
            ...

212:   if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L151

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L212

Recommended Changes: Conditions in _if_ can be write in 2 nested if's to avoid && writing

```diff
- 151:    if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
+ 151:    if (amount0 == 0) {
+          if (amount1 == 0) revert AmountsAreZero();
+        }
   ...

- 212:    if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
+ 212:    if (amount0 == 0) {
+          if (amount1 == 0) revert AmountsAreZero();
+        }
```

### Instance#3 :

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
210 :  if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {
211:             return 0;
212:        }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L210C9-L212C10

Recommended Changes: Convert 3 checks from 1 if into 3 if's , nested for && and consecutive if's for || operator

```diff
- 210 :  if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {
- 211:             return 0;
- 212:        }
+     if (queuedRewards.priorCycleRewards == 0) {
+            if (queuedRewards.cycleRewards == 0) return 0;
+            if (incompleteCycle) return 0;
+        }
```

## [G-06] Missing `zero-address` check in `constructor`

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly. It also wast gas as it requires the redeployment of the contract.

Recommendation : Check input address if it is zero or not using assembly to save more gas see: [G-02]

_4 instances - 4 files:_

### Instance#1: `_strategy` can be checked for address(0)

```solidity
File: src/talos/TalosManager.sol
44:  constructor(
45:        address _strategy,
        int24 _ticksFromLowerRebalance,
        int24 _ticksFromUpperRebalance,
        int24 _ticksFromLowerRerange,
        int24 _ticksFromUpperRerange
    ) {
51:        strategy = ITalosBaseStrategy(_strategy);
        ticksFromLowerRebalance = _ticksFromLowerRebalance;
        ticksFromUpperRebalance = _ticksFromUpperRebalance;
        ticksFromLowerRerange = _ticksFromLowerRerange;
        ticksFromUpperRerange = _ticksFromUpperRerange;
    }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosManager.sol#L44C4-L51C50

### Instance#2: `_rewardToken` ,`_flywheelRewards` ,`_owner` can be checked for address(0)

```solidity
File: /src/rewards/base/FlywheelCore.sol
45:     constructor(address _rewardToken, address _flywheelRewards, IFlywheelBooster     _flywheelBooster,   address _owner) {
46:        _initializeOwner(_owner);
47:        rewardToken = _rewardToken;
48:        flywheelRewards = _flywheelRewards;
49:       flywheelBooster = _flywheelBooster;
    }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L45C3-L50C6

### Instance#3: `_owner` can be checked for address(0)

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

29: constructor(address _owner) {
30:        _initializeOwner(_owner);
31:   }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L29C5-L31C6

### Instance#4: `_asset` can be checked for address(0)

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

22:  constructor(address _asset) {
23:  asset = _asset;
24: rewardsContract = msg.sender;
}

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L22C5-L25C6

Note:This zero address check in (constructors specially) can be implemented in many files all over the protocol

## [G-07] Write for loops in more gas efficient way

Loop can be written in more gas efficient way, by

1. taking length in stack variable,
2. using unchecked{++i} with pre increment
3. not re-initializing to 0 since it is default
4. Using <= is cheaper than <

_1 instance - 1 file:_

### Instance#1:

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

176:        for (uint256 i = 0; i < size; i++) {
177:            ERC20 gauge = ERC20(gauges[i]);
        ...

196:        }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L176C8-L179C76

Recommended Changes: Convert above for loop to this for making gas efficient saves more than 130 Gas per iteration

```diff
-      for (uint256 i = 0; i < size; i++) {
        ERC20 gauge = ERC20(gauges[i]);
        ...

        }

+  uint256 _size = size - 1;
+  for (uint256 i; i <= _size ; ) {
        ERC20 gauge = ERC20(gauges[i]);
              ...
+            unchecked {
+                ++i;
+            }
        }

```

## [G-08] Use unchecked{} whenever underflow/overflow not possible saves 130 gas each time

Because of if condition check before the operation(+/-), it can be decided that underflow/overflow not possible.

_6 instances - 1 file:_

### Instance#1-2 : `(amount0Desired - amount0)` and `(amount1Desired - amount1)` can't underflow due to line 166 and 171 if () condition so they can be unchecked .

```solidity
File: src/talos/base/TalosBaseStrategy.sol
166:   if (amount0 < amount0Desired) {
167:            uint256 refund0 = amount0Desired - amount0;
168:            address(_token0).safeTransfer(msg.sender, refund0);
        }

 171:       if (amount1 < amount1Desired) {
 172:           uint256 refund1 = amount1Desired - amount1;
 173:           address(_token1).safeTransfer(msg.sender, refund1);
        }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166C2-L174C10

Recommended Changes: Add unchecked block becuase underflow not possible here due to if conditions

```diff
if (amount0 < amount0Desired) {
- 167:            uint256 refund0 = amount0Desired - amount0;
+ 167:            uint256 refund0 = unchecked{amount0Desired - amount0};
168:            address(_token0).safeTransfer(msg.sender, refund0);
        }

 171:       if (amount1 < amount1Desired) {
- 172:           uint256 refund1 = amount1Desired - amount1;
+ 172:           uint256 refund1 = unchecked{amount1Desired - amount1};
 173:           address(_token1).safeTransfer(msg.sender, refund1);
        }
```

### Instance#3-4 : `(amount0Desired - amount0)` and `(amount1Desired - amount1)` can't underflow due to line 226 and 231 if () condition so they can be unchecked .

```solidity
File: src/talos/base/TalosBaseStrategy.sol

           // Refund in both assets.
226:     if (amount0 < amount0Desired) {
            //@audit gas unchecked
227:        uint256 refund0 = amount0Desired - amount0;
228:        address(_token0).safeTransfer(msg.sender, refund0);
      }

231:        if (amount1 < amount1Desired) {
               //@audit gas unchecked
232:           uint256 refund1 = amount1Desired - amount1;
233:           address(_token1).safeTransfer(msg.sender, refund1);
        }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L226C7-L234C10

Recommended Changes: Add unchecked{} block around `(amount0Desired - amount0)` and `(amount1Desired - amount1)` at line 227 and 232 like above Instance#1-2 recommendation

### Instance#5-6 : `_protocolFees0 - amount0` and `_protocolFees1 - amount1` can't underflow due to line 398 and 401 if () condition and these stack variables not yet change till line 411 so their subtraction can be unchecked.

```solidity
File: src/talos/base/TalosBaseStrategy.sol

398:      if (amount0 > _protocolFees0) {
399:            revert Token0AmountIsBiggerThanProtocolFees();
400:        }
401:        if (amount1 > _protocolFees1) {
402:            revert Token1AmountIsBiggerThanProtocolFees();
403:        }
        ERC20 _token0 = token0;
        ERC20 _token1 = token1;
        uint256 balance0 = _token0.balanceOf(address(this));
        uint256 balance1 = _token1.balanceOf(address(this));
        require(balance0 >= amount0 && balance1 >= amount1);
        if (amount0 > 0) _token0.transfer(msg.sender, amount0);
        if (amount1 > 0) _token1.transfer(msg.sender, amount1);

412:        protocolFees0 = _protocolFees0 - amount0;
413:        protocolFees1 = _protocolFees1 - amount1;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L398C7-L413C50

Recommended Changes: Add unchecked{} block around `_protocolFees0 - amount0` and `_protocolFees1 - amount1` at line 412 and 413

## [G-09] Use separate require's for different checks instead of merging multiple checks into one

It saves gas and more readable.

_1 instance - 1 file:_

### Instance#1: Use 2 require's instead of one for each condition check

```solidity
File: src/talos/base/TalosBaseStrategy.sol

408: require(balance0 >= amount0 && balance1 >= amount1);
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L408
Recommended Changes:

```diff
        uint256 balance0 = _token0.balanceOf(address(this));
        uint256 balance1 = _token1.balanceOf(address(this));
- 408:     require(balance0 >= amount0 && balance1 >= amount1);
+ 408:     require(balance0 >= amount0 );
+ 409:     require( balance1 >= amount1);
        if (amount0 > 0) _token0.transfer(msg.sender, amount0);
        if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

## [G-10] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read.

_2 instances - 1 file:_

### Instance#1 : Here struct `queuedRewards` can be of storage type to save gas, and it's data variables can be cached to stack rather than reading from memory will be much cheaper.

```solidity
File : /src/rewards/rewards/FlywheelGaugeRewards.sol

204:   QueuedRewards memory queuedRewards = gaugeQueuedRewards[ERC20(msg.sender)];

206:        uint32 cycle = gaugeCycle;
207:        bool incompleteCycle = queuedRewards.storedCycle > cycle;

        // no rewards
        //queuseRewards variables can be cached to stack from storage directly rather than re- reading from memory saves  MLOAD
 210:       if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {
            return 0;
        }

 214:       // if stored cycle != 0 it must be >= the last queued cycle
 215:      assert(queuedRewards.storedCycle >= cycle);

 217:       // always accrue prior rewards
 218:       accruedRewards = queuedRewards.priorCycleRewards;
 219:       uint112 cycleRewardsNext = queuedRewards.cycleRewards;

 221:       if (incompleteCycle) {
            // If current cycle queue incomplete, do nothing to current cycle rewards or accrued
 223:       } else {
            accruedRewards += cycleRewardsNext;
            cycleRewardsNext = 0;
        }

 228:       gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({
            priorCycleRewards: 0,
            cycleRewards: cycleRewardsNext,
 231:           storedCycle: queuedRewards.storedCycle
        });
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L204C6-L232C12

Recommended Changes : queueRewards Data variables can be cached to stack rather than reading from memory will be much cheaper. It will save writing to memory MSTORE opcode and will save MLOAD by caching into stack variables.

```diff
-       QueuedRewards memory queuedRewards = gaugeQueuedRewards[ERC20(msg.sender)];
+       QueuedRewards storage queuedRewards = gaugeQueuedRewards[ERC20(msg.sender)];

206:        uint32 cycle = gaugeCycle;
+           uint112  _cycleRewards= queuedRewards.cycleRewards;
+           uint112  _priorCycleRewards= queuedRewards.priorCycleRewards;
+           uint32  _storedCycle= queuedRewards.storedCycle;
- 207:        bool incompleteCycle = queuedRewards.storedCycle > cycle;
+            bool incompleteCycle = ._storedCycle > cycle;

        // no rewards
        //@audit queuedRewards variables can be cached to stack from storage directly rather than re- reading from memory saves  MLOAD
- 210:       if (queuedRewards.priorCycleRewards == 0 && (queuedRewards.cycleRewards == 0 || incompleteCycle)) {
+        if (_priorCycleRewards == 0 && (_cycleRewards == 0 || incompleteCycle)) {
            return 0;
        }

 214:       // if stored cycle != 0 it must be >= the last queued cycle
- 215:      assert(queuedRewards.storedCycle >= cycle);
+ 215:      assert(_storedCycle >= cycle);

 217:       // always accrue prior rewards
- 218:       accruedRewards = queuedRewards.priorCycleRewards;
- 219:       uint112 cycleRewardsNext = queuedRewards.cycleRewards;
+ 218:       accruedRewards =_priorCycleRewards;
+ 219:       uint112 cycleRewardsNext = _cycleRewards;

 221:       if (incompleteCycle) {
            // If current cycle queue incomplete, do nothing to current cycle rewards or accrued
 223:       } else {
            accruedRewards += cycleRewardsNext;
            cycleRewardsNext = 0;
        }

 228:       gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({
            priorCycleRewards: 0,
            cycleRewards: cycleRewardsNext,
- 231:           storedCycle: queuedRewards.storedCycle
+                storedCycle: _storedCycle
        });
```

### Instance#2 : Here struct `queuedRewards` can be of storage type to save gas, and it's data variables can be cached to stack rather than reading from memory will be much cheaper.

```solidity
File : /src/rewards/rewards/FlywheelGaugeRewards.sol

 179:    QueuedRewards memory queuedRewards = gaugeQueuedRewards[gauge];
            //@audit reading from stack is cheaper than reading from memory

            // Cycle queue already started
            require(queuedRewards.storedCycle < currentCycle);

            assert(
                queuedRewards.storedCycle == 0 ||
                    queuedRewards.storedCycle >= lastCycle
            );

            uint112 completedRewards = queuedRewards.storedCycle == lastCycle
                ? queuedRewards.cycleRewards
                : 0;
            uint256 nextRewards = gaugeToken.calculateGaugeAllocation(
                address(gauge),
                totalQueuedForCycle
            );
            require(nextRewards <= type(uint112).max); // safe cast

            gaugeQueuedRewards[gauge] = QueuedRewards({
                priorCycleRewards: queuedRewards.priorCycleRewards +
                    completedRewards,
                cycleRewards: uint112(nextRewards),
 192:               storedCycle: currentCycle
 193:           });
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L179C11-L193C16
Recommended Changes : This can also be updated like above instance#1 of G-10 to be gas efficient

## [G-11] State variables can be updated once in final rather than re-writing same variable more than once in one function. Saves unnecessary SSTORE.

Saves Gas for each SSTORE

_2 instances - 1 file:_

### Instance#1-2: Here `paginationOffset` and `nextCycleQueuedRewards` can be cached into stack variables , update into them multiple times(whenever necessary) and finally write those stack variables into these state variables. It can save Unnecessary SSTORE and SLOAD opcodes .

```solidity
File : src/rewards/rewards/FlywheelGaugeRewards.sol

118:  if (currentCycle <= lastCycle) revert CycleError();

120:        if (currentCycle > nextCycle) {
            nextCycle = currentCycle;
            paginationOffset = 0;
        }

        uint32 offset = paginationOffset;

        // important to only calculate the reward amount once to prevent each page from having a different reward amount
        if (offset == 0) {
            // queue the rewards stream and sanity check the tokens were received
            uint256 balanceBefore = rewardToken.balanceOf(address(this));
            uint256 newRewards = minter.getRewards();
            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);
            require(newRewards <= type(uint112).max); // safe cast
            nextCycleQueuedRewards += uint112(newRewards); // in case a previous incomplete cycle had rewards, add on
        }

        uint112 queued = nextCycleQueuedRewards;

        uint256 remaining = gaugeToken.numGauges() - offset;

        // Important to do non-strict inequality to include the case where the numRewards is just enough to complete the cycle
        if (remaining <= numRewards) {
            numRewards = remaining;
            gaugeCycle = currentCycle;
            nextCycleQueuedRewards = 0;
            paginationOffset = 0;
            emit CycleStart(currentCycle, queued);
149:        } else {
150:            paginationOffset = offset + numRewards.toUint32();
        }
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L179C11-L193C16

Recommended Changes: Cache `paginationOffset` and `nextCycleQueuedRewards` into stack variables and make a extra copy of one of those 1 to having initial value whenever read needed before updation and finally write them into state variables at last. It can save extra SSTORE and SLOAD opcodes.

```diff
118:  if (currentCycle <= lastCycle) revert CycleError();
+       uint32 offsetState= paginationOffset;
+       uint112 nextCycleQueuedRewardsState= nextCycleQueuedRewards;
120:        if (currentCycle > nextCycle) {
            nextCycle = currentCycle;
-           paginationOffset = 0;
+            offsetState=0;
        }
-        uint32 offset = paginationOffset;
+        uint32 offset = offsetState;

        // important to only calculate the reward amount once to prevent each page from having a different reward amount
        if (offset == 0) {
            // queue the rewards stream and sanity check the tokens were received
            uint256 balanceBefore = rewardToken.balanceOf(address(this));
            uint256 newRewards = minter.getRewards();
            require(rewardToken.balanceOf(address(this)) - balanceBefore >= newRewards);
            require(newRewards <= type(uint112).max); // safe cast
-            nextCycleQueuedRewards += uint112(newRewards); // in case a previous incomplete cycle had rewards, add on
+            nextCycleQueuedRewardsState += uint112(newRewards);
        }
-        uint112 queued = nextCycleQueuedRewards;
+        uint112 queued = nextCycleQueuedRewardsState;

        uint256 remaining = gaugeToken.numGauges() - offset;

        // Important to do non-strict inequality to include the case where the numRewards is just enough to complete the cycle
        if (remaining <= numRewards) {
            numRewards = remaining;
            gaugeCycle = currentCycle;
-           nextCycleQueuedRewards = 0;
-           paginationOffset = 0;
+           nextCycleQueuedRewardsState = 0;
+           offsetState = 0;
            emit CycleStart(currentCycle, queued);
  149:        } else {
- 150:            paginationOffset = offset + numRewards.toUint32();
+                 offsetState = offset + numRewards.toUint32();
        }
+           nextCycleQueuedRewards =  nextCycleQueuedRewardsState;
+            paginationOffset = offsetState;
    ...

```
