## Title
Migrate from NonFungiblePositionManager to UniV3Pool directly

## Details and Impact
You can directly transact with the UNI V3 pool contract , with this , the additional gas costs associated with the NonfungiblePositionManager contract and mint ERC721 are also removed. TalosStrategy can get any details related to the Pool by using PoolActions library. 

For ref :
https://github.com/code-423n4/2021-12-mellow-findings/issues/133

Add bool in TalosBaseStrategy.sol, for example isVanilla, if yes, so directly work with Pool contract, if not, work with _nonfungiblePositionManager.

## Links
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L16