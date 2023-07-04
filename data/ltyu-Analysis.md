As there was only so much time for such a large codebase, this audit was partially completed and the analysis is provided in the context of the completed modules. The modules that were completed are: Ulysses (AMM and Omnichain), and Hermes (with its dependencies). 

### Analysis of the codebase (What's unique? What's using existing patterns?)
Overall, the codebase is clean, with a lot of comments, and supporting documentation. The use of natspec when needed was not distracting, but was helpful where a little extra color was needed, such as when [assembly was used](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-amm/UlyssesPool.sol#L380-L408). 

Whether the devs are aware or not, I would like to gently remind that the use of SafeTransferLib may only give the disguise of being safe. The Library is intended to make token transfers safer by handling missing return values. However, as noted in the natspec, the Library does not check if the token has code. Keep this in mind in the future if return values are relied on.

### Architecture feedback
**Documentation**
I think the [documentation](https://v2-docs.maiadao.io/protocols/overview/vaults/strategies#how-do-maia-vaults-increase-your-yields) could be more technical. The documentation has a lot of high level concepts that were hard to connect to the code without clarification. 

For example, in the first paragraph of "Virtual Liquidity"
```
Virtual Liquidity refers to the mirroring of assets locked in different chains within a single liquidity management environment. This opens up doors to unmatched composability.
```
To someone uninitiated, it's unclear what the mirror of assets locked in different chains means, how this "opens up doors to unmatched composability".

There are also a few discrepancies between the code and documentation. For [example](https://v2-docs.maiadao.io/protocols/Ulysses/overview/unified-liquidity/fees#rebalancing-fees), the first condition in the rebalancing fee table is incorrect. Although mistakes in inevitable, many mistakes add additional understanding time to auditors and other technical-minded readers.

I also appreciated that there were videos that walked through the code.

### Centralization risks
One of the ethos I got from this project, is that decentralization is the developer's minds. Although there are a lot of `onlyOwner` type of functions, as noted by the Sponsor, a lot of the contracts' ownership can either be renounced as needed, or would be given to Governance.

### Systemic risks
Anycall is one of the key components of making all this work. There is a lack of integration testing for this component.

### Other recommendations
**Test Coverage**
I think the biggest weakness of this codebase was that the coverage was unclear. As mentioned in the discord discussion, `forge coverage` does not work. From an auditor perspective, this created a lot of blindspots on what to focus on. As a developer, it's unclear what code remains untested. After reviewing some of the tests, I believe there are missing tests (such as UlyssesFactory). This resulted in many of the issues found. 

Another minor nitpick, but related, is that of the reuse of custom errors. Some custom errors, such as `DelegationError` is used in multiple functions (see ERC20MultiVotes). This made debugging harder when I was running my own tests.

### Time spent:
60 hours