### 1. What did you learn from reviewing this codebase? Be specific.

	I learn a lot with the MAIA DAO Audit, Firstly I learn a lot about omnichain and that's a very interesting subject
	to help develop the interoperability in crypto space. I learn about the ERC4626 Vault implementation,
	I better understand external calls, better understand of inheritance and composition,
	I read very interesting past attacks on projects similar to MAIA DAO that have given me new eyes to read the codebase.
	It's my first audit with a big codebase like this, it's very interesting to see all the relationships between contracts and this 
	make me feel more confortable to audit. I also learn more about bridges and the common vulnerabilities
	about this subject.Also discover the Anycall v7 system of Multichain you use to send/receive cross-chains messages.
	
### 2. What approach did you take in evaluating this codebase in order to help you grow your skills and code review resources?
	
	On the recommendations of bytes032, I try a new approach with this audit. 
	I took notes for every questions I had about implementation 
	and Solidity behaviors, I also took notes on my intuitions about possible security issues and make tests. 
	That's make a lot of notes to review, but this help me to learn and understand a lot
	of things about contract's construction, create real understanding on Solidity's actual 
	behaviors and find issues.
	For example I had a doubt on arrays and mapping implementations of your UlyssesToken contract,
	that lead me to make some test on arrays and mapping behaviors with differents scenarios and I	
	find an issue about the ERC4626 implementation which prevents users from withdrawing their tokens.
	
	I also tried an approach that focused heavily on the States variables by making a Machine State.
	Unwanted changes in states can cause possibles unwanted behaviors that can lead to funds loss, 
	funds stucks in contract, poor user experiences and/or make the whole system useless. 
	I make formal verifications test to check the possible violations of assumed state invariants.
	I also find an issue with this approach on the RootPort contract where the function 
	addBridgeAgentFactory() is badly coded as it does not update the state correctly and prevents 
	the addition of a new factory. If you loss the one BridgeAgentFactory, you can't use addBridgeAgent() 
	and make a lot of systems functionnality unusable.
	
	I also make big diagrams to better understand the flow of the calls and the paths of the funds, it's
	first time I make this and I won a lot of time in the codebase understanding. I use the drawio website for make this.
	Make a diagram helped me a lot, I recommend to everyone.
	
	On the side I study on common vulnerabilities on Solidity each day before starting to audit, it's available on the bytes032 blog.
	https://blog.bytes032.xyz/p/how-to-learn-common-vulnerabilities
	I read a lot of contents like threads/blogs of Trust, bytes032 and pashov, very good content.
	I also read 30 minutes each day of articles and content about security, I recommend you this weekly newsletter with a lot of good content about blockchain security 
	https://substack.com/@blockthreat?utm_source=substack&utm_medium=email
	
### 3. How much time did you spend?

	It's my 2nd real audit, my first audit where I find bug and I dedicated two intensive weeks on it.
	
### 4. Architecture and Centralization Risks
	Disclaimer: This part is written with my knowledges on the subject of risks centralizations and what I know about your project. I make recommendations with eyes of young security researcher.

	When I read your codebase, I found your architecture very interesting for the way the differents contracts interact with each other.
	Each contract have his role and separation is make in good way, router for users interactions, the Agents contracts, the Agents Executor,
	it's interesting way to updates states across the chain but it is increasing the security risks.
	A big codebase lead to have more security risks related to code implementations and developers assumptions.
	
	I think about centralization risks, a lot of your main contracts with the sensitive functions (Port and BridgeAgent) are used with onlyOwner, this is the
	weakest point of your centralization risk, in case of hacking, lossing private key or mistakes with Ownable functions, the major functionnality of the system
	can become compromised or unusable. I don't know if you use it, but the classical recommendation is using a multisig wallet for reduce the failure point of one
	wallet can control the Governor part of your ecosystem. 
	Other way to make your ecosystem less centralized if to use timelock for all the big decisions on the ecosystem, example the DAO make a major decision, a time before change can be 
	1 week before application.
	With this process if a user think this decision can be bad for his funds, he can withdraw its funds during the waiting time period before the major decision is applied,
	it can improve confidence in the ecosystem. A good example of protocol using this mechanism is Optimism
	An example of positive point with this method is in the recent of Tornado Cash protocol, where a malicious actor succeeded in taking over governance of the protocol but can't make action
	instantly, and this has made it possible to users to withdraws their funds before the hacker make incurable important actions.
	
	Thanks to read me, I hope my analysis can help you in some ways.

Fulum

### Time spent:
100 hours