### Gas Optimizations
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [G&#x2011;01] | Catch num parameters as local variables in ERC20Boost.gauges() and ERC20Boost.userGauges() to save gas | 2 |
| [G&#x2011;02] | It costs more gas to initialize variables with their default value than letting the default value be applied | 10 |
| [G&#x2011;03] | Use modifier instead of repeating code for access validation in GovernorBravoDelegateMaia.sol | 7 |
| [G&#x2011;04] | Catch function paramters as local variables to save gas | 2 |

### [G&#x2011;01]  Catch num parameters as local variables in ERC20Boost.gauges() to save gas
Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read. It will save for each iteration.
There are 2 instances of this issue:

In ERC20Boost.sol. at L-50.
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L50-L58)

In ERC20Boost.sol at L-96.
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L96)

### Recommended Mitigation steps
```Solidity
File: src/erc-20/ERC20Boost.sol

    function gauges(uint256 offset, uint256 num) external view returns (address[] memory values) {
+       uint256 _num = num;
-        values = new address[](num);
+        values = new address[](_num);
-        for (uint256 i = 0; i < num;) {
+        for (uint256 i = 0; i < _num;) {
            unchecked {
                values[i] = _gauges.at(offset + i); // will revert if out of bounds
                i++;
            }
        }
    }
```

```Solidity
File: src/erc-20/ERC20Boost.sol

    function userGauges(address user, uint256 offset, uint256 num) external view returns (address[] memory values) {
+       uint256 _num = num;
-        values = new address[](num);
+        values = new address[](_num);
-        for (uint256 i = 0; i < num;) {
+        for (uint256 i = 0; i < _num;) {
            unchecked {
                values[i] = _userGauges[user].at(offset + i); // will revert if out of bounds
                i++;
            }
        }
    }
```

### [G&#x2011;02]  It costs more gas to initialize variables with their default value than letting the default value be applied
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

There are 10 instances of this issue:
In ERC20Boost.sol at L-50,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L52)

In ERC20Boost.sol at L-98,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L98)

In ERC20Boost.sol at L-156,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L156)

In ERC20Boost.sol at L-207,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L207)

In ERC20Boost.sol at L-236,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L236)

In ERC20Gauges.sol at L-114,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L114)

In ERC20Gauges.sol at L-259,
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L259)

In ERC20Gauges.sol at L-341
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L338)

In ERC20Gauges.sol at L-536
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L536)

In ERC20MultiVotes.sol at L-328
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L328)

### Recommended Mitigation steps

For example: 

```Solidity

-      for (uint256 i = 0; i < num;) {
+      for (uint256 i; i < num;) {
```

### [G&#x2011;03]  Use modifier instead of repeating code for access validation in GovernorBravoDelegateMaia.sol
In GovernorBravoDelegateMaia.sol. There is repeated code for access validation in functions like initialize(), _setVotingDelay(), _setVotingPeriod(), _setProposalThreshold(), _setWhitelistGuardian(), _initiate(), _setPendingAdmin(). However a modifier can be used which will save lots of bytes thereby saving good amount of gas.
There are 7 instance of this issue:

### Recommended Mitigation steps
For example,

```Solidity

+   modifier onlyAdmin(){
+   require(msg.sender == admin, "GovernorBravo::access to admin only");
+   _; 
+    }

-    function _initiate(address governorAlpha) external {
+    function _initiate(address governorAlpha) external onlyAdmin{
-       require(msg.sender == admin, "GovernorBravo::_initiate: admin only");
        require(initialProposalId == 0, "GovernorBravo::_initiate: can only initiate once");
        proposalCount = GovernorAlpha(governorAlpha).proposalCount();
        initialProposalId = proposalCount;
        timelock.acceptAdmin();
    }
```

### [G&#x2011;04]  Catch function paramters as local variables to save gas
Catching function parameters as local variables. Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read. It will save for each iteration.
There are 2 instances of this issue:
In ERC20Gauges.sol at L-112
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L112)

In ERC20Gauges.sol at L-153
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L153)

Recommended Mitigation steps

For example as taken from G-02:

```Solidity

    function gauges(uint256 offset, uint256 num) external view returns (address[] memory values) {
+       uint256 _num = num;
-        values = new address[](num);
+        values = new address[](_num);
-        for (uint256 i = 0; i < num;) {
+        for (uint256 i = 0; i < _num;) {
            unchecked {
                values[i] = _gauges.at(offset + i); // will revert if out of bounds
                i++;
            }
        }
    }
```