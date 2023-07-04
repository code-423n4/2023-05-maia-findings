**src/ulysses-omnichain/token/ERC20hTokenRoot.sol**
- L22 - A variable is created in storage, localBranchPortAddress, which is never used, therefore it should be removed.


**src/maia/factories/PartnerManagerFactory.sol**
- L21/24/44/49 - Two getter functions are created, but the variables in storage are public, therefore the variables in storage should be returned internal.


**src/governance/GovernorBravoDelegator.sol**
- L42/44 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**src/gauges/factories/BribesFactory.sol**
- L63 - A getter function is created, but the variable in storage is public, therefore the variable in storage should be returned internal.


**src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol**
- L85/89 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTE COST EXTRA GAS


**src/ulysses-amm/UlyssesToken.sol**
- L115/116/118 - Subtraction operations are carried out, but previously the previous validation is carried out, therefore it would not be possible for an underflow to occur. Gas could be saved by doing these operations unchecked.


**src/gauges/factories/BaseV2GaugeManager.sol**
- L29/51 - The variable in storage, gaugeFactories, is public but in turn has a getter created, therefore the variable in storage should be set as internal.


**src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol**
- L64/68/84/119/123 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
