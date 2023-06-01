






[G-1]	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements	60
[G-2]	Use assembly to check for address(0)	31
[G-3]	Use custom errors instead of require	108
[G-4]	Empty blocks should be removed or emit something	3
[G-5]	Non efficient zero initialization	56
[G-6]	Use assembly to write address storage values	209
[G-7]	Duplicated require/if checks should be refactored to a modifier or function	2
[G-8]	Functions guaranteed to revert when called by normal users can be marked payable	92
[G-9]	State variable read in a loop	68
[G-10]	State variables only set in the constructor should be declared immutable	18
[G-11]	Don’t compare boolean expressions to boolean literals	1
[G-12]	Using storage instead of memory for structs/arrays saves gas	8
[G-13]	Use calldata instead of memory for function parameters	22
[G-14]	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements1	2
[G-15]	Function calls should be cached	32
[G-16]	Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead	21
[G-17]	Change public to external for functions that are not called internally	89
[G-18]	<array>.length should not be looked up in every loop of a for-loop	29
[G-19]	Multiplication by two should use bit shifting	3
[G-20]	Reduce the size of error messages	65
[G-21]	Internal functions only called once can be inlined to save gas	38
[G-22]	Using bools for storage incurs overhead	71
[G-23]	Multiple mappings can be replaced with a single struct mapping	56
[G-24]	Setting the constructor to payable	57
[G-25]	Use unchecked keyword for loop counter	16
[G-26]	Do not reduce approval on transferFrom if current allowance is type(uint256).max	5
[G-27]	Bytes constants are more efficient than string constants	1
[G-28]	Pre-increments and pre-decrements are cheaper than post-increments and post-decrements1	2
[G-29]	<x> += <y> costs more gas than <x> = <x> + <y> for state variables	6
[G-30]	Use solidity version 0.8.20 to gain gas boost	142
[G-31]	Variable names that consist of all capital letters should be reserved for constant/immutable variables	1
[G-32]	Division by two should use bit shifting	9