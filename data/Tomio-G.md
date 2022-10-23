1.
Title: Using SafeMath for solidity >0.8

Proof of Concept:
[FeeHelper.sol#L14](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L14)

Recommended Mitigation Steps:
it's better to remove `using SafeMath for uint256` for solidity >0.8

reference: https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2465
________________________________________________________________________

2.
Title: `>=` is cheaper than `>`

Impact:
Strict inequalities (`>`) are more expensive than non-strict ones (`>=`). This is due to some supplementary checks (ISZERO, 3 gas)

Proof of Concept:
[FeeHelper.sol#L83](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L83)

Recommended Mitigation Steps:
Consider using `>=` instead of `>` to avoid some opcodes
________________________________________________________________________

3.
Title: Gas optimization to dividing by 2

Proof of Concept:
[Oracle.sol#L162](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L162)

Recommended Mitigation Steps:
Replace `/ 2` with `>> 1`

Reference: [here](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
________________________________________________________________________

4.
Title: Consider make constant as private to save gas

Proof of Concept:
[LBFactory.sol#L25-L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30)

Recommended Mitigation Steps:
I suggest changing the visibility from `public` to `internal` or `private`
________________________________________________________________________

5.
Title: Using bytes constant is more gas efficient

Reference: [Here](https://ethereum.stackexchange.com/questions/3795/why-do-solidity-examples-use-bytes32-type-instead-of-string)

Proof of Concept:
[LBToken.sol#L29-L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29-L30)

Recommended Mitigation Steps:
Change it to `bytes(1..32) constant`
________________________________________________________________________

6.
Title: Caching `length` for loop can save gas

Proof of Concept:
[LBToken.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90)

Recommended Mitigation Steps:
Change to:

```
  uint256 Length = _accounts.length;
  for (uint256 i; i < Length; ++i) {
```
________________________________________________________________________

7.
Title: Using `msg.sender` directly is more effective

Proof of Concept:
[LBToken.sol#L141](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L141)

Recommended Mitigation Steps:
In function safeTransferFrom() use `msg.sender` directly instead of caching it to `_spender`.
________________________________________________________________________