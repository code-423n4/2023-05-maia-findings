# QA Report

## [L-01] The contract is doing no sense operation in ERC20gauges.sol

The contract is multiplying and dividing by the same number which is equal to multiply by 1  

```
function _getGaugeCycleEnd() internal view returns (uint32) {
        uint32 nowPlusOneCycle = block.timestamp.toUint32() + gaugeCycleLength;
        unchecked {
            return (nowPlusOneCycle / gaugeCycleLength) * gaugeCycleLength; // @audit the contract is multiplying and dividing by the same number
        }
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L77

## [L-02] The contract ERC20Multivotes.sol is no following correctly the EIP-712

The contract is not computing the chain id in the abi.encode in the delegateBySig function.

```
function delegateBySig(address delegatee, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s) public {
        ...
         keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry)) // the sign doestn have chain id 
          ...
        _delegate(signer, delegatee);
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L368

## [L-03] The contract talosBaseStrategy.sol is not managing the protocol fee

The contract talosBaseStrategy.sol is using a protocol fee in which there is no function to set the protocolfee, the user should be aware of set the fee in the constructor is not the fee is gonna be 0 always.

```
function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {
        uint256 _protocolFees0 = protocolFees0; 
        uint256 _protocolFees1 = protocolFees1;
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L394-L396

## The id of the first pool is gonna be number 2 in ulyseesFactory.sol contract

see the implementation of the createPool function:
```
function _createPool(ERC20 asset, address owner) private returns (uint256 _poolId) {
        if (address(asset) == address(0)) revert InvalidAsset();
        _poolId = ++poolId; // the first call its gonna be 2
        pools[_poolId] =
            UlyssesPoolDeployer.deployPool(_poolId, address(asset), "Ulysses Pool", "ULP", owner, address(this));
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/factories/UlyssesFactory.sol#L83-L88

You can solutionate this doing the sume in the final:
```
function _createPool(ERC20 asset, address owner) private returns (uint256 _poolId) {
        if (address(asset) == address(0)) revert InvalidAsset();
        _poolId = poolId;
        pools[_poolId] =
            UlyssesPoolDeployer.deployPool(_poolId, address(asset), "Ulysses Pool", "ULP", owner, address(this));
        ++poolId;
    }
```





