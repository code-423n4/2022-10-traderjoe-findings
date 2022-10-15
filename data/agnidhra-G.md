### Don't Initialize Variables with Default Value

```
s/EnumerableSet.sol::342 => * - `index` must be strictly less than {length}.
2022-10-traderjoe/src/LBFactory.sol::73 => return allLBPairs.length;
2022-10-traderjoe/src/LBFactory.sol::79 => return _quoteAssetWhitelist.length();
2022-10-traderjoe/src/LBFactory.sol::291 => emit LBPairCreated(_tokenX, _tokenY, _binStep, _LBPair, allLBPairs.length - 1);
2022-10-traderjoe/src/LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::481 => if (_ids.length == 0 || _ids.length != _distributionX.length || _ids.length != _distributionY.length)
2022-10-traderjoe/src/LBPair.sol::493 => liquidityMinted = new uint256[](_ids.length);
2022-10-traderjoe/src/LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::677 => /// @notice Increase the length of the oracle
2022-10-traderjoe/src/LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBQuoter.sol::59 => if (_route.length < 2) {
2022-10-traderjoe/src/LBQuoter.sol::65 => uint256 swapLength = _route.length - 1;
2022-10-traderjoe/src/LBQuoter.sol::69 => quote.amounts = new uint256[](_route.length);
2022-10-traderjoe/src/LBQuoter.sol::70 => quote.virtualAmountsWithoutSlippage = new uint256[](_route.length);
2022-10-traderjoe/src/LBQuoter.sol::99 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
2022-10-traderjoe/src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBQuoter.sol::139 => if (_route.length < 2) {
2022-10-traderjoe/src/LBQuoter.sol::144 => uint256 swapLength = _route.length - 1;
2022-10-traderjoe/src/LBQuoter.sol::148 => quote.amounts = new uint256[](_route.length);
2022-10-traderjoe/src/LBQuoter.sol::149 => quote.virtualAmountsWithoutSlippage = new uint256[](_route.length);
2022-10-traderjoe/src/LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
2022-10-traderjoe/src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBRouter.sol::43 => if (_pairBinSteps.length == 0 || _pairBinSteps.length + 1 != _tokenPath.length)
2022-10-traderjoe/src/LBRouter.sol::385 => if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
2022-10-traderjoe/src/LBRouter.sol::386 => revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
2022-10-traderjoe/src/LBRouter.sol::467 => if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
2022-10-traderjoe/src/LBRouter.sol::468 => revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
2022-10-traderjoe/src/LBRouter.sol::541 => IERC20 _targetToken = _tokenPath[_pairs.length];
2022-10-traderjoe/src/LBRouter.sol::569 => if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
2022-10-traderjoe/src/LBRouter.sol::570 => revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
2022-10-traderjoe/src/LBRouter.sol::605 => IERC20 _targetToken = _tokenPath[_pairs.length];
2022-10-traderjoe/src/LBRouter.sol::662 => if (_liq.deltaIds.length != _liq.distributionX.length && _liq.deltaIds.length != _liq.distributionY.length)
2022-10-traderjoe/src/LBRouter.sol::673 => depositIds = new uint256[](_liq.deltaIds.length);
2022-10-traderjoe/src/LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::707 => amountsIn = new uint256[](_tokenPath.length);
2022-10-traderjoe/src/LBRouter.sol::708 => // Avoid doing -1, as `_pairs.length == _pairBinSteps.length-1`
2022-10-traderjoe/src/LBRouter.sol::709 => amountsIn[_pairs.length] = _amountOut;
2022-10-traderjoe/src/LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
2022-10-traderjoe/src/LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::785 => _recipient = i + 1 == _pairs.length ? _to : _pairs[i + 1];
2022-10-traderjoe/src/LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::838 => _recipient = i + 1 == _pairs.length ? _to : _pairs[i + 1];
2022-10-traderjoe/src/LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::885 => _recipient = i + 1 == _pairs.length ? _to : _pairs[i + 1];
2022-10-traderjoe/src/LBRouter.sol::946 => pairs = new address[](_pairBinSteps.length);
2022-10-traderjoe/src/LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
2022-10-traderjoe/src/LBToken.sol::42 => modifier checkLength(uint256 _lengthA, uint256 _lengthB) {
2022-10-traderjoe/src/LBToken.sol::43 => if (_lengthA != _lengthB) revert LBToken__LengthMismatch(_lengthA, _lengthB);
2022-10-traderjoe/src/LBToken.sol::84 => checkLength(_accounts.length, _ids.length)
2022-10-traderjoe/src/LBToken.sol::87 => batchBalances = new uint256[](_accounts.length);
2022-10-traderjoe/src/LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
2022-10-traderjoe/src/LBToken.sol::108 => return _userIds[_account].length();
2022-10-traderjoe/src/LBToken.sol::158 => checkLength(_ids.length, _amounts.length)
2022-10-traderjoe/src/LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/libraries/TokenHelper.sol::76 => if (!(success && (result.length == 0 || abi.decode(result, (bool))))) {
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::111 => assertEq(binSteps.length, 3);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::118 => assertEq(binSteps.length, 4);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::126 => assertEq(binSteps.length, 3);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::137 => assertEq(LBPairBinSteps.length, 3);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::146 => assertEq(LBPairBinStepsReversed.length, 3);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::158 => assertEq(LBPairBinStepsAfterPresetRemoval.length, 3);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::167 => assertEq(LBPairBinStepsAfterIgnored.length, 3);
2022-10-traderjoe/test/LBFactory.MultiPools.t.sol::178 => assertEq(LBPairBinStepsAfterRemovalOfIgnored.length, 3);
2022-10-traderjoe/test/LBFactory.t.sol::59 => assertEq(factory.getAllLBPairs(token6D, token18D).length, 0);
2022-10-traderjoe/test/LBFactory.t.sol::61 => assertEq(factory.getAllLBPairs(token6D, token18D).length, 1);
2022-10-traderjoe/test/LBFactory.t.sol::64 => assertEq(factory.getAllLBPairs(token6D, token18D).length, 2);
2022-10-traderjoe/test/LBFactory.t.sol::81 => assertEq(factory.getAllLBPairs(token6D, token18D).length, 3);
2022-10-traderjoe/test/LBFactory.t.sol::154 => assertEq(LBPairBinSteps.length, 1);
2022-10-traderjoe/test/LBToken.t.sol::202 => vm.expectRevert(abi.encodeWithSelector(LBToken__LengthMismatch.selector, _ids.length, amounts.length));
2022-10-traderjoe/test/LBToken.t.sol::209 => vm.expectRevert(abi.encodeWithSelector(LBToken__LengthMismatch.selector, accounts.length, _ids.length));
2022-10-traderjoe/test/mocks/Faucet.sol::71 => return faucetTokens.length;
2022-10-traderjoe/test/mocks/Faucet.sol::108 => uint256 lastIndex = faucetTokens.length - 1;
2022-10-traderjoe/test/mocks/Faucet.sol::160 => uint256 len = faucetTokens.length;
2022-10-traderjoe/test/mocks/Faucet.sol::183 => tokenToIndices[_token.ERC20] = faucetTokens.length;
```
### Use != 0 instead of > 0 for Unsigned Integer Comparison

```

2022-10-traderjoe/src/LBFactory.sol::161 => if (_nbPresets > 0) {
2022-10-traderjoe/src/LBFactory.sol::191 => if (_nbAvailable > 0) {
2022-10-traderjoe/src/LBQuoter.sol::79 => if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
2022-10-traderjoe/src/LBQuoter.sol::82 => if (reserveIn > 0 && reserveOut > 0) {
2022-10-traderjoe/src/LBQuoter.sol::99 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
2022-10-traderjoe/src/LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {
2022-10-traderjoe/src/LBQuoter.sol::157 => if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
2022-10-traderjoe/src/LBQuoter.sol::160 => if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
2022-10-traderjoe/src/LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
2022-10-traderjoe/src/libraries/Math128x128.sol::71 => for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta > 0; delta >>= 1) {
2022-10-traderjoe/test/LBFactory.t.sol::413 => vm.assume(baseFactorIncrement > 0);
2022-10-traderjoe/test/LBFactory.t.sol::438 => vm.assume(maxVolatilityIncrement > 0);
2022-10-traderjoe/test/LBPair.Fees.t.sol::226 => vm.assume(amountY > 0);
2022-10-traderjoe/test/LBPair.Fees.t.sol::228 => vm.assume(amountX > 0);
2022-10-traderjoe/test/LBTokenInternal.t.sol::47 => vm.assume(excessiveBurnAmount > 0);
2022-10-traderjoe/test/LBTokenInternal.t.sol::56 => vm.assume(mintAmount > 0 && burnAmount > 0);
2022-10-traderjoe/test/TestHelper.sol::167 => amountXIn += binLiquidity > 0
2022-10-traderjoe/test/mocks/Faucet.sol::164 => if (token.amountPerRequest > 0 && address(this).balance >= token.amountPerRequest) {
2022-10-traderjoe/test/mocks/Faucet.sol::171 => if (token.amountPerRequest > 0 && token.ERC20.balanceOf(address(this)) >= token.amountPerRequest)
```

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

```

2022-10-traderjoe/src/LBFactory.sol::265 => bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
2022-10-traderjoe/src/LBPair.sol::657 => let slot := keccak256(0, 64)
2022-10-traderjoe/src/LBPair.sol::1001 => _data := sload(keccak256(0, 64))
2022-10-traderjoe/test/Faucet.t.sol::13 => address internal constant ALICE = address(bytes20(bytes32(keccak256(bytes("ALICE")))));
2022-10-traderjoe/test/Faucet.t.sol::14 => address internal constant BOB = address(bytes20(bytes32(keccak256(bytes("BOB")))));
2022-10-traderjoe/test/Faucet.t.sol::15 => address internal constant OPERATOR = address(bytes20(bytes32(keccak256(bytes("OPERATOR")))));
```

### Long Revert Strings

```
2022-10-traderjoe/src/LBFactory.sol::30 => uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
2022-10-traderjoe/src/LBFactory.sol::560 => // Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
2022-10-traderjoe/src/LBFactory.sol::562 => // decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
2022-10-traderjoe/src/libraries/BinHelper.sol::26 => // can't overflow as `2**23 + log2(price) < 2**23 + 2**128 < max(uint256)`
2022-10-traderjoe/src/libraries/FeeHelper.sol::102 => // Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
2022-10-traderjoe/src/libraries/FeeHelper.sol::104 => // decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
2022-10-traderjoe/src/libraries/Math128x128.sol::37 => // If we use an offset of 128 bits, y would need 129 bits and y**2 would would overflow and we would have to
2022-10-traderjoe/src/libraries/Math128x128.sol::57 => // Calculate the integer part of the logarithm and add it to the result and finally calculate y = x * 2^(-n).
2022-10-traderjoe/src/libraries/Math128x128.sol::64 => // This is y = x * 2^(-n).
2022-10-traderjoe/src/libraries/Math128x128.sol::79 => // Corresponds to z/2 on Wikipedia.
2022-10-traderjoe/src/libraries/Math512Bits.sol::17 => /// @dev Credit to Remco Bloemen under MIT license https://xn--2-umb.com/21/muldiv
2022-10-traderjoe/src/libraries/Math512Bits.sol::43 => /// @dev Credit to Remco Bloemen under MIT license https://xn--2-umb.com/21/muldiv
2022-10-traderjoe/src/libraries/Math512Bits.sol::79 => /// @dev Credit to Remco Bloemen under MIT license https://xn--2-umb.com/21/muldiv
2022-10-traderjoe/src/libraries/Math512Bits.sol::106 => /// @dev Credit to Remco Bloemen under MIT license https://xn--2-umb.com/21/muldiv
2022-10-traderjoe/src/libraries/Math512Bits.sol::139 => /// @dev Credit to Remco Bloemen under MIT license https://xn--2-umb.com/21/muldiv
2022-10-traderjoe/src/libraries/Math512Bits.sol::171 => // variables such that product = prod1 * 2^256 + prod0.
2022-10-traderjoe/src/libraries/Oracle.sol::162 => _middle = (_low + _high) / 2;
2022-10-traderjoe/src/libraries/TreeMath.sol::105 => // Optimization of `(_id / 256, _id % 256)`
2022-10-traderjoe/src/libraries/TreeMath.sol::114 => // Optimization of `_branchId * 256 + _leafId`
2022-10-traderjoe/test/LBFactory.t.sol::68 => DEFAULT_BASE_FACTOR / 4,
2022-10-traderjoe/test/LBFactory.t.sol::72 => DEFAULT_VARIABLE_FEE_CONTROL / 4,
2022-10-traderjoe/test/LBPair.Fees.t.sol::200 => (uint256 amountYInForSwap, ) = router.getSwapIn(pair, amountYInLiquidity / 4, true);
2022-10-traderjoe/test/LBPair.Fees.t.sol::207 => (amountYInForSwap, ) = router.getSwapIn(pair, amountYInLiquidity / 4, true);
2022-10-traderjoe/test/LBPair.Liquidity.t.sol::70 => vm.assume(_price < 2**239);
2022-10-traderjoe/test/LBPair.Liquidity.t.sol::105 => assertApproxEqRel(currentBinReserveX, amountXIn / 2, 1e3, "currentBinReserveX");
2022-10-traderjoe/test/LBPair.Liquidity.t.sol::106 => assertApproxEqRel(currentBinReserveY, amountYIn / 2, 1e3, "currentBinReserveY");
2022-10-traderjoe/test/LBPair.Liquidity.t.sol::109 => assertApproxEqRel(binYReserve1, amountYIn / 2, 1e3, "binYReserve1");
2022-10-traderjoe/test/LBPair.Liquidity.t.sol::112 => assertApproxEqRel(binXReserve0, amountXIn / 2, 1e3, "binXReserve1");
2022-10-traderjoe/test/LBPair.Liquidity.t.sol::124 => ) = spreadLiquidity(amount1In * 2, ID_ONE, 5, 0);
2022-10-traderjoe/test/LBPair.Oracle.t.sol::140 => ) = spreadLiquidity(amount1In * 2, ID_ONE, 99, 100);
2022-10-traderjoe/test/LBPair.Oracle.t.sol::181 => ) = spreadLiquidity(amount1In * 2, ID_ONE, 99, 100);
2022-10-traderjoe/test/LBPair.Oracle.t.sol::225 => (uint256 amountYInForSwap, ) = router.getSwapIn(pair, amountYInLiquidity / 4, true);
2022-10-traderjoe/test/LBPair.t.sol::78 => _distributionX[0] = Constants.PRECISION / 2;
2022-10-traderjoe/test/LBPair.t.sol::79 => _distributionX[1] = Constants.PRECISION / 2 + 1;
2022-10-traderjoe/test/LBPair.t.sol::85 => _distributionY[0] = Constants.PRECISION / 2;
2022-10-traderjoe/test/LBPair.t.sol::86 => _distributionY[1] = Constants.PRECISION / 2 + 1;
2022-10-traderjoe/test/LBRouter.Liquidity.t.sol::362 => assertEq(taxToken.balanceOf(DEV), amountTokenIn / 4 + 1); //2 transfers with 50% tax
2022-10-traderjoe/test/LBRouter.Swaps.t.sol::212 => amountOut = amountOut / 2;
2022-10-traderjoe/test/LBRouter.Swaps.t.sol::241 => amountOut = amountOut / 2;
2022-10-traderjoe/test/LBRouter.Swaps.t.sol::280 => amountOut = amountOut / 2;
2022-10-traderjoe/test/LBRouter.Swaps.t.sol::314 => amountOut = amountOut / 2;
2022-10-traderjoe/test/LBToken.t.sol::231 => for (uint256 i; i < binAmount / 2; i++) {
2022-10-traderjoe/test/LBToken.t.sol::232 => _ids[i] = _startId - (binAmount / 2) * (1 + _gap) + i * (1 + _gap);
2022-10-traderjoe/test/LBTokenInternal.t.sol::24 => _mint(address(0), 2**23, 1000);
2022-10-traderjoe/test/LBTokenInternal.t.sol::29 => uint256 binNumber = 2**23;
2022-10-traderjoe/test/LBTokenInternal.t.sol::43 => _burn(address(0), 2**23, 1000);
2022-10-traderjoe/test/LBTokenInternal.t.sol::49 => uint256 binNumber = 2**23;
2022-10-traderjoe/test/LBTokenInternal.t.sol::59 => uint256 binNumber = 2**23;
2022-10-traderjoe/test/TestHelper.sol::26 => uint24 internal constant ID_ONE = 2**23;
2022-10-traderjoe/test/TestHelper.sol::151 => uint24 spread = _numberBins / 2;
2022-10-traderjoe/test/TestHelper.sol::253 => uint256 spread = _numberBins / 2;
2022-10-traderjoe/test/mocks/ERC20WithTransferTax.sol::28 => uint256 tax = amount / 2;
2022-10-traderjoe/test/mocks/ERC20WithTransferTax.sol::35 => uint256 tax = amount / 2;
```
