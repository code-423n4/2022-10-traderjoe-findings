# Gas Optimizations

### Using Bit Manipulation(Right Shift) for Division By 2

[Oracle.sol#L162](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L162)
`_middle = (_low + _high) / 2;` can be replaced with `_middle = (_low + _high) >> 1;` to save gas.

