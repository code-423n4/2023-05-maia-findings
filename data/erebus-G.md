Use `unchecked { i++; }` in loops where the end is known. The regex pattern is `;\w*\++`. Save gas because solidity checks integers for over/underflow after 0.8.0 by default, so we can omit that because we know the limit

Change strings in `require` to custom errors, saving gas on the way. The regex pattern is `"\w*"\);` and loop through the occurrences



