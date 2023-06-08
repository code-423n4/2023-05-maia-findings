## [Q-1] Protocol fees do not match documentation
According to the [documentation](https://v2-docs.maiadao.io/protocols/Ulysses/overview/unified-liquidity/fees#protocol-fees), the protocol fee "will be set at 3 bps at launch, has a minimum value of 1 bp and maximum value of 10 bps." However this is not the case for all 3 scenarios. 

According to the code, the discrepancies are:
- The [default](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L67) fee is set to 1bps, instead of 3bps
- The [max fee](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L51-L52) is 100 bps, instead of 10bps
- There is [no minimum check](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L323-L330) to the fee, instead of 1bps

Recommendation
- Consider updating the code to match the documentation, or vice-versa.
