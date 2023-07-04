### QA Report Issues List

- [x] **Low 01** → There may be problems with the use of Layer2
- [x] **Low 02** → Head Overflow Bug in Calldata Tuple ABI-Reencoding
- [x] **Low 03** → There is a risk that a user with a high governance power will not be able to bid with `propose()`
- [x] **Low 04** → Migrating with "migratePartnerVault()" may result in loss of user funds
- [x] **Low 05** → Malicious user can create pool with poison ERC20 contract
- [x] **Low 06** → Project Upgrade and Stop Scenario should be
- [x] **Low 07**→ Project has  security risk from DAO attack using proposal
- [x] **Low 08**→ First ERC4626 deposit exploit can break share calculation
- [x] **Low 09**→ Missing Event for  initialize
- [x] **Low 10**→ Missing maxwithdraw check in withdraw function of ERC-4626
- [x] **Low 11**→ `gaugeWeight` , `gaugeBoost` , `governance` contracts must have the same addresses in Hermes and Maia, this has no control
- [x] **Low 12**→ Processing of `poolId` and `tokenId` incorrectly starts with 2 instead of 1
- [x] **Low 13**→ If onlyOwner runs `renounceOwnership()` in the `PartnerManagerFactory` contract, the contract may become unavailable
- [x] **Low 14**→ There isnt skim function
- [x] **Low 15**→ Contract (ERC4626.sol) used as dependency does not track upstream changes
- [x] **Low 16**→ Use ERC-5143: Slippage Protection for Tokenized Vault
- [x] **Non-Critical  17**→ Unused Import
- [x] **Non-Critical  18**→ Assembly Codes Specific – Should Have Comments
- [x] **Non-Critical  19**→ Using a `modifier` with no meaning can cause confusion
- [x] **Non-Critical  20**→ With 0 address control of `owner`, it is best practice to maintain consistency across the entire codebase.
- [x] **Non-Critical  21**→ `DIVISIONER` is inconsistent across contracts
- [x] **Non-Critical  22**→ The `nonce` architecture of the `delegateBySig()` function isn’t usefull  
- [x] **Non-Critical  23**→ Does not `event-emit` during significant parameter changes

### [Low-01] There may be problems with the use of Layer2

According to the scope information of the project, it is stated that it can be used in rollup chains and popular EVM chains.

```js
README.md:
  797  - Is it a fork of a popular project?:   true
  798: - Does it use rollups?:   true
  799: - Is it multi-chain?:  true
  800: - Does it use a side-chain?: true

```

Some conventions in the project are set to Pragma ^0.8.19, allowing the conventions to be compiled with any 0.8.x compiler. The problem with this is that Arbitrum is [Compatible](https://developer.arbitrum.io/solidity-support) with 0.8.20 and newer. Contracts compiled with these versions will result in a non-functional or potentially damaged version that does not behave as expected. The default behavior of the compiler will be to use the latest version which means it will compile with version 0.8.20 which will produce broken code by default.

### [Low-02] Head Overflow Bug in Calldata Tuple ABI-Reencoding

There is a known security vulnerability between versions 0.5.8 - 0.8.16 of Solidity, details on it below

The effects of the bug manifest when a contract performs ABI-encoding of a tuple that meets all of the following conditions:
The last component of the tuple is a (potentially nested) statically-sized calldata array with the most base type being either uint or bytes32. E.g. bytes32[10] or uint[2][2][2].
The tuple contains at least one dynamic component. E.g. bytes or a struct containing a dynamic array.
The code is using ABI coder v2, which is the default since Solidity 0.8.0.


https://blog.soliditylang.org/2022/08/08/calldata-tuple-reencoding-head-overflow-bug/


```solidity
   3: pragma solidity ^0.8.0;

src/ulysses-omnichain/interfaces/IVirtualAccount.sol:
   7: struct Call {
   8:     address target;
   9:     bytes callData; // @audit dynamic
  10: }


src/ulysses-omnichain/VirtualAccount.sol:
  40      /// @inheritdoc IVirtualAccount
  41:     function call(Call[] calldata calls)  // @audit Call tupple 
  42:         external
  43:         requiresApprovedCaller
  44:         returns (uint256 blockNumber, bytes[] memory returnData)
  45:     {
```

### Recommended Mitigation Steps

Because of this problem, I recommend coding the contract with fixed pragma instead of floating pragma for compiling with min 0.8.16 and higher versions. VirtualAccount contract can be compiled with floating pragma with all versions 0.8

### [Low-03] There is a risk that a user with a high governance power will not be able to bid with `propose()`

Centralization risk in the DOA mechanism is that the people who can submit proposals must be on the whitelist, which is contrary to the essence of the DAO as it carries the risk of a user not being able to submit a proposal in the DAO even if he has a very high stake.

We should point out that this problem is beyond the centrality risk and is contrary to the functioning of the DAO. Because a user who has a governance token to be active in the DAO may not be able to bid if he is not included in the whitelist (there is no information in the documents about whether there is a warning that he cannot make a proposal if he is not on the whitelist)

There is no information on how, under what conditions and by whom the While List will be taken.

```solidity

src/governance/GovernorBravoDelegateMaia.sol:
  103       */
  104:     function propose(
  105:         address[] memory targets,
  106:         uint256[] memory values,
  107:         string[] memory signatures,
  108:         bytes[] memory calldatas,
  109:         string memory description
  110:     ) public returns (uint256) {
  111:         // Reject proposals before initiating as Governor
  112:         require(initialProposalId != 0, "GovernorBravo::propose: Governor Bravo not active");
  113:         // Allow addresses above proposal threshold and whitelisted addresses to propose
  114:         require(
  115:             govToken.getPriorVotes(msg.sender, sub256(block.number, 1)) > getProposalThresholdAmount()
  116:                 || isWhitelisted(msg.sender),
  117:             "GovernorBravo::propose: proposer votes below proposal threshold"
  118:         );
  119:         require(
  120:             targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,
  121:             "GovernorBravo::propose: proposal function information arity mismatch"
  122:         );
  123:         require(targets.length != 0, "GovernorBravo::propose: must provide actions");
  124:         require(targets.length <= proposalMaxOperations, "GovernorBravo::propose: too many actions");
  125: 
  126:         uint256 latestProposalId = latestProposalIds[msg.sender];
  127:         if (latestProposalId != 0) {
  128:             ProposalState proposersLatestProposalState = state(latestProposalId);
  129:             require(
  130:                 proposersLatestProposalState != ProposalState.Active,
  131:                 "GovernorBravo::propose: one live proposal per proposer, found an already active proposal"
  132:             );
  133:             require(
  134:                 proposersLatestProposalState != ProposalState.Pending,
  135:                 "GovernorBravo::propose: one live proposal per proposer, found an already pending proposal"
  136:             );
  137:         }
  138: 
  139:         uint256 startBlock = add256(block.number, votingDelay);
  140:         uint256 endBlock = add256(startBlock, votingPeriod);
  141: 
  142:         proposalCount++;
  143:         uint256 newProposalID = proposalCount;
  144:         Proposal storage newProposal = proposals[newProposalID];
  145:         // This should never happen but add a check in case.
  146:         require(newProposal.id == 0, "GovernorBravo::propose: ProposalID collsion");
  147:         newProposal.id = newProposalID;
  148:         newProposal.proposer = msg.sender;
  149:         newProposal.eta = 0;
  150:         newProposal.targets = targets;
  151:         newProposal.values = values;
  152:         newProposal.signatures = signatures;
  153:         newProposal.calldatas = calldatas;
  154:         newProposal.startBlock = startBlock;
  155:         newProposal.endBlock = endBlock;
  156:         newProposal.forVotes = 0;
  157:         newProposal.againstVotes = 0;
  158:         newProposal.abstainVotes = 0;
  159:         newProposal.canceled = false;
  160:         newProposal.executed = false;
  161: 
  162:         latestProposalIds[newProposal.proposer] = newProposal.id;
  163: 
  164:         emit ProposalCreated(
  165:             newProposal.id, msg.sender, targets, values, signatures, calldatas, startBlock, endBlock, description
  166:         );
  167:         return newProposal.id;
  168:     }
```


## Proof of concept

1- Alice receives a governance token to be actively involved in the project's DAO, participates in the voting, and also wants to present a proposal with the proposal in the project, but cannot do this because there is no whitelist.
2- There is no information about the whitelist conditions and how to whitelist Alice in the documents and NatSpec comments.

It is observed that the DAO proposals of the project are voted by a small number of people, for example this can be seen in the proposal below.As the project is new, this is normal in DAO ecosystems, but the centrality risk should be expected to decrease over time;

https://snapshot.org/#/maiadao.eth/proposal/0x38d9ffaa0641eb494c20d2b034df321a6865bf8859487468e1583dd095837488

## Recommended Mitigation Steps

1- In the short term; Clarify the whitelist terms and processes and add them to the documents, and inform the users as a front-end warning in Governance token purchases.
2- In the Long Term; In accordance with the philosophy of the DAO, ensure that a proposal can be made according to the share weight.

### [Low-04] Migrating with "migratePartnerVault()" may result in loss of user funds

The `ERC4626PartnerManager.migratePartnerVault()` function defines the new vault contract in case vaults are migrated.

```solidity
src/maia/tokens/ERC4626PartnerManager.sol:
  187      /// @inheritdoc IERC4626PartnerManager
  188:     function migratePartnerVault(address newPartnerVault) external onlyOwner {
  189:         if (factory.vaultIds(IBaseVault(newPartnerVault)) == 0) revert UnrecognizedVault();
  190: 
  191:         address oldPartnerVault = partnerVault;
  192:         if (oldPartnerVault != address(0)) IBaseVault(oldPartnerVault).clearAll();
  193:         bHermesToken.claimOutstanding();
  194: 
  195:         address(gaugeWeight).safeApprove(oldPartnerVault, 0);
  196:         address(gaugeBoost).safeApprove(oldPartnerVault, 0);
  197:         address(governance).safeApprove(oldPartnerVault, 0);
  198:         address(partnerGovernance).safeApprove(oldPartnerVault, 0);
  199: 
  200:         address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
  201:         address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
  202:         address(governance).safeApprove(newPartnerVault, type(uint256).max);
  203:         address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
  204: 
  205:         partnerVault = newPartnerVault;
  206:         if (newPartnerVault != address(0)) IBaseVault(newPartnerVault).applyAll();
  207: 
  208:         emit MigratePartnerVault(address(this), newPartnerVault);
  209:     }
```
However, it has some design vulnerabilities.
1- Best practice, many projects use upgradable pattern instead of migrate, using a more war-tested method is more accurate in terms of security. Upgradability allows for making changes to contract logic while preserving the state and user funds. Migrating contracts can introduce additional risks, as the new contract may not have the same level of security or functionality. Consider implementing an upgradability pattern, such as using proxy contracts or modular design, to allow for safer upgrades without compromising user funds.



2- There may be user losses due to the funds remaining in the old safe, there is no control regarding this.



## Recommended Mitigation Steps

To mitigate this risk, you should implement appropriate measures to handle user funds during migration. This could involve implementing mechanisms such as time-limited withdrawal periods or providing clear instructions and notifications to users about the migration process, ensuring they have the opportunity to withdraw their funds from the old vault before the migration occurs.

```diff
src/maia/tokens/ERC4626PartnerManager.sol:
  187      /// @inheritdoc IERC4626PartnerManager
  188:     function migratePartnerVault(address newPartnerVault) external onlyOwner {
  189:         if (factory.vaultIds(IBaseVault(newPartnerVault)) == 0) revert UnrecognizedVault();
  190: 
  191:         address oldPartnerVault = partnerVault;
- 192:         if (oldPartnerVault != address(0)) IBaseVault(oldPartnerVault).clearAll();
+                 if (oldPartnerVault != address(0)) {
+                     // Check if there are user funds in the old vault
+                     uint256 oldVaultBalance = IBaseVault(oldPartnerVault).getBalance(); // Assuming a function to retrieve the balance
+                     if (oldVaultBalance > 0) {
+                          // Handle the situation where user funds exist in the old vault
+                          // You can choose an appropriate action, such as notifying users or allowing them to withdraw their funds
+                        // It's important to define a clear process and communicate it to users in advance
+                        revert UserFundsExistInOldVault();
+                      }
+                      IBaseVault(oldPartnerVault).clearAll();
+                }

  193:         bHermesToken.claimOutstanding();
  194: 
  195:         address(gaugeWeight).safeApprove(oldPartnerVault, 0);
  196:         address(gaugeBoost).safeApprove(oldPartnerVault, 0);
  197:         address(governance).safeApprove(oldPartnerVault, 0);
  198:         address(partnerGovernance).safeApprove(oldPartnerVault, 0);
  199: 
  200:         address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);
  201:         address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);
  202:         address(governance).safeApprove(newPartnerVault, type(uint256).max);
  203:         address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
  204: 
  205:         partnerVault = newPartnerVault;
  206:         if (newPartnerVault != address(0)) IBaseVault(newPartnerVault).applyAll();
  207: 
  208:         emit MigratePartnerVault(address(this), newPartnerVault);
  209:     }
```

### [Low-05] Malicious user can create pool with poison ERC20 contract

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L74-L88

Pools created with the `createPool()` function run the risk of introducing a poison ERC20 contract.


```solidity
src/ulysses-amm/factories/UlyssesFactory.sol:
  73      /// @inheritdoc IUlyssesFactory
  74:     function createPool(ERC20 asset, address owner) external returns (uint256) {
  75:         return _createPool(asset, owner);
  76:     }
  77: 

  83:     function _createPool(ERC20 asset, address owner) private returns (uint256 _poolId) {
  84:         if (address(asset) == address(0)) revert InvalidAsset();
  85:         _poolId = ++poolId;
  86:         pools[_poolId] =
  87:             UlyssesPoolDeployer.deployPool(_poolId, address(asset), "Ulysses Pool", "ULP", owner, address(this));
  88:     }

src/ulysses-amm/factories/UlyssesFactory.sol:
  24       */
  25:     function deployPool(
  26:         uint256 id,
  27:         address asset,
  28:         string calldata name,
  29:         string calldata symbol,
  30:         address owner,
  31:         address factory
  32:     ) public returns (UlyssesPool) {
  33:         return new UlyssesPool(id, asset, name, symbol, owner, factory);
  34:     }


```

Specified because the contract `UlyssesFactory.sol` is in scope

```js
README.md:
 
  30: - Our protocol has permissionless factories where anyonce can create with poison erc20 tokens or add poison erc20 tokens. 
  While contracts generated by these are not in scope, __if it does affect other contracts or other balances, it is in scope.__
```

### [Low-06] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

This can be done by adding the 'pause' architecture, which is included in many projects, to critical functions, and by authorizing the existing onlyOwner.

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol
### [Low-07] Project has  security risk from DAO attack using proposal

`GovernorBravoDelegateMaia.propose()` function used to propose a new proposal , Sender must have delegates above the proposal threshold.This functin is very critical because it builds an important task where DAO proposals are given, however it should be tightly controlled for a recent security concern, the proposal mechanism in the DAO must have limits, not everyone can read the code in proposal evaluation, the following hack is done using exactly this function, each proposal in It may even need to pass a minor inspection.


https://cointelegraph.com/news/attacker-hijacks-tornado-cash-governance-via-malicious-proposal


```solidity

src/governance/GovernorBravoDelegateMaia.sol:

  103       */

  104:     function propose(
  105:         address[] memory targets,
  106:         uint256[] memory values,
  107:         string[] memory signatures,
  108:         bytes[] memory calldatas,
  109:         string memory description
  110:     ) public returns (uint256) {
                   // Code Details...

```

This vulnerability is very new and very difficult to prevent, but the importance of the project regarding this vulnerability could not be seen in the documents and NatSpec comments.

It is known that only Whitelist users can submit proposals, but whitelist terms etc. unknown, this problem persists.


## Proof of concept

A similar vulnerability has been analyzed in full detail here;
https://twitter.com/BlockSecTeam/status/1660214665429876740?t=bxFysCYHryV3Rx0yeGmmTg&s=33
https://docs.google.com/presentation/d/1QgYUmowhFxjuh18eV7zIlz0icr4MfnjKOJJ__yYVz-w/edit#slide=id.p

## Recommended Mitigation Steps

Projects should have a short audit of the proposals

https://a16zcrypto.com/posts/article/dao-governance-attacks-and-how-to-avoid-them/


### [Low-08] First ERC4626 deposit exploit can break share calculation

A well known attack vector for almost all shares based liquidity pool contracts, where an early user can manipulate the price per share and profit from late users' deposits because of the precision loss caused by the rather large value of price per share.


```solidity

src/erc-4626/ERC4626.sol:
  105      /// @inheritdoc IERC4626
  106:     function convertToShares(uint256 assets) public view virtual returns (uint256) {
  107:         uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.
  108: 
  109:         return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());
  110:     }
  111  

src/erc-4626/ERC4626DepositOnly.sol:
  67      /// @inheritdoc IERC4626DepositOnly
  68:     function convertToShares(uint256 assets) public view virtual returns (uint256) {
  69:         uint256 supply = totalSupply; // Saves an extra SLOAD if totalSupply is non-zero.
  70: 
  71:         return supply == 0 ? assets : assets.mulDiv(supply, totalAssets());
  72:     }

```
### Proof Of Concept
1 - A malicious early user can `deposit()` with 1 wei of asset token as the first depositor of the LToken, and get 1 wei of shares

2 - Then the attacker can send 10000e18 - 1 of asset tokens and inflate the price per share from 1.0000 to an extreme value of 1.0000e22 ( from (1 + 10000e18 - 1) / 1)

3 - As a result, the future user who deposits 19999e18 will only receive 1 wei (from 19999e18 * 1 / 10000e18) of shares token

4 - They will immediately lose 9999e18 or half of their deposits if they `redeem()` right after the `deposit()`

The attacker can profit from future users' deposits. While the late users will lose part of their funds to the attacker.


```solidity
// test/ERC4626-Cloned.t.sol
function SharePriceManipulation() external {
    address USER1 = address(0x583031D1113aD414F02576BD6afaBfb302140225);
    address USER2 = address(0xdD870fA1b7C4700F2BD7f44238821C26f7392148);
    vm.label(USER1, "USER1");
    vm.label(USER2, "USER2");

    // Resetting the withdrawal fee for cleaner amounts.
    ERC4626-Cloned.setWithdrawalPenalty(0);

    vm.startPrank(address(VaultToken));        
    VaultToken.mint(USER1, 10e18);
    VaultToken.mint(USER2, 19e18);
    vm.stopPrank();

    vm.startPrank(USER1);
    VaultToken.approve(address(ERC4626-Cloned), 1);
    // USER1 deposits 1 wei of VaultToken and gets 1 wei of shares.
    ERC4626-Cloned.deposit(1, USER1);
    // USER1 sends 10e18-1 of VaultToken and sets the price of 1 wei of shares to 10e18 VaultToken.
    VaultToken.transfer(address(ERC4626-Cloned), 10e18-1);
    vm.stopPrank();

    vm.startPrank(USER2);
    VaultToken.approve(address(ERC4626-Cloned), 19e18);
    // USER2 deposits 19e18 of VaultToken and gets 1 wei of shares due to rounding and the price manipulation.
    ERC4626-Cloned.deposit(19e18, USER2);
    vm.stopPrank();

    // USER1 and USER2 redeem their shares.           
    vm.prank(USER1);
    ERC4626-Cloned.redeem(1, USER1, USER1);
    vm.prank(USER2);
    ERC4626-Cloned.redeem(1, USER2, USER2);

    // USER1 and USER2 both got 14.5 VaultToken.
    // But USER1 deposited 10 VaultToken and USER2 deposited 19 VaultToken – thus, USER1 stole VaultToken tokens from USER2.
    // With withdrawal fees enabled, USER1 would've been penalized more than USER2
    // (14.065 VaultToken vs 14.935 VaultToken tokens withdrawn, respectively),
    // but USER1 would've still gotten more VaultToken that she deposited.
    assertEq(VaultToken.balanceOf(USER1), 14.5e18);
    assertEq(VaultToken.balanceOf(USER2), 14.5e18);
}
```




### Recommended Mitigation Steps
Consider either of these options:

1-Consider sending the first 1000 shares to the address 0, a mitigation used in Uniswap V2

2-In the deposit function of project, consider requiring a reasonably high minimal amount of assets during first deposit. The amount needs to be high enough to mint many shares to reduce the rounding error and low enough to be affordable to users.

3-On the first deposit, consider minting a fixed and high amount of shares, irrespective of the deposited amount

4-Consider seeding the pools during deployment. This needs to be done in the deployment transactions to avoiding front-running attacks. The amount needs to be high enough to reduce the rounding error.

5-Consider sending first 1000 wei of shares to the zero address. This will significantly increase the cost of the attack by forcing an attacker to pay 1000 times of the share price they want to set. For a well-intended user, 1000 wei of shares is a negligible amount that won't diminish their share significantly.



### [Low-09] Missing Event for  initialize

**Context:**
```solidity
12 results - 12 files

src/governance/GovernorBravoDelegateMaia.sol:
  55       */
  56:     function initialize(
  57          address timelock_,

src/hermes/interfaces/IBaseV2Minter.sol:
  51       */
  52:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external;
  53  

src/hermes/minters/BaseV2Minter.sol:
  77      /// @inheritdoc IBaseV2Minter
  78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {
  79          if (initializer != msg.sender) revert NotInitializer();

src/ulysses-omnichain/BaseBranchRouter.sol:
  36      /// @notice Contract state initialization function.
  37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {
  38          require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");

src/ulysses-omnichain/BranchPort.sol:
  98  
  99:     function initialize(address _coreBranchRouter, address _bridgeAgentFactory) external virtual onlyOwner {
  100          require(coreBranchRouterAddress == address(0), "Contract already initialized");

src/ulysses-omnichain/CoreRootRouter.sol:
  62  
  63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {
  64          bridgeAgentAddress = payable(_bridgeAgentAddress);

src/ulysses-omnichain/MulticallRootRouter.sol:
  73  
  74:     function initialize(address _bridgeAgentAddress) external onlyOwner {
  75          require(_bridgeAgentAddress != address(0), "Bridge Agent Address cannot be 0");

src/ulysses-omnichain/RootPort.sol:
  127  
  128:     function initialize(address _bridgeAgentFactory, address _coreRootRouter) external onlyOwner {
  129          require(_setup, "Setup ended.");

src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol:
  53  
  54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {
  55          address newCoreBridgeAgent = address(

src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:
  82  
  83:     function initialize(address _coreRootBridgeAgent) external virtual onlyOwner {
  84          require(_coreRootBridgeAgent != address(0), "Core Root Bridge Agent cannot be 0");

src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol:
  34  
  35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {
  36          require(_coreRouter != address(0), "CoreRouter address cannot be 0");

src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol:
  39  
  40:     function initialize(address _coreRouter) external onlyOwner {
  41          require(_coreRouter != address(0), "CoreRouter address cannot be 0");


```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes
Issuing event-emit during initialization is a detail that many projects skip

**Recommendation:**
Add Event-Emit


### [Low-10] Missing maxwithdraw check in withdraw function of ERC-4626

In the [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626#maxwithdraw) specification it reads:

However withdraw functions misses this check:
```
Maximum amount of the underlying asset that can be withdrawn from the owner 
balance in the Vault, through a withdraw call.

```

**Context:**
```solidity
src/erc-4626/ERC4626.sol:
  60      /// @inheritdoc IERC4626
  61:     function withdraw(uint256 assets, address receiver, address owner) public virtual returns (uint256 shares) {
  62:         shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
  63: 
  64:         if (msg.sender != owner) {
  65:             uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
  66: 
  67:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
  68:         }
  69: 
  70:         beforeWithdraw(assets, shares);
  71: 
  72:         _burn(owner, shares);
  73: 
  74:         emit Withdraw(msg.sender, receiver, owner, assets, shares);
  75: 
  76:         address(asset).safeTransfer(receiver, assets);
  77:     }


src/erc-4626/ERC4626MultiToken.sol:
  130  
  131:     /// @inheritdoc IERC4626MultiToken
  132:     function withdraw(uint256[] calldata assetsAmounts, address receiver, address owner)
  133:         public
  134:         virtual
  135:         nonReentrant
  136:         returns (uint256 shares)
  137:     {
  138:         shares = previewWithdraw(assetsAmounts); // No need to check for rounding error, previewWithdraw rounds up.
  139: 
  140:         if (msg.sender != owner) {
  141:             uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
  142: 
  143:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
  144:         }
  145: 
  146:         beforeWithdraw(assetsAmounts, shares);
  147: 
  148:         _burn(owner, shares);
  149: 
  150:         emit Withdraw(msg.sender, receiver, owner, assetsAmounts, shares);
  151: 
  152:         sendAssets(assetsAmounts, receiver);
  153:     }
```

**Recommendation:**
An additional check is added to the function withdraw in ERC4626.sol .This checks if the amount of asset is less than or equal to the amount allowed by owner.

```diff

src/erc-4626/ERC4626.sol:
  61:     function withdraw(uint256 assets, address receiver, address owner) public virtual returns (uint256 shares) {
  62:         shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.
  63: 
  64:         if (msg.sender != owner) {
  65:             uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.
  66: 
- 67:             if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
+                   if (allowed != type(uint256).max) {
+              	         require(assets <= allowance[owner][msg.sender], "amount to be withdraw is more than allowed");
+                        allowance[owner][msg.sender] = allowed - shares;
+                   }
  68:         }
  69: 
  70:         beforeWithdraw(assets, shares);
  71: 
  72:         _burn(owner, shares);
  73: 
  74:         emit Withdraw(msg.sender, receiver, owner, assets, shares);
  75: 
  76:         address(asset).safeTransfer(receiver, assets);
  77:     }

```

### [Low-11] `gaugeWeight` , `gaugeBoost` , `governance` contracts must have the same addresses in Hermes and Maia, this has no control

Addresses of `gaugeWeight` , `gaugeBoost` , `governance` contracts are initially set in the `constructor` part of Hermes and Maia projects, it is important that these addresses are the same, there is no control, this problem is reported because deploy tests cannot be seen in test contracts

```solidity

src/hermes/UtilityManager.sol:
  44:     constructor(address _gaugeWeight, address _gaugeBoost, address _governance) {
  45:         gaugeWeight = bHermesGauges(_gaugeWeight);
  46:         gaugeBoost = bHermesBoost(_gaugeBoost);
  47:         governance = ERC20Votes(_governance);
  48:     }

src/maia/PartnerUtilityManager.sol:
  36:     constructor(
  37:         address _gaugeWeight,
  38:         address _gaugeBoost,
  39:         address _governance,
  40:         address _partnerGovernance,
  41:         address _partnerVault
  42:     ) UtilityManager(_gaugeWeight, _gaugeBoost, _governance) {
  43:         partnerGovernance = ERC20Votes(_partnerGovernance);
  44:         partnerVault = _partnerVault;
  45:     }

```
### Recommended Mitigation Steps

In deploy contracts, update the contract addresses here to feed from each other, this way the possibility of an error is eliminated


### [Low-12] Processing of `poolId` and `tokenId` incorrectly starts with 2 instead of 1

`poolId` and `tokenId` values are initialized with 1 in the contract by default, but when creating pool and token with create functions, the first value is set to 2, therefore IDs 1 are empty, this causes problems in processing arrays and monitoring in offchain.

```diff

src/ulysses-amm/factories/UlyssesFactory.sol:
  47  
  48:     ///@notice next poolId
- 49:     uint256 public poolId = 1;
+           uint256 public poolId; // Default value 0
  50: 
  51:     ///@notice next tokenId
- 52:     uint256 public tokenId = 1;
+           uint256 public tokenId; // Default value 0
83:     function _createPool(ERC20 asset, address owner) private returns (uint256 _poolId) {
  84:         if (address(asset) == address(0)) revert InvalidAsset();
  85:         _poolId = ++poolId;
  86:         pools[_poolId] =
  87:             UlyssesPoolDeployer.deployPool(_poolId, address(asset), "Ulysses Pool", "ULP", owner, address(this));
  88:     }

```

### [Low-13] If onlyOwner runs `renounceOwnership()` in the `PartnerManagerFactory` contract, the contract may become unavailable

There are two dynamic arrays in the `PartnerManagerFactory` contract, values are added to these arrays with the `push` keyword, if the number in these arrays increases, the block may be over the gas limit, for such cases it is necessary to have the feature of deleting elements from the array with the `pop` keyword, this is exactly what the contract has;

```solidity
src/maia/factories/PartnerManagerFactory.sol:
  21:     PartnerManager[] public override partners;
  24:     IBaseVault[] public override vaults;


src/maia/factories/PartnerManagerFactory.sol:
  79      /// @inheritdoc IPartnerManagerFactory
  80:     function removePartner(PartnerManager partnerManager) external onlyOwner {
  81:         if (partners[partnerIds[partnerManager]] != partnerManager) revert InvalidPartnerManager();
  82:         delete partners[partnerIds[partnerManager]];
  83:         delete partnerIds[partnerManager];
  84: 
  85:         emit RemovedPartner(partnerManager);
  86:     }

  89:     function removeVault(IBaseVault vault) external onlyOwner {
  90:         if (vaults[vaultIds[vault]] != vault) revert InvalidVault();
  91:         delete vaults[vaultIds[vault]];
  92:         delete vaultIds[vault];
  93: 
  94:         emit RemovedVault(vault);
  95:     }

```

Therefore, the `onlyOwner` authority here is very important for the contract, but the `Ownable.sol` library imported with the import has the `renounceOwnership()` feature, in case the owner accidentally triggers this function, the remove functions will not work and the contract will block gas due to arrays. may have a continuous structure that exceeds its limit

https://github.com/Vectorized/solady/blob/main/src/auth/Ownable.sol#L136

```solidity

src/maia/factories/PartnerManagerFactory.sol:
    4: import {Ownable} from "solady/auth/Ownable.sol";
  12: contract PartnerManagerFactory is Ownable, IPartnerManagerFactory {
```

### Recommended Mitigation Steps

The solution to this is to overide and disable the renounceOwnership() function as implemented in many contracts in his project, it is important to include this code in the contract


```solidity
 function renounceOwnership() public payable override onlyOwner {
        revert("Cannot renounce ownership");
    }
```

### [Low-14] There isnt skim function

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L19

User can lost tokens, which were send directly to `UlyssesPool` contract, without using special functions in `UlyssesPool.sol`
 
User cant get back any tokens, if he mistakable send them directly to pool.sol (using transfer function of token contract)

https://medium.com/coinmonks/how-to-sync-and-skim-in-uniswap-b536c921e66e


## Recommendation
Add `skim` function, like in uniswap, which allow users transfer tokens back. 

For this purpose, contract should know exact count of loan/collateral tokens, which were transfered through deposit/withdraw/borrow/.. functions.

### [Low-15] Contract (ERC4626.sol) used as dependency does not track upstream changes

Description 
ERC4626 uses a modified version of solmate's ERC4626 implementation, but the documentation does not specify which version or commit is used. This indicates that the protocol does not track upstream changes in contracts used as dependencies. Therefore, contracts may not reflect updates or security fixes implemented in their dependencies, as these updates need to be manually integrated.

Exploit Scenario 
Third-party contract (Solmate's ERC4626) used in project receives an update with a critical fix for a vulnerability, but the update is not yet manually integrated in the current version of the protocol. An attacker detects the use of vulnerable ERC4626 contract in the protocol and exploits the vulnerability.

Codebase References
[ERC4626.sol](https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol)

Recommendation 
Review the codebase and document the source and version of each dependency. Include third-party sources as modules in the project to maintain path consistency and ensure dependencies are updated periodically.


### [Low-16] Use ERC-5143: Slippage Protection for Tokenized Vault

Project use ERC-4626 standart

EIP-4626 is vulnerable to the so-called inflation attacks. This attack results from the possibility to manipulate the exchange rate and front run a victim’s deposit when the vault has low liquidity volume.


**Proposed mitigation**


The following standard extends the EIP-4626 Tokenized Vault standard with functions dedicated to the safe interaction between EOAs and the vault when price is subject to slippage.

https://eips.ethereum.org/EIPS/eip-5143

### [Non Critical-17] Unused Import

Some imports aren’t used inside the project

```solidity
src/erc-20/ERC20Boost.sol:

   12: import {IBaseV2Gauge} from "@gauges/interfaces/IBaseV2Gauge.sol";
   14: import {Errors} from "./interfaces/Errors.sol";


src/maia/vMaia.sol:
  6: import {Ownable} from "solady/auth/Ownable.sol";

```



### [Non Critical-18] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.


```solidity
src/governance/GovernorBravoDelegateMaia.sol:
  537          uint256 chainId;
  538:         assembly {

src/governance/GovernorBravoDelegator.sol:
  60          (bool success, bytes memory returnData) = callee.delegatecall(data);
  61:         assembly {
  62              if eq(success, 0) { revert(add(returnData, 0x20), returndatasize()) }
  75:         assembly {
  76              let free_mem_ptr := mload(0x40)

src/maia/libraries/DateTimeLib.sol:
  42          /// @solidity memory-safe-assembly
  43:         assembly {
  44:             epochDay := add(epochDay, 719468)
  45              let doe := mod(epochDay, 146097)

src/ulysses-amm/UlyssesPool.sol:
   355          /// @solidity memory-safe-assembly
   356:         assembly {
   379          /// @solidity memory-safe-assembly
   380:         assembly {

   552          /// @solidity memory-safe-assembly
   553:         assembly {

   580          /// @solidity memory-safe-assembly
   581:         assembly {
   582              switch positiveTransfer

   632          /// @solidity memory-safe-assembly
   633:         assembly {
   634              switch lt(newRebalancingFee, oldRebalancingFee)

   697          // @solidity memory-safe-assembly
   698:         assembly {
   699              // Load the rebalancing fee slot to get the fee parameters

   736          /// @solidity memory-safe-assembly
   737:         assembly {


```


### [Non Critical-19] Using a `modifier` with no meaning can cause confusion

`claimPartnerGovernance()` function has `checkPartnerGovernance` modifier but this modifier has no function, it may be better not to use this modifier at all


```solidity
src/maia/PartnerUtilityManager.sol:
  157      /// @inheritdoc IPartnerUtilityManager
  158:     function claimPartnerGovernance(uint256 amount) public checkPartnerGovernance(amount) {
  159:         userClaimedPartnerGovernance[msg.sender] += amount;
  160:         address(partnerGovernance).safeTransfer(msg.sender, amount);
  161:     }
  162: 
  163:     /*///////////////////////////////////////////////////////////////
  164:                             MODIFIERS
  165:     //////////////////////////////////////////////////////////////*/
  166: 
  167:     /// @dev Checks available governance allows for call.
  168:     modifier checkPartnerGovernance(uint256 amount) virtual;
  169  }
```



### [Non Critical-20] With 0 address control of `owner`, it is best practice to maintain consistency across the entire codebase.

The `owner` authority is an important authorization in almost all contracts, this address is defined in the constructor or initialize, where 0 address control is already included in Automatic discovery

However, in the codebase, while some contracts check with the `require(_owner != address(0))` statement, some contracts do not, for consistency in the codebase, specify a single style in such critical definitions.


0 Address check contracts;
```solidity
5 results - 5 files

src/ulysses-amm/UlyssesPool.sol:
  87      ) UlyssesERC4626(_asset, _name, _symbol) {
  88:         require(_owner != address(0));
  89          factory = UlyssesFactory(_factory);

src/ulysses-amm/factories/UlyssesFactory.sol:
  60      constructor(address _owner) {
  61:         require(_owner != address(0), "Owner cannot be 0");
  62          _initializeOwner(_owner);

src/ulysses-omnichain/BranchPort.sol:
  94      constructor(address _owner) {
  95:         require(_owner != address(0), "Owner is zero address");
  96          _initializeOwner(_owner);

src/ulysses-omnichain/RootPort.sol:
  158      function forefeitOwnership(address _owner) external onlyOwner {
  159:         require(_owner != address(0), "Owner cannot be 0 address.");
  160          _setOwner(address(_owner));

src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol:
  69          require(_localPortAddress != address(0), "Port Address cannot be 0");
  70:         require(_owner != address(0), "Owner cannot be 0");
  71 

```



### [Non Critical-21] `DIVISIONER` is inconsistent across contracts


The constant `DIVISIONER` declared in  GovernorBravoDelegateMaia.sol is 1 ether (1e18), but in  BoostAggregator.sol  it is 10000. In  BranchPort.sol it's called 1e4

```solidity
src/governance/GovernorBravoDelegateMaia.sol:
  36:     uint256 public constant DIVISIONER = 1 ether;

src/talos/boost-aggregator/BoostAggregator.sol:
  56:     uint256 private constant DIVISIONER = 10000;

src/ulysses-amm/UlyssesPool.sol:
  65:     uint256 private constant DIVISIONER = 1 ether;

src/ulysses-omnichain/BranchPort.sol:
  91:     uint256 internal constant DIVISIONER = 1e4;

```

Recommendation 
Consider using the same `DIVISIONER` name and value to be more consistent across the codebase.

### [Non Critical-22] The `nonce` architecture of the `delegateBySig()` function isn’t usefull  

The user who needs to use this function must know the next `nonce` value in each operation and add it to the arguments, if he cannot find it, the function will revert, we can probably get this by querying the nonce mapping on the blockchain during the front-end, but this is not an architecturally correct design and seriously consumes resources

As best practice, we can provide practicality by using the design pattern that he uses in many of his projects, you can find the updated code below

```diff
src/erc-20/ERC20MultiVotes.sol:
  362  
  363:     function delegateBySig(address delegatee, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s) public {
  364:         require(block.timestamp <= expiry, "ERC20MultiVotes: signature expired");
+                 uint256 currentValidNonce = _nonces[signer];
  365:         address signer = ecrecover(
  366:             keccak256(
  367:                 abi.encodePacked(
- 368:                     "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
+	               "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, currentValidNonce, expiry))
  369:                 )
  370:             ),
  371:             v,
  372:             r,
  373:             s
  374:         );
- 375:         require(nonce == nonces[signer]++, "ERC20MultiVotes: invalid nonce");
+	 _nonces[signer] = currentValidNonce + 1;
  376:         require(signer != address(0));
  377:         _delegate(signer, delegatee);
  378:     }
```




### [Non Critical-23] Does not `event-emit` during significant parameter changes

The following 3 parameter changes update important states, off-chain apps need to emit emits to track them, add emit to these functions

```solidity
src/talos/boost-aggregator/BoostAggregator.sol:

  143:     function addWhitelistedAddress(address user) external onlyOwner {
  144:         whitelistedAddresses[user] = true;
  145:     }

  148:     function removeWhitelistedAddress(address user) external onlyOwner {
  149:         delete whitelistedAddresses[user];
  150:     }

  153:     function setProtocolFee(uint256 _protocolFee) external onlyOwner {
  154:         if (_protocolFee > DIVISIONER) revert FeeTooHigh();
  155:         protocolFee = _protocolFee;
  156:     }

```
