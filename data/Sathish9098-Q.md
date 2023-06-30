# LOW FINDINGS

##

## [L-1] ``getUserGaugeBoost[user][msg.sender]`` can be invalid if ``totalSupply`` is zero

### Impact

The function ``getUserGaugeBoost[user][msg.sender]`` will always be invalid if the total supply of the gauge is zero. This is because the ``totalGaugeBoost`` field in the ``GaugeState`` struct is initialized with the total supply of the gauge. If the total supply is zero, then the ``totalGaugeBoost`` field will also be zero, which will make the ``getUserGaugeBoost[user][msg.sender]`` value invalid

### POC

```solidity
FILE: Breadcrumbs2023-05-maia/src/erc-20/ERC20Boost.sol

131: getUserGaugeBoost[user][msg.sender] =
132:            GaugeState({userGaugeBoost: userGaugeBoost, totalGaugeBoost: totalSupply.toUint128()});

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L131-L132

### Recommended Mitigation
The function could be modified to check if the total supply of the gauge is greater than zero before initializing the ``totalGaugeBoost`` field. This would prevent the ``getUserGaugeBoost[user][msg.sender]`` value from being invalid

```solidity

if (totalSupply > 0) {
        getUserGaugeBoost[user][msg.sender] =
            GaugeState({userGaugeBoost: userGaugeBoost, totalGaugeBoost: totalSupply.toUint128()});
    }

```

##

## [L-2] Unchecked subtraction of ``uint32`` from ``uint`` can lead to unexpected results in ``incrementFreezeWindow`` function

### Impact
The function unchecked { if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError(); } in that ``unchecked`` keyword tells the compiler to not check for overflows. This means that if the subtraction of cycle and block.timestamp overflows, the function will not revert.

This may be not problem now but may cause problems in the future .

```solidity
FILE: 2023-05-maia/src/erc-20/ERC20Gauges.sol

204:  unchecked {
205:            if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError();
206:        }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L204-L206

### Recommended Mitigation

Remove ``unchecked`` from ``cycle - block.timestamp`` subtraction operation. If any overflow/Underflows this will revert 

##

## [L-3] ``decimals()`` not part of ERC20 standard

### Impact
decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

### POC

```solidity
FILE: Breadcrumbs2023-05-maia/src/erc-4626/ERC4626MultiToken.sol

51: require(ERC20(_assets[i]).decimals() == 18);

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626MultiToken.sol#L51

### Recommended Mitigation

This code is more robust than the original code because it will not fail if the token does not implement the decimals() function.



```solidity
if (erc20(_assets[i]).supportsInterface(IERC20Decimals(1))) {
  require(erc20(_assets[i]).decimals() == 18);
} else {
  revert('Token does not implement IERC20Decimals');
}

```
This code will revert if the token does not support the IERC20Decimals interface or if the decimals property of the token is not equal to 18. This ensures that the token will always have the correct decimals property.

##

## [L-4] ``assets``,``assetsAmounts`` array lengths not checked

### Impact
The issue is that the ``assets[i]`` variable is a ``state variable``, while the ``assetsAmounts`` variable is a ``user-defined array``. This means that there is a chance that the two variables could be ``mismatched``. If the ``two variables`` are ``mismatched``, then the ``safeTransferFrom`` function could ``revert``.

If the two variables length are mismatched, then an array index out of bound exception may occur

### POC

```solidity
FILE: 2023-05-maia/src/erc-4626/ERC4626MultiToken.sol

66: function receiveAssets(uint256[] memory assetsAmounts) private {
67:        uint256 length = assetsAmounts.length;
68:        for (uint256 i = 0; i < length;) {
69:            assets[i].safeTransferFrom(msg.sender, address(this), assetsAmounts[i]);
70:
71:            unchecked {
72:                i++;
73:            }

```

### Recommended Mitigation

Add the length check 
```solidity

require(assets.length==assetsAmounts.length, "The length mismatched");

```

##

## [L-5] The function ``deposit`` does not have a limit on the amount of tokens that can be deposited

### Impact
The ``deposit()`` function does not check to see if the total amount of tokens that the user is trying to deposit is greater than the total supply of tokens in the contract.

If an ``attacker`` were to deposit a large amount of tokens, it could cause the contract to run out of funds. This could also cause the contract to become ``unstable``. 

### POC

```solidity
FILE: Breadcrumbs2023-05-maia/src/erc-4626/ERC4626MultiToken.sol

function deposit(uint256[] calldata assetsAmounts, address receiver)
        public
        virtual
        nonReentrant
        returns (uint256 shares)
    {
        // Check for rounding error since we round down in previewDeposit.
        require((shares = previewDeposit(assetsAmounts)) != 0, "ZERO_SHARES");

        // Need to transfer before minting or ERC777s could reenter.
        receiveAssets(assetsAmounts);

        _mint(receiver, shares);

        emit Deposit(msg.sender, receiver, assetsAmounts, shares);

        afterDeposit(assetsAmounts, shares);
    }


```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626MultiToken.sol#L93-L110

### Recommended Mitigation
You could add a ``limit`` to the ``amount of tokens`` that can be ``deposited``

```solidity

require(shares <= maxSharesAllowed, "INSUFFICIENT_BALANCE");

```

##

## [L-6] The ``distribute()`` function does not specify the ``gauge`` that the incentive will be ``created from``

### Impact
The function distribute is not safe. The attacker could call the function and create an incentive from a gauge that they control. This could allow the attacker to steal the incentive funds.

### POC

```solidity
FILE: 2023-05-maia/src/gauges/UniswapV3Gauge.sol

53: function distribute(uint256 amount) internal override {
54:        IUniswapV3Staker(uniswapV3Staker).createIncentiveFromGauge(amount);
55:    }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/UniswapV3Gauge.sol#L53C60-L55

### Recommended Mitigation
If the gauge parameter is not null, then the function will call the ``createIncentiveFromGauge`` function of the ``IUniswapV3Staker`` contract. The ``createIncentiveFromGauge`` function will create an incentive from the specified gauge and deposit the incentive funds into the contract.

This code will make the distribute function more secure and will prevent an attacker from stealing the incentive funds.

```solidity

function distribute(uint256 amount, address gauge) internal override {
    IUniswapV3Staker(uniswapV3Staker).createIncentiveFromGauge(amount, gauge);
}

```

##

## [L-7] Adding ``addStrategyForRewards()`` default strategy to the flywheel could be a problem if the default strategy is not appropriate for the gauge.

### Impact
Using a default strategy that is not appropriate for the gauge could be significant. For example, if the default strategy is designed for a different type of gauge, it could result in the incentive funds being distributed in an incorrect manner. This could lead to a number of problems,

- The incentive funds could be distributed to the wrong parties.
- The incentive funds could be distributed in an unfair manner.
- The incentive funds could be used for malicious purposes.

### POC

```solidity
FILE: 2023-05-maia/src/gauges/factories/BribesFactory.sol

function addGaugetoFlywheel(address gauge, address bribeToken) external onlyGaugeFactory {
        if (address(flywheelTokens[bribeToken]) == address(0)) createBribeFlywheel(bribeToken);

        flywheelTokens[bribeToken].addStrategyForRewards(ERC20(gauge));
    }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/factories/BribesFactory.sol#L72-L76

### Recommended Mitigation
You could modify the function to allow the ``caller`` to specify the ``strategy`` that they want to add to the ``flywheel``

##

## [L-8]  ``createBribeFlywheel`` does not allow the caller to remove the flywheel once it has been added

### Impact
This could be a problem if the flywheel is no longer needed or if it is no longer being used.

### POC

```solidity
FILE: 2023-05-maia/src/gauges/factories/BribesFactory.sol

 function createBribeFlywheel(address bribeToken) public {
        if (address(flywheelTokens[bribeToken]) != address(0)) revert BribeFlywheelAlreadyExists();

        FlywheelCore flywheel = new FlywheelCore(
            bribeToken,
            FlywheelBribeRewards(address(0)),
            flywheelGaugeWeightBooster,
            address(this)
        );

        flywheelTokens[bribeToken] = flywheel;

        uint256 id = bribeFlywheels.length;
        bribeFlywheels.push(flywheel);
        bribeFlywheelIds[flywheel] = id;
        activeBribeFlywheels[flywheel] = true;

        flywheel.setFlywheelRewards(address(new FlywheelBribeRewards(flywheel, rewardsCycleLength)));

        emit BribeFlywheelCreated(bribeToken, flywheel);
    }

```

### Recommended Mitigation
Implement deleteBribeFlywheel() function to remove flywheel is no longer needed or if it is no longer being used.

##

## [L-9] 






Create/create2 used . So related bugs should be investigated 

bHermes.sol possible to mint zero address if to is address(0)

Initialize functions could be front run 




[H-1]	Incorrect comparison implementation	2
[M-1]	The owner is a single point of failure and a centralization risk	92
[L-1]	Contracts are vulnerable to fee-on-transfer token related accounting issues	91
[L-2]	Array does not have a pop function	44
[L-3]	Setters should have initial value check	14
[L-4]	Division by zero not prevented	21
[L-5]	Loss of precision	23
[L-6]	Signature Malleability of EVM's ecrecover	2
[L-7]	Use of floating pragma	52
[L-8]	Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists	73
[L-9]	External calls in an un-bounded for-loop may result in a DOS	48
[L-10]	Unsafe casting may overflow	106
[L-11]	Some ERC20 revert on zero value transfer	91
[L-12]	Zero address check is missing	59
[L-13]	Some ERC20 tokens dust would be remained in the contract	1
[L-14]	Division before multiplication can lead to precision errors	1
[L-15]	Array lengths not checked	12
[L-16]	Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership	32
[L-17]	Do not use deprecated library functions	9
[L-18]	No limits when setting min/max amounts	15
[L-19]	Approve type(uint256).max not work with some tokens	9
[L-20]	Owner can renounce Ownership	29
[L-21]	Unsafe ERC20 operations	15
[N-1]	Typos in the code	98
[N-2]	Use a more recent version of solidity	68
[N-3]	Lines are too long	192
[N-4]	Inconsistent spacing in comments	1283
[N-5]	Large numeric literals should use underscores for readability	20
[N-6]	TODO in the code	2
[N-7]	Using named parameters in mapping is best practice	183
[N-8]	Function ordering does not follow the Solidity style guide	124
[N-9]	Private and internal variables must start with an underscore	231
[N-10]	Function parameter name must be in mixedCase	5
[N-11]	Variable name must be in mixedCase	6
[N-12]	It is standard for all external and public functions to be overridden from an interface	367
[N-13]	Constant name must be in capitalized SNAKE_CASE	23
[N-14]	Function must not be longer than 50 lines	28
[N-15]	Function name must be in mixedCase	2
[N-16]	Contract name must be in CamelCase	5
[N-17]	Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)	2
[N-18]	constants should be defined rather than using magic numbers	102
[N-19]	Reverts should use customer errors instead of strings	283
[N-20]	Event missing indexed field	16
[N-21]	Constants in comparisons should appear on the left side	180
[N-22]	Use modifier instead of require/if for special msg.sender	11
[N-23]	Use multiple require() and if statements instead of &&	44
[N-24]	else-block not required	5
[N-25]	Unsigned divisions can be marked as unchecked	7
[N-26]	Import only specific files	2
[N-27]	require()/revert() statements should have descriptive reason strings	35
[N-28]	Interfaces should be indicated with an I prefix in the contract name	6
[N-29]	Redundant return statement with named return variable	22
[N-30]	Use safeTransferOwnership instead of transferOwnership function	3
[N-31]	Contract name must be in CamelCase.	7
[N-32]	Critical functions should be a two step procedure	3
[N-33]	Shorthand way to write if / else statement	1
[N-34]	Use require instead of assert	3
[N-35]	Imports could be organized more systematically	119
[N-36]	Constant redefined elsewhere	37
[N-37]	NatSpec @return argument is missing	75
[N-38]	NatSpec @param is missing	35