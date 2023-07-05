# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Using bools for storage incurs overhead | 10 |
| [GAS-2](#GAS-2) | Cache array length outside of loop | 4 |
| [GAS-3](#GAS-3) | State variables should be cached in stack variables rather than re-reading them from storage | 6 |
| [GAS-4](#GAS-4) | Use calldata instead of memory for function arguments that do not get mutated | 20 |
| [GAS-5](#GAS-5) | For Operations that will not overflow, you could use unchecked | 767 |
| [GAS-6](#GAS-6) | Use Custom Errors | 186 |
| [GAS-7](#GAS-7) | Don't initialize variables with default value | 7 |
| [GAS-8](#GAS-8) | Long revert strings | 54 |
| [GAS-9](#GAS-9) | Functions guaranteed to revert when called by normal users can be marked `payable` | 37 |
| [GAS-10](#GAS-10) | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 4 |
| [GAS-11](#GAS-11) | Using `private` rather than `public` for constants, saves gas | 11 |
| [GAS-12](#GAS-12) | Splitting require() statements that use && saves gas | 6 |
| [GAS-13](#GAS-13) | Use != 0 instead of > 0 for unsigned integer comparison | 61 |
| [GAS-14](#GAS-14) | `internal` functions not called by the contract should be removed | 27 |
### <a name="GAS-1"></a>[GAS-1] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (10)*:
```solidity
File: OFT/OFTCoreV2.sol

22:     bool public useCustomAdapterParams;

23:     mapping(uint16 => mapping(bytes => mapping(uint64 => bool))) public creditedPackets;

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

38:     mapping(address => bool) public mintVault;

40:     mapping(address => bool) public vaultMintPaused;

41:     mapping(address => bool) public vaultBurnPaused;

46:     mapping(address => bool) redemptionProvider;

47:     mapping(address => bool) public tokenMiner;

61:     bool public premiumTradingEnabled;

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

57:     bool public isEUSDBuyoutAllowed = true;

```

```solidity
File: mocks/LZEndpointMock.sol

27:     bool public nextMsgBlocked;

```

### <a name="GAS-2"></a>[GAS-2] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (4)*:
```solidity
File: lybra/configuration/LybraConfigurator.sol

236:         for (uint256 i = 0; i < _contracts.length; i++) {

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

94:         for (uint i = 0; i < _pools.length; i++) {

138:         for (uint i = 0; i < pools.length; i++) {

```

```solidity
File: mocks/LZEndpointMock.sol

172:                 for (uint i = 0; i < msgs.length - 1; i++) {

```

### <a name="GAS-3"></a>[GAS-3] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*Saves 100 gas per instance*

*Instances (6)*:
```solidity
File: lybra/configuration/LybraConfigurator.sol

299:                 EUSD.transfer(address(lybraProtocolRewardsPool), balance);

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

153:         uint256 etherInLp = (IEUSD(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2).balanceOf(ethlbrLpToken) * uint(etherPrice)) / 1e8;

234:             rewardRatio = (amount + remainingRewards) / duration;

```

```solidity
File: lybra/miner/ProtocolRewardsPool.sol

236:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e36 / token.decimals()) / totalStaked();

```

```solidity
File: lybra/miner/stakerewardV2pool.sol

137:             rewardRatio = (_amount + remainingRewards) / duration;

```

```solidity
File: mocks/LZEndpointMock.sol

216:         nativeFee = nativeFee + relayerFee + oracleFee;

```

### <a name="GAS-4"></a>[GAS-4] Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

*Instances (20)*:
```solidity
File: OFT/interfaces/IStargateReceiver.sol

8:         bytes memory _srcAddress,

12:         bytes memory payload

```

```solidity
File: OFT/interfaces/IStargateRouter.sol

26:         lzTxObj memory _lzTxParams,

39:         lzTxObj memory _lzTxParams

55:         lzTxObj memory _lzTxParams

70:         lzTxObj memory _lzTxParams

```

```solidity
File: lybra/Proxy/LybraProxy.sol

9:     constructor(address _logic,address _admin,bytes memory _data) TransparentUpgradeableProxy(_logic, _admin, _data) {}

```

```solidity
File: lybra/governance/AdminTimelock.sol

8:     constructor(uint256 minDelay, address[] memory proposers, address[] memory executors, address admin) TimelockController(minDelay, proposers, executors, admin) {}

8:     constructor(uint256 minDelay, address[] memory proposers, address[] memory executors, address admin) TimelockController(minDelay, proposers, executors, admin) {}

```

```solidity
File: lybra/governance/GovernanceTimelock.sol

15:     constructor(uint256 minDelay, address[] memory proposers, address[] memory executors, address admin) TimelockController(minDelay, proposers, executors, admin) {

15:     constructor(uint256 minDelay, address[] memory proposers, address[] memory executors, address admin) TimelockController(minDelay, proposers, executors, admin) {

```

```solidity
File: lybra/governance/LybraGovernance.sol

38:       constructor(string memory name_, TimelockController timelock_, address _esLBR) GovernorTimelockControl(timelock_)  Governor(name_) {

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

93:     function setPools(address[] memory _pools) external onlyOwner {

```

```solidity
File: lybra/miner/esLBRBoost.sol

33:     function addLockSetting(esLBRLockSetting memory setting) external onlyOwner {

```

```solidity
File: mocks/LZEndpointMock.sol

114:     function send(uint16 _chainId, bytes memory _path, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes memory _adapterParams) external payable override sendNonReentrant {

114:     function send(uint16 _chainId, bytes memory _path, bytes calldata _payload, address payable _refundAddress, address _zroPaymentAddress, bytes memory _adapterParams) external payable override sendNonReentrant {

205:     function estimateFees(uint16 _dstChainId, address _userApplication, bytes memory _payload, bool _payInZRO, bytes memory _adapterParams) public view override returns (uint nativeFee, uint zroFee) {

205:     function estimateFees(uint16 _dstChainId, address _userApplication, bytes memory _payload, bool _payInZRO, bytes memory _adapterParams) public view override returns (uint nativeFee, uint zroFee) {

286:         bytes memory /*_config*/

346:     function setDefaultAdapterParams(bytes memory _adapterParams) external {

```

### <a name="GAS-5"></a>[GAS-5] For Operations that will not overflow, you could use unchecked

*Instances (767)*:
```solidity
File: OFT/BaseOFTV2.sol

5: import "./OFTCoreV2.sol";

6: import "./IOFTV2.sol";

7: import "@openzeppelin/contracts/utils/introspection/ERC165.sol";

7: import "@openzeppelin/contracts/utils/introspection/ERC165.sol";

7: import "@openzeppelin/contracts/utils/introspection/ERC165.sol";

7: import "@openzeppelin/contracts/utils/introspection/ERC165.sol";

```

```solidity
File: OFT/ICommonOFT.sol

5: import "@openzeppelin/contracts/utils/introspection/IERC165.sol";

5: import "@openzeppelin/contracts/utils/introspection/IERC165.sol";

5: import "@openzeppelin/contracts/utils/introspection/IERC165.sol";

5: import "@openzeppelin/contracts/utils/introspection/IERC165.sol";

```

```solidity
File: OFT/IOFTV2.sol

5: import "./ICommonOFT.sol";

```

```solidity
File: OFT/OFTCoreV2.sol

5: import "./lzApp/NonblockingLzApp.sol";

5: import "./lzApp/NonblockingLzApp.sol";

6: import "./util/ExcessivelySafeCall.sol";

6: import "./util/ExcessivelySafeCall.sol";

7: import "./ICommonOFT.sol";

8: import "./IOFTReceiverV2.sol";

98:         amount = _debitFrom(_from, _dstChainId, _toAddress, amount); // amount returned should not have dust

98:         amount = _debitFrom(_from, _dstChainId, _toAddress, amount); // amount returned should not have dust

186:         uint amountSD = _amount / _ld2sdRatio();

192:         return _amountSD * _ld2sdRatio();

197:         amountAfter = _amount - dust;

207:         to = _payload.toAddress(13); // drop the first 12 bytes of bytes32

207:         to = _payload.toAddress(13); // drop the first 12 bytes of bytes32

225:         to = _payload.toAddress(13); // drop the first 12 bytes of bytes32

225:         to = _payload.toAddress(13); // drop the first 12 bytes of bytes32

229:         payload = _payload.slice(81, _payload.length - 81);

```

```solidity
File: OFT/interfaces/ILayerZeroEndpoint.sol

5: import "./ILayerZeroUserApplicationConfig.sol";

```

```solidity
File: OFT/interfaces/IStargateFactory.sol

4: import "./IStargatePool.sol";

```

```solidity
File: OFT/interfaces/IStargateWidget.sol

6: import "../interfaces/IStargateRouter.sol";

6: import "../interfaces/IStargateRouter.sol";

10:         uint256 tenthBps; // bps is to an extra decimal place

10:         uint256 tenthBps; // bps is to an extra decimal place

```

```solidity
File: OFT/lzApp/LzApp.sol

5: import "@openzeppelin/contracts/access/Ownable.sol";

5: import "@openzeppelin/contracts/access/Ownable.sol";

5: import "@openzeppelin/contracts/access/Ownable.sol";

6: import "../interfaces/ILayerZeroReceiver.sol";

6: import "../interfaces/ILayerZeroReceiver.sol";

7: import "../interfaces/ILayerZeroUserApplicationConfig.sol";

7: import "../interfaces/ILayerZeroUserApplicationConfig.sol";

8: import "../interfaces/ILayerZeroEndpoint.sol";

8: import "../interfaces/ILayerZeroEndpoint.sol";

9: import "../util/BytesLib.sol";

9: import "../util/BytesLib.sol";

58:         uint minGasLimit = minDstGasLookup[_dstChainId][_type] + _extraGas;

72:         if (payloadSizeLimit == 0) { // use default if not set

72:         if (payloadSizeLimit == 0) { // use default if not set

115:         return path.slice(0, path.length - 20); // the last 20 bytes should be address(this)

115:         return path.slice(0, path.length - 20); // the last 20 bytes should be address(this)

115:         return path.slice(0, path.length - 20); // the last 20 bytes should be address(this)

```

```solidity
File: OFT/lzApp/NonblockingLzApp.sol

5: import "./LzApp.sol";

6: import "../util/ExcessivelySafeCall.sol";

6: import "../util/ExcessivelySafeCall.sol";

```

```solidity
File: OFT/util/BitLib.sol

23:             r += 128;

27:             r += 64;

31:             r += 32;

35:             r += 16;

39:             r += 8;

43:             r += 4;

47:             r += 2;

49:         if (x >= 0x2) r += 1;

53:         x = (x & m1 ) + ((x >>  1) & m1 );

54:         x = (x & m2 ) + ((x >>  2) & m2 );

55:         x = (x & m4 ) + ((x >>  4) & m4 );

56:         x = (x & m8 ) + ((x >>  8) & m8 );

57:         x = (x & m16) + ((x >> 16) & m16);

58:         x = (x & m32) + ((x >> 32) & m32);

59:         x = (x & m64) + ((x >> 64) & m64);

60:         x = (x & m128) + ((x >> 128) & m128);

```

```solidity
File: OFT/util/BytesLib.sol

84:             not(31) // Round down to the nearest 32 bytes.

84:             not(31) // Round down to the nearest 32 bytes.

237:         require(_length + 31 >= _length, "slice_overflow");

238:         require(_bytes.length >= _start + _length, "slice_outOfBounds");

298:         require(_bytes.length >= _start + 20, "toAddress_outOfBounds");

309:         require(_bytes.length >= _start + 1 , "toUint8_outOfBounds");

320:         require(_bytes.length >= _start + 2, "toUint16_outOfBounds");

331:         require(_bytes.length >= _start + 4, "toUint32_outOfBounds");

342:         require(_bytes.length >= _start + 8, "toUint64_outOfBounds");

353:         require(_bytes.length >= _start + 12, "toUint96_outOfBounds");

364:         require(_bytes.length >= _start + 16, "toUint128_outOfBounds");

375:         require(_bytes.length >= _start + 32, "toUint256_outOfBounds");

386:         require(_bytes.length >= _start + 32, "toBytes32_outOfBounds");

```

```solidity
File: OFT/util/ExcessivelySafeCall.sol

39:             _gas, // gas

39:             _gas, // gas

40:             _target, // recipient

40:             _target, // recipient

41:             0, // ether value

41:             0, // ether value

42:             add(_calldata, 0x20), // inloc

42:             add(_calldata, 0x20), // inloc

43:             mload(_calldata), // inlen

43:             mload(_calldata), // inlen

44:             0, // outloc

44:             0, // outloc

45:             0 // outlen

45:             0 // outlen

91:             _gas, // gas

91:             _gas, // gas

92:             _target, // recipient

92:             _target, // recipient

93:             add(_calldata, 0x20), // inloc

93:             add(_calldata, 0x20), // inloc

94:             mload(_calldata), // inlen

94:             mload(_calldata), // inlen

95:             0, // outloc

95:             0, // outloc

96:             0 // outlen

96:             0 // outlen

```

```solidity
File: lybra/Proxy/LybraProxy.sol

5: import "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

5: import "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

5: import "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

5: import "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

```

```solidity
File: lybra/Proxy/LybraProxyAdmin.sol

4: import "@openzeppelin/contracts/proxy/transparent/ProxyAdmin.sol";

4: import "@openzeppelin/contracts/proxy/transparent/ProxyAdmin.sol";

4: import "@openzeppelin/contracts/proxy/transparent/ProxyAdmin.sol";

4: import "@openzeppelin/contracts/proxy/transparent/ProxyAdmin.sol";

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

17: import "../interfaces/IGovernanceTimelock.sol";

17: import "../interfaces/IGovernanceTimelock.sol";

18: import "../interfaces/IEUSD.sol";

18: import "../interfaces/IEUSD.sol";

127:         require(newRatio >= 130 * 1e18 && newRatio <= 150 * 1e18 && newRatio <= vaultSafeCollateralRatio[pool] + 1e19, "LNA");

127:         require(newRatio >= 130 * 1e18 && newRatio <= 150 * 1e18 && newRatio <= vaultSafeCollateralRatio[pool] + 1e19, "LNA");

127:         require(newRatio >= 130 * 1e18 && newRatio <= 150 * 1e18 && newRatio <= vaultSafeCollateralRatio[pool] + 1e19, "LNA");

200:             require(newRatio >= 160 * 1e18, "eUSD vault safe collateralRatio should more than 160%");

202:             require(newRatio >= vaultBadCollateralRatio[pool] + 1e19, "PeUSD vault safe collateralRatio should more than bad collateralRatio");

236:         for (uint256 i = 0; i < _contracts.length; i++) {

236:         for (uint256 i = 0; i < _contracts.length; i++) {

303:                 uint256 amount = curvePool.exchange_underlying(0, 2, balance, balance * price * 998 / 1e21);

303:                 uint256 amount = curvePool.exchange_underlying(0, 2, balance, balance * price * 998 / 1e21);

303:                 uint256 amount = curvePool.exchange_underlying(0, 2, balance, balance * price * 998 / 1e21);

334:         if (vaultSafeCollateralRatio[pool] == 0) return 160 * 1e18;

339:         if(vaultBadCollateralRatio[pool] == 0) return vaultSafeCollateralRatio[pool] - 1e19;

357:         return (EUSD.totalSupply() * maxStableRatio) / 10_000;

357:         return (EUSD.totalSupply() * maxStableRatio) / 10_000;

```

```solidity
File: lybra/governance/AdminTimelock.sol

5: import "@openzeppelin/contracts/governance/TimelockController.sol";

5: import "@openzeppelin/contracts/governance/TimelockController.sol";

5: import "@openzeppelin/contracts/governance/TimelockController.sol";

```

```solidity
File: lybra/governance/GovernanceTimelock.sol

5: import "@openzeppelin/contracts/governance/TimelockController.sol";

5: import "@openzeppelin/contracts/governance/TimelockController.sol";

5: import "@openzeppelin/contracts/governance/TimelockController.sol";

```

```solidity
File: lybra/governance/LybraGovernance.sol

5: import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";

5: import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";

5: import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";

5: import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";

6: import "../interfaces/IesLBR.sol";

6: import "../interfaces/IesLBR.sol";

7: import "../interfaces/IGovernanceTimelock.sol";

7: import "../interfaces/IGovernanceTimelock.sol";

52:         return esLBR.getPastTotalSupply(timepoint) / 3;

60:         return proposalData[proposalId].supportVotes[1] + proposalData[proposalId].supportVotes[2] >= quorum(proposalSnapshot(proposalId));

84:         proposalExtraData.supportVotes[support] += weight;

90:         proposalExtraData.totalVotes += weight;

106:     function _execute(uint256 /* proposalId */, address[] memory targets, uint256[] memory values, bytes[] memory calldatas, bytes32 descriptionHash) internal virtual override {

106:     function _execute(uint256 /* proposalId */, address[] memory targets, uint256[] memory values, bytes[] memory calldatas, bytes32 descriptionHash) internal virtual override {

106:     function _execute(uint256 /* proposalId */, address[] memory targets, uint256[] memory values, bytes[] memory calldatas, bytes32 descriptionHash) internal virtual override {

106:     function _execute(uint256 /* proposalId */, address[] memory targets, uint256[] memory values, bytes[] memory calldatas, bytes32 descriptionHash) internal virtual override {

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

12: import "@openzeppelin/contracts/access/Ownable.sol";

12: import "@openzeppelin/contracts/access/Ownable.sol";

12: import "@openzeppelin/contracts/access/Ownable.sol";

13: import "../interfaces/IesLBR.sol";

13: import "../interfaces/IesLBR.sol";

14: import "../interfaces/IEUSD.sol";

14: import "../interfaces/IEUSD.sol";

15: import "../interfaces/ILybra.sol";

15: import "../interfaces/ILybra.sol";

16: import "../interfaces/Iconfigurator.sol";

16: import "../interfaces/Iconfigurator.sol";

17: import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

17: import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

17: import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

17: import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

17: import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

50:     uint256 public extraRatio = 50 * 1e18;

51:     uint256 public peUSDExtraRatio = 10 * 1e18;

94:         for (uint i = 0; i < _pools.length; i++) {

94:         for (uint i = 0; i < _pools.length; i++) {

138:         for (uint i = 0; i < pools.length; i++) {

138:         for (uint i = 0; i < pools.length; i++) {

142:                 borrowed = borrowed * (1e20 + peUSDExtraRatio) / 1e20;

142:                 borrowed = borrowed * (1e20 + peUSDExtraRatio) / 1e20;

142:                 borrowed = borrowed * (1e20 + peUSDExtraRatio) / 1e20;

144:             amount += borrowed;

153:         uint256 etherInLp = (IEUSD(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2).balanceOf(ethlbrLpToken) * uint(etherPrice)) / 1e8;

153:         uint256 etherInLp = (IEUSD(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2).balanceOf(ethlbrLpToken) * uint(etherPrice)) / 1e8;

154:         uint256 lbrInLp = (IEUSD(LBR).balanceOf(ethlbrLpToken) * uint(lbrPrice)) / 1e8;

154:         uint256 lbrInLp = (IEUSD(LBR).balanceOf(ethlbrLpToken) * uint(lbrPrice)) / 1e8;

156:         return (userStaked * (lbrInLp + etherInLp)) / totalLp;

156:         return (userStaked * (lbrInLp + etherInLp)) / totalLp;

156:         return (userStaked * (lbrInLp + etherInLp)) / totalLp;

168:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalStaked();

168:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalStaked();

168:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalStaked();

168:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalStaked();

168:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalStaked();

181:         return 100 * 1e18 + redemptionBoost + esLBRBoost.getUserBoost(_account, userUpdatedAt[_account], finishAt);

181:         return 100 * 1e18 + redemptionBoost + esLBRBoost.getUserBoost(_account, userUpdatedAt[_account], finishAt);

181:         return 100 * 1e18 + redemptionBoost + esLBRBoost.getUserBoost(_account, userUpdatedAt[_account], finishAt);

185:         return ((stakedOf(_account) * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

185:         return ((stakedOf(_account) * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

185:         return ((stakedOf(_account) * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

185:         return ((stakedOf(_account) * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

185:         return ((stakedOf(_account) * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

189:         return (stakedLBRLpValue(user) * 10000) / stakedOf(user) < 500;

189:         return (stakedLBRLpValue(user) * 10000) / stakedOf(user) < 500;

210:             uint256 biddingFee = (reward * biddingFeeRatio) / 10000;

210:             uint256 biddingFee = (reward * biddingFeeRatio) / 10000;

213:                 biddingFee = biddingFee * uint256(lbrPrice) / 1e8;

213:                 biddingFee = biddingFee * uint256(lbrPrice) / 1e8;

231:             rewardRatio = amount / duration;

233:             uint256 remainingRewards = (finishAt - block.timestamp) * rewardRatio;

233:             uint256 remainingRewards = (finishAt - block.timestamp) * rewardRatio;

234:             rewardRatio = (amount + remainingRewards) / duration;

234:             rewardRatio = (amount + remainingRewards) / duration;

239:         finishAt = block.timestamp + duration;

```

```solidity
File: lybra/miner/ProtocolRewardsPool.sol

12: import "@openzeppelin/contracts/access/Ownable.sol";

12: import "@openzeppelin/contracts/access/Ownable.sol";

12: import "@openzeppelin/contracts/access/Ownable.sol";

13: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

13: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

13: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

13: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

14: import "../interfaces/IEUSD.sol";

14: import "../interfaces/IEUSD.sol";

15: import "../interfaces/Iconfigurator.sol";

15: import "../interfaces/Iconfigurator.sol";

16: import "../interfaces/IesLBR.sol";

16: import "../interfaces/IesLBR.sol";

88:         require(block.timestamp >= esLBRBoost.getUnlockTime(msg.sender), "Your lock-in period has not ended. You can't convert your esLBR now.");

93:             total += unstakeRatio[msg.sender] * (time2fullRedemption[msg.sender] - block.timestamp);

93:             total += unstakeRatio[msg.sender] * (time2fullRedemption[msg.sender] - block.timestamp);

93:             total += unstakeRatio[msg.sender] * (time2fullRedemption[msg.sender] - block.timestamp);

95:         unstakeRatio[msg.sender] = total / exitCycle;

96:         time2fullRedemption[msg.sender] = block.timestamp + exitCycle;

114:         require(block.timestamp + exitCycle - 3 days > time2fullRedemption[msg.sender], "ENW");

114:         require(block.timestamp + exitCycle - 3 days > time2fullRedemption[msg.sender], "ENW");

115:         uint256 burnAmount = getReservedLBRForVesting(msg.sender) - getPreUnlockableAmount(msg.sender);

116:         uint256 amount = getPreUnlockableAmount(msg.sender) + getClaimAbleLBR(msg.sender);

122:         grabableAmount += burnAmount;

133:         grabableAmount -= amount;

134:         LBR.burn(msg.sender, (amount * grabFeeRatio) / 10000);

134:         LBR.burn(msg.sender, (amount * grabFeeRatio) / 10000);

142:         uint256 amount = getReservedLBRForVesting(msg.sender) + getClaimAbleLBR(msg.sender);

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

152:         amount = (a * (75e18 - ((time2fullRedemption[user] - block.timestamp) * 70e18) / (exitCycle / 1 days - 3) / 1 days)) / 100e18;

157:             amount = block.timestamp > time2fullRedemption[user] ? unstakeRatio[user] * (time2fullRedemption[user] - lastWithdrawTime[user]) : unstakeRatio[user] * (block.timestamp - lastWithdrawTime[user]);

157:             amount = block.timestamp > time2fullRedemption[user] ? unstakeRatio[user] * (time2fullRedemption[user] - lastWithdrawTime[user]) : unstakeRatio[user] * (block.timestamp - lastWithdrawTime[user]);

157:             amount = block.timestamp > time2fullRedemption[user] ? unstakeRatio[user] * (time2fullRedemption[user] - lastWithdrawTime[user]) : unstakeRatio[user] * (block.timestamp - lastWithdrawTime[user]);

157:             amount = block.timestamp > time2fullRedemption[user] ? unstakeRatio[user] * (time2fullRedemption[user] - lastWithdrawTime[user]) : unstakeRatio[user] * (block.timestamp - lastWithdrawTime[user]);

163:             amount = unstakeRatio[user] * (time2fullRedemption[user] - block.timestamp);

163:             amount = unstakeRatio[user] * (time2fullRedemption[user] - block.timestamp);

168:         return ((stakedOf(_account) * (rewardPerTokenStored - userRewardPerTokenPaid[_account])) / 1e18) + rewards[_account];

168:         return ((stakedOf(_account) * (rewardPerTokenStored - userRewardPerTokenPaid[_account])) / 1e18) + rewards[_account];

168:         return ((stakedOf(_account) * (rewardPerTokenStored - userRewardPerTokenPaid[_account])) / 1e18) + rewards[_account];

168:         return ((stakedOf(_account) * (rewardPerTokenStored - userRewardPerTokenPaid[_account])) / 1e18) + rewards[_account];

196:             uint256 eUSDShare = balance >= reward ? reward : reward - balance;

201:                 if(peUSDBalance >= reward - eUSDShare) {

202:                     peUSD.transfer(msg.sender, reward - eUSDShare);

203:                     emit ClaimReward(msg.sender, EUSD.getMintedEUSDByShares(eUSDShare), address(peUSD), reward - eUSDShare, block.timestamp);

209:                     uint256 tokenAmount = (reward - eUSDShare - peUSDBalance) * token.decimals() / 1e18;

209:                     uint256 tokenAmount = (reward - eUSDShare - peUSDBalance) * token.decimals() / 1e18;

209:                     uint256 tokenAmount = (reward - eUSDShare - peUSDBalance) * token.decimals() / 1e18;

209:                     uint256 tokenAmount = (reward - eUSDShare - peUSDBalance) * token.decimals() / 1e18;

211:                     emit ClaimReward(msg.sender, EUSD.getMintedEUSDByShares(eUSDShare), address(token), reward - eUSDShare, block.timestamp);

233:             rewardPerTokenStored = rewardPerTokenStored + (share * 1e18) / totalStaked();

233:             rewardPerTokenStored = rewardPerTokenStored + (share * 1e18) / totalStaked();

233:             rewardPerTokenStored = rewardPerTokenStored + (share * 1e18) / totalStaked();

236:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e36 / token.decimals()) / totalStaked();

236:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e36 / token.decimals()) / totalStaked();

236:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e36 / token.decimals()) / totalStaked();

236:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e36 / token.decimals()) / totalStaked();

238:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e18) / totalStaked();

238:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e18) / totalStaked();

238:             rewardPerTokenStored = rewardPerTokenStored + (amount * 1e18) / totalStaked();

```

```solidity
File: lybra/miner/esLBRBoost.sol

5: import "@openzeppelin/contracts/access/Ownable.sol";

5: import "@openzeppelin/contracts/access/Ownable.sol";

5: import "@openzeppelin/contracts/access/Ownable.sol";

26:         esLBRLockSettings.push(esLBRLockSetting(30 days, 20 * 1e18));

27:         esLBRLockSettings.push(esLBRLockSetting(90 days, 30 * 1e18));

28:         esLBRLockSettings.push(esLBRLockSetting(180 days, 50 * 1e18));

29:         esLBRLockSettings.push(esLBRLockSetting(365 days, 100 * 1e18));

42:             require(userStatus.duration <= _setting.duration, "Your lock-in period has not ended, and the term can only be extended, not reduced.");

44:         userLockStatus[msg.sender] = LockStatus(block.timestamp + _setting.duration, _setting.duration, _setting.miningBoost);

67:             return ((boostEndTime - userUpdatedAt) * maxBoost) / (time - userUpdatedAt);

67:             return ((boostEndTime - userUpdatedAt) * maxBoost) / (time - userUpdatedAt);

67:             return ((boostEndTime - userUpdatedAt) * maxBoost) / (time - userUpdatedAt);

67:             return ((boostEndTime - userUpdatedAt) * maxBoost) / (time - userUpdatedAt);

```

```solidity
File: lybra/miner/stakerewardV2pool.sol

4: import "@openzeppelin/contracts/access/Ownable.sol";

4: import "@openzeppelin/contracts/access/Ownable.sol";

4: import "@openzeppelin/contracts/access/Ownable.sol";

5: import "../interfaces/IesLBR.sol";

5: import "../interfaces/IesLBR.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

79:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalSupply;

79:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalSupply;

79:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalSupply;

79:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalSupply;

79:         return rewardPerTokenStored + (rewardRatio * (lastTimeRewardApplicable() - updatedAt) * 1e18) / totalSupply;

87:         balanceOf[msg.sender] += _amount;

88:         totalSupply += _amount;

95:         balanceOf[msg.sender] -= _amount;

96:         totalSupply -= _amount;

102:         return 100 * 1e18 + esLBRBoost.getUserBoost(_account, userUpdatedAt[_account], finishAt);

102:         return 100 * 1e18 + esLBRBoost.getUserBoost(_account, userUpdatedAt[_account], finishAt);

107:         return ((balanceOf[_account] * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

107:         return ((balanceOf[_account] * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

107:         return ((balanceOf[_account] * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

107:         return ((balanceOf[_account] * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

107:         return ((balanceOf[_account] * getBoost(_account) * (rewardPerToken() - userRewardPerTokenPaid[_account])) / 1e38) + rewards[_account];

134:             rewardRatio = _amount / duration;

136:             uint256 remainingRewards = (finishAt - block.timestamp) * rewardRatio;

136:             uint256 remainingRewards = (finishAt - block.timestamp) * rewardRatio;

137:             rewardRatio = (_amount + remainingRewards) / duration;

137:             rewardRatio = (_amount + remainingRewards) / duration;

142:         finishAt = block.timestamp + duration;

```

```solidity
File: lybra/pools/LybraRETHVault.sol

5: import "../interfaces/IEUSD.sol";

5: import "../interfaces/IEUSD.sol";

6: import "./base/LybraPeUSDVaultBase.sol";

6: import "./base/LybraPeUSDVaultBase.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

32:         depositedAsset[msg.sender] += balance - preBalance;

32:         depositedAsset[msg.sender] += balance - preBalance;

38:         emit DepositEther(msg.sender, address(collateralAsset), msg.value,balance - preBalance, block.timestamp);

47:         return (_etherPrice() * IRETH(address(collateralAsset)).getExchangeRatio()) / 1e18;

47:         return (_etherPrice() * IRETH(address(collateralAsset)).getExchangeRatio()) / 1e18;

```

```solidity
File: lybra/pools/LybraStETHVault.sol

5: import "../interfaces/IEUSD.sol";

5: import "../interfaces/IEUSD.sol";

6: import "./base/LybraEUSDVaultBase.sol";

6: import "./base/LybraEUSDVaultBase.sol";

43:         totalDepositedAsset += msg.value;

44:         depositedAsset[msg.sender] += msg.value;

63:         uint256 excessAmount = collateralAsset.balanceOf(address(this)) - totalDepositedAsset;

66:         uint256 payAmount = (((realAmount * getAssetPrice()) / 1e18) * getDutchAuctionDiscountPrice()) / 10000;

66:         uint256 payAmount = (((realAmount * getAssetPrice()) / 1e18) * getDutchAuctionDiscountPrice()) / 10000;

66:         uint256 payAmount = (((realAmount * getAssetPrice()) / 1e18) * getDutchAuctionDiscountPrice()) / 10000;

66:         uint256 payAmount = (((realAmount * getAssetPrice()) / 1e18) * getDutchAuctionDiscountPrice()) / 10000;

68:         uint256 income = feeStored + _newFee();

75:             uint256 sharesAmount = EUSD.getSharesByMintedEUSD(payAmount - income);

78:                 sharesAmount = (payAmount - income);

88:             feeStored = income - payAmount;

102:         uint256 time = (block.timestamp - lidoRebaseTime) % 1 days;

104:         return 10000 - (time / 30 minutes - 1) * 100;

104:         return 10000 - (time / 30 minutes - 1) * 100;

104:         return 10000 - (time / 30 minutes - 1) * 100;

104:         return 10000 - (time / 30 minutes - 1) * 100;

```

```solidity
File: lybra/pools/LybraWbETHVault.sol

5: import "../interfaces/IEUSD.sol";

5: import "../interfaces/IEUSD.sol";

6: import "./base/LybraPeUSDVaultBase.sol";

6: import "./base/LybraPeUSDVaultBase.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

25:         depositedAsset[msg.sender] += balance - preBalance;

25:         depositedAsset[msg.sender] += balance - preBalance;

31:         emit DepositEther(msg.sender, address(collateralAsset), msg.value,balance - preBalance, block.timestamp);

35:         return (_etherPrice() * IWBETH(address(collateralAsset)).exchangeRatio()) / 1e18;

35:         return (_etherPrice() * IWBETH(address(collateralAsset)).exchangeRatio()) / 1e18;

```

```solidity
File: lybra/pools/LybraWstETHVault.sol

5: import "../interfaces/IEUSD.sol";

5: import "../interfaces/IEUSD.sol";

6: import "./base/LybraPeUSDVaultBase.sol";

6: import "./base/LybraPeUSDVaultBase.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

39:         depositedAsset[msg.sender] += wstETHAmount;

49:         return (_etherPrice() * IWstETH(address(collateralAsset)).stEthPerToken()) / 1e18;

49:         return (_etherPrice() * IWstETH(address(collateralAsset)).stEthPerToken()) / 1e18;

```

```solidity
File: lybra/pools/base/LybraEUSDVaultBase.sol

5: import "../../interfaces/IEUSD.sol";

5: import "../../interfaces/IEUSD.sol";

5: import "../../interfaces/IEUSD.sol";

6: import "../../interfaces/Iconfigurator.sol";

6: import "../../interfaces/Iconfigurator.sol";

6: import "../../interfaces/Iconfigurator.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

21:     uint256 public immutable badCollateralRatio = 150 * 1e18;

78:         totalDepositedAsset += assetAmount;

79:         depositedAsset[msg.sender] += assetAmount;

102:         totalDepositedAsset -= amount;

103:         depositedAsset[msg.sender] -= amount;

115:         withdrawal = block.timestamp - 3 days >= depositedTime[user] ? amount : (amount * 999) / 1000;

115:         withdrawal = block.timestamp - 3 days >= depositedTime[user] ? amount : (amount * 999) / 1000;

115:         withdrawal = block.timestamp - 3 days >= depositedTime[user] ? amount : (amount * 999) / 1000;

156:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / borrowed[onBehalfOf];

156:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / borrowed[onBehalfOf];

156:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / borrowed[onBehalfOf];

159:         require(assetAmount * 2 <= depositedAsset[onBehalfOf], "a max of 50% collateral can be liquidated");

161:         uint256 eusdAmount = (assetAmount * assetPrice) / 1e18;

161:         uint256 eusdAmount = (assetAmount * assetPrice) / 1e18;

164:         uint256 reducedAsset = (assetAmount * 11) / 10;

164:         uint256 reducedAsset = (assetAmount * 11) / 10;

165:         totalDepositedAsset -= reducedAsset;

166:         depositedAsset[onBehalfOf] -= reducedAsset;

171:             reward2keeper = (reducedAsset * configurator.vaultKeeperRatio(address(this))) / 110;

171:             reward2keeper = (reducedAsset * configurator.vaultKeeperRatio(address(this))) / 110;

172:             collateralAsset.transfer(provider, reducedAsset - reward2keeper);

189:         require((totalDepositedAsset * assetPrice * 100) / poolTotalEUSDCirculation < badCollateralRatio, "overallCollateralRatio should below 150%");

189:         require((totalDepositedAsset * assetPrice * 100) / poolTotalEUSDCirculation < badCollateralRatio, "overallCollateralRatio should below 150%");

189:         require((totalDepositedAsset * assetPrice * 100) / poolTotalEUSDCirculation < badCollateralRatio, "overallCollateralRatio should below 150%");

190:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / borrowed[onBehalfOf];

190:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / borrowed[onBehalfOf];

190:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / borrowed[onBehalfOf];

191:         require(onBehalfOfCollateralRatio < 125 * 1e18, "borrowers collateralRatio should below 125%");

193:         uint256 eusdAmount = (assetAmount * assetPrice) / 1e18;

193:         uint256 eusdAmount = (assetAmount * assetPrice) / 1e18;

195:             eusdAmount = (eusdAmount * 1e20) / onBehalfOfCollateralRatio;

195:             eusdAmount = (eusdAmount * 1e20) / onBehalfOfCollateralRatio;

201:         totalDepositedAsset -= assetAmount;

202:         depositedAsset[onBehalfOf] -= assetAmount;

204:         if (msg.sender != provider && onBehalfOfCollateralRatio >= 1e20 + configurator.vaultKeeperRatio(address(this)) * 1e18) {

204:         if (msg.sender != provider && onBehalfOfCollateralRatio >= 1e20 + configurator.vaultKeeperRatio(address(this)) * 1e18) {

205:             reward2keeper = ((assetAmount * configurator.vaultKeeperRatio(address(this))) * 1e18) / onBehalfOfCollateralRatio;

205:             reward2keeper = ((assetAmount * configurator.vaultKeeperRatio(address(this))) * 1e18) / onBehalfOfCollateralRatio;

205:             reward2keeper = ((assetAmount * configurator.vaultKeeperRatio(address(this))) * 1e18) / onBehalfOfCollateralRatio;

208:         collateralAsset.transfer(provider, assetAmount - reward2keeper);

236:         uint256 providerCollateralRatio = (depositedAsset[provider] * assetPrice * 100) / borrowed[provider];

236:         uint256 providerCollateralRatio = (depositedAsset[provider] * assetPrice * 100) / borrowed[provider];

236:         uint256 providerCollateralRatio = (depositedAsset[provider] * assetPrice * 100) / borrowed[provider];

237:         require(providerCollateralRatio >= 100 * 1e18, "provider's collateral ratio should more than 100%");

239:         uint256 collateralAmount = (((eusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

239:         uint256 collateralAmount = (((eusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

239:         uint256 collateralAmount = (((eusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

239:         uint256 collateralAmount = (((eusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

239:         uint256 collateralAmount = (((eusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

240:         depositedAsset[provider] -= collateralAmount;

241:         totalDepositedAsset -= collateralAmount;

260:         require(poolTotalEUSDCirculation + _mintAmount <= configurator.mintVaultMaxSupply(address(this)), "ESL");

262:         borrowed[_provider] += _mintAmount;

266:         poolTotalEUSDCirculation += _mintAmount;

282:         borrowed[_onBehalfOf] -= amount;

284:         poolTotalEUSDCirculation -= amount;

292:         if (((depositedAsset[_user] * _assetPrice * 100) / borrowed[_user]) < configurator.getSafeCollateralRatio(address(this))) revert("collateralRatio is Below safeCollateralRatio");

292:         if (((depositedAsset[_user] * _assetPrice * 100) / borrowed[_user]) < configurator.getSafeCollateralRatio(address(this))) revert("collateralRatio is Below safeCollateralRatio");

292:         if (((depositedAsset[_user] * _assetPrice * 100) / borrowed[_user]) < configurator.getSafeCollateralRatio(address(this))) revert("collateralRatio is Below safeCollateralRatio");

296:         feeStored += _newFee();

301:         return (poolTotalEUSDCirculation * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - lastReportTime)) / (86400 * 365) / 10000;

301:         return (poolTotalEUSDCirculation * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - lastReportTime)) / (86400 * 365) / 10000;

301:         return (poolTotalEUSDCirculation * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - lastReportTime)) / (86400 * 365) / 10000;

301:         return (poolTotalEUSDCirculation * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - lastReportTime)) / (86400 * 365) / 10000;

301:         return (poolTotalEUSDCirculation * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - lastReportTime)) / (86400 * 365) / 10000;

301:         return (poolTotalEUSDCirculation * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - lastReportTime)) / (86400 * 365) / 10000;

```

```solidity
File: lybra/pools/base/LybraPeUSDVaultBase.sol

5: import "../../interfaces/Iconfigurator.sol";

5: import "../../interfaces/Iconfigurator.sol";

5: import "../../interfaces/Iconfigurator.sol";

6: import "../../interfaces/IPeUSD.sol";

6: import "../../interfaces/IPeUSD.sol";

6: import "../../interfaces/IPeUSD.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

62:         require(collateralAsset.balanceOf(address(this)) >= preBalance + assetAmount, "");

64:         depositedAsset[msg.sender] += assetAmount;

127:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / getBorrowedOf(onBehalfOf);

127:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / getBorrowedOf(onBehalfOf);

127:         uint256 onBehalfOfCollateralRatio = (depositedAsset[onBehalfOf] * assetPrice * 100) / getBorrowedOf(onBehalfOf);

130:         require(assetAmount * 2 <= depositedAsset[onBehalfOf], "a max of 50% collateral can be liquidated");

132:         uint256 peusdAmount = (assetAmount * assetPrice) / 1e18;

132:         uint256 peusdAmount = (assetAmount * assetPrice) / 1e18;

135:         uint256 reducedAsset = (assetAmount * 11) / 10;

135:         uint256 reducedAsset = (assetAmount * 11) / 10;

136:         depositedAsset[onBehalfOf] -= reducedAsset;

141:             reward2keeper = (reducedAsset * configurator.vaultKeeperRatio(address(this))) / 110;

141:             reward2keeper = (reducedAsset * configurator.vaultKeeperRatio(address(this))) / 110;

142:             collateralAsset.transfer(provider, reducedAsset - reward2keeper);

161:         uint256 providerCollateralRatio = (depositedAsset[provider] * assetPrice * 100) / borrowed[provider];

161:         uint256 providerCollateralRatio = (depositedAsset[provider] * assetPrice * 100) / borrowed[provider];

161:         uint256 providerCollateralRatio = (depositedAsset[provider] * assetPrice * 100) / borrowed[provider];

162:         require(providerCollateralRatio >= 100 * 1e18, "provider's collateral ratio should more than 100%");

164:         uint256 collateralAmount = (((peusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

164:         uint256 collateralAmount = (((peusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

164:         uint256 collateralAmount = (((peusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

164:         uint256 collateralAmount = (((peusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

164:         uint256 collateralAmount = (((peusdAmount * 1e18) / assetPrice) * (10000 - configurator.redemptionFee())) / 10000;

165:         depositedAsset[provider] -= collateralAmount;

174:         require(poolTotalPeUSDCirculation + _mintAmount <= configurator.mintVaultMaxSupply(address(this)), "ESL");

179:         borrowed[_provider] += _mintAmount;

182:         poolTotalPeUSDCirculation += _mintAmount;

196:         uint256 amount = borrowed[_onBehalfOf] + totalFee >= _amount ? _amount : borrowed[_onBehalfOf] + totalFee;

196:         uint256 amount = borrowed[_onBehalfOf] + totalFee >= _amount ? _amount : borrowed[_onBehalfOf] + totalFee;

200:             PeUSD.burn(_provider, amount - totalFee);

202:             feeStored[_onBehalfOf] = totalFee - amount;

206:         borrowed[_onBehalfOf] -= amount;

207:         poolTotalPeUSDCirculation -= amount;

214:         depositedAsset[_provider] -= _amount;

226:         if (((depositedAsset[user] * price * 100) / getBorrowedOf(user)) < configurator.getSafeCollateralRatio(address(this))) 

226:         if (((depositedAsset[user] * price * 100) / getBorrowedOf(user)) < configurator.getSafeCollateralRatio(address(this))) 

226:         if (((depositedAsset[user] * price * 100) / getBorrowedOf(user)) < configurator.getSafeCollateralRatio(address(this))) 

232:             feeStored[user] += _newFee(user);

238:         return (borrowed[user] * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - feeUpdatedAt[user])) / (86400 * 365) / 10000;

238:         return (borrowed[user] * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - feeUpdatedAt[user])) / (86400 * 365) / 10000;

238:         return (borrowed[user] * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - feeUpdatedAt[user])) / (86400 * 365) / 10000;

238:         return (borrowed[user] * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - feeUpdatedAt[user])) / (86400 * 365) / 10000;

238:         return (borrowed[user] * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - feeUpdatedAt[user])) / (86400 * 365) / 10000;

238:         return (borrowed[user] * configurator.vaultMintFeeApy(address(this)) * (block.timestamp - feeUpdatedAt[user])) / (86400 * 365) / 10000;

254:         return borrowed[user] + feeStored[user] + _newFee(user);

254:         return borrowed[user] + feeStored[user] + _newFee(user);

```

```solidity
File: lybra/token/EUSD.sol

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/utils/Context.sol";

7: import "@openzeppelin/contracts/utils/Context.sol";

7: import "@openzeppelin/contracts/utils/Context.sol";

8: import "../interfaces/Iconfigurator.sol";

8: import "../interfaces/Iconfigurator.sol";

182:                 _approve(owner, spender, currentAllowance - amount);

273:             _approve(owner, spender, currentAllowance - subtractedValue);

425:         _totalSupply += _mintAmount;

444:         _totalSupply -= _burnAmount;

```

```solidity
File: lybra/token/LBR.sol

9: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

9: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

9: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

9: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

10: import "../interfaces/Iconfigurator.sol";

10: import "../interfaces/Iconfigurator.sol";

11: import "../../OFT/BaseOFTV2.sol";

11: import "../../OFT/BaseOFTV2.sol";

11: import "../../OFT/BaseOFTV2.sol";

15:     uint256 maxSupply = 100_000_000 * 1e18;

22:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

22:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

22:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

27:         require(totalSupply() + amount <= maxSupply, "exceeding the maximum supply quantity.");

```

```solidity
File: lybra/token/PeUSD.sol

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

6: import "../../OFT/BaseOFTV2.sol";

6: import "../../OFT/BaseOFTV2.sol";

6: import "../../OFT/BaseOFTV2.sol";

11:     constructor(uint8 _sharedDecimals, address _lzEndpoint) ERC20("peg-eUSD", "peUSD") BaseOFTV2(_sharedDecimals, _lzEndpoint) {

14:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

14:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

14:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

```

```solidity
File: lybra/token/PeUSDMainnetStableVision.sol

16: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

16: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

16: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

16: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

17: import "../../OFT/BaseOFTV2.sol";

17: import "../../OFT/BaseOFTV2.sol";

17: import "../../OFT/BaseOFTV2.sol";

18: import "../interfaces/Iconfigurator.sol";

18: import "../interfaces/Iconfigurator.sol";

19: import "../interfaces/IEUSD.sol";

19: import "../interfaces/IEUSD.sol";

55:     constructor(address _config, uint8 _sharedDecimals, address _lzEndpoint) ERC20("peg-eUSD", "PeUSD") BaseOFTV2(_sharedDecimals, _lzEndpoint) {

60:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

60:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

60:         ld2sdRatio = 10 ** (decimals - _sharedDecimals);

81:         require(EUSD.balanceOf(address(this)) + eusdAmount <= configurator.getEUSDMaxLocked(),"ESL");

85:         userConvertInfo[user].depositedEUSDShares += share;

86:         userConvertInfo[user].mintedPeUSD += eusdAmount;

117:         uint256 share = (userConvertInfo[msg.sender].depositedEUSDShares * peusdAmount) / userConvertInfo[msg.sender].mintedPeUSD;

117:         uint256 share = (userConvertInfo[msg.sender].depositedEUSDShares * peusdAmount) / userConvertInfo[msg.sender].mintedPeUSD;

118:         userConvertInfo[msg.sender].mintedPeUSD -= peusdAmount;

119:         userConvertInfo[msg.sender].depositedEUSDShares -= share;

157:         return (share * configurator.flashloanFee()) / 10_000;

157:         return (share * configurator.flashloanFee()) / 10_000;

168:         return EUSD.getMintedEUSDByShares(userConvertInfo[user].depositedEUSDShares) - userConvertInfo[user].mintedPeUSD;

```

```solidity
File: lybra/token/esLBR.sol

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

11: import "../interfaces/Iconfigurator.sol";

11: import "../interfaces/Iconfigurator.sol";

20:     uint256 maxSupply = 100_000_000 * 1e18;

32:         require(totalSupply() + amount <= maxSupply, "exceeding the maximum supply quantity.");

```

```solidity
File: mocks/LZEndpointMock.sol

6: import "../OFT/interfaces/ILayerZeroReceiver.sol";

6: import "../OFT/interfaces/ILayerZeroReceiver.sol";

6: import "../OFT/interfaces/ILayerZeroReceiver.sol";

7: import "../OFT/interfaces/ILayerZeroEndpoint.sol";

7: import "../OFT/interfaces/ILayerZeroEndpoint.sol";

7: import "../OFT/interfaces/ILayerZeroEndpoint.sol";

8: import "../OFT/libraries/LzLib.sol";

8: import "../OFT/libraries/LzLib.sol";

8: import "../OFT/libraries/LzLib.sol";

12: - blocking: LayerZero provides ordered delivery of messages from a given sender to a destination chain.

13: - non-reentrancy: endpoint has a non-reentrancy guard for both the send() and receive(), respectively.

13: - non-reentrancy: endpoint has a non-reentrancy guard for both the send() and receive(), respectively.

13: - non-reentrancy: endpoint has a non-reentrancy guard for both the send() and receive(), respectively.

14: - adapter parameters: allows UAs to add arbitrary transaction params in the send() function, like airdrop on destination chain.

16: - no messaging library versioning

17: - send() will short circuit to lzReceive()

18: - no user application configuration

59:         uint128 dstPriceRatio; // 10^10

59:         uint128 dstPriceRatio; // 10^10

102:             dstPriceRatio: 1e10, // 1:1, same chain, same native coin

102:             dstPriceRatio: 1e10, // 1:1, same chain, same native coin

108:         protocolFeeConfig = ProtocolFeeConfig({zroFee: 1e18, nativeBP: 1000}); // BP 0.1

108:         protocolFeeConfig = ProtocolFeeConfig({zroFee: 1e18, nativeBP: 1000}); // BP 0.1

115:         require(_path.length == 40, "LayerZeroMock: incorrect remote address size"); // only support evm chains

115:         require(_path.length == 40, "LayerZeroMock: incorrect remote address size"); // only support evm chains

130:         uint64 nonce = ++outboundNonce[_chainId][msg.sender];

130:         uint64 nonce = ++outboundNonce[_chainId][msg.sender];

133:         uint amount = msg.value - nativeFee;

149:         bytes memory srcUaAddress = abi.encodePacked(msg.sender, dstAddr); // cast this address to bytes

149:         bytes memory srcUaAddress = abi.encodePacked(msg.sender, dstAddr); // cast this address to bytes

158:         require(_nonce == ++inboundNonce[_srcChainId][_path], "LayerZeroMock: wrong nonce");

158:         require(_nonce == ++inboundNonce[_srcChainId][_path], "LayerZeroMock: wrong nonce");

172:                 for (uint i = 0; i < msgs.length - 1; i++) {

172:                 for (uint i = 0; i < msgs.length - 1; i++) {

172:                 for (uint i = 0; i < msgs.length - 1; i++) {

173:                     msgs[i + 1] = msgs[i];

216:         nativeFee = nativeFee + relayerFee + oracleFee;

216:         nativeFee = nativeFee + relayerFee + oracleFee;

262:         uint16, /*_version*/

262:         uint16, /*_version*/

262:         uint16, /*_version*/

262:         uint16, /*_version*/

263:         uint16, /*_chainId*/

263:         uint16, /*_chainId*/

263:         uint16, /*_chainId*/

263:         uint16, /*_chainId*/

264:         address, /*_ua*/

264:         address, /*_ua*/

264:         address, /*_ua*/

264:         address, /*_ua*/

265:         uint /*_configType*/

265:         uint /*_configType*/

265:         uint /*_configType*/

265:         uint /*_configType*/

271:         address /*_userApplication*/

271:         address /*_userApplication*/

271:         address /*_userApplication*/

271:         address /*_userApplication*/

277:         address /*_userApplication*/

277:         address /*_userApplication*/

277:         address /*_userApplication*/

277:         address /*_userApplication*/

283:         uint16, /*_version*/

283:         uint16, /*_version*/

283:         uint16, /*_version*/

283:         uint16, /*_version*/

284:         uint16, /*_chainId*/

284:         uint16, /*_chainId*/

284:         uint16, /*_chainId*/

284:         uint16, /*_chainId*/

285:         uint, /*_configType*/

285:         uint, /*_configType*/

285:         uint, /*_configType*/

285:         uint, /*_configType*/

286:         bytes memory /*_config*/

286:         bytes memory /*_config*/

286:         bytes memory /*_config*/

286:         bytes memory /*_config*/

290:         uint16 /*version*/

290:         uint16 /*version*/

290:         uint16 /*version*/

290:         uint16 /*version*/

294:         uint16 /*version*/

294:         uint16 /*version*/

294:         uint16 /*version*/

294:         uint16 /*version*/

357:             QueuedPayload memory payload = msgs[msgs.length - 1];

367:             return ((_relayerFee + _oracleFee) * protocolFeeConfig.nativeBP) / 10000;

367:             return ((_relayerFee + _oracleFee) * protocolFeeConfig.nativeBP) / 10000;

367:             return ((_relayerFee + _oracleFee) * protocolFeeConfig.nativeBP) / 10000;

372:         uint16, /* _dstChainId */

372:         uint16, /* _dstChainId */

372:         uint16, /* _dstChainId */

372:         uint16, /* _dstChainId */

373:         uint16, /* _outboundProofType */

373:         uint16, /* _outboundProofType */

373:         uint16, /* _outboundProofType */

373:         uint16, /* _outboundProofType */

374:         address, /* _userApplication */

374:         address, /* _userApplication */

374:         address, /* _userApplication */

374:         address, /* _userApplication */

379:         uint totalRemoteToken; // = baseGas + extraGas + requiredNativeAmount

379:         uint totalRemoteToken; // = baseGas + extraGas + requiredNativeAmount

379:         uint totalRemoteToken; // = baseGas + extraGas + requiredNativeAmount

379:         uint totalRemoteToken; // = baseGas + extraGas + requiredNativeAmount

382:             totalRemoteToken += dstNativeAmt;

385:         uint remoteGasTotal = relayerFeeConfig.dstGasPriceInWei * (relayerFeeConfig.baseGas + extraGas);

385:         uint remoteGasTotal = relayerFeeConfig.dstGasPriceInWei * (relayerFeeConfig.baseGas + extraGas);

386:         totalRemoteToken += remoteGasTotal;

390:         uint basePrice = (totalRemoteToken * relayerFeeConfig.dstPriceRatio) / 10**10;

390:         uint basePrice = (totalRemoteToken * relayerFeeConfig.dstPriceRatio) / 10**10;

390:         uint basePrice = (totalRemoteToken * relayerFeeConfig.dstPriceRatio) / 10**10;

390:         uint basePrice = (totalRemoteToken * relayerFeeConfig.dstPriceRatio) / 10**10;

393:         uint pricePerByte = (relayerFeeConfig.dstGasPriceInWei * relayerFeeConfig.gasPerByte * relayerFeeConfig.dstPriceRatio) / 10**10;

393:         uint pricePerByte = (relayerFeeConfig.dstGasPriceInWei * relayerFeeConfig.gasPerByte * relayerFeeConfig.dstPriceRatio) / 10**10;

393:         uint pricePerByte = (relayerFeeConfig.dstGasPriceInWei * relayerFeeConfig.gasPerByte * relayerFeeConfig.dstPriceRatio) / 10**10;

393:         uint pricePerByte = (relayerFeeConfig.dstGasPriceInWei * relayerFeeConfig.gasPerByte * relayerFeeConfig.dstPriceRatio) / 10**10;

393:         uint pricePerByte = (relayerFeeConfig.dstGasPriceInWei * relayerFeeConfig.gasPerByte * relayerFeeConfig.dstPriceRatio) / 10**10;

395:         return basePrice + _payloadSize * pricePerByte;

395:         return basePrice + _payloadSize * pricePerByte;

```

```solidity
File: mocks/chainLinkMock.sol

6:     uint256 price = 1600*1e8;

15:         return price * 1e10;

```

```solidity
File: mocks/mockCurve.sol

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

```

```solidity
File: mocks/mockEUSD.sol

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "@openzeppelin/contracts/utils/Context.sol";

7: import "@openzeppelin/contracts/utils/Context.sol";

7: import "@openzeppelin/contracts/utils/Context.sol";

8: import "../lybra/interfaces/Iconfigurator.sol";

8: import "../lybra/interfaces/Iconfigurator.sol";

8: import "../lybra/interfaces/Iconfigurator.sol";

201:                 _approve(owner, spender, currentAllowance - amount);

303:             _approve(owner, spender, currentAllowance - subtractedValue);

480:         _totalSupply += _mintAmount;

503:         _totalSupply -= _burnAmount;

```

```solidity
File: mocks/mockEtherPriceOracle.sol

30:         return (1, 1800 * 1e8, 1687380923, 1687380923, 1 );

```

```solidity
File: mocks/mockLBRPriceOracle.sol

6:     uint256 price = 10*1e8;

```

```solidity
File: mocks/mockUSDC.sol

10: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

10: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

10: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

10: import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

33:         _mint(msg.sender, 1000000*1e18);

37:         _mint(msg.sender, 10000*1e18);

38:         return 10000*1e18;

```

```solidity
File: mocks/mockWstETH.sol

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

10: import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

73:         _mint(msg.sender, 100*1e18);

74:         return 100*1e18;

```

```solidity
File: mocks/stETHMock.sol

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

5: import "@openzeppelin/contracts/utils/math/SafeMath.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

91:         _mintShares(msg.sender, 10000 * 1e18);

92:         totalEther = 10000 * 1e18;

100:         totalEther = _getTotalMintedEUSD() + msg.value;

105:         _mintShares(msg.sender, 100 * 1e18);

112:         return totalEther + totalEther * (block.timestamp - updateTime) / 365 / 20 days ;

112:         return totalEther + totalEther * (block.timestamp - updateTime) / 365 / 20 days ;

112:         return totalEther + totalEther * (block.timestamp - updateTime) / 365 / 20 days ;

112:         return totalEther + totalEther * (block.timestamp - updateTime) / 365 / 20 days ;

112:         return totalEther + totalEther * (block.timestamp - updateTime) / 365 / 20 days ;

```

### <a name="GAS-6"></a>[GAS-6] Use Custom Errors
[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (186)*:
```solidity
File: OFT/OFTCoreV2.sol

52:         require(_msgSender() == address(this), "OFTCore: caller must be OFTCore");

90:             revert("OFTCore: unknown packet type");

99:         require(amount > 0, "OFTCore: amount too small");

124:         require(amount > 0, "OFTCore: amount too small");

181:             require(_adapterParams.length == 0, "OFTCore: _adapterParams must be empty.");

187:         require(amountSD <= type(uint64).max, "OFTCore: amountSD overflow");

205:         require(_payload.toUint8(0) == PT_SEND && _payload.length == 41, "OFTCore: invalid payload");

223:         require(_payload.toUint8(0) == PT_SEND_AND_CALL, "OFTCore: invalid payload");

```

```solidity
File: OFT/libraries/LzLib.sol

38:         require(_params.airdropAmount > 0, "Airdrop amount must be greater than 0");

39:         require(_params.airdropAddress != bytes32(0x0), "Airdrop address must be set");

48:         require(_adapterParams.length == 34 || _adapterParams.length > 66, "Invalid adapterParams");

56:         require(_adapterParams.length == 34 || _adapterParams.length > 66, "Invalid adapterParams");

61:         require(txType == 1 || txType == 2, "Unsupported txType");

62:         require(uaGas > 0, "Gas too low");

```

```solidity
File: OFT/lzApp/LzApp.sol

37:         require(_msgSender() == address(lzEndpoint), "LzApp: invalid endpoint caller");

41:         require(_srcAddress.length == trustedRemote.length && trustedRemote.length > 0 && keccak256(_srcAddress) == keccak256(trustedRemote), "LzApp: invalid source sending contract");

51:         require(trustedRemote.length != 0, "LzApp: destination chain is not a trusted source");

59:         require(minGasLimit > 0, "LzApp: minGasLimit not set");

60:         require(providedGasLimit >= minGasLimit, "LzApp: gas limit is too low");

64:         require(_adapterParams.length >= 34, "LzApp: invalid adapterParams");

75:         require(_payloadSize <= payloadSizeLimit, "LzApp: payload size is too large");

114:         require(path.length != 0, "LzApp: no trusted path record");

124:         require(_minGas > 0, "LzApp: invalid minGas");

```

```solidity
File: OFT/lzApp/NonblockingLzApp.sol

39:         require(_msgSender() == address(this), "NonblockingLzApp: caller must be LzApp");

49:         require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");

50:         require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");

```

```solidity
File: OFT/util/BytesLib.sol

237:         require(_length + 31 >= _length, "slice_overflow");

238:         require(_bytes.length >= _start + _length, "slice_outOfBounds");

298:         require(_bytes.length >= _start + 20, "toAddress_outOfBounds");

309:         require(_bytes.length >= _start + 1 , "toUint8_outOfBounds");

320:         require(_bytes.length >= _start + 2, "toUint16_outOfBounds");

331:         require(_bytes.length >= _start + 4, "toUint32_outOfBounds");

342:         require(_bytes.length >= _start + 8, "toUint64_outOfBounds");

353:         require(_bytes.length >= _start + 12, "toUint96_outOfBounds");

364:         require(_bytes.length >= _start + 16, "toUint128_outOfBounds");

375:         require(_bytes.length >= _start + 32, "toUint256_outOfBounds");

386:         require(_bytes.length >= _start + 32, "toBytes32_outOfBounds");

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

127:         require(newRatio >= 130 * 1e18 && newRatio <= 150 * 1e18 && newRatio <= vaultSafeCollateralRatio[pool] + 1e19, "LNA");

187:         require(newFee <= 500, "Max Redemption Fee is 5%");

200:             require(newRatio >= 160 * 1e18, "eUSD vault safe collateralRatio should more than 160%");

202:             require(newRatio >= vaultBadCollateralRatio[pool] + 1e19, "PeUSD vault safe collateralRatio should more than bad collateralRatio");

214:         require(newApy <= 200, "Borrow APY cannot exceed 2%");

225:         require(newRatio <= 5, "Max Keeper reward is 5%");

247:         require(_ratio <= 10_000, "The maximum value is 10000");

```

```solidity
File: lybra/governance/LybraGovernance.sol

78:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

79:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

82:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

107:         require(GovernanceTimelock.checkOnlyRole(keccak256("TIMELOCK"), msg.sender), "not authorized");

152:        require(clock() == block.number, "Votes: broken clock mode");

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

95:             require(configurator.mintVault(_pools[i]), "NOT_VAULT");

101:         require(_biddingRatio <= 8000, "BCE");

106:         require(ratio <= 1e20, "BCE");

111:         require(ratio <= 1e20, "BCE");

120:         require(finishAt < block.timestamp, "reward duration not finished");

193:         require(!isOtherEarningsClaimable(msg.sender), "Insufficient DLP, unable to claim rewards");

203:         require(isOtherEarningsClaimable(user), "The rewards of the user cannot be bought out");

205:             require(isEUSDBuyoutAllowed, "The purchase using EUSD is not permitted.");

215:                 require(success, "TF");

229:         require(amount > 0, "amount = 0");

237:         require(rewardRatio > 0, "reward ratio = 0");

```

```solidity
File: lybra/miner/ProtocolRewardsPool.sol

59:         require(_ratio <= 8000, "BCE");

88:         require(block.timestamp >= esLBRBoost.getUnlockTime(msg.sender), "Your lock-in period has not ended. You can't convert your esLBR now.");

114:         require(block.timestamp + exitCycle - 3 days > time2fullRedemption[msg.sender], "ENW");

132:         require(amount > 0, "QMG");

230:         require(amount > 0, "amount = 0");

```

```solidity
File: lybra/miner/esLBRBoost.sol

42:             require(userStatus.duration <= _setting.duration, "Your lock-in period has not ended, and the term can only be extended, not reduced.");

```

```solidity
File: lybra/miner/stakerewardV2pool.sol

84:         require(_amount > 0, "amount = 0");

86:         require(success, "TF");

94:         require(_amount > 0, "amount = 0");

122:         require(finishAt < block.timestamp, "reward duration not finished");

140:         require(rewardRatio > 0, "reward ratio = 0");

```

```solidity
File: lybra/pools/LybraRETHVault.sol

28:         require(msg.value >= 1 ether, "DNL");

42:         require(configurator.hasRole(keccak256("TIMELOCK"), msg.sender));

```

```solidity
File: lybra/pools/LybraStETHVault.sol

26:         require(configurator.hasRole(keccak256("ADMIN"), msg.sender), "not authorized");

38:         require(msg.value >= 1 ether, "DNL");

41:         require(sharesAmount > 0, "ZERO_DEPOSIT");

64:         require(excessAmount > 0 && stETHAmount > 0, "Only LSD excess income can be exchanged");

71:             require(success, "TF");

86:             require(success, "TF");

```

```solidity
File: lybra/pools/LybraWbETHVault.sol

21:         require(msg.value >= 1 ether, "DNL");

```

```solidity
File: lybra/pools/LybraWstETHVault.sol

30:         require(msg.value >= 1 ether, "DNL");

33:         require(sharesAmount > 0, "ZERO_DEPOSIT");

```

```solidity
File: lybra/pools/base/LybraEUSDVaultBase.sol

73:         require(assetAmount >= 1 ether, "Deposit should not be less than 1 stETH.");

76:         require(success, "TF");

99:         require(onBehalfOf != address(0), "TZA");

100:         require(amount > 0, "ZERO_WITHDRAW");

101:         require(depositedAsset[msg.sender] >= amount, "Withdraw amount exceeds deposited amount.");

127:         require(onBehalfOf != address(0), "MINT_TO_THE_ZERO_ADDRESS");

128:         require(amount > 0, "ZERO_MINT");

141:         require(onBehalfOf != address(0), "BURN_TO_THE_ZERO_ADDRESS");

157:         require(onBehalfOfCollateralRatio < badCollateralRatio, "Borrowers collateral ratio should below badCollateralRatio");

159:         require(assetAmount * 2 <= depositedAsset[onBehalfOf], "a max of 50% collateral can be liquidated");

160:         require(EUSD.allowance(provider, address(this)) > 0, "provider should authorize to provide liquidation EUSD");

189:         require((totalDepositedAsset * assetPrice * 100) / poolTotalEUSDCirculation < badCollateralRatio, "overallCollateralRatio should below 150%");

191:         require(onBehalfOfCollateralRatio < 125 * 1e18, "borrowers collateralRatio should below 125%");

192:         require(assetAmount <= depositedAsset[onBehalfOf], "total of collateral can be liquidated at most");

197:         require(EUSD.allowance(provider, address(this)) >= eusdAmount, "provider should authorize to provide liquidation EUSD");

233:         require(configurator.isRedemptionProvider(provider), "provider is not a RedemptionProvider");

234:         require(borrowed[provider] >= eusdAmount, "eusdAmount cannot surpass providers debt");

237:         require(providerCollateralRatio >= 100 * 1e18, "provider's collateral ratio should more than 100%");

260:         require(poolTotalEUSDCirculation + _mintAmount <= configurator.mintVaultMaxSupply(address(this)), "ESL");

292:         if (((depositedAsset[_user] * _assetPrice * 100) / borrowed[_user]) < configurator.getSafeCollateralRatio(address(this))) revert("collateralRatio is Below safeCollateralRatio");

```

```solidity
File: lybra/pools/base/LybraPeUSDVaultBase.sol

59:         require(assetAmount >= 1 ether, "Deposit should not be less than 1 collateral asset.");

62:         require(collateralAsset.balanceOf(address(this)) >= preBalance + assetAmount, "");

83:         require(onBehalfOf != address(0), "TZA");

84:         require(amount > 0, "ZA");

97:         require(onBehalfOf != address(0), "TZA");

98:         require(amount > 0, "ZA");

111:         require(onBehalfOf != address(0), "TZA");

112:         require(amount > 0, "ZA");

128:         require(onBehalfOfCollateralRatio < configurator.getBadCollateralRatio(address(this)), "Borrowers collateral ratio should below badCollateralRatio");

130:         require(assetAmount * 2 <= depositedAsset[onBehalfOf], "a max of 50% collateral can be liquidated");

131:         require(PeUSD.allowance(provider, address(this)) > 0, "provider should authorize to provide liquidation EUSD");

158:         require(configurator.isRedemptionProvider(provider), "provider is not a RedemptionProvider");

159:         require(borrowed[provider] >= peusdAmount, "peusdAmount cannot surpass providers debt");

162:         require(providerCollateralRatio >= 100 * 1e18, "provider's collateral ratio should more than 100%");

174:         require(poolTotalPeUSDCirculation + _mintAmount <= configurator.mintVaultMaxSupply(address(this)), "ESL");

213:         require(depositedAsset[_provider] >= _amount, "Withdraw amount exceeds deposited amount.");

227:             revert("collateralRatio is Below safeCollateralRatio");

```

```solidity
File: lybra/token/EUSD.sol

80:         require(configurator.mintVault(msg.sender), "RCP");

84:         require(!configurator.vaultMintPaused(msg.sender), "MPP");

88:         require(!configurator.vaultBurnPaused(msg.sender), "BPP");

180:             require(currentAllowance >= amount, "ERC20: insufficient allowance");

271:         require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

367:         require(_owner != address(0), "APPROVE_FROM_ZERO_ADDRESS");

368:         require(_spender != address(0), "APPROVE_TO_ZERO_ADDRESS");

392:         require(_sender != address(0), "TRANSFER_FROM_THE_ZERO_ADDRESS");

393:         require(_recipient != address(0), "TRANSFER_TO_THE_ZERO_ADDRESS");

396:         require(_sharesAmount <= currentSenderShares, "TRANSFER_AMOUNT_EXCEEDS_BALANCE");

412:         require(_recipient != address(0), "MINT_TO_THE_ZERO_ADDRESS");

441:         require(_account != address(0), "BURN_FROM_THE_ZERO_ADDRESS");

460:         require(_account != address(0), "BURN_FROM_THE_ZERO_ADDRESS");

466:         require(_sharesAmount <= accountShares, "BURN_AMOUNT_EXCEEDS_BALANCE");

```

```solidity
File: lybra/token/LBR.sol

21:         require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");

26:         require(configurator.tokenMiner(msg.sender), "not authorized");

27:         require(totalSupply() + amount <= maxSupply, "exceeding the maximum supply quantity.");

33:         require(configurator.tokenMiner(msg.sender), "not authorized");

```

```solidity
File: lybra/token/PeUSD.sol

13:         require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");

```

```solidity
File: lybra/token/PeUSDMainnetStableVision.sol

43:         require(configurator.mintVault(msg.sender), "RCP");

47:         require(!configurator.vaultMintPaused(msg.sender), "MPP");

51:         require(!configurator.vaultBurnPaused(msg.sender), "BPP");

59:         require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");

64:         require(to != address(0), "TZA");

80:         require(_msgSender() == user || _msgSender() == address(this), "MDM");

81:         require(EUSD.balanceOf(address(this)) + eusdAmount <= configurator.getEUSDMaxLocked(),"ESL");

83:         require(success, "TF");

115:         require(peusdAmount <= userConvertInfo[msg.sender].mintedPeUSD &&peusdAmount > 0, "PCE");

134:         require(success, "TF");

```

```solidity
File: lybra/token/esLBR.sol

27:         revert("not authorized");

31:         require(configurator.tokenMiner(msg.sender), "not authorized");

32:         require(totalSupply() + amount <= maxSupply, "exceeding the maximum supply quantity.");

39:         require(configurator.tokenMiner(msg.sender), "not authorized");

```

```solidity
File: mocks/LZEndpointMock.sol

79:         require(_send_entered_state == _NOT_ENTERED, "LayerZeroMock: no send reentrancy");

86:         require(_receive_entered_state == _NOT_ENTERED, "LayerZeroMock: no receive reentrancy");

115:         require(_path.length == 40, "LayerZeroMock: incorrect remote address size"); // only support evm chains

123:         require(lzEndpoint != address(0), "LayerZeroMock: destination LayerZero Endpoint not found");

128:         require(msg.value >= nativeFee, "LayerZeroMock: not enough native for fees");

136:             require(success, "LayerZeroMock: failed to refund");

158:         require(_nonce == ++inboundNonce[_srcChainId][_path], "LayerZeroMock: wrong nonce");

225:         require(sp.payloadHash != bytes32(0), "LayerZeroMock: no stored payload");

226:         require(_payload.length == sp.payloadLength && keccak256(_payload) == sp.payloadHash, "LayerZeroMock: invalid payload");

300:         require(sp.payloadHash != bytes32(0), "LayerZeroMock: no stored payload");

301:         require(sp.dstAddress == msg.sender, "LayerZeroMock: invalid caller");

381:             require(relayerFeeConfig.dstNativeAmtCap >= dstNativeAmt, "LayerZeroMock: dstNativeAmt too large ");

```

```solidity
File: mocks/mockEUSD.sol

90:         require(configurator.mintVault(msg.sender), "");

94:         require(!configurator.vaultMintPaused(msg.sender), "");

98:         require(!configurator.vaultBurnPaused(msg.sender), "");

199:             require(currentAllowance >= amount, "ERC20: insufficient allowance");

301:         require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

412:         require(_owner != address(0), "APPROVE_FROM_ZERO_ADDRESS");

413:         require(_spender != address(0), "APPROVE_TO_ZERO_ADDRESS");

441:         require(_sender != address(0), "TRANSFER_FROM_THE_ZERO_ADDRESS");

442:         require(_recipient != address(0), "TRANSFER_TO_THE_ZERO_ADDRESS");

467:         require(_recipient != address(0), "MINT_TO_THE_ZERO_ADDRESS");

500:         require(_account != address(0), "BURN_FROM_THE_ZERO_ADDRESS");

522:         require(_account != address(0), "BURN_FROM_THE_ZERO_ADDRESS");

528:         require(_sharesAmount <= accountShares, "BURN_AMOUNT_EXCEEDS_BALANCE");

```

```solidity
File: mocks/mockWstETH.sol

65:         require(_stETHAmount > 0, "wstETH: can't wrap zero stETH");

86:         require(_wstETHAmount > 0, "wstETH: zero amount unwrap not allowed");

```

```solidity
File: mocks/stETHMock.sol

403:         require(_owner != address(0), "APPROVE_FROM_ZERO_ADDRESS");

404:         require(_spender != address(0), "APPROVE_TO_ZERO_ADDRESS");

439:         require(_sender != address(0), "TRANSFER_FROM_THE_ZERO_ADDRESS");

440:         require(_recipient != address(0), "TRANSFER_TO_THE_ZERO_ADDRESS");

465:         require(_recipient != address(0), "MINT_TO_THE_ZERO_ADDRESS");

494:         require(_account != address(0), "BURN_FROM_THE_ZERO_ADDRESS");

497:         require(_sharesAmount <= accountShares, "BURN_AMOUNT_EXCEEDS_BALANCE");

```

### <a name="GAS-7"></a>[GAS-7] Don't initialize variables with default value

*Instances (7)*:
```solidity
File: OFT/OFTCoreV2.sol

14:     uint public constant NO_EXTRA_GAS = 0;

17:     uint8 public constant PT_SEND = 0;

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

236:         for (uint256 i = 0; i < _contracts.length; i++) {

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

94:         for (uint i = 0; i < _pools.length; i++) {

138:         for (uint i = 0; i < pools.length; i++) {

```

```solidity
File: lybra/pools/base/LybraEUSDVaultBase.sol

30:     uint8 immutable vaultType = 0;

```

```solidity
File: mocks/LZEndpointMock.sol

172:                 for (uint i = 0; i < msgs.length - 1; i++) {

```

### <a name="GAS-8"></a>[GAS-8] Long revert strings

*Instances (54)*:
```solidity
File: OFT/OFTCoreV2.sol

181:             require(_adapterParams.length == 0, "OFTCore: _adapterParams must be empty.");

```

```solidity
File: OFT/libraries/LzLib.sol

38:         require(_params.airdropAmount > 0, "Airdrop amount must be greater than 0");

```

```solidity
File: OFT/lzApp/LzApp.sol

41:         require(_srcAddress.length == trustedRemote.length && trustedRemote.length > 0 && keccak256(_srcAddress) == keccak256(trustedRemote), "LzApp: invalid source sending contract");

51:         require(trustedRemote.length != 0, "LzApp: destination chain is not a trusted source");

```

```solidity
File: OFT/lzApp/NonblockingLzApp.sol

39:         require(_msgSender() == address(this), "NonblockingLzApp: caller must be LzApp");

49:         require(payloadHash != bytes32(0), "NonblockingLzApp: no stored message");

50:         require(keccak256(_payload) == payloadHash, "NonblockingLzApp: invalid payload");

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

200:             require(newRatio >= 160 * 1e18, "eUSD vault safe collateralRatio should more than 160%");

202:             require(newRatio >= vaultBadCollateralRatio[pool] + 1e19, "PeUSD vault safe collateralRatio should more than bad collateralRatio");

```

```solidity
File: lybra/governance/LybraGovernance.sol

78:         require(state(proposalId) == ProposalState.Active, "GovernorBravo::castVoteInternal: voting is closed");

79:         require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");

82:         require(receipt.hasVoted == false, "GovernorBravo::castVoteInternal: voter already voted");

107:         require(GovernanceTimelock.checkOnlyRole(keccak256("TIMELOCK"), msg.sender), "not authorized");

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

193:         require(!isOtherEarningsClaimable(msg.sender), "Insufficient DLP, unable to claim rewards");

203:         require(isOtherEarningsClaimable(user), "The rewards of the user cannot be bought out");

205:             require(isEUSDBuyoutAllowed, "The purchase using EUSD is not permitted.");

```

```solidity
File: lybra/miner/ProtocolRewardsPool.sol

88:         require(block.timestamp >= esLBRBoost.getUnlockTime(msg.sender), "Your lock-in period has not ended. You can't convert your esLBR now.");

```

```solidity
File: lybra/miner/esLBRBoost.sol

42:             require(userStatus.duration <= _setting.duration, "Your lock-in period has not ended, and the term can only be extended, not reduced.");

```

```solidity
File: lybra/pools/LybraStETHVault.sol

26:         require(configurator.hasRole(keccak256("ADMIN"), msg.sender), "not authorized");

64:         require(excessAmount > 0 && stETHAmount > 0, "Only LSD excess income can be exchanged");

```

```solidity
File: lybra/pools/base/LybraEUSDVaultBase.sol

73:         require(assetAmount >= 1 ether, "Deposit should not be less than 1 stETH.");

101:         require(depositedAsset[msg.sender] >= amount, "Withdraw amount exceeds deposited amount.");

157:         require(onBehalfOfCollateralRatio < badCollateralRatio, "Borrowers collateral ratio should below badCollateralRatio");

159:         require(assetAmount * 2 <= depositedAsset[onBehalfOf], "a max of 50% collateral can be liquidated");

160:         require(EUSD.allowance(provider, address(this)) > 0, "provider should authorize to provide liquidation EUSD");

189:         require((totalDepositedAsset * assetPrice * 100) / poolTotalEUSDCirculation < badCollateralRatio, "overallCollateralRatio should below 150%");

191:         require(onBehalfOfCollateralRatio < 125 * 1e18, "borrowers collateralRatio should below 125%");

192:         require(assetAmount <= depositedAsset[onBehalfOf], "total of collateral can be liquidated at most");

197:         require(EUSD.allowance(provider, address(this)) >= eusdAmount, "provider should authorize to provide liquidation EUSD");

233:         require(configurator.isRedemptionProvider(provider), "provider is not a RedemptionProvider");

234:         require(borrowed[provider] >= eusdAmount, "eusdAmount cannot surpass providers debt");

237:         require(providerCollateralRatio >= 100 * 1e18, "provider's collateral ratio should more than 100%");

```

```solidity
File: lybra/pools/base/LybraPeUSDVaultBase.sol

59:         require(assetAmount >= 1 ether, "Deposit should not be less than 1 collateral asset.");

128:         require(onBehalfOfCollateralRatio < configurator.getBadCollateralRatio(address(this)), "Borrowers collateral ratio should below badCollateralRatio");

130:         require(assetAmount * 2 <= depositedAsset[onBehalfOf], "a max of 50% collateral can be liquidated");

131:         require(PeUSD.allowance(provider, address(this)) > 0, "provider should authorize to provide liquidation EUSD");

158:         require(configurator.isRedemptionProvider(provider), "provider is not a RedemptionProvider");

159:         require(borrowed[provider] >= peusdAmount, "peusdAmount cannot surpass providers debt");

162:         require(providerCollateralRatio >= 100 * 1e18, "provider's collateral ratio should more than 100%");

213:         require(depositedAsset[_provider] >= _amount, "Withdraw amount exceeds deposited amount.");

```

```solidity
File: lybra/token/EUSD.sol

271:         require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

```

```solidity
File: lybra/token/LBR.sol

21:         require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");

27:         require(totalSupply() + amount <= maxSupply, "exceeding the maximum supply quantity.");

```

```solidity
File: lybra/token/PeUSD.sol

13:         require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");

```

```solidity
File: lybra/token/PeUSDMainnetStableVision.sol

59:         require(_sharedDecimals <= decimals, "OFT: sharedDecimals must be <= decimals");

```

```solidity
File: lybra/token/esLBR.sol

32:         require(totalSupply() + amount <= maxSupply, "exceeding the maximum supply quantity.");

```

```solidity
File: mocks/LZEndpointMock.sol

79:         require(_send_entered_state == _NOT_ENTERED, "LayerZeroMock: no send reentrancy");

86:         require(_receive_entered_state == _NOT_ENTERED, "LayerZeroMock: no receive reentrancy");

115:         require(_path.length == 40, "LayerZeroMock: incorrect remote address size"); // only support evm chains

123:         require(lzEndpoint != address(0), "LayerZeroMock: destination LayerZero Endpoint not found");

128:         require(msg.value >= nativeFee, "LayerZeroMock: not enough native for fees");

381:             require(relayerFeeConfig.dstNativeAmtCap >= dstNativeAmt, "LayerZeroMock: dstNativeAmt too large ");

```

```solidity
File: mocks/mockEUSD.sol

301:         require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

```

```solidity
File: mocks/mockWstETH.sol

86:         require(_wstETHAmount > 0, "wstETH: zero amount unwrap not allowed");

```

### <a name="GAS-9"></a>[GAS-9] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (37)*:
```solidity
File: OFT/OFTCoreV2.sol

62:     function setUseCustomAdapterParams(bool _useCustomAdapterParams) public virtual onlyOwner {

```

```solidity
File: OFT/lzApp/LzApp.sol

84:     function setConfig(uint16 _version, uint16 _chainId, uint _configType, bytes calldata _config) external override onlyOwner {

88:     function setSendVersion(uint16 _version) external override onlyOwner {

92:     function setReceiveVersion(uint16 _version) external override onlyOwner {

96:     function forceResumeReceive(uint16 _srcChainId, bytes calldata _srcAddress) external override onlyOwner {

102:     function setTrustedRemote(uint16 _remoteChainId, bytes calldata _path) external onlyOwner {

107:     function setTrustedRemoteAddress(uint16 _remoteChainId, bytes calldata _remoteAddress) external onlyOwner {

118:     function setPrecrime(address _precrime) external onlyOwner {

123:     function setMinDstGas(uint16 _dstChainId, uint16 _packetType, uint _minGas) external onlyOwner {

130:     function setPayloadSizeLimit(uint16 _dstChainId, uint _size) external onlyOwner {

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

98:     function initToken(address _eusd, address _peusd) external onlyRole(DAO) {

109:     function setMintVault(address pool, bool isActive) external onlyRole(DAO) {

119:     function setMintVaultMaxSupply(address pool, uint256 maxSupply) external onlyRole(DAO) {

126:     function setBadCollateralRatio(address pool, uint256 newRatio) external onlyRole(DAO) {

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

84:     function setToken(address _lbr, address _eslbr) external onlyOwner {

89:     function setLBROracle(address _lbrOracle) external onlyOwner {

93:     function setPools(address[] memory _pools) external onlyOwner {

100:     function setBiddingCost(uint256 _biddingRatio) external onlyOwner {

105:     function setExtraRatio(uint256 ratio) external onlyOwner {

110:     function setPeUSDExtraRatio(uint256 ratio) external onlyOwner {

115:     function setBoost(address _boost) external onlyOwner {

119:     function setRewardsDuration(uint256 _duration) external onlyOwner {

124:     function setEthlbrStakeInfo(address _pool, address _lp) external onlyOwner {

128:     function setEUSDBuyoutAllowed(bool _bool) external onlyOwner {

```

```solidity
File: lybra/miner/ProtocolRewardsPool.sol

52:     function setTokenAddress(address _eslbr, address _lbr, address _boost) external onlyOwner {

58:     function setGrabCost(uint256 _ratio) external onlyOwner {

```

```solidity
File: lybra/miner/esLBRBoost.sol

33:     function addLockSetting(esLBRLockSetting memory setting) external onlyOwner {

```

```solidity
File: lybra/miner/stakerewardV2pool.sol

121:     function setRewardsDuration(uint256 _duration) external onlyOwner {

127:     function setBoost(address _boost) external onlyOwner {

132:     function notifyRewardAmount(uint256 _amount) external onlyOwner updateReward(address(0)) {

```

```solidity
File: lybra/token/EUSD.sol

411:     function mint(address _recipient, uint256 _mintAmount) external onlyMintVault MintPaused returns (uint256 newTotalShares) {

440:     function burn(address _account, uint256 _burnAmount) external onlyMintVault BurnPaused returns (uint256 newTotalShares) {

459:     function burnShares(address _account, uint256 _sharesAmount) external onlyMintVault BurnPaused returns (uint256 newTotalShares) {

464:     function _onlyBurnShares(address _account, uint256 _sharesAmount) private returns (uint256 newTotalShares) {

```

```solidity
File: lybra/token/PeUSDMainnetStableVision.sol

63:     function mint(address to, uint256 amount) external onlyMintVault MintPaused returns (bool) {

69:     function burn(address account, uint256 amount) external onlyMintVault BurnPaused returns (bool) {

```

```solidity
File: mocks/mockEUSD.sol

526:     function _onlyBurnShares( address _account, uint256 _sharesAmount) private returns(uint256 newTotalShares){

```

### <a name="GAS-10"></a>[GAS-10] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
*Saves 5 gas per loop*

*Instances (4)*:
```solidity
File: lybra/configuration/LybraConfigurator.sol

236:         for (uint256 i = 0; i < _contracts.length; i++) {

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

94:         for (uint i = 0; i < _pools.length; i++) {

138:         for (uint i = 0; i < pools.length; i++) {

```

```solidity
File: mocks/LZEndpointMock.sol

172:                 for (uint i = 0; i < msgs.length - 1; i++) {

```

### <a name="GAS-11"></a>[GAS-11] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (11)*:
```solidity
File: OFT/OFTCoreV2.sol

14:     uint public constant NO_EXTRA_GAS = 0;

17:     uint8 public constant PT_SEND = 0;

18:     uint8 public constant PT_SEND_AND_CALL = 1;

```

```solidity
File: OFT/lzApp/LzApp.sol

18:     uint constant public DEFAULT_PAYLOAD_SIZE_LIMIT = 10000;

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

76:     bytes32 public constant DAO = keccak256("DAO");

77:     bytes32 public constant TIMELOCK = keccak256("TIMELOCK");

78:     bytes32 public constant ADMIN = keccak256("ADMIN");

```

```solidity
File: lybra/governance/GovernanceTimelock.sol

10:     bytes32 public constant DAO = keccak256("DAO");

11:     bytes32 public constant TIMELOCK = keccak256("TIMELOCK");

12:     bytes32 public constant ADMIN = keccak256("ADMIN");

13:     bytes32 public constant GOV = keccak256("GOV");

```

### <a name="GAS-12"></a>[GAS-12] Splitting require() statements that use && saves gas

*Instances (6)*:
```solidity
File: OFT/OFTCoreV2.sol

205:         require(_payload.toUint8(0) == PT_SEND && _payload.length == 41, "OFTCore: invalid payload");

```

```solidity
File: OFT/lzApp/LzApp.sol

41:         require(_srcAddress.length == trustedRemote.length && trustedRemote.length > 0 && keccak256(_srcAddress) == keccak256(trustedRemote), "LzApp: invalid source sending contract");

```

```solidity
File: lybra/configuration/LybraConfigurator.sol

127:         require(newRatio >= 130 * 1e18 && newRatio <= 150 * 1e18 && newRatio <= vaultSafeCollateralRatio[pool] + 1e19, "LNA");

```

```solidity
File: lybra/pools/LybraStETHVault.sol

64:         require(excessAmount > 0 && stETHAmount > 0, "Only LSD excess income can be exchanged");

```

```solidity
File: lybra/token/PeUSDMainnetStableVision.sol

115:         require(peusdAmount <= userConvertInfo[msg.sender].mintedPeUSD &&peusdAmount > 0, "PCE");

```

```solidity
File: mocks/LZEndpointMock.sol

226:         require(_payload.length == sp.payloadLength && keccak256(_payload) == sp.payloadHash, "LayerZeroMock: invalid payload");

```

### <a name="GAS-13"></a>[GAS-13] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (61)*:
```solidity
File: OFT/ICommonOFT.sol

3: pragma solidity >=0.5.0;

```

```solidity
File: OFT/IOFTReceiverV2.sol

3: pragma solidity >=0.5.0;

```

```solidity
File: OFT/IOFTV2.sol

3: pragma solidity >=0.5.0;

```

```solidity
File: OFT/OFTCoreV2.sol

99:         require(amount > 0, "OFTCore: amount too small");

124:         require(amount > 0, "OFTCore: amount too small");

174:         return _account.code.length > 0;

```

```solidity
File: OFT/interfaces/ILayerZeroEndpoint.sol

3: pragma solidity >=0.5.0;

```

```solidity
File: OFT/interfaces/ILayerZeroReceiver.sol

3: pragma solidity >=0.5.0;

```

```solidity
File: OFT/interfaces/ILayerZeroUserApplicationConfig.sol

3: pragma solidity >=0.5.0;

```

```solidity
File: OFT/libraries/LzLib.sol

3: pragma solidity >=0.6.0;

38:         require(_params.airdropAmount > 0, "Airdrop amount must be greater than 0");

62:         require(uaGas > 0, "Gas too low");

```

```solidity
File: OFT/lzApp/LzApp.sol

41:         require(_srcAddress.length == trustedRemote.length && trustedRemote.length > 0 && keccak256(_srcAddress) == keccak256(trustedRemote), "LzApp: invalid source sending contract");

59:         require(minGasLimit > 0, "LzApp: minGasLimit not set");

124:         require(_minGas > 0, "LzApp: invalid minGas");

```

```solidity
File: OFT/util/BitLib.sol

2: pragma solidity >=0.5.0;

```

```solidity
File: OFT/util/BytesLib.sol

9: pragma solidity >=0.8.0 <0.9.0;

9: pragma solidity >=0.8.0 <0.9.0;

```

```solidity
File: OFT/util/ExcessivelySafeCall.sol

2: pragma solidity >=0.7.6;

```

```solidity
File: lybra/miner/EUSDMiningIncentives.sol

195:         if (reward > 0) {

208:         if (reward > 0) {

229:         require(amount > 0, "amount = 0");

237:         require(rewardRatio > 0, "reward ratio = 0");

```

```solidity
File: lybra/miner/ProtocolRewardsPool.sol

102:         if (amount > 0) {

117:         if (amount > 0) {

132:         require(amount > 0, "QMG");

192:         if (reward > 0) {

205:                     if(peUSDBalance > 0) {

230:         require(amount > 0, "amount = 0");

```

```solidity
File: lybra/miner/stakerewardV2pool.sol

84:         require(_amount > 0, "amount = 0");

94:         require(_amount > 0, "amount = 0");

113:         if (reward > 0) {

140:         require(rewardRatio > 0, "reward ratio = 0");

```

```solidity
File: lybra/pools/LybraRETHVault.sol

34:         if (mintAmount > 0) {

```

```solidity
File: lybra/pools/LybraStETHVault.sol

41:         require(sharesAmount > 0, "ZERO_DEPOSIT");

47:         if (mintAmount > 0) {

64:         require(excessAmount > 0 && stETHAmount > 0, "Only LSD excess income can be exchanged");

64:         require(excessAmount > 0 && stETHAmount > 0, "Only LSD excess income can be exchanged");

```

```solidity
File: lybra/pools/LybraWbETHVault.sol

27:         if (mintAmount > 0) {

```

```solidity
File: lybra/pools/LybraWstETHVault.sol

33:         require(sharesAmount > 0, "ZERO_DEPOSIT");

41:         if (mintAmount > 0) {

```

```solidity
File: lybra/pools/base/LybraEUSDVaultBase.sol

82:         if (mintAmount > 0) {

100:         require(amount > 0, "ZERO_WITHDRAW");

108:         if (borrowed[msg.sender] > 0) {

128:         require(amount > 0, "ZERO_MINT");

160:         require(EUSD.allowance(provider, address(this)) > 0, "provider should authorize to provide liquidation EUSD");

```

```solidity
File: lybra/pools/base/LybraPeUSDVaultBase.sol

65:         if (mintAmount > 0) {

84:         require(amount > 0, "ZA");

98:         require(amount > 0, "ZA");

112:         require(amount > 0, "ZA");

131:         require(PeUSD.allowance(provider, address(this)) > 0, "provider should authorize to provide liquidation EUSD");

216:         if (getBorrowedOf(_provider) > 0) {

```

```solidity
File: lybra/token/PeUSDMainnetStableVision.sol

115:         require(peusdAmount <= userConvertInfo[msg.sender].mintedPeUSD &&peusdAmount > 0, "PCE");

```

```solidity
File: mocks/LZEndpointMock.sol

126:         bytes memory adapterParams = _adapterParams.length > 0 ? _adapterParams : defaultAdapterParams;

134:         if (amount > 0) {

142:         if (dstNativeAmt > 0) {

167:             if (msgs.length > 0) {

206:         bytes memory adapterParams = _adapterParams.length > 0 ? _adapterParams : defaultAdapterParams;

356:         while (msgs.length > 0) {

```

```solidity
File: mocks/mockWstETH.sol

65:         require(_stETHAmount > 0, "wstETH: can't wrap zero stETH");

86:         require(_wstETHAmount > 0, "wstETH: zero amount unwrap not allowed");

```

### <a name="GAS-14"></a>[GAS-14] `internal` functions not called by the contract should be removed
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*Instances (27)*:
```solidity
File: OFT/libraries/LzLib.sol

21:     function buildAdapterParams(LzLib.AirdropParams memory _airdropParams, uint _uaGasLimit) internal pure returns (bytes memory adapterParams) {

47:     function getGasLimit(bytes memory _adapterParams) internal pure returns (uint gasLimit) {

55:     function decodeAdapterParams(bytes memory _adapterParams) internal pure returns (uint16 txType, uint uaGas, uint airdropAmount, address payable airdropAddress) {

74:     function bytes32ToAddress(bytes32 _bytes32Address) internal pure returns (address _address) {

78:     function addressToBytes32(address _address) internal pure returns (bytes32 _bytes32Address) {

```

```solidity
File: OFT/util/BitLib.sol

18:     function mostSignificantBitPosition(uint256 x) internal pure returns (uint8 r) {

52:     function countSetBits(uint x) internal pure returns (uint256) {

```

```solidity
File: OFT/util/BytesLib.sol

13:     function concat(

91:     function concatStorage(bytes storage _preBytes, bytes memory _postBytes) internal {

228:     function slice(

297:     function toAddress(bytes memory _bytes, uint256 _start) internal pure returns (address) {

308:     function toUint8(bytes memory _bytes, uint256 _start) internal pure returns (uint8) {

319:     function toUint16(bytes memory _bytes, uint256 _start) internal pure returns (uint16) {

330:     function toUint32(bytes memory _bytes, uint256 _start) internal pure returns (uint32) {

341:     function toUint64(bytes memory _bytes, uint256 _start) internal pure returns (uint64) {

352:     function toUint96(bytes memory _bytes, uint256 _start) internal pure returns (uint96) {

363:     function toUint128(bytes memory _bytes, uint256 _start) internal pure returns (uint128) {

374:     function toUint256(bytes memory _bytes, uint256 _start) internal pure returns (uint256) {

385:     function toBytes32(bytes memory _bytes, uint256 _start) internal pure returns (bytes32) {

396:     function equal(bytes memory _preBytes, bytes memory _postBytes) internal pure returns (bool) {

439:     function equalStorage(

```

```solidity
File: OFT/util/ExcessivelySafeCall.sol

23:     function excessivelySafeCall(

75:     function excessivelySafeStaticCall(

120:     function swapSelector(bytes4 _newSelector, bytes memory _buf)

```

```solidity
File: lybra/pools/base/LybraEUSDVaultBase.sol

307:     function _etherPrice() internal returns (uint256) {

```

```solidity
File: lybra/pools/base/LybraPeUSDVaultBase.sol

244:     function _etherPrice() internal returns (uint256) {

```

```solidity
File: mocks/stETHMock.sol

490:     function _burnShares(

```

