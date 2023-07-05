Use `unchecked { i++; }` in loops where the end is known. The regex pattern is `; ?\w*\++`. Save gas because solidity checks integers for over/underflow after 0.8.0 by default, so we can omit that because we know the limit

Change strings in `require` to custom errors, saving gas on the way. The regex pattern is `"\w*"\);` and loop through the occurrences

Do not initialize variables to 0 due to being set to that by default. For example, just put in loops `for(uint256 i; ...)` instead of `for(uint256 i = 0; ...)`. It's useless work. For ocurrences just put in visual studio the next regex pattern: `[^ ]* = 0`

