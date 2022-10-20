# LBPair.mint in L. 466:

The function documentation states that both `_distributionX` and `_distributionY` need to fulfill `sum(_distributionX) = 1e18 (100%) or 0 (0%)`. This is not asserted in the `mint` function itself (only that the sum is not bigger than 1e18) and neither in the `LBRouter._addLiquidity` function. This means that both values could also be somewhere inbetween. The consequences could be:
- `_mintInfo.amountXIn` and `_mintInfo.amountYin` are not used effectively 
- the transaction will revert in `LBRouter._addLiquidity` with `LBRouter__AmountSlippageCaught` due to not having added more than `_liq.amountXMin` or `_liq.amountYMin` 