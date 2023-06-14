Gas Issue #1. Use indexed events rather than non indexed events.

Events that have indexed variables actually use less gas than events that have non-indexed variables.
Adding the indexed key word to the events within these contracts can lower the cost of execution for the functions that emit them.  

All-in-all, there are: 
13 events within GovernorBravoInterfaces.sol (in governance folder) (line 6, 24, 27, 30, 33, 36, 39, 42, 45, 48, 51, 54, 57) 
https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L24,
 
13 in total within GovernorBravoInterfaces.sol(in out-of-scope folder) (line 6, 24, 27, 30, 33, 36, 39, 42, 45, 48, 51, 54, 57)
https://github.com/code-423n4/2023-05-maia/blob/main/src/out-of-scope/governance/GovernorBravoInterfaces.sol#L24,

1 event within PoolActions.sol (23)
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L23,
  
1 event within PoolVariables.sol (28)
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L28,
 
and 2 events within TalosStrategyVanilla.sol (165, 170)
https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L165, 

 
Amount of gas savings per indexed event: 16 gas units

 

Gas Issue #2. More efficient Storage packing in contract storage and Structs.


https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L41
There are 3 instances where storage slots can be packed more efficiently, which will save gas during contract executions. First, inside of UniswapV3Guage (and the inheriting BaseV2Guage contract on line 41), address public override strategy can be put below FlywheelCore[] private bribeFlywheels. This would use one less slot of storage (see storage layout below).

                     old 

    // address private _owner; // slot 0                                   

    // mapping(FlywheelCore => bool) public override isActive; // slot 1
    // mapping(FlywheelCore => bool) public override added; // slot 2
    // address public override strategy; // slot 3
    // MultiRewardsDepot public override multiRewardsDepot; // slot 4
    // uint256 public override epoch; // slot 5
    // FlywheelCore[] private bribeFlywheels; // slot 6

    // uint24 public override minimumWidth;  // slot 7 (UniswapV3Guage)


                    new 

    // address private _owner; // slot 0                                   

    // mapping(FlywheelCore => bool) public override isActive; // slot 1
    // mapping(FlywheelCore => bool) public override added; // slot 2
    // MultiRewardsDepot public override multiRewardsDepot; // slot 3
    // uint256 public override epoch; // slot 4
    // FlywheelCore[] private bribeFlywheels; // slot 5
    // address public override strategy; // slot 6

    // uint24 public override minimumWidth;  (UniswapV3Guage)



https://github.com/code-423n4/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L109
Furthermore, inside contract GovernorBravoDelegateStorageV1 within the GovernorBravoInterfaces.sol file (line 109, 131, 133), the variables within the Proposal struct can be packed even more than they currently are.  Specifically, bool canceled and bool executed can be moved next to address proposer.  This way, one less slot will be used (see below).

             old

    // struct Proposal {    
    //     uint256 id;  // slot 0
    //     address proposer; // slot 1
    //     uint256 eta; // slot 2
    //     address[] targets; // slot 3
    //     uint256[] values; // slot 4
    //     string[] signatures; // slot 5
    //     bytes[] calldatas; // slot 6
    //     uint256 startBlock; // slot 7
    //     uint256 endBlock; // slot 8
    //     uint256 forVotes; // slot 9
    //     uint256 againstVotes; // slot 10
    //     uint256 abstainVotes; // slot 11
    //     bool canceled; // slot 12
    //     bool executed;
    //     mapping(address => Receipt) receipts; // slot 13
    // }

            new 

    // struct Proposal {    
    //     uint256 id;  // slot 0
    //     address proposer; // slot 1
    //     bool canceled;
    //     bool executed;
    //     uint256 eta; // slot 2
    //     address[] targets; // slot 3
    //     uint256[] values; // slot 4
    //     string[] signatures; // slot 5
    //     bytes[] calldatas; // slot 6
    //     uint256 startBlock; // slot 7
    //     uint256 endBlock; // slot 8
    //     uint256 forVotes; // slot 9
    //     uint256 againstVotes; // slot 10
    //     uint256 abstainVotes; // slot 11
    //     mapping(address => Receipt) receipts; // slot 12
    // }


https://github.com/code-423n4/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L39
Likewise, In TalosBaseStrategy.sol, uint128 public override liquidity can be moved down to below protocolFees1.  One less slot would be used (see below).

                       old

    // uint256 public override tokenId;  slot 0
    // uint128 public override liquidity; slot 1
    // uint256 public protocolFees0; // slot 2
    // uint256 public protocolFees1; // slot 3
    // int24 public override tickLower; // slot 4
    // int24 public override tickUpper;
    // bool public initialized;


                       new 

    // uint256 public override tokenId;  slot 0
    // uint256 public protocolFees0; // slot 1
    // uint256 public protocolFees1; // slot 2
    // uint128 public override liquidity; slot 3
    // int24 public override tickLower;
    // int24 public override tickUpper;
    // bool public initialized;


