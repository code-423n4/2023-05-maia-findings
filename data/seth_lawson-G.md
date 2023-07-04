[G-01] Length of cache array outside loop

If the length of an array is not cached, the Solidity compiler will read it at each iteration of a loop. This results in an additional gas overhead, either a sload operation for a storage array (100 additional gases per iteration, except for the first), or a mload operation for an in-memory array (3 additional gases per iteration, except for the first).

Instances (219):

lib\forge-std\src\StdCheats.sol:
  250:         for (uint256 i; i < rawTxs.length; i++) {
  318:         for (uint256 i; i < rawReceipts.length; i++) {
  349:         for (uint256 i; i < rawLogs.length; i++) {

lib\forge-std\src\StdStorage.sol:
   66:             for (uint256 i = 0; i < reads.length; i++) {
  183:         for (uint256 i = 0; i < b.length; i++) {
  317:         for (uint256 i = 0; i < b.length; i++) {

lib\forge-std\test\StdCheats.t.sol:
  305:         for (uint256 i = 0; i < b.length; i++) {

lib\openzeppelin-contracts\contracts\finance\PaymentSplitter.sol:
  55:         for (uint256 i = 0; i < payees.length; i++) {

lib\openzeppelin-contracts\contracts\governance\Governor.sol:
  324:         for (uint256 i = 0; i < targets.length; ++i) {
  341:             for (uint256 i = 0; i < targets.length; ++i) {

lib\openzeppelin-contracts\contracts\governance\TimelockController.sol:
   97:         for (uint256 i = 0; i < proposers.length; ++i) {
  103:         for (uint256 i = 0; i < executors.length; ++i) {
  255:         for (uint256 i = 0; i < targets.length; ++i) {
  335:         for (uint256 i = 0; i < targets.length; ++i) {

lib\openzeppelin-contracts\contracts\governance\compatibility\GovernorCompatibilityBravo.sol:
  132:         for (uint256 i = 0; i < fullcalldatas.length; ++i) {

lib\openzeppelin-contracts\contracts\governance\extensions\GovernorTimelockCompound.sol:
  103:         for (uint256 i = 0; i < targets.length; ++i) {
  129:         for (uint256 i = 0; i < targets.length; ++i) {
  148:             for (uint256 i = 0; i < targets.length; ++i) {

lib\openzeppelin-contracts\contracts\mocks\ERC721ConsecutiveEnumerableMock.sol:
  15:         for (uint256 i = 0; i < receivers.length; ++i) {

lib\openzeppelin-contracts\contracts\mocks\ERC721ConsecutiveMock.sol:
  22:         for (uint256 i = 0; i < delegates.length; ++i) {
  26:         for (uint256 i = 0; i < receivers.length; ++i) {

lib\openzeppelin-contracts\contracts\mocks\MulticallTest.sol:
  14:         for (uint256 i = 0; i < recipients.length; i++) {
  19:         for (uint256 i = 0; i < results.length; i++) {

lib\openzeppelin-contracts\contracts\mocks\ERC165\ERC165InterfacesSupported.sol:
  54:         for (uint256 i = 0; i < interfaceIds.length; i++) {

lib\openzeppelin-contracts\contracts\token\ERC1155\ERC1155.sol:
   93:         for (uint256 i = 0; i < accounts.length; ++i) {
  213:         for (uint256 i = 0; i < ids.length; ++i) {
  312:         for (uint256 i = 0; i < ids.length; i++) {
  378:         for (uint256 i = 0; i < ids.length; i++) {

lib\openzeppelin-contracts\contracts\token\ERC1155\extensions\ERC1155Supply.sol:
  47:             for (uint256 i = 0; i < ids.length; ++i) {
  53:             for (uint256 i = 0; i < ids.length; ++i) {

lib\openzeppelin-contracts\contracts\token\ERC777\ERC777.sol:
  69:         for (uint256 i = 0; i < defaultOperators_.length; i++) {

lib\openzeppelin-contracts\contracts\utils\Multicall.sol:
  19:         for (uint256 i = 0; i < data.length; i++) {

lib\openzeppelin-contracts\contracts\utils\cryptography\MerkleProof.sol:
  58:         for (uint256 i = 0; i < proof.length; i++) {
  71:         for (uint256 i = 0; i < proof.length; i++) {

lib\openzeppelin-contracts\contracts\utils\introspection\ERC165Checker.sol:
  62:             for (uint256 i = 0; i < interfaceIds.length; i++) {
  86:         for (uint256 i = 0; i < interfaceIds.length; i++) {

lib\openzeppelin-contracts\lib\forge-std\lib\ds-test\src\test.sol:
  446:             for (uint i = 0; i < a.length; i++) {

lib\openzeppelin-contracts\lib\forge-std\src\StdChains.sol:
  142:         for (uint256 i = 0; i < strb.length; i++) {

lib\openzeppelin-contracts\lib\forge-std\src\StdCheats.sol:
  245:         for (uint256 i; i < rawTxs.length; i++) {
  313:         for (uint256 i; i < rawReceipts.length; i++) {
  344:         for (uint256 i; i < rawLogs.length; i++) {

lib\openzeppelin-contracts\lib\forge-std\src\StdStorage.sol:
   66:             for (uint256 i = 0; i < reads.length; i++) {
  183:         for (uint256 i = 0; i < b.length; i++) {
  317:         for (uint256 i = 0; i < b.length; i++) {

lib\openzeppelin-contracts\lib\forge-std\test\StdCheats.t.sol:
  288:         for (uint256 i = 0; i < b.length; i++) {

lib\openzeppelin-contracts\test\token\ERC1155\ERC1155.behavior.js:
  522:           for (let i = 0; i < newBalances.length; i++) {

lib\openzeppelin-contracts\test\token\ERC1155\ERC1155.test.js:
  105:           for (let i = 0; i < holderBatchBalances.length; i++) {
  226:           for (let i = 0; i < holderBatchBalances.length; i++) {

lib\openzeppelin-contracts\test\token\ERC1155\utils\ERC1155Holder.test.js:
  38:     for (let i = 1; i < multiTokenIds.length; i++) {
  44:     for (let i = 0; i < multiTokenIds.length; i++) {
  57:     for (let i = 0; i < multiTokenIds.length; i++) {

lib\openzeppelin-contracts\test\token\ERC721\extensions\ERC721Consecutive.t.sol:
  18:         for (uint256 i = 0; i < batches.length; i++) {

lib\solady\js\solady.js:
   74:         for (var i = 0; i < data.length; i += 2) {
  109:         for (var i = 0; i < data.length;) {

lib\solady\js\solady.test.js:
  67:         for (var i = 0; i < s.length; i++) {

lib\solady\lib\ds-test\src\test.sol:
  446:             for (uint i = 0; i < a.length; i++) {

lib\solady\test\DynamicBufferLib.t.sol:
   25:             for (uint256 i = start; i < inputs.length; ++i) {
   51:             for (uint256 i; i < inputs.length; ++i) {
   63:             for (uint256 i; i < chunks.length; ++i) {
   74:             for (uint256 i; i < chunks.length; ++i) {
  139:             for (uint256 i; i < inputs.length; ++i) {

lib\solady\test\ERC721.t.sol:
  319:                 for (uint256 i; i != tokens[j].length;) {
  331:                 for (uint256 i; i != tokens[j].length; ++i) {
  338:                 for (uint256 i; i != tokens[j].length; ++i) {
  350:                 for (uint256 i; i != tokens[j].length; ++i) {
  357:                     for (uint256 i; i != tokens[j].length; ++i) {
  372:                     for (uint256 i; i != tokens[j].length; ++i) {
  380:                 for (uint256 i; i != tokens[j].length; ++i) {
  386:                 for (uint256 i; i != tokens[j].length; ++i) {
  392:                 for (uint256 i; i != tokens[j].length; ++i) {

lib\solady\test\ERC1155.t.sol:
  503:         for (uint256 i = 0; i < t.ids.length; i++) {

lib\solady\test\LibSort.t.sol:
    13:             for (uint256 i = 1; i < aCopy.length; ++i) {
    43:             for (uint256 i = 0; i < a.length; ++i) {
    48:             for (uint256 i = 0; i < a.length; ++i) {
   100:             for (uint256 i; i < a.length; ++i) {
   115:             for (uint256 i; i < a.length; ++i) {
   126:             for (uint256 i; i < a.length; ++i) {
   137:             for (uint256 i; i < a.length; ++i) {
   149:             for (uint256 i; i < a.length; ++i) {
   159:             for (uint256 i; i < a.length; ++i) {
   177:             for (uint256 i; i < a.length; ++i) {
   209:             for (uint256 i; i < a.length; ++i) {
   220:             for (uint256 i; i < a.length; ++i) {
   231:             for (uint256 i; i < a.length; ++i) {
   246:             for (uint256 i; i < a.length; ++i) {
   257:             for (uint256 i; i < a.length; ++i) {
   268:             for (uint256 i; i < a.length; ++i) {
   330:         for (uint256 i = 0; i < a.length; ++i) {
   456:             for (uint256 i = randomIndex + 1; i < a.length; ++i) {
   474:             for (uint256 i; i != a.length; ++i) {
   507:             for (uint256 i; i != a.length; ++i) {
   705:             for (uint256 i; i != aSubB.length; ++i) {
   711:             for (uint256 i; i != b.length; ++i) {
   718:             for (uint256 i; i != bSubA.length; ++i) {
   724:             for (uint256 i; i != a.length; ++i) {
   730:             for (uint256 i; i != aIntersectionB.length; ++i) {
   886:             for (uint256 i; i != a.length; ++i) {
   889:             for (uint256 i; i != b.length; ++i) {
   905:             for (uint256 i; i != a.length; ++i) {
   908:             for (uint256 i; i != b.length; ++i) {
   925:             for (uint256 i; i != a.length; ++i) {
   947:             for (uint256 i; i != a.length; ++i) {
   969:             for (uint256 i; i != a.length; ++i) {
   991:             for (uint256 i; i != a.length; ++i) {
  1006:             for (uint256 i = 1; i < a.length; ++i) {
  1015:             for (uint256 i = 1; i < a.length; ++i) {
  1024:             for (uint256 i = 1; i < a.length; ++i) {
  1111:             for (uint256 i; i != a.length; ++i) {
  1120:             for (uint256 i; i != a.length; ++i) {
  1129:             for (uint256 i; i != a.length; ++i) {

lib\solady\test\LibString.t.sol:
   309:             for (uint256 i; i != raw.length; ++i) {
   331:             for (uint256 i; i != raw.length; ++i) {
   735:                 for (uint256 i; i < indices.length; ++i) {
   807:                 for (uint256 i; i < elements.length; ++i) {
   818:             for (uint256 i; i < splitted.length; ++i) {
  1180:             for (uint256 i; i < sBytes.length; ++i) {
  1277:             for (uint256 i; i < a.length; ++i) {

lib\solady\test\LibZip.t.sol:
  161:             for (uint256 i; i < numbers.length; ++i) {

lib\solady\test\Multicallable.t.sol:
  87:             for (uint256 i; i != data.length; ++i) {
  98:             for (uint256 i; i != data.length; ++i) {

lib\solady\test\OwnableRoles.t.sol:
  168:             for (uint256 i; i < ordinals.length; ++i) {
  179:                 for (uint256 i; i != ordinals.length; ++i) {

lib\solady\test\utils\mocks\MockMulticallable.sol:
  51:             for (uint256 i = 0; i < data.length; i++) {

lib\solmate\lib\ds-test\src\test.sol:
  446:             for (uint i = 0; i < a.length; i++) {

lib\solmate\src\test\ERC1155.t.sol:
   895:         for (uint256 i = 0; i < normalizedIds.length; i++) {
   935:         for (uint256 i = 0; i < normalizedIds.length; i++) {
   996:         for (uint256 i = 0; i < normalizedIds.length; i++) {
  1134:         for (uint256 i = 0; i < normalizedIds.length; i++) {
  1188:         for (uint256 i = 0; i < normalizedIds.length; i++) {
  1226:         for (uint256 i = 0; i < normalizedTos.length; i++) {

lib\solmate\src\test\utils\DSTestPlus.sol:
  143:         for (uint256 i = 0; i < a.length; i++) {

lib\solmate\src\tokens\ERC1155.sol:
   93:         for (uint256 i = 0; i < ids.length; ) {
  131:             for (uint256 i = 0; i < owners.length; ++i) {

lib\v3-core\contracts\libraries\Oracle.sol:
  339:             for (uint256 i = 0; i < secondsAgos.length; i++) {

lib\v3-core\contracts\test\OracleTest.sol:
  61:         for (uint256 i = 0; i < params.length; i++) {

lib\v3-periphery\contracts\base\Multicall.sol:
  13:         for (uint256 i = 0; i < data.length; i++) {

lib\v3-periphery\contracts\lens\UniswapInterfaceMulticall.sol:
  30:         for (uint256 i = 0; i < calls.length; i++) {

lib\v3-periphery\contracts\libraries\HexStrings.sol:
  23:         for (uint256 i = buffer.length; i > 0; i--) {

lib\v3-periphery\contracts\libraries\NFTDescriptor.sol:
  80:         for (uint8 i = 0; i < symbolBytes.length; i++) {
  88:             for (uint8 i = 0; i < symbolBytes.length; i++) {

lib\v3-periphery\contracts\libraries\OracleLibrary.sol:
  158:         for (uint256 i; i < weightedTickData.length; i++) {
  180:         for (uint256 i = 1; i <= ticks.length; i++) {

lib\v3-periphery\contracts\test\MockObservations.sol:
  27:         for (uint256 i = 0; i < _blockTimestamps.length; i++) {

lib\v3-periphery\test\Base64.spec.ts:
  76:       for (let i = 0; i < inputs.length; i++) {

lib\v3-periphery\test\shared\path.ts:
  15:   for (let i = 0; i < fees.length; i++) {

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
  253:             for (uint256 i = 1; i < bandwidthStateList.length;) {
  278:             for (uint256 i = 1; i < bandwidthStateList.length;) {
  296:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

src\ulysses-amm\UlyssesToken.sol:
   95:         for (uint256 i = 0; i < assets.length; i++) {
  111:         for (uint256 i = 0; i < assets.length; i++) {

src\ulysses-omnichain\ArbitrumBranchPort.sol:
  101:         for (uint256 i = 0; i < _localAddresses.length;) {
  136:         for (uint256 i = 0; i < _localAddresses.length;) {

src\ulysses-omnichain\BranchBridgeAgent.sol:
   283:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
   719:         for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
   951:         for (uint256 i = 0; i < deposit.hTokens.length;) {
  1354:         for (uint256 i = 0; i < _deposits.length; i++) {

src\ulysses-omnichain\BranchPort.sol:
  231:         for (uint256 i = 0; i < _localAddresses.length;) {
  267:         for (uint256 i = 0; i < _localAddresses.length;) {

src\ulysses-omnichain\MulticallRootRouter.sol:
  146:         for (uint256 i = 0; i < outputTokens.length;) {
  311:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {
  387:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {
  463:             for (uint256 i = 0; i < outputParams.outputTokens.length;) {

src\ulysses-omnichain\RootBridgeAgent.sol:
  342:         for (uint256 i = 0; i < _globalAddresses.length;) {
  408:         for (uint256 i = 0; i < _dParams.hTokens.length;) {
  561:         for (uint256 i = 0; i < newGas.length;) {
  596:         for (uint256 i = 0; i < settlement.hTokens.length;) {

src\ulysses-omnichain\VirtualAccount.sol:
  48:         for (uint256 i = 0; i < calls.length; i++) {

test\test-utils\invariant\handlers\UlyssesPoolHandler.t.sol:
  104:         for (uint256 i = 1; i < pool.getBandwidthStateList().length; i++) {

test\test-utils\invariant\handlers\UlyssesTokenHandler.t.sol:
   68:         for (uint256 i = 0; i < assets.length; i++) {
   74:         for (uint256 i = 0; i < assets.length; i++) {
  248:         for (uint256 i = 0; i < assetsForAction.length; i++) {
  404:         for (uint256 i = 0; i < tokens.length; i++) {
  410:         for (uint256 i = 0; i < tokens.length; i++) {
  432:             for (uint256 i = 0; i < _underlyings_.length; i++) {
  436:             for (uint256 i = 0; i < _underlyings_.length; i++) {
  445:         for (uint256 i = 0; i < _underlyings_.length; i++) {
  454:             for (uint256 i = 0; i < _underlyings_.length; i++) {

test\test-utils\invariant\helpers\AddressSet.sol:
  34:         for (uint256 i; i < s.addrs.length; ++i) {
  43:         for (uint256 i; i < s.addrs.length; ++i) {

test\test-utils\invariant\helpers\UlyssesTokenProp.t.sol:
   22:         for (uint256 i = 0; i < _underlyings_.length; i++) {
   30:         for (uint256 i = 0; i < _underlyings_.length; i++) {
  115:         for (uint256 i = 0; i < UlyssesToken(_vault_).getAssets().length; i++) {
  146:         for (uint256 i = 0; i < assetsPreview.length; i++) {
  164:         for (uint256 i = 0; i < UlyssesToken(_vault_).getAssets().length; i++) {
  222:         for (uint256 i = 0; i < UlyssesToken(_vault_).getAssets().length; i++) {
  252:         for (uint256 i = 0; i < preview.length; i++) {
  275:         for (uint256 i = 0; i < UlyssesToken(_vault_).getAssets().length; i++) {
  293:         for (uint256 i = 0; i < assets2.length; i++) {
  331:         for (uint256 i = 0; i < assets2.length; i++) {
  356:         for (uint256 i = 0; i < assets2.length; i++) {
  370:         for (uint256 i = 0; i < assets2.length; i++) {
  458:         for (uint256 i = 0; i < assets.length; i++) {

test\ulysses-amm\UlyssesPoolTest.t.sol:
  140:         for (uint256 i = 0; i < pools.length; i++) {
  145:             for (uint256 j = 1; j < bandwidthStateList.length; j++) {
  199:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {
  227:         for (uint256 i = 1; i < bandwidthStateList.length; i++) {

test\ulysses-omnichain\BranchBridgeAgentTest.t.sol:
  896:         for (uint256 i = 0; i < _hTokens.length; i++) {

test\ulysses-omnichain\mocks\Multicall2.sol:
  22:         for (uint256 i = 0; i < calls.length; i++) {
  70:         for (uint256 i = 0; i < calls.length; i++) {

