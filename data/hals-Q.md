# Findings Summary

| ID     | Title                                                                                         | Severity |
| ------ | --------------------------------------------------------------------------------------------- | -------- |
| [L-01] | Returned value of `_mint` in ERC4626 is not checked                                           | Low      |
| [L-02] | Miscalculated `startTime` in `createIncentiveFromGauge` function                              | Low      |
| [L-03] | Users will not get rewards on staked tokens if `protocolFee==DIVISIONER` in `BoostAggregator` | Low      |

# Low

## [L-01] Returned value of `_mint` in ERC4626 is not checked

## Details

Returned value of `_mint` in ERC4626 is not checked if it's > 0 or not,as there might be a case due to inflation attack where the minted share for the user would be equal to zero.

## Impact

User loses his asset without getting any shares.

## Proof of Concept

Instances: 2

[Line 39](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626.sol#L39)
[Line 53](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626.sol#L53)

```solidity
File:2023-05-maia/src/erc-4626/ERC4626.sol
Line 39:_mint(receiver, shares);
Line 53:_mint(receiver, shares);
```

## Tools Used

Manual Testing.

## Recommended Mitigation Steps

Check if minted shares > 0.

#

## [L-02] Miscalculated `startTime` in `createIncentiveFromGauge` function

## Details

In `UniswapV3Staker` contract: the value of `startTime` calculated in `createIncentiveFromGauge` function is calculated as an endTime:  
`IncentiveTime.computeEnd(block.timestamp)`.

## Impact

This will result in rewards being assigned to a wrong calculated (non existent) incentiveId,and the user will lose his asset that's being transferred as rewards.

## Proof of Concept

[Line 39](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L140)

```solidity
File:2023-05-maia/src/uni-v3-staker/UniswapV3Staker.sol
Line 140:int96 startTime = IncentiveTime.computeEnd(block.timestamp);
```

## Tools Used

Manual Testing.

## Recommended Mitigation Steps

Update `startTime` as : `startTime=IncentiveTime.computeStart(block.timestamp)`

#

## [L-03] Users will not get rewards on staked tokens if `protocolFee==DIVISIONER` in `BoostAggregator`

## Details

In `BoostAggregator` contract: since **there's no check** on `protocolFee` if it equals to the `DIVISIONER` when updating it by the `setProtocolFee` function; this will result in users getting no rewards on their staked tokens if the `protocolFee` is set equal to `DIVISIONER` (which is setting a 100% protocolFee).

## Impact

Users will not get rewards on their staked tokens.

## Proof of Concept

[Line 154](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/boost-aggregator/BoostAggregator.sol#L154)
[Line 119](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/boost-aggregator/BoostAggregator.sol#L119)

```solidity
File:2023-05-maia/src/talos/boost-aggregator/BoostAggregator.sol
Line 119: uint256 newProtocolRewards = (pendingRewards * protocolFee) / DIVISIONER;
Line 154:if (_protocolFee > DIVISIONER) revert FeeTooHigh();
```

## Tools Used

Manual Testing.

## Recommended Mitigation Steps

Update `setProtocolFee` to check for: ` if (_protocolFee >= DIVISIONER) revert FeeTooHigh();`.
