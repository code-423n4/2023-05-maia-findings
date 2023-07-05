*This analysis report is meant to accompany my Gas Optimizations Report submission. All insights will be given through the perspective of optimizing the codebase.*

## Immediate Impressions
The overall storage layout for this codebase can be greatly improved so that substantial gas savings are had during deployment and runtime. In certain contracts state variables are able to have a reduced `uint` size, meaning they can be packed with other state variables. The state variables in question are able to be reduced either because they represent timestamps/block-numbers (in which case a `uint` type `>= uint40` [would suffice](https://twitter.com/PaulRBerg/status/1591832937179250693)) or they are enforced to have a max upper bounds. They can then be reduced to a `uint` type that is able to hold the max upper bound and packed with other state variables.

In a few contracts, dynamic storage arrays are accompanied by a seperate state variable meant to hold the length of the array. It should be know that dynamic arrays in solidity already hold the array length and it is updated whenever an item in the array is added or removed. Therefore, the inlcusion of these separate state variables are wasting a large amount of gas.

Some less obvious optimizations include refactoring code to avoid extra SLOADs or External Calls in `public`/`internal` functions that are invoked within the parent function. Although creating separate functions is good practice and keeps the code organized, it can be easy to let redundant storage reads/external calls go unnoticed. It is important to note that while refactoring is needed to mitigate these instances, maintaining code readbility is something to consider. In my report I outlined two methods (seen as `diffs`) to mitigate these sorts of findings. One method would be to refactor the `invoked` function, which would also require refactoring every other function that invokes the `invoked` function. The other method would be to adopt the logic of the `invoked` function into the parent function that contains the inital storage reads/write/external calls. The second option would slightly increase deployment costs since the bytecode would be larger due to the injected logic and the first method may hinder readability.

## Approach
This being a large codebase, I organized my approach to analyze the most impactful areas first. Below is an outline of my "checklist":

- storage layout
- mitigating SLOADs and SSTOREs
- mitigating external calls
- avoiding unnecessary memory expansion
- utilizing and optimizing calldata
- miscellaneous optimizations

Naturally, I began with the largest contracts and continued on with any inheriting contracts. This done in an attempt to gain a thorough understanding of each contract's storage layout (as optimizations to this area will yield the most savings).

## Comments
It is important to note that since the contracts will be compiled with `--via-ir`, some of the optimizations may not work or yield the suspected gas savings. All optimizations illustrated using the protocols tests compiler configuration as a basis (since this is how we were able to perform benchmarks), i.e. **not** using `--via-ir`.

I purposely excluded `bot-like` optimizations in an effort to provide the most value to the sponsors. In that same token, only runtime gas is highlighted in my report as it will be incurred for the lifetime of the protocol. In addition, since `view`/`pure` functions do not cost gas on their own, only `view`/`pure` functions that are called within state-mutating functions underwent optimizations. Finally, an effort was made to provide diffs for each instance so that the sponsor is able to understand the optimization in it's entirety and the necessary refactoring needed to carry the optimization out.

### Time spent:
50 hours