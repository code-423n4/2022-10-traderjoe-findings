# 1. [G-1] For loops: ++i (--i) cost less gas compare to i++ (i--)

Instances include:

    File src/LBQuoter.sol, line 75:       for (uint256 i; i < swapLength; i++)
    File src/LBQuoter.sol, line 100:       for (uint256 j; j < LBPairsAvailable.length; j++)
    File src/LBQuoter.sol, line 154:      for (uint256 i = swapLength; i > 0; i--)
    File src/LBQuoter.sol, line 177:       for (uint256 j; j < LBPairsAvailable.length; j++)
    File src/LBRouter.sol, line 711:       for (uint256 i = _pairs.length; i != 0; i--)

I suggest using `++i` (or `++j`) instead of `i++` (or `j++`) to increment the value of an uint variable.

# 2. [G-2] For loops: Cache the length of arrays in the loops to save gas

    File src/LBPair.sol, line 274:       for (uint256 i; i < _ids.length; ++i)
    File src/LBPair.sol, line 496:       for (uint256 i; i < _ids.length; ++i)
    File src/LBPair.sol, line 623:       for (uint256 i; i < _ids.length; ++i)
    File src/LBPair.sol, line 701:       for (uint256 i; i < _ids.length; ++i)

    File src/LBQuoter.sol, line 154:       for (uint256 j; j < LBPairsAvailable.length; j++)
    File src/LBQuoter.sol, line 177:       for (uint256 j; j < LBPairsAvailable.length; j++)

    File src/LBRouter.sol, line 674:       for (uint256 i; i < depositIds.length; ++i)
    File src/LBRouter.sol, line 711:       for (uint256 i = _pairs.length; i != 0; i--)
    File src/LBRouter.sol, line 778:       for (uint256 i; i < _pairs.length; ++i)
    File src/LBRouter.sol, line 831:       for (uint256 i; i < _pairs.length; ++i)
    File src/LBRouter.sol, line 878:       for (uint256 i; i < _pairs.length; ++i)
    File src/LBRouter.sol, line 951:       for (uint256 i; i < pairs.length; ++i)

    File src/LBToken.sol, line 90:       for (uint256 i; i < _accounts.length; ++i)
    File src/LBToken.sol, line 163:       for (uint256 i; i < _ids.length; ++i)

I suggest storing the array’s length in a variable before the for-loop, and use it instead.

# 3. [G-3] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File src/LBQuoter.sol, line 75:       for (uint256 i; i < swapLength; i++)
    File src/LBQuoter.sol, line 154:       for (uint256 j; j < LBPairsAvailable.length; j++)

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    unchecked {
      for (uint256 i; i < numIterations; ++i) { 
        ...
      }
    }


# 4. [G-4] Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Instances include:

    File src/libraries/Samples.sol, line 19:       uint256 private constant _OFFSET_INITIALIZED = 0;

I suggest removing explicit initializations for default values.

# 5. [G-5] Variables: "Constant" expressions are expressions, not constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Instances include:

    File src/libraries/BinHelper.sol, line 14:       int256 private constant REAL_ID_SHIFT = 1 << 23;
    File src/libraries/Constants.sol, line 10:       uint256 internal constant SCALE = 1 << SCALE_OFFSET;
    File src/libraries/Math128x128.sol, line 18:       uint256 constant LOG_SCALE = 1 << LOG_SCALE_OFFSET;
    File src/libraries/Math128x128.sol, line 19:       uint256 constant LOG_SCALE_SQUARED = LOG_SCALE * LOG_SCALE;

Change these expressions from `constant` to `immutable` and implement the calculation in the constructor, or hardcode these values in the constants and add a comment to say how the value was calculated.

# 6. [G-6] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File src/LBPair.sol, line 189:     max = oracleSampleLifetime * oracleActiveSize;
    File src/LBPair.sol, line 336-337:     _amountIn -= _amountInToBin + _fees.total;
                                                           _amountOut += _amountOutOfBin;

    File src/LBRouter.sol, line 125-130:    _amountInWithFees = _amountInToBin + _fee;
                                                               if (_amountInWithFees + _reserve > type(uint112).max) revert LBRouter__SwapOverflows(_activeId);
                                                               amountIn += _amountInWithFees;
                                                               feesIn += _fee;
                                                               _amountOut -= _amountOutOfBin;

    File src/LBToken.sol, line 211:     _totalSupplies[_id] += _amount;

# 7. [G-7] Variables: Cache read variables in memory will save gas

    File src/LBFactory.sol, line 195-197:      
                                                              for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
                                                                  if (_avLBPairBinSteps.decode(1, i) == 1) {
                                                                       LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];
                                                                       ...
                                                                   }
                                                              }    // I suggest create a memory variable of the storage variable `_LBPairsInfo` outside for loop, and use it instead.

    File src/LBPair.sol, line 274-283:      
                                                              for (uint256 i; i < _ids.length; ++i) {
                                                                  ....
                                                                       Bin memory _bin = _bins[_id];
                                                                  ...
                                                              }    // I suggest create a memory variable of the storage variable `_bins` outside for loop, and use it instead.
    File src/LBPair.sol, line 326-327:      
                                                              while (true) {
                                                                  Bin memory _bin = _bins[_pair.activeId];
                                                                  ...
                                                              }    // I suggest create a memory variable of the storage variable `_bins` outside while loop, and use it instead.
    File src/LBPair.sol, line 496-498:      
                                                              for (uint256 i; i < _ids.length; ++i) {
                                                                  ...
                                                                  Bin memory _bin = _bins[_mintInfo.id];
                                                                  ...
                                                              }    // I suggest create a memory variable of the storage variable `_bins` outside while loop, and use it instead.
    File src/LBPair.sol, line 701-706:      
                                                              for (uint256 i; i < _ids.length; ++i) {
                                                                  ...
                                                                     Bin memory _bin = _bins[_id];
                                                                  ...
                                                              }    // I suggest create a memory variable of the storage variable `_bins` outside while loop, and use it instead.
    File src/LBPair.sol, line 929-930:      
                                                              for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {
                                                                  _oracle.initialize(_id);
                                                              }    // I suggest create a memory variable of the storage variable `_oracle` outside while loop, and use it instead.

    File src/LBQuoter.sol, line 75-77-94:      
                                                              for (uint256 i; i < swapLength; i++) {
                                                                  quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);
                                                                  ...
                                                                  ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(
                                                                  ...
                                                              }    // I suggest create memory variables of the storage variable `factoryV1` and `factoryV2` outside while loop, and use them instead.
    File src/LBPair.sol, line 154-156:      
                                                              for (uint256 i = swapLength; i > 0; i--) {
                                                                  quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]);
                                                                  ...
                                                              }    // I suggest create a memory variable of the storage variable `factoryV1` outside while loop, and use it instead.

# 8. [G-8] Parameter: If we are not modifying the passed parameter we should pass it as `calldata` because `calldata` is more gas efficient than `memory`.

I suggest using `calldata` instead of `memory` here:

    File src/LBPair.sol, line 261:     function pendingFees(address _account, uint256[] memory _ids)
    File src/LBPair.sol, line 466:     function mint(
                                                                uint256[] memory _ids,
                                                                uint256[] memory _distributionX,
                                                                uint256[] memory _distributionY,
                                                                address _to
                                                      )
    File src/LBPair.sol, line 616:     function burn(
                                                                uint256[] memory _ids,
                                                                uint256[] memory _amounts,
                                                                address _to
                                                      )
    File src/LBPair.sol, line 688:     function collectFees(address _account, uint256[] memory _ids)
    File src/LBPair.sol, line 844:     function _getPendingFees(
                                                                Bin memory _bin,
                                                                address _account,
                                                                uint256 _id,
                                                                uint256 _balance
                                                      )
    File src/LBPair.sol, line 861:     function _updateUserDebts(
                                                                Bin memory _bin,
                                                                address _account,
                                                                uint256 _id,
                                                                uint256 _balance
                                                      )
    File src/LBPair.sol, line 881:     function _cacheFees(
                                                                Bin memory _bin,
                                                                address _user,
                                                                uint256 _id,
                                                                uint256 _previousBalance,
                                                                uint256 _newBalance
                                                      )

    File src/LBQuoter.sol, line 54:     function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
    File src/LBQuoter.sol, line 134:     function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
    File src/LBQuoter.sol, line 42:     modifier verifyInputs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
    File src/LBQuoter.sol, line 207:     function addLiquidity(LiquidityParameters memory _liquidityParameters)
    File src/LBQuoter.sol, line 274:     function removeLiquidity
    File src/LBQuoter.sol, line 308:     function removeLiquidityAVAX
    File src/LBQuoter.sol, line 352:     function swapExactTokensForTokens
    File src/LBQuoter.sol, line 377:     function swapExactTokensForAVAX
    File src/LBQuoter.sol, line 407:     function swapExactAVAXForTokens
    File src/LBQuoter.sol, line 431:     function swapTokensForExactTokens
    File src/LBQuoter.sol, line 459:     function swapTokensForExactAVAX
    File src/LBQuoter.sol, line 493:     function swapAVAXForExactTokens
    File src/LBQuoter.sol, line 531:     function swapExactTokensForTokensSupportingFeeOnTransferTokens
    File src/LBQuoter.sol, line 561:     function swapExactTokensForAVAXSupportingFeeOnTransferTokens
    File src/LBQuoter.sol, line 594:     function swapExactAVAXForTokensSupportingFeeOnTransferTokens
    File src/LBQuoter.sol, functions: sweepLBToken, _addLiquidity, _getAmountsIn, _removeLiquidity, _swapExactTokensForTokens, _swapTokensForExactTokens, _swapSupportingFeeOnTransferTokens, _getPairs

    File src/LBToken.sol, line 79:     function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
    File src/LBToken.sol, line 149:     function safeBatchTransferFrom



