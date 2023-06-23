- L1. Delegate function should revert if newDelegatee = address(0)
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L161-L178

The _delegate function will continue execution if the newDelegate address is Zero after undelegate the user. So the user will end up without a delegator and he may not be notified of that.
```
if (newDelegatee != address(0)) { //@audit QA if address 0 the user will be already undelegated. Should revert here
            _incrementDelegation(delegator, newDelegatee, freeVotes(delegator));
        }
```

- L2. `_setWhitelistAccountExpiration` doesn't check if the expiration set is not in the future, causing the account to be automaticely expired when `isWhitelisted` invoked.
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L447C5-L455C6

```
function isWhitelisted(address account) public view returns (bool) {
        return (whitelistAccountExpirations[account] > block.timestamp);
    }
```

```
function _setWhitelistAccountExpiration(address account, uint256 expiration) external {
        require(
            msg.sender == admin || msg.sender == whitelistGuardian,
            "GovernorBravo::_setWhitelistAccountExpiration: admin only"
        );
        whitelistAccountExpirations[account] = expiration;

        emit WhitelistAccountExpirationSet(account, expiration);
    }
```

- L3. BandwidthLen could be higher than the MAX_DESTINATIONS value defined due to usage of `=>` instead of `>`.
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L171

```
index = bandwidthStateList.length;

        if (index > MAX_DESTINATIONS) revert TooManyDestinations();


```

- L4. Functions marked as payable while it doesn't handle native tokens.
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L102C5-L116C6
```
function depositToPort(address underlyingAddress, uint256 amount) external payable lock { //@audit QA shouldn't be marked as payable.
        IArbPort(localPortAddress).depositToPort(
            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
        );
    }


    function withdrawFromPort(address localAddress, uint256 amount) external payable lock { //@audit QA shouldn't be marked as payable.
        IArbPort(localPortAddress).withdrawFromPort(msg.sender, msg.sender, localAddress, amount);
    }
```

- L5. `addPartner` and `addVault` use comparison instead of assigment.
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L58C5-L73C6
```
function addPartner(PartnerManager newPartnerManager) external onlyOwner {
        uint256 id = partners.length;
        partners.push(newPartnerManager);
        partnerIds[newPartnerManager] == id; // @audit this should be an assigment and not comparison

        emit AddedPartner(newPartnerManager, id);
    }

    /// @inheritdoc IPartnerManagerFactory
    function addVault(IBaseVault newVault) external onlyOwner {
        uint256 id = vaults.length;
        vaults.push(newVault);
        vaultIds[newVault] == id; // @audit this should be an assigment and not comparison

        emit AddedVault(newVault, id);
    }
```

- NC1. claimReward function will send the entire reward if the amountRequested is Zero.
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L262C4-L275C1

The function doesn't check if amountRequested > 0, hence the function will reset the rewards to 0 and send the full reward.

```
function claimReward(address to, uint256 amountRequested) external returns (uint256 reward) {
        reward = rewards[msg.sender];
        if (amountRequested != 0 && amountRequested < reward) {
            reward = amountRequested;
            rewards[msg.sender] -= reward;
        } else {
            rewards[msg.sender] = 0;
        }

        if (reward > 0) hermes.safeTransfer(to, reward);

        emit RewardClaimed(to, reward);
    }
```

- NC2. IUniswapV3Staker.sol doesn't implement the `restakeToken` function
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol

- NC3. `createIncentive` doesn't allow to create new incentive for the time `Now` as supposed due to use `<=` instead of `<`.
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L164

```
if (startTime <= block.timestamp) revert IncentiveStartTimeMustBeNowOrInTheFuture();
```
