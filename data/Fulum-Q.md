#### 1) withdraw and redeem emit events even if the safeTransfer revert

## Impact
On the ```withdraw``` and ```redeem``` functions, the event ```Withdraw``` is emitted before the ```safeTransfer``` , if the function revert, the event give infos on a withdraw that never happened.

## Proof of Concept

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626.sol#L61-L77

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/erc-4626/ERC4626.sol#L80-L97

## Tools Used
Manual review

## Recommended Mitigation Steps

Use the ```emit Withdraw``` at the end of the function ```withdraw```:

```
function withdraw(uint256 assets, address receiver, address owner) public virtual returns (uint256 shares) {
        shares = previewWithdraw(assets); // No need to check for rounding error, previewWithdraw rounds up.

        if (msg.sender != owner) {
            uint256 allowed = allowance[owner][msg.sender]; // Saves gas for limited approvals.

            if (allowed != type(uint256).max) allowance[owner][msg.sender] = allowed - shares;
        }

        beforeWithdraw(assets, shares);

        _burn(owner, shares);

        address(asset).safeTransfer(receiver, assets);
		
        emit Withdraw(msg.sender, receiver, owner, assets, shares);
    }
```

Make same things with the ```redeem``` function.

#### 2) Missing event for states changes in ```BranchPort```.

## Impact
Missing event on the ```addBridgeAgent``` and ```setCoreRouter``` functions. Functions mutating storage should emit an Event for easy off-chain monitoring.

## Proof of Concept

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L290-L294

https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/ulysses-omnichain/BranchPort.sol#L301-L305

## Tools Used
Manual review

## Recommended Mitigation Steps

Declare an event in ```IBranchPort.sol``` and emit at the end of ```addBridgeAgent```:

```
function addBridgeAgent(
        address _bridgeAgent
    ) external requiresBridgeAgentFactory {
        isBridgeAgent[_bridgeAgent] = true;
        bridgeAgents.push(_bridgeAgent);
        bridgeAgentsLenght++;
		
	emit BridgeAgentAdded(_bridgeAgent);
    }
```
	
Make same things with the ```setCoreRouter``` function.