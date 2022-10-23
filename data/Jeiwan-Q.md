# Incorrect comment of `_swapForY` argument
## Targets
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L300
## Impact
The incorrect comment can confuse developers.
## Proof of Concept
The comment says:
> /// @param _swapForY whether the token sent was Y (true) or X (false)

However, `_swapForY` is true when the token sent is X and the token bought is Y. This can be seen from [this piece of code](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L406-L412):
```solidity
if (_swapForY) {
    amountYOut = _amountOut;
    tokenY.safeTransfer(_to, _amountOut);
} else {
    amountXOut = _amountOut;
    tokenX.safeTransfer(_to, _amountOut);
}
```
## Tools Used
Manual review.
## Recommended Mitigation Steps
Fix the comment, e.g. [use this one](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L241).