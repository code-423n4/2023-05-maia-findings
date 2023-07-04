# Low 001

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



## Low 002

## Title

If a user enters non-empty params value, execution will revert on Root Chain and deposits won't be processed.

## Link

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L192
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L135
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L239
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L379

## Impact

including a `params` field when bridging will only cause a revert.

## Proof of Concept

When making deposits on BranchChain, a `params` field allows user to input a bytes value, which would be encoded in the data that would be sent to root chain.
On Root chain, RootBridgeAgent#executeWithDeposit checks for length of data, and if more than 112(length of encoded data-`params`length), it calls `Router.anyExecuteDepositSingle`. The problem is that `anyExecuteDepositSingle` in both MulticallRootRouter and CoreRootRouter reverts. This means that by specifying a `params` value on branch chain, call would revert on root chain.

## Tools Used

Manual Review

## Recommendation

Remove the `params` parameter from each of the functions in BranchBridgeAgent since it would always only cause a revert.

