## [L-01] Division Before Multiplication (Instances Missed By The Bot)
Solidity's integer division truncates. Thus, performing division before multiplication can lead to precision loss.

Vulnerable Code 
``` solidity
uint256 _newEpoch = (block.timestamp / WEEK) * WEEK;
```
If ```WEEK``` Value Is Somehow Greater Than ```block.timestamp```, Then The Value of ```uint256 _newEpoch``` Will Be Zero.
##### Vulnerable Code Link
[BaseV2Guage.sol#L84](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/BaseV2Gauge.sol#L84)
[BaseV2Guage.sol#L68](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/BaseV2Gauge.sol#L68)
[FlywheelAcummulatedRewards.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L50)
[FlywheelGaugeRewards.sol#60](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L60)
[FlywheelGaugeRewards.sol#79](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L79)
[FlywheelGaugeRewards.sol#114](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L114)

##### Tool Used 
Manual / Slither
##### Recommended Mitigation Step
Consider ordering multiplication before division.
##### Reference
[Division Before Multiplication](https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply)

## [L-02] ```ERC20Boost.decrementGaugeBoost``` ignores return value by ```_userGauges[msg.sender].remove(gauge)``` 
The return value of an external call is not stored in a local or state variable. It fails to handle the return value when attempting to remove the gauge associated with the user, which could potentially allow an attacker to bypass gauge removal and manipulate the contract state.
Vulnerable Code
``` solidty
_userGauges[msg.sender].remove(gauge);
```
##### Vulnerable Code Link 
[ERCBoost.sol#L178](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L178)
[BaseV2Guage.sol#L115](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/BaseV2Gauge.sol#L115)
[FlyWheelGuageRewards.sol#L76](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L76)
##### Tools Used 
Slither
##### Recommended Mitigation Step
Ensure that all the return values of the function calls are used.
##### Reference 
[Unused Return](https://github.com/crytic/slither/wiki/Detector-Documentation#unused-return)
## [L-03] ```IUniswapV3Staker.tokenIdRewards``` Shadows ```IUniswapV3Staker.rewards```
##### Vulnerable Code Link
[IUniswapV3Staker.sol#L156](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L156)
[IUniswapV3Staker.sol#L162](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L162)
##### Tools Used
Slither
##### Recommended Mitigation Step
Rename the local variables that shadow another component.
##### Reference
[Local Variable Shadowing](https://github.com/crytic/slither/wiki/Detector-Documentation#local-variable-shadowing)
## [L-04] ```UtilityManager.checkWeight``` does not always execute _; or revertModifier
If a modifier does not execute _ or revert, the execution of the function will return the default value, which can be misleading for the caller.
Vulnerable Code 
``` solidity
    modifier checkWeight(uint256 amount) virtual;
```
##### Vulnerable Code Link
[UtitlityManager.sol#L141](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/UtilityManager.sol#L141)
[UtitlityManager.sol#L145](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/UtilityManager.sol#L145)
[UtitlityManager.sol#L147](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/hermes/UtilityManager.sol#L147)
[RewardsDepot.sol](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/depots/RewardsDepot.sol#L24)
##### Tools Used 
Slither
##### Recommended Mitigation Step 
All the paths in a modifier must execute _ or revert.
##### Reference 
[Incorrect Modifier](https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-modifier)
## [L-05] Dangerous Usage Of ```block.timestamp```
```block.timestamp``` can be manipulated by miners. Suppose Bob's contract relies on ```block.timestamp``` for its randomness. Eve is a miner and manipulates ```block.timestamp``` to exploit Bob's contract.
Vulnerable Code 
``` solidity
            if (cycle - block.timestamp <= incrementFreezeWindow) revert IncrementFreezeError();
```
##### Vulnerable Code Link
[ERC20Guages.sol#L205](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L205)
[FlywheelAcummulatedRewards.sol#L46](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L46)
##### Tools Used 
Solidity Visual Developer / Slither 
##### Recommended Mitigation Steps
Avoid relying on ```block.timestamp```
##### Reference 
[block.timestamp](https://github.com/crytic/slither/wiki/Detector-Documentation#block-timestamp)
## [L-06] ```BaseV2GaugeManager.changeAdmin``` lacks a zero-check
The lack of a zero address check in the changeAdmin function allows an attacker to set the admin address to the zero address, resulting in unauthorized access and control over the contract.
Vulnerable Code 
``` solidity
function changeAdmin(address newAdmin) external onlyAdmin {
        admin = newAdmin;

        emit ChangedAdmin(newAdmin);
    }
```
##### Vulnerable Code Link
[BaseV2GaugeManager.sol#L146](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/gauges/factories/BaseV2GaugeManager.sol#L146)
##### Tools Used 
Remix/Slither
##### Reference 
[Missing Zero Address Check](https://github.com/crytic/slither/wiki/Detector-Documentation#missing-zero-address-validation)
## [L-07] The ```initialize``` Function Is Missing An Event Emission After Assigning The Value To ```bridgeAgentExecutorAddress```.
Missing Emmiting an event makes it difficult to track changes in the contract's state or important updates related to the ```bridgeAgentExecutorAddress``` off-chain
Vulnerable Code 
``` solidity
function initialize(address _localBridgeAgentAddress) external onlyOwner {
        require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");
        localBridgeAgentAddress = _localBridgeAgentAddress;
        bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress();
        renounceOwnership();
    }
```
##### Vulnerable Code Link 
[BaseBranchRouter.sol#L37](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BaseBranchRouter.sol#L37-L42)
##### Tools Used 
VS Code/ Slither 
##### Recommended Mitigation Steps
Emit an event for critical parameter changes.
##### Reference
[Missing Event Access](https://github.com/crytic/slither/wiki/Detector-Documentation#missing-events-access-control)
## [L-07] ```RootBridgeAgent._gasSwapIn.amount0``` Is A Local Variable Never Initialized
Vulnerable Code 
``` solidity
returns (int256 amount0, int256 amount1
```
##### Vulnerable Code Link
[RootBridgeAgent.sol#L690](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L690)
[RootBridgeAgent.sol#L980](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L980)
[RootBridgeAgent.sol#L1036](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L1036)
[RootBridgeAgent.sol#L871](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L871)
[RootBridgeAgent.sol#L953](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/RootBridgeAgent.sol#L953)
##### Tools Used 
Solidity Visual Developer/Slither
##### Recommended Mitigation Steps 
Initialize all the variables. If a variable is meant to be initialized to zero, explicitly set it to zero to improve code readability.
##### Reference 
[Uninitialized Local Variable](https://github.com/crytic/slither/wiki/Detector-Documentation#uninitialized-local-variables)
## [N-01] ```ERC20Boost._burn``` is never used and should be removed
Vulnerable Code 
``` solidity
function _burn(address from, uint256 amount) internal override notAttached(from, amount) {
        super._burn(from, amount);
    }
```
Vulnerable Code Link
[ERC20Boost.sol#L302](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L302)
[ERC20Gauges.sol#L379](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L379)
[ERC20Gauges.sol#L485](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L485)
[ERC20Gauges.sol#L383](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Gauges.sol#L383)
[ERC20MultiVotes.sol#L258](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L258)
[ERC20MultiVotes.sol#L260](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L260)
##### Tools Used
Slither
##### Recommended Mitigation Steps
Remove unused functions.
##### Reference 
[Dead Code](https://github.com/crytic/slither/wiki/Detector-Documentation#dead-code)
## [N-02] ```ERC4626.sol``` Is Missing Inheritance From ```IERC4626DepositOnly```
##### Recommended Mitigation Steps 
ERC4626 should inherit from IERC4626DepositOnly
## [N-03] Avoid Using Low Level Calls 
Function ```call``` in ```VirtualAccount.sol``` Is Using Low Level Call. The use of low-level calls is error-prone. Low-level calls do not check for code existence or call success.
Vulnerable Code
``` solidity
 function call(Call[] calldata calls)
        external
        requiresApprovedCaller
        returns (uint256 blockNumber, bytes[] memory returnData)
    {
        ..........
        for (uint256 i = 0; i < calls.length; i++) {
            .........
            (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);
            .........
            }
    }
```
##### Vulnerable Code Link
[VirtualAccount.sol#L49](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/VirtualAccount.sol#L49)
##### Tools Used 
Slither
##### Recommended Mitigation Steps 
Avoid low-level calls. Check the call success. If the call is meant for a contract, check for code existence.
##### Reference 
[Low Level Calls](https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-calls)