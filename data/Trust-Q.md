# Issues

## 1. LBRouter's swapAVAXForExactTokens not working as intended

LBRouter's swapAVAXForExactTokens will only work when sending exact msg.value = amountIn[0]. The functionality which returns excess funds to the user in the last line has mistaken the subtracted and subtracted from amount:

```
if (msg.value > amountsIn[0]) 
    _safeTransferAVAX(_to, amountsIn[0] - msg.value);
```

This should be fixed as this will impact usability of the router.

## 2. LBRouter's _addLiquidity incorrect validation

_addLiquidity does not verify that the arrays it receives are of the same length. Should be || instead of && at the line below.

```
 if (_liq.deltaIds.length != _liq.distributionX.length && _liq.deltaIds.length != _liq.distributionY.length)
                revert LBRouter__LengthsMismatch();
```