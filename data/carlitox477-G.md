# LBFactory#getAllBinSteps can avoid double comparisson
Replace lines at [LBFactory#getAllBinSteps](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L165) and [LBFactory#getAllBinSteps](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195) for

```solidity
uint256 upper_bound = MAX_BIN_STEP + 1
for (uint256 i = MIN_BIN_STEP; i < upper_bound; ++i) {
```

This would avoid two comparison operation in each iteration due to operator **<=**

# unchecked{++i} costs less gas than i++
There are 4 instances of this issue:
```
File: contracts/LBQuoter.sol
75:         for (uint256 i; i < swapLength; i++) {
100:        for (uint256 j; j < LBPairsAvailable.length; j++) {
154:        for (uint256 i = swapLength; i > 0; i--) {
177:        for (uint256 j; j < LBPairsAvailable.length; j++) {

File: contracts/LBRouter.sol
711:        for (uint256 i = _pairs.length; i != 0; i--) {
```

# Check msg.value != 0 to avoid further processing
This can be done at the start of functions:
* [LBRouter#swapExactAVAXForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L407)
* [LBRouter#swapAVAXForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L493)
* [LBRouter#addLiquidityAVAX](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L230)