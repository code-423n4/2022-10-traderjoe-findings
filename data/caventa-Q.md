1. USE A MORE RECENT VERSION OF SOLIDITY

^0.8.0 is used.
Should use 0.8.17
New features and bugs fixed are introduced in the latest version.

2. SHOULD EMIT EVENT IN CATCH BLOCK

https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBQuoter.sol#L123
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBQuoter.sol#L203

Meaningful values should be emitted in the event instead of letting it fails silently.