
Create/create2 used . So related bugs should be investigated 




[H-1]	Incorrect comparison implementation	2
[M-1]	The owner is a single point of failure and a centralization risk	92
[L-1]	Contracts are vulnerable to fee-on-transfer token related accounting issues	91
[L-2]	Array does not have a pop function	44
[L-3]	Setters should have initial value check	14
[L-4]	Division by zero not prevented	21
[L-5]	Loss of precision	23
[L-6]	Signature Malleability of EVM's ecrecover	2
[L-7]	Use of floating pragma	52
[L-8]	Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists	73
[L-9]	External calls in an un-bounded for-loop may result in a DOS	48
[L-10]	Unsafe casting may overflow	106
[L-11]	Some ERC20 revert on zero value transfer	91
[L-12]	Zero address check is missing	59
[L-13]	Some ERC20 tokens dust would be remained in the contract	1
[L-14]	Division before multiplication can lead to precision errors	1
[L-15]	Array lengths not checked	12
[L-16]	Use Ownable2Step's transfer function rather than Ownable's for transfers of ownership	32
[L-17]	Do not use deprecated library functions	9
[L-18]	No limits when setting min/max amounts	15
[L-19]	Approve type(uint256).max not work with some tokens	9
[L-20]	Owner can renounce Ownership	29
[L-21]	Unsafe ERC20 operations	15
[N-1]	Typos in the code	98
[N-2]	Use a more recent version of solidity	68
[N-3]	Lines are too long	192
[N-4]	Inconsistent spacing in comments	1283
[N-5]	Large numeric literals should use underscores for readability	20
[N-6]	TODO in the code	2
[N-7]	Using named parameters in mapping is best practice	183
[N-8]	Function ordering does not follow the Solidity style guide	124
[N-9]	Private and internal variables must start with an underscore	231
[N-10]	Function parameter name must be in mixedCase	5
[N-11]	Variable name must be in mixedCase	6
[N-12]	It is standard for all external and public functions to be overridden from an interface	367
[N-13]	Constant name must be in capitalized SNAKE_CASE	23
[N-14]	Function must not be longer than 50 lines	28
[N-15]	Function name must be in mixedCase	2
[N-16]	Contract name must be in CamelCase	5
[N-17]	Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)	2
[N-18]	constants should be defined rather than using magic numbers	102
[N-19]	Reverts should use customer errors instead of strings	283
[N-20]	Event missing indexed field	16
[N-21]	Constants in comparisons should appear on the left side	180
[N-22]	Use modifier instead of require/if for special msg.sender	11
[N-23]	Use multiple require() and if statements instead of &&	44
[N-24]	else-block not required	5
[N-25]	Unsigned divisions can be marked as unchecked	7
[N-26]	Import only specific files	2
[N-27]	require()/revert() statements should have descriptive reason strings	35
[N-28]	Interfaces should be indicated with an I prefix in the contract name	6
[N-29]	Redundant return statement with named return variable	22
[N-30]	Use safeTransferOwnership instead of transferOwnership function	3
[N-31]	Contract name must be in CamelCase.	7
[N-32]	Critical functions should be a two step procedure	3
[N-33]	Shorthand way to write if / else statement	1
[N-34]	Use require instead of assert	3
[N-35]	Imports could be organized more systematically	119
[N-36]	Constant redefined elsewhere	37
[N-37]	NatSpec @return argument is missing	75
[N-38]	NatSpec @param is missing	35