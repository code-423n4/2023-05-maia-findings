## [L-01] Use more recent Solidity compiler version
The project currently uses solidity pragma version 0.8.0. Newer software versions have security updates and bug fixes, consider using atleast solidity pragma version 0.8.19.

Files: all files.

#### Recommended Mitigation Steps
Consider using atleast solidity pragma version 0.8.19

## [L-02] The `decimals()` function is optional for ERC20 specification so not all tokens may implement the `decimals()` function.

The decimals function is called in the constructor of the `UlyssesERC4626` smart contract.

```
constructor(address _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, 18) {
        asset = _asset;

        if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals(); //@audit decimals function not available on all erc20s
    }
```

File: https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/erc-4626/UlyssesERC4626.sol#L27

## [L-03] `owner` Variable shadowing in MulticallRootRouter.sol

The state variable `owner` from the Ownable contract is shadowed by the `owner` local variable of the `MulticallRootRouter._approveAndCallOut()` and `MulticallRootRouter._approveMultipleAndCallOut` functions.

Avoid variable shadowing by renaming the local variable `owner`
File: https://github.com/code-423n4/2023-05-maia/blob/cfed0dfa3bebdac0993b1b42239b4944eb0b196c/src/ulysses-omnichain/MulticallRootRouter.sol#L138
```
function _approveAndCallOut(
        address owner,
        address recipient,
        address outputToken,
        uint256 amountOut,
        uint256 depositOut,
        uint24 toChain
    ) internal virtual {
        //Approve Root Port to spend/send output hTokens.
        ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

        //Move output hTokens from Root to Branch and call 'clearToken'.
        IBridgeAgent(bridgeAgentAddress).callOutAndBridge{value: msg.value}(
            owner,
            recipient,
            "",
            outputToken,
            amountOut,
            depositOut,
            toChain //@audit shadowing owner
        );
    }
```
#### Recommended Mitigation steps
Avoid variable shadowing by renaming the local variable `owner`
