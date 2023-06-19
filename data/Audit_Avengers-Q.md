# Issues
- _bHermes address provided to the constructor is missing a zero address check. This could result in unexpected behavior when attempting to use the contract.
- _owner address provided to the constructor is missing a zero address check. This could result in the contract being unusable due to the owner being a zero address.

[Link to code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/factories/PartnerManagerFactory.sol#LL37C4-L41C4)

File:   src/maia/factories/PartnerManagerFactory.sol
```
constructor(ERC20 _bHermes, address _owner) {
        _initializeOwner(_owner); // no zero address check for owner, zac implemented in other contracts e.g. UlyssesFactory.sol
        bHermes = _bHermes; // no zero address check
        partners.push(PartnerManager(address(0)));
    }
```
## Recommendation
Implement a zero address check using the require function and the != (inequality) operator with address(0).

### Example:
```
// zero address check nHermes token contract.
require(_owner != address(0), "Owner cannot be 0");
require(bHermes != address(0), "Invalid partner bHermes token address");

// assign after successful zero address checks.
_initializeOwner(_owner);
bHermes = _bHermes;
partners.push(PartnerManager(address(0)));
```