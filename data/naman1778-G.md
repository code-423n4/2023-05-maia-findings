## [G-01] Using calldata instead of memory for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it’s still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I’ve also flagged instances where the function is public but can be marked as external since it’s not called by the contract, and cases where a constructor is involved

There are 27 instances of this issue in 13 files

    File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

    54: function createToken(string memory _name, string memory _symbol)

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

    File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

    60: function createToken(string memory _name, string memory _symbol)

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

    File: src/ulysses-amm/UlyssesToken.sol	

    88: function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

    File: src/ulysses-omnichain/BaseBranchRouter.sol

    65: function callOutAndBridge(bytes calldata params, DepositInput memory dParams, uint128 remoteExecutionGas)

    76: function callOutAndBridgeMultiple(
    77:    bytes calldata params,
    78:    DepositMultipleInput memory dParams,
    79:    uint128 remoteExecutionGas
    80: ) external payable lock {

    112: function anyExecuteSettlement(bytes calldata, SettlementParams memory)

    123: function anyExecuteSettlementMultiple(bytes calldata, SettlementMultipleParams memory)

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol

    File: src/ulysses-omnichain/ArbitrumBranchPort.sol

    96: function bridgeInMultiple(address _recipient, address[] memory _localAddresses, uint256[] memory _amounts)

    129: function bridgeOutMultiple(
    130:     address _depositor,
    131:     address[] memory _localAddresses,
    132:     address[] memory _underlyingAddresses,
    133:     uint256[] memory _amounts,
    134:     uint256[] memory _deposits
    135: ) external override(IBranchPort, BranchPort) requiresBridgeAgent {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

    File: src/ulysses-omnichain/CoreRootRouter.sol

    379: function anyExecuteDepositSingle(bytes1, bytes memory, DepositParams memory, uint24)

    390: function anyExecuteDepositMultiple(bytes1, bytes calldata, DepositMultipleParams memory, uint24)

    400: function anyExecuteSigned(bytes1, bytes memory, address, uint24)

    411: function anyExecuteSignedDepositSingle(bytes1, bytes memory, DepositParams memory, address, uint24)

    422: function anyExecuteSignedDepositMultiple(bytes1, bytes memory, DepositMultipleParams memory, address, uint24)

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol

    File: src/ulysses-omnichain/BranchPort.sol

    226: function bridgeInMultiple(address _recipient, address[] memory _localAddresses, uint256[] memory _amounts)

    260: function bridgeOutMultiple(
    261:    address _depositor,
    262:    address[] memory _localAddresses,
    263:    address[] memory _underlyingAddresses,
    264:    uint256[] memory _amounts,
    265:    uint256[] memory _deposits
    266: ) external virtual requiresBridgeAgent {

    421: function anyExecuteSignedDepositMultiple(
    422:     bytes1 funcId,
    423:     bytes memory encodedData,
    424:     DepositMultipleParams calldata,
    425:     address userAccount,
    426:     uint24
    427: ) external payable requiresExecutor lock returns (bool success, bytes memory result) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol

    File: src/ulysses-omnichain/RootPort.sol	

    420: function addNewChain(
    421:     address _pledger,
    422:     uint256 _pledgedInitialAmount,
    423:     address _coreBranchBridgeAgentAddress,
    424:     uint24 _chainId,
    425:     string memory _wrappedGasTokenName,
    426:     string memory _wrappedGasTokenSymbol,
    427:     uint24 _fee,
    428:     uint24 _priceImpactPercentage,
    429:     uint160 _sqrtPriceX96,
    430:     address _nonFungiblePositionManagerAddress,
    431:     address _newLocalBranchWrappedNativeTokenAddress,
    432:     address _newUnderlyingBranchWrappedNativeTokenAddress
    433: ) external onlyOwner {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol

    File: src/uni-v3-staker/UniswapV3Staker.sol 

    157: function createIncentive(IncentiveKey memory key, uint256 reward) external {

    187: function endIncentive(IncentiveKey memory key) external returns (uint256 refund) {

    243: function withdrawToken(uint256 tokenId, address to, bytes memory data) external {

    287: function getRewardInfo(IncentiveKey memory key, uint256 tokenId)

    361: function unstakeToken(IncentiveKey memory key, uint256 tokenId) external {

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol

    File: src/ulysses-omnichain/RootBridgeAgent.sol

    275: function callOut(address _recipient, bytes memory _data, uint24 _toChain) external payable lock requiresRouter {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

    File: src/ulysses-omnichain/BranchBridgeAgent.sol

    192: function callOutAndBridge(bytes calldata _params, DepositInput memory _dParams, uint128 _remoteExecutionGas)

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

    File: src/talos/factories/TalosBaseStrategyFactory.sol	

    55: function createTalosBaseStrategy(
    57:     IUniswapV3Pool pool,
    58:     ITalosOptimizer optimizer,
    59:     address strategyManager,
    60:     bytes memory data
    61: ) external {

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol

    File: src/gauges/factories/BaseV2GaugeFactory.sol 

    109: function createGauge(address strategy, bytes memory data) external onlyOwner {

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized("Naman");
            c1.optimized("Naman");
        }
    }
    
    contract Contract0 {
    
         function not_optimized(string memory a) public returns(string memory){
             return a;
         }
    }
    
    contract Contract1 {
    
         function optimized(string calldata a) public returns(string calldata){
             return a;
         }
    }

#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 100747                                    | 535             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 790             | 790 | 790    | 790 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 66917                                     | 366             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 556             | 556 | 556    | 556 | 1       |

## [G-02] Use abi.encodePacked() instead of abi.encode()

Changing abi.encode function to abi.encodePacked can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient (see [Solidity-Encode-Gas-Comparison](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)).

Consider using abi.encodePacked() here:

Use abi.encodePacked() where possible to save gas

There are 17 instances of this issue in 8 files:

    File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

    53: abi.encode(_underlyingAddress, address(0), name, symbol);

    98: bytes memory data = abi.encode(newBridgeAgent, _rootBridgeAgent);

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

    File: src/ulysses-omnichain/CoreBranchRouter.sol 	

    48: bytes memory data = abi.encode(msg.sender, _globalAddress, _toChain, _rootExecutionGas);

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol

    File: src/ulysses-omnichain/CoreRootRouter.sol 

    107: bytes memory data = abi.encode(

    158: bytes memory data = abi.encode(

    238: bytes memory data = abi.encode(_branchBridgeAgentFactory);

    259: bytes memory data = abi.encode(_branchBridgeAgent);

    282: bytes memory data = abi.encode(_underlyingToken, _minimumReservesRatio);

    309: bytes memory data = abi.encode(_portStrategy, _underlyingToken, _dailyManagementLimit, _isUpdateDailyLimit);

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol

    File: src/governance/GovernorBravoDelegateMaia.sol

    198: !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

    346: keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

    347: bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

    File: src/ulysses-omnichain/RootBridgeAgent.sol

    689: abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

    733: abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

    File: src/erc-20/ERC20MultiVotes.sol

    368: "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol

    File: src/uni-v3-staker/libraries/IncentiveId.sol

    17: return keccak256(abi.encode(key));

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol

    File: src/talos/libraries/PoolActions.sol	

    51: abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized();
            c1.optimized();
        }
    }
    contract Contract0 {
        string a = "Code4rena";
        function not_optimized() public returns(bytes32){
            return keccak256(abi.encode(a));
        }
    }
    contract Contract1 {
        string a = "Code4rena";
        function optimized() public returns(bytes32){
            return keccak256(abi.encodePacked(a));
        }
    }

#### Gas Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 101871                                    | 683             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| not_optimized                             | 2661            | 2661 | 2661   | 2661 | 1       |


| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 99465                                     | 671             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| optimized                                 | 2608            | 2608 | 2608   | 2608 | 1       |

## [G-03] Using XOR (^) and OR (|) bitwise equivalents // check for equal to and not equal to

On Remix, given only uint256 types, the following are logical equivalents, but don’t cost the same amount of gas:

(a != b || c != d || e != f) costs 571
((a ^ b) | (c ^ d) | (e ^ f)) != 0 costs 498 (saving 73 gas)
Consider rewriting as following to save gas:

To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.
Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.

Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas:

However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values

There are 7 instances of this issue in 4 files:

    File: src/ulysses-omnichain/RootPort.sol 	

    494: getUnderlyingTokenFromLocal[_ecoTokenGlobalAddress][localChainId] != address(0)
    495:     || getLocalTokenFromUnder[_ecoTokenGlobalAddress][localChainId] != address(0)

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol

    File: src/governance/GovernorBravoDelegateMaia.sol

    449: msg.sender == admin || msg.sender == whitelistGuardian,

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

    File: src/ulysses-omnichain/RootBridgeAgent.sol

    665: if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();

    709: if (gasTokenGlobalAddress == address(0) || poolAddress == address(0)) revert InvalidGasPool();

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

    File: src/ulysses-omnichain/BranchBridgeAgent.sol

    814: _hTokens.length != _tokens.length || _tokens.length != _amounts.length
    815:     || _amounts.length != _deposits.length

    1243: if ((flag == 0x00) || (flag == 0x01) || (flag == 0x02)) {

    1272: } else if ((flag == 0x04) || (flag == 0x05)) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized(5,10,16);
            c1.optimized(5,10,16);
        }
    }
    contract Contract0 {
        address a;
        function not_optimized(uint8 x,uint8 y, uint8 z) public returns(bool){
            if(x!=5 || y!=10 || z!=15){
                return true;
            }
        }
    }
    contract Contract1 {
        address a;
        function optimized(uint8 x,uint8 y, uint8 z) public returns(bool){
            if(((x^5) | (y^10) | (z^15)) != 0){
                return true;
            }
        }
    }
    
#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 53705                                     | 300             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 622             | 622 | 622    | 622 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 49899                                     | 280             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 569             | 569 | 569    | 569 | 1       |v

## [G-04] Instead of counting down in for statements, count up

Counting down is more gas efficient than counting up in for loop.
There are 65 instances of this issue in 20 files:

    File: src/ulysses-omnichain/VirtualAccount.sol	

    48: for (uint256 i = 0; i < calls.length; i++) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol

    File: src/ulysses-amm/UlyssesRouter.sol 
 
    78: for (uint256 i = 0; i < length;) {
    82:        ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol

    File: src/ulysses-amm/UlyssesToken.sol	

    95: for (uint256 i = 0; i < assets.length; i++) {

    111: for (uint256 i = 0; i < assets.length; i++) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

    File: src/gauges/factories/BaseV2GaugeManager.sol 

    64: for (uint256 i = 0; i < length;) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol

    File: src/ulysses-amm/factories/UlyssesFactory.sol

    99: for (uint256 i = 0; i < length;) {
    103:         ++i;

    107: for (uint256 i = 0; i < length;) {
    119:         ++i;

    110: for (uint256 j = 0; j < length;) {    
    114:         ++j;

    123: for (uint256 i = 0; i < length;) { 
    127:         ++i;

    145: for (uint256 i = 0; i < length;) {
    153:         ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol

    File: src/ulysses-omnichain/ArbitrumBranchPort.sol

    101: for (uint256 i = 0; i < _localAddresses.length;) {
    105:         ++i;

    136: for (uint256 i = 0; i < _localAddresses.length;) {
    151:         ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol

    File: src/rewards/rewards/FlywheelGaugeRewards.sol	

    176: for (uint256 i = 0; i < size; i++) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol

    File: src/ulysses-omnichain/BranchPort.sol

    231: for (uint256 i = 0; i < _localAddresses.length;) {
    235:         ++i;

    267: for (uint256 i = 0; i < _localAddresses.length;) {
    280:         i++;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol

    File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

    364: for (uint256 i = 0; i < uint256(uint8(numOfAssets));) {
    411:         ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol

    File: src/ulysses-omnichain/MulticallRootRouter.sol	

    146: for (uint256 i = 0; i < outputTokens.length;) {
    150:         ++i;

    311: for (uint256 i = 0; i < outputParams.outputTokens.length;) {
    315:         ++i;

    387: for (uint256 i = 0; i < outputParams.outputTokens.length;) {
    391:         ++i;

    463: for (uint256 i = 0; i < outputParams.outputTokens.length;) {
    467:         ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol

    File: src/governance/GovernorBravoDelegateMaia.sol

    181: for (uint256 i = 0; i < proposal.targets.length; i++) {

    215: for (uint256 i = 0; i < proposal.targets.length; i++) {

    251: for (uint256 i = 0; i < proposal.targets.length; i++) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

    File: src/ulysses-amm/UlyssesPool.sol 	

    130: for (uint256 i = 1; i < bandwidthStateList.length; i++) {

    175: for (uint256 i = 1; i < index; i++) {

    189: for (uint256 i = 1; i < index;) {
    198:         ++i;

    211: for (uint256 i = 1; i <= index; i++) {

    232: for (uint256 i = 1; i < bandwidthStateList.length; i++) {

    253: for (uint256 i = 1; i < bandwidthStateList.length;) {
    265:         ++i;

    278: for (uint256 i = 1; i < bandwidthStateList.length;) {
    289:         ++i;

    296: for (uint256 i = 1; i < bandwidthStateList.length; i++) {

    906: for (uint256 i = 1; i < length;) {
    931:         ++i;

    961: for (uint256 i = 1; i < length;) {
    1002:         ++i;

    1043: for (uint256 i = 1; i < length;) {
    1069:         ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol

    File: src/ulysses-omnichain/RootBridgeAgent.sol

    342: for (uint256 i = 0; i < _globalAddresses.length;) {
    354:         ++i;

    408: for (uint256 i = 0; i < _dParams.hTokens.length;) {
    423:         ++i;

    561: for (uint256 i = 0; i < newGas.length;) {
    564:         ++i;

    596: for (uint256 i = 0; i < settlement.hTokens.length;) {
    610:         ++i;

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol

    File: src/ulysses-omnichain/BranchBridgeAgent.sol

    283: for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
    
    580: for (uint256 i = 0; i < numOfAssets;) {
    632:         ++i; 

    719: for (uint256 i = 0; i < _dParams.hTokens.length; i++) {
    
    951: for (uint256 i = 0; i < deposit.hTokens.length;) {
    955:         ++i;

    1354: for (uint256 i = 0; i < _deposits.length; i++) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol

    File: src/gauges/BaseV2Gauge.sol 

    114: for (uint256 i = 0; i < length;) {
    118:         i++;

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol

    File: src/gauges/factories/BaseV2GaugeFactory.sol 	

    77: for (uint256 i = 0; i < length;) {
    81:         i++;

    93: for (uint256 i = start; i < end;) {
    97:         i++;

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol

    File: src/erc-4626/ERC4626MultiToken.sol 

    50: for (uint256 i = 0; i < length;) {
    60:         i++;

    68: for (uint256 i = 0; i < length;) {
    72:         i++;

    79: for (uint256 i = 0; i < length;) {
    83:         i++;

    170: for (uint256 i = 0; i < length;) {
    174:         i++;

    201: for (uint256 i = 0; i < length;) {
    205:         i++;

    216: for (uint256 i = 0; i < length;) {
    219:         i++;

    235: for (uint256 i = 0; i < length;) {
    238:         i++;

    250: for (uint256 i = 0; i < length;) {
    254:         i++;

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol

    File: src/erc-20/ERC20Boost.sol 	    

    52: for (uint256 i = 0; i < num;) {
    55:         i++;
 
    98: for (uint256 i = 0; i < num;) {
    101:         i++;

    156: for (uint256 i = 0; i < length;) {
    166:         i++;

    207: for (uint256 i = 0; i < num && i < length;) {
    224:         i++;

    236: for (uint256 i = 0; i < size;) {
    245:         i++;

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol

    File: src/erc-20/ERC20MultiVotes.sol

    328: for (uint256 i = 0; i < size && (userFreeVotes + totalFreed) < votes; i++) {

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol

    File: src/erc-20/ERC20Gauges.sol	

    114: for (uint256 i = 0; i < num;) {
    117:         i++;

    155: for (uint256 i = 0; i < num;) {
    158:         i++;

    259: for (uint256 i = 0; i < size;) {
    266:         i++;

    338: for (uint256 i = 0; i < size;) {
    346:         i++;       

    536: for (uint256 i = 0; i < size && (userFreeWeight + totalFreed) < weight;) {
    548:         i++;

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.AddNum();
            c1.AddNum();
        }
    }


    contract Contract0 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=0;i<=9;i++){
                _num = _num +1;
            }
            num = _num;
        }
    }


    contract Contract1 {
        uint256 num = 3;
        function AddNum() public {
            uint256 _num = num;
            for(uint i=9;i>=0;i--){
                _num = _num +1;
            }
            num = _num;
        }
    }

#### Gas Report

| Contract0 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 77011                                     | 311             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 7040            | 7040 | 7040   | 7040 | 1       |

| Contract1 contract                        |                 |      |        |      |         |
|-------------------------------------------|-----------------|------|--------|------|---------|
| Deployment Cost                           | Deployment Size |      |        |      |         |
| 73811                                     | 295             |      |        |      |         |
| Function Name                             | min             | avg  | median | max  | # calls |
| AddNum                                    | 3819            | 3819 | 3819   | 3819 | 1       | 


## [G-05] Use != 0 instead of > 0

Using > 0 costs more gas than != 0 when used on a uint.

There are 2 instances of this issue in 2 files:

    File: src/ulysses-amm/UlyssesToken.sol	

    35: require(_weight > 0);

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol

    File: src/erc-4626/ERC4626MultiToken.sol 	

    52: require(_weights[i] > 0);

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol

#### Test Code

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized(5);
            c1.optimized(5);
        }
    }
    
    contract Contract0 {
        function not_optimized(uint8 x) public returns(uint8){
            require(x>0,"x is greater than 0");
        }
    }
    
    contract Contract1 {
        function optimized(uint8 x) public returns(uint8){
            require(x!=0,"x is greater than 0");
        }
    }v

#### Gas Report

| Contract0 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 58911                                     | 326             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| not_optimized                             | 327             | 327 | 327    | 327 | 1       |


| Contract1 contract                        |                 |     |        |     |         |
|-------------------------------------------|-----------------|-----|--------|-----|---------|
| Deployment Cost                           | Deployment Size |     |        |     |         |
| 59111                                     | 327             |     |        |     |         |
| Function Name                             | min             | avg | median | max | # calls |
| optimized                                 | 327             | 327 | 327    | 327 | 1       |

## [G-06] Stack variable used as a cheaper cache for a state variable is only used once

If the variable is only accessed once, it’s cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend.

There are 5 instances of this issue in 3 files:

    File: src/ulysses-amm/UlyssesRouter.sol 
 
    50: UlyssesPool ulysses = getUlyssesLP(poolId);

    60: UlyssesPool ulysses = getUlyssesLP(poolId);

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol

    File: src/gauges/factories/BribesFactory.sol 	

    91: uint256 id = bribeFlywheels.length;

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol

    File: src/ulysses-omnichain/RootPort.sol 	

    189: address globalAddress = getGlobalTokenFromLocal[_localAddress][_fromChain];

    204: address localAddress = getLocalTokenFromGlobal[_globalAddress][_fromChain];

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol

#### Test Code 

    contract GasTest is DSTest {
        Contract0 c0;
        Contract1 c1;
        function setUp() public {
            c0 = new Contract0();
            c1 = new Contract1();
        }
        function testGas() public {
            c0.not_optimized();
            c1.optimized();
        }
    }
    
    contract Contract0 {
        uint256 num = 5;
        uint256 sum;
        function not_optimized() public returns(bool){
            uint256 num1 = num;
            sum = num1 + 2;
        }
    }
    
    contract Contract1 {
        uint256 num = 5;
        uint256 sum;
        function optimized() public returns(bool){
            sum = num + 2;
        }
    }

#### Gas Report

| Contract0 contract                        |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 63799                                     | 244             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| not_optimized                             | 24462           | 24462 | 24462  | 24462 | 1       |


| Contract1 contract                        |                 |       |        |       |         |
|-------------------------------------------|-----------------|-------|--------|-------|---------|
| Deployment Cost                           | Deployment Size |       |        |       |         |
| 63599                                     | 243             |       |        |       |         |
| Function Name                             | min             | avg   | median | max   | # calls |
| optimized                                 | 24460           | 24460 | 24460  | 24460 | 1       |