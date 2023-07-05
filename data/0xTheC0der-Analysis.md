# Short analysis
Hello, this is my first analysis at C4 and I only got 30min left before the deadline, however I want to give it a shot and share some quick insights.  

## Scope
Due to my limited availablity I only reviewed the ERC-4626, Maia, Ulysses Token & Ulysses Omnichain parts of the ecosystem. Thereby I have found 2 high & 6 medium severity bugs (my own classification, let the judges decide).

## Testing
My greatest concern is the testing, although there is already an extensive amount of unit tests. This protocol lacks more involved integration tests which is also reflected by my findings which show that some functionalities/methods can be broken (DoS) just by using another method of the **same** contract.  
For example, test a whole deposit & withdraw cycle of an ERC-4626 vault and check if the vault and its underlying tokens have the same state as before the deposit (totalSupply, approval, etc.).

## Complexity
Many parts of the code gave me the impression of vast complexity, especially the Ulysses Omnichain protocol could be implemented in a simpler way leaving less room for vulnerablities (according to what I've seen in audits of other cross-chain protocols).

## Token standards
Try to comply with token standards, especially when putting the standard into the contract's name.  
For example, all your ERC-4626 vaults return the underlying `asset` of type `ERC20` but the [standard](https://eips.ethereum.org/EIPS/eip-4626) requires it to be of type `address`. Might be a minor issue but still leads to a `revert` in case a user uses a standard inferface contract to interact with your vaults.

## Mixed use of libraries
I noticed mixed used of the `FixedPointMathLib` from Solmate and Solady throughout the contracts. Especially the ERC-4626 vault implementation is based on Solmate's but uses the `FixedPointMathLib` from Solady. I'd strongly recommend to stick with one of the available implementations throughout the whole protocol, not only for consistency but also to avoid potential hard to detect errors.

Have a nice day!



### Time spent:
16 hours