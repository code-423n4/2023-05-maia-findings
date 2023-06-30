# GAS REPORT

## [G-01] Some staments can coverted to a modifier.

The bhermes contract has some validation that is repeatly in several functions, the devs can create a modifier to decrease the gas in those functon.

```
file: src/hermes/bHermes.sol#L140-L149
function transfer(address to, uint256 amount) public virtual override returns (bool) {
        uint256 userBalance = balanceOf[msg.sender];

        if (
            userBalance - userClaimedWeight[msg.sender] < amount || userBalance - userClaimedBoost[msg.sender] < amount
                || userBalance - userClaimedGovernance[msg.sender] < amount
        ) revert InsufficientUnderlying();

        return super.transfer(to, amount);
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/bHermes.sol#L140

```
file: src/hermes/bHermes.sol#L158-L167
function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {
        uint256 userBalance = balanceOf[from];

        if (
            userBalance - userClaimedWeight[from] < amount || userBalance - userClaimedBoost[from] < amount
                || userBalance - userClaimedGovernance[from] < amount
        ) revert InsufficientUnderlying();

        return super.transferFrom(from, to, amount);
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/bHermes.sol#L158C5-L167C6

The modifier :

```
modifier checkTransfer (uint256 amount) {
        uint256 userBalance = balanceOf[from];

        if (
            userBalance - userClaimedWeight[from] < amount || userBalance - userClaimedBoost[from] < amount
                || userBalance - userClaimedGovernance[from] < amount
        ) revert InsufficientUnderlying();

}
```
Devs can implemented in both functions

```
function transfer(address to, uint256 amount) public virtual  checkTransfer(amount) override returns (bool) {}
function transferFrom(address from, address to, uint256 amount) public virtual checkTransfer(amount) override returns (bool) {}

```

## [G-02] check for 0 value insted check if is not 0 value

The flyWheelCore.sol contract is checking for if is not 0 value and do the logic:
```
file:src/rewards/base/FlywheelCore.sol#L94-L104
function claimRewards(address user) external {
        uint256 accrued = rewardsAccrued[user];

        if (accrued != 0) {
            rewardsAccrued[user] = 0;

            rewardToken.safeTransferFrom(address(flywheelRewards), user, accrued);

            emit ClaimRewards(user, accrued);
        }
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/base/FlywheelCore.sol#L94C5-L104C6

Is more gas efficients check for 0 value and revert or return and the do the logic:
```
function gasClaimRewards(address user) external { //23941
        uint256 accrued = rewardsAccrued[user];

        if (accrued == 0) {
            
           revert();
        }
         rewardsAccrued[user] = 0;
         rewardToken.safeTransferFrom(address(flywheelRewards), user, accrued);
         emit ClaimRewards(user, accrued);
    }
```

To prove this we can do this simple example in remix:

```
function claimRewards(address user) external { 
        uint256 accrued = rewardsAccrued[user];

        if (accrued != 0) {
            rewardsAccrued[user] = 0;
            emit ClaimRewards(user, accrued);
        }
    }

    function gasClaimRewards(address user) external { 
        uint256 accrued = rewardsAccrued[user];

        if (accrued == 0) {
           revert();
        }
         rewardsAccrued[user] = 0;
            emit ClaimRewards(user, accrued);
    }
```
Diferent betwent both function:

Actual function:

gas	                38594 
transaction cost	23960 gas 
execution cost	        7328 gas 

Gas optmize function:
gas	                38573 gas
transaction cost	23941 gas 
execution cost	        7309 gas 

## [G-03] The contract UlyssesToken.sol is doing a sum that can be replace.

The UlyssesToken.sol is doing a sum in addAsset function to keep the length and then pushing into the array but you can push first and then keep the length.

to see this more clearly check the code:

```
file:src/ulysses-amm/UlyssesToken.sol#L44-L57
function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {
        ...
        assetId[asset] = assets.length + 1;
        assets.push(asset);
        ...
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesToken.sol#L44C5-L57C6

Insted of doing a assets.length + 1, the devs can change the position of the 2 lines above and you dont gonna need the sum saving gas:
```
function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {
        ...
        assets.push(asset);
        assetId[asset] = assets.length;
        ...
    }
```

## [G-04] The contract BrachBrigeAgent.sol is creating a new innecesary memory variable.

The contract BrachBrigeAgent.sol is creating a new  memory variable to keep an input value and then passing the new variable to a function, insted of passing the variable directly.

```
file: src/ulysses-omnichain/BranchBridgeAgent.sol
function retryDeposit(
        bool _isSigned,
        uint32 _depositNonce,
        bytes calldata _params,
        uint128 _remoteExecutionGas,
        uint24 _toChain
    ) external payable lock requiresFallbackGas {
            ...
            uint32 nonce = _depositNonce;

            if (_isSigned) {
                packedData = abi.encodePacked(
                    bytes1(0x06),
                    msg.sender,
                    uint8(getDeposit[_depositNonce].hTokens.length),
                    nonce,
                    getDeposit[nonce].hTokens,
                    getDeposit[nonce].tokens,
                    getDeposit[nonce].amounts,
                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                );
           ...
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L367

In the same function the contract is passing the nonce directly from the input several times and in the above case is creating a new memory variable and then passing the new variable:

```
function retryDeposit(
        bool _isSigned,
        uint32 _depositNonce,
        bytes calldata _params,
        uint128 _remoteExecutionGas,
        uint24 _toChain
    ) external payable lock requiresFallbackGas {
            ...
            if (_isSigned) {
                packedData = abi.encodePacked(
                    bytes1(0x06),
                    msg.sender,
                    uint8(getDeposit[_depositNonce].hTokens.length),
                    _depositNonce,
                    getDeposit[nonce].hTokens,
                    getDeposit[nonce].tokens,
                    getDeposit[nonce].amounts,
                    _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
                    _toChain,
                    _params,
                    msg.value.toUint128(),
                    _remoteExecutionGas
                );
           ...
    }
```

## [G-05] The contract BrachPort.sol is doing the same substract 3 time in the same function:

The contract BrachPort.sol is doing the same substract 3 time in the same function:
```
file:src/ulysses-omnichain/BranchPort.sol#L260-L283
function bridgeOutMultiple(
        address _depositor,
        address[] memory _localAddresses,
        address[] memory _underlyingAddresses,
        uint256[] memory _amounts,
        uint256[] memory _deposits
    ) external virtual requiresBridgeAgent {
        for (uint256 i = 0; i < _localAddresses.length;) {
            if (_deposits[i] > 0) {
                _underlyingAddresses[i].safeTransferFrom(
                    _depositor,
                    address(this),
                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
                );
            }
            if (_amounts[i] - _deposits[i] > 0) {
                _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);
                ERC20hTokenBranch(_localAddresses[i]).burn(_amounts[i] - _deposits[i]);
            }
            unchecked {
                i++;
            }
        }
    }
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L260C5-L283C6

Insted of doing the rest 3 times the devs can save the sustract in a memory variable and save gas:

```
function bridgeOutMultiple(
        address _depositor,
        address[] memory _localAddresses,
        address[] memory _underlyingAddresses,
        uint256[] memory _amounts,
        uint256[] memory _deposits
    ) external virtual requiresBridgeAgent {
        for (uint256 i = 0; i < _localAddresses.length;) {
            if (_deposits[i] > 0) {
                _underlyingAddresses[i].safeTransferFrom(
                    _depositor,
                    address(this),
                    _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
                );
            }
            uint256 substract = _amounts[i] - _deposits[i];
            if (substract > 0) {
                _localAddresses[i].safeTransferFrom(_depositor, address(this), substract);
                ERC20hTokenBranch(_localAddresses[i]).burn(substract);
            }
            unchecked {
                i++;
            }
        }
    }
```



