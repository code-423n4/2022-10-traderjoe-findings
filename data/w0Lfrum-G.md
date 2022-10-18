# Gas Optimizations

### Using Bit Manipulation(Right Shift) for Division By 2

[Oracle.sol#L162](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L162)

`_middle = (_low + _high) / 2;` can be replaced with `_middle = (_low + _high) >> 1;` to save gas.

### Using Unchecked Blocks and Pre-Increment in For Loops

[LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75), [LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100), [LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154), [LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)

Using Unchecked blocks along with pre-increment/pre-decrement in for loops saves gas.

For Example:

    for(uint256 i; i < arr.length; i++) {
        // lines of code
    }

Can instead be :

    for(uint256 i; i < arr.length;) {
        // lines of code
        unchecked {   // unchecked block to save gas
            ++i;            // pre-increment to save more gas
        }
    }

Example 2:

    for(uint256 i = arr.length ; i > 0; i--) {
        // lines of code
    }

Can instead be :

    for(uint256 i = arr.length; i > 0;) {
        // lines of code
        unchecked {   // unchecked block to save gas
            --i;            // pre-decrement to save more gas
        }
    }
