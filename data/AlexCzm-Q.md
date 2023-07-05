# Code4rena MaiaDao Protocol May 2023 - QA Report


## Low Severity Findings

### [L-01] `UniswapV3Staker.restakeToken()` : Only token owner can restake

  

Beside unstaking `_unstakeToken()` function allow anyone to restake other users tokens as long as block time is after the end time of incentive.

The re-stake part is verified by following line:

```solidity

// anyone can call restakeToken if the block time is after the end time of the incentive

if ((isNotRestake || block.timestamp < endTime) && owner != msg.sender) revert NotCalledByOwner();

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L373-L374

  

But `restakeToken()` calls the internal `_restakeToken()` with `isNotRestake == true` thus disabling the intended behavior

  

```solidity
function restakeToken(uint256 tokenId) external {

IncentiveKey storage incentiveId = stakedIncentiveKey[tokenId];

if (incentiveId.startTime != 0) _unstakeToken(incentiveId, tokenId, true); // @audit isNotRestake should be `false` not `true`
...

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/UniswapV3Staker.sol#L340-L342

  
#### Mitigation


Inside `restakeToken()` call `_restakeToken()` with `isNotRestake == false` instead of `true`:

  
```solidity

function restakeToken(uint256 tokenId) external {

IncentiveKey storage incentiveId = stakedIncentiveKey[tokenId];

if (incentiveId.startTime != 0) _unstakeToken(incentiveId, tokenId, false);

```


### [L-02] Missing `require`

 Return value is not checked as it's done in other [places](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L191) .

```solidity

_userGauges[msg.sender].remove(gauge);

```
  
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L178

    

#### Mitigation

Add missing require:

`require(_userGauges[msg.sender].remove(gauge));`
 

## Non-Critical Findings
  

### [NC-01] Wrong natspec  comments

 
The natspec comments of the following functions are incorrect.

  
<details>

<summary><i>There are 11 instances of this issue:</i></summary>

  
```solidity
/**
* @notice returns the set of gauges the user has allocated to, they may be live or deprecated.
*/
function freeGaugeBoost(address user) external view returns (uint256);
```

  
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L104-L107

  
```solidity
/// @notice mapping of tokenId to user
function tokenIdRewards(uint256) external view returns (uint256);
```
  

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/interfaces/IBoostAggregator.sol#L44-L45

  
```solidity
/// @notice Transfers `amountRequested` of accrued `rewardToken` rewards from the contract to the recipient `to`
/// @param to The address where claimed rewards will be sent to
/// @return reward The amount of reward tokens claimed
function claimAllRewards(address to) external returns (uint256 reward);
```

  
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L207-L210C14

  
```solidity
//@audit these 2 functions returns `_gauges`, live and deprecated
/**
* @notice returns the set of live gauges
*/
// @audit returns all gauges, deprecated ones too
function gauges() external view returns (address[] memory);

/**
* @notice returns a paginated subset of live gauges
* @param offset the index of the first gauge element to read
* @param num the number of gauges to return
*/
function gauges(uint256 offset, uint256 num) external view returns (address[] memory values);
```
  

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L72-L82

  
```solidity
//@audit returns all gauges number (including deprecated ones)
/**
* @notice returns the number of live gauges
*/
function numGauges() external view returns (uint256);
```

  
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L89-L92C14

  
```solidity
/**
* @notice returns the number of live gauges
*/
function numDeprecatedGauges() external view returns (uint256);
```


https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Boost.sol#L99-L102

```solidity
//@audit returns the set of all gauges 
/**
* @notice returns the set of live gauges
*/
function gauges() external view returns (address[] memory);

/**
* @notice returns a paginated subset of live gauges
* @param offset the index of the first gauge element to read
* @param num the number of gauges to return
*/
function gauges(uint256 offset, uint256 num) external view returns (address[] memory values);

```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Gauges.sol#L97-L107C16

  

```solidity
//@audit returns the number of all gauges
/**
* @notice returns the number of live gauges
*/
function numGauges() external view returns (uint256);
```  

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Gauges.sol#L114-L117C17

  
  

```solidity

/**
* @notice returns the number of live gauges
*/
function numDeprecatedGauges() external view returns (uint256);
```

  

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/interfaces/IERC20Gauges.sol#L124-L127

</details>

### [NC-02] Visibility for constructor is ignored

`public` can be removed.

```solidity
constructor(
address timelock_,
address govToken_,
address admin_,
address implementation_,
uint256 votingPeriod_,
uint256 votingDelay_,
uint256 proposalThreshold_
) public {...}
```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoDelegator.sol#L8-L16C15

### [NC-03] The documentation does not align with the implementation.

vMaia [docs](https://v2-docs.maiadao.io/protocols/overview/tokenomics/vMaia#docusaurus_skipToContent_fallback) says that users `can only withdraw their staked tokens on the first Monday of each month` but code implements Tuesday as a withdrawal day.


```solidity
(bool isTuesday, uint256 _unstakePeriodStart) = DateTimeLib.isTuesday(block.timestamp);
```

  

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/vMaia.sol#L109

  
### [NC-04] Unused import

The following import is not used:

```solidity
import {TalosManager} from "../TalosManager.sol";
```

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/factories/TalosStrategyVanillaFactory.sol#L10