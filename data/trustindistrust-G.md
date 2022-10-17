### Remove unnecessary computation from `LBRouter.removeLiquidity()`

[Location](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L287)

#### Description

The function contains a swapping of the `_tokenX` and `_tokenY` values after a boolean test. However, the values themselves are never used after this point, rendering the inversion purposeless. It can be safely omitted. Only the swap between `_amountXMin`|`_amountYMin` is needed.

Test suite passed all checks after the change. Gas saved is 7 (675818 - 675811). The test `testRemoveLiquidityReverseOrder` was used to verify the change.