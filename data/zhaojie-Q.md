   
Array length is not checked

src -> erc4626 -> ERC4626MultiToken.sol

```
 function sendAssets(uint256[] memory assetsAmounts, address receiver) private {
        uint256 length = assetsAmounts.length;
        //@audit Length check is not performed assetsAmounts
        for (uint256 i = 0; i < length;) {
            assets[i].safeTransfer(receiver, assetsAmounts[i]);
            unchecked {
                i++;
            }
        }
    }
```

The assets.length may not Equal to assetsAmounts.length, it is possible to cause unknown exceptions, need to check the array length.
