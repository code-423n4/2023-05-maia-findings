# 🛠️ Analysis - Maia DAO Project 
### Summary
| List |Head |Details|
|:--|:----------------|:------|
|a) |The approach I followed when reviewing the code | Stages in my code review and analysis |
|b) |Analysis of the code base | What is unique? How are the existing patterns used? |
|c) |Test analysis | Test scope of the project and quality of tests |
|d) |Architectural | Architecture feedback |
|e) |Documents  | What is the scope and quality of documentation for Users and Administrators? |
|f) |Centralization risks | How was the risk of centralization handled in the project, what could be alternatives? |
|g) |Systemic risks | Potential systemic risks in the project |
|h) |Competition analysis| What are similar projects? |
|i) |Security Approach of the Project | Audit approach of the Project |
|j) |Other Audit Reports and Automated Findings | What are the previous Audit reports and their analysis |
|k) |Gas Optimization | Gas usage approach of the project and alternative solutions to it |
|l) |Project team | Details about the project team and approach |
|m) |Other recommendations | What is unique? How are the existing patterns used? |
|n) |New insights and learning from this audit | Things learned from the project |
|o) |Summary of Vulnerabilities | Audit Results |
|p) |Time spent on analysis | Time detail of individual stages in my code review and analysis |


## a) The approach I followed when reviewing the code

First, by examining the scope of the code, I determined my code review and analysis strategy.
https://github.com/code-423n4/2023-05-maia#scope

Accordingly, I analyzed and audited the subject in the following steps;

| Number |Stage |Details|Information|
|:--|:----------------|:------|:------|
|1|Compile and Run Test|[Installation](https://github.com/code-423n4/2023-05-maia#install-and-first-build)|Test and installation structure is simple, cleanly designed|
|2|Architecture Review|The [video](https://maiaeco.notion.site/Code4rena-Code-Overview-3f455e66c457427ab3650a73a614641c) explainer provides a high-level overview of the Project system and the [docs](https://v2-docs.maiadao.io/) describe the core components.|Provides a basic architectural teaching for General Architecture|
|3|Graphical Analysis  |Graphical Analysis with [Solidity-metrics](https://github.com/ConsenSys/solidity-metrics)|A visual view has been made to dominate the general structure of the codes of the project.|
|4|Slither Analysis  | [Slither Report](https://github.com/code-423n4/2023-05-maia#slither)|The Slither output did not indicate a direct security risk to us, but at some points it did give some insight into the project's careful scrutiny. These ; 1) Re-entrancy risks |
|5|Test Suits|[Tests](https://github.com/code-423n4/2023-05-maia/tree/main/test)|In this section, the scope and content of the tests of the project are analyzed.|
|6|Manuel Code Review|[Scope](https://github.com/code-423n4/2023-05-maia#scope)|According to the architectural design, the codes were examined starting from the "Hermes, which is the main starting point.|
|7|Project Spearbit Audit |[February Zellic Audit Report - May Zellic Audit Report](https://github.com/code-423n4/2023-05-maia/tree/main/audits)|This reports gives us a clue about the topics that should be considered in the current audit|
|8|Infographic|[Figma](https://www.figma.com/)|I made Visual drawings to understand the hard-to-understand mechanisms|
|9|Special focus on Areas of  Concern|[Areas of Concern](https://github.com/code-423n4/2023-05-maia#areas-of-concern)|The specific focus areas indicated by the project team are the most sensitive and the potential logic-mathematical errors are also focused on.|

## b) Analysis of the code base

Maia is the yield powerhouse of DeFi. With a 100% fair launch via bonds $MAIA is a truly community-owned token. In turn, the Maian ecosystem aims to be a one-stop shop for different DeFi native financial instruments, a fully fledged trading hub currently featuring:

- Maia Decentralized Strategy Vaults
- Hermes Omnichain AMM and YLM (Yield and Liquidity Marketplace)
- TALOS Transparent Automated Liquidity Omnichain Strategies
- Ulysses Omnichain Liquidity Protocol


### The first stage of the audit - Maia Decentralized Strategy Vaults; 
___
- src/maia/PartnerUtilityManager.sol: When implemented, this contract allows for the partner management of bHermes utility tokens.This contract uses the Solmate library for `safeTransfer` operations, a known effect of this library is,Solmate's SafeTransferLib, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following codes will not revert in case the token doesn't exist .

```solidity
src/maia/PartnerUtilityManager.sol:
  100      /// @inheritdoc IPartnerUtilityManager
  101:     function forfeitPartnerGovernance(uint256 amount) public {
  102:         userClaimedPartnerGovernance[msg.sender] -= amount;
  103:         /// @dev partnerGovernance is kept in this contract and not sent to vaults to avoid governance attacks.
  104:         address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);
  105:     }

  158:     function claimPartnerGovernance(uint256 amount) public checkPartnerGovernance(amount) {
  159:         userClaimedPartnerGovernance[msg.sender] += amount;
  160:         address(partnerGovernance).safeTransfer(msg.sender, amount);
  161:     }

```
___

- src/maia/factories/PartnerManagerFactory.sol: This contract is used to manage the list of partners and vaults.
One of the major concerns in the PartnerManagerFactory contract is the dynamic arrays of `partners` and `vaults`. These arrays are a complete gas monster, they consume a lot of gas and can cause gas grief problems.This contract has a central onlyOwner that allows adding and removing `partners` and `vaults`

This topic is mentioned in automatic find
https://gist.github.com/itsmetechjay/09ae039958715d881a47209f74af1b7c#l-2-array-does-not-have-a-pop-function

The architectural preference here may be to use a mapping instead of using an array, thus solving the problems of gas grief in large arrays.

```solidity
src/maia/factories/PartnerManagerFactory.sol:
  19  
  20:     /// @inheritdoc IPartnerManagerFactory
  21:     PartnerManager[] public override partners;
  22: 
  23:     /// @inheritdoc IPartnerManagerFactory
  24:     IBaseVault[] public override vaults;
```
There is a 4 structure in Contract, consisting of 2 dynamic arrays and 2 mappings, this structure can be downloaded to only 2 mappings, only mappings and `PartnerManager` and `IBaseVault` are registered, it can be removed with the `delete` keyword, this structure it will be simpler.

```solidity
src/maia/factories/PartnerManagerFactory.sol:
   19
   20: /// @inheritdoc IPartnerManagerFactory
   21: PartnerManager[] public override partners;
   22:
   23: /// @inheritdoc IPartnerManagerFactory
   24: IBaseVault[] public override vaults;
   25:
   26:/// @inheritdoc IPartnerManagerFactory
   27: mapping(PartnerManager => uint256) public override partnerIds;
   28:
   29: /// @inheritdoc IPartnerManagerFactory
   30: mapping(IBaseVault => uint256) public override vaultIds;
```
___

- src/maia/tokens/Maia.sol: ERC20 representing a share of the Maia ecosystem.As detailed in the centrality risk section, the mint function is only performed by OnlyOwner, a high centrality risk is observed.

___
- src/maia/tokens/ERC4626PartnerManager.sol: Partner Manager is an ERC-4626 compliant Partner token which: distributes bHermes utility tokens (Weight, Boost, Governance) in exchange for staking Partner tokens.

___
- src/maia/libraries/DateTimeLib.sol: Library for date time operations. To get the current month and check if it is the first Tuesday of the month.

___




### The second stage of the audit - Hermes Omnichain AMM and YLM (Yield and Liquidity Marketplace); 
The most important contract in the control of the Hermes project is [BaseV2Minter](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol)

Because [ updatePeriod()](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L123-L155) function produces complex and important logic, this function is examined in more detail in the audit

### The third stage of the audit - TALOS Transparent Automated Liquidity Omnichain Strategies;

Talos is decentralized Uniswap V3 Liquidity Management protocol , so a basic knowledge of Uniswap v3 is required to understand the codes of this protocol

Uniswap v3 Detailed explanation
https://uniswapv3book.com/docs/introduction/introduction-to-markets/

Understanding Uniswap v3 Mathematics
https://atiselsts.github.io/pdfs/uniswap-v3-liquidity-math.pdf

### The fourth stage of the audit- Ulysses Omnichain Liquidity Protocol ;

Ulysses Protocol is a decentralized and permissionless community owned 'Omnichain Liquidity Protocol' designed to promote capital efficiency in the face of an ever more fragmented liquidity landscape in DeFi. Enabling Liquidity Providers to deploy their assets from one chain and earn revenue from activity in an array of chains all while mitigating the negative effects of current market solutions. In addition, offering a way for DeFi protocols to lower their operational and managing costs by incentivizing liquidity for a single unified liquidity token instead of managing incentives for pools scattered accross different AMMs and chains.

It is important to understand two separate parts in the control of the Ulysses Protocol: `ulyssess-amm` and `ulyssess-omnichain`

`ulyssess-amm` : It has an architecture that works like a classic AMM consisting of Pool, Router and Token

`ulyssess-omnichain` : This part of Ulysses does the most critical tasks, namely `omnichain` functions, here it is necessary to know the concept of omnichain,

What is Omnichain then?
Omnichain is the principal of a protocol offering the ease of use of transfering hustle-free assets between chains. In order to transfer funds from Ethereum to Solana or vice-versa, for example, you needed to either use a CEx (centralized exchange) or a bridging protocol like Wormhole with high fees and long waiting queues.


<img width="1312" alt="image" src="https://github.com/code-423n4/2023-05-maia/assets/104318932/820bc7af-ce7c-4da7-8d32-3db3e79c57ec">

Ulysses Omnichain Liquidity System uses AnycallV7 (https://github.com/anyswap/multichain-smart-contracts/tree/main/contracts/anycall/v7) for cross-chain messaging, it is integrated using the Pay on Destination flag (0x06), meaning execution gas fees are credited to the recipient contract (Bridge Agent) deducting the gas spent from this contract's executionBudget kept in the AnycallConfig contract (https://docs.multichain.org/developer-guide/anycall-v7/estimate-fee-pay-fees-on-destination-chain)

`ulyssess-amm` : Before analyzing the codes, it is necessary to know the general structure, how is the interaction between contracts in general, we can look at this infographic for this.

<img width="1110" alt="image" src="https://github.com/code-423n4/2023-05-chainlink-findings/assets/104318932/6fe20ec1-84e7-4831-a0eb-204d1f701ae5">

### ERC4626 ;
ERC4626 uses a modified version of solmate's ERC4626 implementation, but the documentation does not specify which version or commit is used. This indicates that the protocol does not track upstream changes in contracts used as dependencies. Therefore, contracts may not reflect updates or security fixes implemented in their dependencies, as these updates need to be manually integrated.

```solidity
src/erc-4626/ERC4626.sol:
  10  
  11: /// @title Minimal ERC4626 tokenized Vault implementation
  12: /// @author Solmate (https://github.com/transmissions11/solmate/blob/main/src/mixins/ERC4626.sol)
  13: abstract contract ERC4626 is ERC20, IERC4626 {

```

Review the codebase and document the source and version of each dependency. Include third-party sources as modules in the project to maintain path consistency and ensure dependencies are updated periodically.


## c) Test analysis
What did the project do differently? ;
The project, while designing the tests, modeled it with the "Threat Model", wrote the tests and documented it and presented it to the auditors, which increases the auditability and gives us the project team's view of the threat models specifically for the project.

What could they have done better?;
It seems that the scope of the test has not been determined. Failure to calculate test coverage can cause very small details to be overlooked in such large projects

```js
README.md:
  788: - What is the overall line coverage percentage provided by your tests?:  Can't provide accurate information, so 0
```

Test coverage is generally of good quality and based on Unit Tests, more Integration Tests can be added

We see that the project team allocates the testing time and energy to parts with high potential, such as ERC4626, not to parts such as ERC20, where there will be no major potential problems.
I would like to exemplify this;

Let's take a look at the test of ERC20's `burn()` function, it's very simple and short.
- The `testBurn()` test function tests whether an address `burn` a minted token
- `testBurnFailed()` tests whether an address cannot `burn` a non-minting token
the simple of the simple 

```solidity
   test/erc-20/ERC20BoostTest.t.sol:
  134  
  135:     function testBurn() public {
  136:         token.mint(address(1), 100 ether);
  137:         hevm.prank(address(1));
  138:         token.burn(address(1), 100 ether);
  139:         assertEq(token.balanceOf(address(1)), 0);
  140:     }
  141: 
  142:     function testBurnFailed() public {
  143:         testAttach();
  144:         hevm.expectRevert(IERC20Boost.AttachedBoost.selector);
  145:         hevm.prank(address(1));
  146:         token.burn(address(1), 100 ether);
  147:     }
```


On the other hand, we see that the test of the `MultipleMintDeposit` structure of ERC4626 is discussed in great detail, it has been seen that the following logic has been tested in the test contract;


```solidity
 // Scenario:
        // A = Alice, B = Bob
        //  ________________________________________________________
        // | Vault shares | A share | A assets | B share | B assets |
        // |========================================================|
        // | 1. Alice mints 2000 shares (costs 2000 tokens)         |
        // |--------------|---------|----------|---------|----------|
        // |         2000 |    2000 |     2000 |       0 |        0 |
        // |--------------|---------|----------|---------|----------|
        // | 2. Bob deposits 4000 tokens (mints 4000 shares)        |
        // |--------------|---------|----------|---------|----------|
        // |         6000 |    2000 |     2000 |    4000 |     4000 |
        // |--------------|---------|----------|---------|----------|
        // | 3. Vault mutates by +3000 tokens...                    |
        // |    (simulated yield returned from strategy)...         |
        // |--------------|---------|----------|---------|----------|
        // |         6000 |    2000 |     3000 |    4000 |     6000 |
        // |--------------|---------|----------|---------|----------|
        // | 4. Alice deposits 2000 tokens (mints 1333 shares)      |
        // |--------------|---------|----------|---------|----------|
        // |         7333 |    3333 |     4999 |    4000 |     6000 |
        // |--------------|---------|----------|---------|----------|
        // | 5. Bob mints 2000 shares (costs 3001 assets)           |
        // |    NOTE: Bob's assets spent got rounded up             |
        // |    NOTE: Alice's vault assets got rounded up           |
        // |--------------|---------|----------|---------|----------|
        // |         9333 |    3333 |     5000 |    6000 |     9000 |
        // |--------------|---------|----------|---------|----------|
        // | 6. Vault mutates by +3000 tokens...                    |
        // |    (simulated yield returned from strategy)            |
        // |    NOTE: Vault holds 17001 tokens, but sum of          |
        // |          assetsOf() is 17000.                          |
        // |--------------|---------|----------|---------|----------|
        // |         9333 |    3333 |     6071 |    6000 |    10929 |
        // |______________|_________|__________|_________|__________|

```







## d) Architectural 



- Hermes Omnichain AMM and YLM (Yield and Liquidity Marketplace)


- TALOS Transparent Automated Liquidity Omnichain Strategies



- Maia Decentralized Strategy Vaults
Maia token utilized OHM's Staking & Bonding Mechanics during the initial deployment phase. Emissions were redistributed through the base and capped by the $180,000 MAIA supply.
[What is the OHM's Staking & Bonding Mechanics](https://www.youtube.com/watch?v=nIwDU9L9KYU)


- Ulysses Omnichain Liquidity Protocol
Ulysses Protocol has two major components, the Virtualized and the Unified Liquidity Management. The latter being inspired by the Delta Algorithm put forward by Layer 0;
https://www.dropbox.com/s/gf3606jedromp61/Delta-Solving.The.Bridging-Trilemma.pdf?dl=0

Ulyssess: It has two separate concepts as Virtualization and Unified
Virtualization: Allows the use of the same unit token across different networks
Unified: Allows merging the same volume tokens from different networks in the same ERC4626 vault
It uses Anyswap in `annyCall v7` to use these two architectures;
https://docs.multichain.org/developer-guide/anycall-v7

Other architectural correct design in the project; `event` and `error`s are defined in interfaces.

## e) Documents 


### - Philosophy - The Myth of Maia
The eldest of the seven nymphs that make up the constellation of Pleiades, Daughter of Atlas, who carries the world’s weight on his shoulders and mother of Hermes, messenger of the gods.
Spending most her life alone inside a cave in the peak of Mount Kyllene, not much is known about Maia other than she was a shy Earth goddess, devoting her life to nurture and whose very name translated to “Nursing Mother” the embodiment of growth! 🌿


### -Video explanation of the codes of the Project;
https://maiaeco.notion.site/Code4rena-Code-Overview-3f455e66c457427ab3650a73a614641c#00dbbf98f5624342a1273a6c706112b6
Video narration of the code of the project is effective, especially for auditors who want visual learning.



### -Project details;
https://v2-docs.maiadao.io/
In the documentation, the link of the [Hermes-Twitter](]https://twitter.com/HermesOminchain) account is broken at the bottom of the page, apart from that, the documents summarize the project in general terms.



### -Hermes
Hermes provides significant improvements over V1, which is a soldily fork. There are three ideas behind this model:
Lockers direct emissions to gauges and receive the revenue of each gauge they vote for, proportional to the amount of votes for that gauge
Lockers can boost their liquidity farming rewards from gauges by up to 2.5x
Lockers receive rebases weekly proportional to inflation
One of the improvements made is that instead of veNFTs that are locked for 4 years, every lock is made permanent. Claiming rebases was also substituted with a deposit only ERC4626 tokenized vault, which instead of claiming rebases, the burn (permanent lock) rate is increased. This allows users only to vote once and remove unecessary weekly tasks to optimize their funds.
Hermes also introduced a new gauge system, that accepts any kind of yield. The first gauges to be deployed are the Uniswap V3 gauges, which allow users to stake their NFTs and receive rewards. The gauges also allow users to boost their rewards up to 2.5x.

### -Maia
Maia is the first implementation of the Partner bHermes Vault. It allows users to stake their Maia converting it to vMaia and receive two of the three bHermes utilities deposited in the vault. The utilities are: weight and governance. The third utility is boost, which is not claimable by users, but instead used by Maia's Treasury to host a boost aggregator with Talos Positions to enable further accumulation of hermes.

### -Talos
Talos is decentralized Uniswap V3 Liquidity Management protocol. It allows anyone to create and manage new LPs. These LPs always start 50/50 and if they have a manager, it can call two strategies: rebalancing and reranging. Talos LPs are Uni V3 NFT wrappers, while this is less gas efficient, it allows for easier integrations with other protocols, like staking in Uniswap V3 gauges.
Staked Talos Positions need to be attached to a boost aggregator, anyone can deploy one using Boost Aggregator Factory. This allows users to pool together and share the same boost.

### - Ulysses
Ulysses is devided in two separate concepts: Virtualized and Unified Liquidity. Virtualized liquidity is made possible by using anycall v7 as our messaging layer and means that an asset deposited from a specific chain, is recognized as a different asset from the "same" asset but from a different chain (ex: arb ETH is different from mainnet ETH). Unified Liquidity then unifies these tokens using a stableswap AMM and then depositing them in a Unified Liquidity Token, which is a Multi-Asset ERC4626 tokenized vault. This allows users to deposit any asset from any chain and receive a 1:1 representation of the underlying assets. These Unified Liquidity Tokens can then be used in any other protocol, like Uniswap v3.

All Maia Dao Projects-Documents Links;
[Twitter - MaiaDAOEco](https://twitter.com/MaiaDAOEco)
[Twitter - HermesOmnichain](https://twitter.com/HermesOmnichain)
[Discord - Maia DAO Ecosystem](https://discord.com/invite/gCggeM65y2)
[Telegram - Maia DAO ](https://t.me/maiadao)
[Medium - Maia DAO](https://medium.com/@maiaDAO)
[Website - Maia DAO](https://maiadao.io/#/stake)
[Website - Hermes](https://hermes.maiadao.io/#/swap)
[Maia Dao Documents ](https://docs.maiadao.xyz/)
[Hermes Documents](https://docs.maiadao.io/)
[Governance - Proposal Maia DAO](https://snapshot.org/#/maiadao.eth)


##  f) Centralization risks 
- DAO Mechanism

It is observed that the DAO proposals of the project are voted by a small number of people, for example this can be seen in the proposal below.As the project is new, this is normal in DAO ecosystems, but the centrality risk should be expected to decrease over time;

https://snapshot.org/#/maiadao.eth/proposal/0x38d9ffaa0641eb494c20d2b034df321a6865bf8859487468e1583dd095837488

Centralization risk in the DOA mechanism is that the people who can submit proposals must be on the whitelist, which is contrary to the essence of the DAO as it carries the risk of a user not being able to submit a proposal in the DAO even if he has a very high stake.

We should point out that this problem is beyond the centrality risk and is contrary to the functioning of the DAO. Because a user who has a governance token to be active in the DAO may not be able to bid if he is not included in the whitelist (there is no information in the documents about whether there is a warning that he cannot make a proposal if he is not on the whitelist)

There is no information on how, under what conditions and by whom the While List will be taken.
1- In the short term; Clarify the whitelist terms and processes and add them to the documents, and inform the users as a front-end warning in Governance token purchases.
2- In the Long Term; In accordance with the philosophy of the DAO, ensure that a proposal can be made according to the share weight.


- Maia -Hermes Token

Minting of Maia and Hermes token, which are one of the foundations of the project, shows us the direct centralization, as the EOA account is not protected with Multi-Sign, details should be given to reduce the centrality : 
```solidity
src/maia/tokens/Maia.sol:
  39   */
  40: contract Maia is ERC20, Ownable {
  41:     constructor(address _owner) ERC20("Maia", "MAIA", 18) {
  42:         _initializeOwner(_owner);
  43:     }
  44: 
  45:     /*///////////////////////////////////////////////////////////////
  46:                         ERC20 LOGIC
  47:     //////////////////////////////////////////////////////////////*/
  48: 
  49:     /**
  50:      * @notice Responsible for minting new hermes tokens.
  51:      * @dev Checks if the sender is an allowed minter.
  52:      * @param account account to mint tokens to.
  53:      * @param amount amount of hermes to mint.
  54:      */
  55:     function mint(address account, uint256 amount) external onlyOwner {
  56:         _mint(account, amount);
  57:     }
  58: }

```
##  g) Systemic risks 
According to the nformation of the project, it is stated that it can be used in rollup chains and popular EVM chains.

```js
README.md:
  797  - Is it a fork of a popular project?:   true
  798: - Does it use rollups?:   true
  799: - Is it multi-chain?:  true
  800: - Does it use a side-chain?: true
```
We can talk about a systemic risk here because there are some Layer2 special cases.
Some conventions in the project are set to Pragma ^0.8.19, allowing the conventions to be compiled with any 0.8.x compiler. The problem with this is that Arbitrum is [Compatible](https://developer.arbitrum.io/solidity-support) with 0.8.20 and newer. Contracts compiled with these versions will result in a non-functional or potentially damaged version that does not behave as expected. The default behavior of the compiler will be to use the latest version which means it will compile with version 0.8.20 which will produce broken code by default.

## h) Competition analysis
There is no such onchain governance organization project represented by a non-transferable token.


## i) Security Approach of the Project

Successful current security understanding of the project;
1 - First they did the main audit from a reputable auditing organization like Zellic  and resolved all the security concerns in the report
2- They manage the 2nd audit process with an innovative audit such as Code4rena, in which many auditors examine the codes.

What the project should add in the understanding of Security;
1- By distributing the project to testnets, ensuring that the audits are carried out in onchain audit. (This will increase coverage)
2- After the project is published on the mainnet, there should be emergency action plans (not found in the documents)

## j) Other Audit Reports and Automated Findings 

**Automated Findings:**
https://gist.github.com/itsmetechjay/09ae039958715d881a47209f74af1b7c
Automatic findings have material misinformation


[L1-Automatic Finding-Contracts are vulnerable to fee-on-transfer token related accounting issues
](https://gist.github.com/itsmetechjay/09ae039958715d881a47209f74af1b7c#l-1-contracts-are-vulnerable-to-fee-on-transfer-token-related-accounting-issues)

This finding is an invalid finding already stated by the project team;

```js
README.md:
  25  There are a couple of known issues that are intended and not in scope for this audit:
  26: - Rebases and Fee-On-Transfer tokens are not supported. 
```


**Other Audit Reports (Zellic):**
https://github.com/code-423n4/2023-05-maia/tree/main/audits

While investigating the potential risks in the project, the past audit reports can give us serious insights, so they should be taken into account and analyzed in detail. 

## Zellic Audit Report - Maia DAO
| Vulnerability |Risk Level |Description |Category|Remediation|
|:--|:----------------|:------|:------|:------|
| 3.1|Critical Risk |Missing access control on ``addGaugetoFlywheel``    | Coding Mistakes   | OK✓ |
| 3.2|Critical Risk |  Missing transfer hook in ``TalosStrategyStaked``  | Coding Mistakes   | OK✓ |
| 3.3|Critical Risk |  Lack of validation when creating a Talos staked strategy | Coding Mistakes   | OK✓ |
| 3.4|High|  Reentrancy when incrementing and decrementing gauges  | Coding Mistakes   | OK✓ |
| 3.5|High | Incorrect calculation of maximum-allowed mint  | Coding Mistakes   | OK✓ |
| 3.6|High |  Incorrect total gauge weight calculation  | Coding Mistakes   | OK✓ |
| 3.7|High |  Protocol fee calculation is reversed  | Coding Mistakes   | OK✓ |
| 3.8|Low |  Lack of verification when staking NFT  | Coding Mistakes   | OK✓ |
| 3.9|Medium |  Lack of slippage protection | Business Logic  | OK✓ |
| 3.10|Medium | Potential loss of weekly emissions  | Coding Mistakes   | OK✓ |
| 3.11|Medium |  Lack of updating the ``getUserBoost``  | Coding Mistakes   | OK✓ |
| 3.12|Low | Erroneous full value reset of ``_delegatesVotesCount``  | Coding Mistakes   | OK✓ |
| 3.13|Low |  Lack of deleting a gauge from the ``getUserGaugeBoost``  | Coding Mistakes   | OK✓ |
| 3.14|Low | Incorrect initial optimizer ID  | Coding Mistakes   | OK✓ |
| 3.15|Low |  Lack of input validation  |Business Logic  | OK✓ |


## Zellic Audit Report - Ulysses Protocol
| Vulnerability |Risk Level |Description |Category|Remediation|
|:--|:----------------|:------|:------|:------|
| 3.1|Critical Risk |Lack of the RootBridgeAgent contract verification   | Coding Mistakes   | OK✓ |
| 3.2|Critical Risk |  Missing access control for Multicall | Coding Mistakes   | OK✓ |
| 3.3|Critical Risk |  Multitoken lacks validations | Coding Mistakes   | OK✓ |
| 3.4|Critical Risk|  Multiple redeems of the same deposit are possible  | Coding Mistakes   | OK✓ |
| 3.5|Critical Risk | Broken fee sweep  | Coding Mistakes   | OK✓ |
| 3.6|High | Asset removal is broken | Coding Mistakes   | OK✓ |
| 3.7|High | Unsupported function codes  | Coding Mistakes   | OK✓ |
| 3.8|High |  Missing access control on ``anyExecuteNoSettlement``  | Coding Mistakes   | OK✓ |
| 3.9|High|  The protocol fee from pools will be claimed to zero address| Coding Mistakes  | OK✓ |
| 3.10|High | Unlimited cross-chain asset transfer without deposit requirement | Coding Mistakes   | OK✓ |
| 3.11|Medium |  ChainId type confusion  | Coding Mistakes   | OK✓ |
| 3.12|Medium| Incorrect accounting of total and strategies debt  | Coding Mistakes   | OK✓ |
| 3.13|Medium |  Bridging assets erroneously mints new assets | Coding Mistakes   | OK✓ |
| 3.14|Low | Lack of input validation  | Coding Mistakes   | OK✓ |
| 3.15|Informational |  Lack of new owner address check |Coding Mistakes  | OK✓ |
| 3.16|Informational |  Addresses may accidentally be overwritten |Coding Mistakes  | OK✓ |
| 3.17|Informational | Ownable contracts allow renouncing |Coding Mistakes  | OK✓ |
| 3.18|Informational | Lack of validation in UlyssesFactory |Coding Mistakes  | OK✓ |



## k) Gas Optimization
The project is generally efficient in terms of gas optimizations, many generally accepted gas optimizations have been implemented, gas optimizations with minor effects are already mentioned in automatic finding, but gas optimizations will not be a priority considering code readability and code base size

##  l) Project team
Information not available

## m) Other recommendations

- src/governance/GovernorBravoDelegateMaia.sol : `propose()` function used to propose a new proposal , Sender must have delegates above the proposal threshold.This functin is very critical because it builds an important task where DAO proposals are given, however it should be tightly controlled for a recent security concern, the proposal mechanism in the DAO must have limits, not everyone can read the code in proposal evaluation, the following hack is done using exactly this function, each proposal in It may even need to pass a minor inspection.

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

- src/maia/tokens/ERC4626PartnerManager.sol: The `ERC4626PartnerManager.migratePartnerVault()` function defines the new vault contract in case vaults are migrated.

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

To mitigate this risk, you should implement appropriate measures to handle user funds during migration. This could involve implementing mechanisms such as time-limited withdrawal periods or providing clear instructions and notifications to users about the migration process, ensuring they have the opportunity to withdraw their funds from the old vault before the migration occurs.

- src/hermes/UtilityManager.sol: In Contract, 3 modifiers are empty  `checkWeight` , `checkBoost` , `checkGovernance`  and are not used, there is no information in NatSpec comments about this, such designs can cause confusion

<img width="478" alt="image" src="https://github.com/code-423n4/2023-05-maia/assets/104318932/13ce92d5-13f8-48fb-a3d4-3f2b28a4a29a">

- Document : Infographics related to critical contracts and architectures in the project in terms of control and intelligibility can be used,this makes it easier to both understand and control the project, as an example I have prepared a simple infographic of `Hermes.UtilityManager.sol` in the project.
<img width="1554" alt="image" src="https://github.com/code-423n4/2023-05-maia/assets/104318932/b55685b1-51d3-4eb8-b005-611117a42fa1">

- OnlyOwner initialize : The `onlyOwner` modifier is used to load many contracts and initialize and initialize states in the project, the reason for using it is that it is not initialized by unauthorized persons, in these contracts the `onlyOwner` modifier is not used anywhere other than the initialize or constructor, below is an example from the project, this Architectural decision may be too waste of resources for both gas optimization and security (we import ownable.sol for a single place), instead we can provide this security in the Deploy script and set the constructor and initialize arguments securely in deploy atomically in all contracts

```solidity
src/ulysses-omnichain/BaseBranchRouter.sol:
  27  
  28:     constructor() {
  29:         _initializeOwner(msg.sender);
  30:     }
  31: 
  32:     /*///////////////////////////////////////////////////////////////
  33:                         OWNER FUNCTIONS
  34:     //////////////////////////////////////////////////////////////*/
  35: 
  36:     /// @notice Contract state initialization function.
  37:     function initialize(address _localBridgeAgentAddress) external onlyOwner {
  38:         require(_localBridgeAgentAddress != address(0), "Bridge Agent address cannot be 0");
  39:         localBridgeAgentAddress = _localBridgeAgentAddress;
  40:         bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress();
  41:         renounceOwnership();
  42:     }
```

As an example of secure deploy by owner, I can show the Deploy script in ArtGobbler project, even with `computeAddress`, addresses of all contracts before deploy can be calculated and atomically all contracts and initializes can be deployed and set at the same time, so onlyOwner controls can be removed from constructors and initializes

```solidity
2022-09-artgobblers/script/deploy/DeployBase.s.sol:
  60  
  61:     function run() external {
  62:         vm.startBroadcast();
  63: 
  64:         // Precomputed contract addresses, based on contract deploy nonces.
  65:         // tx.origin is the address who will actually broadcast the contract creations below.
  66:         address gobblerAddress = LibRLP.computeAddress(tx.origin, vm.getNonce(tx.origin) + 4);
  67:         address pageAddress = LibRLP.computeAddress(tx.origin, vm.getNonce(tx.origin) + 5);
  68: 
  69:         // Deploy team and community reserves, owned by cold wallet.
  70:         teamReserve = new GobblerReserve(ArtGobblers(gobblerAddress), teamColdWallet);
  71:         communityReserve = new GobblerReserve(ArtGobblers(gobblerAddress), teamColdWallet);
  72:         randProvider = new ChainlinkV1RandProvider(ArtGobblers(gobblerAddress),vrfCoordinator,linkToken,chainlinkKeyHash,chainlinkFee);
  73: 
  74:         // Deploy goo contract.
  75:         goo = new Goo();
  76: 
  77:         // Deploy gobblers contract,
  78:         artGobblers = new ArtGobblers();
  79: 
  80:         // Deploy pages contract.
  81:         pages = new Pages(mintStart, goo, teamColdWallet, artGobblers, pagesBaseUri);
  82: 
  83:         vm.stopBroadcast();
  84:     }
  85: }
```

## n) New insights and learning from this audit 
1- I learned how a 4-fold utility tokens  structure `Weight`, `Boost`, `Governance` and `PartnerGovernance` is used effectively in a project.

2- We are familiar with DAOs and proposals before, I learned the whitelist structure and the proposal process for this project.

3- In ERC4626 Vault,  I saw and learned the `migrate()` process for the first time in this project.

4- I learned the concept of `Virtualized liquidity` and the design of using anycall v7 . 

5-I learned about the idea of combining tokens from different networks with Unified Liquidity and depositing them in ERC4626 vaults, this is a revolutionary architecture that will result in incredible capital efficiency

6- There were plenty of examples for me to learn the details of StabelSwap AMM swaps.

## o) Summary of Vulnerabilities 

The list and details of all the results that I analyzed the project and shared in Code4rena are shared separately.

 Audit Results
- [x] **Medium  01** → No slippage control on deposit of `PoolActions.rerange`
- [x] **Medium  02** → Anyone can create a Pool by directly calling the factory and may cause lost money to users
- [x] **Medium  03** → Missing deadline checks allow pending transactions to be maliciously executed
- [x] **High  04** → Route[] array size in `swap()` function is underflow due to no 0 check
- [x] **High  05** → Modifier of `claimPartnerGovernance()` does not check balance of msg.sender
- [x] **Low 06** → There may be problems with the use of Layer2
- [x] **Low 07** → Head Overflow Bug in Calldata Tuple ABI-Reencoding
- [x] **Low 08** → There is a risk that a user with a high governance power will not be able to bid with `propose()`
- [x] **Low 09** → Migrating with "migratePartnerVault()" may result in loss of user funds
- [x] **Low 10** → Malicious user can create pool with poison ERC20 contract
- [x] **Low 11** → Project Upgrade and Stop Scenario should be
- [x] **Low 12**→ Project has  security risk from DAO attack using proposal
- [x] **Low 13**→ First ERC4626 deposit exploit can break share calculation
- [x] **Low 14**→ Missing Event for  initialize
- [x] **Low 15**→ Missing maxwithdraw check in withdraw function of ERC-4626
- [x] **Low 16**→ `gaugeWeight` , `gaugeBoost` , `governance` contracts must have the same addresses in Hermes and Maia, this has no control
- [x] **Low 17**→ Processing of `poolId` and `tokenId` incorrectly starts with 2 instead of 1
- [x] **Low 18**→ If onlyOwner runs `renounceOwnership()` in the `PartnerManagerFactory` contract, the contract may become unavailable
- [x] **Low 19**→ There isnt skim function
- [x] **Low 20**→ Contract (ERC4626.sol) used as dependency does not track upstream changes
- [x] **Low 21**→ Use ERC-5143: Slippage Protection for Tokenized Vault
- [x] **Non-Critical  22**→ Unused Import
- [x] **Non-Critical  23**→ Assembly Codes Specific – Should Have Comments
- [x] **Non-Critical  24**→ Using a `modifier` with no meaning can cause confusion
- [x] **Non-Critical  25**→ With 0 address control of `owner`, it is best practice to maintain consistency across the entire codebase.
- [x] **Non-Critical  26**→ `DIVISIONER` is inconsistent across contracts
- [x] **Non-Critical  27**→ The `nonce` architecture of the `delegateBySig()` function isn’t usefull  
- [x] **Non-Critical  28**→ Does not `event-emit` during significant parameter changes

## p) Time spent on analysis
I allocated about 5 days to the project, most of this time was spent with architectural examination and analysis.

### Time spent:
35 hours