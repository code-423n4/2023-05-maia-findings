# Summary
## Low Risk
| |Issue|Instances|
|:-:|:-|:-:|
|[[L-01]](#l-01-basev2gaugefactorygaugeid-default-value-can-point-to-different-active-gauge)|`BaseV2GaugeFactory#gaugeId` default value can point to different, active gauge|1|
|[[L-02]](#l-02-consider-adding-sweep-functionality-for-nfts-sent-to-uniswapv3staker-using-erc721transfer)|Consider adding sweep functionality for NFTs sent to `UniswapV3Staker` using `ERC721#transfer`|1|
|[[L-03]](#L-03-must-approve-0-first)|Must approve 0 first|2|
|[[L-04]](#l-04-solidity-version-is-vulnerable-to-assembly-bug)|Solidity version is vulnerable to assembly bug|20|
|[[L-05]](#l-05-consider-adding-denylist)|Consider adding denylist|9|
|[[L-06]](#l-06-missing-contract-existence-check-for-low-level-call)|Missing contract existence check for low level call|10|
|[[L-07]](#l-07-decimals-is-not-part-of-the-erc20-standard)|`decimals()` is not part of the ERC20 standard|20|
|[[L-08]](#l-08-division-before-multiplications-leads-to-precision-loss)|Division before multiplications leads to precision loss|1|
|[[L-09]](#l-09-checking-bytecode-length-is-insufficient-to-check-user-is-an-eoa)|Checking bytecode length is insufficient to check user is an EOA|2|
|[[L-10]](#l-10-empty-receivepayable-fallback-function)|Empty `receive`/`payable` `fallback` function|3|
|[[L-11]](#l-11-external-call-recipient-may-consume-all-transaction-gas)|External call recipient may consume all transaction gas|10|
|[[L-12]](#l-12-use-fixed-compiler-version)|Use fixed compiler version|58|
|[[L-13]](#l-13-initialize-can-be-frontrun)|`initialize` can be frontrun|2|
|[[L-14]](#l-14-cei-pattern-not-followed)|CEI pattern not followed|23|
|[[L-15]](#l-15-only-one-of-_mint-_safemint-implemented)|Only one of `_mint`, `_safeMint` implemented|2|
|[[L-16]](#l-16-use-safetransfer-instead-of-transfer-for-token-transfers)|Use `safeTransfer` instead of `transfer` for token transfers|2|
|[[L-17]](#l-17-setters-should-emit-events)|Setters should emit events|12|
|[[L-18]](#l-18-solidity-version-0814-is-susceptible-to-an-optimiser-bug)|Solidity version <=0.8.14 is susceptible to an optimiser bug|30|
|[[L-19]](#l-19-solidity-version-0820-may-not-work-on-other-chains-due-to-push0)|Solidity version 0.8.20 may not work on other chains due to `PUSH0`|85|
|[[L-20]](#L-20-handle-case-when-totalsupply-is-0)|Handle case when `totalSupply` is 0|2|
|[[L-21]](#L-21-downcasting-uint-or-int-may-result-in-overflow)|Downcasting `uint` or `int` may result in overflow|18|
|[[L-22]](#L-22-unused-ownable)|Unused `Ownable`|2|
|[[L-23]](#L-23-missing-address0-checks-in-constructorinitialize)|Missing `address(0)` checks in constructor/initialize|61|

Total issues: 21

Total instances: 374

&nbsp;
# Low Risk Issues
## [L-01] `BaseV2GaugeFactory#gaugeId` default value can point to different, active gauge

Push null gauge to zero index like done in [`UlyssesPool`'s constructor](https://github.com/code-423n4/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L94) to prevent nonexistent gauges from pointing to the gauge at index 1.

```solidity
    constructor(
        uint256 _id,
        address _asset,
        string memory _name,
        string memory _symbol,
        address _owner,
        address _factory
    ) UlyssesERC4626(_asset, _name, _symbol) {
        require(_owner != address(0));
        factory = UlyssesFactory(_factory);
        _initializeOwner(_owner);
        require(_id != 0);
        id = _id;

        bandwidthStateList.push(BandwidthState({bandwidth: 0, destination: UlyssesPool(address(0)), weight: 0}));
    }
```

Instances: 1

https://github.com/code-423n4/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L36

## [L-02] Consider adding sweep functionality for NFTs sent to `UniswapV3Staker` using `ERC721#transfer`

The fact that users must directly send their NFT to the `UniswapV3Staker` contract rather than calling a deposit function introduces the possibility that they use `ERC721#transfer` rather than `ERC721#safeTransfer`, which would not trigger the `onERC721Received` function and lead to the NFT being stuck in the contract. Implementing a sweep function would allow a trusted admin to return NFTs mistakenly sent in this way.

e.g.
```solidity
function sweepToken(uint256 tokenId, address receiver) external onlyOwner {
    require(nonfungiblePositionManager.ownerOf(tokenId) == address(this), "invalid tokenId");
    require(deposits[tokenId].owner == address(0), "tokenId was deposited");

    nonfungiblePositionManager.safeTransfer(receiver, tokenId);
}
```

Instances: 1

https://github.com/code-423n4/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L218-L236

## [L-03] Must approve 0 first

Some tokens (like USDT) do not work when changing the allowance from an
existing non-zero allowance value. They must first be approved by zero and then
the actual allowance must be approved.

**Recommendation:** add an approval with amount 0 before approving the desired
amount, or use `safeApprove` from OpenZeppelin's
[SafeERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol)
library.

<details><summary>Instances: 2</summary>

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

120:         ERC20hTokenRoot(outputToken).approve(bridgeAgentAddress, amountOut);

148:             ERC20hTokenRoot(outputTokens[i]).approve(bridgeAgentAddress, amountsOut[i]);

```
[L120](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L120), [L148](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L148)

</details>

&nbsp;
## [L-04] Solidity version is vulnerable to assembly bug

A bug introduced in Solidity version 0.8.13 and patched in version 0.8.17
incorrectly removes storage writes within assembly blocks. See
[here](https://blog.soliditylang.org/2022/09/08/storage-write-removal-before-conditional-termination/)
for more information.

<details><summary>Instances: 20</summary>

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

538:         assembly {
539:             chainId := chainid()
540:         }

```
[L538](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L538)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

380:         assembly {
381:             // Store bandwidth state slot in memory
382:             mstore(0x00, bandwidthStateList.slot)
383:             // Hash the bandwidth state slot to get the bandwidth state list start
384:             let bandwidthStateListStart := keccak256(0x00, 0x20)

386:             // Total difference from target bandwidth of all bandwidth states
387:             let totalDiff
388:             // Total difference from target bandwidth of all bandwidth states
389:             let transfered
390:             // Total amount to be distributed according to each bandwidth weights
391:             let transferedChange

393:             for { let i := 1 } lt(i, length) { i := add(i, 1) } {
394:                 // Load bandwidth and weight from storage
395:                 // Each bandwidth state occupies two storage slots
396:                 let slot := sload(add(bandwidthStateListStart, mul(i, 2)))
397:                 // Bandwidth is the first 248 bits of the slot
398:                 let bandwidth := and(slot, 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff)
399:                 // Weight is the last 8 bits of the slot
400:                 let weight := shr(248, slot)

402:                 // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
403:                 if mul(weight, gt(_totalSupply, div(not(0), weight))) {
404:                     // Store the function selector of `MulDivFailed()`.
405:                     mstore(0x00, 0xad251c27)
406:                     // Revert with (offset, size).
407:                     revert(0x1c, 0x04)
408:                 }

410:                 // Calculate the target bandwidth
411:                 let targetBandwidth := div(mul(_totalSupply, weight), _totalWeights)

413:                 // Calculate the difference from the target bandwidth
414:                 switch positiveTransfer
415:                 // If the transfer is positive, calculate deficit from target bandwidth
416:                 case true {
417:                     // If there is a deficit, store the difference
418:                     if gt(targetBandwidth, bandwidth) {
419:                         // Calculate the difference
420:                         let diff := sub(targetBandwidth, bandwidth)
421:                         // Add the difference to the total difference
422:                         totalDiff := add(totalDiff, diff)
423:                         // Store the difference in the diffs array
424:                         mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)
425:                     }
426:                 }
427:                 // If the transfer is negative, calculate surplus from target bandwidth
428:                 default {
429:                     // If there is a surplus, store the difference
430:                     if gt(bandwidth, targetBandwidth) {
431:                         // Calculate the difference
432:                         let diff := sub(bandwidth, targetBandwidth)
433:                         // Add the difference to the total difference
434:                         totalDiff := add(totalDiff, diff)
435:                         // Store the difference in the diffs array
436:                         mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)
437:                     }
438:                 }
439:             }

441:             // Calculate the amount to be distributed according deficit/surplus
442:             // and/or the amount to be distributed according to each bandwidth weights
443:             switch gt(amount, totalDiff)
444:             // If the amount is greater than the total deficit/surplus
445:             case true {
446:                 // Total deficit/surplus is distributed
447:                 transfered := totalDiff
448:                 // Set rest to be distributed according to each bandwidth weights
449:                 transferedChange := sub(amount, totalDiff)
450:             }
451:             // If the amount is less than the total deficit/surplus
452:             default {
453:                 // Amount will be distributed according to deficit/surplus
454:                 transfered := amount
455:             }

457:             for { let i := 1 } lt(i, length) { i := add(i, 1) } {
458:                 // Increase/decrease amount of bandwidth for each bandwidth state
459:                 let bandwidthUpdate

461:                 // If there is a deficit/surplus, calculate the amount to be distributed
462:                 if gt(transfered, 0) {
463:                     // Load the difference from the diffs array
464:                     let diff := mload(add(diffs, add(mul(i, 0x20), 0x20)))

466:                     // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
467:                     if mul(diff, gt(transfered, div(not(0), diff))) {
468:                         // Store the function selector of `MulDivFailed()`.
469:                         mstore(0x00, 0xad251c27)
470:                         // Revert with (offset, size).
471:                         revert(0x1c, 0x04)
472:                     }

474:                     // Calculate the amount to be distributed according to deficit/surplus
475:                     switch roundUp
476:                     // If round up then do mulDivUp(transfered, diff, totalDiff)
477:                     case true {
478:                         bandwidthUpdate :=
479:                             add(
480:                                 iszero(iszero(mod(mul(transfered, diff), totalDiff))), div(mul(transfered, diff), totalDiff)
481:                             )
482:                     }
483:                     // If round down then do mulDiv(transfered, diff, totalDiff)
484:                     default { bandwidthUpdate := div(mul(transfered, diff), totalDiff) }
485:                 }

487:                 // If there is a rest, calculate the amount to be distributed according to each bandwidth weights
488:                 if gt(transferedChange, 0) {
489:                     // Load weight from storage
490:                     let weight := shr(248, sload(add(bandwidthStateListStart, mul(i, 2))))

492:                     // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
493:                     if mul(weight, gt(transferedChange, div(not(0), weight))) {
494:                         // Store the function selector of `MulDivFailed()`.
495:                         mstore(0x00, 0xad251c27)
496:                         // Revert with (offset, size).
497:                         revert(0x1c, 0x04)
498:                     }

500:                     // Calculate the amount to be distributed according to each bandwidth weights
501:                     switch roundUp
502:                     // If round up then do mulDivUp(transferedChange, weight, _totalWeights)
503:                     case true {
504:                         bandwidthUpdate :=
505:                             add(
506:                                 bandwidthUpdate,
507:                                 add(
508:                                     iszero(iszero(mod(mul(transferedChange, weight), _totalWeights))),
509:                                     div(mul(transferedChange, weight), _totalWeights)
510:                                 )
511:                             )
512:                     }
513:                     // If round down then do mulDiv(transferedChange, weight, _totalWeights)
514:                     default {
515:                         bandwidthUpdate := add(bandwidthUpdate, div(mul(transferedChange, weight), _totalWeights))
516:                     }
517:                 }

519:                 // If there is an update in bandwidth
520:                 if gt(bandwidthUpdate, 0) {
521:                     // Store the amount to be updated in the bandwidthUpdateAmounts array
522:                     mstore(add(bandwidthUpdateAmounts, add(mul(i, 0x20), 0x20)), bandwidthUpdate)
523:                 }
524:             }
525:         }

553:         assembly {
554:             // Load bandwidth and weight from storage
555:             let slot := sload(destinationState.slot)
556:             // Bandwidth is the first 248 bits of the slot
557:             bandwidth := and(slot, 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff)
558:             // Weight is the last 8 bits of the slot
559:             weight := shr(248, slot)

561:             // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
562:             if mul(weight, gt(_totalSupply, div(not(0), weight))) {
563:                 // Store the function selector of `MulDivFailed()`.
564:                 mstore(0x00, 0xad251c27)
565:                 // Revert with (offset, size).
566:                 revert(0x1c, 0x04)
567:             }

569:             // Get the target bandwidth
570:             targetBandwidth := div(mul(_totalSupply, weight), _totalWeights)
571:         }

581:         assembly {
582:             switch positiveTransfer
583:             // If the transfer is positive
584:             case true {
585:                 // Add the difference to the bandwidth
586:                 bandwidth := add(bandwidth, difference)

588:                 // Revert if bandwidth overflows
589:                 if lt(bandwidth, difference) {
590:                     // Store the function selector of `Overflow()`.
591:                     mstore(0x00, 0x35278d12)
592:                     // Revert with (offset, size).
593:                     revert(0x1c, 0x04)
594:                 }
595:             }
596:             // If the transfer is negative
597:             default {
598:                 // Revert if bandwidth underflows
599:                 if gt(difference, bandwidth) {
600:                     // Store the function selector of `Underflow()`.
601:                     mstore(0x00, 0xcaccb6d9)
602:                     // Revert with (offset, size).
603:                     revert(0x1c, 0x04)
604:                 }

606:                 // Subtract the difference from the bandwidth
607:                 bandwidth := sub(bandwidth, difference)
608:             }

610:             // True on deposit, mint and redeem
611:             if gt(_newTotalSupply, 0) {
612:                 // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
613:                 if mul(weight, gt(_newTotalSupply, div(not(0), weight))) {
614:                     // Store the function selector of `MulDivFailed()`.
615:                     mstore(0x00, 0xad251c27)
616:                     // Revert with (offset, size).
617:                     revert(0x1c, 0x04)
618:                 }

620:                 // Get the new target bandwidth after total supply change
621:                 targetBandwidth := div(mul(_newTotalSupply, weight), _totalWeights)
622:             }
623:         }

633:         assembly {
634:             switch lt(newRebalancingFee, oldRebalancingFee)
635:             // If new fee is lower than old fee
636:             case true {
637:                 // Calculate the positive fee
638:                 positivefee := sub(oldRebalancingFee, newRebalancingFee)

640:                 // If depositFees is true, add the positive fee to the bandwidth
641:                 if depositFees {
642:                     bandwidth := add(bandwidth, positivefee)

644:                     // Revert if bandwidth overflows
645:                     if lt(bandwidth, positivefee) {
646:                         // Store the function selector of `Overflow()`.
647:                         mstore(0x00, 0x35278d12)
648:                         // Revert with (offset, size).
649:                         revert(0x1c, 0x04)
650:                     }
651:                 }
652:             }
653:             default {
654:                 // If new fee is higher than old fee
655:                 if gt(newRebalancingFee, oldRebalancingFee) {
656:                     // Calculate the negative fee
657:                     negativeFee := sub(newRebalancingFee, oldRebalancingFee)
658:                 }
659:             }

661:             if gt(bandwidth, 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff) {
662:                 // Store the function selector of `Overflow()`.
663:                 mstore(0x00, 0x35278d12)
664:                 // Revert with (offset, size).
665:                 revert(0x1c, 0x04)
666:             }

668:             // Update storage with the new bandwidth
669:             sstore(destinationState.slot, or(bandwidth, shl(248, weight)))
670:         }

698:         assembly {
699:             // Load the rebalancing fee slot to get the fee parameters
700:             let feeSlot := sload(fees.slot)
701:             // Get sigma2 from the first 8 bytes of the fee slot
702:             let sigma2 := shr(192, feeSlot)
703:             // Get sigma1 from the next 8 bytes of the fee slot
704:             let sigma1 := and(shr(128, feeSlot), 0xffffffffffffffff)
705:             // Get lambda2 from the next 8 bytes of the fee slot
706:             lambda2 := and(shr(64, feeSlot), 0xffffffffffffffff)
707:             // Get lambda1 from the last 8 bytes of the fee slot
708:             lambda1 := and(feeSlot, 0xffffffffffffffff)

710:             // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
711:             if mul(sigma1, gt(targetBandwidth, div(not(0), sigma1))) {
712:                 // Store the function selector of `MulDivFailed()`.
713:                 mstore(0x00, 0xad251c27)
714:                 // Revert with (offset, size).
715:                 revert(0x1c, 0x04)
716:             }

718:             // Calculate the upper bound for the first fee
719:             upperBound1 := div(mul(targetBandwidth, sigma1), DIVISIONER)

721:             // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
722:             if mul(sigma2, gt(targetBandwidth, div(not(0), sigma2))) {
723:                 // Store the function selector of `MulDivFailed()`.
724:                 mstore(0x00, 0xad251c27)
725:                 // Revert with (offset, size).
726:                 revert(0x1c, 0x04)
727:             }

729:             // Calculate the upper bound for the second fee
730:             upperBound2 := div(mul(targetBandwidth, sigma2), DIVISIONER)
731:         }

737:         assembly {
738:             // Calculate the maximum width of the trapezium
739:             maxWidth := sub(upperBound1, upperBound2)
740:         }

751:             assembly {
752:                 // offset = lambda1 * 2
753:                 lambda1 := shl(1, lambda1)
754:             }

760:             assembly {
761:                 // Add the two fees together
762:                 fee := add(fee, fee2)
763:             }

851:         assembly {
852:             // Calculate the height of the trapezium
853:             // The height is calculated as `upperBound - bandwidth`
854:             let height := sub(upperBound, bandwidth)

856:             // Equivalent to `require(y == 0 || x <= type(uint256).max / y)`.
857:             if mul(feeTier, gt(height, div(not(0), feeTier))) {
858:                 // Store the function selector of `MulDivFailed()`.
859:                 mstore(0x00, 0xad251c27)
860:                 // Revert with (offset, size).
861:                 revert(0x1c, 0x04)
862:             }

864:             // Calculate the width of the trapezium, rounded up
865:             // The width is calculated as `feeTier * height / maxWidth + offset`
866:             let width :=
867:                 add(add(iszero(iszero(mod(mul(height, feeTier), maxWidth))), div(mul(height, feeTier), maxWidth)), offset)

869:             // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
870:             if mul(height, gt(width, div(not(0), height))) {
871:                 // Store the function selector of `MulDivFailed()`.
872:                 mstore(0x00, 0xad251c27)
873:                 // Revert with (offset, size).
874:                 revert(0x1c, 0x04)
875:             }

877:             // Calculate the fee for this tier
878:             switch roundDown
879:             // If round down then do mulDiv(transfered, diff, totalDiff)
880:             case true { fee := div(mul(width, height), DIVISIONER) }
881:             // If round up then do mulDivUp(transfered, diff, totalDiff)
882:             default {
883:                 fee := add(iszero(iszero(mod(mul(width, height), DIVISIONER))), div(mul(width, height), DIVISIONER))
884:             }
885:         }

913:                 assembly {
914:                     // Add updateAmount to output
915:                     output := add(output, updateAmount)
916:                 }

948:         assembly {
949:             // Get the new total supply by adding amount to totalSupply
950:             _newTotalSupply := add(_totalSupply, amount)
951:         }

1007:         assembly {
1008:             // Revert if output underflows
1009:             if gt(negativeFee, output) {
1010:                 // Store the function selector of `Underflow()`.
1011:                 mstore(0x00, 0xcaccb6d9)
1012:                 // Revert with (offset, size).
1013:                 revert(0x1c, 0x04)
1014:             }

1016:             // Subtracting assets to deposit or shares to mint
1017:             output := sub(output, negativeFee)
1018:         }

1032:         assembly {
1033:             // Get the old total supply by adding burned shares to totalSupply
1034:             _totalSupply := add(_newTotalSupply, shares)
1035:         }

1050:                 assembly {
1051:                     // Add updateAmount to assets
1052:                     assets := add(assets, updateAmount)
1053:                 }

1062:                 assembly {
1063:                     // Update negativeFee
1064:                     negativeFee := add(negativeFee, _negativeFee)
1065:                 }

1074:         assembly {
1075:             // Revert if assets underflows
1076:             if gt(negativeFee, assets) {
1077:                 // Store the function selector of `Underflow()`.
1078:                 mstore(0x00, 0xcaccb6d9)
1079:                 // Revert with (offset, size).
1080:                 revert(0x1c, 0x04)
1081:             }

1083:             // Subtracting assets to withdraw
1084:             assets := sub(assets, negativeFee)
1085:         }

1112:         assembly {
1113:             // Get the protocol fee from storage
1114:             let _protocolFee := sload(protocolFee.slot)

1116:             // Equivalent to require(denominator != 0 && (y == 0 || x <= type(uint256).max / y))
1117:             if mul(_protocolFee, gt(assets, div(not(0), _protocolFee))) {
1118:                 // Store the function selector of `MulDivFailed()`.
1119:                 mstore(0x00, 0xad251c27)
1120:                 // Revert with (offset, size).
1121:                 revert(0x1c, 0x04)
1122:             }

1124:             // Calculate the base fee, rounding up
1125:             let baseFee :=
1126:                 add(iszero(iszero(mod(mul(assets, _protocolFee), DIVISIONER))), div(mul(assets, _protocolFee), DIVISIONER))

1128:             // Revert if assets underflows
1129:             if gt(baseFee, assets) {
1130:                 // Store the function selector of `Underflow()`.
1131:                 mstore(0x00, 0xcaccb6d9)
1132:                 // Revert with (offset, size).
1133:                 revert(0x1c, 0x04)
1134:             }

1136:             // Subtract the base fee from assets
1137:             output := sub(assets, baseFee)
1138:         }

1175:         assembly {
1176:             // Revert if output underflows
1177:             if gt(negativeFee, assets) {
1178:                 // Store the function selector of `Underflow()`.
1179:                 mstore(0x00, 0xcaccb6d9)
1180:                 // Revert with (offset, size).
1181:                 revert(0x1c, 0x04)
1182:             }

1184:             // Subtract the negative fee from assets
1185:             output := sub(assets, negativeFee)
1186:         }

```
[L380](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L380), [L386](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L386), [L393](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L393), [L402](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L402), [L410](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L410), [L413](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L413), [L441](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L441), [L457](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L457), [L461](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L461), [L466](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L466), [L474](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L474), [L487](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L487), [L492](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L492), [L500](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L500), [L519](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L519), [L553](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L553), [L561](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L561), [L569](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L569), [L581](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L581), [L588](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L588), [L606](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L606), [L610](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L610), [L620](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L620), [L633](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L633), [L640](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L640), [L644](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L644), [L661](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L661), [L668](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L668), [L698](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L698), [L710](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L710), [L718](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L718), [L721](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L721), [L729](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L729), [L737](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L737), [L751](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L751), [L760](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L760), [L851](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L851), [L856](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L856), [L864](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L864), [L869](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L869), [L877](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L877), [L913](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L913), [L948](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L948), [L1007](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1007), [L1016](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1016), [L1032](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1032), [L1050](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1050), [L1062](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1062), [L1074](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1074), [L1083](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1083), [L1112](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1112), [L1116](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1116), [L1124](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1124), [L1128](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1128), [L1136](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1136), [L1175](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1175), [L1184](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1184)

```solidity
File: src/maia/libraries/DateTimeLib.sol

43:         assembly {
44:             epochDay := add(epochDay, 719468)
45:             let doe := mod(epochDay, 146097)
46:             let yoe := div(sub(sub(add(doe, div(doe, 36524)), div(doe, 1460)), eq(doe, 146096)), 365)
47:             let doy := sub(doe, sub(add(mul(365, yoe), shr(2, yoe)), div(yoe, 100)))
48:             let mp := div(add(mul(5, doy), 2), 153)
49:             month := sub(add(mp, 3), mul(gt(mp, 9), 12))
50:         }

```
[L43](https://github.com/2023-05-maia/blob/main/src/maia/libraries/DateTimeLib.sol#L43)

</details>

&nbsp;
## [L-05] Consider adding denylist

A denylist helps to prevent malicious users from spending stolen ERC20 or
ERC721 tokens in the protocol.

<details><summary>Instances: 9</summary>

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

36:     function withdrawERC721(address _token, uint256 _tokenId) external requiresApprovedCaller {

60:     function onERC721Received(address, address, uint256, bytes calldata) external pure override returns (bytes4) {

```
[L36](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L36), [L60](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L60)

```solidity
File: src/hermes/bHermes.sol

158:     function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {

```
[L158](https://github.com/2023-05-maia/blob/main/src/hermes/bHermes.sol#L158)

```solidity
File: src/talos/TalosStrategyStaked.sol

94:     function transferFrom(address _from, address _to, uint256 _amount) public override returns (bool) {

```
[L94](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L94)

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

279:     function transferFrom(address from, address to, uint256 amount)
280:         public
281:         virtual
282:         override
283:         checkTransfer(from, amount)
284:         returns (bool)
285:     {

```
[L279](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L279)

```solidity
File: src/erc-20/ERC20Boost.sol

323:     function transferFrom(address from, address to, uint256 amount)
324:         public
325:         override
326:         notAttached(from, amount)
327:         returns (bool)
328:     {

```
[L323](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L323)

```solidity
File: src/erc-20/ERC20MultiVotes.sol

303:     function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {

```
[L303](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L303)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

328:     function onERC721Received(address, address, uint256, bytes calldata) external pure override returns (bytes4) {

```
[L328](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L328)

```solidity
File: src/erc-20/ERC20Gauges.sol

508:     function transferFrom(address from, address to, uint256 amount) public virtual override returns (bool) {

```
[L508](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L508)

</details>

&nbsp;
## [L-06] Missing contract existence check for low level call

Low-level calls return success if there is no code present at the specified
address. Add a check to verify that `target.code.size > 0` (or in assembly,
use `extcodesize`).

<details><summary>Instances: 10</summary>

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

49:             (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

```
[L49](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L49)

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

281:             IVirtualAccount(userAccount).call(calls);

289:             IVirtualAccount(userAccount).call(calls);

309:             IVirtualAccount(userAccount).call(calls);

357:             IVirtualAccount(userAccount).call(calls);

365:             IVirtualAccount(userAccount).call(calls);

385:             IVirtualAccount(userAccount).call(calls);

433:             IVirtualAccount(userAccount).call(calls);

441:             IVirtualAccount(userAccount).call(calls);

461:             IVirtualAccount(userAccount).call(calls);

```
[L281](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L281), [L289](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L289), [L309](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L309), [L357](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L357), [L365](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L365), [L385](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L385), [L433](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L433), [L441](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L441), [L461](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L461)

</details>

&nbsp;
## [L-07] `decimals()` is not part of the ERC20 standard

The decimals() function is not a part of the
[ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was addedlater as an
[optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol).
As such, some valid ERC20 tokens do not support this interface, so it is unsafe to
blindly cast all tokens to this interface, and then call this function.

<details><summary>Instances: 20</summary>

```solidity
File: src/ulysses-amm/UlyssesToken.sol

46:         require(ERC20(asset).decimals() == 18);

```
[L46](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L46)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

72:         underlyingAddress.safeTransfer(_recipient, _denormalizeDecimals(_deposit, ERC20(underlyingAddress).decimals()));

81:         _underlyingAddress.safeTransfer(
82:             _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
83:         );

119:             _underlyingAddress.safeTransferFrom(
120:                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
121:             );

138:                 _underlyingAddresses[i].safeTransferFrom(
139:                     _depositor,
140:                     address(this),
141:                     _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
142:                 );

```
[L72](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L72), [L81](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L81), [L119](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L119), [L138](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L138)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

103:         IArbPort(localPortAddress).depositToPort(
104:             msg.sender, msg.sender, underlyingAddress, _normalizeDecimals(amount, ERC20(underlyingAddress).decimals())
105:         );

```
[L103](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L103)

```solidity
File: src/ulysses-omnichain/BranchPort.sol

211:         _underlyingAddress.safeTransfer(
212:             _recipient, _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
213:         );

253:             _underlyingAddress.safeTransferFrom(
254:                 _depositor, address(this), _denormalizeDecimals(_deposit, ERC20(_underlyingAddress).decimals())
255:             );

269:                 _underlyingAddresses[i].safeTransferFrom(
270:                     _depositor,
271:                     address(this),
272:                     _denormalizeDecimals(_deposits[i], ERC20(_underlyingAddresses[i]).decimals())
273:                 );

```
[L211](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L211), [L253](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L253), [L269](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L269)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

245:         bytes memory packedData = abi.encodePacked(
246:             bytes1(0x05),
247:             msg.sender,
248:             depositNonce,
249:             _dParams.hToken,
250:             _dParams.token,
251:             _dParams.amount,
252:             _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
253:             _dParams.toChain,
254:             _params,
255:             msg.value.toUint128(),
256:             _remoteExecutionGas
257:         );

284:             _deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

334:                 packedData = abi.encodePacked(
335:                     bytes1(0x05),
336:                     msg.sender,
337:                     _depositNonce,
338:                     getDeposit[_depositNonce].hTokens[0],
339:                     getDeposit[_depositNonce].tokens[0],
340:                     getDeposit[_depositNonce].amounts[0],
341:                     _normalizeDecimals(
342:                         getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
343:                     ),
344:                     _toChain,
345:                     _params,
346:                     msg.value.toUint128(),
347:                     _remoteExecutionGas
348:                 );

350:                 packedData = abi.encodePacked(
351:                     bytes1(0x02),
352:                     _depositNonce,
353:                     getDeposit[_depositNonce].hTokens[0],
354:                     getDeposit[_depositNonce].tokens[0],
355:                     getDeposit[_depositNonce].amounts[0],
356:                     _normalizeDecimals(
357:                         getDeposit[_depositNonce].deposits[0], ERC20(getDeposit[_depositNonce].tokens[0]).decimals()
358:                     ),
359:                     _toChain,
360:                     _params,
361:                     msg.value.toUint128(),
362:                     _remoteExecutionGas
363:                 );

681:         bytes memory packedData = abi.encodePacked(
682:             bytes1(0x02),
683:             depositNonce,
684:             _dParams.hToken,
685:             _dParams.token,
686:             _dParams.amount,
687:             _normalizeDecimals(_dParams.deposit, ERC20(_dParams.token).decimals()),
688:             _dParams.toChain,
689:             _params,
690:             _gasToBridgeOut,
691:             _remoteExecutionGas
692:         );

720:             deposits[i] = _normalizeDecimals(_dParams.deposits[i], ERC20(_dParams.tokens[i]).decimals());

1355:             deposits[i] = _normalizeDecimals(_deposits[i], ERC20(_tokens[i]).decimals());

```
[L245](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L245), [L284](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L284), [L334](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L334), [L350](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L350), [L681](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L681), [L720](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L720), [L1355](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1355)

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```
[L23](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L23)

```solidity
File: src/erc-4626/UlyssesERC4626.sol

27:         if (ERC20(_asset).decimals() != 18) revert InvalidAssetDecimals();

```
[L27](https://github.com/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L27)

```solidity
File: src/erc-4626/ERC4626.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```
[L23](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L23)

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

51:             require(ERC20(_assets[i]).decimals() == 18);

```
[L51](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L51)

</details>

&nbsp;
## [L-08] Division before multiplications leads to precision loss

Since Solidity cannot deal with decimal values, make sure to use multiplication before division to avoid precision loss due to rounding errors.

<details><summary>Instances: 1</summary>

```solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

34:             boostedSecondsInsideX128 = uint160(
35:                 ((secondsInsideX128 * 4) / 10) + ((((stakedDuration << 128) * boostAmount) / boostTotalSupply) * 6) / 10
36:             );

```
[L34](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/RewardMath.sol#L34)

</details>

&nbsp;
## [L-09] Checking bytecode length is insufficient to check user is an EOA

The protocol implements an EOA check by ensuring that the bytecode length at
the address is equal to 0. However this can be gamed since during a smart
contracts `constructor`, its bytecode is technically empty. Therefore the
check can be passed by a smart contract. While unlikely, this edge case is
still worth consideration.

<details><summary>Instances: 2</summary>

```solidity
File: src/erc-20/ERC20MultiVotes.sol

105:         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts

```
[L105](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L105)

```solidity
File: src/erc-20/ERC20Gauges.sol

464:         if (canExceedMax && account.code.length == 0) revert Errors.NonContractError(); // can only approve contracts

```
[L464](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L464)

</details>

&nbsp;
## [L-10] Empty `receive`/`payable` `fallback` function

If the intention is for the ether to be used, the function should call
another function or emit an event, otherwise it should revert (e.g.
`require(msg.sender == address(weth))`), otherwise users might send ether
to the contract mistakenly and lose funds.

<details><summary>Instances: 3</summary>

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

284:     fallback() external payable {}

```
[L284](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L284)

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

1334:     fallback() external payable {}

```
[L1334](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1334)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

1419:     fallback() external payable {}

```
[L1419](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1419)

</details>

&nbsp;
## [L-11] External call recipient may consume all transaction gas

There is no limit specified on the amount of gas used, so the recipient can
use up all of the transaction's gas, causing it to revert. Use
`addr.call{gas: <amount>}("")` or 
[this](https://github.com/nomad-xyz/ExcessivelySafeCall) library instead.

<details><summary>Instances: 10</summary>

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

49:             (bool success, bytes memory data) = calls[i].target.call(calls[i].callData);

```
[L49](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L49)

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

281:             IVirtualAccount(userAccount).call(calls);

289:             IVirtualAccount(userAccount).call(calls);

309:             IVirtualAccount(userAccount).call(calls);

357:             IVirtualAccount(userAccount).call(calls);

365:             IVirtualAccount(userAccount).call(calls);

385:             IVirtualAccount(userAccount).call(calls);

433:             IVirtualAccount(userAccount).call(calls);

441:             IVirtualAccount(userAccount).call(calls);

461:             IVirtualAccount(userAccount).call(calls);

```
[L281](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L281), [L289](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L289), [L309](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L309), [L357](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L357), [L365](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L365), [L385](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L385), [L433](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L433), [L441](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L441), [L461](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L461)

</details>

&nbsp;
## [L-12] Use fixed compiler version

A floating pragma should not be used for non-interface contracts as it risks a
different compiler version being used in production vs testing, which may lead
to unintended and unexpected discrepencies between the behaviour of the protocol
during testing and during production.

<details><summary>Instances: 58</summary>

```solidity
File: src/hermes/tokens/HERMES.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/HERMES.sol#L2)

```solidity
File: src/maia/tokens/Maia.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/tokens/Maia.sol#L2)

```solidity
File: src/rewards/booster/FlywheelBoosterGaugeWeight.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3)

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L2)

```solidity
File: src/hermes/tokens/bHermesBoost.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L2)

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelBribeRewards.sol#L3)

```solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelInstantRewards.sol#L3)

```solidity
File: src/rewards/FlywheelCoreInstant.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol#L2)

```solidity
File: src/rewards/FlywheelCoreStrategy.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol#L2)

```solidity
File: src/hermes/tokens/bHermesGauges.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L2)

```solidity
File: src/hermes/tokens/bHermesVotes.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol#L2)

```solidity
File: src/talos/factories/TalosStrategyVanillaFactory.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L3)

```solidity
File: src/talos/factories/BoostAggregatorFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/BoostAggregatorFactory.sol#L2)

```solidity
File: src/talos/factories/OptimizerFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/OptimizerFactory.sol#L2)

```solidity
File: src/gauges/UniswapV3Gauge.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L2)

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L2)

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2)

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L3)

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2)

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol#L2)

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2)

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L2)

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2)

```solidity
File: src/talos/TalosOptimizer.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L3)

```solidity
File: src/governance/GovernorBravoDelegator.sol

2: pragma solidity ^0.8.10;

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L2)

```solidity
File: src/maia/vMaia.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/maia/vMaia.sol#L3)

```solidity
File: src/gauges/factories/BribesFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L2)

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L3)

```solidity
File: src/talos/TalosManager.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/TalosManager.sol#L2)

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2)

```solidity
File: src/hermes/bHermes.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/bHermes.sol#L2)

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2)

```solidity
File: src/ulysses-amm/UlyssesToken.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L2)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2)

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L3)

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2)

```solidity
File: src/hermes/minters/BaseV2Minter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L2)

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L2)

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L2)

```solidity
File: src/talos/TalosStrategyVanilla.sol

3: pragma solidity >=0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L3)

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L2)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2)

```solidity
File: src/talos/TalosStrategyStaked.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L2)

```solidity
File: src/governance/GovernorBravoInterfaces.sol

2: pragma solidity ^0.8.10;

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L2)

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L3)

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/BranchPort.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L3)

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2)

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/RootPort.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L3)

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L3)

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

2: pragma solidity ^0.8.10;

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L2)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L2)

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L2)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L2)

</details>

&nbsp;
## [L-13] `initialize` can be frontrun

Lack of access control on `initialize` function means that it can be frontrun,
allowing a malicious user to steal ownership of the contract and necessitating
an expensive re-deployment.

<details><summary>Instances: 2</summary>

```solidity
File: src/hermes/minters/BaseV2Minter.sol

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```
[L78](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L78)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

102:     function init(uint256 amount0Desired, uint256 amount1Desired, address receiver)
103:         external
104:         virtual
105:         nonReentrant
106:         returns (uint256 shares, uint256 amount0, uint256 amount1)
107:     {

```
[L102](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L102)

</details>

&nbsp;
## [L-14] CEI pattern not followed

The
[Checks-Effects-Interactions](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html)
pattern is a best practice that reduces the attack surface for reentrancy
attacks.

Place state variable updates before external calls within functions.

<details><summary>Instances: 23</summary>

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

159:     function withdrawProtocolFees(address to) external onlyOwner {
160:         uniswapV3Staker.claimReward(to, protocolRewards);
161:         delete protocolRewards;

```
[L159](https://github.com/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L159)

```solidity
File: src/talos/TalosStrategyStaked.sol

164:     function _unstake(uint256 _tokenId) internal {

166:         boostAggregator.unstakeAndWithdraw(_tokenId);

168:         stakeFlag = false;

173:     function _stake(uint256 _tokenId) internal {

177:         try nonfungiblePositionManager.safeTransferFrom(address(this), address(boostAggregator), _tokenId) {
178:             stakeFlag = true; // flag to store staking state to avoid failing to unstake when it is not staked

```
[L164](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L164), [L166](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L166), [L168](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L168), [L173](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L173), [L177](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L177)

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

72:     function queueRewardsForCycle() external returns (uint256 totalQueuedForCycle) {

76:         minter.updatePeriod();

85:         gaugeCycle = currentCycle;

100:         nextCycleQueuedRewards = 0;
101:         paginationOffset = 0;

107:     function queueRewardsForCyclePaginated(uint256 numRewards) external {

111:         minter.updatePeriod();

121:             nextCycle = currentCycle;
122:             paginationOffset = 0;

144:             gaugeCycle = currentCycle;
145:             nextCycleQueuedRewards = 0;
146:             paginationOffset = 0;

149:             paginationOffset = offset + numRewards.toUint32();

```
[L72](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L72), [L76](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L76), [L85](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L85), [L100](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L100), [L107](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L107), [L111](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L111), [L121](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L121), [L144](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L144), [L149](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L149)

```solidity
File: src/ulysses-omnichain/RootPort.sol

140:     function initializeCore(
141:         address _coreRootBridgeAgent,
142:         address _coreLocalBranchBridgeAgent,
143:         address _localBranchPortAddress
144:     ) external onlyOwner {

153:         IBridgeAgent(_coreRootBridgeAgent).syncBranchBridgeAgent(_coreLocalBranchBridgeAgent, localChainId);
154:         getBridgeAgentManager[_coreRootBridgeAgent] = owner();

420:     function addNewChain(
421:         address _pledger,
422:         uint256 _pledgedInitialAmount,
423:         address _coreBranchBridgeAgentAddress,
424:         uint24 _chainId,
425:         string memory _wrappedGasTokenName,
426:         string memory _wrappedGasTokenSymbol,
427:         uint24 _fee,
428:         uint24 _priceImpactPercentage,
429:         uint160 _sqrtPriceX96,
430:         address _nonFungiblePositionManagerAddress,
431:         address _newLocalBranchWrappedNativeTokenAddress,
432:         address _newUnderlyingBranchWrappedNativeTokenAddress
433:     ) external onlyOwner {

440:         ERC20hTokenRoot(newGlobalToken).mint(_pledger, _pledgedInitialAmount, _chainId);

442:         IBridgeAgent(ICoreRootRouter(coreRootRouterAddress).bridgeAgentAddress()).syncBranchBridgeAgent(
443:             _coreBranchBridgeAgentAddress, _chainId
444:         );

446:         getWrappedNativeToken[_chainId] = _newUnderlyingBranchWrappedNativeTokenAddress;
447:         isChainId[_chainId] = true;
448:         isGlobalAddress[newGlobalToken] = true;
449:         getGlobalTokenFromLocal[_newLocalBranchWrappedNativeTokenAddress][_chainId] = newGlobalToken;
450:         getLocalTokenFromGlobal[newGlobalToken][_chainId] = _newLocalBranchWrappedNativeTokenAddress;
451:         getLocalTokenFromUnder[_newUnderlyingBranchWrappedNativeTokenAddress][_chainId] =
452:             _newLocalBranchWrappedNativeTokenAddress;
453:         getUnderlyingTokenFromLocal[_newLocalBranchWrappedNativeTokenAddress][_chainId] =
454:             _newUnderlyingBranchWrappedNativeTokenAddress;

473:         getGasPoolInfo[chainId] = GasPoolInfo({
474:             zeroForOneOnInflow: zeroForOneOnInflow,
475:             priceImpactPercentage: _priceImpactPercentage,
476:             gasTokenGlobalAddress: newGlobalToken,
477:             poolAddress: newGasPoolAddress
478:         });

```
[L140](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L140), [L153](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L153), [L420](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L420), [L440](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L440), [L442](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L442), [L446](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L446), [L473](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L473)

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

476:     function _stakeToken(uint256 tokenId, IUniswapV3Pool pool, int24 tickLower, int24 tickUpper, uint128 liquidity)
477:         private
478:     {

496:         if (!hermesGaugeBoost.isUserGauge(tokenOwner, address(gauge))) {
497:             _userAttachements[tokenOwner][pool] = tokenId;

507:             _stakes[tokenId][incentiveId] = Stake({
508:                 secondsPerLiquidityInsideInitialX128: secondsPerLiquidityInsideX128,
509:                 liquidityNoOverflow: type(uint96).max,
510:                 liquidityIfOverflow: liquidity
511:             });

```
[L476](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L476), [L496](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L496), [L507](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L507)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

159:     function addNewBandwidth(uint256 poolId, uint8 weight) external nonReentrant onlyOwner returns (uint256 index) {

202:         bandwidthStateList.push(
203:             BandwidthState({bandwidth: newBandwidth.toUint248(), destination: destination, weight: weight})
204:         );

206:         destinations[destinationId] = index;
207:         destinationIds[address(destination)] = index;

```
[L159](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L159), [L202](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L202), [L206](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L206)

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

860:     function anyExecute(bytes calldata data)
861:         external
862:         virtual
863:         requiresExecutor
864:         returns (bool success, bytes memory result)
865:     {

926:             try RootBridgeAgentExecutor(bridgeAgentExecutorAddress).executeSystemRequest(
927:                 localRouterAddress, data, fromChainId
928:             ) returns (bool, bytes memory res) {

936:             executionHistory[fromChainId][nonce] = true;

961:             executionHistory[fromChainId][nonce] = true;

985:             executionHistory[fromChainId][nonce] = true;

1009:             executionHistory[fromChainId][nonce] = true;

1045:             executionHistory[fromChainId][nonce] = true;

1080:             executionHistory[fromChainId][nonce] = true;

1115:             executionHistory[fromChainId][nonce] = true;

1138:             executionHistory[fromChainId][nonce] = true;

1148:                 executionHistory[fromChainId][nonce] = true;

```
[L860](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L860), [L926](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L926), [L936](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L936), [L961](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L961), [L985](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L985), [L1009](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1009), [L1045](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1045), [L1080](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1080), [L1115](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1115), [L1138](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1138), [L1148](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L1148)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

857:     function _createDepositSingle(
858:         address _user,
859:         address _hToken,
860:         address _token,
861:         uint256 _amount,
862:         uint256 _deposit,
863:         uint128 _gasToBridgeOut
864:     ) internal {

866:         IPort(localPortAddress).bridgeOut(_user, _hToken, _token, _amount, _deposit);

882:         getDeposit[_getAndIncrementDepositNonce()] = Deposit({
883:             owner: _user,
884:             hTokens: hTokens,
885:             tokens: tokens,
886:             amounts: amounts,
887:             deposits: deposits,
888:             status: DepositStatus.Success,
889:             depositedGas: _gasToBridgeOut
890:         });

903:     function _createDepositMultiple(
904:         address _user,
905:         address[] memory _hTokens,
906:         address[] memory _tokens,
907:         uint256[] memory _amounts,
908:         uint256[] memory _deposits,
909:         uint128 _gasToBridgeOut
910:     ) internal {

912:         IPort(localPortAddress).bridgeOutMultiple(_user, _hTokens, _tokens, _amounts, _deposits);

918:         getDeposit[_getAndIncrementDepositNonce()] = Deposit({
919:             owner: _user,
920:             hTokens: _hTokens,
921:             tokens: _tokens,
922:             amounts: _amounts,
923:             deposits: _deposits,
924:             status: DepositStatus.Success,
925:             depositedGas: _gasToBridgeOut
926:         });

1118:     function anyExecute(bytes calldata data)
1119:         external
1120:         virtual
1121:         requiresExecutor
1122:         returns (bool success, bytes memory result)
1123:     {

1154:             try BranchBridgeAgentExecutor(bridgeAgentExecutorAddress).executeNoSettlement(localRouterAddress, data)
1155:             returns (bool, bytes memory res) {

1162:             executionHistory[nonce] = true;

1186:             executionHistory[nonce] = true;

1210:             executionHistory[nonce] = true;

```
[L857](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L857), [L866](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L866), [L882](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L882), [L903](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L903), [L912](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L912), [L918](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L918), [L1118](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1118), [L1154](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1154), [L1162](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1162), [L1186](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1186), [L1210](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L1210)

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

55:     function createTalosBaseStrategy(
56:         IUniswapV3Pool pool,
57:         ITalosOptimizer optimizer,
58:         address strategyManager,
59:         bytes memory data
60:     ) external {
61:         if (optimizerFactory.optimizerIds(TalosOptimizer(address(optimizer))) == 0) {

67:         strategyIds[strategy] = strategies.length;

```
[L55](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L55), [L67](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L67)

```solidity
File: src/gauges/BaseV2Gauge.sol

128:     function addBribeFlywheel(FlywheelCore bribeFlywheel) external onlyOwner {

133:         FlywheelBribeRewards(flyWheelRewards).setRewardsDepot(multiRewardsDepot);

135:         multiRewardsDepot.addAsset(flyWheelRewards, bribeFlywheel.rewardToken()); // @audit not removed ever ?

137:         isActive[bribeFlywheel] = true;
138:         added[bribeFlywheel] = true;

```
[L128](https://github.com/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L128), [L133](https://github.com/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L133), [L135](https://github.com/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L135), [L137](https://github.com/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L137)

```solidity
File: src/rewards/base/FlywheelCore.sol

125:     function setFlywheelRewards(address newFlywheelRewards) external onlyOwner {

128:             rewardToken.safeTransferFrom(address(flywheelRewards), address(newFlywheelRewards), oldRewardBalance);

131:         flywheelRewards = newFlywheelRewards;

```
[L125](https://github.com/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L125), [L128](https://github.com/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L128), [L131](https://github.com/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L131)

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

216:     function increaseConversionRate(uint256 newRate) external onlyOwner {

219:         if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) {

223:         bHermesRate = newRate;

```
[L216](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L216), [L219](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L219), [L223](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L223)

```solidity
File: src/erc-20/ERC20Boost.sol

116:     function attach(address user) external {
117:         if (!_gauges.contains(msg.sender) || _deprecatedGauges.contains(msg.sender)) {

127:             getUserBoost[user] = userGaugeBoost;

150:     function updateUserBoost(address user) external {

159:             if (!_deprecatedGauges.contains(gauge)) {

169:         getUserBoost[user] = userBoost;

```
[L116](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L116), [L127](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L127), [L150](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L150), [L159](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L159), [L169](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L169)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

102:     function init(uint256 amount0Desired, uint256 amount1Desired, address receiver)
103:         external
104:         virtual
105:         nonReentrant
106:         returns (uint256 shares, uint256 amount0, uint256 amount1)
107:     {

119:             PoolVariables.checkRange(_tickLower, _tickUpper); // Check ticks also for overflow/underflow
120:             tickLower = _tickLower;
121:             tickUpper = _tickUpper;

154:         liquidity = _liquidity;
155:         tokenId = _tokenId;

163:         initialized = true;

349:     function _withdrawAll(uint256 _tokenId) internal {

353:         _nonfungiblePositionManager.decreaseLiquidity(
354:             INonfungiblePositionManager.DecreaseLiquidityParams({
355:                 tokenId: _tokenId,
356:                 liquidity: _liquidity,
357:                 amount0Min: 0,
358:                 amount1Min: 0,
359:                 deadline: block.timestamp
360:             })
361:         );
362:         liquidity = 0;

394:     function collectProtocolFees(uint256 amount0, uint256 amount1) external nonReentrant onlyOwner {

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);
410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

412:         protocolFees0 = _protocolFees0 - amount0;
413:         protocolFees1 = _protocolFees1 - amount1;

```
[L102](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L102), [L119](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L119), [L154](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L154), [L163](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L163), [L349](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L349), [L353](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L353), [L394](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L394), [L409](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409), [L412](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L412)

```solidity
File: src/erc-20/ERC20Gauges.sol

292:     function _decrementGaugeWeight(address user, address gauge, uint112 weight, uint32 cycle) internal {

297:         IBaseV2Gauge(gauge).accrueBribes(user);

299:         getUserGaugeWeight[user][gauge] = oldWeight - weight;

```
[L292](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L292), [L297](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L297), [L299](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L299)

</details>

&nbsp;
## [L-15] Only one of `_mint`, `_safeMint` implemented

If one of the functions is re-implemented, or has new arguments, the other
should be as well. The `_mint` variant is supposed to skip
`onERC721Received` checks, whereas `_safeMint` does not. Not having both
points to a possible issue with spec-compatability.

<details><summary>Instances: 2</summary>

```solidity
File: src/hermes/bHermes.sol

128:     function _mint(address to, uint256 amount) internal virtual override {

```
[L128](https://github.com/2023-05-maia/blob/main/src/hermes/bHermes.sol#L128)

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

240:     function _mint(address to, uint256 amount) internal virtual override {

```
[L240](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L240)

</details>

&nbsp;
## [L-16] Use `safeTransfer` instead of `transfer` for token transfers

`SafeERC20` is a wrapper around ERC20 operations that throw on failure (when the token contract returns false). This prevents calls executing if a token transfer is unsuccessful. Simply add `using SafeERC20 for ERC20`.

<details><summary>Instances: 2</summary>

```solidity
File: src/talos/base/TalosBaseStrategy.sol

409:         if (amount0 > 0) _token0.transfer(msg.sender, amount0);

410:         if (amount1 > 0) _token1.transfer(msg.sender, amount1);

```
[L409](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L409), [L410](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L410)

</details>

&nbsp;
## [L-17] Setters should emit events

Permissioned setter functions without a timelock should emit events so that
users can react to critical protocol changes.

<details><summary>Instances: 12</summary>

```solidity
File: src/talos/TalosOptimizer.sol

62:     function setMaxTotalSupply(uint256 _maxTotalSupply) external onlyOwner {

68:     function setTwapDuration(uint32 _twapDuration) external onlyOwner {

74:     function setMaxTwapDeviation(int24 _maxTwapDeviation) external onlyOwner {

80:     function setTickRange(int24 _tickRangeMultiplier) external onlyOwner {

85:     function setPriceImpact(uint24 _priceImpactPercentage) external onlyOwner {

```
[L62](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L62), [L68](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L68), [L74](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L74), [L80](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L80), [L85](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L85)

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

98:     function setMinimumWidth(address gauge, uint24 minimumWidth) external onlyOwner {

```
[L98](https://github.com/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L98)

```solidity
File: src/hermes/minters/BaseV2Minter.sol

86:     function setDao(address _dao) external onlyOwner {

92:     function setDaoShare(uint256 _daoShare) external onlyOwner {

98:     function setTailEmission(uint256 _tail_emission) external onlyOwner {

```
[L86](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L86), [L92](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L92), [L98](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L98)

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

153:     function setProtocolFee(uint256 _protocolFee) external onlyOwner {

```
[L153](https://github.com/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L153)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

223:     function setWeight(uint256 poolId, uint8 weight) external nonReentrant onlyOwner {

308:     function setFees(Fees calldata _fees) external nonReentrant onlyOwner {

```
[L223](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L223), [L308](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L308)

</details>

&nbsp;
## [L-18] Solidity version <=0.8.14 is susceptible to an optimiser bug

In solidity versions 0.8.13 and 0.8.14, there is an 
[optimizer bug](https://github.com/ethereum/solidity-blog/blob/499ab8abc19391be7b7b34f88953a067029a5b45/_posts/2022-06-15-inline-assembly-memory-side-effects-bug.md)
where, if the use of a variable is in a separate `assembly` block from the
block in which it was stored, the `mstore` operation is optimized out, leading
to uninitialized memory. The code currently does not have such a pattern of
execution, but it does use `mstore`s in `assembly` blocks, so it is a risk for
future changes. The affected solidity versions should be avoided if at all
possible.

See
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d
for more details.

<details><summary>Instances: 30</summary>

```solidity
File: src/governance/GovernorBravoDelegator.sol

2: pragma solidity ^0.8.10;

76:             let free_mem_ptr := mload(0x40)
77:             returndatacopy(free_mem_ptr, 0, returndatasize())

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L2), [L76](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L76)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

2: pragma solidity ^0.8.0;

360:                 mstore(0x00, 0x87138d5c)

368:                 mstore(0x00, 0xc2f5625a)

382:             mstore(0x00, bandwidthStateList.slot)

405:                     mstore(0x00, 0xad251c27)

424:                         mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)

436:                         mstore(add(diffs, add(mul(i, 0x20), 0x20)), diff)

464:                     let diff := mload(add(diffs, add(mul(i, 0x20), 0x20)))

469:                         mstore(0x00, 0xad251c27)

495:                         mstore(0x00, 0xad251c27)

522:                     mstore(add(bandwidthUpdateAmounts, add(mul(i, 0x20), 0x20)), bandwidthUpdate)

564:                 mstore(0x00, 0xad251c27)

591:                     mstore(0x00, 0x35278d12)

601:                     mstore(0x00, 0xcaccb6d9)

615:                     mstore(0x00, 0xad251c27)

647:                         mstore(0x00, 0x35278d12)

663:                 mstore(0x00, 0x35278d12)

713:                 mstore(0x00, 0xad251c27)

724:                 mstore(0x00, 0xad251c27)

859:                 mstore(0x00, 0xad251c27)

872:                 mstore(0x00, 0xad251c27)

1011:                 mstore(0x00, 0xcaccb6d9)

1078:                 mstore(0x00, 0xcaccb6d9)

1102:                 mstore(0x00, 0x3c930918)

1119:                 mstore(0x00, 0xad251c27)

1131:                 mstore(0x00, 0xcaccb6d9)

1156:                 mstore(0x00, 0x3c930918)

1163:                 mstore(0x00, 0xc2f5625a)

1179:                 mstore(0x00, 0xcaccb6d9)

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L2), [L360](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L360), [L368](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L368), [L382](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L382), [L405](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L405), [L424](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L424), [L436](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L436), [L464](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L464), [L469](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L469), [L495](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L495), [L522](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L522), [L564](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L564), [L591](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L591), [L601](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L601), [L615](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L615), [L647](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L647), [L663](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L663), [L713](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L713), [L724](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L724), [L859](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L859), [L872](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L872), [L1011](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1011), [L1078](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1078), [L1102](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1102), [L1119](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1119), [L1131](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1131), [L1156](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1156), [L1163](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1163), [L1179](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L1179)

</details>

&nbsp;
## [L-19] Solidity version 0.8.20 may not work on other chains due to `PUSH0`

The compiler for Solidity 0.8.20 switches the default target EVM version to
[Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note),
which includes the new PUSH0 op code. This op code may not yet be implemented
on all L2s, so deployment on these chains will fail. See this relevant
[issue](https://github.com/ethereum/solidity/issues/14254) on the official
Solidity github for reference. To work around this issue, use an earlier EVM
version.

<details><summary>Instances: 85</summary>

```solidity
File: src/hermes/tokens/HERMES.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/HERMES.sol#L2)

```solidity
File: src/maia/tokens/Maia.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/tokens/Maia.sol#L2)

```solidity
File: src/rewards/booster/FlywheelBoosterGaugeWeight.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L3)

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L2)

```solidity
File: src/hermes/tokens/bHermesBoost.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L2)

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelBribeRewards.sol#L3)

```solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelInstantRewards.sol#L3)

```solidity
File: src/rewards/FlywheelCoreInstant.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol#L2)

```solidity
File: src/rewards/FlywheelCoreStrategy.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol#L2)

```solidity
File: src/hermes/tokens/bHermesGauges.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L2)

```solidity
File: src/hermes/tokens/bHermesVotes.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol#L2)

```solidity
File: src/talos/factories/TalosStrategyVanillaFactory.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L3)

```solidity
File: src/talos/factories/BoostAggregatorFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/BoostAggregatorFactory.sol#L2)

```solidity
File: src/talos/factories/OptimizerFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/OptimizerFactory.sol#L2)

```solidity
File: src/gauges/UniswapV3Gauge.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L2)

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L2)

```solidity
File: src/ulysses-omnichain/token/ERC20hTokenRoot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/token/ERC20hTokenRoot.sol#L2)

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L3)

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenRootFactory.sol#L2)

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol#L2)

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L2)

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L2)

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L2)

```solidity
File: src/talos/TalosOptimizer.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L3)

```solidity
File: src/governance/GovernorBravoDelegator.sol

2: pragma solidity ^0.8.10;

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L2)

```solidity
File: src/maia/vMaia.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/maia/vMaia.sol#L3)

```solidity
File: src/gauges/factories/BribesFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L2)

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L3)

```solidity
File: src/talos/TalosManager.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/TalosManager.sol#L2)

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L2)

```solidity
File: src/hermes/bHermes.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/bHermes.sol#L2)

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L2)

```solidity
File: src/ulysses-amm/UlyssesToken.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L2)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgentExecutor.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgentExecutor.sol#L2)

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L3)

```solidity
File: src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/BranchBridgeAgentFactory.sol#L2)

```solidity
File: src/hermes/minters/BaseV2Minter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L2)

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L2)

```solidity
File: src/ulysses-omnichain/BaseBranchRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BaseBranchRouter.sol#L2)

```solidity
File: src/talos/TalosStrategyVanilla.sol

3: pragma solidity >=0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L3)

```solidity
File: src/ulysses-amm/factories/UlyssesFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/factories/UlyssesFactory.sol#L2)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L3)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L2)

```solidity
File: src/talos/TalosStrategyStaked.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L2)

```solidity
File: src/governance/GovernorBravoInterfaces.sol

2: pragma solidity ^0.8.10;

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoInterfaces.sol#L2)

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L3)

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/BranchPort.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchPort.sol#L3)

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L2)

```solidity
File: src/ulysses-omnichain/MulticallRootRouter.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/MulticallRootRouter.sol#L2)

```solidity
File: src/ulysses-omnichain/RootPort.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootPort.sol#L3)

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L3)

```solidity
File: src/governance/GovernorBravoDelegateMaia.sol

2: pragma solidity ^0.8.10;

```
[L2](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegateMaia.sol#L2)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L2)

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L2)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L2)

```solidity
File: src/rewards/depots/RewardsDepot.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/rewards/depots/RewardsDepot.sol#L2)

```solidity
File: src/rewards/base/BaseFlywheelRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L3)

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L3)

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/strategies/TalosStrategySimple.sol#L2)

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L2)

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L2)

```solidity
File: src/erc-4626/UlyssesERC4626.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L2)

```solidity
File: src/gauges/BaseV2Gauge.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L2)

```solidity
File: src/hermes/UtilityManager.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L3)

```solidity
File: src/erc-4626/ERC4626.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L2)

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L3)

```solidity
File: src/maia/PartnerUtilityManager.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L2)

```solidity
File: src/rewards/base/FlywheelCore.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L3)

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L2)

```solidity
File: src/erc-4626/ERC4626MultiToken.sol

2: pragma solidity >=0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626MultiToken.sol#L2)

```solidity
File: src/erc-20/ERC20Boost.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Boost.sol#L3)

```solidity
File: src/erc-20/ERC20MultiVotes.sol

4: pragma solidity ^0.8.0;

```
[L4](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20MultiVotes.sol#L4)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

3: pragma solidity >=0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L3)

```solidity
File: src/erc-20/ERC20Gauges.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/erc-20/ERC20Gauges.sol#L3)

```solidity
File: src/uni-v3-staker/libraries/IncentiveId.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveId.sol#L2)

```solidity
File: src/ulysses-omnichain/lib/AnycallFlags.sol

3: pragma solidity ^0.8.10;

```
[L3](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/lib/AnycallFlags.sol#L3)

```solidity
File: src/maia/libraries/DateTimeLib.sol

2: pragma solidity ^0.8.4;

```
[L2](https://github.com/2023-05-maia/blob/main/src/maia/libraries/DateTimeLib.sol#L2)

```solidity
File: src/uni-v3-staker/libraries/NFTPositionInfo.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/NFTPositionInfo.sol#L3)

```solidity
File: src/uni-v3-staker/libraries/IncentiveTime.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L2)

```solidity
File: src/uni-v3-staker/libraries/RewardMath.sol

2: pragma solidity ^0.8.0;

```
[L2](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/RewardMath.sol#L2)

```solidity
File: src/talos/libraries/PoolActions.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/libraries/PoolActions.sol#L3)

```solidity
File: src/talos/libraries/PoolVariables.sol

3: pragma solidity ^0.8.0;

```
[L3](https://github.com/2023-05-maia/blob/main/src/talos/libraries/PoolVariables.sol#L3)

</details>

&nbsp;
## [L-20] Handle case when `totalSupply` is 0

The case in which `totalSupply` is equal to zero should be explicitly handled
to avoid unexpected behaviour.

<details><summary>Instances: 2</summary>

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

219:         if (newRate > (address(bHermesToken).balanceOf(address(this)) / totalSupply)) {

```
[L219](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L219)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

261:             uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);

```
[L261](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L261)

</details>

&nbsp;
## [L-21] Downcasting `uint` or `int` may result in overflow

Consider using OpenZeppelin's `SafeCast` library to prevent unexpected overflows.

<details><summary>Instances: 18</summary>

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

160:         DepositMultipleParams memory dParams = _bridgeInMultiple(
161:             _router,
162:             _data[
163:                 PARAMS_START:
164:                     PARAMS_END_OFFSET + uint16(uint8(bytes1(_data[PARAMS_START]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
165:             ],
166:             _fromChainId
167:         );

177:             (success, result) = IRouter(_router).anyExecuteDepositMultiple(
178:                 bytes1(_data[PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE]),
179:                 _data[
180:                     PARAMS_START + PARAMS_END_OFFSET + uint16(numOfAssets) * PARAMS_TKN_SET_SIZE_MULTIPLE:
181:                         length - PARAMS_GAS_IN
182:                 ],
183:                 dParams,
184:                 _fromChainId
185:             );

266:         DepositMultipleParams memory dParams = _bridgeInMultiple(
267:             _account,
268:             _data[
269:                 PARAMS_START_SIGNED:
270:                     PARAMS_END_SIGNED_OFFSET
271:                         + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE
272:             ],
273:             _fromChainId
274:         );

283:                 (success, result) = IRouter(_router).anyExecuteSignedDepositMultiple(
284:                     _data[PARAMS_END_SIGNED_OFFSET
285:                         + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE],
286:                     _data[
287:                         PARAMS_START + PARAMS_END_SIGNED_OFFSET
288:                             + uint16(uint8(bytes1(_data[PARAMS_START_SIGNED]))) * PARAMS_TKN_SET_SIZE_MULTIPLE:
289:                             _data.length - PARAMS_GAS_IN
290:                     ],
291:                     dParams,
292:                     _account,
293:                     _fromChainId
294:                 );

```
[L160](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L160), [L177](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L177), [L266](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L266), [L283](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L283)

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

416:             secondsInsideX128 = RewardMath.computeBoostedSecondsInsideX128(
417:                 stakedDuration,
418:                 liquidity,
419:                 uint128(boostAmount),
420:                 uint128(boostTotalSupply),
421:                 secondsPerLiquidityInsideInitialX128,
422:                 secondsPerLiquidityInsideX128
423:             );

515:             stake.liquidityNoOverflow = uint96(liquidity);

```
[L416](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L416), [L515](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L515)

```solidity
File: src/ulysses-omnichain/RootBridgeAgent.sol

359:         bytes memory data = abi.encodePacked(
360:             bytes1(0x02),
361:             _recipient,
362:             uint8(hTokens.length),
363:             settlementNonce,
364:             hTokens,
365:             tokens,
366:             _amounts,
367:             _deposits,
368:             _data,
369:             _manageGasOut(_toChain)
370:         );

```
[L359](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgent.sol#L359)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

288:         bytes memory packedData = abi.encodePacked(
289:             bytes1(0x06),
290:             msg.sender,
291:             uint8(_dParams.hTokens.length),
292:             depositNonce,
293:             _dParams.hTokens,
294:             _dParams.tokens,
295:             _dParams.amounts,
296:             _deposits,
297:             _dParams.toChain,
298:             _params,
299:             msg.value.toUint128(),
300:             _remoteExecutionGas
301:         );

332:         if (uint8(getDeposit[_depositNonce].hTokens.length) == 1) {

365:         } else if (uint8(getDeposit[_depositNonce].hTokens.length) > 1) {

370:                 packedData = abi.encodePacked(
371:                     bytes1(0x06),
372:                     msg.sender,
373:                     uint8(getDeposit[_depositNonce].hTokens.length),
374:                     nonce,
375:                     getDeposit[nonce].hTokens,
376:                     getDeposit[nonce].tokens,
377:                     getDeposit[nonce].amounts,
378:                     _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
379:                     _toChain,
380:                     _params,
381:                     msg.value.toUint128(),
382:                     _remoteExecutionGas
383:                 );

385:                 packedData = abi.encodePacked(
386:                     bytes1(0x03),
387:                     uint8(getDeposit[nonce].hTokens.length),
388:                     _depositNonce,
389:                     getDeposit[nonce].hTokens,
390:                     getDeposit[nonce].tokens,
391:                     getDeposit[nonce].amounts,
392:                     _normalizeDecimalsMultiple(getDeposit[nonce].deposits, getDeposit[nonce].tokens),
393:                     _toChain,
394:                     _params,
395:                     msg.value.toUint128(),
396:                     _remoteExecutionGas
397:                 );

724:         bytes memory packedData = abi.encodePacked(
725:             bytes1(0x03),
726:             uint8(_dParams.hTokens.length),
727:             depositNonce,
728:             _dParams.hTokens,
729:             _dParams.tokens,
730:             _dParams.amounts,
731:             deposits,
732:             _dParams.toChain,
733:             _params,
734:             _gasToBridgeOut,
735:             _remoteExecutionGas
736:         );

```
[L288](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L288), [L332](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L332), [L365](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L365), [L370](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L370), [L385](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L385), [L724](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L724)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

261:             uint128 liquidityToDecrease = uint128((liquidity * shares) / totalSupply);

```
[L261](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L261)

```solidity
File: src/uni-v3-staker/libraries/IncentiveTime.sol

20:         return uint96(((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) * INCENTIVES_DURATION + INCENTIVES_OFFSET);

26:         return uint96(
27:             (((timestamp - INCENTIVES_OFFSET) / INCENTIVES_DURATION) + 1) * INCENTIVES_DURATION + INCENTIVES_OFFSET
28:         );

32:         end = start + uint96(INCENTIVES_DURATION);

41:         end = start + uint96(INCENTIVES_DURATION);

```
[L20](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L20), [L26](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L26), [L32](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L32), [L41](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/libraries/IncentiveTime.sol#L41)

</details>

&nbsp;
## [L-22] Unused `Ownable`

`Ownable` is inherited from but the `onlyOwner` modifier is never used.

<details><summary>Instances: 2</summary>

```solidity
File: src/gauges/factories/BribesFactory.sol

18: contract BribesFactory is Ownable, IBribesFactory {

```
[L18](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L18)

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

18: contract FlywheelGaugeRewards is Ownable, IFlywheelGaugeRewards {

```
[L18](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L18)

</details>

&nbsp;
## [L-23] Missing `address(0)` checks in constructor/initialize

Failing to check for invalid parameters on deployment may result in an erroneous input and require an expensive redeployment.

<details><summary>Instances: 61</summary>

```solidity
File: src/hermes/tokens/HERMES.sol

48:     constructor(address _owner) ERC20("Hermes", "HERMES", 18) {

```
[L48](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/HERMES.sol#L48)

```solidity
File: src/maia/tokens/Maia.sol

41:     constructor(address _owner) ERC20("Maia", "MAIA", 18) {

```
[L41](https://github.com/2023-05-maia/blob/main/src/maia/tokens/Maia.sol#L41)

```solidity
File: src/rewards/booster/FlywheelBoosterGaugeWeight.sol

48:     constructor(bHermesGauges _bHermesGauges) {

```
[L48](https://github.com/2023-05-maia/blob/main/src/rewards/booster/FlywheelBoosterGaugeWeight.sol#L48)

```solidity
File: src/rewards/depots/SingleRewardsDepot.sol

22:     constructor(address _asset) {

```
[L22](https://github.com/2023-05-maia/blob/main/src/rewards/depots/SingleRewardsDepot.sol#L22)

```solidity
File: src/hermes/tokens/bHermesBoost.sol

22:     constructor(address _owner) ERC20("bHermes Boost", "bHERMES-B", 18) {

```
[L22](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesBoost.sol#L22)

```solidity
File: src/rewards/rewards/FlywheelBribeRewards.sol

27:     constructor(FlywheelCore _flywheel, uint256 _rewardsCycleLength)
28:         FlywheelAcummulatedRewards(_flywheel, _rewardsCycleLength)
29:     {}

```
[L27](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelBribeRewards.sol#L27)

```solidity
File: src/rewards/rewards/FlywheelInstantRewards.sol

28:     constructor(FlywheelCore _flywheel) BaseFlywheelRewards(_flywheel) {

```
[L28](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelInstantRewards.sol#L28)

```solidity
File: src/rewards/FlywheelCoreInstant.sol

33:     constructor(
34:         address _rewardToken,
35:         IFlywheelRewards _flywheelRewards,
36:         IFlywheelBooster _flywheelBooster,
37:         address _owner
38:     ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}

```
[L33](https://github.com/2023-05-maia/blob/main/src/rewards/FlywheelCoreInstant.sol#L33)

```solidity
File: src/rewards/FlywheelCoreStrategy.sol

32:     constructor(
33:         address _rewardToken,
34:         IFlywheelRewards _flywheelRewards,
35:         IFlywheelBooster _flywheelBooster,
36:         address _owner
37:     ) Core(_rewardToken, address(_flywheelRewards), _flywheelBooster, _owner) {}

```
[L32](https://github.com/2023-05-maia/blob/main/src/rewards/FlywheelCoreStrategy.sol#L32)

```solidity
File: src/hermes/tokens/bHermesGauges.sol

26:     constructor(address _owner, uint32 _rewardsCycleLength, uint32 _incrementFreezeWindow)
27:         ERC20Gauges(_rewardsCycleLength, _incrementFreezeWindow)
28:         ERC20("bHermes Gauges", "bHERMES-G", 18)
29:     {

```
[L26](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesGauges.sol#L26)

```solidity
File: src/hermes/tokens/bHermesVotes.sol

20:     constructor(address _owner) ERC20("bHermes Votes", "bHERMES-V", 18) {

```
[L20](https://github.com/2023-05-maia/blob/main/src/hermes/tokens/bHermesVotes.sol#L20)

```solidity
File: src/talos/factories/TalosStrategyVanillaFactory.sol

24:     constructor(INonfungiblePositionManager _nonfungiblePositionManager, OptimizerFactory _optimizerFactory)
25:         TalosBaseStrategyFactory(_nonfungiblePositionManager, _optimizerFactory)
26:     {}

```
[L24](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosStrategyVanillaFactory.sol#L24)

```solidity
File: src/talos/factories/BoostAggregatorFactory.sol

34:     constructor(UniswapV3Staker _uniswapV3Staker) {

```
[L34](https://github.com/2023-05-maia/blob/main/src/talos/factories/BoostAggregatorFactory.sol#L34)

```solidity
File: src/gauges/UniswapV3Gauge.sol

33:     constructor(
34:         FlywheelGaugeRewards _flywheelGaugeRewards,
35:         address _uniswapV3Staker,
36:         address _uniswapV3Pool,
37:         uint24 _minimumWidth,
38:         address _owner
39:     ) BaseV2Gauge(_flywheelGaugeRewards, _uniswapV3Pool, _owner) {

```
[L33](https://github.com/2023-05-maia/blob/main/src/gauges/UniswapV3Gauge.sol#L33)

```solidity
File: src/rewards/depots/MultiRewardsDepot.sol

29:     constructor(address _owner) {

```
[L29](https://github.com/2023-05-maia/blob/main/src/rewards/depots/MultiRewardsDepot.sol#L29)

```solidity
File: src/ulysses-omnichain/VirtualAccount.sol

25:     constructor(address _userAddress, address _localPortAddress) {

```
[L25](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/VirtualAccount.sol#L25)

```solidity
File: src/talos/factories/TalosStrategyStakedFactory.sol

40:     constructor(
41:         INonfungiblePositionManager _nonfungiblePositionManager,
42:         OptimizerFactory _optimizerFactory,
43:         BoostAggregatorFactory _boostAggregatorFactory
44:     ) TalosBaseStrategyFactory(_nonfungiblePositionManager, _optimizerFactory) {

```
[L40](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosStrategyStakedFactory.sol#L40)

```solidity
File: src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol

35:     function initialize(address _wrappedNativeTokenAddress, address _coreRouter) external onlyOwner {

```
[L35](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ERC20hTokenBranchFactory.sol#L35)

```solidity
File: src/maia/factories/PartnerManagerFactory.sol

37:     constructor(ERC20 _bHermes, address _owner) {

```
[L37](https://github.com/2023-05-maia/blob/main/src/maia/factories/PartnerManagerFactory.sol#L37)

```solidity
File: src/ulysses-amm/UlyssesRouter.sol

20:     constructor(UlyssesFactory _ulyssesFactory) {

```
[L20](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesRouter.sol#L20)

```solidity
File: src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol

48:     constructor(
49:         uint24 _rootChainId,
50:         WETH9 _wrappedNativeToken,
51:         address _localAnyCallAddress,
52:         address _rootPortAddress,
53:         address _daoAddress
54:     ) {

```
[L48](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/RootBridgeAgentFactory.sol#L48)

```solidity
File: src/talos/TalosOptimizer.sol

34:     constructor(
35:         uint32 _twapDuration,
36:         int24 _maxTwapDeviation,
37:         int24 _tickRangeMultiplier,
38:         uint24 _priceImpactPercentage,
39:         uint256 _maxTotalSupply,
40:         address _owner
41:     ) {

```
[L34](https://github.com/2023-05-maia/blob/main/src/talos/TalosOptimizer.sol#L34)

```solidity
File: src/governance/GovernorBravoDelegator.sol

8:     constructor(
9:         address timelock_,
10:         address govToken_,
11:         address admin_,
12:         address implementation_,
13:         uint256 votingPeriod_,
14:         uint256 votingDelay_,
15:         uint256 proposalThreshold_
16:     ) public {

```
[L8](https://github.com/2023-05-maia/blob/main/src/governance/GovernorBravoDelegator.sol#L8)

```solidity
File: src/maia/vMaia.sol

48:     constructor(
49:         PartnerManagerFactory _factory,
50:         uint256 _bHermesRate,
51:         ERC20 _partnerAsset,
52:         string memory _name,
53:         string memory _symbol,
54:         address _bhermes,
55:         address _partnerVault,
56:         address _owner
57:     ) ERC4626PartnerManager(_factory, _bHermesRate, _partnerAsset, _name, _symbol, _bhermes, _partnerVault, _owner) {

```
[L48](https://github.com/2023-05-maia/blob/main/src/maia/vMaia.sol#L48)

```solidity
File: src/gauges/factories/BribesFactory.sol

50:     constructor(
51:         BaseV2GaugeManager _gaugeManager,
52:         FlywheelBoosterGaugeWeight _flywheelGaugeWeightBooster,
53:         uint256 _rewardsCycleLength,
54:         address _owner
55:     ) {

```
[L50](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BribesFactory.sol#L50)

```solidity
File: src/gauges/factories/UniswapV3GaugeFactory.sol

50:     constructor(
51:         BaseV2GaugeManager _gaugeManager,
52:         bHermesBoost _bHermesBoost,
53:         IUniswapV3Factory _factory,
54:         INonfungiblePositionManager _nonfungiblePositionManager,
55:         FlywheelGaugeRewards _flywheelGaugeRewards,
56:         BribesFactory _bribesFactory,
57:         address _owner
58:     ) BaseV2GaugeFactory(_gaugeManager, _bHermesBoost, _bribesFactory, _owner) {

```
[L50](https://github.com/2023-05-maia/blob/main/src/gauges/factories/UniswapV3GaugeFactory.sol#L50)

```solidity
File: src/talos/TalosManager.sol

44:     constructor(
45:         address _strategy,
46:         int24 _ticksFromLowerRebalance,
47:         int24 _ticksFromUpperRebalance,
48:         int24 _ticksFromLowerRerange,
49:         int24 _ticksFromUpperRerange
50:     ) {

```
[L44](https://github.com/2023-05-maia/blob/main/src/talos/TalosManager.sol#L44)

```solidity
File: src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol

54:     function initialize(address _coreRootBridgeAgent) external override onlyOwner {

```
[L54](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/factories/ArbitrumBranchBridgeAgentFactory.sol#L54)

```solidity
File: src/hermes/bHermes.sol

55:     constructor(ERC20 _hermes, address _owner, uint32 _gaugeCycleLength, uint32 _incrementFreezeWindow)
56:         UtilityManager(
57:             address(new bHermesGauges(_owner, _gaugeCycleLength, _incrementFreezeWindow)),
58:             address(new bHermesBoost(_owner)),
59:             address(new bHermesVotes(_owner))
60:         )
61:         ERC4626DepositOnly(_hermes, "Burned Hermes: Gov + Yield + Boost", "bHermes")
62:     {}

```
[L55](https://github.com/2023-05-maia/blob/main/src/hermes/bHermes.sol#L55)

```solidity
File: src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol

38:     constructor(address _hTokenFactoryAddress, address _localPortAddress)
39:         CoreBranchRouter(_hTokenFactoryAddress, _localPortAddress)
40:     {}

```
[L38](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumCoreBranchRouter.sol#L38)

```solidity
File: src/ulysses-amm/UlyssesToken.sol

21:     constructor(
22:         uint256 _id,
23:         address[] memory _assets,
24:         uint256[] memory _weights,
25:         string memory _name,
26:         string memory _symbol,
27:         address _owner
28:     ) ERC4626MultiToken(_assets, _weights, _name, _symbol) {

```
[L21](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesToken.sol#L21)

```solidity
File: src/gauges/factories/BaseV2GaugeManager.sol

43:     constructor(bHermes _bHermes, address _owner, address _admin) {

```
[L43](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeManager.sol#L43)

```solidity
File: src/hermes/minters/BaseV2Minter.sol

53:     constructor(
54:         address _vault, // the B(3,3) system that will be locked into
55:         address _dao,
56:         address _owner
57:     ) {

78:     function initialize(FlywheelGaugeRewards _flywheelGaugeRewards) external {

```
[L53](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L53), [L78](https://github.com/2023-05-maia/blob/main/src/hermes/minters/BaseV2Minter.sol#L78)

```solidity
File: src/talos/boost-aggregator/BoostAggregator.sol

64:     constructor(UniswapV3Staker _uniswapV3Staker, ERC20 _hermes, address _owner) {

```
[L64](https://github.com/2023-05-maia/blob/main/src/talos/boost-aggregator/BoostAggregator.sol#L64)

```solidity
File: src/talos/TalosStrategyVanilla.sol

58:     constructor(
59:         IUniswapV3Pool _pool,
60:         ITalosOptimizer _optimizer,
61:         INonfungiblePositionManager _nonfungiblePositionManager,
62:         address _strategyManager,
63:         address _owner
64:     ) TalosStrategySimple(_pool, _optimizer, _nonfungiblePositionManager, _strategyManager, _owner) {}

```
[L58](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyVanilla.sol#L58)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchPort.sol

33:     constructor(uint24 _localChainId, address _rootPortAddress, address _owner) BranchPort(_owner) {

```
[L33](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchPort.sol#L33)

```solidity
File: src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol

71:     constructor(
72:         WETH9 _wrappedNativeToken,
73:         uint256 _localChainId,
74:         address _rootBridgeAgentAddress,
75:         address _localAnyCallAddress,
76:         address _localAnyCallExecutorAddress,
77:         address _localRouterAddress,
78:         address _localPortAddress
79:     )
80:         BranchBridgeAgent(
81:             _wrappedNativeToken,
82:             _localChainId,
83:             _localChainId,
84:             _rootBridgeAgentAddress,
85:             _localAnyCallAddress,
86:             _localAnyCallExecutorAddress,
87:             _localRouterAddress,
88:             _localPortAddress
89:         )
90:     {}

```
[L71](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/ArbitrumBranchBridgeAgent.sol#L71)

```solidity
File: src/talos/TalosStrategyStaked.sol

63:     constructor(
64:         IUniswapV3Pool _pool,
65:         ITalosOptimizer _optimizer,
66:         BoostAggregator _boostAggregator,
67:         address _strategyManager,
68:         FlywheelCoreInstant _flywheel,
69:         address _owner
70:     )
71:         TalosStrategySimple(
72:             _pool,
73:             _optimizer,
74:             _boostAggregator.nonfungiblePositionManager(),
75:             _strategyManager,
76:             _owner
77:         )
78:     {

```
[L63](https://github.com/2023-05-maia/blob/main/src/talos/TalosStrategyStaked.sol#L63)

```solidity
File: src/rewards/rewards/FlywheelGaugeRewards.sol

53:     constructor(address _rewardToken, address _owner, ERC20Gauges _gaugeToken, IBaseV2Minter _minter) {

```
[L53](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelGaugeRewards.sol#L53)

```solidity
File: src/ulysses-omnichain/CoreBranchRouter.sol

26:     constructor(address _hTokenFactoryAddress, address _localPortAddress) BaseBranchRouter() {

```
[L26](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreBranchRouter.sol#L26)

```solidity
File: src/ulysses-omnichain/CoreRootRouter.sol

56:     constructor(uint24 _rootChainId, address _wrappedNativeToken, address _rootPortAddress) {

63:     function initialize(address _bridgeAgentAddress, address _hTokenFactory) external onlyOwner {

```
[L56](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L56), [L63](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/CoreRootRouter.sol#L63)

```solidity
File: src/ulysses-omnichain/RootBridgeAgentExecutor.sol

61:     constructor(address owner) {

```
[L61](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/RootBridgeAgentExecutor.sol#L61)

```solidity
File: src/uni-v3-staker/UniswapV3Staker.sol

114:     constructor(
115:         IUniswapV3Factory _factory,
116:         INonfungiblePositionManager _nonfungiblePositionManager,
117:         IUniswapV3GaugeFactory _uniswapV3GaugeFactory,
118:         bHermesBoost _hermesGaugeBoost,
119:         uint256 _maxIncentiveStartLeadTime,
120:         address _minter,
121:         address _hermes
122:     ) {

```
[L114](https://github.com/2023-05-maia/blob/main/src/uni-v3-staker/UniswapV3Staker.sol#L114)

```solidity
File: src/ulysses-amm/UlyssesPool.sol

80:     constructor(
81:         uint256 _id,
82:         address _asset,
83:         string memory _name,
84:         string memory _symbol,
85:         address _owner,
86:         address _factory
87:     ) UlyssesERC4626(_asset, _name, _symbol) {

```
[L80](https://github.com/2023-05-maia/blob/main/src/ulysses-amm/UlyssesPool.sol#L80)

```solidity
File: src/ulysses-omnichain/BranchBridgeAgent.sol

141:     constructor(
142:         WETH9 _wrappedNativeToken,
143:         uint256 _rootChainId,
144:         uint256 _localChainId,
145:         address _rootBridgeAgentAddress,
146:         address _localAnyCallAddress,
147:         address _localAnyCallExecutorAddress,
148:         address _localRouterAddress,
149:         address _localPortAddress
150:     ) {

```
[L141](https://github.com/2023-05-maia/blob/main/src/ulysses-omnichain/BranchBridgeAgent.sol#L141)

```solidity
File: src/rewards/base/BaseFlywheelRewards.sol

31:     constructor(FlywheelCore _flywheel) {

```
[L31](https://github.com/2023-05-maia/blob/main/src/rewards/base/BaseFlywheelRewards.sol#L31)

```solidity
File: src/rewards/rewards/FlywheelAcummulatedRewards.sol

33:     constructor(FlywheelCore _flywheel, uint256 _rewardsCycleLength) BaseFlywheelRewards(_flywheel) {

```
[L33](https://github.com/2023-05-maia/blob/main/src/rewards/rewards/FlywheelAcummulatedRewards.sol#L33)

```solidity
File: src/talos/strategies/TalosStrategySimple.sol

18:     constructor(
19:         IUniswapV3Pool _pool,
20:         ITalosOptimizer _strategy,
21:         INonfungiblePositionManager _nonfungiblePositionManager,
22:         address _strategyManager,
23:         address _owner
24:     ) TalosBaseStrategy(_pool, _strategy, _nonfungiblePositionManager, _strategyManager, _owner) {}

```
[L18](https://github.com/2023-05-maia/blob/main/src/talos/strategies/TalosStrategySimple.sol#L18)

```solidity
File: src/talos/factories/TalosBaseStrategyFactory.sol

39:     constructor(INonfungiblePositionManager _nonfungiblePositionManager, OptimizerFactory _optimizerFactory) {

```
[L39](https://github.com/2023-05-maia/blob/main/src/talos/factories/TalosBaseStrategyFactory.sol#L39)

```solidity
File: src/erc-4626/ERC4626DepositOnly.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```
[L23](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626DepositOnly.sol#L23)

```solidity
File: src/erc-4626/UlyssesERC4626.sol

24:     constructor(address _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, 18) {

```
[L24](https://github.com/2023-05-maia/blob/main/src/erc-4626/UlyssesERC4626.sol#L24)

```solidity
File: src/gauges/BaseV2Gauge.sol

61:     constructor(FlywheelGaugeRewards _flywheelGaugeRewards, address _strategy, address _owner) {

```
[L61](https://github.com/2023-05-maia/blob/main/src/gauges/BaseV2Gauge.sol#L61)

```solidity
File: src/hermes/UtilityManager.sol

44:     constructor(address _gaugeWeight, address _gaugeBoost, address _governance) {

```
[L44](https://github.com/2023-05-maia/blob/main/src/hermes/UtilityManager.sol#L44)

```solidity
File: src/erc-4626/ERC4626.sol

23:     constructor(ERC20 _asset, string memory _name, string memory _symbol) ERC20(_name, _symbol, _asset.decimals()) {

```
[L23](https://github.com/2023-05-maia/blob/main/src/erc-4626/ERC4626.sol#L23)

```solidity
File: src/gauges/factories/BaseV2GaugeFactory.sol

51:     constructor(
52:         BaseV2GaugeManager _gaugeManager,
53:         bHermesBoost _bHermesBoost,
54:         BribesFactory _bribesFactory,
55:         address _owner
56:     ) {

```
[L51](https://github.com/2023-05-maia/blob/main/src/gauges/factories/BaseV2GaugeFactory.sol#L51)

```solidity
File: src/maia/PartnerUtilityManager.sol

36:     constructor(
37:         address _gaugeWeight,
38:         address _gaugeBoost,
39:         address _governance,
40:         address _partnerGovernance,
41:         address _partnerVault
42:     ) UtilityManager(_gaugeWeight, _gaugeBoost, _governance) {

```
[L36](https://github.com/2023-05-maia/blob/main/src/maia/PartnerUtilityManager.sol#L36)

```solidity
File: src/rewards/base/FlywheelCore.sol

45:     constructor(address _rewardToken, address _flywheelRewards, IFlywheelBooster _flywheelBooster, address _owner) {

```
[L45](https://github.com/2023-05-maia/blob/main/src/rewards/base/FlywheelCore.sol#L45)

```solidity
File: src/maia/tokens/ERC4626PartnerManager.sol

50:     constructor(
51:         PartnerManagerFactory _factory,
52:         uint256 _bHermesRate,
53:         ERC20 _partnerAsset,
54:         string memory _name,
55:         string memory _symbol,
56:         address _bhermes,
57:         address _partnerVault,
58:         address _owner
59:     )
60:         PartnerUtilityManager(
61:             address(bHermes(_bhermes).gaugeWeight()),
62:             address(bHermes(_bhermes).gaugeBoost()),
63:             address(bHermes(_bhermes).governance()),
64:             address(new ERC20MultiVotes(_owner)),
65:             partnerVault
66:         )
67:         ERC4626(
68:             _partnerAsset,
69:             string.concat(_name, " - Burned Hermes: Aggregated Gov + Yield + Boost"),
70:             string.concat(_symbol, "-bHermes")
71:         )
72:     {

```
[L50](https://github.com/2023-05-maia/blob/main/src/maia/tokens/ERC4626PartnerManager.sol#L50)

```solidity
File: src/talos/base/TalosBaseStrategy.sol

79:     constructor(
80:         IUniswapV3Pool _pool,
81:         ITalosOptimizer _optimizer,
82:         INonfungiblePositionManager _nonfungiblePositionManager,
83:         address _strategyManager,
84:         address _owner
85:     ) ERC20("TALOS LP", "TLP", 18) {

```
[L79](https://github.com/2023-05-maia/blob/main/src/talos/base/TalosBaseStrategy.sol#L79)

</details>

&nbsp;

&nbsp;

