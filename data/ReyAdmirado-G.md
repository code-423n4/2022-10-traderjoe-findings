| | issue |
| ----------- | ----------- |
| 1 | [state variables can be packed into fewer storage slots](#1-state-variables-can-be-packed-into-fewer-storage-slots) |
| 2 | [Stack variable used as a cheaper cache for a state variable is only used once](#2-stack-variable-used-as-a-cheaper-cache-for-a-state-variable-is-only-used-once) |
| 3 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#3-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 4 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#4-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 5 | [not using the named return variables when a function returns, wastes deployment gas](#5-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 6 | [can make the variable outside the loop to save gas](#6-can-make-the-variable-outside-the-loop-to-save-gas) |
| 7 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#7-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 8 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#8-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 9 | [using `bool` for storage incurs overhead](#9-using-bool-for-storage-incurs-overhead) |
| 10 | [internal functions only called once can be inlined to save gas](#10-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 11 | [abi.encode() is less efficient than abi.encodepacked()](#11-abiencode-is-less-efficient-than-abiencodepacked) |
| 12 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#12-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 13 | [using private rather than public for constants, saves gas](#13-using-private-rather-than-public-for-constants-saves-gas) |
| 14 | [don’t use safemath once the solidity version is 0.8.0 or greater](#14-dont-use-safemath-once-the-solidity-version-is-080-or-greater) |
| 15 | [bytes constants are more efficient than string constants](#15-bytes-constants-are-more-efficient-than-string-constants) |
| 16 | [public library function should be made private/internal](#16-public-library-function-should-be-made-privateinternal) |
| 17 | [public functions not called by the contract should be declared external instead](#17-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |





## 1. state variables can be packed into fewer storage slots

If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper.

`creationUnlocked` can be put after `LBPairImplementation` so they can be in the same slot to save gas, also they are both used in the `createLBPair` function (line 234) which saves even more gas.
- [LBFactory.sol#L39](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L39)


## 2. Stack variable used as a cheaper cache for a state variable is only used once

If the variable is only accessed once, it’s cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend

- [LBFactory.sol#L261](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L261)
- [LBFactory.sol#L265](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265)
- [LBFactory.sol#L363](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L363)

- [LBPair.sol#L320](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L320)
- [LBPair.sol#L867](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L867)
- [LBPair.sol#L868](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L868)

- [LBRouter.sol#L713](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L713)

- [LBToken.sol#L137](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L137)


## 3. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

there cant be a underflow because its checked by the if statement beforehand
- [LBQuoter.sol#L65](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L65)

- [LBQuoter.sol#L144](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L144)

in the for loop i is being checked so it will underflow so we can use unchecked so we skip all the underflow checks for `i - 1`
- [LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)

- [LBRouter.sol#L711](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)


## 4. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves 113 gas

- [LBPair.sol#L452](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L452)
- [LBPair.sol#L453](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L453)
- [LBPair.sol#L717](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L717)
- [LBPair.sol#L720](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L720)

- [LBToken.sol#L211](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L211)
- [LBToken.sol#L241](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L241)

- [FeeDistributionHelper.sol#L42](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L42)

## 5. not using the named return variables when a function returns, wastes deployment gas

- [LBPair.sol#L994](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994)


## 6. can make the variable outside the loop to save gas

- [LBPair.sol#L275](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L275)
- [LBPair.sol#L280](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L280)
- [LBPair.sol#L285](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L285)
- [LBPair.sol#L496](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496)
- [LBPair.sol#L624](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L624)
- [LBPair.sol#L625](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L625)
- [LBPair.sol#L629](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L629)
- [LBPair.sol#L631](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L631)
- [LBPair.sol#L633](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L633)
- [LBPair.sol#L634](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L634)
- [LBPair.sol#L702](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L702)
- [LBPair.sol#L703](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L703)
- [LBPair.sol#L709](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L709)

- [LBQuoter.sol#L80](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L80)
- [LBQuoter.sol#L102](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L102)
- [LBQuoter.sol#L179](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L179)

- [LBRouter.sol#L675](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L675)


## 7. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)
- [LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)
- [LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)
- [LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)

- [LBRouter.sol#L711](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)


## 8. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, removes the need for such a loop in the contract code and runtime execution. 

- [LBPair.sol#L261](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261)
- [LBPair.sol#L467](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L467)
- [LBPair.sol#L468](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L468)
- [LBPair.sol#L469](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L469)
- [LBPair.sol#L617](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L617)
- [LBPair.sol#L618](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L618)
- [LBPair.sol#L688](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L688)

- [LBRouter.sol#L207](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L207)
- [LBRouter.sol#L230](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230)
- [LBRouter.sol#L280](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280)
- [LBRouter.sol#L281](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L281)
- [LBRouter.sol#L313](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313)
- [LBRouter.sol#L314](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L314)
- [LBRouter.sol#L355](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L355)
- [LBRouter.sol#L356](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L356)
- [LBRouter.sol#L380](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L380)
- [LBRouter.sol#L381](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L381)
- [LBRouter.sol#L409](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L409)
- [LBRouter.sol#L410](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L410)
- [LBRouter.sol#L434](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L434)
- [LBRouter.sol#L435](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L435)
- [LBRouter.sol#L462](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L462)
- [LBRouter.sol#L463](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L463)
- [LBRouter.sol#L495](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495)
- [LBRouter.sol#L496](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L496)
- [LBRouter.sol#L534](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L534)
- [LBRouter.sol#L535](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L535)
- [LBRouter.sol#L564](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L564)
- [LBRouter.sol#L565](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L565)
- [LBRouter.sol#L596](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L596)
- [LBRouter.sol#L597](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L597)
- [LBRouter.sol#L645](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L645)
- [LBRouter.sol#L646](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L646)


## 9. using `bool` for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [LBFactory.sol#L39](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L39)
- [LBFactory.sol#L316](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L316)
- [LBFactory.sol#L485](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485)

- [LBPair.sol#L243](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L243)
- [LBPair.sol#L304](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L304)

- [LBQuoter.sol#L102](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L102)
- [LBQuoter.sol#L179](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L179)
- [LBQuoter.sol#L237](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L237)

- [LBRouter.sol#L93](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L93)
- [LBRouter.sol#L151](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L151)
- [LBRouter.sol#L320](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L320)
- [LBRouter.sol#L798](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L798)
- [LBRouter.sol#L848](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L848)
- [LBRouter.sol#L964](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L964)

- [LBToken.sol#L21](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L21)
- [LBToken.sol#L122](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122)
- [LBToken.sol#L256](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L256)


## 10. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

- [LBToken.sol#L253](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L253)


## 11. abi.encode() is less efficient than abi.encodepacked()

- [LBFactory.sol#L265](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265)


## 12. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed


## 13. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [LBFactory.sol#L25](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25)
- [LBFactory.sol#L27](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L27)
- [LBFactory.sol#L28](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L28)
- [LBFactory.sol#L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L30)


## 14. don’t use safemath once the solidity version is 0.8.0 or greater

- [LBPair.sol#L17](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L17)
- [SwapHelper.sol#L10](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L10)


## 15. bytes constants are more efficient than string constants

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

- [LBToken.sol#L29](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29)
- [LBToken.sol#L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L30)


## 16. public library function should be made private/internal

Changing from public will remove the compiler-introduced checks for msg.value and decrease the contract’s method ID table size


## 17. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

- [LBQuoter.sol#L54](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54)
- [LBQuoter.sol#L134](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134)

- [LBRouter.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L90)

- [LBToken.sol#L49](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49)
- [LBToken.sol#L55](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55)
- [LBToken.sol#L63](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L63)
- [LBToken.sol#L79](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L79)
- [LBToken.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L100)
- [LBToken.sol#L107](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L107)
- [LBToken.sol#L115](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L115)
- [LBToken.sol#L122](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122)
- [LBToken.sol#L131](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L131)
