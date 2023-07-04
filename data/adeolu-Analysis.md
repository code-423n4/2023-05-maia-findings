# Approach taken in evaluating the codebase
Manual review, reading of docs, audit page. 


# Anything that you learned from evaluating the codebase
Learnt about bridges and cross chain communication. 

# Any comments for the judge to contextualize your findings
- I found some divisions before multiplication to be needless. Asides from precision/rounding error. They basically result to 1, mathematically.

- Math for converting 18 decimal amounts to other decimal amounts is faulty and can result in seriously bad accounting in the bridge contracts. 


### Time spent:
12 hours