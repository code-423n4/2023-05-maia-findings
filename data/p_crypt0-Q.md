QA report

# [Low] Hermes.Sol and all other tokens can have owners initialised to zero upon deployment

The constructor for Hermes.sol and other ERC20 tokens does not check that addresses can be initialised to zero.

````
constructor(address _owner) ERC20("Hermes", "HERMES", 18) {
        _initializeOwner(_owner);
    }
````

This would mean that upon incorrect initialisation, Hermes contract owner could become the zero address and thus, actions such as minting could not occur.

# [QA]

## IbHermesUnderlying.sol
#### Missing comment in IbHermesUnderlying.sol interface for the bHermes() view.

Line 45 is missing an `@notice` comment.

Suggested addition: 

```
/**
 * @notice Returns the bHermes contract address.
 */
function bHermes() external view returns (address);
```

#### Inconsistent capitalisation for @notice comment for custom error NotbHermes()

Line 42 original spelling used a lower-case `t` for the first letter.

Remedy this for consistency by using an uppercase `T` or by using the following

`    /// @notice Error thrown when minter is not bHermes contract.
`

# [QA] Unnecessary SafeTransferLib being used for Address in ERC20hTokenRoot.sol and ERC20hTokenRootFactory.sol but SafeTransferLib functionality never used.

In these contracts, the `SafeTransferLib` Library is explictly stated as being used to extend the functionality of addresses, however, is never explictly used in the contract.


# [QA] `£` symbol instead of `@` in comment for OptimizerFactory in TalosBaseStrategyFactory.sol
Line 25, the comment reads:
`    /// £inheritdoc ITalosBaseStrategyFactory
` 

but should read:
`    /// @inheritdoc ITalosBaseStrategyFactory
`

Recommended action:
Replace the £ with an @ for inherit doc to function.

# [Informational] OptimizerFactory.sol optimizerIds start from 1 due to null TalosOptimizer being pushed to optimizer in constructor.

This is an informational notice, that optimizerIds are in-effect, 1-based rather than usual 0-based due to a null TalosOptimizer being constructed and pushed to the optimizers list.

# [QA] Erc20hTokenRootFactory.sol and ERC20hTokenBranchFactory.sol `hTokensLenght` mispelled.

 The uint representing the length of hTokens is spelt wrongly as `hTokensLenght` and should be re-named to `hTokensLength` rather than continuing the mistake..
 
 
 Example:
 
 ```
     /// @audit hTokensLength is mispelled: should be hTokensLength.
    uint256 public hTokensLenght;
	
	// should be 
	uint256 public hTokensLength;


 ```