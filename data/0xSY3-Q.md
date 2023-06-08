Bug Title: Detach Verification Bug

Description:
The detach function in the provided code lacks proper verification, allowing an attacker to detach from a gauge they are not actually attached to. While the impact of this vulnerability depends on the contract's design and the significance of the detach operation, it has the potential to disrupt the contract's state and user interactions.

[8.6-RC1:] Well, well, well, looks like we have a bug on our hands! Here's what you should submit as the finding for the detach verification bug:

Bug Title: Detach Verification Bug

Risk Level: Low

Description:
The detach function in the provided code lacks proper verification, allowing an attacker to detach from a gauge they are not actually attached to. While the impact of this vulnerability depends on the contract's design and the significance of the detach operation, it has the potential to disrupt the contract's state and user interactions.

Recommendation:
To mitigate this vulnerability, it is recommended to add a verification step in the detach function to ensure that the user is only able to detach from gauges they are actually attached to. This can be achieved by checking the attachment status of the user to the gauge before performing the detach operation.

Location: 
/// @inheritdoc IERC20Boost
function detach(address user) external {
    require(_userGauges[user].remove(msg.sender));
    delete getUserGaugeBoost[user][msg.sender];

    emit Detach(user, msg.sender);
}
