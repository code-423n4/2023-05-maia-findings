# QA Report
## Summary
Total 06 Low and 06 Non-Critical
### Low Risk Issues
- [L-01] Nonce used for multiple purposes
- [L-02] `multicall()`s involving `permit()` and `delegateBySig()` can be DOSed
- [L-03] Misleading comments
- [L-04] The comparison condition is not correct
- [L-05] A proposal is not created if the proposal has the exact same Threshold votes
- [L-06] `GovernorBravoDelegateMaia.sol` will not work properly for `Arbitrum` or `Optimism` due to `block.number`.
## [L-01] Nonce used for multiple purposes
The nonce mapping used for `permit()` calls is the same as the one used for `delegateBySig()`. This should at the very least be documented so signers know that the order of operations between the two functions matters, and so that `multicall()`s can be organized appropriately
```
File: 2023-05-maia/src/erc-20/ERC20MultiVotes.sol
375:        require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");
```
[ERC20MultiVotes.sol#L375](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L375)

## [L-02] `multicall()`s involving `permit()` and `delegateBySig()` can be DOSed
Attackers monitoring the blockchain for multicalls can front-run by calling `permit()` and `delegateBySig()` before the `multicall()`, causing it to revert. Have separate flavors of the functions where the `multicall()` data is included in the hash.
```
File: 2023-05-maia/src/erc-20/ERC20MultiVotes.sol
375:        require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");
```
[ERC20MultiVotes.sol#L375](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L375)

## [L-03] Misleading comments
```
File: 2023-05-maia/src/rewards/interfaces/IFlywheelCore.sol
63:     *   @return the cumulative amount of rewards accrued to user (including prior)
```
The cumulative amount of rewards accrued to the user since the last claim
[IFlywheelCore.sol#L63](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/interfaces/IFlywheelCore.sol#L63)
```
File: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol
54:    /// @notice The maximum lambda1 that can be set (10%)
55:    uint256 private constant MAX_LAMBDA1 = 1e17;
------------
--- 309:        // Lower fee must be lower than 1%
+++ 309:        // Lower fee must be lower than 10%
310:        if (_fees.lambda1 > MAX_LAMBDA1) revert InvalidFee();
```
[MAX_LAMBDA1](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-amm/UlyssesPool.sol#L54-L55) is a constant set to 1e17 (10%).
```
File: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol
--- 992:                    // if _positiveFee > 0 then add _positiveFee to output
+++ 992:                    // if _positiveFee > 0 then add _positiveFee to negativeFee
993:                    // Subtracting assets to deposit
994:                    case true { negativeFee := add(negativeFee, _positiveFee) }
```
[Line 94](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-amm/UlyssesPool.sol#L994) add `_positiveFee` to  `negativeFee` not `output` as described on [Line 992](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-amm/UlyssesPool.sol#L992)

## [L-04] The comparison condition is not correct
```
File: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol
183:        totalWeights = newTotalWeights;
184:
--- 185:        if (newTotalWeights > MAX_TOTAL_WEIGHT) revert InvalidWeight();
+++ 185:        if (totalWeights > MAX_TOTAL_WEIGHT) revert InvalidWeight();
```
Use `totalWeights` instead of `newTotalWeights ` [here](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-amm/UlyssesPool.sol#L185)

## [L-05] A proposal is not created if the proposal has the exact same Threshold votes
### Description:
```
File: 2023-05-maia/src/governance/GovernorBravoDelegateMaia.sol
        // Allow addresses above proposal threshold and whitelisted addresses to propose
        require(
            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
                || isWhitelisted(msg.sender),
            "GovernorBravo::propose: proposer votes below proposal threshold"
        );
```
[LINK TO CODE](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/governance/GovernorBravoDelegateMaia.sol#L113-L118)
- In current development it is not allowed to create a proposal if the proposal has the exact same Threshold votes
- Consider allowing a proposal to be generated when the caller's voting weight is greater than or equal to the threshold
### Recommendation:
```
--- 115:            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
+++ 115:            govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) >= getProposalThresholdAmount()
```
Change from `>` to `>=`

## [L-06] `GovernorBravoDelegateMaia.sol` will not work properly for `Arbitrum` or `Optimism` due to `block.number`.
### Description:
-[GovernorBravoDelegateMaia.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol) : Implementation of Maia Ecosystem Governance and on-chain execution. `block.number` is used in [propose()](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/governance/GovernorBravoDelegateMaia.sol#L115), [cancel](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/governance/GovernorBravoDelegateMaia.sol#L238-L245) and [state](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/governance/GovernorBravoDelegateMaia.sol#L304-L306).
- According to Arbitrum [Docs](https://developer.arbitrum.io/time) `block.number` returns the most recently synced L1 block number. Once per minute the block number in the Sequencer is synced to the actual L1 block number. This period could be abused to completely bypass this protection. The user would open their position 1 Arbitrum block before the sync happens, the close it the very next block. It would appear that there has been 5 block (60 / 12) since the last transaction but in reality it has only been 1 Arbitrum block. Given that Arbitrum has 2 seconds blocks I would be impossible to block this behavior through parameter changes.
- It also presents an issue for [Optimism](https://community.optimism.io/docs/developers/build/differences/#block-numbers-and-timestamps) because each transaction is it's own block. No matter what value is used for the block delay, the user can pad enough tiny transactions to allow them to close the trade immediately.
### Recommendation:
Should be measured using `block.timestamp` rather than `block.number`.
### Non-Critical Issues
- [N-01] Consider addings checks for signature malleability
- [N-02] USE NAMED PARAMETERS FOR MAPPING TYPE DECLARATIONS
- [N-03] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
- [N-04] SOLIDITY VERSION 0.8.19 CAN BE USED
- [N-5] ACCORDING TO THE SYNTAX RULES, USE => `mapping (` INSTEAD OF => `mapping(` USING SPACES AS KEYWORD
- [N-06] Use SMTChecker
## [N-01] Consider addings checks for signature malleability
```
File: 2023-05-maia/src/erc-20/ERC20MultiVotes.sol
        address signer = ecrecover(
            keccak256(
                abi.encodePacked(
                    "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
                )
            ),
            v,
            r,
            s
        );
        require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");
        require(signer != address(0));
        _delegate(signer, delegatee);
    }
```
[ERC20MultiVotes.sol#L365-L378](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20MultiVotes.sol#L365-L378)

## [N-02] USE NAMED PARAMETERS FOR MAPPING TYPE DECLARATIONS
### All contracts
Consider using named parameters in mappings (e.g. mapping(address account => uint256 balance)) to improve readability. This feature is present since Solidity 0.8.18
Eg:
```
File: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol
36:    /// @notice destinationIds[address] => destinationId
--- 37:    mapping(address => uint256) public destinationIds;
+++ 37:    mapping(address destinationIds[address]  => uint256 destinationId) public destinationIds;
```
[UlyssesPool.sol#L36-L37](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-amm/UlyssesPool.sol#L36-L37)

## [N-03] USE A SINGLE FILE FOR ALL SYSTEM-WIDE CONSTANTS
### Description:
There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values).

This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

#### constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

## [N-04] SOLIDITY VERSION 0.8.19 CAN BE USED
### CONTEXTS: ALL CONTRACTS
Using the more updated version of Solidity can enhance security. As described in https://github.com/ethereum/solidity/releases, Version `0.8.19` is the latest version of Solidity, which "contains a fix for a long-standing bug that can result in code that is only used in creation code to also be included in runtime bytecode". To be more secured and more future-proofed, please consider using Version `0.8.19` for the following contracts.

## [N-5] ACCORDING TO THE SYNTAX RULES, USE => `mapping (` INSTEAD OF => `mapping(` USING SPACES AS KEYWORD
### CONTEXTS: ALL CONTRACTS
Eg:
```
File: 2023-05-maia/src/ulysses-amm/UlyssesPool.sol
--- 34:    mapping(uint256 => uint256) public destinations;
+++ 34:    mapping (uint256 => uint256) public destinations;
```
[UlyssesPool.sol#L34](https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-amm/UlyssesPool.sol#L34)

## [N-06] Use SMTChecker
The highest tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19