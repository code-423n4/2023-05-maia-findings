## 1. Comments don't match code (or code doesn't match comments):

### 1.1 src/ulysses-amm/UlyssesPool.sol - function setFees()
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L309

```solidity
// Lower fee must be lower than 1%
if (_fees.lambda1 > MAX_LAMBDA1) revert InvalidFee();
```
**The code actually matches the following condition:**
Lower fee must be lower than or equal 1%

### 1.2 src/ulysses-amm/UlyssesPool.sol - function setFees()
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L314 

```solidity
// Upper bound must be lower than 100%
if (_fees.sigma1 > DIVISIONER) revert InvalidFee();
```
**The code actually matches the following condition:**
Upper bound must be lower than or equal 100%

### 1.3 src/ulysses-amm/UlyssesPool.sol - function setFees()
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L316

```solidity
// Lower bound must be lower than Upper bound and higher than 1%
if (_fees.sigma1 <= _fees.sigma2 || _fees.sigma2 < MIN_SIGMA2) revert InvalidFee();
```
**The code actually matches the following condition:**
Lower bound must be lower than Upper bound and higher than or equal 1%


### 1.4 src/ulysses-amm/UlyssesPool.sol - function _calculateRebalancingFee(
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L742

```solidity
// If the bandwidth is smaller than upperBound2
if (bandwidth >= upperBound2) {
```
**The code actually matches the following condition:** 
If the bandwidth is greater than upperBound2 or equal


## Recommendation: 
Either fix the comments or fix the code.
