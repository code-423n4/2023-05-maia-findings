## Summary

### Low Risk Issues
|Number|Issue|Instances| |
|-|:-|:-:|:-:|
| [L&#x2011;01] | Avoid self transfer of tokens | 2 |
| [L&#x2011;02] | Array operation may be hampered in ERC20Gauges.sol | 2 |
| [L&#x2011;03] | Delegatee can be set to null address in delegateBySig()  | 1 |
| [L&#x2011;04] | Use Openzeppelin ECDSA library instead of ecrecover prevent signature attacks  | 1 |
| [L&#x2011;05] | Use Openzeppelin initializer modifier to prevent re-intialization  | 1 |

### [L&#x2011;01]  Avoid self transfer of tokens
The tokens should not be transferred to self which may lead to accounting erros. Ensure recipient is not the sender.

There is 2 instance of this issue:
In ERC20Boost.sol at L-315 and L-323.
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L312-L330)

### Recommended Mitigation steps

```Solidity
File: src/erc-20/ERC20Boost.sol

   function transfer(address to, uint256 amount) public override notAttached(msg.sender, amount) returns (bool) {
+       require(msg.sender != to, "self transfer not allowed");
        return super.transfer(to, amount);
    }


    function transferFrom(address from, address to, uint256 amount)
        public
        override
        notAttached(from, amount)
        returns (bool)
    {
+       require(from != to, "self transfer not allowed");
        return super.transferFrom(from, to, amount);
    }
```

### [L&#x2011;02]  Array lengths not checked in ERC20Gauges.sol
If the length of the arrays are not required to be of the same length, user operations may not be fully executed.

There are 000 instances of this issue:
In ERC20Gauges.sol at L-245
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L245)

In ERC20Gauges.sol at L-322
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L322)

### Recommended Mitigation steps
For example:

```Solidity
File: src/erc-20/ERC20Gauges.sol

    function incrementGauges(address[] calldata gaugeList, uint112[] calldata weights)
        external
        nonReentrant
        returns (uint256 newUserWeight)
    {
+    require(gaugeList != 0 "empty guage list");
+    require(weights != 0 "empty guage list");
+    require(gaugeList == weights "empty guage list");

     // some code
```

### [L&#x2011;03]  Delegatee can be set to null address in delegateBySig()
Prevent the delegatee from setting address(0).
There is 1 instance of this issue:
In ERC20MultiVotes.sol at 363
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L363)

### Recommended Mitigation steps

```Solidity
File: src/erc-20/ERC20MultiVotes.sol

    function delegateBySig(address delegatee, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s) public {
+       require(delegatee != address(0), "invalid address");
        require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");

//   Some code
    }
```

### [L&#x2011;04]  Use Openzeppelin ECDSA library instead of ecrecover prevent signature attacks
ecrecover should not be used and is succeptible to signature attacks. It is recommended to use openzeppelin ECDSA library which is the best solution to prevent signature attacks.

There is 1 instance of this issue:
In ERC20MultiVotes.sol at L-365.
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L365)

[Openzeppelin ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol)

### [L&#x2011;05]  Use Openzeppelin initializer modifier to prevent re-intialization
In BaseV2Minter.sol, initialize() can be re-initialized multiple times due to missing initializer modifier. It is recommended to add openzeppelin initializer modifier on intialize() function.

There is 1 instance of this issue:
In BaseV2Minter.sol at L-78.
[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L78)
