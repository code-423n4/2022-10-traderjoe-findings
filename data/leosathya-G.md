### [G-01] Multiple address mapping can be combined into a single mapping of an address to a struct, where appropriate 

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

There are 3 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L18
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L19
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L45


### [G-02] Using Bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the  slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L19

#### Recommended Mitigation

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past



### [G-3] Some public function could be external for saving gas

There is 10 instance of this issue :: 

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49 
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L63
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L71
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L79
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L100
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L107
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L115
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L136
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L149



### [G-4] >= costs less gas than >

There is 1 instance of this issue :: 

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L184


### [G-05] <x> += <y> costs more gas than <x> = <x> + <y> for state variables 

There are 29 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L212 
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L242
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L128
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L129
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L130
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L173
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L174
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L175
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L149
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L116
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L120
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L124
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L128
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L132
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L136
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L139
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L42
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L71
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L159
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L88
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L91
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L95
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L97
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L115
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L118
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L119
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L120
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L123
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L126
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L127
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L128



### [G-06] Loop can be more optimizable

There are 2 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L165
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195

#### Recommended Mitigation

. Should not initialize uint with default value i.e uint i=0 **TO** uint i;
. Should use ++i instead i++
. Should uncheck i++


### [G-07] State Variable should first cached to memory, then used further

There are 11 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L165
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol#L52
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol#L56
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L49
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L54
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L58
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L62
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L72
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L71
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L75



### [G-08] Divide by 2 should be bit-shift. If possible try to use bit-shift in replace of multiplication and division

There is 4 instance of this issue::

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol#L27
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol#L56
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L196
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol



### [G-09] Rather than using exact value expression assign to constant state variable

There is 3 instance of this issue :: 

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Constants.sol#L10
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L18
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L19


### [G-10] Use of custom errors are more efficient than require()/revert() with error message 

There are Multiple  instances of this issue:

#### Recommended Mitigation

require() with long error message length more than 32bytes can replace with custom error message to save gas.


### [G-11] By making some public variable private more gas can be saved



### [G-12] Function that guaranteed to revert when called by normal user can be marked payable 

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 



### [G-13] Unchecking those arithmetic operation which can't overflow or underflow can save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic 

