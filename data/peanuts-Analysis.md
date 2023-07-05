## Table of Contents

1. Context and Summary
2. Approach taken in evaluating the codebase
3. Codebase Quality Analysis
4. Centralization Risks
5. Mechanism Review
6. Architecture Recommendations
7. Reflection and Learning
8. Hours Spent

### 1. Context and Summary

My findings are based on TALOS contracts, and all the interactions with UniswapV3's NonFungiblePositionManager and UniswapV3's Pool. 

The Transparent Automated Liquidity Omnichain Strategies (TALOS) contracts is basically a pooled together version of concentrated liquidity LPs. In other words, instead of every person having their own liquidity position and NFT in a pool, users can pool together an increase liquidity inside one liquidity position. Anyone can create new and/or use existing LPs, and each LP can choose to have multiple strategies associated with it on launch.

The liquidity position is accounted through the use of shares. Whenever users deposit liquidity inside the position, shares are distributed back to them. These shares can then be redeemed for tokens. 

The concept of concentrated liquidity positions is great for things like gauge. For example, Staked positions allow users to stake their UNIV3 NFTs in the Hermes Uniswap V3 gauge, earning HERMES rewards instead of fees for providing liquidity to the gauge. The protocol is also flexible in that if users do not want to stake their UNIV3 NFT, then they can just earn fees through the Vanilla strategy.

### 2. Approach taken in evaluating the codebase

I focused heavily on any contracts that rely on UniswapV3 Positions, which is mainly the TALOS contracts. 

- Firstly, I made sure that all function calls from TALOS to Uniswap's Non-fungible Position Manager, such as `decreaseLiquidity`, `increaseLiquidity` and `collect` is correctly integrated. 
- Afterwards, I dived into the nested function calls and see how `rerange` and `rebalance` worked.
- I checked the implementation of different modifiers such as `checkDeviation` and unique functions like `getTwap` and made sure that every function was written properly.
- Lastly, I checked `getSwapToEqualAmountsParams`, `uniswapV3SwapCallback` to make sure that the correct variables are called.

- Other than TALOS, I searched the protocol for unique implementations such as ERC4626, FlyWheel, ERC20Gauge, and checked the protocol's code against the common mistakes made when rewriting implementations.   
- I briefly looked at HERMES to understand how TalosStrategyStaked worked together with Hermes Uniswap V3 gauge. 

### 3. Codebase Quality Analysis

The codebase is written extremely well for such a complex protocol. In ERC20Gauges.sol, functions such as `_incrementGaugeWeight` is well checked to make sure that the gauge used is not deprecated, which is a common issue when protocol uses gauge-like contracts.

```
    function _incrementGaugeWeight(address user, address gauge, uint112 weight, uint32 cycle) internal {
        if (!_gauges.contains(gauge) || _deprecatedGauges.contains(gauge)) revert InvalidGaugeError();
```

The ERC4626 Vaults implements all the different functions correctly which is very impressive. Functions that are supposed to round down are rounded down, and functions that are supposed to round up are rounded up. 

```
    function previewMint(uint256 shares) public view virtual returns (uint256) {
        uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.
        return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);
    }
```

The TALOS contracts integrates Uniswap well, like handling uniswapV3SwapCallback, increasing and decreasing liquidity properly. I don't really see a burn position NFT function but that's probably a non issue and it'll be pretty hard to call burn anyways considering that the liquidity has to be zero. 

```
    /// @inheritdoc INonfungiblePositionManager
    function burn(uint256 tokenId) external payable override isAuthorizedForToken(tokenId) {
        Position storage position = _positions[tokenId];
        require(position.liquidity == 0 && position.tokensOwed0 == 0 && position.tokensOwed1 == 0, 'Not cleared');
        delete _positions[tokenId];
        _burn(tokenId);
    }
```

###  4. Centralization risks

I'd like to highlight one particular centralization issue regarding the flywheel contracts. Initially I wanted to put the issue as medium risk but I think it'll be better if I put it here instead since it's heavily reliant on a privileged actor. The issue is that FlywheelCore.setBooster() can be used to steal unclaimed rewards. 

A malicious authorized user can steal all unclaimed rewards and break the reward accounting. Even if the authorized user is benevolent the fact that there is a [rug vector](https://twitter.com/RugDocIO/status/1411732108029181960) available may negatively impact the protocol’s reputation. Furthermore since this contract is meant to be used by other projects, the trustworthiness of every project cannot be vouched for. The whole issue can be found here:

Issue: https://github.com/code-423n4/2022-04-xtribe-findings/issues/23

The gist of the issue is that `flywheelBooster` can be set to a zero value which makes it such that an attacker can choose specific amounts of rewardToken to assign to himself/herself.

Other than that particular centralization risk, since I've largely only combed through TALOS contracts, the contracts are created in a rather permissionless way which is good for decentralization.

### 5. Mechanism Review

The 2 different Vaults - Vanilla and Staked - which inherits the base strategy, is easy to follow and well written. Anyone can choose to deploy any one of the position.

I particularly liked the `checkDeviation` mechanism which checks the deviation of ticks. 

```
    function checkDeviation(IUniswapV3Pool pool, int24 maxTwapDeviation, uint32 twapDuration) public view {
        (, int24 currentTick,,,,,) = pool.slot0();
        int24 twap = getTwap(pool, twapDuration);
        int24 deviation = currentTick > twap ? currentTick - twap : twap - currentTick;
        if (deviation > maxTwapDeviation) revert DeviationTooHigh();
    }
```

### 6. Architecture Recommendations

I think it would be great if there was a video walkthrough of how everything fits together (something like a summary video), like how each 'smaller protocols' interacts with one another. For the longest time, I thought that it was four different projects but after some digging through I realize they were all connected.

More NATSPEC can be included in functions like `rerange` and `rebalance`, even an end-to-end scenario, like what happens first, what happens next, what happens in the end. For example, in rebalance, comments could be like `collect fees` -> `withdraw all liquidity` -> `collect all tokens` -> `check appropriate tick ranges` -> `check whether token0 is swapped or token1 is swapped` ..... `NFT is minted once again with 50-50 ratio`. 

### 7. Reflection and Learning

It was an extremely hard codebase to tackle, probably the hardest I've done so far just because of the sheer size. I think I spent too much time trying to comprehend how everything worked together. In retrospect, I should have just picked something and start and use that start as a benchmark. Also, the current back and forth of protocol and uniswap actually made me mint some small LP positions in uniswap of my own just to make sure I know what's going on, like when are fees collected, how does increaseLiquidity actually work, how does collect work etc.

I also started working on the codebase pretty late, which didn't give me enough time to walk through the codebase as one whole entity. Before this, I was trying to prep for the codebase by reading up on Uniswap V3's whitepaper and understanding the math, but on hindsight I should have done both learning and auditing in tandem.   

I've learned quite a bit on interacting with Uniswap, wouldn't say that I am extremely proficient but I now know how protocols can interact with UniswapV3 a little better. Although this contest is probably out of my comfort zone, I'm glad I attempted this audit.  

### 8. Hours Spent

24 Hours.

### Time spent:
24 hours