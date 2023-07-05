## Approach
After reading the project overview and identifying the project tree structure, I've started reading the simpler contracts and gradually move to more complex ones.
My approach was to review line by line and add audit tags for section that I've found susceptible of having problems. 
For sections that looked like a bug or exploit, I've moved to the units tasks to understand how the functions where intended to be called in practice.
I've used some of the audit time to research and try to understand the various defi concepts used in the protocol.

## Learnings
1. Creation of vaults that support multiple assets through an expansion of ERC4626 used in UlyssesToken.
2. Adding ERC20Gauges to handle rewards distribution.
3. Ability for LPs to add gauges and receive bribes.
4. Adding ERC20Boost to ERC20Gauges to boost rewards.

### Time spent:
80 hours