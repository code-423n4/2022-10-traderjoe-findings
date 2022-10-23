## Summary

G-01 Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate (1 instance)
G-02 State variables can be packed into fewer storage slots (1 instance)
G-03 Use calldata instead of memory for function parameters (34 instances)
G-04 <x> += <y> costs more gas than <x> = <x> + <y> for state variables (32 instances)
G-05 internal functions only called once can be inlined to save gas (9 instance)
G-06 Using bools for storage incurs overhead (1 instance)
G-07 Use a more recent version of solidity (6 instances)
G-08 Using private rather than public for constants, saves gas (4 instances)
G-09 Empty blocks should be removed or emit something (3 instances)
G-10 Use require() rather than assert()(1 instance)
G-11 Use simple comparison in trinary logic / in if statement (5 instances)

Total: 97 instances in 11 issues

---

 
## G-01 Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

1 instance in 1 file:

LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L68-L70


## G-02 State variables can be packed into fewer storage slots

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage. By arranging the variables, it is possible to minimize the number of slots used within a contract’s storage and therefore reduce deployment costs. address type variables are each of 20 bytes size (way less than 32 bytes). However, they here take up a whole 32 bytes slot (they are contiguous). As bool type variables are of size 1 byte, there’s a slot here that can get saved by moving them closer to an address

1 instance in 1 file:

LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L34-L39

 
## G-03 Use calldata instead of memory for function parameters

Having function arguments use calldata instead of memory can save gas. Recommended Mitigation Steps: Change function arguments from memory to calldata.

34 instance in 4 files:

LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L467-L469 (3)
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L845
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L862
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L881

LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134

LBRouter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L207
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L281
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L314
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L355
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L356
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L380
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L381
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L409
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L410
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L434
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L435
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L462
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L463
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L496
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L534
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L535
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L564
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L565
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L596
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L597
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L645
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L646


## G-04 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

32 instances in 4 files:

LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L149 (7)

LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L227
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L228
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L287
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L288
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L337
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L452
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L453
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L508
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L509
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L540
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L572
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L573
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L575
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L576
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L639
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L646
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L711
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L712
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L896
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L897

LBRouter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L128
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L129
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L174
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L175

LBToken.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L211


## G-05 internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

9 instance in 3 files:

LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L808
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L943
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L969
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1016

LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L217
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L238

LBToken.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L202
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L227
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L253
 

## G-06 Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. 
This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use uint256(1) and uint256(2) for true/false

1 instance in 1 file:

LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L39


## G-07 Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>). 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

5 instance in 5 files:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L3
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol#L3


## G-08 Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

4 instances in 1 file:

LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L27
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L28
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L30
 

## G-09 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

3 instance2 in 2 files:

LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L123
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L203

LBToken.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L326

 
## G-10 Use require() rather than assert()

Require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking. Properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix. 

1 instance in 1 file:

LBFactory.so
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141

 

## G-11 Use simple comparison in trinary logic / in if statement 

The comparison operators >= and <= use more gas than >, <, or ==. Replacing the >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas. 

Recommended Mitigation Steps: 
Replace the comparison operator and reverse the logic to save gas using the suggestions above.

5 instance in 2 files:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L549

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L278
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L517
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L643
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L924

 
