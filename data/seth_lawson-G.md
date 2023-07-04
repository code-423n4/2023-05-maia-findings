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

