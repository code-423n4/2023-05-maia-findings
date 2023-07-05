|      |   ISSUE   |  INSTANCE  |
|------|-----------|------------|
|[G-01]|Avoid contract existence checks by using low level calls | 168 |
|[G-02]|Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard | 28 |
|[G-03]|Avoid emitting storage values|3|
|[G-04]|Using > 0 costs more gas than != 0 when used on a uint in a require() statement|2|
|[G-05]|Can Make The Variable Outside The Loop To Save Gas |27|
|[G-06]|Structs can be packed into fewer storage slots|6|
|[G-07]|Make 3 event parameters indexed when possible|75|
|[G-08]|>= costs less gas than >|21|
|[G-09]|Expressions for constant values such as a call to keccak256(), should use immutable rather than constant|3|
|[G-10]|Using private rather than public for constants, saves gas|10|
|[G-11]|Do not calculate constants|2|
|[G-12]|State variables can be cached instead of re-reading them from storage|10|
|[G-13]|Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement|6|
|[G-14]|abi.encode() is less efficient than abi.encodePacked()|4|
|[G-15]|Use constants instead of type(uintx).max|40|
|[G-16]|Use hardcode address instead address(this)|42|
|[G-17]|A modifier used only once and not being inherited should be inlined to save gas|7|
|[G-18]|Using delete statement can save gas|5|
|[G-19]| Amounts should be checked for 0 before calling a transfer|5|
|[G-20]| Use assembly to hash instead of solidity|6|
|[G-21]| Loop best practice to save gas | 14 |
|[G-22]| Gas savings can be achieved by changing the model for assigning value to the structure|8|
|[G-23]|Use assembly for math (add, sub, mul, div)|6|
|[G-24]| Access mappings directly rather than using accessor functions|10|
|[G-25]|internal functions not called by the contract should be removed to save deployment gas|1|
|[G-26]|use Mappings Instead of Arrays|2|

## [G‑01] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence.

there are 168 instance of this issue
```solidity
File: /src/erc-20/ERC20Gauges.sol
208    IBaseV2Gauge(gauge).accrueBribes(user);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L208

```solidity
File: /src/erc-4626/UlyssesERC4626.sol
27   if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L27


```solidity
File: /src/gauges/factories/UniswapV3GaugeFactory.sol
100   UniswapV3Gauge(gauge).setMinimumWidth(minimumWidth);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L100

```solidity
File: /src/gauges/UniswapV3Gauge.sol
54    IUniswapV3Staker(uniswapV3Staker).createIncentiveFromGauge(amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L54


```solidity
File: /src/hermes/minters/BaseV2Minter.sol
61    underlying = address(ERC4626(_vault).asset());

109   return HERMES(underlying).totalSupply() - vault.totalAssets();

119   return (vault.totalAssets() * _minted) / HERMES(underlying).totalSupply();

140   HERMES(underlying).mint(address(this), _required - _balanceOf);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L61

```solidity
File: /src/maia/tokens/ERC4626PartnerManager.sol

192   if (oldPartnerVault != address(0)) IBaseVault(oldPartnerVault).clearAll();

206   if (newPartnerVault != address(0)) IBaseVault(newPartnerVault).applyAll();

244   ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L192

```solidity
File: /src/rewards/FlywheelCoreInstant.sol
41    return IFlywheelInstantRewards(flywheelRewards).getAccruedRewards();
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreInstant.sol#L41


```solidity
File: /src/rewards/FlywheelCoreStrategy.sol
40   return IFlywheelAcummulatedRewards(flywheelRewards).getAccruedRewards(strategy);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/rewards/FlywheelCoreStrategy.sol#L40


```solidity
File: /src/talos/TalosStrategyStaked.sol
82    _boostAggregator.setOwnRewardsDepot(address(FlywheelInstantRewards(_flywheel.flywheelRewards()).rewardsDepot()));
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyStaked.sol#L82

```solidity
File: /src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol
104   IPort(localPortAddress).addBridgeAgent(newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L104

```solidity
File: /src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol
99    IPort(localPortAddress).addBridgeAgent(newCoreBridgeAgent);

139   IPort(localPortAddress).addBridgeAgent(newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L99


```solidity
File: /src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol
62    localAnyCallExecutorAddress = IAnycallProxy(localAnyCallAddress).executor();

88    IRootPort(rootPortAddress).addBridgeAgent(msg.sender, newBridgeAgent);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L62


```solidity
File: /src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol
103   IArbPort(localPortAddress).depositToPort(
            msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
        );

115   IArbPort(localPortAddress).withdrawFromPort(msg.sender, msg.sender, localAddress, amount);

143   IRootBridgeAgent(rootBridgeAgentAddress).anyExecute(_callData);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L103

```solidity
File: /src/ulysses-omnichain/ArbitrumBranchPort.sol
49    address globalToken = IRootPort(rootPortAddress).getLocalTokenFromUnder(_underlyingAddress, localChainId);

54    IRootPort(rootPortAddress).mintToLocalBranch(_recipient, globalToken, _deposit);

62    if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {

66     address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);

70   IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);

72   underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));

82   _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

92    IRootPort(rootPortAddress).bridgeToLocalBranchFromRoot(_recipient, _localAddress, _amount);

102   IRootPort(rootPortAddress).bridgeToLocalBranchFromRoot(_recipient, _localAddresses[i], _amounts[i]);

120   _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

124   IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(_depositor, _localAddress, _amount - _deposit);

141   _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())

145   IRootPort(rootPortAddress).bridgeToRootFromLocalBranch(
                    _depositor, _localAddresses[i], _amounts[i] - _deposits[i]
                );
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L49

```solidity
File: /src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol
49  string memory name = ERC20(_underlyingAddress).name();

50  string memory symbol = ERC20(_underlyingAddress).symbol();

59   IBridgeAgent(localBridgeAgentAddress).performCallOut(msg.sender, packedData, 0, 0);

83    if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {

88   address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(
            _newBranchRouter, _rootBridgeAgent, _rootBridgeAgentFactory
        );    

93   if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {

104  IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, 0, 0);    
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L49


```solidity
File: /src/ulysses-omnichain/BaseBranchRouter.sol
40    bridgeAgentExecutorAddress = IBridgeAgent(localBridgeAgentAddress).bridgeAgentExecutorAddress();

50    return IBridgeAgent(localBridgeAgentAddress).getDepositEntry(_depositNonce);

59    IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(
            msg.sender, params, 0, remoteExecutionGas
        );
70     IBridgeAgent(localBridgeAgentAddress).performCallOutAndBridge{value: msg.value}(
            msg.sender, params, dParams, 0, remoteExecutionGas
        );
81         IBridgeAgent(localBridgeAgentAddress).performCallOutAndBridgeMultiple{value: msg.value}(
            msg.sender, params, dParams, 0, remoteExecutionGas
        );

88      IBridgeAgent(localBridgeAgentAddress).retrySettlement{value: msg.value}(_settlementNonce, _gasToBoostSettlement);

93    IBridgeAgent(localBridgeAgentAddress).redeemDeposit(_depositNonce);


```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BaseBranchRouter.sol#L40


```solidity
File: /src/ulysses-omnichain/BranchBridgeAgent.sol
252   _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),

284   _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

342    getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

357     getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()

624      IPort(localPortAddress).bridgeIn(_recipient, _hTokens[i], _amounts[i] - _deposits[i]);

628    IPort(localPortAddress).withdraw(_recipient, _tokens[i], _deposits[i]);

687    _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),

720    deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

866    IPort(localPortAddress).bridgeOut(_user, _hToken, _token, _amount, _deposit);

912    IPort(localPortAddress).bridgeOutMultiple(_user, _hTokens, _tokens, _amounts, _deposits);

980     IPort(localPortAddress).bridgeIn(_recipient, _hToken, _amount - _deposit);

984    IPort(localPortAddress).withdraw(_recipient, _token, _deposit);

1008    IAnycallProxy(localAnyCallAddress).anyCall(
            rootBridgeAgentAddress, _calldata, rootChainId, AnycallFlags.FLAG_ALLOW_FALLBACK, ""
        );

1078     IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);

1103   IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);

1110   (from, fromChainId,) = IAnycallExecutor(localAnyCallExecutorAddress).context();

1154   try BranchBridgeAgentExecutor(bridgeAgentExecutorAddress).executeNoSettlement(localRouterAddress, data)

1177    try BranchBridgeAgentExecutor(bridgeAgentExecutorAddress).executeWithSettlement(

1201     try BranchBridgeAgentExecutor(bridgeAgentExecutorAddress).executeWithSettlementMultiple(

1324    IAnycallConfig anycallConfig = IAnycallConfig(IAnycallProxy(localAnyCallAddress).config());

1388    (address from,,) = IAnycallExecutor(localAnyCallExecutorAddress).context();

76    (success, result) = IRouter(_router).anyExecuteNoSettlement(_data[25:_data.length - PARAMS_GAS_OUT]);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L252

```solidity
File: /src/ulysses-omnichain/BranchBridgeAgentExecutor.sol
104   BranchBridgeAgent(payable(msg.sender)).clearToken(
            sParams.recipient, sParams.hToken, sParams.token, sParams.amount, sParams.deposit
        );

110    (success, result) = IRouter(_router).anyExecuteSettlement(_data[129:_data.length - PARAMS_GAS_OUT], sParams);

147   (success, result) = IRouter(_router).anyExecuteSettlementMultiple(    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L104

```solidity
File: /src/ulysses-omnichain/BranchPort.sol
127   uint256 currBalance = ERC20(_token).balanceOf(address(this));

138   uint256 currBalance = ERC20(_token).balanceOf(address(this));

180   IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

212    _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

222   ERC20hTokenBranch(_localAddress).mint(_recipient, _amount);

232    ERC20hTokenBranch(_localAddresses[i]).mint(_recipient, _amounts[i]);

250   ERC20hTokenBranch(_localAddress).burn(_amount - _deposit);

254  _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

272  _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())


```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127


```solidity
File: /src/ulysses-omnichain/CoreBranchRouter.sol
54   IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(
            msg.sender, packedData, 0, _remoteExecutionGas
        );

65    string memory name = ERC20(_underlyingAddress).name();

66    string memory symbol = ERC20(_underlyingAddress).symbol();   

69    ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(name, symbol);

78     IBridgeAgent(localBridgeAgentAddress).performCallOut{value: msg.value}(msg.sender, packedData, 0, 0);

102    ERC20hToken newToken = ITokenFactory(hTokenFactoryAddress).createToken(_name, _symbol);

111    IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _rootExecutionGas, 0);

133    if (!IPort(localPortAddress).isBridgeAgentFactory(_branchBridgeAgentFactory)) {

138     address newBridgeAgent = IBridgeAgentFactory(_branchBridgeAgentFactory).createBridgeAgent(
            _newBranchRouter, _rootBridgeAgent, _rootBridgeAgentFactory
        );

143     if (!IPort(localPortAddress).isBridgeAgent(newBridgeAgent)) {
            revert UnrecognizedBridgeAgent();
        }

154     IBridgeAgent(localBridgeAgentAddress).performSystemCallOut(address(this), packedData, _remoteExecutionGas, 0);

164   if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {

165   IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);

167   IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);

178     if (!IPort(localPortAddress).isBridgeAgent(_branchBridgeAgent)) revert UnrecognizedBridgeAgent();

179   IPort(localPortAddress).toggleBridgeAgent(_branchBridgeAgent);

190   if (!IPort(localPortAddress).isStrategyToken(_underlyingToken)) {

191   IPort(localPortAddress).addStrategyToken(_underlyingToken, _minimumReservesRatio);

193   IPort(localPortAddress).toggleStrategyToken(_underlyingToken);

212   if (!IPort(localPortAddress).isPortStrategy(_portStrategy, _underlyingToken)) {

214   IPort(localPortAddress).addPortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

217    IPort(localPortAddress).updatePortStrategy(_portStrategy, _underlyingToken, _dailyManagementLimit);

220   IPort(localPortAddress).togglePortStrategy(_portStrategy, _underlyingToken);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreBranchRouter.sol#L54


```solidity
File: /src/ulysses-omnichain/CoreRootRouter.sol
65    bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

90    if (msg.sender != IPort(rootPortAddress).getBridgeAgentManager(_rootBridgeAgent)) {
            revert UnauthorizedCallerNotManager();
        }

95    if (!IPort(rootPortAddress).isChainId(_toChain)) revert InvalidChainId();

98     if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_toChain) != address(0)) revert InvalidChainId();

101   if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_toChain)) revert UnauthorizedChainId();

104   address rootBridgeAgentFactory = IBridgeAgent(_rootBridgeAgent).factoryAddress();

115   IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

128     IPort(rootPortAddress).syncBranchBridgeAgentWithRoot(_newBranchBridgeAgent, _rootBridgeAgent, _fromChain);
    }

148   if (!IPort(rootPortAddress).isGlobalAddress(_globalAddress)) {

153   if (IPort(rootPortAddress).isGlobalToken(_globalAddress, _toChain)) {

159    _globalAddress, ERC20(_globalAddress).name(), ERC20(_globalAddress).symbol(), _remoteExecutionGas

166   IBridgeAgent(bridgeAgentAddress).callOut(_gasReceiver, packedData, _toChain);

187    IPort(rootPortAddress).isGlobalAddress(_underlyingAddress)

188      || IPort(rootPortAddress).isLocalToken(_underlyingAddress, _fromChain)

189      || IPort(rootPortAddress).isUnderlyingToken(_underlyingAddress, _fromChain)

193    address newToken = address(IFactory(hTokenFactoryAddress).createToken(_name, _symbol));

196    IPort(rootPortAddress).setAddresses(
            newToken, (_fromChain == rootChainId) ? newToken : _localAddress, _underlyingAddress, _fromChain
        );

210    if (IPort(rootPortAddress).isLocalToken(_localAddress, _toChain)) revert TokenAlreadyAdded();

213    IPort(rootPortAddress).setLocalAddress(_globalAddress, _localAddress, _toChain);

233    if (!IPort(rootPortAddress).isBridgeAgentFactory(_rootBridgeAgentFactory)) {

244    IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

265      IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);

288     IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);


315     IBridgeAgent(bridgeAgentAddress).callOut{value: msg.value}(_gasReceiver, packedData, _toChain);
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/ulysses-omnichain/CoreRootRouter.sol#L65

```solidity
File: /src/ulysses-omnichain/MulticallRootRouter.sol
78     bridgeAgentExecutorAddress = IBridgeAgent(_bridgeAgentAddress).bridgeAgentExecutorAddress();

96    (blockNumber, returnData) = IMulticall(multicallAddress).aggregate(calls);

120   ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

123   IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(
            owner, recipient, "", outputToken, amountOut, depositOut, toChain
        );

155   IBridgeAgent(bridgeAgentAddress).callOutAndBridgeMultiple{value: msg.value}(
            owner, recipient, "", outputTokens, amountsOut, depositsOut, toChain
        );   
        
281   IVirtualAccount(userAccount).call(calls);

289   IVirtualAccount(userAccount).call(calls);

292    IVirtualAccount(userAccount).withdrawERC20(outputParams.outputToken, outputParams.amountOut);

295   IVirtualAccount(userAccount).userAddress(),

309   IVirtualAccount(userAccount).call(calls);

312     IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

320   IVirtualAccount(userAccount).userAddress(),

388   IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

396   IVirtualAccount(userAccount).userAddress(),

444   IVirtualAccount(userAccount).withdrawERC20(outputParams.outputToken, outputParams.amountOut);

464    IVirtualAccount(userAccount).withdrawERC20(outputParams.outputTokens[i], outputParams.amountsOut[i]);

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L78

```solidity
File: /src/ulysses-omnichain/RootBridgeAgentExecutor.sol
84   (success, result) = IRouter(_router).anyExecuteResponse(
            bytes1(_data[PARAMS_TKN_START]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId
        );
105    IRouter(_router).anyExecute(bytes1(_data[5]), _data[6:_data.length - PARAMS_GAS_IN], _fromChainId);

137   (success, result) = IRouter(_router).anyExecuteDepositSingle(
                _data[112], _data[113:_data.length - PARAMS_GAS_IN], dParams, _fromChainId
            );

177   (success, result) = IRouter(_router).anyExecuteDepositMultiple(
                bytes1(_data[PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE]),
                _data[
                    PARAMS_START + PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE:
                        length - PARAMS_GAS_IN
                ],
                dParams,
                _fromChainId
            );
208        IRouter(_router).anyExecuteSigned(_data[25], _data[26:_data.length - PARAMS_GAS_IN], _account, _fromChainId);

241   (success, result) = IRouter(_router).anyExecuteSignedDepositSingle(
                _data[132], _data[133:_data.length - PARAMS_GAS_IN], dParams, _account, _fromChainId
            );

283   (success, result) = IRouter(_router).anyExecuteSignedDepositMultiple(
                    _data[PARAMS_END_SIGNED_OFFSET
                        + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE],
                    _data[
                        PARAMS_START + PARAMS_END_SIGNED_OFFSET
                            + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE:
                            _data.length - PARAMS_GAS_IN
                    ],
                    dParams,
                    _account,
                    _fromChainId
                );

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L84


```solidity
File: /src/ulysses-omnichain/RootPort.sol
153   IBridgeAgent(_coreRootBridgeAgent).syncBranchBridgeAgent(_coreLocalBranchBridgeAgent, localChainId);

295   ERC20hTokenRoot(_hToken).mint(_to, _amount, _fromChain);

301   ERC20hTokenRoot(_hToken).burn(_from, _amount, _fromChain);

332   ERC20hTokenRoot(_hToken).burn(_from, _amount, localChainId);

383   if (IBridgeAgent(_rootBridgeAgent).getBranchBridgeAgent(_branchChainId) != address(0)) {

386   if (!IBridgeAgent(_rootBridgeAgent).isBranchBridgeAgentAllowed(_branchChainId)) {

389    IBridgeAgent(_rootBridgeAgent).syncBranchBridgeAgent(_newBranchBridgeAgent, _branchChainId);

435    IERC20hTokenRootFactory(ICoreRootRouter(coreRootRouterAddress).hTokenFactoryAddress()).createToken(
                _wrappedGasTokenName, _wrappedGasTokenSymbol
            )
440   ERC20hTokenRoot(newGlobalToken).mint(_pledger, _pledgedInitialAmount, _chainId);

442   IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(
            _coreBranchBridgeAgentAddress, _chainId
        );

465    newGasPoolAddress = INonfungiblePositionManager(_nonFungiblePositionManagerAddress)
                .createAndInitializePoolIfNecessary(newGlobalToken, wrappedNativeTokenAddress, _fee, _sqrtPriceX96);
469    newGasPoolAddress = INonfungiblePositionManager(_nonFungiblePositionManagerAddress)
                .createAndInitializePoolIfNecessary(wrappedNativeTokenAddress, newGlobalToken, _fee, _sqrtPriceX96);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L153


```solidity
File: /src/ulysses-omnichain/VirtualAccount.sol
37   ERC721(_token).transferFrom(address(this), msg.sender, _tokenId);

70   if ((!IRootPort(localPortAddress).isRouterApproved(this, msg.sender)) && (msg.sender != userAddress)) {
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L37

## [G-02] Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard
Using true and false will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using 1 and 2 (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier.

```solidity
File:   src/erc-20/ERC20Gauges.sol
188   function incrementGauge(address gauge, uint112 weight) external nonReentrant returns (uint112 newUserWeight) {

245   function incrementGauges(address[] calldata gaugeList, uint112[] calldata weights)
        external
        nonReentrant
        returns (uint256 newUserWeight)
    {

273    function decrementGauge(address gauge, uint112 weight) external nonReentrant returns (uint112 newUserWeight) {

322   function decrementGauges(address[] calldata gaugeList, uint112[] calldata weights)
        external
        nonReentrant
        returns (uint112 newUserWeight)
    {

519   function _decrementWeightUntilFree(address user, uint256 weight) internal nonReentrant {                
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L188

```solidity
File:   src/erc-4626/ERC4626MultiToken.sol
93    function deposit(uint256[] calldata assetsAmounts, address receiver)
        public
        virtual
        nonReentrant
        returns (uint256 shares)
    {

113   function mint(uint256 shares, address receiver)
        public
        virtual
        nonReentrant
        returns (uint256[] memory assetsAmounts)
    {

132    function withdraw(uint256[] calldata assetsAmounts, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256 shares)
    {

156   function redeem(uint256 shares, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256[] memory assetsAmounts)
    {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L93

```solidity
File:  src/erc-4626/UlyssesERC4626.sol
34   function deposit(uint256 assets, address receiver) public virtual nonReentrant returns (uint256 shares) {

47   function mint(uint256 shares, address receiver) public virtual nonReentrant returns (uint256 assets) {

59    function redeem(uint256 shares, address receiver, address owner)
        public
        virtual
        nonReentrant
        returns (uint256 assets)
    {

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L34

```solidity
File:   src/talos/base/TalosBaseStrategy.sol
102   function init(uint256 amount0Desired, uint256 amount1Desired, address receiver)
        external
        virtual
        nonReentrant
        returns (uint256 shares, uint256 amount0, uint256 amount1)
    {

182  function deposit(uint256 amount0Desired, uint256 amount1Desired, address receiver)
        public
        virtual
        override
        nonReentrant
        checkDeviation
        returns (uint256 shares, uint256 amount0, uint256 amount1)
    {

238   function redeem(uint256 shares, uint256 amount0Min, uint256 amount1Min, address receiver, address _owner)
        public
        virtual
        override
        nonReentrant
        checkDeviation
        returns (uint256 amount0, uint256 amount1)
    {

298   function rerange() external virtual override nonReentrant checkDeviation onlyStrategyManager {

311  function rebalance() external virtual override nonReentrant checkDeviation onlyStrategyManager {

394   function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {        
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L102

```solidity
File:   src/ulysses-amm/UlyssesPool.sol
150    function claimProtocolFees() external nonReentrant returns (uint256 claimed) {

159    function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {

223     function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {

308     function setFees(Fees calldata _fees) external nonReentrant onlyOwner {

323     function setProtocolFee(uint256 _protocolFee) external nonReentrant {

1093    function swapIn(uint256 assets, uint256 poolId) external nonReentrant returns (uint256 output) {

1147     function swapFromPool(uint256 assets, address user) external nonReentrant returns (uint256 output) {        
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L150

```solidity
File:  src/ulysses-amm/UlyssesToken.sol
44   function addAsset(address asset, uint256 _weight) external nonReentrant onlyOwner {

60   function removeAsset(address asset) external nonReentrant onlyOwner {

88    function setWeights(uint256[] memory _weights) external nonReentrant onlyOwner {        
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L44

## [G-03] Avoid emitting storage values
Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. We can avoid unecessary SLOADs by caching storage values that were previously accessed and emitting those cached values.
```solidity
File:   src/talos/base/TalosBaseStrategy.sol
160   emit Initialize(tokenId, msg.sender, receiver, amount0, amount1, shares);

305   emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);

318   emit Rerange(tokenId, tickLower, tickUpper, amount0, amount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L160

## [G-04] Using > 0 costs more gas than != 0 when used on a uint in a require() statement
This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.
```solidity
File:  src/erc-4626/ERC4626MultiToken.sol
52    require(_weights[i] > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52

```solidity
File:    src/ulysses-amm/UlyssesToken.sol
47       require(_weight > 0);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L47

## [G-05] Can Make The Variable Outside The Loop To Save Gas 
When you declare a variable inside a loop, Solidity creates a new instance of the variable for each iteration of the loop. This can lead to unnecessary gas costs, especially if the loop is executed frequently or iterates over a large number of elements.

By declaring the variable outside the loop, you can avoid the creation of multiple instances of the variable and reduce the gas cost of your contract. Here's an example:

```
contract MyContract {
    function sum(uint256[] memory values) public pure returns (uint256) {
        uint256 total = 0;
        
        for (uint256 i = 0; i < values.length; i++) {
            total += values[i];
        }
        
        return total;
    }
}
```
```solidity
File:  src/erc-20/ERC20Boost.sol
157   address gauge = gaugeList[i];

208   address gauge = gaugeList[offset + i];

237   address gauge = gaugeList[i];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L157


```solidity
File: src/erc-20/ERC20Gauges.sol
260  address gauge = gaugeList[i];

261  uint112 weight = weights[i];

339  address gauge = gaugeList[i];

340  uint112 weight = weights[i];

537  address gauge = gaugeList[i];

538  uint112 userGaugeWeight = getUserGaugeWeight[user][gauge];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L260

```solidity
File:  src/erc-20/ERC20MultiVotes.sol
329   address delegatee = delegateList[i];

330   uint256 delegateVotes = _delegatesVotesCount[user][delegatee];
           
332   uint256 votesToFree = FixedPointMathLib.min(delegateVotes, userUnusedVotes(delegatee));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L329

```solidity
File:  src/erc-4626/ERC4626MultiToken.sol
202   uint256 share = assetsAmounts[i].mulDiv(_totalWeights, weights[i]);

251   uint256 share = assetsAmounts[i].mulDivUp(_totalWeights, weights[i]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L202

```solidity
File:  src/ulysses-amm/UlyssesPool.sol
131   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

176   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

190   uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

212   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

233   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

255   uint256 oldBandwidth = bandwidthStateList[i].bandwidth;

297   uint256 targetBandwidth = totalSupply.mulDiv(bandwidthStateList[i].weight, totalWeights);

908   uint256 updateAmount = bandwidthUpdateAmounts[i];

963   uint256 updateAmount = bandwidthUpdateAmounts[i];

145   uint256 updateAmount = bandwidthUpdateAmounts[i];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L131

```solidity
File:  src/ulysses-amm/UlyssesToken.sol
112   uint256 assetBalance = assets[i].balanceOf(address(this));

113   uint256 newAssetBalance = totalSupply.mulDivUp(weights[i], totalWeights);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L112

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol
146    address destination = address(pools[poolIds[i]]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L146

## [G-06] Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

```solidity
File:   src/governance/GovernorBravoInterfaces.sol
105    struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
        uint256 eta;
        /// @notice the ordered list of target addresses for calls to be made
        address[] targets;
        /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
        uint256[] values;
        /// @notice The ordered list of function signatures to be called
        string[] signatures;
        /// @notice The ordered list of calldata to be passed to each call
        bytes[] calldatas;
        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
        uint256 startBlock;
        /// @notice The block at which voting ends: votes must be cast prior to this block
        uint256 endBlock;
        /// @notice Current number of votes in favor of this proposal
        uint256 forVotes;
        /// @notice Current number of votes in opposition to this proposal
        uint256 againstVotes;
        /// @notice Current number of votes for abstaining for this proposal
        uint256 abstainVotes;
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L105-L136


```solidity
File: /src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
26    struct DepositInput {
    //Deposit Info
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}


44   struct DepositParams {
    //Deposit Info
    uint32 depositNonce; //Deposit nonce.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
    uint128 depositedGas; //BRanch chain gas token amount sent with request.
}
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26



```solidity
File: /src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
31    struct Settlement {
    uint24 toChain; //Destination chain for interaction.
    uint128 gasToBridgeOut; //Gas owed to user
    address owner; //Owner of the settlement
    address recipient; //Recipient of the settlement.
    SettlementStatus status; //Status of the settlement
    address[] hTokens; //Input Local hTokens Addresses.
    address[] tokens; //Input Native / underlying Token Addresses.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    bytes callData; //Call data for settlement
}
63    struct DepositParams {
    //Deposit Info
    uint32 depositNonce; //Deposit nonce.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
73  struct DepositMultipleParams {
    //Deposit Info
    uint8 numberOfAssets; //Number of assets to deposit.
    uint32 depositNonce; //Deposit nonce.
    address[] hTokens; //Input Local hTokens Address.
    address[] tokens; //Input Native / underlying Token Address.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    uint24 toChain; //Destination chain for interaction.
}
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L31


## [G-07] Make 3 event parameters indexed when possible
It’s the most gas efficient to make up to 3 event parameters indexed. If there are less than 3 parameters, you need to make all parameters indexed.

[Reference](https://code4rena.com/reports/2023-01-drips#g-05-make-3-event-parameters-indexed-when-possible)

```solidity
File:  src/erc-20/interfaces/IERC20Boost.sol
222   event UpdateUserBoost(address indexed user, uint256 updatedBoost);

225   event DecrementUserGaugeBoost(address indexed user, address indexed gauge, uint256 UpdatedBoost);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Boost.sol#L222

```solidity
File:  src/erc-20/interfaces/IERC20Gauges.sol
246    event IncrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);

249    event DecrementGaugeWeight(address indexed user, address indexed gauge, uint256 weight, uint32 cycleEnd);

258    event MaxGaugesUpdate(uint256 oldMaxGauges, uint256 newMaxGauges);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20Gauges.sol#L246

```solidity
File: src/erc-20/interfaces/IERC20MultiVotes.sol
147    event MaxDelegatesUpdate(uint256 oldMaxDelegates, uint256 newMaxDelegates);

153    event Delegation(address indexed delegator, address indexed delegate, uint256 amount);

159    event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/interfaces/IERC20MultiVotes.sol#L147

```solidity
File:  src/erc-4626/interfaces/IERC4626.sol
93    event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626.sol#L93

```solidity
File:  src/erc-4626/interfaces/IERC4626DepositOnly.sol
64    event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626DepositOnly.sol#L64

```solidity
File:  src/erc-4626/interfaces/IERC4626MultiToken.sol
159    event Deposit(address indexed caller, address indexed owner, uint256[] assets, uint256 shares);

178    event AssetAdded(address asset, uint256 weight);

184    event AssetRemoved(address asset);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IERC4626MultiToken.sol#L159

```solidity
File:  src/erc-4626/interfaces/IUlyssesERC4626.sol
86   event Deposit(address indexed caller, address indexed owner, uint256 assets, uint256 shares);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/interfaces/IUlyssesERC4626.sol#L86

```solidity
File:  src/gauges/interfaces/IBaseV2GaugeManager.sol
111    event AddedGaugeFactory(address gaugeFactory);

114    event RemovedGaugeFactory(address gaugeFactory);

117    event ChangedbHermesGaugeOwner(address newOwner);

120    event ChangedAdmin(address newAdmin);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/interfaces/IBaseV2GaugeManager.sol#L111

```solidity
File: src/governance/GovernorBravoInterfaces.sol
6     event ProposalCreated(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        string description
    );
24    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

27    event ProposalCanceled(uint256 id);

30    event ProposalQueued(uint256 id, uint256 eta);

33    event ProposalExecuted(uint256 id);

36    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);

39    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);

42    event NewImplementation(address oldImplementation, address newImplementation);

45    event ProposalThresholdSet(uint256 oldProposalThreshold, uint256 newProposalThreshold);

48    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);

51    event NewAdmin(address oldAdmin, address newAdmin);

54    event WhitelistAccountExpirationSet(address account, uint256 expiration);

57    event WhitelistGuardianSet(address oldGuardian, address newGuardian);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L6

```solidity
File:  src/hermes/interfaces/IBaseV2Minter.sol
109    event Mint(address indexed sender, uint256 weekly, uint256 circulatingSupply, uint256 growth, uint256 dao_share);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IBaseV2Minter.sol#L109

```solidity
File:  src/hermes/interfaces/IUtilityManager.sol
82    event ForfeitWeight(address indexed user, uint256 amount);

85    event ForfeitBoost(address indexed user, uint256 amount);

88    event ForfeitGovernance(address indexed user, uint256 amount);

91    event ClaimWeight(address indexed user, uint256 amount);

94    event ClaimBoost(address indexed user, uint256 amount);

97    event ClaimGovernance(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/interfaces/IUtilityManager.sol#L82

```solidity
File:  src/maia/interfaces/IERC4626PartnerManager.sol
78    event AccrueRewards(address indexed user, uint256 rewardsDelta, uint256 rewardsIndex);

85    event ClaimRewards(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/interfaces/IERC4626PartnerManager.sol#L78

```solidity
File:  src/rewards/interfaces/IFlywheelAcummulatedRewards.sol
42    event NewRewardsCycle(uint32 indexed start, uint256 indexed end, uint256 reward);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelAcummulatedRewards.sol#L42

```solidity
File:  src/rewards/interfaces/IFlywheelCore.sol
121    event AccrueRewards(ERC20 indexed strategy, address indexed user, uint256 rewardsDelta, uint256 rewardsIndex);

128    event ClaimRewards(address indexed user, uint256 amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelCore.sol#L121

```solidity
File:  src/rewards/interfaces/IFlywheelGaugeRewards.sol
93    event CycleStart(uint32 indexed cycleStart, uint256 rewardAmount);

96    event QueueRewards(address indexed gauge, uint32 indexed cycleStart, uint256 rewardAmount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/interfaces/IFlywheelGaugeRewards.sol#L93

```solidity
File:  src/talos/TalosStrategyVanilla.sol
165    event CollectFees(uint256 feesFromPool0, uint256 feesFromPool1, uint256 usersFees0, uint256 usersFees1);

170    event CompoundFees(uint256 amount0, uint256 amount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L165

```solidity
File:  src/talos/interfaces/ITalosBaseStrategy.sol
187    event RewardPaid(address indexed sender, uint256 fees0, uint256 fees1);

235    event Rerange(uint256 indexed tokenId, int24 tickLower, int24 tickUpper, uint256 amount0, uint256 amount1);

240    event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/interfaces/ITalosBaseStrategy.sol#L187

```solidity
File:  src/talos/libraries/PoolActions.sol
23     event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L23

```solidity
File:  src/talos/libraries/PoolVariables.sol
28    event Snapshot(uint256 totalAmount0, uint256 totalAmount1);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L28

```solidity
File:  src/ulysses-amm/interfaces/IUlyssesPool.sol
237    event Swap(address indexed caller, uint256 indexed poolId, uint256 assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/interfaces/IUlyssesPool.sol#L237

```solidity
File:  src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol
281    event LogCallin(bytes1 selector, bytes data, uint256 fromChainId);

282    event LogCallout(bytes1 selector, bytes data, uint256, uint256 toChainId);

293    event LogCalloutFail(bytes1 selector, bytes data, uint256 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L281

```solidity
File:  src/ulysses-omnichain/interfaces/IBranchPort.sol
198    event DebtCreated(address indexed _strategy, address indexed _token, uint256 _amount);

199    event DebtRepaid(address indexed _strategy, address indexed _token, uint256 _amount);

201    event StrategyTokenAdded(address indexed _token, uint256 _minimumReservesRatio);

202    event StrategyTokenToggled(address indexed _token);


204    event PortStrategyAdded(address indexed _portStrategy, address indexed _token, uint256 _dailyManagementLimit);

205    event PortStrategyToggled(address indexed _portStrategy, address indexed _token);

206    event PortStrategyUpdated(address indexed _portStrategy, address indexed _token, uint256 _dailyManagementLimit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IBranchPort.sol#L198

```solidity
File:  src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol
378    event LogCallin(bytes1 selector, bytes data, uint24 fromChainId);

379    event LogCallout(bytes1 selector, bytes data, uint256, uint24 toChainId);

380    event LogCalloutFail(bytes1 selector, bytes data, uint24 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L378

```solidity
File:  src/ulysses-omnichain/interfaces/IRootPort.sol
315    event BridgeAgentAdded(address indexed bridgeAgent, address manager);

322    event VirtualAccountCreated(address indexed user, address account);

324    event LocalTokenAdded(
        address indexed underlyingAddress, address localAddress, address globalAddress, uint24 chainId
    );
327    event GlobalTokenAdded(address indexed localAddress, address indexed globalAddress, uint24 chainId);    
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootPort.sol#L315

```solidity
File:  src/uni-v3-staker/interfaces/IUniswapV3Staker.sol
264    event IncentiveCreated(IUniswapV3Pool indexed pool, uint256 startTime, uint256 reward);

269    event IncentiveEnded(bytes32 indexed incentiveId, uint256 refund);

281    event TokenStaked(uint256 indexed tokenId, bytes32 indexed incentiveId, uint128 liquidity);

291    event RewardClaimed(address indexed to, uint256 reward);

296    event BribeDepotUpdated(IUniswapV3Pool indexed uniswapV3Pool, address bribeDepot);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L264

## [G-08] >= costs less gas than >
The compiler uses opcodes for solidity code that uses >, but only requires for >=, which saves 3 gas

```solidity
File:  src/erc-4626/ERC4626MultiToken.sol
252    if (share > shares) shares = share;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L52

```solidity
File:  src/gauges/factories/BaseV2GaugeFactory.sol
91     if (end > length) end = length;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L252


```solidity
File:     src/talos/boost-aggregator/BoostAggregator.sol
93       if (_daoShare > max_dao_share) revert DaoShareTooHigh();

99       if (_tail_emission > max_tail_emission) revert TailEmissionTooHigh();
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L93


```solidity
File:    src/rewards/rewards/FlywheelGaugeRewards.sol
120     if (currentCycle > nextCycle)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L120


```solidity
File:   /src/talos/base/TalosBaseStrategy.sol
158    if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();

219    if (totalSupply > optimizer.maxTotalSupply()) revert ExceedingMaxTotalSupply();

398    if (amount0 > _protocolFees0)

401    if (amount1 > _protocolFees1)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L158


```solidity
File:   src/talos/factories/TalosBaseStrategyFactory.sol
138     if (balance > assets)

171     if (index > MAX_DESTINATIONS) revert TooManyDestinations();

185     if (newTotalWeights > MAX_TOTAL_WEIGHT) revert InvalidWeight();

243     if (totalWeights > MAX_TOTAL_WEIGHT || oldTotalWeights == newTotalWeights)

252     if (oldTotalWeights > newTotalWeights)

310     if (_fees.lambda1 > MAX_LAMBDA1) revert InvalidFee();

315     if (_fees.sigma1 > DIVISIONER) revert InvalidFee();

327     if (_protocolFee > MAX_PROTOCOL_FEE) revert InvalidFee();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L138


```solidity
File: src/talos/TalosOptimizer.sol
115   if (assetBalance > newAssetBalance)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L47


```solidity
File: /src/ulysses-omnichain/BranchBridgeAgent.sol
1035    if (minExecCost > gasRemaining) 

1050    if (gasLeft - gasAfterTransfer > TRANSFER_OVERHEAD) 

1069    if (minExecCost > getDeposit[_depositNonce].depositedGas)
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1035

## [G-09] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant
The reason for this is that constant variables are evaluated at runtime and their value is included in the bytecode of the contract. This means that any expensive operations performed as part of the constant expression, such as a call to keccak256(), will be executed every time the contract is deployed, even if the result is always the same. This can result in higher gas costs.

In contrast, immutable variables are evaluated at compilation time, and their values are included in the bytecode of the contract as constants. This means that any expensive operations performed as part of the immutable expression are only executed once, when the contract is compiled, and the result is reused every time the contract is deployed. This can result in lower gas costs compared to using constant variables.

Let's consider an example to illustrate this. Suppose we want to store the hash of a string as a constant value in our contract. We could do this using a constant variable, like so:

```
bytes32 constant MY_HASH = keccak256("my string");
```
Alternatively, we could use an immutable variable, like so:
```
bytes32 immutable MY_HASH = keccak256("my string");
```
```solidity
File:   src/erc-20/ERC20MultiVotes.sol
360   bytes32 public constant DELEGATION_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L360

```solidity
File:   src/governance/GovernorBravoDelegateMaia.sol
42    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256("EIP712Domain(string name,uint256 chainId,address verifyingContract)")

46    bytes32 public constant BALLOT_TYPEHASH = keccak256("Ballot(uint256 proposalId,uint8 support)");
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L42

## [G-10] Using private rather than public for constants, saves gas
When you declare a constant variable as public, Solidity generates a getter function that allows anyone to read the value of the constant. This getter function can consume gas, especially if the constant is read frequently or the contract is called by multiple users.

By using private instead of public for constants, you can prevent the generation of the getter function and reduce the overall gas cost of your contract.


```solidity  
File:  src/governance/GovernorBravoDelegateMaia.sol 
9    string public constant name = "vMaia Governor Bravo";

12   uint256 public constant MIN_PROPOSAL_THRESHOLD = 0.005 ether; // 0.5% of GovToken

15   uint256 public constant MAX_PROPOSAL_THRESHOLD = 0.05 ether; // 5% of GovToken

18   uint256 public constant MIN_VOTING_PERIOD = 80640; // About 2 weeks

21   uint256 public constant MAX_VOTING_PERIOD = 161280; // About 4 weeks

24   uint256 public constant MIN_VOTING_DELAY = 40320; // About 1 weeks

27   uint256 public constant MAX_VOTING_DELAY = 80640; // About 2 weeks

30   uint256 public constant quorumVotes = 0.35 ether; // 35% of GovToken

33   uint256 public constant proposalMaxOperations = 10; // 10 actions

36   uint256 public constant DIVISIONER = 1 ether;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L9


## [G-11] Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
File:   src/hermes/minters/BaseV2Minter.sol
24      uint256 internal constant week = 86400 * 7;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L24

```solidity
File:   src/talos/TalosStrategyVanilla.sol  
47      uint24 private constant protocolFee = 2 * 1e5; //20%
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L47

## [G-12] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.
```solidity
File:  src/governance/GovernorBravoDelegateMaia.sol
234    if (msg.sender != proposal.proposer && msg.sender != admin) {

236    if (isWhitelisted(proposal.proposer)) {

238    (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount())

244     (govToken.getPriorVotes(proposal.proposer, sub256(block.number, 1)) < getProposalThresholdAmount()),
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L234-L244


```solidity
File:   src/ulysses-omnichain/ArbitrumBranchPort.sol
62    if (!IRootPort(rootPortAddress).isGlobalToken(_globalAddress, localChainId)) {

66    address underlyingAddress = IRootPort(rootPortAddress).getUnderlyingTokenFromLocal(_globalAddress, localChainId);

70    IRootPort(rootPortAddress).burnFromLocalBranch(_depositor, _globalAddress, _deposit);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L62-L70

```solidity
File:    src/ulysses-omnichain/CoreBranchRouter.sol
        if (!IPort(localPortAddress).isBridgeAgentFactory(_newBridgeAgentFactoryAddress)) {
            IPort(localPortAddress).addBridgeAgentFactory(_newBridgeAgentFactoryAddress);
        } else {
            IPort(localPortAddress).toggleBridgeAgentFactory(_newBridgeAgentFactoryAddress);
        }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L164-L168

## [G‑13] Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }.

```solidity
File:   src/governance/GovernorBravoDelegateMaia.sol
532    require(b <= a, "subtraction underflow");
        return a - b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L532-L533

```solidity
File:   src/hermes/minters/BaseV2Minter.sol
140   HERMES(underlying).mint(address(this), _required - _balanceOf);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L140

```solidity
File:   src/talos/base/TalosBaseStrategy.sol
166   if (amount0 < amount0Desired) {
            uint256 refund0 = amount0Desired - amount0;
            address(_token0).safeTransfer(msg.sender, refund0);
        }

171    if (amount1 < amount1Desired) {
            uint256 refund1 = amount1Desired - amount1;
            address(_token1).safeTransfer(msg.sender, refund1);
        }
226    if (amount0 < amount0Desired) {
            uint256 refund0 = amount0Desired - amount0;
            address(_token0).safeTransfer(msg.sender, refund0);
        }

231   if (amount1 < amount1Desired) {
            uint256 refund1 = amount1Desired - amount1;
            address(_token1).safeTransfer(msg.sender, refund1);
        }  
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L166-L174



## [G-14] abi.encode() is less efficient than abi.encodePacked()
In terms of efficiency, abi.encodePacked() is generally considered to be more gas-efficient than abi.encode(), because it skips the step of adding function signatures and other metadata to the encoded data. However, this comes at the cost of reduced safety, as abi.encodePacked() does not perform any type checking or padding of data.

```solidity
File:   src/governance/GovernorBravoDelegateMaia.sol
346   keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
File:  src/talos/libraries/PoolActions.sol
51   abi.encode(SwapCallbackData({zeroForOne: zeroForOne}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L51

```solidity
File:   src/ulysses-omnichain/RootBridgeAgent.sol
689   abi.encode(SwapCallbackData({tokenIn: gasTokenGlobalAddress}))

733   abi.encode(SwapCallbackData({tokenIn: address(wrappedNativeToken)}))
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L689

## [G-15] Use constants instead of type(uintx).max
 it's generally more gas-efficient to use constants instead of type(uintX).max when you need to set the maximum value of an unsigned integer type.

The reason for this is that the type(uintX).max expression involves a computation at runtime, whereas a constant is evaluated at compile-time. This means that using type(uintX).max can result in additional gas costs for each transaction that involves the expression.

By using a constant instead of type(uintX).max, you can avoid these additional gas costs and make your code more efficient.

Here's an example of how you can use a constant instead of type(uintX).max:
```
contract MyContract {
    uint120 constant MAX_VALUE = 2**120 - 1;
    
    function doSomething(uint120 value) public {
        require(value <= MAX_VALUE, "Value exceeds maximum");
        
        // Do something
    }
}
```
In the above example, we have a contract with a constant MAX_VALUE that represents the maximum value of a uint120. When the doSomething function is called with a value parameter, it checks whether the value is less than or equal to MAX_VALUE using the <= operator.

By using a constant instead of type(uint120).max, we can make our code more efficient and reduce the gas cost of our contract.

It's important to note that using constants can make your code more readable and maintainable, since the value is defined in one place and can be easily updated if necessary. However, constants should be used with caution and only when their value is known at compile-time.

Here's an example to illustrate this:

```solidity
contract ExampleContract {
    uint256 constant MAX_UINT256 = 2**256 - 1;

    function doSomething() external {
        uint256 maxValue = type(uint256).max;

        // Perform some operations

        if (value > maxValue) {
            // Do something
        }
    }
}

```
In this example, we have defined a constant MAX_UINT256 with the maximum value of a uint256 variable, which is equivalent to 2^256 - 1. Instead of using type(uint256).max directly, we use the constant MAX_UINT256 throughout the contract.


```solidity
File:   src/erc-4626/ERC4626.sol
67      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

84      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

149     return type(uint256).max;

154     return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L67


```solidity
File:  src/erc-4626/ERC4626DepositOnly.sol
99   return type(uint256).max;

104  return type(uint256).max;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L99

```solidity
File:    src/erc-4626/ERC4626MultiToken.sol
143      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

165      if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

200      shares = type(uint256).max;

270      return type(uint256).max;

275      return type(uint256).max;
```

https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L143

```solidity
File:    src/erc-4626/UlyssesERC4626.sol
68       if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;

113      return type(uint256).max;

117      return type(uint256).max;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L68



```solidity
File:   src/gauges/UniswapV3Gauge.sol
45      rewardToken.safeApprove(_uniswapV3Staker, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L45 

```solidity
File:   src/maia/tokens/ERC4626PartnerManager.sol
200     address(gaugeWeight).safeApprove(newPartnerVault, type(uint256).max);

201        address(gaugeBoost).safeApprove(newPartnerVault, type(uint256).max);

202       address(governance).safeApprove(newPartnerVault, type(uint256).max);

203     address(partnerGovernance).safeApprove(newPartnerVault, type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L200
```solidity
File:  src/rewards/base/BaseFlywheelRewards.sol
36     _rewardToken.safeApprove(address(_flywheel), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L36

```solidity
File:   src/rewards/rewards/FlywheelGaugeRewards.sol
133     require(newRewards <= type(uint112).max); 

187     require(nextRewards <= type(uint112).max); 
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L133


```solidity
File:      src/talos/base/TalosBaseStrategy.sol
130        address(_token0).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

131        address(_token1).safeApprove(address(_nonfungiblePositionManager), type(uint256).max);

249        if (allowed != type(uint256).max) allowance[_owner][msg.sender] = allowed - shares

285        amount0Max: type(uint128).max,

286        amount1Max: type(uint128).max

367        amount0Max: type(uint128).max,

368        amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L130



```solidity
File:    src/talos/TalosStrategyStaked.sol
151      amount0Max: type(uint128).max,

152      amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L151

```solidity
File:  src/talos/TalosStrategyVanilla.sol
111          amount0Max: type(uint128).max,

112          amount1Max: type(uint128).max
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/talos/TalosStrategyVanilla.sol#L111


```solidity
File:   src/ulysses-amm/UlyssesRouter.sol
40      address(ulysses.asset()).safeApprove(address(ulysses), type(uint256).max);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L40 

```solidity
File:  src/uni-v3-staker/UniswapV3Staker.sol
70     if (liquidity == type(uint96).max)

385    amount0Max: type(uint128).max,

386    amount1Max: type(uint128).max

456    if (liquidity >= type(uint96).max) stake.liquidityIfOverflow = 0;

506    if (liquidity >= type(uint96).max)

509    liquidityNoOverflow: type(uint96).max,
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L70


## [G-16] Use hardcode address instead address(this)
it can be more gas-efficient to use a hardcoded address instead of the address(this) expression, especially if you need to use the same address multiple times in your contract.

The reason for this is that using address(this) requires an additional EXTCODESIZE operation to retrieve the contract's address from its bytecode, which can increase the gas cost of your contract. By pre-calculating and using a hardcoded address, you can avoid this additional operation and reduce the overall gas cost of your contract.

Here's an example of how you can use a hardcoded address instead of address(this):

```
contract MyContract {
    address public myAddress = 0x1234567890123456789012345678901234567890;
    
    function doSomething() public {
        // Use myAddress instead of address(this)
        require(msg.sender == myAddress, "Caller is not authorized");
        
        // Do something
    }
}
```
In the above example, we have a contract MyContract with a public address variable myAddress. Instead of using address(this) to retrieve the contract's address, we have pre-calculated and hardcoded the address in the variable. This can help to reduce the gas cost of our contract and make our code more efficient.

[References](https://book.getfoundry.sh/reference/forge-std/compute-create-address)

```solidity
File:   src/maia/tokens/ERC4626PartnerManager.sol
162      return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

168      return (address(bHermesToken).balanceOf(address(this))) / bHermesRate - totalSupply;

219      if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) 

226      address(this), totalSupply * newRate - address(partnerGovernance).balanceOf(address(this))

244      ERC20MultiVotes(partnerGovernance).mint(address(this), amount * bHermesRate);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L162

```solidity
File:  src/maia/PartnerUtilityManager.sol
75     if (partnerVault != address(0) && address(gaugeWeight).balanceOf(address(this)) > 0)

85     if (partnerVault != address(0) && address(gaugeBoost).balanceOf(address(this)) > 0) 

95     if (partnerVault != address(0) && address(governance).balanceOf(address(this)) > 0) 

104    address(partnerGovernance).safeTransferFrom(msg.sender, address(this), amount);

128    uint256 weightAvailable = address(gaugeWeight).balanceOf(address(this));

139    uint256 boostAvailable = address(gaugeBoost).balanceOf(address(this));

148    uint256 governanceAvailable = address(governance).balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L75

```solidity
File:   src/talos/base/TalosBaseStrategy.sol
125     address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);

126     address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);

146     recipient: address(this),

196     address(_token0).safeTransferFrom(msg.sender, address(this), amount0Desired);

197     address(_token1).safeTransferFrom(msg.sender, address(this), amount1Desired);

366     recipient: address(this),

406     uint256 balance0 = _token0.balanceOf(address(this));

407     uint256 balance1 = _token1.balanceOf(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L125


```solidity
File:   src/talos/TalosStrategyStaked.sol
90      flywheel.accrue(ERC20(address(this)), msg.sender, _to);

95      flywheel.accrue(ERC20(address(this)), _from, _to);

150     recipient: address(this),

177     try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L90


```solidity
File:   src/ulysses-amm/UlyssesPool.sol
103     return asset.balanceOf(address(this)) - getProtocolFees();

108     return balanceOf[owner].min(asset.balanceOf(address(this)));

127     uint256 balance = asset.balanceOf(address(this));

218     asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

303     asset.safeTransferFrom(msg.sender, address(this), newRebalancingFee - oldRebalancingFee);

1109    asset.safeTransferFrom(msg.sender, address(this), assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L103



```solidity
File:     src/ulysses-omnichain/BranchBridgeAgent.sol
1020      uint256 gasRemaining = wrappedNativeToken.balanceOf(address(this));

1078     IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), minExecCost);

1093      IAnycallConfig(IAnycallProxy(localAnyCallAddress).config()).deposit{value: _executionGasSpent}(address(this));

1103      IPort(localPortAddress).withdraw(address(this), address(wrappedNativeToken), gasAmount);

1325     uint256 executionBudget = anycallConfig.executionBudget(address(this));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1020


```solidity
File:    src/ulysses-omnichain/BranchPort.sol
127     uint256 currBalance = ERC20(_token).balanceOf(address(this));

138     uint256 currBalance = ERC20(_token).balanceOf(address(this));

180     IPortStrategy(_strategy).withdraw(address(this), _token, amountToWithdraw);

249     _localAddress.safeTransferFrom(_depositor, address(this), _amount - _deposit);

254     _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())

271      address(this),

276      _localAddresses[i].safeTransferFrom(_depositor, address(this), _amounts[i] - _deposits[i]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L127



## [G-17] A modifier used only once and not being inherited should be inlined to save gas
When a modifier is used only once and is not inherited by any other contracts, inlining it can reduce gas costs. Inlining means that the modifier's code is directly inserted into the function where it is applied, rather than creating a separate function call for the modifier.
By inlining the modifier, you avoid the overhead of the additional function call, which results in lower gas consumption. This optimization is especially useful when the modifier's code is short or simple.

```solidity
File: /src/talos/boost-aggregator/BoostAggregator.sol
190      modifier onlyWhitelisted(address from) {
        if (!whitelistedAddresses[from]) revert Unauthorized();
        _;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L190


```solidity
File: /src/ulysses-omnichain/BranchPort.sol
423      modifier lock() {
        require(_unlocked == 1);
        _unlocked = 2;
        _;
        _unlocked = 1;
    }

```

https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L423

```solidity
File: /src/gauges/factories/BribesFactory.sol 
105         modifier onlyGaugeFactory() {
        if (!gaugeManager.activeGaugeFactories(BaseV2GaugeFactory(msg.sender))) {
            revert Unauthorized();
        }
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L105

```solidity
File: /src/hermes/tokens/bHermesBoost.sol
32      modifier onlybHermes() {
        if (msg.sender != bHermes) revert NotbHermes();
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L32

```solidity
File: /src/hermes/tokens/bHermesGauges.sol
39       modifier onlybHermes() {
        if (msg.sender != bHermes) revert NotbHermes();
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L39

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol
75     modifier requiresCoreRouter() {
        if (msg.sender != localCoreRouterAddress) revert UnrecognizedCoreRouter();
        _;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L75

```solidity
File:   src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol
74      modifier requiresCoreRouter() {
        if (msg.sender != coreRootRouterAddress && msg.sender != rootPortAddress) {
            revert UnrecognizedCoreRouter();
        }
        _;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L74

## [G-18]  Using delete statement can save gas
```solidity
File:  src/erc-20/ERC20Boost.sol
249    getUserBoost[msg.sender] = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L249 

```solidity
File:     src/erc-20/ERC20MultiVotes.sol
340      _delegatesVotesCount[user][delegatee] = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L340


```solidity
File:    src/rewards/base/FlywheelCore.sol
98       rewardsAccrued[user] = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L98


```solidity
File:   src/talos/libraries/PoolVariables.sol
211     secondsAgo[1] = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L211

```solidity
File:    src/ulysses-amm/UlyssesToken.sol
78       assetId[asset] = 0;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L78

## [G-19] Amounts should be checked for 0 before calling a transfer
It is generally a good practice to check for zero values before making any transfers in smart contract functions. This can help to avoid unnecessary external calls and can save gas costs.

Checking for zero values is especially important when transferring tokens or ether, as sending these assets to an address with a zero value will result in the loss of those assets.

In Solidity, you can check whether a value is zero by using the == operator. Here's an example of how you can check for a zero value before making a transfer:

```
function transfer(address payable recipient, uint256 amount) public {
    require(amount > 0, "Amount must be greater than zero");
    recipient.transfer(amount);
}
```
In the above example, we check to make sure that the amount parameter is greater than zero before making the transfer to the recipient address. If the amount is zero or negative, the function will revert and the transfer will not be made.

```solidity
File:  src/erc-4626/ERC4626.sol
76    address(asset).safeTransfer(receiver, assets);

96    address(asset).safeTransfer(receiver, assets);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L76


```solidity
File: src/erc-4626/ERC4626MultiToken.sol
80    assets[i].safeTransfer(receiver, assetsAmounts[i]);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L80

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol
176   address(hermesGaugeBoost).safeTransfer(to, amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L176

```solidity
File: src/ulysses-omnichain/BranchPort.sol
166   _token.safeTransfer(msg.sender, _amount);
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L166


## [G-20] Use assembly to hash instead of solidity
```
function solidityHash(uint256 a, uint256 b) public view {
    //unoptimized
    keccak256(abi.encodePacked(a, b));
}
```
Gas: 313
```
function assemblyHash(uint256 a, uint256 b) public view {
    //optimized
    assembly {
        mstore(0x00, a)
        mstore(0x20, b)
        let hashedVal := keccak256(0x00, 0x40)
    }
} 
```
Gas: 231

```solidity
File: src/erc-20/ERC20MultiVotes.sol
366  keccak256(
                abi.encodePacked(
                    "\x19\x01", DOMAIN_SEPARATOR(), keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry))
                )
            ),
```
https://github.com/code-423n4/2023-05-maia/tree/main/src/erc-20/ERC20MultiVotes.sol#L366-L370


```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
198   !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

346     keccak256(abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this)));

347    bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

348    bytes32 digest = keccak256(abi.encodePacked("\x19\x01", domainSeparator, structHash));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L346

```solidity
File:  src/uni-v3-staker/libraries/IncentiveId.sol
17    return keccak256(abi.encode(key));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol#L17

## [G-21] Loop best practice to save gas
```
function Plusi() public view {
		for(uint i=0; i<10;) {
			console.log("Number ==",i);
			unchecked{
				++i;
			}
		}
	}

best practice
-----------------------------------------------------
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

```solidity
File: src/erc-4626/ERC4626MultiToken.sol
59  unchecked {
      i++;
    }
71  unchecked {
      i++;
    }

82  unchecked {
      i++;
    }

173 unchecked {
      i++;
    }

204 unchecked {
      i++;
    }

218 unchecked {
      i++;
    }

237 unchecked {
      i++;
    }

253 unchecked {
      i++;
    }

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L59

```solidity
File: src/ulysses-amm/UlyssesPool.sol
197 unchecked {
      i++;
    }

264 unchecked {
      i++;
    }

288 unchecked {
      i++;
    }

930 unchecked {
      i++;
    }

1001 unchecked {
      i++;
    }

1068 unchecked {
      i++;
    }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L197

## [G-22]Gas savings can be achieved by changing the model for assigning value to the structure
Here's an example to illustrate this:
```
struct MyStruct {
    uint256 a;
    uint256 b;
    uint256 c;
}

function assignValuesToStruct(uint256 _a, uint256 _b, uint256 _c) public {
    MyStruct memory myStruct = MyStruct(_a, _b, _c);
    // Do something with myStruct
}
```
In this example, we have a simple MyStruct data structure with three uint256 fields. The assignValuesToStruct function takes three input parameters _a, _b, and _c, and assigns them to the corresponding fields in a new myStruct variable. This is done using the struct constructor syntax, which creates a new instance of the MyStruct struct with the specified field values.

This approach can be more efficient than assigning values to the struct fields one by one, like this:
```
function assignValuesToStruct(uint256 _a, uint256 _b, uint256 _c) public {
    MyStruct memory myStruct;
    myStruct.a = _a;
    myStruct.b = _b;
    myStruct.c = _c;
    // Do something with myStruct
}
```
In this example, the values of _a, _b, and _c are assigned to the corresponding fields of the myStruct variable one by one. This can be less efficient than using the struct constructor syntax, because it requires more memory operations to initialize the struct fields.

By using the struct constructor syntax to assign values to the struct fields, we can save gas by reducing the number of memory operations required to create the struct.

[Reference](https://code4rena.com/reports/2023-01-timeswap#g-06-gas-savings-can-be-achieved-by-changing-the-model-for-assigning-value-to-the-structure-260-gas)

```solidity
File: src/erc-20/ERC20Boost.sol
131    getUserGaugeBoost[user][msg.sender] =
            GaugeState({userGaugeBoost: userGaugeBoost, totalGaugeBoost: totalSupply.toUint128()});
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L131

```solidity
File: src/erc-20/ERC20MultiVotes.sol
253   ckpts.push(Checkpoint({fromBlock: block.number.toUint32(), votes: newWeight.toUint224()}));
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L253

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol
189   gaugeQueuedRewards[gauge] = QueuedRewards({
                priorCycleRewards: queuedRewards.priorCycleRewards + completedRewards,
                cycleRewards: uint112(nextRewards),
                storedCycle: currentCycle
            });

228    gaugeQueuedRewards[ERC20(msg.sender)] = QueuedRewards({
            priorCycleRewards: 0,
            cycleRewards: cycleRewardsNext,
            storedCycle: queuedRewards.storedCycle
        });        
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L189

```solidity
File: src/talos/TalosStrategyStaked.sol
148   INonfungiblePositionManager.CollectParams({
                    tokenId: _tokenId,
                    recipient: address(this),
                    amount0Max: type(uint128).max,
                    amount1Max: type(uint128).max
                })
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L148

```solidity
File: src/talos/TalosStrategyVanilla.sol
108   INonfungiblePositionManager.CollectParams({
                tokenId: _tokenId,
                recipient: address(this),
                amount0Max: type(uint128).max,
                amount1Max: type(uint128).max
            })

142    INonfungiblePositionManager.IncreaseLiquidityParams({
                    tokenId: _tokenId,
                    amount0Desired: balance0,
                    amount1Desired: balance1,
                    amount0Min: 0,
                    amount1Min: 0,
                    deadline: block.timestamp
                })

```
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L108

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol
416   dParams = DepositMultipleParams({
            numberOfAssets: numOfAssets,
            depositNonce: nonce,
            hTokens: hTokens,
            tokens: tokens,
            amounts: amounts,
            deposits: deposits,
            toChain: toChain
        });
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L416


## [G-23] Use assembly for math (add, sub, mul, div)
Use assembly for math instead of Solidity. You can check for overflow/underflow in assembly to ensure safety. If using Solidity versions < 0.8.0 and you are using Safemath, you can gain significant gas savings by using assembly to calculate values and checking for overflow/underflow.

Addition:
```
//addition in Solidity
function addTest(uint256 a, uint256 b) public pure {
    uint256 c = a + b;
}
```
Gas: 303
```
//addition in assembly
function addAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := add(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263

Subtraction
```
//subtraction in Solidity
function subTest(uint256 a, uint256 b) public pure {
  uint256 c = a - b;
}
```
Gas: 300
```
//subtraction in assembly
function subAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := sub(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 263

Multiplication
```
//multiplication in Solidity
function mulTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325
```
//multiplication in assembly
function mulAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := mul(a, b)
        if lt(c, a) {
            mstore(0x00, "overflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265

Division
```
//division in Solidity
function divTest(uint256 a, uint256 b) public pure {
    uint256 c = a * b;
}
```
Gas: 325
```
//division in assembly
function divAssemblyTest(uint256 a, uint256 b) public pure {
    assembly {
        let c := div(a, b)
        if gt(c, a) {
            mstore(0x00, "underflow")
            revert(0x00, 0x20)
        }
    }
}
```
Gas: 265

```solidity
File: src/erc-20/ERC20Gauges.sol
379    function _add112(uint112 a, uint112 b) private pure returns (uint112) {
        return a + b;
       }
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L379

```solidity
File: src/erc-20/ERC20MultiVotes.sol
73   low = mid + 1;

82   return (a & b) + (a ^ b) / 2;

259  return a + b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L73

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol
526   uint256 c = a + b;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L526

```solidity
File: src/hermes/minters/BaseV2Minter.sol
134   uint256 _required = _growth + newWeeklyEmission;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L134

## [G-24] Access mappings directly rather than using accessor functions
Saves having to do two JUMP instructions, along with stack setup

```solidity
File: src/erc-20/ERC20Boost.sol
87   return _userGauges[user].values();

108  return _userGauges[user].length();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L87


```solidity
File: src/erc-20/ERC20Gauges.sol
144   return _userGauges[user].values();

165   return _userGauges[user].length();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L144


```solidity
File: src/erc-20/ERC20MultiVotes.sol
132   return _delegates[delegator].values();

137   return _delegates[delegator].length();
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L132

```solidity
File: src/erc-4626/ERC4626.sol
164   return balanceOf[owner];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L164

```solidity
File: src/erc-4626/ERC4626MultiToken.sol
285   return balanceOf[owner];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L285

```solidity
File: src/erc-4626/UlyssesERC4626.sol
121   return balanceOf[owner];
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L121

## [G-25] internal functions not called by the contract should be removed to save deployment gas
Internal functions in Solidity are only intended to be invoked within the contract or by other internal functions. If an internal function is not called anywhere within the contract, it serves no purpose and contributes unnecessary overhead during deployment. Removing such functions can lead to substantial gas savings.

```solidity
19     function transferRewards(address _asset, address _rewardsContract) internal returns (uint256 balance)
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L19

## [G-26]use Mappings Instead of Arrays
Arrays are useful when you need to maintain an ordered list of data that can be iterated over, but they have a higher gas cost for read and write operations, especially when the size of the array is large. This is because Solidity needs to iterate over the entire array to perform certain operations, such as finding a specific element or deleting an element.
Mappings, on the other hand, are useful when you need to store and access data based on a key, rather than an index. Mappings have a lower gas cost for read and write operations, especially when the size of the mapping is large, since Solidity can perform these operations based on the key directly, without needing to iterate over the entire data structure.

```solidity
File: src/erc-4626/ERC4626MultiToken.sol
23   address[] public assets;

26   uint256[] public weights;
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L23

## [G-27] Use Short-Circuiting rules to your advantage
When using logical disjunction (||), logical conjunction (&&), make sure to order your functions correctly for optimal gas usage. In logical disjunction (OR), if the first function resolves to true, the second one won’t be executed and hence save you gas. In logical disjunction (AND), if the first function evaluates to false, the next function won’t be evaluated. Therefore, you should order your functions accordingly in your solidity code to reduce the probability of needing to evaluate the second function.

```solidity
File: src/erc-20/ERC20Boost.sol
117   if (!_gauges.contains(msg.sender) || _deprecatedGauges.contains(msg.sender)) {

212   if (_deprecatedGauges.contains(gauge) || boost >= gaugeState.userGaugeBoost) {

267   if (gauge == address(0) || !(newAdd || previouslyDeprecated)) revert InvalidGauge();

and so .....
```
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L117

## [G-28] Use ERC721A instead ERC721
ERC721A standard, ERC721A is an improvement standard for ERC721 tokens. It was proposed by the Azuki team and used for developing their NFT collection. Compared with ERC721, ERC721A is a more gas-efficient standard to mint a lot of of NFTs simultaneously. It allows developers to mint multiple NFTs at the same gas price. This has been a great improvement due to Ethereumâ€™s sky-rocketing gas fee.

[Reference](https://nextrope.com/erc721-vs-erc721a-2/)