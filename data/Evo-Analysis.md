Analysis *

**Any comments for the judge to contextualize your findings:**
My findings in this project are relying on big part of questions to the Sponsor since not enough description for example: 
Omni-chain operations flows of the concept, my Audit findings mostly in 3 scopes Maia, Hermes, Omni-chain. so I think that judges always get back to the sponsor for tiny details in the concept because it has different scopes as to know some cases how the protocol should work as intended, for example specific constant values or conditions that fixed in the protocol involved in math validations or amounts range.

**Approach taken in evaluating the codebase:**
Basically I used the same notes that I did written while auditing the code so I read them again and see what I was focusing on, because evaluating a codebase it should take different time than audit as they have different process so I had to rely on my audit skills while not really have experience in evaluating a codebase project and such. 
I would say I was exploring the code after submit a finding and that helped me more to understand how the code is readable for example and a way to confirm my submitted issues. 
this codebase is big to be honest and it takes a while just evaluate few scopes in it, lets say Omni-chain concept that is using Multichain contracts cross chain router protocol it got me in time and exploring for while for their docs also how the implementation is done in the project (which 80% understood to me not fully) so am not sure if this approach did help me to create a clear image about each scope in the project as concept design, flow.

**Architecture recommendations: (only a feedback)**
Using cross-chain protocol aim to enable interoperability between different blockchain networks, which allowing the transfer of assets and data across these networks, while using arbitrum chain is really a good choice because It aims to enhance scalability and reduce transaction fees on the Ethereum network and supporting optimistic rollup. but it is totally different story when it comes the project concept design and implementation.
Maia Omni-chain is deeply customized with the sponsor perspective we are talking here for example about sending and receiving messages between two different chains which it could be achieved in different ways but with taking care of the chain transaction gas fees and user gas fees of the cross chain messaging concept, it is a sensitive area to implement in the project.
my experience was great while observing the implementation of gas flow in the concept but I still see different implementation possible or maybe close to the current one with small modifications.
It was hard in the biggening to understand the process flow for example between BranchBridgeAgent and RootBridgeAgent ending with RootBridgeAgentExecutor but what helped me actually is sponsor answers to my questions, I have no recommendations atm while this is a feedback for my experience during exploring the concept design and code structure.


**Codebase quality analysis:**
- comments in the code are missing few things like:
-- some state variables has no explanations 
-- some core functions needs some or more description 
- the functions order in the contract can get better for example:
external function first then internal come after while view function at
the bottom of the contract.. (my perspective).
- functions blocks are too much and they can be limited into few.
- unsupported functions in the protocol are not clear.
- functions names somehow are repeated and make some confusion.
- some comments are just copied into different part of code place while it's not correct or not apply to the code explanation.
(above could have examples to add better clarity to the points but you can ask me if you are interested).

**Centralization risks**
I would mention Arbitrum chain that is relying on centralized sequencer, it uses currently a centralized sequencer operated by Offchain Labs. It has the ability to control the ordering of transactions which could lead to the sequencer front-running transactions and profiting at the user's expense.









### Time spent:
125 hours