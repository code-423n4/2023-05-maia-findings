# MaiaDAO Analysis by RED-LOTUS team

## Table Of Contents

1. [GovernanceMaia](#GovernanceMaia)
2. [Hermes](#hermes)
3. [Talos](#talos)
4. [Ulysses Accounting Issues](#ulysses-accounting-issues)
5. [Ulysses Omnichain](#ulysses-omnichain)

## Introduction

This analysis report delves into various sections and modules of the MaiaDAO protocol that the RED-LOTUS team audited, specific sections covered are listed within the table of contents above. 

Our approach included a thorough examination and testing of the codebase, research on wider security implications applicable to the protocol and expanded discussion of potential out of scope/known issues from the audit contest. 

A number of potential issues were identified related to centralization and inherent systemic risks associated with specific functionality of the protocol. We supplied feedback on specific sections of architecture and give other recommendations as relevant. 

Throughout our analysis, we aimed to provide a comprehensive understanding of the codebase, suggesting areas for possible improvement. To validate our insights, we supplemented our explanations with illustrative figures, demonstrating robust comprehension of Maia's internal code functionality and interaction with 3rd party services. 

Over the course of the 36-day contest, we dedicated approximately 540+ hours to auditing the codebase. Our ultimate goal is to provide a report that will give the project team wider perspective and value from our research conducted to strengthen the security posture, usability and efficiency of the protocol.

This analysis report and included diagrams are free to be shared with other parties as the project team see fit.

# GovernanceMaia

1. **Analysis of the codebase (What's unique? What's using existing patterns?)**:
    - *Unique*: This contract carries out specific governance mechanisms that are uniquely designed for its specific use case.
    - *Existing Patterns*: The contract adheres to common contract management patterns, such as the use of `admin`, `pendingAdmin`, and functions for administrative transitions (`_setPendingAdmin`, `_acceptAdmin`).
2. **Architecture feedback**:
    - *Use of Redundant Functions*: In Solidity 0.8.x, overflow and underflow protection is built-in. Thus, functions like `add256` and `sub256` are redundant and lead to unnecessary gas consumption.
    - *Inline Assembly Usage*: The `getChainIdInternal` function uses inline assembly to fetch the chain ID. While this is a standard operation, caution is required as it bypasses Solidity’s safety checks.
3. **Centralization risks**:
    - The significant power given to the `admin` role introduces a certain level of centralization risk. If the `admin` key is compromised, it could pose a serious threat to the system.
4. **Systemic risks**:
    - *External Contract Dependencies*: The contract relies on the `GovernorAlpha` and `timelock` contracts. If any of these contracts have vulnerabilities, it would affect this contract.
    - *Governance Mechanism Security*: The contract’s governance mechanism is critical for its operation. A poorly implemented governance mechanism could lead to system-wide issues.
5. **Other recommendations**:
    - *Gas Optimization*: Consider removing the `add256` and `sub256` functions for gas optimization since overflow and underflow protections are already present in Solidity 0.8.x.
    - *Consensus Mechanism*: Consider implementing a mechanism for consensus among a group of admins to reduce centralization risk.

# **Important Functions**

**function propose(address[] memory targets,uint256[] memory values,string[] memory signatures,bytes[] memory calldatas,string memory description) public returns (uint256)**

## **Summary**

The `propose` function allows token holders to propose new proposals for changes to the platform's protocol. However, there are certain requirements that need to be met before a proposal can be submitted. The function checks if the Governor Bravo is active, if the proposer has enough votes above the proposal threshold, and if the proposal contains the necessary information. Once these conditions are met, the function creates a new proposal and assigns it a unique proposal ID.

## **Relations**

- The `propose` function is called by token holders who want to propose a new proposal .
- The function checks if the Governor Bravo is active before allowing the proposal .
- It verifies if the proposer has enough votes above the proposal threshold .
- The function ensures that the proposal contains the required information, such as target addresses, values, signatures, and calldatas .
- It also checks if the number of actions in the proposal is within the allowed limit .
- The function assigns a unique proposal ID to the new proposal .

## **Other Functions Called**

The `propose` function indirectly interacts with the following functions within the vMaia Governor Bravo contract:

1. `totalSupply()`: This function is called in the `getQuorumVotesAmount()` function to calculate the total supply of the governance token .
2. `govToken.totalSupply()`: This function is called in the `getQuorumVotesAmount()` function to get the total supply of the governance token .
3. `govToken.getPriorVotes()`: This function is called in the `require` statement to check if the proposer has enough votes above the proposal threshold .
4. `isWhitelisted()`: This function is called in the `require` statement to check if the proposer is whitelisted .
5. `add256()`: This function is called to calculate the `startBlock` and `endBlock` values for the new proposal .
6. `emit ProposalCreated()`: This event is emitted after a new proposal is successfully created .

These functions are not directly called within the `propose` function, but they are referenced or used in the requirements and checks performed by the `propose` function.

**function castVoteBySig(uint256 proposalId, uint8 support, uint8 v, bytes32 r, bytes32 s) external**

## **Summary**

The `castVoteBySig` function is an external function within the vMaia Governor Bravo contract. It allows for casting a vote for a proposal using an EIP-712 signature. This function accepts the proposal ID, the support (yes or no), and the signature parameters (v, r, s). It verifies the signature and then casts the vote for the specified proposal.

## **Relations**

- The `castVoteBySig` function is an external function that can be called by anyone who wants to cast a vote for a proposal using a signature .
- The function accepts the proposal ID, support (yes or no), and the signature parameters (v, r, s) as inputs .
- It verifies the signature by calculating the domain separator and comparing it with the provided signature .
- Once the signature is verified, the function casts the vote for the specified proposal .

## **People Who Can Cast a Vote**

The `castVoteBySig` function does not have any explicit checks on the signatory variable. Therefore, anyone who has access to a valid signature can potentially cast a vote using this function. The function relies on the validity of the signature to ensure that the vote is authorized.

# **State Machine**

## **States:**

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688541371/Maia/GovernanceMaia_qwnneh.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688541371/Maia/GovernanceMaia_qwnneh.png)

Idle
ProposalCreated
Voting
PendingExecution
Executed
AdminChangingVotingParameters
AdminChangingWhitelist
AdminTransfer

## **Transitions:**

**Idle -> ProposalCreated**

- Function:`_propose`
*- Attack vectors:
  - Check if the proposal submission checks for the proposal threshold.
  - Validate that user balances or permissions are verified correctly.*

 **ProposalCreated -> Voting**
*- Transition happens automatically after the voting delay has passed.
- Attack vectors:
  - Check if the delay is being enforced properly.
  - Validate timestamp manipulation or block manipulation.*

**Voting -> PendingExecution**
*- Function:* `castVote` *(continuously through the voting period)
- Attack vectors:
  - Ensure the user balance is checked accurately at the time of voting.
  - Validate the proper calculation of the votes.
  - Possible double voting attacks.*

**PendingExecution -> Executed**
*- Function: `_*execute`
- *Attack vectors:
  - Ensure that a proposal cannot be executed before it is ready.
  - Validate that a proposal cannot be executed more than once.
  - Ensure proper access control (only the contract should call this usually).*

**Idle -> AdminChangingVotingParameters**

- Functions: *`_setVotingDelay, _*setVotingPeriod, *_setProposalThreshold`
- Attack vectors:
  - Validate proper access control; only admin should be allowed.
  - Ensure the parameters being set are within the valid range.
  - Watch for possible governance attacks (admin setting values that cripple the system).*

**Idle -> AdminChangingWhitelist**
*- Function: `_*setWhitelistAccountExpiration, _setWhitelistGuardian`
*- Attack vectors:
  - Validate proper access control; only admin or whitelist guardian should be allowed.
  - Validate that the expiration timestamps are set accurately.
  - Watch for possible governance attacks (admin whitelisting malicious accounts).*

**Idle -> AdminTransfer**
*- Function: `_*setPendingAdmin`
- *Attack vectors:
  - Validate proper access control; only admin should be allowed.
  - Check that a valid new admin address is provided.*

**AdminTransfer -> Idle**

- Function:  `_acceptAdmin`

*- Attack vectors:
  - Validate proper access control; only pending admin should be allowed.*

---

# Hermes

1. **Analysis of the codebase (What’s unique? What’s using existing patterns?)**:
    - **Unique**: The overall structure of having a parent `bHermes` contract managing three different token contracts (`bHermesBoost`, `bHermesGauges`, `bHermesVotes`), each with different functionalities (boost, gauges, votes) is unique.
    - **Existing Patterns**: The contract uses standard Solidity and Ethereum patterns. It uses the ERC20 standards and Ownable pattern for ownership management. All tokens make use of `onlybHermes` modifier to restrict access.
2. **Architecture feedback**:
    - **Design Choice**: The separation of functionalities (boost, gauges, votes) into individual contracts and a parent contract (`bHermes`) managing these seems to be a good design choice. It isolates functionalities and makes the codebase cleaner and easier to manage.
3. **Centralization risks**:
    - `bHermesBoost`, `bHermesGauges`, and `bHermesVotes` contracts have the `onlybHermes` modifier, which implies that only the `bHermes` contract can call certain functions. While this can provide security, it does centralize control to the `bHermes` contract. Also, the `bHermes` contract is owned by `_gaugeBoost` in `UtilityManager`, making the `_gaugeBoost` address the ultimate controller of the system.
4. **Systemic risks**:
    - **Dependencies**: The contracts are heavily dependent on the behavior of the ERC20 tokens they interact with. Bugs in these contracts could potentially impact the `bHermes` contract as well.
    - **Centralization**: Centralizing control in the `bHermes` contract can be a systemic risk if the contract has bugs or is compromised.

# ERC20Boost/MultiVotes/Gauges Discussion

**Front-running Issue**

- **Delegating Votes**: In the `ERC20MultiVotes` contract, users have the ability to delegate their tokens to another user.
- **Front-running Scenario**: A front-running vulnerability exists within the `undelegate` function. If a delegatee is alerted to an impending `undelegate` transaction in the mempool, they may opt to front-run this transaction.
- **Lock-in Mechanism**: The delegatee could submit their own transaction, providing a higher gas price to allocate tokens to a gauge. This action effectively locks themselves in as the delegatee before the `undelegate` transaction will revert.
- **Risk**: This tactic enables the delegatee to maintain control over the delegated votes, effectively bypassing the undelegation process.

**Deprecated Gauges**

- **Decreasing Gauge Inconsistencies**: The `decrementGaugeBoost` function does not operate identically to the `decrementGaugeBoostIndexed` function, particularly with regards to deprecated gauges.
    - It is possible to decrement a deprecated gauge using `decrementGaugeBoost`, a capability not afforded by `decrementGaugeBoostIndexed`.

# State Machine

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688562164/Maia/001_krhhxj.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688562164/Maia/001_krhhxj.png)

**States:**

- Uninitialized
- Idle
- Minting
- Claiming
- Forfeiting
- Transferring

### **Transitions:**

**Uninitialized -> Idle**

- Transition function: `constructor`
- Attack vectors:
    - Verify that the owner and other initialization parameters are set correctly.
    - Validate that the initialized addresses point to the correct contract addresses.
    - Ensure that the constructor cannot be called more than once.
    - Ensure the initialization of the `gaugeWeight`, `gaugeBoost`, and `governance` contracts does not get front-ran.

**Idle -> Minting**

- Transition function: `_mint`
- Attack vectors:
    - Check if the minting function verifies the correct balances.
    - Validate if the minting function correctly updates the state variables and emits the correct events.
    - Ensure the minting function cannot be called by unauthorized entities.

**Idle -> Claiming**

- Transition function: `claimOutstanding`, `claimMultiple`, `claimMultipleAmounts`, `claimWeight`, `claimBoost`, `claimGovernance`
- Attack vectors:
    - Check if the claiming functions check the sender’s balances correctly.
    - Validate if the claiming functions correctly update the state variables and emit the correct events.
    - Ensure the claiming functions cannot be called by unauthorized entities.
    - Unique attack vectors could involve manipulating the amount of utility tokens a user has claimed to gain an unfair advantage or deny others their rightful tokens.

**Idle -> Forfeiting**

- Transition function: `forfeitMultiple`, `forfeitMultipleAmounts`, `forfeitWeight`, `forfeitBoost`, `forfeitGovernance`
- Attack vectors:
    - Verify if the forfeiting functions check the sender’s balances correctly.
    - Validate if the forfeiting functions correctly update the state variables and emits the correct events.
    - Ensure the forfeiting functions cannot be called by unauthorized entities.
    - Unique attack vectors might include forcing a user to forfeit their utility tokens unfairly or creating a system state where tokens can be infinitely minted and forfeited for gain.

**Idle -> Transferring**

- Function: `transfer`, `transferFrom`
- Attack vectors:
    - Verify if the transfer function checks the sender’s balances correctly.
    - Validate if the transfer function correctly updates the state variables and emits the correct events.
    - Ensure the transfer function cannot be called by unauthorized entities.

---

# Talos

1. **Analysis of the codebase (What’s unique? What’s using existing patterns?)**:
    - Unique: Talos leverages the Uniswap V3’s Nonfungible Position Manager, the BoostAggregator for stake and unstake mechanism, and FlywheelCoreInstant for rewards accruals. Talos is designed to handle all of these complex interactions in a single, cohesive framework, which is unique.
2. **Architecture feedback**:
    - Parameter Management: The functions for updating the contract’s parameters are clear and straightforward. They also include checks to ensure valid parameter ranges.
    - Fee Calculation: Talos includes mechanisms for calculating and compounding fees. However, it would be interesting to see how the protocol adjusts its strategy based on external market conditions.
    - There’s a balance in Talos between managing state internally (such as `stakeFlag`, `flywheel`, and `boostAggregator`) and interacting with external contracts (`IUniswapV3Pool`, `BoostAggregator`, `FlywheelCoreInstant`), which is good for separating concerns.
    - It may be beneficial to emit events in the `performUpkeep` function for better off-chain monitoring and tracking of state changes.
    - Governance: It might be beneficial to introduce a decentralized governance mechanism for decision making in Talos, reducing centralization risks.
3. **Centralization risks**:
    - Talos’s behavior can be influenced by the `strategyManager` and `owner` addresses, which introduce a certain level of centralization risk. If these addresses are controlled by a malicious entity, they could disrupt Talos’s operations or perform actions that are not in the best interests of other stakeholders.
4. **Systemic risks**:
    - Dependency Risk: Talos heavily depends on Uniswap’s contracts, Flywheel contracts, and `BoostAggregator` contract. If there are bugs or vulnerabilities in those contracts, or if they change their behavior, it could impact the functioning of Talos.
    - The use of concentrated liquidity in Uniswap V3 means the contract’s liquidity could be unutilized if the market price moves outside the specified price range. Additionally, large market price swings could lead to impermanent loss.
    - Oracle Failure: Talos implicitly relies on the Uniswap V3 price oracles (via the Pool contract) for handling positions. Any failure or manipulation of these oracles could have serious impacts.
5. **Other recommendations**:
    - The constructor could use the ‘Ownable’ contract from OpenZeppelin for setting the `owner` instead of directly setting it via constructor arguments.
    - It might be beneficial to implement a circuit breaker or pause mechanism. This could help in situations where a bug or vulnerability is discovered, allowing contract operations to be halted while the issue is resolved.
    - It would be helpful to have events emitted for major state changes, such as staking/unstaking actions. This can aid off-chain systems in keeping track of contract activities.

# TalosBaseStrategy Discussion

**Uniswap Interactions**

- Slippage Check: In `init`, `withdrawAll`, and `deposit`, there is no slippage check with `amount0Min` and `amount1Min`. This could result in a loss of funds under certain scenarios and edge cases.
- User Error: `redeem` allows users to specify `amount0Min` and `amount1Min`. This is good; however, there is no check to ensure that these values won't result in a loss of funds. In the hands of an inexperienced user, this is no different from setting them to 0.
- Front-running Mitigations: `deadline` is utilizing `block.timestamp`. Although through proof of stake, it is more difficult to manipulate the `block.timestamp`, it is not impossible.
- Qualitative Analysis: `(amount0, amount1)` is overwritten with the fees. This does not present a vulnerability; however, in the future, it may cause issues if there are changes.

**Share Conversions**

- Uniswap Help: `nonfungiblePositionManager.increaseLiquidity` does not use all of the tokens allotted to it. Instead, it adds disproportionate values of each token in a way that keeps the pool balanced. Due to this, Talos is able to utilize the total liquidity added to the pool (`liquidityDifference`) for their share calculations without measuring the value of each token specifically. (E.g., User A calls `deposit` with `10e18` for both `amount0Desired` and `amount1Desired`. Suppose the pool only uses 3 of token0 to pair up with 9 of token 1, then `liquidityDifference = 12`, and we can carry on with the share calculation.

# State Machines

# TalosManager States:

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688541737/Maia/TalosManager_sdsj4j.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688541737/Maia/TalosManager_sdsj4j.png)

- Idle
- CheckingUpkeep
- Rebalancing
- Reranging

## **Transitions:**

**Idle -> CheckingUpkeep**

- Function: `checkUpkeep`
- Attack vectors:
    - Timestamp manipulation affecting TWAP.
    - Incorrect assessment of rebalance or rerange need.

**CheckingUpkeep -> Rebalancing**

- Function: `performUpkeep`
- Condition: `getRebalance(strategy) == true`
- Attack vectors:
    - External contract vulnerabilities (in `strategy.rebalance`).

**CheckingUpkeep -> Reranging**

- Function: `performUpkeep`
- Condition: `getRerange(strategy) == true`
- Attack vectors:
    - External contract vulnerabilities (in `strategy.rerange`).

**Rebalancing -> Idle**

- After executing `strategy.rebalance()`
- Attack vectors:
    - Front-running attacks.

**Reranging -> Idle**

- After executing `strategy.rerange()`
- Attack vectors:
    - Front-running attacks.

# TalosStakedStrategy States:

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688541955/Maia/TalosStaked_in27bq.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688541955/Maia/TalosStaked_in27bq.png)

- Initialized
- NotStaked
- Staked

## Transitions:

**Initialized -> NotStaked**

- Function: `constructor`
    - Attack vectors:
        - Check if the initial parameters such as the Uniswap pool, optimizer, BoostAggregator, strategy manager, and Flywheel are correctly set and validated.
        - Validate that the BoostAggregator and Flywheel contracts are set up correctly.

**NotStaked -> Staked**

- Function: `_stake`
    - Transition happens when a user performs actions such as redeeming tokens, making a deposit, or rearranging positions, given there is sufficient liquidity in the pool.
    - Attack vectors:
        - Ensure the contract has enough liquidity to stake.
        - Check if the tokens are correctly transferred to the BoostAggregator.
        - Validate the update of the `stakeFlag` state.

**Staked -> NotStaked**

- Function: `_unstake`
    - Transition happens when a user initiates actions that may cause state changes, given there is sufficient liquidity in the pool.
    - Attack vectors:
        - Ensure the contract has enough liquidity to unstake.
        - Check if the tokens are correctly unstaked and withdrawn from the BoostAggregator.
        - Validate the update of the `stakeFlag` state.

# TalosStrategyVanilla States

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542016/Maia/TalosStrategyVanilla_ttntlm.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542016/Maia/TalosStrategyVanilla_ttntlm.png)

- Idle
- FeesAccumulating
- FeesEarned
- FeesCompounded
- PositionRebalanced

## Transitions:

**Idle -> FeesAccumulating**

- Occurs as users trade within the Uniswap V3 pool range set by the strategy.
- Attack vectors:
    - Trades can manipulate the pool price.

**FeesAccumulating -> FeesEarned**

- Function: `_earnFees`
- Conditions: Automatically triggered before a position redeem (`beforeRedeem`) or deposit (`beforeDeposit`), or before reranging the position (`beforeRerange`).
- Attack vectors:
    - Contract balance manipulation.

**FeesEarned -> FeesCompounded**

- Function: `_compoundFees`
- Conditions: Automatically triggered after a position redeem (`beforeRedeem`) or deposit (`beforeDeposit`).
- Attack vectors:
    - Contract balance manipulation.
    - No slippage protection

**FeesCompounded -> PositionRebalanced**

- Occurs when the strategy rebalances or reranges the position.
- Attack vectors:
    - Rebalancing strategies can be manipulated.
    - Contract balance manipulation.

**PositionRebalanced -> Idle**

- After the position has been rebalanced and the strategy waits for the next accumulation of fees.
- Attack vectors:
    - Trades can manipulate the pool price.

---

# Ulysses Accounting Issues

### Accounting Issues

- Our audit of the smart contracts in Maia Ecosystem uncovered a critical issue within the Ulysses omnichain component. This issue restricts the interaction of tokens with decimals other than 18.
- The problem originated from incorrect assumptions made regarding two internal functions: `_normalizeDecimals()` and `_denormalizeDecimals()`.
- The intended purpose of these functions was to normalize the value of tokens without 18 decimals, enabling their compatibility with Maia's Ulysses omnichain.
- However, due to improper implementation, other tokens were unable to function properly with the omnichain.

### Demonstration

- To demonstrate this issue, we need to modify the following [line](https://github.com/code-423n4/2023-05-maia/blob/main/test/ulysses-omnichain/BranchBridgeAgentTest.t.sol#L55) in `test/ulysses-omnichain/BranchBridgeAgentTest.t.sol` to use 6 decimals instead of 18:
    - `underlyingToken = new MockERC20("underlying token", "UNDER", 6);`
- Running `forge test --match-contract BranchBridgeAgentTest` will result in multiple test failures.

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688297769/Maia_zzvhyh.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688297769/Maia_zzvhyh.png)

- The flowchart illustrates the execution flow of a failed test case, specifically `testCallOutWithDeposit()`, providing insight into the scenario that caused the failure.
- When the test file calls `BaseBranchRouter::callOutAndBridge()` through the `testCallOutWithDeposit()` function, it is forwarded to `BranchBridgeAgent::performCallOutAndBridge`, which in turn calls the internal function `_callOutAndBridge`.
- Within this function, two key actions take place:
    - All received data is stored in the `packedData` variable.
    - This received data is passed to `_depositAndCall()`.
- The problem arises when the deposit value is normalized before being stored in `packedData`, but it is passed as is to `_depositAndCall()`.
- Progressing further, the values received by `_depositAndCall()` are subsequently passed to `_createDepositSingle()`, which performs the following actions:
    - Updates the state by storing the normalized `_deposit` value.
    - Invokes `BranchPort::bridgeOut()` to retrieve tokens from the user.
- In `bridgeOut()`, when the contract pulls the tokens, it denormalizes the `_deposit` value (which was never normalized). This leads to an underflow, resulting in the entire transaction reverting and preventing the user from depositing the token.

### Mitigation

- The specific steps to mitigate this issue are outlined in the vulnerability report.

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688297769/Maia_1_otw3xe.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688297769/Maia_1_otw3xe.png)

- In this updated flowchart, we made the following changes to ensure the success of the test case:
    - Modified `_normalizeDecimals()` and `_denormalizeDecimals()` to produce the correct output.
    - Normalized the `_deposit` value when passing it to `BranchBridgeAgent::_depositAndCall()`.
    - Denormalized the `_deposit` value when storing it in the `getDeposit` mapping.

### Recommendation

- This demonstration highlights only one scenario, but we believe there may be multiple functions within the omnichain that have not implemented normalization and denormalization correctly.
- Our recommendation is to use the normalized value when interacting with the omnichain and employ the denormalized value for external interactions.

---

# Ulysses Omnichain

# Feedback and Recommendations

## Concerns with AnyCall

Multichain’s AnyCall router is at its version 7 release.  What is convenient is that it is an cross-chain solution that can work on both L1s and L2s without needing the cross-chain infrastructure implemented by dApps themselves.  

What is challenging with AnyCall involves 3 issues:

1. Multichain has poor communication and technical support on their public Discord
2. The AnyCall documentation is sparse, and the source needs to be referenced for technical details
3. The SMPC network suffers from a lack of client diversity, creating opportunities for security vulnerabilities across the entire network

## Cross-chain integration with AnyCall misconfigured

Part of the consequence of the poor documentation and MultiChain’s poor communication (mainly reachability on the Discord server) seems to have led to the following problem.  Because it’s not clear how to implement the protocol correctly, and developer relations on the server is difficult to find so that confusion can be cleared, the following outcome happens

### ****Branch-to-Branch txs fail, and "pay on src" misconfiguration****

`anyCall` accepts 2 different execution gas payment schemes:

1. pay on source chain
2. pay on destination chain

The documentation and intention (image below) of the ulysses omnichain protocol remark that "pay on destination" is how the system is made to execution gas payments.  However the opposite is true. We’ll see that this issue goes a bit deeper.  In `BranchBridgeAgent.sol`, the call to `anyCall` for the branch chain system is configured with the equivalent flag for "pay on source", `FLAG_ALLOW_CALLBACK`: 

(from: [https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L1087-L1094](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchBridgeAgent.sol#L1087-L1094))

```solidity
/**
 * @notice Internal function performs call to AnycallProxy Contract for cross-chain messaging.
 *   @param _calldata ABI encoded function call.
 */
function _performCall(bytes memory _calldata) internal virtual {
    //Sends message to AnycallProxy
    IAnycallProxy(localAnyCallAddress).anyCall(
        rootBridgeAgentAddress, _calldata, rootChainId, AnycallFlags.FLAG_ALLOW_FALLBACK, ""
    );
}
```

### Looking at the `AnycallV7Upgradeable.sol` and `AnycallV7Config.sol`

See the screenshots below. They show the relevant code for what happens when `IAnycallProxy.anyCall` is executed.

For #1, we can see the actual on-chain `anyCall` function call.  The key line is underlined in red.  All calls here can't pass without paying fees (through `_paySrcFees`) on the source chain. The calculation can be seen in [[1]](https://github.com/anyswap/multichain-smart-contracts/blob/main/contracts/anycall/v7/AnycallV7Config.sol#L522-L547).

What happens next in `_paySrcFees` is in image #2, underlined in red.  There's a requirement `require(msg.value >= fees, "no enough src fee")`.  This is the trap.  There won't be a `msg.value` in the call to `IAnycallProxy.anyCall`, as seen in image #3.

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542279/Maia/_paySrcFees_underlined_pbrjjd.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542279/Maia/_paySrcFees_underlined_pbrjjd.png)

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542279/Maia/require_no_enough_src_fee_underlined2_lxaltv.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542279/Maia/require_no_enough_src_fee_underlined2_lxaltv.png)

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542277/Maia/anycall_underlined3_ve4v3m.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542277/Maia/anycall_underlined3_ve4v3m.png)

### Aren’t there tests showing that this works?

Yes, but they use a mock with a fake `anyCall` [[2]](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/test/ulysses-omnichain/BranchBridgeAgentTest.t.sol), not the actual contracts though a forking test. What we get in the tests are placeholder addresses and mocks that return pre-determined values.  For that reason it was likely missed that the misconfigured integration with AnyCall was missed.

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542279/Maia/mocks_underlined4_qfgns4.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542279/Maia/mocks_underlined4_qfgns4.png)

## Testing Strategy causing missed implementation failures

The current testing strategy involves creating mocks for a large number of system objects.  That includes 3rd-party tokens, internal contracts, and external protocols such as AnyCall itself.  While it is helpful to be able to control the execution environment within a testing suite, the lack of exposure to actual systems in tests - especially those of cornerstone systems such as AnyCall in the Ulysses Omnichain tests - exposes more risk to failed assumptions than is necessary.

Using the Foundry toolkit’s forking tests is a useful mitigation to this.  Without needing to deploy local chains and relevant 3rd-party contracts, tests can be created that can utilize and instrument deployed versions of important systems, with the full control of EVM parameters.  These can be combined with the mocking system in Foundry to create tests that are closer to real-world parameters, and they help expose and correct assumptions and bugs in the codebase under test.

## Repeated Check-Effects-Interaction violations

There are many instances where possible reentrancy is potentially introduced in the codebase.  A large number of external-facing functions in `BranchBridgeAgent.sol` alone demonstrates this case.  Using Slither on the codebase, we can see this more clearly:

```solidity
INFO:Slither:./src/ulysses-omnichain/*.sol analyzed (268 contracts with 5 detectors), 145 result(s) found
```

145 possible cases of reentrancy were flagged in the `src/ulysses-omnichain/` folder alone.

While most code paths were not able to be explored in the time allotted for the audit, there is a greather-than-zero chance that viable reentrancy is introduced due to violations of the check-effects-interactions pattern.

## Internal documentation for implementation sources difficult to read because of `@inheritdoc`

The copious use of NatSpec in the codebase has been very welcome.  In general it provides  good documentation for the intention of each function.  

What could be improved on is the use of `@inheritdoc` in the implementations of various interfaces.  While the files containing the interfaces themselves have the documentation, the implementation files are likely to lack useful NatSpec in lieu of `@inheritdoc` tags, making the process of auditing those files more tedious, since switching contexts between the two files is necessary, and the fact there is no built-in command apparent in the project to generate documentation from the NatSpec comments themselves.

## Poisoned or Weird ERC-20 Tokens

It can be observed that the use or deposit of poisoned or weird ERC-20 tokens was marked as a known issue by the project team.  It is stated by the project team in the C4 Contest details that: 

“Our protocol has permissionless factories where anyone can create with poison 20 tokens or add poison erc20 tokens. While contracts generated by these are not in scope, **if it does affect other contracts or other balances, it is in scope.”**

However, we believe this functionality should be thoroughly investigated and mitigating controls implemented. 

What **would** occur if a user did deposit malicious or ‘weird’ ERC20 tokens and what strategies can be utilized to protect the protocol from such malicious use cases?

Although there are multiple variations of malicious or weird ERC20 tokens that can deposited, there are a selection of potential hazardous implementations. Specifically, Fee on Transfer, Rebasing, Flash-Mintable, Tokens with Blacklists such as USDC and USDT are viewed as potentially hazardous to the wider functionality of Ulysses and reputation of the protocol if unsophisticated users accidentally utlize them.

Branch Ports serve as a vault with a single-asset pool for each Omnichain token active in a given chain. Due to the burn and mint process in a swap, if any of the hazardous tokens mentioned above were deposited, it could disrupt accurate accounting between pools during swaps.

It is well understood that an on-chain, contract-level allow-list of known good tokens is not a viable mitigating control due the permission-less nature of Ulyssess Omnichian. It is recommended that an off-chain allow-list in the official UI to protect unsophisticated users from utilizing underlying or associated hTokens that violate the expectations of how the Omnichain protocol should function.

# Architecture Description and Diagrams

While exploring the Ulysses-Omnichain codebase, it was helpful to diagram the architecture. Here are some diagrams that resulted. 

NOTE: these diagrams were generated with PlantUML.  Source can be provided by request.

## Bridge Agents

### Cross-chain Contract Architecture

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/1_prsn9q.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/1_prsn9q.png)

### AnyCall Focus

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/2_fpvcyt.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/2_fpvcyt.png)

## AnyCall integration

### Ulysses + anyCall v7 summary

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/3_ppej0r.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/3_ppej0r.png)

### BranchBridgeAgent cross-chain request framework

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542588/Maia/4_puzsgg.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542588/Maia/4_puzsgg.png)

### **Example comms: a Cross-chain swap**

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542588/Maia/5_ai3dsv.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542588/Maia/5_ai3dsv.png)

### Generic AnyCall Cross-Chain Transactions

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/6_b5ftud.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/6_b5ftud.png)

### Intended Gas Swap and Execution Cost Payment

![https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/7_csrsra.png](https://res.cloudinary.com/duqxx6dlj/image/upload/v1688542587/Maia/7_csrsra.png)

### Time spent:
540 hours