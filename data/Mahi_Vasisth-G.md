Usage of uints/ints smaller than 32 bytes (256 bits) incurrs overhead: 

2023-05-maia/src/talos/factories/OptimizerFactory.sol: 

The EVM operates with 32 byte words. Therefore, if you declare state variables less than 32 bytes the EVM will need to perform extra operations to cast your value to the specified size.
Code proof: 

function createTalosOptimizer( 

        uint32 _twapDuration, 

        int24 _maxTwapDeviation, 

        int24 _tickRangeMultiplier, 

        uint24 _priceImpactPercentage, 

        uint256 _maxTotalSupply, 

        address owner 

    ) external { 

        TalosOptimizer optimizer = new TalosOptimizer( 

            _twapDuration, 

            _maxTwapDeviation, 

            _tickRangeMultiplier, 

            _priceImpactPercentage, 

            _maxTotalSupply, 

            owner 

        ); 
       optimizerIds[optimizer] = optimizers.length; 
        optimizers.push(optimizer); 

    } 

} 

Before : 

Gas :211970 
Transaction cost :184361 
Execution cost : 121765 

After converting the sizes of variables  in uint256: 
Gas :181699

Transaction cost :158033 

Execution cost :97347

 



 