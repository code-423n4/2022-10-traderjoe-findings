# 1) Use calldata instead of memory
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory.

6 Instances:
LBToken.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55

LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L106
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L597

LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L235

interfaces/ILBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L201

Recommended Mitigation Steps:
Consider replacement memory with calldata.


# 2) Prefix increments
Prefix increments are cheaper than postfix increments - 6 gas. 

3 Instances:
LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177

Recommended Mitigation Steps:
Consider changing i++ to ++i.
