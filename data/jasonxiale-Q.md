# Using ERC721.transferFrom() instead of safeTransferFrom() may cause the user's NFT to be frozen in a contract that does not support ERC721
There are certain smart contracts that do not support ERC721, using transferFrom() may result in the NFT being sent to such contracts.
The NFT may get stuck in the contract that does support ERC721.
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/VirtualAccount.sol#L37
```solidity
        ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);
```

# using toUint128 to cast down msg.value instead of uint128(msg.value)
To prevent msg.value from overflow, `msg.value.toUint128` should be used instead of `uint128(msg.value)`
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L247
```solidity
            userFeeInfo.depositedGas = uint128(msg.value);
```