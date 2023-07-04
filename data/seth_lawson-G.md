# G 01 : Length of cache array outside loop

If the length of an array is not cached, the Solidity compiler will read it at each iteration of a loop. This results in an additional gas overhead, either a sload operation for a storage array (100 additional gases per iteration, except for the first), or a mload operation for an in-memory array (3 additional gases per iteration, except for the first).

Instances (27):

src\governance\GovernorBravoDelegateMaia.sol:
  181:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  215:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  251:         for (uint256 i = 0; i < proposal.targets.length; i++) {


src\out-of-scope\governance\GovernorBravoDelegateSeverity1.sol:
  183:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  217:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  253:         for (uint256 i = 0; i < proposal.targets.length; i++) {
 

src\out-of-scope\governance\GovernorBravoDelegateSeverity2.sol:
  183:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  217:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  253:         for (uint256 i = 0; i < proposal.targets.length; i++) {


src\out-of-scope\governance\GovernorBravoDelegateSeverity3.sol:
  183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

  217:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

src\out-of-scope\governance\GovernorBravoDelegateSeverity4.sol:
  183:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  217:         for (uint256 i = 0; i < proposal.targets.length; i++) {
 
  253:         for (uint256 i = 0; i < proposal.targets.length; i++) {

src\out-of-scope\governance\GovernorBravoDelegateSeverity5.sol:
  183:         for (uint256 i = 0; i < proposal.targets.length; i++) {

  217:         for (uint256 i = 0; i < proposal.targets.length; i++) {

  253:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  

src\ulysses-amm\UlyssesPool.sol:
  130:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {
  
  232:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {
  
  296:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {
  
src\ulysses-amm\UlyssesToken.sol:
   95:         for (uint256 i = 0; i < assets.length; i++) {
  111:         for (uint256 i = 0; i < assets.length; i++) {

src\ulysses-omnichain\BranchBridgeAgent.sol:
   283:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
   719:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
  1354:         for (uint256 i = 0; i < _deposits.length; i++) {

src\ulysses-omnichain\VirtualAccount.sol:
  48:         for (uint256 i = 0; i < calls.length; i++) {



# G 02 Use != 0 instead of > 0 for unsigned integer comparison

35 results - 11 files

src\erc-20\ERC20Gauges.sol:
  417:         if (weight > 0) {
  441:         if (weight > 0) {
 
src\rewards\base\FlywheelCore.sol:
  127:         if (oldRewardBalance > 0) {
  162:         if (strategyRewardsAccrued > 0) {
  

src\talos\TalosStrategyVanilla.sol:
  139:         if (_liquidity > 0) {

src\ulysses-amm\UlyssesPool.sol: 
   153:         if (claimed > 0) {
   191:             if (oldBandwidth > 0) {
   256:                     if (oldBandwidth > 0) {
   272:             if (oldBandwidth > 0) {
   282:                     } else if (leftOverBandwidth > 0) {
   911:             if (updateAmount > 0) {
  1048:             if (updateAmount > 0) {

src\ulysses-omnichain\ArbitrumBranchBridgeAgent.sol:
  160:         if (gasRemaining > 0) {

src\ulysses-omnichain\ArbitrumBranchPort.sol:
  118:         if (_deposit > 0) {
  123:         if (_amount - _deposit > 0) {
  137:             if (_deposits[i] > 0) {
  144:             if (_amounts[i] - _deposits[i] > 0) {
  

src\ulysses-omnichain\BranchBridgeAgent.sol:
  623:             if (_amounts[i] - _deposits[i] > 0) {  
  627:             if (_deposits[i] > 0) { 
  979:         if (_amount - _deposit > 0) { 
  983:         if (_deposit > 0) {

src\ulysses-omnichain\BranchPort.sol:
  248:         if (_amount - _deposit > 0) { 
  252:         if (_deposit > 0) {
  268:             if (_deposits[i] > 0) {  
  275:             if (_amounts[i] - _deposits[i] > 0) {
 
src\ulysses-omnichain\RootBridgeAgent.sol:
   451:         if (_amount - _deposit > 0) {  
   457:         if (_deposit > 0) {
   750:             if (_initialGas > 0) { 
   757:         if (_initialGas > 0) {
  1161:             if (initialGas > 0) {
  1171:         if (initialGas > 0) {
  
src\uni-v3-staker\libraries\RewardMath.sol:
  31:         if (boostTotalSupply > 0) {
