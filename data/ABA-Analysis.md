# Analysis of the MaiaDAO ecosystem codebase

## Codebase overview

Maia DAO Ecosystem [codebase](https://github.com/code-423n4/2023-05-maia/tree/main/src) is separated into the following directories:
```
├───erc-20
├───erc-4626
├───gauges
├───governance
├───hermes
├───maia
├───rewards
├───talos
├───ulysses-amm
├───ulysses-omnichain
└───uni-v3-staker
```
An observation for new developers or auditors, some contracts are grouped on _functionality similarity_ others on _component of which they belong to_ which can prove a bit difficult to follow at first

**Grouped based on similar functionality**
- erc-20
- erc-4626
- gauges
- governance
- rewards
- uni-v3-staker

**Grouped based on belonging project**
- hermes
- maia
- talos
- ulysses-amm
- ulysses-omnichain

Those grouped by project often use common code from the other directories. In particular `erc-20`, `erc-4626` and `rewards` are heavily used.

**Other codebase observations**
- contracts and variables need better naming as in some cases the name has nothing to do with the functionality. Example: [FlywheelBoosterGaugeWeight](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol) does not actually boost anything, it stores the user gauge related balances
- it would be better to group all code by component where possible, instead of by name
- code documentation is at a minimum accepted level and can always improve; the same with project testing
- nice ASCII art on some contracts!


## Patterns and reused code

Several MaiaDAO components are actually extensions, forks or inspired by existing projects. In total MaiaDAO borrows code from at least 3 different major projects:
- **Hermes** is a [Solidly](http://web.archive.org/web/20220422051938/https://github.com/solidlyexchange/solidly) fork
- **Talos** is based on [popsicle.finance](https://github.com/Popsicle-Finance)
- **Flywheel reward logic** is inspired by [Tribe DAO Contracts](https://github.com/fei-protocol/flywheel-v2)

and a few smaller code components are borrowed from (or inspired by) popular libraries such as:
- **ERC4626 vaults** implementation is extended from [Solmate](https://github.com/transmissions11/solmate/blob/main/src/mixins/ERC4626.sol)
- **UniswapV3Staker** (uni-v3-staker) is a modified implementation of the original [UniswapV3Staker](https://github.com/Uniswap/v3-staker/blob/main/contracts/UniswapV3Staker.sol)
- **vMAIA DateTimeLib** utility is a stripped down version of [Solady](https://github.com/vectorized/solady/blob/main/src/utils/DateTimeLib.sol)'s DateTimeLib

## Project unique attributes

From a unique point of view (at least subjectively to the author of this description) MaiaDAO codebase:
- manages to combine a large number of complex and complicated components into one, this is a feat into itself
- uses extensive, _and I do mean extensive_, use of factories for almost every logic contract there is (there are in total 20 different factory contracts)
- uses peculiar time constraints; for example, `vMAIA` unstaking can happen only on the first Tuesday of the month, why not on the first Friday? or Saturday?

## Learning resources

In order to help any future auditor or developer of the project, this section was created to provide resources (or aggregate existing resources) in that direction

- A [high level description of each project contract Solidity file](https://github.com/code-423n4/2023-05-maia#contracts) was provided by the MaiaDAO team. 

- The [code4ena MaiaDAO documentation](https://github.com/code-423n4/2023-05-maia#overview) also aggregates all team provided resources up to this point and can serve as a good initial read for people wishing to understand the system as a whole

For future auditors of the project, the following advice may be of help:
- when staring an audit, with regards to Hermes, start from either rewards logic ([FlywheelCore](https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol)) or Hermes Minter ([BaseV2Minter](https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol)) and work your way from there
 - read the project documentation and attempt to make a summary specifically for your usage or understanding
 - after going through the documentation ask yourself questions about assumptions that you read
- make a high level component interaction diagram. Recommended tool: [excalidraw](https://excalidraw.com/), [miro](https://miro.com/) or [draw.io](https://app.diagrams.net/)


The following link: [**ABA MaiaDAO overview**](https://ethereal-net-2b6.notion.site/ABA-MaiaDAO-overview-bc57e0924a3745b48ef2f6b5f61bdb3a),
 is this authors's own attempt to summaries MaiaDAOs documentation. At the end of the article you will find sections helpful for future auditors:
- **Questions to answer when auditing the codebase**: after reading the documentation, what type of questions, as an auditor should you ask yourself
- **Containing components and their respective audits**: a section with information about projects from which code was borrowed (or is used) and their respective audits. It is possible some issue may reappear or you can use POCs or the mental model the auditors have conveyed in the audit


# Architecture feedback

## Current Architecture

- The architecture in itself is complicated and at places also complex. It tries to follow the 4 major components (protocols) of the MaiaDAO ecosystem: Maia, Hermes, Talos and Ulysses

- Extensive usage of factories also add an extra layer of complexity but that is unfortunately unavoidable due to the nature of the project.

- Rewards logic has at its core the bHermes token, which presents an interesting multi-use scenarios for the ERC20 token. 

- Project also uses the [ve(3,3) model](https://andrecronje.medium.com/ve-3-3-44466eaa088b) as an observation

The following link shows a partial architecture diagram that contains the rewards logic for Hermes and UniswapV3Staker gauge logic:
**https://link.excalidraw.com/readonly/JnTC9HB2yLadiFxKzlwA?darkMode=true**

## Future considerations

- Some issues with the current architecture are regarding the removal of an element. Removals are not entirely atomic, several different "remove/disable" actions need to be done separately across the protocol to fully remove a component from it. Consider making a more thorough and documented removal system.

- in case of a migration to a V3 (similar to how now the project is migrating to a V2), current architecture and V2 implementation overall does not take this into consideration. If this is not properly treated it can cause project problems in the future. Example, with current implementation you cannot stop the UniswapV3 Staker or migrate th

- not necessarily an architectural issue, but project uses `Solmate ERC20` implementation which [allows for transfer to 0 address](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC20.sol#L76-L110). This may have negative repercussions in the future. As a suggestion, add checks that no transfers can go to zero address or user OpenZeppelin's ERC20 implementation.

# Centralization risks

- Trust assumptions need to be respected for the project to survive. A malicious (or compromised) ownership can remove liquidity from almost the entire project. This can be done either by adding malicious strategies (Gauges) to existing project reward components or by adding malicious reward tokens.

- The following statement may be controversial: due to the highly complicated nature of the project, even it anyone can kickoff system critical and time dependent actions on-chain (no access control limitations), these are still mostly done by the team. An example is queuing weekly pending bHermes rewards. In that respect, if the team does not setup proper automation then protocol health may plummet and rewards may be lost.

# Systemic risks

- MaiaDAO has one of the most prolific development team that the author has seen. That being said, a major issue lies in the fact that the core developers is a team of 3. This results in a [project bus factor of 3](https://en.wikipedia.org/wiki/Bus_factor). If anything happens to one of the team members, development will be drastically stalled or even project may be closed down. Add more team members.


- MaiaDAO uses Multichain's [`Anycall`](https://docs.multichain.org/developer-guide/anycall-v7) implementation for inter-chain communication. Recent events regarding the [Multichain team being arrested](https://twitter.com/BoringSleuth/status/1661399578313211905) and having needed to [shutdown a part of their supported bridges](https://twitter.com/MultichainOrg/status/1663941611380965376) raises concerns regarding the future of the protocol. It is best to also explore alternatives for `Anycall`.

- From a token/liquidity point of view, the protocol does have any more of a system risk that other DeFi products. Its spread to V3 Uniswap liquidity mining, staking, and liquidity marketplaces gives the project a better changes of surviving any black swan type events that may occur.


# Other recommendations

### **Protocol needs more developers**

The rate of code development outpaces the security insight and mental attention to details that the team can handle. MaiaDAO developers have proven incredibly good developers but growing the system, in a relatively short time span, with such a small team has meant that fine details are missed out. This can be seen even by the presence of typos in the code that, extending on the [broken windows theory](https://en.wikipedia.org/wiki/Broken_windows_theory), also implies hidden code smells and issues. 

A more evident example is the simple _protocol fee calculation reversal_ that was seen [in a previous audit](https://github.com/Maia-DAO/maia-ecosystem-monorepo/blob/1-audit/audits/Maia%20DAO%20February%202023%20-%20Zellic%20Audit%20Report.pdf). This type of issue is identified with a simple test. It's presence, among others, enforce the idea that the team needs more developers and eyes on the project.

### **Implement proper events monitoring** 

MaiaDAO ecosystem implementation leaves the subtle impression that events are not used to their full potential. There are several cases where events are missing and even some that seem to be more likely emitted because "it's good practice", rather that actually being good for the team. Consider using events to their full potential.

### **User more of our auditing tools**

The majority of auditors use Visual Studio Code with some specific plugins. A recomendation for the project developers is to use VC with the plugins:
- [Solidity Visual Developer](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) and [make it work with Solidity](https://twitter.com/abarbatei/status/1655492604736274435)
    - do not underestimate typos, they are to code akin to code how [canaries are to a coal mine](https://en.wiktionary.org/wiki/canary_in_a_coal_mine)

### **Export entire project documentation to a text file to be given as input to ChatGPT**

As AI technology becomes more powerful so do we must embrace and use it. Export the entire project documentation in an input file that can be feed to a ChatGPT session. This way you allow anyone to ask questions to it instead of you as developers.

### **Keep on being beautiful human beings**

_You can put a price on a Code4rena audit contest but you can't put a price on humanity_ (quote by ABA). The MaiaDAO team has gracefully and patiently interacted and answered each and every question the auditor community had for them. There were times where "Hi, I sent you a DM" was the only type of messages you saw in the discord channel but the team always answered. Thank you for being beautiful human beings and please keep being so!

# Auditor logistics

71.75 auditing hours that includes:
- 13.5h writing the [ABA MaiaDAO overview](https://ethereal-net-2b6.notion.site/ABA-MaiaDAO-overview-bc57e0924a3745b48ef2f6b5f61bdb3a) and [Excalidraw diagram](https://link.excalidraw.com/readonly/JnTC9HB2yLadiFxKzlwA?darkMode=true)
- 4.75h writing this analysis
- the remaining time on finding issues and writing the report for each of them on the spot

Unfortunately I did not manage to audit Talos or Ulysses at all as I preferred depth of understanding versus spread of reach.

### Time spent:
71.75 hours