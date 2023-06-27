## Analysis of the MaiaDAO Ecosystem

- The MaiaDAO ecosystem is aiming to solve a problem that is on the rise in the blockchain ecosystem, which is a fragmentaded liquidity among all the different networks that exists and the new ones that will come in the future.
  - MaiaDAO and the Ulysses System creates an omnichain liquidity  environment that makes possible to virtually put together liquidity from different networks and use to distribute it as needed on the system.
  - All the liquidity providers receive the benefit of earning interest on the liquidity they provided not only by the usage it has on a single chain, but on the omnichain.

- There are some special tokens that gives users the right to vote, gain boots on their rewards.

- The MaiaDAO ecosystem implemented the UiswapV3Staker for the rewards of the Staked Positions.

- The reward system is heavily inspired by the Tribe DAO contract (flywheel-v2), which that in part helped the security of the contracts of the MaiaDAO system, building on top of already audited and battle tested contracts gets rides off many bugs in comparisson to developing the logic from scratch.

- There are different types of vaults, which depending on the type of operation and system to interact with it'll be the type of vault to use.

- The approach I took to review this codebase was starting to read the documentation and videos provided by the sponsor, because the whole system is composed of 4 individual systems (Hermes, Talos, Maia & Ulysses) that coexists and are co-dependent of each other. So, my plan was to understood the systems individually as they were presented in the videos provided by the sponsor, and as I went deep on the codebase start to connecting the dots about how the systems interact with each other.
- I found a couple of vulnerabilities that were specific to a single system and also specific to the logic of a single contract, but there were also vulnerabilities that were caused because of the interaction between different systems.

- A personal advice I might give to the sponsor for their future endeavors is to take special care with logic that involves manipulation of bytes, it is a quite overwhelming and complicated process that one can esily get lost, specially when these operations involves shifting, downcasting and operating between different data types.
  - Also always double check the logic implemented on the for loops, make sure that the output is the correct one and that values were not lost during the loop's iteration.

- I personally learned and mastered the process to encode/decode data, operations to manipulate bytes such as shiting bytes, I got familiar with the UniswapQ96 Notation, learned a lot about how bridging between different networks can happen, this protocol uses the AnyCall contracts to achieve the interaction between networks.
  - Also I got more familiar with how solidity threads the inheritance hierarchy and how the abstract contracts can be used to reuse code and override whatever is required to make the final implementation exactly as it is required.

- I didn't really track the time I put into this contest, but I'd say it was around 70 - 100 hours, I did learn a lot from auditing this codebase, the sponsor was super responsive and helpful when clarifying doubts.

- There were some parts of the codebase that I was amazed by how the logic works, specifically about the internal accounting on all the personalized ERC20 contracts, even though the implementation of those contracts was heavy interconnected, the logic of the acounting was very well implemented and with no flawless.

- In General, the integration of the 4 systems looks good, with a couple of issues when manipulating bytes and with functions using heavy math formulas.

### Time spent:
85 hours