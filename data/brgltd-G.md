## Issues found

# [01] Prefix increment costs less gas than postfix increment

Saves roughly 5 gas per loop.

There are 5 instances of this issue.

```
File: src/LBQuoter.sol
75: for (uint256 i; i < swapLength; i++) {
100: for (uint256 j; j < LBPairsAvailable.length; j++) {
154: for (uint256 i = swapLength; i > 0; i--) {
177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

```
File: src/LBRouter.sol
711: for (uint256 i = _pairs.length; i != 0; i--) {
```

# [02] The increment/decrement in for loop post condition can be made unchecked to save gas

It can save 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked).

There are 5 instances of this issue.

```
File: src/LBQuoter.sol
75: for (uint256 i; i < swapLength; i++) {
100: for (uint256 j; j < LBPairsAvailable.length; j++) {
154: for (uint256 i = swapLength; i > 0; i--) {
177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

```
File: src/LBRouter.sol
711: for (uint256 i = _pairs.length; i != 0; i--) {
```

# [03] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There are 4 instances of this issue.

```
File: src/LBFactory.sol
25: uint256 public constant override MAX_FEE = 0.1e18; // 10%
27: uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
28: uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
30: uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```

# [04] x += y costs more gas than x = x + y for state variables

Using the addition operator instead of plus-equals saves 113 [gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8).

There are 13 instances of this issue.

```
File: src/LBPair.sol
452: _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
453: _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
717: _pairInformation.feesX.total -= uint128(amountX);
720: _pairInformation.feesY.total -= uint128(amountY);
```

```
File: src/LBRouter.sol
128: amountIn += _amountInWithFees;
129: feesIn += _fee;
130: _amountOut -= _amountOutOfBin;
173: _amountIn -= _amountInToBin + _fees.total;
174: feesIn += _fees.total;
175: amountOut += _amountOutOfBin;
```

```
File: src/LBToken.sol
211: _totalSupplies[_id] += _amount;
241: _totalSupplies[_id] -= _amount;
```

```
File: src/libraries/FeeDistributionHelper.sol
42: _pairFees.protocol += _fees.protocol;
```
