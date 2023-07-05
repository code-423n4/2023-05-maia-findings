### Any comments for the judge to contextualize your findings
I've added contexts for some issues that seem complex. This is to help the judge reducing the time spent on judging. I hope it helps.
For issues that are gas related, please note that the goal of the PoC is to prove that the issue exist and not necessarily giving the exact numbers.

###  Approach taken in evaluating the codebase
I followed these steps:
- Went through the [docs](https://v2-docs.maiadao.io/) to understand the concept. 
	- There are 4 major parts
		- Maia
		- Hermes
		- Talos
		- Ulysses
	- Sometimes I encounter some inconsistency between the code and the docs. here is an example:
		- In the docs, it says 
			> Users can stake their MAIA tokens at any time, but can only withdraw their staked tokens on the first Monday of each month.

			However, in the code the implementation is consdiering Tuesday instead. Links:
				- https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol#L22-L25
				- https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/vMaia.sol#L120

- Started to read the codebase high-level, starting from Hermes as it made more sense than starting from Maia.
- Started to read the contracts and connect the dots between functions and what they are supposed to do keeping in mind what I've read in the docs.
- If something isn't clear or maybe missing, I research the docs or ask the sponsor for confirmation. The sponsor was amazingly very responsive and supportive.
- Now I pick public entry points from a contract, then I start to understand the  logic. From there, I reach internal functionalities. This is helpful for me when auditing. 
- During this I write notes about potential issues to be investigated later.
- I do investigate the notes later, then go on from there creating an issue from it, writing a PoC ..etc or disregard it.
- For OmniChain specifically since it is a complex component of the system, I've created a diagram for the request/response flow.
		

### Architecture recommendations
- Ulysses Omnichain
	- The current design requires the user to deposit gas for each deposit independently. This way each deposit has its own deposted gas. I think it would be easier and more convenient (even more efficient) to have one bucket for each user (instead of for each deposit). From a UX perspective, nothing would change for the user except it gets better. For example, the user would be able to track all his/her deposited gas since it is stored in one bucket. At the moment, this is not possible.
	- Moving deposited gas (or even tokens) between two deposits would be a great feature. It is beneficial for the end user. At the moment, if a deposit failed, I should claim it and then can use it for a new deposit.
	- It would be better if there was a layer that abstracts the technical  communication between Ulysses and anyCall V7. Decoupling it would reduce the risk of having a hard dependency (i.e. anyCall V7). It also allows to move to other solutions in case it is needed or required. 
	- Ports are used to store tokens/gas deposited. For each chain, there is one port. This is risky, because this means all users deposits are accumulated in one single contract. It is more secure to have a contract (created dynamically) to store each user's deposit. At the moment, any risk on a port, means a risk for all users funds.
	- tx.gasPrice is used to calcualte the cost when sending a request from a chain to another. However, gas prices could change especially since there are multiple chains. So underpayments and overpayments are likely. We are interested here more in underpayments. This is a tricky issue. However, this should be addressed and evaulated the risk of it. On a large scale, if it happens often, many underpayments will eventually cause the system not to function as intended.
	

### Systemic and/or Centralization risks
- Ulysses Omnichain depends heavily on anyCall V7 (by Multi-chain). [anyCall  contracts](https://github.com/anyswap/multichain-smart-contracts/blob/main/contracts/anycall/v7/AnycallV7Upgradeable.sol) are upgradeable. This means, technically speaking it can be destructed. This also means any systemic risks (or centralization risks) of anyCall is automatically systemic risk (or centralization risks) for Ulysses Omnichain. This should be evaluated and documented even if there is no disaster recovery plan. (Apologies if I missed a document that actually elaborates on this, but couldn't find any).


### Time spent:
144 hours