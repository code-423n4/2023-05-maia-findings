First off I'll like to say I appreciate the video documentation by 0xLight and colleague as the docs alone  would not have been sufficient for me. However there are a lot of things that can be done better in the codebase and the documentation. Although I spent half the alloted time on this contest because I was working on other contests prior to MAIA DAO.

## Approach taken in evaluating the codebase
Being the nature of the documentation, I used `low hanging fruit first approach` starting from the more familiar contracts like the ERC20s and, talos and even Ulyssis contracts respectively thereafter to the rewards and then the specified areas of concentration like the UniswapV3Staker.

I adopted a line by line manual review of the codebase and yes I would say it served me better considering the limited time I had find vulnerabilities.


## Architecture recommendations
Taking a wholistic view of the project, the contract seem to piece together nicely and would have been way easier to audit if most functions were already implemented before the audit. The down side to having a lot of unimplemented functions is that an auditor may want to assume functions and parameter description during a deep dive and miss a possible vulnerability. This could have been better handled if the NATSPEC formatting had at least 95% coverage for such a large project.



## Codebase quality analysis
As regards reusability, the codebase had a lot of reusable functions which helps with modularity but on the flip side, reusability for a large codebase (that is normally perceived to have a large attack surface). modularity could pose a challenge which I expatiate on in the centralization risk section of this analysis report.

Naming conventions were a huge challenge in the codebase, for instance in some interfaces some Interface functions Natspec comments gave a wrong description of the functions and also some variables used to cache formulas involving state variables were within functions where not descriptive enough. A good instance is using a variable called ```required``` for a variable that I later discovered was used to cache the required amount of ```dHermes``` weekly emissions. Although asking questions on the C4 discord helped but it was not as efficient as when the variables had descriptive enough names.




## Centralization risks

Centralisation risk for the most part does not hang on only owner or even modifier controlled functions, it can lie in functions that are reused in the codebase. If a function that is used widely in the codebase happens to be vulnerable to an attack, that would translate to a single point of failure and their could lead to auditors reporting findings that seem like duplicates and a judges can miss an important vulnerability if care is not taken.


## Mechanism review
As of the time of this audit, the Maia Dao project goal seems good for the purpose it serves but there is a lot of work to especially in implementation of contract functions and since like I mention in a previous section the project is large and realistically provides a large attack surface,


## Systemic risks

As regards systemic risk, I personally found a few DOS vulnerabilities which I offered suggestions for but overall I believe if the as long as the contracts prioritise audit as a going concern and duly implement audit reviews on the codebase systemic risk would be brought to the barest minimum.

### Time spent:
145 hours