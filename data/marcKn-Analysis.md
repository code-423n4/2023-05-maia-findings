# Analysis Report: Maia DAO

## 1. How much time did you spend and why are my results worth reading?

I started late with this contest, mid of June, having aproximattely 2 weeks for reviewing and auditing. I knew with 6000 SLOC the time will be too short to do a full audit. So I know I will focus on ERC20MultiVotes, ERC20Gauges, ER20Boost, with Gauges / Factories, and incentive and Bribes. For Thalos and Ulysses there was just time to grasp the concept but there was was not enough time to audit them, as well not for Governance. 

I feel prepared to review this protocol, because I did an intense 8 months soldidity bootcamp with Rareskills.io to enhancing my blockchain knowledge, now focussing on Defi. With about 20 years of coding experience from  Basic to Windows to SQL server to linux to Web2, this gives me another competence. This is my third code4arena contest, the second one was Chainlink CCIP Protocol. This was my first in depth exposure to a Defi protocol in a contest.

## 2 What approach did you take in evaluating this codebase in order to help you grow your skills?

This is a huge codebase with many contracts. At the beginning it was very hard to orient myself so I started chatting with the Co-Founders on discord and reading about the project: Where did the project come from, what resources do they have, who are the founders, what problem they want to achieve, from where do they have their code, what is the difference between Version1 and Version2, which blockchain they are on now and in the future.

In short I figured out Maia DAO is deployed on the Metis blockchain, Hermes was Metis main exchange. I figured out as well that business seems to work good at start but in the last year growth stalled. Therefore the project members wanted to move to Arbitrum and create a multichain approch in Version 2. On their notation site I found their goal for version 2:

> A Decentralized and Permissionless Yield and Liquidity Omnichain Marketplace packed with a Concentrated and Unified Liquidity AMM

Maia DAO v2 allows users from the beginning to do liquidity mining on UniswapV3 NFT positions and later other yield farming. It means at start you can stake your NFT tokens from UniswapV3, representing a pool share. If you stake as a User an NFT you will receive regular weekly emissions of Hermes Token and give your fees from the protocol for it to a gauge. These Hermes tokens can be locked permanently in the bHermes, which gives you back 3 untility tokens, one for voting in Governance, one for boosting your Hermes emissions and one for voting on Gauges (the gauges with the most votes will receive most of the emissions). With voting on gauges you get back a part of the fees generated from UniswapV3 and from other UniswapV3 NFTs.

Talos adds another layer to this logic, as you can deposit liquidity in one single NFT with other users. This has the advantage to collectivly follow strategies like keep a 50%-50% percentage of a pool (with rebalance) protecting yourself from impermanent loss or other strategies in a more gas efficienet, faster and less complex way than when you would do it on your own.

Ulysses allows the protocol to combine fragmented liquidity from different blockchain and combine them into unified pools on Arbitrum. Further these pools can be combined into unified tokens. These two instrument can be used later on in Hermes for creating a omnichain AMM.

I started reading the codebase from top to bottom, switching between the codebase and if there was a question I asked them the Co-founders, Chat-GPT and read the tests. 

## 3. What did you learn from reviewing this codebase? Be specific. Analysis of the codebase (What's unique? What's using existing patterns?)
I realized that the code base has different level of code quality, some parts were written fully by the project team, other just copied from other projects, sometimes parts of it adapted. ERC20MultiVotes and ERC20Gauges and ERC20Boost seem to be battle tested, production code. I lost quite a lot of time to understand what was just copied and what was new, and what codebase was altered. So maybe some hints regarding where the code is coming from and what was altered would have made me more productive. 

What was difficult as well was the lack of documentation and visuialization. With Chainlink CCIP there was much more documentation and it was easier to understand the codebase and fix issues. Here I spent more than half of the time for understanding the protocol, what the purpose is and its architecture. As well the size of the code was a challenge - CCIP was half of the size.

Reading the tests I realized there was done a lot of unit testing but not enough integration testing. As well reading the report of Zellic it was clear that most of the low hanging fruits bugs were already found. So I decided to focus on understanding the protocol and find issues in functions that were not tested enough or long chains of contract calls. 

I learned a lot about Defi - what is a DEX, what is Liquidity Mining, what the NFT a LP receives after providing liquidity. I learned quite some things about UniswapV3. What is staking. What is a gauge. What is a bribe. What is a boost. How a tokenomics model can look like. What is a governance token. What is a multichain approach. What is a bridge. What is a keeper. Many concepts. And most importantly how these concepts are implemented in code.

For me this is a huge project and it is remarkable how far the project team came. I hope that the third audit with Code4Arena will help them to find the last bugs and security flaws and that they will be able to launch their project soon. If I would start a project like this and would have the resources, I maybe would first start without Ulysses or use a different approach like Chainlink Cross Chain Interoperability Protocol to gain more control over the codebase. But I understand that the project team wants to move fast and therefore they choose the approach they did.


## 4 Pitty the contest is over - I would like to work another 14 days on Talos, Ulysses and governance
I invested myself in the protocol, so if Maia DAO is looking for developers, I would be happy to contribute more! For example I did not have enough time to review Talos, Ulysses and Governance. If my contributions are considered helpful, I would be more than happy to contribute more after the contest, payed only for medium and high severity findings.



### Time spent:
110 hours