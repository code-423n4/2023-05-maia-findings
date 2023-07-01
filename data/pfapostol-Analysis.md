## Approach taken in evaluating the codebase

To evaluate the codebase, I started by reading the documentation for the `hermes`, `maia`, and `TALOS` modules.Then I made a list of terms and concepts that I didn't know. Then I started to learn something new: learned about soldily.exchange (https://github.com/sanchitdawarsd/solidly.exchange), ve(3,3) concept, unified liquidity, omnichain, gauges and other concepts unknown to me at the time. 

After that, I began to study the code in depth, starting with the `hermes` module, then moving on to `maia`, `TALOS` and the logic of `rewards`, I read file by file, function by function, building an understanding of how each module works and how they relate to each other. Due to the main work, I had little time, so I excluded the `Ulysses` module from the audit process, as it seems very complicated and requires more time to study it thoroughly.

For each contract, I've created a storage abstraction that shows all state variables and explores the ways in which functions can affect or be affected by those variables.

After understanding how contracts affect each other and how external actors can affect them, I started building attack vectors and trying to fuzz external (public) contract interfaces to achieve some unintended contract states.

I didn't find much in these contests, but I gained a lot of knowledge which should be very useful in the next code4rena contests.

## Architecture recommendations

The current architecture is quite complex, and due to my omission of the entire "Ulysses" part of the protocol, I cannot make sufficient recommendations for the entire architecture.

## Codebase quality analysis



The code is very well written, use the same style for all contracts. There's really a lot of modulation (fragmentation). each functional part is separated into a separate contract (or abstract contract), which makes it difficult for the user to understand the code at a glance, but for people who have been studying (writing) this codebase for a while, it is very easy to use due to the similar logic in different contracts.

Due to code fragmentation, some functions repeat each other, some modifiers are called twice or duplicate existing checks in an external function, which negatively affects the cost of the project's gas, but in general does not harm the usability and stability of the system.

Test coverage is not complete, which leads to security issues, including the one I reported.

Some contracts in the codebase have been rewritten from the code of external projects or a previous version of the project, which has led to some strange anomalies in the code. For example, `src\erc-20\ERC20MultiVotes.sol` uses `EnumerableSet` for `delegates` when the function logic guarantees that `delegates` can only be one address. My guess is that this happened because the developers didn't want to break the existing code, but it makes the codebase more complex and gas inefficient for no practical reason.

## Conclution

This project was one of the most interesting I've seen on code4rena, i regret that I didn't have time to cover it in full and spend more time looking for edge cases. But this project forced me to expand my theoretical knowledge about DeFi applications and the concepts they use.

### Time spent:
30 hours