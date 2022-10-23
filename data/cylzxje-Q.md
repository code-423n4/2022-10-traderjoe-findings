#### [L-01] Use require() instead of assert() 
assert() should only be used in invariant checking. Unless a `Panic` really is intended.

src/LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141

Recommend using `require()` or custom error