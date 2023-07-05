
## events NewImplementation, NewPendingAdmin, NewAdmin, WhitelistAccountExpirationSet and WhitelistGuardianSet does not use indexed addresses. Off-chain scripts cannot efficiently filter events without indexed addresses.

### Proof Of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L42

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L48

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L51

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L54

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/governance/GovernorBravoInterfaces.sol#L57

### -----------------------------------------------------------

## Zero address does not check in function "accrueBribes".

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/ccc9a39240dbd8eab22299737370996b2b833efd/src/gauges/BaseV2Gauge.sol#L111

### Steps to Mitigation:

**Check to make sure zero address will not be passed.**

### -----------------------------------------------------------

## Zero address does not check in function "_incrementGaugeWeight" Gauge parameter address. Functions "incrementGauges" and "incrementGauge" are calling this function and considering that each one of them can be called by anyone or any contract, passing zero address as gauge address can cause unexpected behaviors. Same issue exists in "_decrementGaugeWeight" function too.

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/ccc9a39240dbd8eab22299737370996b2b833efd/src/erc-20/ERC20Gauges.sol#L202

https://github.com/code-423n4/2023-05-maia/blob/ccc9a39240dbd8eab22299737370996b2b833efd/src/erc-20/ERC20Gauges.sol#L292

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in these two functions.**

### -----------------------------------------------------------

## Zero address does not check in function "depositToPort" _depositor and _recipient parameters. 

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L45-L47

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "withdrawFromPort" _depositor and _recipient parameters. 

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L58-L60

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "withdraw" _recipient and _underlyingAddress parameters. 

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L76-L80

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "bridgeIn" _recipient and _localAddress parameters. 

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L87-L91

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "bridgeInMultiple" _recipient parameter. 

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L96-L100

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "bridgeOut" _depositor, _localAddress and _underlyingAddress parameters.

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L111-L117

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "bridgeOutMultiple" _depositor parameter.

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumBranchPort.sol#L129-L135

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "addLocalToken" _underlyingAddress parameter.

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L47

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in functions "anyExecuteSigned", "anyExecuteSignedDepositSingle" and "anyExecuteSignedDepositMultiple" userAccount parameter.

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/MulticallRootRouter.sol#L268-L275

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/MulticallRootRouter.sol#L345-L351

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/MulticallRootRouter.sol#L421-L427

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**

### -----------------------------------------------------------

## Zero address does not check in function "addBranchToBridgeAgent" _newBranchRouter and _gasReceiver parameters. It can cause unexpected effects as it is external and payable.

### Proof of Concept:

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/CoreRootRouter.sol#L81-L88

### Steps to Mitigation:

**Consider check to make sure zero address will not be passed in this function.**