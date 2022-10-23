## Summary

L-01 Missing checks for address(0x0) when assigning values to address state variables (7 instances)
L-02 require() should be used instead of assert() (1 instance)
L-03 Lack of zero-address checks for immutable addresses  (6 instances)

N-01 Use a more recent version of solidity (6 instances)

20 instances in 4 issues

---

## L-01 Missing checks for address(0x0) when assigning values to address state variables 

7 instances in 3 files:

LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63

LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L41
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L42
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L43

LBRouter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L53
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L53
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L55
 

## L-02 require() should be used instead of assert()

Require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking. Properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix. 

1 instance in 1 file:

LBFactory.so
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141



## L-03 Lack of zero-address checks for immutable addresses 
 
Lack of zero-address checks for immutable addresses will force contract redeployment if zero-address used accidentally. 
Zero-address checks as input validation on address parameters are always a best practice. 
This is especially true for critical addresses that are immutable and set in the constructor because they cannot be changed later. 
Accidentally using zero addresses here will lead to failing logic or force contract redeployment and increased gas costs. 

Recommend adding zero-address input validation for these addresses in the constructor.

6 instances in 2 files:

LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L21
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L23
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L25

LBRouter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L28
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L29
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L30



## N-01 Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>). 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

6 instances:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol#L3

 

