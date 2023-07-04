## Approach taken in evaluating the codebase

Due to the size of the codebase, my approach was to tackle each subprotocol separately, and then consider their interactions once I was familiar with each of them individually. Since I was unable to spend the entire contest time period auditing, I did not manage to thoroughly understand each subprotocol, however I do feel that I had a strong grasp on certain sections by the end of the audit, notably the contracts in the uni-v3-staker and talos folders.

## Architecture recommendations

Overall the codebase is nicely written and well thought out. I do however have some feedback considering the `UniswapV3Staker` contract: rewards are calculated using epochs, and whenever a `NonFungiblePositionManager` NFT is staked, it is staked for the current epoch. This means that each time a new epoch begins, the user must `restake()` their NFT in order to continue earning rewards. While the devs have tried to make this simple by omitting access control from the `restake()` function entirely, it still provides a poor user experience and may lead to users unknowingly sacrificing rewards.

## Codebase quality analysis

For the most part the code is well written and follows best practices in terms of style. Contracts are organized in an appropriate and understandable manner, with `interface` and `library` contracts correctly having their own folders.

When it comes to interacting with external contracts, in some areas it was clear that the devs did not fully understand the code they were interacting with:
- use of `transfer` instead of `safeTransfer` for ERC20 tokens [1](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409) [2](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410)
- failure to utilize Uniswap slippage control [1](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L201-L210) [2](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L263-L271) [3](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L73-L87) [4](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L135-L149) [5](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L353-L361)
- failure to utilize Uniswap's deadline parameter [1](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L201-L210) [2](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L263-L271) [3](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L73-L87) [4](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L135-L149) [5](https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L353-L361)

Some other best practices that were not followed include:
- use of floating pragmas for non-abstract non-interface contracts
- `require`/`revert` statements lacking error message
- assembly blocks lacking descriptive comments
- naming convention for `constant` variables (they should be named using UPPERCASE_WITH_UNDERSCORES)

## Centralization risks

The Maia ecosystem uses trusted admins throughout the protocol. Centralization risks are outlined below:
- admin may mint arbitrary amount of `HERMES`, `Maia` ERC20 tokens, diluting the supply and stealing value from holders
- admin may remove assets from `MultiRewardsDepot`, `UlyssesToken` at will, potentially catching users off guard
- admin may set an arbitrary dao share in `BaseV2Minter` through frontrunning to mislead users and steal funds
- admin controls weight and fees of `UlyssesPool`, which can be changed at any point to the detriment of users

## Systemic risks

- Epochs in gauge contracts inheriting from `BaseV2Gauge` must each be started manually by calling `newEpoch()`. If keepers are intended to be used for this, then the protocol is dependent on those keepers remaining functional, as if they don't user rewards could be lost. This risk is mitigated somewhat by allowing anyone to call the `newEpoch()` function, however relying on an EOA for this purpose is not reliable.
- Contract `UniswapV3Staker` allows users to deposit Uniswap V3 pool positions in order to earn incentivized rewards. Should a pool become very illiquid/inactive, adverse consequences could occur.
- The omnichain functionality of the protocol inherently relies on a messenger to relay messages from one blockchain to another, which must occur off chain. When using this functionality, users must be aware of the fact that they are trusting this entity to be operating correctly. Failure here likely results in lost funds.

### Time spent:
25 hours