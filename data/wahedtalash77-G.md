# Gas Optimization

## [G-1]  we shold choose less range instead of `uint256` like `uint8` so the best way is to put them in struct. It should be noted that in a struct, uint8 DOES cost less than a traditional uint, because of the tight packing feature. Also be sure that your uints are next to your other uints.
24: `uint256 internal constant week = 86400 * 7;`
26: ` uint256 internal constant base = 1000;`
28:  `uint256 internal constant max_tail_emission = 100;`
29: `uint256 internal constant max_dao_share = 300;`
42: `uint256 public override daoShare = 100;`
43: `uint256 public override tailEmission = 20;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol

143:  `uint256 internal _unlocked = 1;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L143

49: `uint256 public poolId = 1;`
52: `uint256 public tokenId = 1;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L49-L52

## [G-02] For uint use != 0 instead of > 0 .
139: `if (_liquidity > 0) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L139

118: `     if (_deposit > 0) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L118

160: `if (gasRemaining > 0) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L160

234: `if (accruedRewards > 0) rewardToken.safeTransfer(msg.sender, accruedRewards);`
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L234

## [G-3] you can save storage by reordering Holding struct fields in the following way.

```
struct DepositInput {
    //Deposit Info
    uint24 toChain; //Destination chain for interaction.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L26C1-L33C2

```
struct Deposit {
    uint128 depositedGas;
    uint256[] deposits;
    uint256[] amounts;
    address owner;
    address[] hTokens;
    address[] tokens;
    DepositStatus status;
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L16C17-L24

```
struct DepositMultipleInput {
    //Deposit Info
    uint24 toChain; //Destination chain for interaction.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    address[] hTokens; //Input Local hTokens Address.
    address[] tokens; //Input Native / underlying Token Address.
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L35C1-L42C2

```
struct DepositParams {
    //Deposit Info
    uint24 toChain; //Destination chain for interaction.
    uint32 depositNonce; //Deposit nonce.
    uint128 depositedGas; //BRanch chain gas token amount sent with request.
    uint256 amount; //Amount of Local hTokens deposited for interaction.
    uint256 deposit; //Amount of native tokens deposited for interaction.
    address hToken; //Input Local hTokens Address.
    address token; //Input Native / underlying Token Address.
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L44C1-L53C2

```
struct DepositMultipleParams {
    //Deposit Info
    uint8 numberOfAssets; //Number of assets to deposit.
    uint24 toChain; //Destination chain for interaction.
    uint32 depositNonce; //Deposit nonce.
    uint128 depositedGas; //BRanch chain gas token amount sent with request.
    uint256[] amounts; //Amount of Local hTokens deposited for interaction.
    uint256[] deposits; //Amount of native tokens deposited for interaction.
    address[] hTokens; //Input Local hTokens Address.
    address[] tokens; //Input Native / underlying Token Address.
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L55C1-L65C2

```
struct SettlementParams {
    uint32 settlementNonce;
    uint256 amount;
    uint256 deposit;
    address recipient;
    address hToken;
    address token;
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L67C1-L74C2

```
struct SettlementMultipleParams {
    uint8 numberOfAssets; //Number of assets to deposit.
    uint32 settlementNonce;
    uint256[] amounts;
    uint256[] deposits;
    address recipient;
    address[] hTokens;
    address[] tokens;
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchBridgeAgent.sol#L76C1-L84C2
```
struct GasPoolInfo {
    //zeroForOne when swapping gas from branch chain into root chain gas
    uint24 priceImpactPercentage; //Price impact percentage
    address poolAddress; //Uniswap V3 Pool Address
    bool zeroForOneOnInflow;
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L19C1-L24C2

==All of the follwing `struct` should be fixed.==
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L31-L82
==And==
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/uni-v3-staker/interfaces/IUniswapV3Staker.sol#L61C4-L80
```
struct GasPoolInfo {
    uint24 priceImpactPercentage;
    address gasTokenGlobalAddress;
    address poolAddress;
    bool zeroForOneOnInflow;
}
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootPort.sol#L14-L19


## [G-04] Use calldata instead of memory for function arguments that do not get mutated

```   
function callOutAndBridgeMultiple(
        address _owner,
        address _recipient,
        bytes memory _data,
        address[] memory _globalAddresses,
        uint256[] memory _amounts,
        uint256[] memory _deposits,
        uint24 _toChain
    ) external payable;
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L248C1-L256C24

268: `function bridgeIn(address _recipient, DepositParams memory _dParams, uint24 _fromChain) external;`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L268

```
function addNewChain(
        address _pledger,
        uint256 _pledgedInitialAmount,
        address _coreBranchBridgeAgentAddress,
        uint24 _chainId,
        string memory _wrappedGasTokenName,
        string memory _wrappedGasTokenSymbol,
        uint24 _fee,
        uint24 _priceImpactPercentage,
        uint160 _sqrtPriceX96,
        address _nonFungiblePositionManagerAddress,
        address _newLocalBranchWrappedNativeTokenAddress,
        address _newUnderlyingBranchWrappedNativeTokenAddress
    ) external;
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootPort.sol#L280C1-L293C16

```
 function bridgeOutMultiple(
        address _depositor,
        address[] memory _localAddresses,
        address[] memory _underlyingAddresses,
        uint256[] memory _amounts,
        uint256[] memory _deposits
    ) external;
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IBranchPort.sol#L120C4-L126C16

all of this page 
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/interfaces/IRootRouter.sol

## [G-5] Events are not indexed

``` 
	event LogCallin(bytes1 selector, bytes data, uint24 fromChainId);
    event LogCallout(bytes1 selector, bytes data, uint256, uint24 toChainId);
    event LogCalloutFail(bytes1 selector, bytes data, uint24 toChainId);
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/interfaces/IRootBridgeAgent.sol#L378C4-L380C73



## [G-06] If statements that use && can be refactored into nested if statements
 463: `if (canExceedMax && account.code.length == 0) revert Errors.NonContractError();`
https://github.com/code-423n4/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L464

273:`if (amount0 == 0 && amount1 == 0) revert AmountsAreZero();`
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L273


## [G-07]  Avoiding Initialization of the variables to it's default value.

https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol

## [G-08]  Avoiding Initialization of Loop Index If It Is 0 and Avoid unnecessary read of array length in for loops can save gas.
48: ` for (uint256 i = 0; i < calls.length; i++) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L48

99: `for (uint256 i = 0; i < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L99

107: ` for (uint256 i = 0; i < length;) {`
110: `     for (uint256 j = 0; j < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L107-L110

123: ` for (uint256 i = 0; i < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L123

145: `  for (uint256 i = 0; i < length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L145

101: ` for (uint256 i = 0; i < _localAddresses.length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L101

176: ` for (uint256 i = 0; i < size; i++) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L176

311: `  for (uint256 i = 0; i < outputParams.outputTokens.length;) {`
https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L311

## [G-09] Rearranging order of state variable declarations to pack them will save storage slots and gas
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/base/TalosBaseStrategy.sol#L37-L54

## [G-10] duplication of code 
```
  // next cycle is always the next even divisor of the cycle length above current block timestamp.
 uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
        uint32 lastCycle = gaugeCycle;

        // ensure new cycle has begun
        if (currentCycle <= lastCycle) revert CycleError();
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L79C5-L83C60
==with==
```
  // next cycle is always the next even divisor of the cycle length above current block timestamp.
        uint32 currentCycle = (block.timestamp.toUint32() / gaugeCycleLength) * gaugeCycleLength;
        uint32 lastCycle = gaugeCycle;

        // ensure new cycle has begun
        if (currentCycle <= lastCycle) revert CycleError();
```
https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/rewards/FlywheelGaugeRewards.sol#L113-L118

## [G-11] Use solidity version 0.8.19 to gain some gas boost
>Upgrade to the latest solidity version 0.8.19 to get additional gas savings.
> 
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L2
>
>https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L2
> 
>...
>See the latest release for reference: https://blog.soliditylang.org/2023/02/22/solidity-0.8.19-release-announcement/


