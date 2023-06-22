QA1. UlyssesPool.maxRedeem() needs to consider the protocol fees. 

Impact: without considering the protocol fees, a user might redeem too much funds and then there is no sufficient left amount of funds for the protocol fees.  

The correction is the following:

```diff
 function maxRedeem(address owner) public view override returns (uint256) {
-        return balanceOf[owner].min(asset.balanceOf(address(this)));
+        return balanceOf[owner].min(asset.balanceOf(address(this))- getProtocolFees());

    }
```