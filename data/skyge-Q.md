https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L58
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L67

Should check input value, add a require statement to check that `newPartnerManager` and `newVault` are not equal to `address(0)`.

```
if(address(newPartnerManager) == address(0)) revert InvalidPartnerManager();
if(address(newVault) == address(0)) revert InvalidVault();
```