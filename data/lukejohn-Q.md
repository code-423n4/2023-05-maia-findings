QA1. UlyssesPool.maxRedeem() needs to consider the protocol fees. 

Impact: without considering the protocol fees, a user might redeem too much funds and then there is no sufficient left amount of funds for the protocol fees.  

The correction is the following:

```diff
 function maxRedeem(address owner) public view override returns (uint256) {
-        return balanceOf[owner].min(asset.balanceOf(address(this)));
+        return balanceOf[owner].min(asset.balanceOf(address(this))- getProtocolFees());

    }
```

QA2. ERC20Boost.decrementGaugeBoost() fails to update getUserBoost[user]. As a result, ``freeGaugeBoost[user]`` and ``noAttached(user, amount)`` are not accurate and up-to-date. Then,  a user might not be able to perform burn(), transfer() or transferFrom() for the boost tokens since these three functions are constrained by modifier ``noAttached(user, amount)`` but ``noAttached(user, amount)`` might fail due to such inaccuracy of ``freeGaugeBoost[user]``.

[https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L175-L187](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-20/ERC20Boost.sol#L175-L187)

The following POC code illustrates the following scenario - see function  testGaugeBoost():

1) depositor 1 deposits 1001 HERMES into _bhermes and claims 1001 boost tokens;

2) three gauges, gauge1, gauge2, and guage3 are added to the gaugeBoost;

3) Each gauge, gauge1, gauge2 and gauge2 attaches user depositor to itself, as a result, getUserBoost[depositor1] = 1001;

4) Gauge1, gauge2 and gauge3 calls gaugeBoost.decrementGaugeBoost() to decrement its guage boost by 1000 respectively. Supposedly, getUserBoost[depositor1] needs to be changed to 1 as a result, However, the gaugeBoost.decrementGaugeBoost() fails to do so, so getUserBoost[depositor1] = 1001 remains to be the same. 

4) As a result, when depositor1 tries to transfer 50 boost tokens to depositor2, it fails although we expect it to be successful since we have 1000 free boost tokens for depositor1. 

```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import "../src/maia/tokens/Maia.sol";
import "../src/hermes/tokens/HERMES.sol";
import "../src/hermes/bHermes.sol";
import "../src/hermes/tokens/bHermesGauges.sol";
import "../src/hermes/tokens/bHermesBoost.sol";
import {bHermesVotes as ERC20Votes} from "../src/hermes/tokens/bHermesVotes.sol";
import {IBaseV2Gauge} from "@gauges/interfaces/IBaseV2Gauge.sol";
import "../src/erc-20/interfaces/IERC20Gauges.sol";
import "../src/rewards/rewards/FlywheelGaugeRewards.sol";
import "../src/hermes/minters/BaseV2Minter.sol";
import {MockERC20} from "solmate/test/utils/mocks/MockERC20.sol";
import {UniswapV3Factory, UniswapV3Pool} from "@uniswap/v3-core/contracts/UniswapV3Factory.sol";

import {UniswapV3Factory, UniswapV3Pool} from "@uniswap/v3-core/contracts/UniswapV3Factory.sol";

import {IWETH9} from "@uniswap/v3-periphery/contracts/interfaces/external/IWETH9.sol";
import {ISwapRouter} from "@uniswap/v3-periphery/contracts/interfaces/ISwapRouter.sol";
import {
    NonfungiblePositionManager, IUniswapV3Pool
} from "@uniswap/v3-periphery/contracts/NonfungiblePositionManager.sol";
import {SwapRouter} from "@uniswap/v3-periphery/contracts/SwapRouter.sol";

import {TalosBaseStrategy} from "@talos/base/TalosBaseStrategy.sol";
import {TalosOptimizer} from "@talos/TalosOptimizer.sol";
import {PoolVariables, PoolActions} from "@talos/libraries/PoolActions.sol";
import {
    IUniswapV3Pool,
    UniswapV3Staker,
    IUniswapV3Staker,
    IncentiveTime,
    IncentiveId,
    bHermesBoost
} from "@v3-staker/UniswapV3Staker.sol";
import {TalosStrategyVanilla} from "@talos/TalosStrategyVanilla.sol";
import {UniswapV3GaugeFactory} from "@gauges/factories/UniswapV3GaugeFactory.sol";
import "@uniswap/v3-core/contracts/libraries/TickMath.sol";
import "../src/talos/libraries/PoolVariables.sol";
import "@uniswap/v3-periphery/contracts/libraries/PoolAddress.sol";
import "../src/rewards/booster/FlywheelBoosterGaugeWeight.sol";
import "../src/gauges/factories/BribesFactory.sol";
import "../src/gauges/factories/BaseV2GaugeManager.sol";
import "../src/weth9.sol";
import "../lib/v3-periphery/contracts/interfaces/INonfungiblePositionManager.sol";
import "../src/rewards/FlywheelCoreInstant.sol";





contract MaiaTest is Test {
    Maia _maia; 
    HERMES _hermes;
    bHermes _bhermes;

    bHermesGauges gaugeWeight;
    bHermesBoost  gaugeBoost;
    ERC20Votes    governance;

    MockERC20   _tokenA;
    MockERC20   _tokenB;
    UniswapV3Factory uniswapV3Factory;
    SwapRouter swapRouterContract;
    ISwapRouter swapRouter;
    NonfungiblePositionManager nonfungiblePositionManager;
    IUniswapV3Pool pool;
    UniswapV3Pool poolContract;
    TalosStrategyVanilla talosBaseStrategy;         /**???? */
    TalosOptimizer strategyOptimizer;            /** ??? */
    bHermesBoost _bHermesBoost;
    IUniswapV3Staker uniswapV3Staker;
    UniswapV3Staker uniswapV3StakerContract;
    FlywheelBoosterGaugeWeight flywheelGaugeWeightBooster;
    BribesFactory bribesFactory;
    UniswapV3GaugeFactory uniswapV3GaugeFactory;
    BaseV2GaugeManager baseV2GaugeManager;


    IWETH9 WETH9 = IWETH9(new weth9()); 

    address owner = address(0x901);   
    address depositor1 = address(0x101);
    address depositor2 = address(0x102);
    address depositor3 = address(0x103);
   
    address gauge1 = address(0x801);
    address gauge2 = address(0x802);
    address gauge3 = address(0x803);

    FlywheelGaugeRewards _flywheelGaugeRewards;
    BaseV2Minter _baseV2Minter;


    function setUp() public {
        _maia = new Maia(owner);
        _hermes = new HERMES(owner);
        _bhermes = new bHermes(_hermes, owner, 1 days, 2 hours);


        gaugeWeight = _bhermes.gaugeWeight();
        gaugeBoost = _bhermes.gaugeBoost();
        governance = _bhermes.governance();

  
        vm.startPrank(owner);
        
        _maia.mint(depositor1, 0);
        _maia.mint(depositor1, 1101e18);
        _maia.mint(depositor2, 1102e18);
        _maia.mint(depositor3, 1103e18);
        _maia.mint(address(this), 1_000_000e18);
        
        
        _hermes.mint(depositor1, 0);
        _hermes.mint(depositor1, 5_000_000e18);
        _hermes.mint(depositor2, 5_000_001e18);
        _hermes.mint(depositor3, 5_000_002e18);
        _hermes.mint(address(this), 5_000_003e18);

       
        // add gauges
        
        gaugeWeight.addGauge(gauge1);
        gaugeWeight.addGauge(gauge2);
        gaugeWeight.addGauge(gauge3);
        gaugeWeight.setMaxGauges(10);
        gaugeWeight.setMaxDelegates(10);
        
        vm.stopPrank();


        // create tokens
        _tokenA = new MockERC20("TokenA", "TKA", 18);
        _tokenB = new MockERC20("TokenB", "TKB", 18);

        _tokenA.mint(address(this), 25e18);
        _tokenB.mint(address(this), 25e18);
        vm.prank(owner);
        _hermes.mint(address(this), 25e18);
     

        uniswapV3Factory = new UniswapV3Factory();
        console2.log("factory address");
        console2.logAddress(address(uniswapV3Factory));
        swapRouterContract = new SwapRouter(address(uniswapV3Factory), address(WETH9));
        swapRouter = ISwapRouter(address(swapRouterContract));
     
        nonfungiblePositionManager = new NonfungiblePositionManager(
            address(uniswapV3Factory),   // uniswapV3Factory
            address(WETH9),         // weth9
            address(0)              // token descriptor
        );
        address poolAddress = uniswapV3Factory.createPool(address(WETH9), address(_tokenB), 3000);
        pool = IUniswapV3Pool(poolAddress);
        console2.log("pool address: ");
        console2.logAddress(address(pool));
        poolContract = UniswapV3Pool(poolAddress);
        poolContract.initialize(56022770974786139918731938227);  // inital price sqrtpirce x 96
        poolContract.increaseObservationCardinalityNext(100);
        // just a bunch of parameter values
        strategyOptimizer = new TalosOptimizer(100, 40, 16, 2000, type(uint256).max, address(this));

        
        _bHermesBoost = _bhermes.gaugeBoost();

        baseV2GaugeManager  = new BaseV2GaugeManager(_bhermes, address(this), address(this));

        
        
        
        /*
        uniswapV3StakerContract = new UniswapV3Staker(
            uniswapV3Factory,
            nonfungiblePositionManager,
            UniswapV3GaugeFactory(address(this)),
            _bHermesBoost,
            31536000,
            address(this),
            address(_hermes)
        );
        uniswapV3Staker = IUniswapV3Staker(address(uniswapV3StakerContract));
        */

        talosBaseStrategy = new TalosStrategyVanilla(
            pool,
            strategyOptimizer,
            nonfungiblePositionManager,
            address(this),                     // strategy manager
            address(this)
        );
       

        _baseV2Minter = new BaseV2Minter(address(_bhermes), owner, owner);  // reward token, dao, owner

        vm.prank(owner);
        _hermes.transferOwnership(address(_baseV2Minter)); // now only _baseV2Minter can mint HERMES

        _flywheelGaugeRewards = new FlywheelGaugeRewards(
            address(_hermes), // reward token
            address(this),     
            _bhermes.gaugeWeight(),  //  bHermesGauges
            _baseV2Minter           // BaseV2Minter
        );
        _baseV2Minter.initialize(_flywheelGaugeRewards);


        flywheelGaugeWeightBooster = new FlywheelBoosterGaugeWeight(_bhermes.gaugeWeight());

        bribesFactory = new BribesFactory(
            BaseV2GaugeManager(address(this)),
            flywheelGaugeWeightBooster,
            1 weeks,
            address(this)
        );

       


        uniswapV3GaugeFactory = new UniswapV3GaugeFactory(
            BaseV2GaugeManager(address(0)),       // zero address is the GaugeManager
            _bHermesBoost,
            uniswapV3Factory,
            nonfungiblePositionManager,
            _flywheelGaugeRewards,
            bribesFactory,
            address(this)
        );

        uniswapV3StakerContract = uniswapV3GaugeFactory.uniswapV3Staker();

        uniswapV3Staker = IUniswapV3Staker(address(uniswapV3StakerContract));

        baseV2GaugeManager =  new BaseV2GaugeManager(_bhermes, address(this), address(this));
       

        console2.log("circulating supply: %d", _baseV2Minter.circulatingSupply()); // underlying.totalsupply - vault(_bhermes).totalAssets(herm)

        console2.log("\n\nset up is done.\n\n");
        console2.log("****************************************************");      
    }


function testGaugeBoost() public{
    uint deposit1Amount = 1001;

     vm.startPrank(depositor1);
    _hermes.approve(address(_bhermes), deposit1Amount);
    _bhermes.deposit(deposit1Amount, address(depositor1));       // depositor hermest into vault
    console2.log("depositor1 claim weight: %d", deposit1Amount);
    _bhermes.claimBoost(deposit1Amount);             // claim weight based on bherms balance
    vm.stopPrank();
    console2.log("boost balance of depositor1: %d", gaugeBoost.balanceOf(depositor1));

    vm.startPrank(owner);
    gaugeBoost.addGauge(gauge1);
    gaugeBoost.addGauge(gauge2);
    gaugeBoost.addGauge(gauge3);
    vm.stopPrank();
 
    vm.prank(address(gauge1));
    gaugeBoost.attach(address(depositor1));
    console2.log("getUserBoost[depositor1]: %d", gaugeBoost.getUserBoost(depositor1));

    vm.prank(address(gauge2));
    gaugeBoost.attach(address(depositor1));
    console2.log("getUserBoost[depositor1]: %d", gaugeBoost.getUserBoost(depositor1));
    
    vm.prank(address(gauge3));
    gaugeBoost.attach(address(depositor1));
    console2.log("getUserBoost[depositor1]: %d", gaugeBoost.getUserBoost(depositor1));

    vm.startPrank(address(depositor1));
    gaugeBoost.decrementGaugeBoost(address(gauge1), 1000);
    gaugeBoost.decrementGaugeBoost(address(gauge2), 1000);
    gaugeBoost.decrementGaugeBoost(address(gauge3), 1000);
    vm.stopPrank();
    console2.log("getUserBoost[depositor1]: %d", gaugeBoost.getUserBoost(depositor1));
    
    vm.prank(address(depositor1));
    vm.expectRevert();
    gaugeBoost.transfer(depositor2, 50);
 
}
}
```

QA3. BaseV2Minter.getRewards() does not calculate ``totalQueuedForCycle`` correctly. 

This is because the ``BaseV2Minter`` contract might not have sufficient coverage for the ``weekly`` amount. In this case, ``totalQueuedForCycle`` should be the balance of underlying in the  ``BaseV2Minter`` contract. Correction: 

```diff
function getRewards() external returns (uint256 totalQueuedForCycle) {
        if (address(flywheelGaugeRewards) != msg.sender) revert NotFlywheelGaugeRewards();
        totalQueuedForCycle = weekly;
+       uint256 bal = underlying.balanceOf(address(this));
+       if(bal < totalQueuedForCycle) totalQueuedForCycle = bal;
-        weekly = 0;
+       weekly -= totalQueuedForCycle;
        underlying.safeTransfer(msg.sender, totalQueuedForCycle);
    }
```


QA3. Much confusion is introduced due to renaming in import:
1) FlywheelCore is renamed as core:  

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/rewards/FlywheelCoreInstant.sol#L10C1-L10C62

2) FlyWheelStrategy is renamed as FlyWheelCore sometimes. 

To avoid this confusion, we eliminate all renamings.

QA4: When currentTick is too high or too low, TalosManager.getRebalance() will underflow instead of returning true. 

[https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/TalosManager.sol#L66-L72](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/TalosManager.sol#L66-L72)

Correction: check to make sure underflow will not occur:

```diff
function getRebalance(ITalosBaseStrategy position) private view returns (bool) {
        //Calculate base ticks.
        (, int24 currentTick,,,,,) = position.pool().slot0();

-        return currentTick - position.tickLower() >= ticksFromLowerRebalance
-            || position.tickUpper() - currentTick >= ticksFromUpperRebalance;

+ if(currentTick > position.tickLower() && currentTick - position.tickLower() >= ticksFromLowerRebalance) return true;

+ if(position.tickUpper() > currentTick && position.tickUpper() - currentTick >= ticksFromUpperRebalance) return true; 

+ return false;
    }
```

QA5. When currentTick is too high or too low, TalosManager.getRerange() will underflow instead of returning true. 

[https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/TalosManager.sol#L78-L84](https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/talos/TalosManager.sol#L78-L84)

Correction: check to make sure underflow will not occur:
function getRerange(ITalosBaseStrategy position) private view returns (bool) {
        //Calculate base ticks.
        (, int24 currentTick,,,,,) = position.pool().slot0();

-        return currentTick - position.tickLower() >= ticksFromLowerRerange
-            || position.tickUpper() - currentTick >= ticksFromUpperRerange;

+        if(currentTick > position.tickLower() && currentTick - position.tickLower() >= ticksFromLowerRerange) return true;

+        if(position.tickUpper() > currentTick && position.tickUpper() - currentTick >= ticksFromUpperRerange) return true;

+ return false;
}

