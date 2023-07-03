
***What:***
=====
Maia DAO is a decentralized "omnichain" organization aiming to improve capital efficiency on DeFi. 
The MAIA ecosystem comprises 4 protocols: Hermes, Maia, Talos, and Ulysses. These protocols are orchestrating the allocation of liquidity through the cross-chain "virtual pools" and the governance of the DAO.

This report will focus solely on ***14*** possible gas optimizations I identified in the code across the entire repository ( this is my first report ever on code4rena )

⛽️ ***Gas Report:***
=====

* *GAS001*
  - File: /src/governance/GovernorBravoDelegateMaia.sol:190
  - Desc: the variable `timelock` should be cached to avoid double SLOAD.
  - Code Currently: 
  ```
  function queueOrRevertInternal(
        address target,
        uint256 value,
        string memory signature,
        bytes memory data,
        uint256 eta
    ) internal {
        require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))), <---SLOAD HERE
            "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
        );
        timelock.queueTransaction(target, value, signature, data, eta); <--- ANOTHER SLOAD HERE
    }
    ```

   - Code Suggested:   

   ```
   function queueOrRevertInternal(
            address target,
            uint256 value,
            string memory signature,
            bytes memory data,
            uint256 eta
        ) internal {
            TimelockInterface _t = timelock;
            require(
                !_t.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                "GovernorBravo::queueOrRevertInternal: identical proposal action already queued at eta"
            );
            _t.queueTransaction(target, value, signature, data, eta);
        }```

* *GAS002*
  - File: src/governance/GovernorBravoDelegator.sol:41
  - Desc: The function `_setImplementation` can be payable to avoid the check on VALUE and save gas (only the owner can call it)
  - Code Currently:
  ```
  function _setImplementation(address implementation_) public {
        require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");
        require(
            implementation_ != address(0), "GovernorBravoDelegator::_setImplementation: invalid implementation address"
        );

        address oldImplementation = implementation;
        implementation = implementation_;

        emit NewImplementation(oldImplementation, implementation);
    }
  ```

* *GAS003*
  - File: src/governance/GovernorBravoDelegateMaia.sol
  - Desc: The function `_setVotingDelay` can be payable to avoid check on VALUE and save gas (only the owner can call it).
  - Code Currently:
  ```
  function _setImplementation(address implementation_) public {
        require(msg.sender == admin, "GovernorBravoDelegator::_setImplementation: admin only");
        require(
            implementation_ != address(0), "GovernorBravoDelegator::_setImplementation: invalid implementation address"
        );

        address oldImplementation = implementation;
        implementation = implementation_;

        emit NewImplementation(oldImplementation, implementation);
    }
  ```

* *GAS004*
  - File: src/governance/GovernorBravoDelegateMaia.sol
  - Desc: The function `_setVotingPeriod` can be payable to avoid check on VALUE and save gas (only the owner can call it).
  - Code Currently:
  ```
  function _setVotingPeriod(uint256 newVotingPeriod) external {
        require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            "GovernorBravo::_setVotingPeriod: invalid voting period"
        );
        uint256 oldVotingPeriod = votingPeriod;
        votingPeriod = newVotingPeriod;

        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
    }
  ```

* *GAS005*
  - File: src/governance/GovernorBravoDelegateMaia.sol
  - Desc: The function `_setProposalThreshold` can be payable to avoid check on VALUE and save gas (only the owner can call it).
  - Code Currently:
  ```
  function _setProposalThreshold(uint256 newProposalThreshold) external {
        require(msg.sender == admin, "GovernorBravo::_setProposalThreshold: admin only");
        require(
            newProposalThreshold >= MIN_PROPOSAL_THRESHOLD && newProposalThreshold <= MAX_PROPOSAL_THRESHOLD,
            "GovernorBravo::_setProposalThreshold: invalid proposal threshold"
        );
        uint256 oldProposalThreshold = proposalThreshold;
        proposalThreshold = newProposalThreshold;

        emit ProposalThresholdSet(oldProposalThreshold, proposalThreshold);
    }
  ```

* *GAS006/GAS007/GAS008/GAS009/GAS010* (aggregate)
  - File: src/governance/GovernorBravoDelegateMaia.sol
  - Desc: The function `_setWhitelistAccountExpiration`/`_setWhitelistGuardian`/`_initiate`/`_setPendingAdmin`/`_acceptAdmin` can be payable to avoid VALUE checks as in the previous entries (*GAS002*-->*GAS005*).

* *GAS011* 
  - File: /src/rewards/rewards/FlywheelGaugeRewards.sol
  - Desc: Moving the mapping `gaugeQueuedRewards` at the end saves a storage slot.
  - Storage BEFORE:

    | Name                   | Type                                                                  | Slot | Offset | Bytes | Contract                                                          |
    |------------------------|-----------------------------------------------------------------------|------|--------|-------|-------------------------------------------------------------------|
    | gaugeCycle             | uint32                                                                | 0    | 0      | 4     | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | gaugeQueuedRewards     | mapping(contract ERC20 => struct IFlywheelGaugeRewards.QueuedRewards) | 1    | 0      | 32    | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | nextCycle              | uint32                                                                | 2    | 0      | 4     | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | nextCycleQueuedRewards | uint112                                                               | 2    | 4      | 14    | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | paginationOffset       | uint32                                                                | 2    | 18     | 4     | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards | 

  - Storage AFTER:

    | Name                   | Type                                                                  | Slot | Offset | Bytes | Contract                                                          |
    |------------------------|-----------------------------------------------------------------------|------|--------|-------|-------------------------------------------------------------------|
    | gaugeCycle             | uint32                                                                | 0    | 0      | 4     | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | nextCycle              | uint32                                                                | 0    | 4      | 4     | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | nextCycleQueuedRewards | uint112                                                               | 0    | 8      | 14    | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | paginationOffset       | uint32                                                                | 0    | 22     | 4     | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |
    | gaugeQueuedRewards     | mapping(contract ERC20 => struct IFlywheelGaugeRewards.QueuedRewards) | 1    | 0      | 32    | src/rewards/rewards/FlywheelGaugeRewards.sol:FlywheelGaugeRewards |

* *GAS012*
  - File: /src/ulysses-omnichain/RootPort.sol
  - Desc: Moving the variable `_setup` closer to one of the address result in saving a storage slot.
  - Storage BEFORE:

    | Name                        | Type                                                         | Slot | Offset | Bytes | Contract                                    |
    |-----------------------------|--------------------------------------------------------------|------|--------|-------|---------------------------------------------|
    | localBranchPortAddress      | address                                                      | 0    | 0      | 20    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | coreRootRouterAddress       | address                                                      | 1    | 0      | 20    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | coreRootBridgeAgentAddress  | address                                                      | 2    | 0      | 20    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getUserAccount              | mapping(address => contract VirtualAccount)                  | 3    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isRouterApproved            | mapping(contract VirtualAccount => mapping(address => bool)) | 4    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isChainId                   | mapping(uint256 => bool)                                     | 5    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isBridgeAgent               | mapping(address => bool)                                     | 6    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgents                | address[]                                                    | 7    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgentsLenght          | uint256                                                      | 8    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getBridgeAgentManager       | mapping(address => address)                                  | 9    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isBridgeAgentFactory        | mapping(address => bool)                                     | 10   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgentFactories        | address[]                                                    | 11   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgentFactoriesLenght  | uint256                                                      | 12   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isGlobalAddress             | mapping(address => bool)                                     | 13   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getGlobalTokenFromLocal     | mapping(address => mapping(uint256 => address))              | 14   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getLocalTokenFromGlobal     | mapping(address => mapping(uint256 => address))              | 15   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getLocalTokenFromUnder      | mapping(address => mapping(uint256 => address))              | 16   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getUnderlyingTokenFromLocal | mapping(address => mapping(uint256 => address))              | 17   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getWrappedNativeToken       | mapping(uint256 => address)                                  | 18   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getGasPoolInfo              | mapping(uint256 => struct GasPoolInfo)                       | 19   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | _setup                      | bool                                                         | 20   | 0      | 1     | src/ulysses-omnichain/RootPort.sol:RootPort |
  
  - Storage AFTER:

    | Name                        | Type                                                         | Slot | Offset | Bytes | Contract                                    |
    |-----------------------------|--------------------------------------------------------------|------|--------|-------|---------------------------------------------|
    | localBranchPortAddress      | address                                                      | 0    | 0      | 20    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | coreRootRouterAddress       | address                                                      | 1    | 0      | 20    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | coreRootBridgeAgentAddress  | address                                                      | 2    | 0      | 20    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | _setup                      | bool                                                         | 2    | 20     | 1     | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getUserAccount              | mapping(address => contract VirtualAccount)                  | 3    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isRouterApproved            | mapping(contract VirtualAccount => mapping(address => bool)) | 4    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isChainId                   | mapping(uint256 => bool)                                     | 5    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isBridgeAgent               | mapping(address => bool)                                     | 6    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgents                | address[]                                                    | 7    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgentsLenght          | uint256                                                      | 8    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getBridgeAgentManager       | mapping(address => address)                                  | 9    | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isBridgeAgentFactory        | mapping(address => bool)                                     | 10   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgentFactories        | address[]                                                    | 11   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | bridgeAgentFactoriesLenght  | uint256                                                      | 12   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | isGlobalAddress             | mapping(address => bool)                                     | 13   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getGlobalTokenFromLocal     | mapping(address => mapping(uint256 => address))              | 14   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getLocalTokenFromGlobal     | mapping(address => mapping(uint256 => address))              | 15   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getLocalTokenFromUnder      | mapping(address => mapping(uint256 => address))              | 16   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getUnderlyingTokenFromLocal | mapping(address => mapping(uint256 => address))              | 17   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getWrappedNativeToken       | mapping(uint256 => address)                                  | 18   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |
    | getGasPoolInfo              | mapping(uint256 => struct GasPoolInfo)                       | 19   | 0      | 32    | src/ulysses-omnichain/RootPort.sol:RootPort |

* *GAS013* 
  - File: src/talos/base/TalosBaseStrategy.sol
  - Desc: Moving the variables `tickLower`, `tickUpper` and `initialized` right after `liquidity` save 1 storage slot 
  - Storage BEFORE:

    | Name          | Type                                            | Slot | Offset | Bytes | Contract                                               |
    |---------------|-------------------------------------------------|------|--------|-------|--------------------------------------------------------|
    | name          | string                                          | 0    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | symbol        | string                                          | 1    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | totalSupply   | uint256                                         | 2    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | balanceOf     | mapping(address => uint256)                     | 3    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | allowance     | mapping(address => mapping(address => uint256)) | 4    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | nonces        | mapping(address => uint256)                     | 5    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | locked        | uint256                                         | 6    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | tokenId       | uint256                                         | 7    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | liquidity     | uint128                                         | 8    | 0      | 16    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | protocolFees0 | uint256                                         | 9    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | protocolFees1 | uint256                                         | 10   | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | tickLower     | int24                                           | 11   | 0      | 3     | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | tickUpper     | int24                                           | 11   | 3      | 3     | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | initialized   | bool                                            | 11   | 6      | 1     | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |

  - Storage AFTER:

    | Name          | Type                                            | Slot | Offset | Bytes | Contract                                               |
    |---------------|-------------------------------------------------|------|--------|-------|--------------------------------------------------------|
    | name          | string                                          | 0    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | symbol        | string                                          | 1    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | totalSupply   | uint256                                         | 2    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | balanceOf     | mapping(address => uint256)                     | 3    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | allowance     | mapping(address => mapping(address => uint256)) | 4    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | nonces        | mapping(address => uint256)                     | 5    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | locked        | uint256                                         | 6    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | tokenId       | uint256                                         | 7    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | liquidity     | uint128                                         | 8    | 0      | 16    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | tickLower     | int24                                           | 8    | 16     | 3     | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | tickUpper     | int24                                           | 8    | 19     | 3     | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | initialized   | bool                                            | 8    | 22     | 1     | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | protocolFees0 | uint256                                         | 9    | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |
    | protocolFees1 | uint256                                         | 10   | 0      | 32    | src/talos/base/TalosBaseStrategy.sol:TalosBaseStrategy |

* *GAS014* 
  - File: src/ulysses-omnichain/BranchBridgeAgent.sol
  - Desc: The `_dParams` of the function `callOutAndBridge(bytes calldata _params, DepositInput memory _dParams, uint128 _remoteExecutionGas)` can be of type "calldata:. This would also require to change the function `_callOutAndBridge` (Line 673) with:
  ```
  function _callOutAndBridge(
        address _depositor,
        bytes calldata _params,
        DepositInput calldata _dParams,  <---- change the location type of this too
        uint128 _gasToBridgeOut,
        uint128 _remoteExecutionGas
    ) internal {
  ```
