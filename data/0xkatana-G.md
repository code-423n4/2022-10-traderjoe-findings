# [G-01] Redundant zero initialization

Solidity does not recognize null as a value, so uint variables are initialized to zero. Setting a uint variable to zero is redundant and can waste gas.

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L19

## Recommended Mitigation Steps

Remove the redundant zero initialization
`uint256 i;` instead of `uint256 i = 0;`

# [G-02] Use != 0 instead of > 0

Using `> 0` uses slightly more gas than using `!= 0`. Use `!= 0` when comparing uint variables to zero, which cannot hold values below zero

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L161
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L191
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L79
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L82
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L99
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L157
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L160
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L176

## Recommended Mitigation Steps

Replace `> 0` with `!= 0` to save gas

# [G-03] Use prefix not postfix in loops

Using a prefix increment (++i) instead of a postfix increment (i++) saves gas for each loop cycle and so can have a big gas impact when the loop executes on a large number of elements.

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711

## Recommended Mitigation Steps

Use prefix not postfix to increment in a loop

# [G-04] For loop incrementing can be unsafe

For loops that use i++ do not need to use safemath for this operation because the loop would run out of gas long before this point. Making this addition operation unsafe using unchecked saves gas.

Sample code to make the for loop increment unsafe
```
for (uint i = 0; i < length; i = unchecked_inc(i)) {
    // do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
    unchecked {
        return i + 1;
    }
}
```

Idea borrowed from
https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711

## Recommended Mitigation Steps

Make the increment in for loops unsafe to save gas

# [G-05] Use iszero assembly for zero checks

Comparing a value to zero can be done using the `iszero` EVM opcode. This can save gas

Source from t11s https://twitter.com/transmissions11/status/1474465495243898885

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L370
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L184
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L188
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L316
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L377
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L391
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L481
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L500
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L563
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L627
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L651
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L43
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L97
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L717
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L787
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L840
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L887
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L935
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L284
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L194
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L199
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L76
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol#L87
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol#L92
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BinHelper.sol#L52
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L44
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L60
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L48
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L58
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L24
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L25
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L35
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L36
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L49
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L50
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L42
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L98
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L200

## Recommended Mitigation Steps

Use the assembly `iszero` evm opcode to compare values to zero

# [G-06] Add payable to functions that won't receive ETH

Identifying a function as payable saves gas. Functions that have a modifier like onlyOwner cannot be called by normal users and will not mistakenly receive ETH. These functions can be payable to save gas.

There are many functions that have access modifiers in the contracts. Some examples are
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L317
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L350
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L396
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L434
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L468
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L474
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L485
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L493
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L502
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L520

## Recommended Mitigation Steps

Add payable to these functions for gas savings

# [G-07] Use internal function in place of modifier

An internal function can save gas vs. a modifier. A modifier inlines the code of the original function but an internal function does not.

Source https://blog.polymath.network/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size-c44580b218e6#dde7

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L43
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L32
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L37
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L42
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L32
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L37
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L42
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L29
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L35
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol#L42

## Recommended Mitigation Steps

Use internal functions in place of modifiers to save gas.

# [G-08] Use uint not bool

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L39
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L316
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L485
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L102
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L179
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L237
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L243
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L304
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L93
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L151
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L320
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L798
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L848
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L964
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L122
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L256

## Recommended Mitigation Steps

Replace bool variables with uints

# [G-09] Use newer solidity version

Solidity version before 0.8.0 is used in this project. The latest release of solidity includes changes that can provide gas savings. The improvements include:

* [Low level inliner](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/) from `0.8.2`, leads to cheaper runtime gas. Especially relevant when the contract has small functions. For example, OpenZeppelin libraries typically have a lot of small helper functions and if they are not inlined, they cost an additional 20 to 40 gas because of 2 extra `jump` instructions and additional stack operations needed for function calls.
* [Optimizer improvements in packed structs](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/#optimizer-improvements): Before `0.8.3`, storing packed structs, in some cases used an additional storage read operation. After [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929), if the slot was already cold, this means unnecessary stack operations and extra deploy time costs. However, if the slot was already warm, this means additional cost of `100` gas alongside the same unnecessary stack operations and extra deploy time costs.
* [Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors) from `0.8.4`, leads to cheaper deploy time cost and run time cost. Note: the run time cost is only relevant when the revert condition is met. In short, replace revert strings by custom errors.
* Solidity version 0.8.13 can save more gas with [Yul IR pipeline](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/#yul-ir-pipeline-production-ready)

Source https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#upgrade-to-at-least-084

## Recommended Mitigation Steps

Use solidity release 0.8.13 with Yul IR pipeline and other improvements for gas savings

# [G-10] Use simple comparison in if statement

The comparison operators >= and <= use more gas than >, <, or ==. Replacing the  >= and ≤ operators with a comparison operator that has an opcode in the EVM saves gas

The existing code is 
https://github.com/code-423n4/ 
```
if (balance <= staked) {
    epoch.distribute = 0;
} else {
    epoch.distribute = balance - staked;
}
```

A simple comparison can be used for gas savings by reversing the logic
```
if (balance > staked) {
    epoch.distribute = balance - staked;
} else {
    epoch.distribute = 0;
}
```

## Recommended Mitigation Steps

Replace the comparison operator and reverse the logic to save gas using the suggestions above

# [G-11] Bitshift for divide by 2

When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.

There is a divide by 2 operation on these lines
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L162

## Recommended Mitigation Steps

Bitshift right by one bit instead of dividing by 2 to save gas

# [G-12] Non-public variables save gas

Many constant variables are public, but changing the visibility of these variables to private or internal can save gas.

Locations where this was found include
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L25
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L27
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L28
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L30
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L21
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L23
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L25
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L50
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L28
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L29
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L30

## Recommended Mitigation Steps

Declare some public variables as private or internal to save gas

# [G-13] Write contracts in vyper

The contracts are all written entirely in solidity. Writing contracts with vyper instead of solidity can save gas.

Source https://twitter.com/eiber_david/status/1515737811881807876
doggo demonstrates https://twitter.com/fubuloubu/status/1528179581974417414?t=-hcq_26JFDaHdAQZ-wYxCA&s=19

## Recommended Mitigation Steps

Write some or all of the contracts in vyper to save gas