Contact info:
Twitter: @kamilchmielu
Discord: kchmielewski.eth

I am more than happy to provide any additional information if needed.

### Approach

Maia DAO was the biggest codebase that I had audited so far. My goal from the very beginning was to understand how the system works as a whole. 

Unlike my previous audits, I chose the code-first, docs-later approach. Thanks to that, I didn't get overwhelmed by the complexity of the codebase before even looking at the code. 

I reviewed previous audits by Zellic and decided to focus on Hermes, particularly the rewards system.

Zellic audit uncovered a couple of H/M issues with access control/validation, so I tracked all of the calls performed in Hermes to see if functions could be called in a different order than expected.

I've created a [PlantUML sequence diagram](https://raw.githubusercontent.com/ChmielewskiKamil/diagrams/main/C4/MaiaDAO/HermesSequence.svg) that might be valuable to the protocol team and auditors in the future audits. The [source code](https://github.com/ChmielewskiKamil/diagrams/blob/main/C4/MaiaDAO/HermesSequence.puml) is available as well. By any means, it is not a complete piece of work, but it might form a foundation for something much more helpful. If there would be any interest in that, I am more than happy to provide further guidance.

### Test suite

The original test suite contains a lot of mocks. I've created my own project deployment to test the on-chain integrations and play around with the code on a forked Arbitrum mainnet. If I were to suggest one thing to the protocol team, it would be to create the integration test suite. A) It would make the test coverage more robust, and B) It would decrease the time needed for an auditor to comprehend the codebase well enough to start testing more complex exploit ideas in a sandboxed environment.

As suggested in the [Foundry best practices](https://book.getfoundry.sh/tutorials/best-practices#fork-tests), mocks are the domain of closed-source web2 projects. In web3, they can be treated liberally because all of the APIs are public. Given the size and complexity of the codebase, the usage of mocks is understandable to make testing easier. However, it must be kept in mind that the risks that may arise from an incorrect mock in a highly complex code are significantly higher. 

### Codebase quality (Hermes)

This part of the codebase does not use upgradeability mechanisms. It enforces the user's trust in the protocol and makes it much easier to review for less experienced auditors. 

Variable and function names are verbose, and their intent is clear. Non-NatSpec comments, however, often violate the DRY principle. They duplicate the knowledge contained in the code. They explain the "what the code does?" rather than the "why the code does it?" question. 

### Medium Severity Finding - context

I've spent a significant amount of time considering whether my "restakeToken is not permissionless" finding should be submitted as a medium or as a low-severity issue. 

I've carefully reviewed the [Severity Categorization page](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization) and the medium severity findings from previous audits.

I decided to go with the medium severity for multiple reasons. I assessed the impact on the protocol if the code were to be deployed "as is" without the fix. 

Given the fact that one of the [3 core players in the system are Partners](https://v2-docs.maiadao.io/protocols/Hermes/overview/gauges/introduction), their ability to draw LPs to the protocol by providing bribes is essential. To gain more rented liquidity for themselves, they might want to offer the liquidity providers with automatic re-staking. Because of that, LPs, tempted by higher and easier-to-achieve yield, will provide more funds. 

Without permissionless re-staking, such marketing effort to draw more TVL is impossible and might hit project growth significantly. 

### Final notes

If you've read it this far, I appreciate it.

Have a great day!


### Time spent:
90 hours