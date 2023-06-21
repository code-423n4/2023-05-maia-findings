# Introduction 

The Maia DAO Ecosystem’s contest in code4arena lasted for a month. During that time, I reviewed the code of the protocols in depth, namely [Maia](#maia), [Hermes](#hermes), [Talos](#talos) and [Ulysses](#ulysses). About [Talos](#talos), I didn't have time to really dig in (to do something halfways I better not even do it). It was my first contest since last August, so I refreshed all the knowledge I had back then as well as learnt much more through my findings and the interesting aspects and behavior of each protocol.

# Ecosystem overview 

The whole ecosystem is made of four protocols:

- [Maia](#maia), which implements a vault of `bHermes` tokens, allowing users to stake their $MAIA for `vMAIA` and receive either `bHermesWeight` or `bHermesGovernance` to interact with other protocols in the ecosystem as well as participate in governance affairs.
- [Hermes](#hermes), an Omnichain yield and liquidity marketplace, which lets users to easily move between different chains and provide liquidity from and to any of the chains where [Hermes](#hermes) is deployed.
- [Talos](#talos), which is based on [Uniswap V3](https://blog.uniswap.org/uniswap-v3) and offers users an easy way to manage their liquidity through a common pool of concentrated liquidity LPs.
- [Ulysses](#ulysses), which enables users to deploy their assets from one chain and earn revenue from activity in other chains whilst maintaining a common interface.

Overall, the whole ecosystem is mature and very promising. Its theoretical foundations are solid and the quality of the code is very high, although there are some flaws that must be solved as soon as possible (see my findings in the code4arena contests).

## Maia 

The `vMAIA` token ($MAIA’s staked version) is very straightforward. Just a token that lets users interact with the rest of the ecosystem and participate in governance proposals and decisions. It’s very similar to other “in-house” tokenomics, so I am not gonna deepen too much in this part. I just wanna recommend something that was in my head during my time reviewing this part of the ecosystem: do NOT use `block.timestamp` for deadlines or events that affect in any way user funds.

It can be manipulated by miners, and it has been written a lot about the vulnerabilities and inaccuracies of using `block.timestamp`, so any hack based on such a simple attack vector would harm users and kick out the Maia DAO Ecosystem from the market.

## Hermes 

This part is mainly focused on gauges and boosts, available through the tokens `bHermesGauges` and `bHermesBoost`. The inner working process was very interesting and challenging to understand (I strip all comments and I do not read the docs the first time I review the code), but once it did “click” it started flowing naturally.

I did encounter a few issues with reentrancy between smart contracts and some flaws in the governance system, such as spamming and DOS, but the code was pretty solid and the risk of centralization was low. However, there is this thing that has been coming to my head during the whole month: sybil attacks.

I know it’s usually not in scope for many contests and bug bounties, but the governance works with UNIQUE voters and anyone can propose something and approve himself with bots, so, unless a guardian or the admin sees that and stops him, there is no way to cancel such malicious proposal. I recommend to implement some functionality such as a part of the community (random chosen after every submitted proposal, for example, to avoid sybil attacks) could cancel pending proposals on behalf of the admin or the guardian if the big majority of them agrees (IDK, >70% ??). You know, to stop A and its bots to do bad stuff while the admin (multisig account, though) is on vacation or something.

## Talos 

I didn’t review this part much because of time and the apparent complexity of the protocol, so I cannot say much (I am in college, I do have exams and shitty projects to work on). However, from the comments, the number of tests and the detailed thread model made by the core team at Maia, I would say (naively) that it seems robust enough.

## Ulysses 

This protocol is the one I spent more time on. I have always loved bridges and interchain communications, so I felt like home when I started reviewing the code. One thing that catch my attention was the idea of having a single “virtual” token available in all chains where there are ports and routers (see the [docs](https://v2-docs.maiadao.io/protocols/Ulysses/overview/omnichain/ports) for more, it’s worth it, trust me). However, there was something that made me a bit skeptical about the design:

![](https://v2-docs.maiadao.io/assets/images/Ulysses_Omnichain_Layout-b9046666831696b4668774459d24c1ad.png) 

This topology reminds me of the Web2 times when you could stomp in a LAN through a central router and start spoofing packets from there. You may be thinking “WTF man” but my main worry is that if the root agent (the one in Arbitrum) is compromised somehow or the L1 is hacked or something, it would lead to a complete collapse of the system, leading to the different branches to be inoperable. My suggestion is to decentralize the connections, kind of complete graph where node A is connected to N - 1 nodes and so on, thus increasing the robustness of the network.

# Summary 

Overall, the Maia DAO Ecosystem offers numerous benefits to its participants and is a very promising project. I learnt a lot from this and I used it as a reminder of the good old times and my past knowledge. There are some things I learnt through this contest: messaging between chains with [Multichain](https://app.multichain.org/), existence of tokens which do not follow the EIP20 standard (see [here](https://github.com/code-423n4/2021-10-ambire-findings/issues/35) and go down the rabbit hole) and some weird quirks of assembly and low-level stuff (which are rather easy, although you must “click ’em” first).

 

 


### Time spent:
80 hours