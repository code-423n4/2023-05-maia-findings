# LOW FINDINGS

| Low Count | Issues | Instances |
|----------|----------|----------|
|[L-1]| Lack of ``CEI (Check-Effects-Interactions)`` might be ``susceptible`` to a ``reentrancy attack``| 3 |
| [L-2]  | ``address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount) `` only transfer first value   | 1   |
| [L-3]  | ``Incorrect`` Handling of ``Negative Values`` in ``uniswapV3SwapCallback()`` Function  | 2 |
| [L-4]  | ``addPartner()`` and ``addVault()`` Functions Lack Address Existence Check in ``partners`` and ``vaults Arrays``  | 1  |
| [L-5]  | Invalid ``getUserGaugeBoost[user][msg.sender]`` when ``totalSupply`` is ``Zero``  | 1   |
| [L-6]  | ``Unchecked`` Subtraction of ``uint32`` from ``uint`` in ``incrementFreezeWindow()`` Function May Lead to ``Unexpected Results``   | 1   |
| [L-7]  | ``decimals()`` not part of ERC20 standard  | 1  |
| [L-8]  | Potential ``Array Index Out of Bounds Exception`` Due to Missing Length Check in ``assets and assetsAmounts`` Arrays   | 1   |
| [L-9]  | Missing ``Token Deposit Limit`` in the ``deposit()`` Function   | 1   |
| [L-10]  | ``distribute()`` Function Lacks ``Gauge Identification`` for ``Incentive Creation``  | 1  |
| [L-11]  | Default Strategy in ``addStrategyForRewards()`` Function May Not Suit the ``Gauge``  | 1  |
| [L-12]  | ``Inability`` to Remove ``Flywheel`` in ``createBribeFlywheel`` Function Poses Limitation |  1  |
| [L-13]  | ``Tautology`` when checking  ``msg.sender``  | 1   |
| [L-14]  | Ensuring Proper Verification of ``Callee Address`` in Constructor to Avoid Expected Revert| 1 |
| [L-15]  | ``Timelocks`` not implemented as per ``documentation``  | -  |
| [L-16]  | ``Unexpected Results`` when Converting Negative Values using ``uint256(int256)``  | 2  |
| [L-17]  | Lack of ``Pool, token`` Removal Mechanism in ``UlyssesFactory Contract``  | 1  |
| [L-18]  | Update ``depositNonce`` Value ``Incrementation`` in every Function Calls  | 3  |
| [L-19]  | Unused/empty fallback() function | 2  |
| [L-20]  | ``withdraw()`` should use ``lock`` modifier to avoid reentrancy attack | 1  |
| [L-21]  | Non-zero approval may be ``problems`` for some tokens ``like USDT`` | 1  |
| [L-22]  | Contract existence of ``target[i]`` not checked before low level ``.call`` function| 1   |
| [L-23]  | Gas ``griefing/theft`` is possible on ``unsafe external call``  | 1  |
| [L-24]  | Use ``.call`` instead of ``.transfer`` to send ether  | 1  |
| [L-25]  | ``Return values`` of ``transfer()/transferFrom()`` not checked  |  2  |



# NON CRITICAL FINDINGS

| NC Count | Issues | Instances |
|----------|----------|----------|
| [NC-1]  | Add an event for critical parameter changes  | 4  |
| [NC-2]  | Function does not followed the standard solidity naming  | 1  |
| [NC-3]  | No same value input control    | 4   |
| [NC-4]  | Status of ``approve()`` functions not checked   | 2 |
| [NC-5]  | Rectify all warnings to avoid unexpected behavior  | - |



##

## [L-1] Lack of ``CEI (Check-Effects-Interactions)`` might be ``susceptible`` to a ``reentrancy attack``

### Impact

Token withdrawal is performed by calling the ``withdraw`` function of the ``IPortStrategy`` contract with the specified parameters. Following that, the ``getPortStrategyTokenDebt and getStrategyTokenDebt`` mappings are updated by subtracting the ``amountToWithdraw`` from their respective values.it might be ``susceptible`` to a ``reentrancy attack``. In a ``reentrancy attack``, an external contract can maliciously call back into the contract during the execution of the ``withdrawal process``, potentially leading to unexpected behavior and security vulnerabilities.

### POC
```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/BranchPort.sol

180: IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);
181:
182:        getPortStrategyTokenDebt[_strategy][_token] -= amountToWithdraw;
183:        getStrategyTokenDebt[_token] -= amountToWithdraw;

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L180-L183

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L248-L250

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L276-L277

### Recommended Mitigation

``CEI`` pattern could involve updating the necessary state variables, such as`` getPortStrategyTokenDebt`` and ``getStrategyTokenDebt``, before invoking the ``withdraw`` function of the ``IPortStrategy contract``

```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/BranchPort.sol

+ 182:        getPortStrategyTokenDebt[_strategy][_token] -= amountToWithdraw;
+ 183:        getStrategyTokenDebt[_token] -= amountToWithdraw;
180: IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);
181:
- 182:        getPortStrategyTokenDebt[_strategy][_token] -= amountToWithdraw;
- 183:        getStrategyTokenDebt[_token] -= amountToWithdraw;

```

##

## [L-2] ``address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount) `` only transfer first value 

### Impact
The swap function you provided has a transfer statement that only transfers the amount from ``msg.sender`` to the ``contract address`` for ``routes[0].from``. This means that it transfers the amount for only the`` first element`` in the ``routes array``, and ``subsequent`` elements are ``not accounted`` for in the transfer.

### POC

```solidity
FILE: 2023-05-maia/src/ulysses-amm/UlyssesRouter.sol

function swap(uint256 amount, uint256 minOutput, Route[] calldata routes) external returns (uint256) {
        address(getUlyssesLP(routes[0].from).asset()).safeTransferFrom(msg.sender, address(this), amount);

        uint256 length = routes.length;

        for (uint256 i = 0; i < length;) {
            amount = getUlyssesLP(routes[i].from).swapIn(amount, routes[i].to);

            unchecked {
                ++i;
            }
        }

        if (amount < minOutput) revert OutputTooLow();

        unchecked {
            --length;
        }

        address(getUlyssesLP(routes[length].to).asset()).safeTransfer(msg.sender, amount);

        return amount;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesRouter.sol#L73-L96

### Recommended Mitigation
Swap function that incorporates the ``loop`` to transfer amounts for all elements in the ``routes array``.

```solidity

for (uint256 i = 0; i < routes.length; i++) {
        address(getUlyssesLP(routes[i].from).asset()).safeTransferFrom(msg.sender, address(this), amount);
    }

```

##

## [L-3] ``Incorrect`` Handling of ``Negative Values`` in ``uniswapV3SwapCallback()`` Function

### Impact
The function uniswapV3SwapCallback() does not handle negative values in the amount0 and amount1 parameters in the right way.

The function checks if the value of ``amount0 or amount1`` is equal to zero, and if it is, it reverts. However, this does not prevent the function from being called with ``negative values``.

As per [docs](https://docs.uniswap.org/contracts/v3/reference/core/interfaces/callback/IUniswapV3SwapCallback) the function should check if the value of ``amount0 or amount1`` is ``less than zero``, and if it is, it should send the tokens to the ``pool``. If the value is ``greater than zero``, it should receive the ``tokens from the pool``

## POC
```solidity
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

function uniswapV3SwapCallback(int256 amount0, int256 amount1, bytes calldata _data) external {
        if (msg.sender != address(pool)) revert CallerIsNotPool();
        if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();
        SwapCallbackData memory data = abi.decode(_data, (SwapCallbackData));
        bool zeroForOne = data.zeroForOne;

        if (zeroForOne) address(token0).safeTransfer(msg.sender, uint256(amount0));
        else address(token1).safeTransfer(msg.sender, uint256(amount1));
    }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L333-L341

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L646-L652

### Recommended Mititgation
Need to implement ``controls`` to handle ``negative values`` also

##

## [L-4] ``addPartner()`` and ``addVault()`` Functions Lack Address Existence Check in ``partners`` and ``vaults Arrays``

```
In Solidity, arrays can accept duplicate values. There are no inherent restrictions or limitations on storing duplicate values in arrays.

```

### Impact

Create confusion and uncertainty about which ``PartnerManager, IBaseVault`` accounts is the legitimate one.This could make it difficult for users to interact with the contract or for partners to coordinate their activities

Increase the risk of errors and inconsistencies. If multiple ``PartnerManager,IBaseVault`` accounts are able to approve or deny transactions, it could be difficult to track which account has approved or denied a particular transaction. This could lead to errors and ``inconsistencies`` in the ``contract's state``.

### POC

```solidity
FILE: 2023-05-maia/src/maia/factories/PartnerManagerFactory.sol

function addPartner(PartnerManager newPartnerManager) external onlyOwner {
        uint256 id = partners.length;
        partners.push(newPartnerManager);
        partnerIds[newPartnerManager] == id;

        emit AddedPartner(newPartnerManager, id);
    }

function addVault(IBaseVault newVault) external onlyOwner {
        uint256 id = vaults.length;
        vaults.push(newVault);
        vaultIds[newVault] == id;

        emit AddedVault(newVault, id);
    }


```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/factories/PartnerManagerFactory.sol#L58-L64

### Recommended Mitigation
This code first checks if the ``newPartnerManager,newVault`` addresses already exists in the ``partners, vaults`` arrays 

```
```solidity

   if (partners[partnerIds[partnerManager]] == newPartnerManager) revert InvalidPartnerManager();
   if (vaults[vaultIds[vault]] == newVault) revert InvalidVault();

```
##

## [L-5]  Invalid ``getUserGaugeBoost[user][msg.sender]`` when ``totalSupply`` is ``Zero``

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

## [L-6] ``Unchecked`` Subtraction of ``uint32`` from ``uint`` in ``incrementFreezeWindow()`` Function May Lead to ``Unexpected Results``

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

## [L-7] ``decimals()`` not part of ERC20 standard

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

## [L-8] Potential ``Array Index Out of Bounds Exception`` Due to Missing Length Check in ``assets and assetsAmounts`` Arrays

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

## [L-9] Missing ``Token Deposit Limit`` in the ``deposit()`` Function

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

## [L-10] ``distribute()`` Function Lacks ``Gauge Identification`` for ``Incentive Creation``

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

## [L-11] Default Strategy in ``addStrategyForRewards()`` Function May Not Suit the ``Gauge``

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

## [L-12] ``Inability`` to Remove ``Flywheel`` in ``createBribeFlywheel`` Function Poses Limitation

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

## [L-13] ``Tautology`` when checking  ``msg.sender``

### Impact
While taking into account the address(0) output of ``msg.sender`` is something that must always be done: in this particular case the check is a [tautology](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L508)

Indeed:

- if ``msg.sender == pendingAdmin`` is true, then ``msg.sender != address(0)`` will always be `` true``
- if  ``msg.sender  == pendingAdmin``  is false, then msg.sender  != address(0) will never be evaluated

### POC

```solidity
FILE: 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol

507: require(
508:            msg.sender == pendingAdmin && msg.sender != address(0), "GovernorBravo:_acceptAdmin: pending admin only"
509:        );

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L507-L509

##

## [L-14]  Ensuring Proper Verification of ``Callee Address`` in Constructor to ``Avoid Expected Revert``

### Impact
``delegateTo`` function is called in the ``constructor``, it means that it is being invoked during the contract ``deployment process``. In that case, it is important to ensure that the ``callee`` address passed to the function is indeed a valid contract address.

### POC

```solidity
FILE: Breadcrumbs2023-05-maia/src/governance/GovernorBravoDelegator.sol


 constructor(
        address timelock_,
        address govToken_,
        address admin_,
        address implementation_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThreshold_
    ) public {
        // Admin set to msg.sender for initialization
        admin = msg.sender;

        delegateTo(
            implementation_,
            abi.encodeWithSignature(
                "initialize(address,address,uint256,uint256,uint256)",
                timelock_,
                govToken_,
                votingPeriod_,
                votingDelay_,
                proposalThreshold_
            )
        );

        _setImplementation(implementation_);

        admin = admin_;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegator.sol#L20

### Recommended Mitigation
Include the ``validation`` before calling the ``delegateTo``

```solidity
require(Address.isContract(callee), "callee is not a contract"); // Check if callee is a contract

```
##

## [L-15] ``Timelocks`` not implemented as per ``documentation``

```
``Does it use a timelock function?:  true``

```
### Impact

The documentation for the contract states that there are ``timelocks`` in place to prevent users from withdrawing their collateral asset immediately after depositing it. However, the code for the contract does not actually implement any ``timelocks``.

This is a potential ``security vulnerability``, as it could allow a user to withdraw their collateral asset immediately after depositing it, even if the collateral asset has not yet reached the required level of liquidity.


### Recommended Mitigation
Add the ``timelocks`` for critical setter functions as per documentation 

##

## [L-16] ``Unexpected Results`` when Converting Negative Values using ``uint256(int256)``

### Impact 
When converting a negative int256 value to a uint256 using ``uint256(int256)``, the result will be unexpected. if you try to convert a negative ``int256`` value to a ``uint256``, the result will be ``interpreted`` as the two's complement representation of the negative value. This means that the resulting uint256 value will be a ``large positive number``.

PROOF:
We attempt to convert the negative value ``-10`` to a ``uint256``. However, the resulting value will not be the expected value of ``-10`` but rather a ``large positive number``.

If you execute the function, the returned value will be ``115792089237316195423570985008687907853269984665640564039457584007913129639934``. This result corresponds to the two's complement representation of ``-10`` when ``interpreted`` as an ``unsigned uint256``.

### POC

```solidity
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

339: if (zeroForOne) address(token0).safeTransfer(msg.sender, uint256(amount0));
340: else address(token1).safeTransfer(msg.sender, uint256(amount1));

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L339-L340

### Recommended Mitigation:
Carefully handle ``conversions between signed and unsigned integer`` types and ensure that the values are within the ``valid ranges`` of the ``target type``.

##

## [L-17] Lack of ``Pool, token`` Removal Mechanism in ``UlyssesFactory Contract``

### Impact
Typically, in ``decentralized exchange protocols``, such as the ``Uniswap protocol``, there is a mechanism to ``remove pools`` that are ``no longer desired`` or ``have become inactive``. This removal mechanism is important for several reasons, including ``reducing clutter and maintaining`` an ``up-to-date list of active`` and relevant pools.

Without a pool removal mechanism in the ``UlyssesFactory`` contract, inactive or unwanted pools may ``persist indefinitely``, leading to a ``cluttered pool list``. This could potentially cause confusion for users and impact the ``overall efficiency of the protocol``.

### POC

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/factories/UlyssesFactory.sol#L38

### Recommended Mitigation
Consider implementing a mechanism within the ``UlyssesFactory`` contract that allows for the ``removal of pools,tokens``

##

## [L-18] Update ``depositNonce`` Value ``Incrementation`` in every Function Calls

### Impact
The function uses the ``depositNonce`` variable to encode the data for the ``cross-chain call``. However, the ``depositNonce`` variable always returns the current value, not the ``incremented value``. This means that if the function is called multiple times, the same ``nonce`` will be used for each call. This could ``lead`` to a ``replay attack``

### POC
```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol

226: bytes memory packedData = abi.encodePacked(
227:            bytes1(0x04), msg.sender, depositNonce, _params, msg.value.toUint128(), _remoteExecutionGas
228:        );

248:    depositNonce,

bytes memory packedData = abi.encodePacked(
            bytes1(0x06),
            msg.sender,
            uint8(_dParams.hTokens.length),
            depositNonce,
            _dParams.hTokens,
            _dParams.tokens,
            _dParams.amounts,
            _deposits,
            _dParams.toChain,
            _params,
            msg.value.toUint128(),
            _remoteExecutionGas
        );


```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L226-L228

### Recommended Mitigation
Ensures that a different nonce is used for each call

```solidity

depositNonce++

```

##

## [L-19] Unused/empty fallback() function

### Impact
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert. Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds.

### POC
```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/BranchBridgeAgent.sol

1419: fallback() external payable {}

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L1334

##

## [L-20] ``withdraw()`` should use ``lock`` modifier to avoid reentrancy attack

### Impact
The function ``withdraw()`` should use the ``lock`` modifier to avoid ``reentrancy attacks``. The ``lock`` modifier will ``prevent`` the contract from being called again while it is still executing. This will help to protect the contract from being ``exploited`` by an ``attacker``.

### POC
```solidity
FILE: Breadcrumbs2023-05-maia/src/ulysses-omnichain/BranchPort.sol

function withdraw(address _recipient, address _underlyingAddress, uint256 _deposit)
        external
        virtual
        requiresBridgeAgent


 modifier lock() {
        require(_unlocked == 1);
        _unlocked = 2;
        _;
        _unlocked = 1;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L206-L209

### Recommended Mitigation
Use ``lock`` modifier for withdraw() function

##

## [L-21] Non-zero approval may be ``problems`` for some tokens ``like USDT``

``// Addresses of the output hTokens`` this tokens can be anything 

### Impact
There are some tokens like USDT did not allow to approve allowance from non-zero value to non-zero value.If you try to approve another non-zero value when it's already had allowance, TX will fail.

### POC
```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/BranchPort.sol

120: ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

148: ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/MulticallRootRouter.sol#L148

### Recommended Mitigation
Add an ``approve(0)`` before approving

##

## [L-22] Contract existence of ``target[i]`` not checked before low level ``.call`` function

### Impact
The existence of ``target[i]`` is not checked before making the ``.call`` function. This could potentially lead to issues if ``target[i]`` is not a valid contract address. If11 target[i]11 does not point to a valid contract, the .call function may revert, resulting in a failed transaction.

### POC

```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol

49:(bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/VirtualAccount.sol#L49

### Recommended Mitigation

Check contract existence before .call 

```solidity
address targetAddress = calls[i].target;
require(Address.isContract(targetAddress), "Invalid contract address");

```

## [L-23] Gas ``griefing/theft`` is possible on ``unsafe external call``

### Impact

https://twitter.com/pashovkrum/status/1607024043718316032?t=xs30iD6ORWtE2bTTYsCFIQ&s=19

If the external contract at ``calls[i].target`` is a malicious contract and specifically designed to consume excessive gas, it can manipulate the execution flow to consume more gas than necessary, resulting in a higher gas cost for your transaction. This gas cost cannot be fully recovered because the unused gas is not returned due to the malicious contract's behavior

### POC

```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/VirtualAccount.sol

49: (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/VirtualAccount.sol#L49

### Recommended Mitigation
Usage like below, ‘out’ and ‘outsize’ values are given (0,0) . Thus, this storage disappears and may come from external contracts a possible Gas griefing/theft problem is avoided.

##

## [L-24] Use ``.call`` instead of ``.transfer`` to send ether

### Impact
.transfer will relay 2300 gas and .call will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using .transfer will fail, causing integration issues.

### POC

```solidity
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

409: if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410: if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```
### Recommended Mitigation
Use .call instead of .transfer

##

## [L-25] ``Return values`` of ``transfer()/transferFrom()`` not checked

## Impact
If the return value of these functions is not checked, potential errors or failures during the token transfer can go unnoticed. This can lead to unexpected behavior and vulnerabilities in the contract

### POC

```solidity
FILE: 2023-05-maia/src/talos/base/TalosBaseStrategy.sol

409: if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410: if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```

### Recommended Mitigation
Check the return value of transfer() and transferFrom() and handle any failed transfers appropriately








### 





# NON CRITICAL FINDINGS

##

## [NC-1] Add an event for critical parameter changes

Adding events for critical parameter changes will facilitate offchain monitoring and indexing.

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L86-L89

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L92-L95

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L98-L101

##

## [NC-2] Function does not followed the standard solidity naming 

The ``external`` function names not start with ``_``

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegateMaia.sol#L397

##

## [NC-3] No same value input control 

The setter functions does not have any checks to prevent the same value from being passed. This means that an ``onlyOwner`` could call the function with the same address, which would effectively do nothing

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L86-L89

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L92-L95

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/minters/BaseV2Minter.sol#L98-L101

##

## [NC-4] Status of ``approve()`` functions not checked
 
When using the approve() function to grant an allowance for a specific address to spend tokens on behalf of the sender, it is crucial to verify the return value of the function

```solidity
FILE: 2023-05-maia/src/ulysses-omnichain/BranchPort.sol

120: ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

148: ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/MulticallRootRouter.sol#L148

##

## [NC-5] Rectify all warnings to avoid unexpected behaviour

Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
 --> src/governance/GovernorBravoDelegator.sol:7:1:
  |
7 | contract GovernorBravoDelegator is GovernorBravoDelegatorStorage, GovernorBravoEvents {
  | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
  --> src/governance/GovernorBravoDelegator.sol:71:5:
   |
71 |     fallback() external payable {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
 --> src/out-of-scope/governance/GovernorBravoDelegator.sol:6:1:
  |
6 | contract GovernorBravoDelegator is GovernorBravoDelegatorStorage, GovernorBravoEvents {
  | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
  --> src/out-of-scope/governance/GovernorBravoDelegator.sol:70:5:
   |
70 |     fallback() external payable {
   |     ^ (Relevant source part starts here and spans across multiple lines).


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> src/ulysses-omnichain/BranchBridgeAgent.sol:58:1:
   |
58 | contract BranchBridgeAgent is IBranchBridgeAgent {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1419:5:
     |
1419 |     fallback() external payable {}
     |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol:67:1:
   |
67 | contract ArbitrumBranchBridgeAgent is BranchBridgeAgent {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1419:5:
     |
1419 |     fallback() external payable {}
     |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> src/ulysses-omnichain/CoreBranchRouter.sol:19:1:
   |
19 | contract CoreBranchRouter is BaseBranchRouter {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
   --> src/ulysses-omnichain/CoreBranchRouter.sol:284:5:
    |
284 |     fallback() external payable {}
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol:37:1:
   |
37 | contract ArbitrumCoreBranchRouter is CoreBranchRouter {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
   --> src/ulysses-omnichain/CoreBranchRouter.sol:284:5:
    |
284 |     fallback() external payable {}
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> src/ulysses-omnichain/RootBridgeAgent.sol:90:1:
   |
90 | contract RootBridgeAgent is IRootBridgeAgent {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
    --> src/ulysses-omnichain/RootBridgeAgent.sol:1334:5:
     |
1334 |     fallback() external payable {}
     |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> test/ulysses-omnichain/mocks/MockRootBridgeAgent.t.sol:23:1:
   |
23 | contract MockRootBridgeAgent is RootBridgeAgent {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
    --> src/ulysses-omnichain/RootBridgeAgent.sol:1334:5:
     |
1334 |     fallback() external payable {}
     |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> test/ulysses-omnichain/mocks/MockBranchBridgeAgent.t.sol:16:1:
   |
16 | contract MockBranchBridgeAgent is BranchBridgeAgent {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1419:5:
     |
1419 |     fallback() external payable {}
     |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> test/ulysses-omnichain/ArbitrumBranchTest.t.sol:42:1:
   |
42 | contract ArbitrumBranchTest is DSTestPlus {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
  --> test/ulysses-omnichain/ArbitrumBranchTest.t.sol:43:5:
   |
43 |     fallback() external payable {}
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> test/ulysses-omnichain/BranchBridgeAgentTest.t.sol:21:1:
   |
21 | contract BranchBridgeAgentTest is Test {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
  --> test/ulysses-omnichain/BranchBridgeAgentTest.t.sol:87:5:
   |
87 |     fallback() external payable {}
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (3628): Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.
  --> test/ulysses-omnichain/RootTest.t.sol:53:1:
   |
53 | contract RootTest is DSTestPlus {
   | ^ (Relevant source part starts here and spans across multiple lines).
Note: The payable fallback function is defined here.
   --> test/ulysses-omnichain/RootTest.t.sol:547:5:
    |
547 |     fallback() external payable {}
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (2462): Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
 --> src/governance/GovernorBravoDelegator.sol:8:5:
  |
8 |     constructor(
  |     ^ (Relevant source part starts here and spans across multiple lines).


Warning (2462): Warning: Visibility for constructor is ignored. If you want the contract to be non-deployable, making it "abstract" is sufficient.
 --> src/out-of-scope/governance/GovernorBravoDelegator.sol:7:5:
  |
7 |     constructor(
  |     ^ (Relevant source part starts here and spans across multiple lines).


Warning (8417): Warning: Since the VM version paris, "difficulty" was replaced by "prevrandao", which now returns a random number based on the beacon chain.
  --> test/ulysses-omnichain/mocks/Multicall2.sol:49:22:
   |
49 |         difficulty = block.difficulty;
   |                      ^^^^^^^^^^^^^^^^


Warning (5740): Warning: Unreachable code.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1037:13:
     |
1037 |             return;
     |             ^^^^^^^


Warning (5740): Warning: Unreachable code.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1052:13:
     |
1052 |             return;
     |             ^^^^^^^


Warning (5740): Warning: Unreachable code.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1071:13:
     |
1071 |             return;
     |             ^^^^^^^


Warning (5740): Warning: Unreachable code.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1151:17:
     |
1151 |                 return (true, "already executed tx");
     |                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (5740): Warning: Unreachable code.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1173:17:
     |
1173 |                 return (true, "already executed tx");
     |                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Warning (5740): Warning: Unreachable code.
    --> src/ulysses-omnichain/BranchBridgeAgent.sol:1197:17:
     |
1197 |                 return (true, "already executed tx");
     |                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^















