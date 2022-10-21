## BYTES CONSTANTS ARE CHEAPER THAN STRING CONSTANTS

If the string can fit into 32 bytes, then `bytes32` is cheaper than `string`. `string` is a dynamically sized-type, which has current limitations in Solidity compared to a statically sized variable. This means extra gas is spent upon deployment and the constant is read every time.

[`src/LBToken.sol:29`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29)
[`src/LBToken.sol:30`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L30)

## `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)

[`src/LBQuoter.sol:75`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)
[`src/LBQuoter.sol:100`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)
[`src/LBQuoter.sol:154`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)
[`src/LBQuoter.sol:177`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)
[`src/LBRouter.sol:711`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)