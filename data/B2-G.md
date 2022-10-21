## Use `calldata` instead of `memory` for `external` functions where the function argument is read-only

The `memory` keyword in a function argument causes the underlying code to copy the argument into memory. `Calldata` causes the code to read the transaction without copying it directly, ultimately saves gases.

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313-L314
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495-L496
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L702
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L704
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L617-L618
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L688

## Use `unchecked` to save gas

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L130

## `Multiple` mappings can be combined into a `single` mapping  to a `struct`, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L45 & https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L58

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L18 & https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L24

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L21 & https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L27

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L68 & https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L70

## `require()` should be used instead of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a 
bug in your contract which you should fix”.

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141

##  `Cache` values that are used multiple times 
    
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L65-L70 // @audit `_route.length` is used multiple times.
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L43 // @audit `_pairBinSteps.length` is used multiple times
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L297-L303 // @audit `type(uint16).max` is used multiple times

## Avoid emitting `storage` variable

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L291

## Adding a `return` statement when the function defines a `named` `return` variable, is `redundant`

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994-L1008
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L466-L608
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L151-L163

