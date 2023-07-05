L-01: decrementGaugeBoost() It is recommended to call `updateUserBoost()` after modification in order to synchronize getUserBoost[user]

```solidity
   function decrementGaugeBoost(address gauge, uint256 boost) public {
        GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];
        if (boost >= gaugeState.userGaugeBoost) {
            _userGauges[msg.sender].remove(gauge);
            delete getUserGaugeBoost[msg.sender][gauge];

            emit Detach(msg.sender, gauge);
        } else {
            gaugeState.userGaugeBoost -= boost.toUint128();

            emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
        }

+       updateUserBoost(msg.sender)?
    }
```

L-02: decrementGaugesBoostIndexed() Wrong loop condition judgment
```solidity
   function decrementGaugesBoostIndexed(uint256 boost, uint256 offset, uint256 num) public {
        address[] memory gaugeList = _userGauges[msg.sender].values();

        uint256 length = gaugeList.length;
-       for (uint256 i = 0; i < num && i < length;) {
+       for (uint256 i = 0; i < num && i  + offset < length;) {
            address gauge = gaugeList[offset + i];

            GaugeState storage gaugeState = getUserGaugeBoost[msg.sender][gauge];

            if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {
                require(_userGauges[msg.sender].remove(gauge)); // Remove from set. Should never fail.
                delete getUserGaugeBoost[msg.sender][gauge];

                emit Detach(msg.sender, gauge);
            } else {
                gaugeState.userGaugeBoost -= boost.toUint128();

                emit DecrementUserGaugeBoost(msg.sender, gauge, gaugeState.userGaugeBoost);
            }

            unchecked {
                i++;
            }
        }
    }
```
L-03: notAttached() It is recommended to execute `updateUserBoost` before judging and accurate

```solidity
abstract contract ERC20Boost is ERC20, Ownable, IERC20Boost {
...

    modifier notAttached(address user, uint256 amount) {

+       updateUserBoost(user);
        if (freeGaugeBoost(user) < amount) revert AttachedBoost();
        _;
    }

```    


L-04: ERC4626MultiToken.constructor Suggest adding a judgment that the asset cannot be duplicated
```solidity
abstract contract ERC4626MultiToken is ERC20, ReentrancyGuard, IERC4626MultiToken {
...
    constructor(address[] memory _assets, uint256[] memory _weights, string memory _name, string memory _symbol)
        ERC20(_name, _symbol, 18)
    {
...

        for (uint256 i = 0; i < length;) {
+           if (assetId[_assets[i]] != 0) revert AssetAlreadyAdded();
            require(ERC20(_assets[i]).decimals() == 18);
            require(_weights[i] > 0);

            _totalWeights += _weights[i];
            assetId[_assets[i]] = i + 1;

            emit AssetAdded(_assets[i], _weights[i]);

            unchecked {
                i++;
            }
        }
        totalWeights = _totalWeights;
    }

```
L-05: TalosStrategyStaked.transfer/transferFrom  recommend call _earnFees() to refresh rewards before `accrue()`

```solidity
    function transfer(address _to, uint256 _amount) public override returns (bool) {

+       _earnFees(tokenId);
        flywheel.accrue(ERC20(address(this)), msg.sender, _to);
+       _stake(tokenId);
        return super.transfer(_to, _amount);
    }

    function transferFrom(address _from, address _to, uint256 _amount) public override returns (bool) {
+       _earnFees(tokenId);    
        flywheel.accrue(ERC20(address(this)), _from, _to);
+       _stake(tokenId);        
        return super.transferFrom(_from, _to, _amount);
    }
```