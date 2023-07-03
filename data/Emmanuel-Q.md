## Title

chainIds greater than 16777216 will not be added

## Link

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L424

## Impact

When new blockchains are created with chainId greater than 16777216, Ulysses won't be able to add them to RootPort.

## Proof of Concept

[`RootPort#addNewChain`](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L424) accepts as parameter a `_chainId` whose type is uint24 and max value of uint24 is 16777216

There is no limit to the value of `_chainId` an EVM blockchain can have and when a new chain is created with chainId greater than max(uint24), it will be impossible to add them to Ulysses so that tokens from that chain can also be bridged.
Since the contracts are not upgradeable, this cannot be modified when the need arises.

## Tools Used

Manual Review

## Recommended Mitigation Steps

change the type of `_chainId` from uint24 to uint256.

