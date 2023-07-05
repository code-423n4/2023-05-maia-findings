- `ERC20hTokenBranch::mint`, `ERC20hTokenRoot::mint` returns bool
`ERC20hTokenBranch::mint` and `ERC20hTokenRoot::mint` returns bool and although it doesn't cause any problem now, this can affect composability in the future.
As the bool return value is not checked anywhere in the code, better to remove it.

