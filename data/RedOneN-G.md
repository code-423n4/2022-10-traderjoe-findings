## [G-1] Pre-increment (++i) costs less gas than Post-increment (i++), especially in for loops.

Pre-increment saves 5 gas per loop.

***File : LBQuoter.sol***

LBQuoter.sol#L75




## [G-2] It costs more gas to initialize variables to zero than to let the default of zero be applied.

if a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.


***File : Samples.sol***

Samples.sol#L19


## [G-3] ++i/i++ should be UNCHECKED{++i}/UNCHECKED{i++} when it is not possible for them to overflow (eg. in for and while loops).

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves around 40 gas per loop.

***File : LBFactory.sol***

LBFactory.sol#L165
LBFactory.sol#L195

***File : LBPair.sol***

LBPair.sol#L274
LBPair.sol#L496
LBPair.sol#L623
LBPair.sol#L701

***File : LBRouter.sol***

LBRouter.sol#L674
LBRouter.sol#L778
LBRouter.sol#L831
LBRouter.sol#L878
LBRouter.sol#L951

***File : LBToken.sol***

LBToken.sol#L90
LBToken.sol#L163

***File : LBQuoter.sol***

LBQuoter.sol#L75




## [G-4] Require() should be used instead of assert() to save gas left during a transaction.

using require instead of assert would allow to refund the remaining gas in case of error.

***File : LBFactory.sol***

LBFactory.sol#L141



## [G-5] Usage of UINT/INT smaller than 256bits incurs overhead.

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

***File : FeeHelper.sol***

FeeHelper.sol#L30:L41
FeeHelper.sol#L48
FeeHelper.sol#L49



## [G-6] Non-strict inequalities are cheaper than strict ones.

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas). This also holds true between <= and <.

Various instances across all the different Files/Contracts.


## [G-7] Functions guaranteed to revert when called by normal users (eg. onlyOwner) can be marked payable.

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

***File : PendingOwnable.sol***

PendingOwnable.sol#L68
PendingOwnable.sol#L59
PendingOwnable.sol#L75
PendingOwnable.sol#L84

***File : LBFactory.sol***

LBFactory.sol#L474
LBFactory.sol#L493
LBFactory.sol#L502
LBFactory.sol#L396
LBFactory.sol#L485
LBFactory.sol#L468
LBFactory.sol#L520
LBFactory.sol#L215

***File : LBPair.sol***

LBPair.sol#L788
LBPair.sol#L792




## [G-8] Using PRIVATE rather than PUBLIC for CONSTANTS saves GAS.

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

***File : LBFactory.sol***

LBFactory.sol#L25
LBFactory.sol#L27
LBFactory.sol#L28
LBFactory.sol#L30



## [G-9] multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

***File : LBPair.sol***

LBPair.sol#L68
LBPair.sol#L70

***File : LBToken.sol***

LBToken.sol#L21
LBToken.sol#L27




## [G-10] Use a more recent version of solidity.

Use a solidity version of at least 0.8.2 to get compiler automatic inlining,Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads,Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings,Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

