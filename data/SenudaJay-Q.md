### L-01 - If weight is zero for a particular gauge, no need to increment the gauge weight

![OpenAI Logo](https://i.ibb.co/FX7FLW1/Screenshot-2023-06-26-at-20-07-47.png=2x)
The `incrementGauges` is an external function, and the `weights` array can be any value. If a specific `weight` does become zero, it is not filtered out in the above code, and `_incrementGaugeWeight` function is called for each gauge irrespective of the weight being zero or not. If a `weight` is zero, there is no point of executing this function and furthermore the event `IncrementGaugeWeight` is executed without the gauge being incremented. If there are many zero weights in this array, it would be a waste of gas as well.

We can simply check the value of the `weight` before calling the `_incrementGaugeWeight` function as follows
![OpenAI Logo](https://i.ibb.co/X3TYFMF/Screenshot-2023-06-27-at-22-49-05.png)

### L-02 - Typos in the code

File: src/hermes/bHermes.sol

98:         /// @dev Never overflows since balandeOf >= userClaimed.

Should be corrected as
           /// @dev Never overflows since balanceOf >= userClaimed.