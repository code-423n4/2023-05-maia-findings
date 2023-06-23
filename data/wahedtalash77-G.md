## [G-01]  Avoiding Initialization of Loop Index If It Is 0 and Avoid unnecessary read of array length in for loops can save gas.
48: ` for (uint256 i = 0; i < calls.length; i++) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L48

99: `for (uint256 i = 0; i < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L99

107: ` for (uint256 i = 0; i < length;) {`
110: `     for (uint256 j = 0; j < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L107-L110

123: ` for (uint256 i = 0; i < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L123

145: `  for (uint256 i = 0; i < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L145

101: ` for (uint256 i = 0; i < _localAddresses.length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L101

176: ` for (uint256 i = 0; i < size; i++) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L176

311: `  for (uint256 i = 0; i < outputParams.outputTokens.length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L311



## [G-2]  we shold choose less range instead of `uint256` like `uint8` so the best way is to put them in struct. It should be noted that in a struct, uint8 DOES cost less than a traditional uint, because of the tight packing feature. Also be sure that your uints are next to your other uints.
24: `uint256 internal constant week = 86400 * 7;`
26: ` uint256 internal constant base = 1000;`
28:  `uint256 internal constant max_tail_emission = 100;`
29: `uint256 internal constant max_dao_share = 300;`
42: `uint256 public override daoShare = 100;`
43: `uint256 public override tailEmission = 20;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

143:  `uint256 internal _unlocked = 1;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L143

49: `uint256 public poolId = 1;`
52: `uint256 public tokenId = 1;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L49-L52

## [G-03] For uint use != 0 instead of > 0 .
139: `if (_liquidity > 0) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139

118: `     if (_deposit > 0) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118

160: `if (gasRemaining > 0) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160

234: `if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);`
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234







## [G-04]  Avoiding Initialization of the variables to it's default value.

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol



## [G-5] Use solidity version 0.8.19 to gain some gas boost
>Upgrade to the latest solidity version 0.8.19 to get additional gas savings.
> 
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L2
>
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L2
> 
>...
>See latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/
