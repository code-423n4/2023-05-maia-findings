## [Q-1] Protocol fees do not match documentation
According to the [documentation](https://v2-docs.maiadao.io/protocols/Ulysses/overview/unified-liquidity/fees#protocol-fees) , the protocol fee "will be set at 3 bps at launch, has a minimum value of 1 bp and maximum value of 10 bps." However this is not the case for all 3 scenarios. 

According to the code, the discrepancies are:
- The [default](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L67) fee is set to 1bps, instead of 3bps
- The [max fee](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L51-L52) is 100 bps, instead of 10bps
- There is [no minimum check](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L323-L330) to the fee, instead of 1bps

Recommendation
- Consider updating the code to match the documentation, or vice-versa.

## [Q-2] Rebalancing fee does not match documentation
According to the [documentation](https://v2-docs.maiadao.io/protocols/Ulysses/overview/unified-liquidity/fees#protocol-fees), the condition for the first rebalancing fee (0), is `b - t <= sigma_1B`. However, this does not match what is in the [code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L733). 

After discussed with the Sponsor, the documentation is incorrect.

Recommendation
- Update the documentation to prevent confusion for auditors and future implementors.

## [Q-3] length spelled incorrectly
In few parts of the [code](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootPort.sol#L67), length is spelled as "lenght". Consider fixing these smelling mistakes.
 
## [Q-4] Unreachable code
In line [375](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/CoreRootRouter.sol#L375) of CoreRootRouter.sol, the last return statement never gets called. This is because of the `else` statement. Consider removing this or refactoring the `else` statement.
