***** USE A MORE RECENT VERSION OF SOLIDITY *****

There are 6 instances of this issue:

1- https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L3
2- https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBToken.sol#L3
3- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L3
4- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L3
5- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol
6- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol#L3

The recommendations take into account:

- Risks related to recent releases.
- Risks of complex code generation changes.
- Risks of new language features.
- Risks of known bugs.

Use a simple pragma version that allows any of these versions (Consider using 0.8.16).
