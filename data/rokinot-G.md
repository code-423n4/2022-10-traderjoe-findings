### Functions that can only toggle the current condition of a storage variable can be simplified

Since these functions only switch a bool from true to false or vice versa, and revert if otherwise, instead of passing a boolean variable, change `creationUnlocked = !_locked;` to `creationUnlocked = !creationUnlocked;`

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L312-L317

In `setFactoryLockedState()`, you can remove the `_locked` if check, and the function argument.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L486

### `x += y` is a less efficient operation than `x = x + y`

Both do the same thing, however the later consumes less gas than the previous.

`LBPair.sol` has 22 instances of this issue.
`BitMath.sol` has 14 instances of this issue.
`SwapHelper.sol` has 8 instances of this issue.
[Math128x128.sol#L77](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L77)
[FeeDistributionHelper.sol#L42](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L42)
[Math512Bits.sol#L71](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L71)
[Math512Bits.sol#L99](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L99)
[Math512Bits.sol#L159](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L159)

The same applies for `x -= y`

`LBPair.sol` has 11 instances of this issue.
[LBToken.sol#L241](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L241)
[LBRouter.sol#L130]https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L130)
[LBRouter.sol#L173](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L173)
[SwapHelper.sol#L118-L127](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L118-L127)

### Unnecessary check

`initialize()` is a function which can only be called by the factory, and before calling the initializer, the first check was already done [here](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L252-L254) and [here](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L249), which the check inside the function is redundant.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L112

