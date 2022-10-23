## Issues found

### Cache Array Length Outside of Loop

```
src/LBPair.sol::481 => if (_ids.length == 0 || _ids.length != _distributionX.length || _ids.length != _distributionY.length)
src/LBPair.sol::493 => liquidityMinted = new uint256[](_ids.length);
src/LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
src/LBQuoter.sol::59 => if (_route.length < 2) {
src/LBQuoter.sol::65 => uint256 swapLength = _route.length - 1;
src/LBQuoter.sol::69 => quote.amounts = new uint256[](_route.length);
src/LBQuoter.sol::70 => quote.virtualAmountsWithoutSlippage = new uint256[](_route.length);
src/LBQuoter.sol::99 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
src/LBQuoter.sol::139 => if (_route.length < 2) {
src/LBQuoter.sol::144 => uint256 swapLength = _route.length - 1;
src/LBQuoter.sol::148 => quote.amounts = new uint256[](_route.length);
src/LBQuoter.sol::149 => quote.virtualAmountsWithoutSlippage = new uint256[](_route.length);
src/LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
src/LBRouter.sol::43 => if (_pairBinSteps.length == 0 || _pairBinSteps.length + 1 != _tokenPath.length)
src/LBRouter.sol::385 => if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
src/LBRouter.sol::386 => revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
src/LBRouter.sol::467 => if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
src/LBRouter.sol::468 => revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
src/LBRouter.sol::569 => if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
src/LBRouter.sol::570 => revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
src/LBRouter.sol::662 => if (_liq.deltaIds.length != _liq.distributionX.length && _liq.deltaIds.length != _liq.distributionY.length)
src/LBRouter.sol::673 => depositIds = new uint256[](_liq.deltaIds.length);
src/LBRouter.sol::709 => amountsIn[_pairs.length] = _amountOut;
src/LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
src/LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
src/LBRouter.sol::785 => _recipient = i + 1 == _pairs.length ? _to : _pairs[i + 1];
src/LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
src/LBRouter.sol::838 => _recipient = i + 1 == _pairs.length ? _to : _pairs[i + 1];
src/LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
src/LBRouter.sol::885 => _recipient = i + 1 == _pairs.length ? _to : _pairs[i + 1];
src/LBToken.sol::87 => batchBalances = new uint256[](_accounts.length);
src/LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

```
src/LBFactory.sol::161 => if (_nbPresets > 0) {
src/LBFactory.sol::191 => if (_nbAvailable > 0) {
src/LBQuoter.sol::79 => if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
src/LBQuoter.sol::82 => if (reserveIn > 0 && reserveOut > 0) {
src/LBQuoter.sol::99 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
src/LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {
src/LBQuoter.sol::157 => if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
src/LBQuoter.sol::160 => if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
src/LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
```