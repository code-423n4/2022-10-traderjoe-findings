# Gas Report
## Optimizations found [3]

**Summary Statement**
The main gas improvements found come from seperating conditions rather than using the binary operation &&. 

### [G-01] ++i is cheaper than i++ [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

Pre-incrementing saves gas relative to post-incrementing. A temporary variable must be used by the compiler with post-incrementing.

#### Findings:

*/src/LBQuoter.sol*
Line(s): [75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75), [100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100), [177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)
```solidity
75:	for (uint256 i; i < swapLength; i++) {
100:	for (uint256 j; j < LBPairsAvailable.length; j++) {
177:	for (uint256 j; j < LBPairsAvailable.length; j++) {
```


### [G-02] --i is cheaper than i-- [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-12-i-costs-less-gas-compared-to-i-or-i--1)

Same idea as [G-01]. Pre-incrementing saves gas relative to post-incrementing. A temporary variable must be used by the compiler with post-incrementing.

#### Findings:

*/src/LBQuoter.sol*
Line(s): [154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)
```solidity
154:	for (uint256 i = swapLength; i > 0; i--) {
```

### [G-03] Use nested if instead of && [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#6--use-nested-if-and-avoid-multiple-check-combinations)

Breaking apart if statements save gas.

#### Example Change:
*/src/LBQuoter.sol* [Line 79](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L79)

**From:**
```solidity
if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
    (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i], _route[i], _route[i + 1]);

    if (reserveIn > 0 && reserveOut > 0) {
        quote.amounts[i + 1] = JoeLibrary.getAmountOut(quote.amounts[i], reserveIn, reserveOut);
        quote.virtualAmountsWithoutSlippage[i + 1] = JoeLibrary.quote(
            (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
            reserveIn,
            reserveOut
        );
        quote.fees[i] = 0.003e18; // 0.3%
}
```
**To:**
```solidity
if (quote.pairs[i] != address(0)) {
    if (quote.amounts[i] > 0) {
        (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i], _route[i], _route[i + 1]);

        if (reserveIn > 0) {
		        if (reserveOut > 0) {
                quote.amounts[i + 1] = JoeLibrary.getAmountOut(quote.amounts[i], reserveIn, reserveOut);
                quote.virtualAmountsWithoutSlippage[i + 1] = JoeLibrary.quote(
                    (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
                    reserveIn,
                    reserveOut
                );
                quote.fees[i] = 0.003e18; // 0.3%
			      }
        }
    }
}
```

#### Findings:

*/src/LBQuoter.sol*
Line(s): [79](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L79), [82](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L82), [99](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L99), [157](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L157), [160](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L160), [176](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L176)
```solidity
79:	if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
82:	if (reserveIn > 0 && reserveOut > 0) {
99:	if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
157:	if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
160:	if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
176:	if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
```

*/src/LBRouter.sol*
Line(s): [243](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L243), [246](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L246), [662](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L662)
```solidity
243:	if (_liquidityParameters.tokenX == wavax && _liquidityParameters.amountX == msg.value) {
246:	} else if (_liquidityParameters.tokenY == wavax && _liquidityParameters.amountY == msg.value) {
662:	if (_liq.deltaIds.length != _liq.distributionX.length && _liq.deltaIds.length != _liq.distributionY.length)
```

*/src/libraries/Oracle.sol*
Line(s): [118](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L118)
```solidity
118:	if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {
```

*/src/libraries/TreeMath.sol*
Line(s): [33](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L33), [45](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L45)
```solidity
33:	if ((_rightSide && bit != 0) || (!_rightSide && bit != 255)) {
45:	if ((_rightSide && bit != 0) || (!_rightSide && bit != 255)) {
```

*/src/libraries/TokenHelper.sol*
Line(s): [76](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TokenHelper.sol#L76)
```solidity
76:	if (!(success && (result.length == 0 || abi.decode(result, (bool))))) {
```

*/src/LBFactory.sol*
Line(s): [241](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L241)
```solidity
241:	if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);
```

*/src/LBToken.sol*
Line(s): [284](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L284), [300](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L300)
```solidity
284:	if (_accountBalance == 0 && _amount != 0) {
300:	if (_accountBalance == _amount && _amount != 0) {
```

*/src/LBPair.sol*
Line(s): [278](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L278), [328](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L328), [500](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L500), [651](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L651), [820](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L820), [826](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L826)
```solidity
278:	if (_lastId >= _id && i != 0) revert LBPair__OnlyStrictlyIncreasingId();
328:	if ((!_swapForY && _bin.reserveX != 0) || (_swapForY && _bin.reserveY != 0)) {
500:	if (_bin.reserveX == 0 && _bin.reserveY == 0) _tree.addToTree(_mintInfo.id);
651:	if (_reserveX == 0 && _reserveY == 0) _tree.removeFromTree(_id);
820:	if (_from != address(0) && _from != address(this)) {
826:	if (_to != address(0) && _to != address(this)) {
```