 https://github.com/code-423n4/2023-05-maia/blob/54a45beb1428d85999da3f721f923cbf36ee3d35/src/maia/vMaia.sol#L102-L114
   uint256 private currentMonth;
    uint256 private unstakePeriodEnd;
currentMonth; and unstakePeriodEnd should be cache to save of gas
    function beforeWithdraw(uint256, uint256) internal override {
        /// @dev Check if unstake period has not ended yet, continue if it is the case.
        if (unstakePeriodEnd >= block.timestamp) return;


        uint256 _currentMonth = DateTimeLib.getMonth(block.timestamp);
        if (_currentMonth == currentMonth) revert UnstakePeriodNotLive();


        (bool isTuesday, uint256 _unstakePeriodStart) = DateTimeLib.isTuesday(block.timestamp);
        if (!isTuesday) revert UnstakePeriodNotLive();


        currentMonth = _currentMonth;
        unstakePeriodEnd = _unstakePeriodStart + 1 days;
    }