# [L-01] maxMint, maxDeposit are not compliant with EIP-4626

## Links

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L98-L105
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L269-L276
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L148-L155
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L112-L118




## Impact

Other protocols that integrate with contract may incorrecly assume that the functions are EIP-4626 compliant. This may cause integration issues in the future, thus lead to faulty integrations with 3rd-party systems.

According to EIP-4626 standard, `maxMint` and `maxDeposit`:

```
https://eips.ethereum.org/EIPS/eip-4626

maxDeposit:
MUST return the maximum amount of assets deposit would allow to be deposited for receiver and not cause a revert, which MUST NOT be higher than the actual maximum that would be accepted (it should underestimate if necessary). This assumes that the user has infinite assets, i.e. 


maxMint:
MUST return the maximum amount of shares mint would allow to be deposited to receiver and not cause a revert, which MUST NOT be higher than the actual maximum that would be accepted (it should underestimate if necessary). This assumes that the user has infinite assets, i.e. MUST NOT rely on balanceOf of asset.
```

The current implementation of `maxMint` and `maxDeposit` always returns maximum value of uint256 instead.

## Proof of Concept

```
erc-4626/ERC4626DepositOnly.sol:103:    function maxMint(address) public view virtual returns (uint256) {
erc-4626/ERC4626DepositOnly.sol-104-        return type(uint256).max;
--
erc-4626/ERC4626MultiToken.sol:274:    function maxMint(address) public view virtual returns (uint256) {
erc-4626/ERC4626MultiToken.sol-275-        return type(uint256).max;
--
erc-4626/ERC4626.sol:153:    function maxMint(address) public view virtual returns (uint256) {
erc-4626/ERC4626.sol-154-        return type(uint256).max;
--
erc-4626/UlyssesERC4626.sol:116:    function maxMint(address) public view virtual returns (uint256) {
erc-4626/UlyssesERC4626.sol-117-        return type(uint256).max;

erc-4626/ERC4626DepositOnly.sol:98:    function maxDeposit(address) public view virtual returns (uint256) {
erc-4626/ERC4626DepositOnly.sol-99-        return type(uint256).max;
--
erc-4626/ERC4626MultiToken.sol:269:    function maxDeposit(address) public view virtual returns (uint256) {
erc-4626/ERC4626MultiToken.sol-270-        return type(uint256).max;
--
erc-4626/ERC4626.sol:148:    function maxDeposit(address) public view virtual returns (uint256) {
erc-4626/ERC4626.sol-149-        return type(uint256).max;
--
erc-4626/UlyssesERC4626.sol:112:    function maxDeposit(address) public view virtual returns (uint256) {
erc-4626/UlyssesERC4626.sol-113-        return type(uint256).max;
```



## Recommended Mitigation Steps

Ensure compliance with EIP-4626 specifications. Those function should reflect the limitation of assets supply. For comparison, you can examine how it's done in `maia/tokens/ERC4626PartnerManager.sol`:

```
maia/tokens/ERC4626PartnerManager.sol:161:    function maxDeposit(address) public view virtual override returns (uint256) {
maia/tokens/ERC4626PartnerManager.sol-162-        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

maia/tokens/ERC4626PartnerManager.sol:167:    function maxMint(address) public view virtual override returns (uint256) {
maia/tokens/ERC4626PartnerManager.sol-168-        return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;
```




# [L-02] Potential underflow in TalosStrategyVanilla

## Links

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L130-L131

## Impact

Contract substracts token's balance from protocol fees. However, there's no check which will make sure, that lower value is not substracted from the bigger one. Function may revert due to underflow.

## Proof of Concept

```
File: talos/TalosStrategyVanilla.sol
122          protocolFees0 += earnedProtocolFees0;
123          protocolFees1 += earnedProtocolFees1;
[...]
130          uint256 balance0 = token0.balanceOf(address(this)) - protocolFees0;
131          uint256 balance1 = token1.balanceOf(address(this)) - protocolFees1;
```

Since `protocolFees0`, `protocolFees1` are always increased (by `earnedProtocolFees0`, `earnedProtocolFees1`) - an assumption that:
```
token0.balanceOf(address(this)) > protocolFees0;
token1.balanceOf(address(this)) > protocolFees1;
```
always occurs might be incorrect. There might be a case where:

```
token0.balanceOf(address(this)) < protocolFees0;
token1.balanceOf(address(this)) < protocolFees1;
```
which will lead to underflow and revert.

## Recommended Mitigation Steps

Enforce an additional check, that:

```
token0.balanceOf(address(this)) >= protocolFees0;
token1.balanceOf(address(this)) >= protocolFees1;
```


# [N-01] Misleading function naming in /rewards/depots/SingleRewardsDepot.sol

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L32-L33

```
File: rewards/depots/SingleRewardsDepot.sol

32:      function getRewards() external override onlyFlywheelRewards returns (uint256 balance) {  
33:          return transferRewards(asset, rewardsContract);
```

The naming of function `getRewards` suggests, that it is a function responsible for displaying current rewards. Instead, this function is responsible for transfering rewards (as `transferRewards` suggests).
Since this is a external function (available for `onlyFlywheelRewards`) - its name may be misleading for `onlyFlywheelRewards` callers. They might call them just to see how much rewards they have, while this function will transfer those rewards. 

Recommendation: Avoid naming functions as `get*` - when its implementation does more than just getting a result.


## [N-02] Lack of consistency in mint() implementation for ERC20hTokenBranch.sol

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenBranch.sol#L23


```
File: ulysses-omnichain/token/ERC20hTokenBranch.sol

23:	function mint(address account, uint256 amount) external override onlyOwner returns (bool) {
24:	        _mint(account, amount);
25:	        return true;
26:	    }
```

`mint` function returns true for `ERC20hTokenBranch`, while other minting functions do not return any boolean:

```
./maia/hermes/tokens/bHermesBoost.sol:    function mint(address to, uint256 amount) external onlybHermes {
./maia/hermes/tokens/bHermesGauges.sol:    function mint(address to, uint256 amount) external onlybHermes {
./maia/hermes/tokens/bHermesVotes.sol:    function mint(address to, uint256 amount) external onlybHermes {
./maia/hermes/tokens/HERMES.sol:    function mint(address account, uint256 amount) external onlyOwner {
./maia/maia/tokens/Maia.sol:    function mint(address account, uint256 amount) external onlyOwner {
```

Recommendation: Re-implmement every minting, so does it will return boolean too.

Moreover, there is inconsistence in behaviour for minting `0`. Whenever user mints `0` token (so basically, there will be no minting) - function `mint` will return true.

Recommendation: Check if amount of minting is greater than zero.

