## [L-01]

In BitMath.sol `closestBit()` will revert on `closestBit(x, 0, true)` and `closestBit(x, 255, false)` - in the first case the search starts at the 0 bit and the second case the search starts at the 255 bit, this could be potentially dangerous if in the tree structure that represents the available liquidity bins it happens that we start the search at these specific positions in which case the function will revert unexpectedly. 

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L14-L22

## [L-02]

In `JoeLibrary.sol` there is the function `sortTokens(address tokenA, address tokenB)` and in `LBFactory.sol` there is the function `_sortTokens(IERC20 _tokenA, IERC20 _tokenB)`. Both these functions do the exact same thing with the only difference that in `JoeLibrary.sol` `sortTokens()` performs a zero address require. For Code Quality purposes the function from `JoeLibrary.sol` should be used and the one in `LBFactory.sol` can be omitted, moreover, since the sort function in JoeLibrary performs a zero address check, the zero address checks throughout `LBFactory.sol` after a token sort will be redundant and could be removed for better code structure.

`JoeLibrary.sol` code - https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L12-L16
`LBFactory.sol` code - https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L607-L610

## [L-03]

`removeLiquidity()` in `LBRouter.sol` performs a token re-arrange that serves no purpose. https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L287

## [L-04]

The zero address check in `LBPair.sol` `initialize()` is redundant since intialize() is called from `createLBPair()` in `LBFactory.sol` after a zero address check is performed during sorting the tokens. 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L111
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L252



