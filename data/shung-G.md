# Gas Optimizations Report

## [G-01] Owner token enumeration is an extremely expensive operation but it is not essential to the protocol

`LBToken` [enumerates token/bin IDs owned by users in a pair](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L26-L27). The enumeration is only exposed through [two external functions](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L96-L109), which are just for convenience for off-chain usage, and not necessary for the functionality of the protocol. Removing enumeration will save tremendous amounts of gas during essential operations of adding and removing liquidity.

### Impact of enumeration

OpenZeppelin’s EnumerableSet roughly costs 50,000 gas when adding and removing elements from the set. Even for a small price range, adding liquidity in Liquidity Book requires minting tokens from many bins. For example, currently [the testnet user interface](https://gadgetzan.traderjoexyz.com/poolv2/0xb6076c93701d6a07266c31066b298aec6dd65c2d/0xab231a5744c8e6c45481754928ccffffd4aa0732/1) mints 31 tokens when adding liquidity in a normal distribution shape. This operation [roughly costs 4,000,000 gas](https://testnet.snowtrace.io/tx/0x88812398557021281e52eacf38f5064a344d4bee0290e96b37ffc2bea6102042), and removal costs about half of that. Given a volatile market, we can expect users to remove and re-add liquidity pretty often. This coupled operation costs around 6,000,000 gas if you have the minimum amount of bins in a normal distribution (31 as allowed by the current UI), which will be about 0.15 AVAX (25 nAVAX base fee). That would be \$2.25 in current AVAX price (\$15). And that would be \$15 when AVAX is \$100, and \$120 when AVAX is $100 and network is heavily used (200 nAVAX base fee). Given that the protocol needs `swap_fee_earned / gas_fee_to_move_liquidity` to be greater than `1` to incentivize users to chase the price to concentrate the liquidity, the mint & burn fees must be as little as possible to allow non-whales to be also able to profitably move around their liquidity. Removing the enumeration can nearly halve that cost, making the protocol enticing to more users.

### [Non-]reasons to enumerate

Enumeration allows user interfaces to easily see which bins a user is in directly from the blockchain. With the absence of enumeration, Trader Joe will need to index this information either using in-house tools or using something like The Graph. Trader Joe team is already familiar with indexing through their NFT marketplace Joepegs, therefore it seems practical for them to go off-chain indexing route.

Enumeration allows a decentralized way to pull the information from the blockchain. We have to admit that not enumerating would be in detriment to user interfaces that would have wanted to integrate Liquidity Book by using decentralized methods only. However, that is a very small percent of builders that hold such principles. The rest of the builders can also use off-chain indexing.

There is also the end user who might want to learn which bins they are in conveniently using decentralized methods. They can still do this in decentralized manner by checking all the bins, given the bin IDs are determined by step and price and have a range of few thousand (bin step = 100) to few millions (bin step = 1). Admittedly this is not very convenient, but it is doable.

### Diff to remove enumeration

The following diff removes the enumeration from the code and tests.

```diff
diff --git a/src/LBToken.sol b/src/LBToken.sol
index 47aa528..6cb1dbc 100644
--- a/src/LBToken.sol
+++ b/src/LBToken.sol
@@ -23,9 +23,6 @@ contract LBToken is ILBToken {
     /// @dev Mapping from token id to total supplies
     mapping(uint256 => uint256) private _totalSupplies;

-    /// @dev  Mapping from account to set of ids, where user currently have a non-zero balance
-    mapping(address => EnumerableSet.UintSet) private _userIds;
-
     string private constant _name = "Liquidity Book Token";
     string private constant _symbol = "LBT";

@@ -93,21 +90,6 @@ contract LBToken is ILBToken {
         }
     }

-    /// @notice Returns the type id at index `_index` where `account` has a non-zero balance
-    /// @param _account The address of the account
-    /// @param _index The position index
-    /// @return The `account` non-zero position at index `_index`
-    function userPositionAtIndex(address _account, uint256 _index) public view virtual override returns (uint256) {
-        return _userIds[_account].at(_index);
-    }
-
-    /// @notice Returns the number of non-zero balances of `account`
-    /// @param _account The address of the account
-    /// @return The number of non-zero balances of `account`
-    function userPositionNumber(address _account) public view virtual override returns (uint256) {
-        return _userIds[_account].length();
-    }
-
     /// @notice Returns true if `spender` is approved to transfer `_account`'s tokens
     /// @param _owner The address of the owner
     /// @param _spender The address of the spender
@@ -190,9 +172,6 @@ contract LBToken is ILBToken {
             _balances[_id][_from] = _fromBalance - _amount;
             _balances[_id][_to] = _toBalance + _amount;
         }
-
-        _remove(_from, _id, _fromBalance, _amount);
-        _add(_to, _id, _toBalance, _amount);
     }

     /// @dev Creates `_amount` tokens of type `_id`, and assigns them to `_account`
@@ -215,8 +194,6 @@ contract LBToken is ILBToken {
             _balances[_id][_account] = _accountBalance + _amount;
         }

-        _add(_account, _id, _accountBalance, _amount);
-
         emit TransferSingle(msg.sender, address(0), _account, _id, _amount);
     }

@@ -241,8 +218,6 @@ contract LBToken is ILBToken {
             _totalSupplies[_id] -= _amount;
         }

-        _remove(_account, _id, _accountBalance, _amount);
-
         emit TransferSingle(msg.sender, _account, address(0), _id, _amount);
     }

@@ -270,38 +245,6 @@ contract LBToken is ILBToken {
         return _owner == _spender || _spenderApprovals[_owner][_spender];
     }

-    /// @notice Internal function to add an id to an user's set
-    /// @param _account The user's address
-    /// @param _id The id of the token
-    /// @param _accountBalance The user's balance
-    /// @param _amount The amount of tokens
-    function _add(
-        address _account,
-        uint256 _id,
-        uint256 _accountBalance,
-        uint256 _amount
-    ) internal {
-        if (_accountBalance == 0 && _amount != 0) {
-            _userIds[_account].add(_id);
-        }
-    }
-
-    /// @notice Internal function to remove an id from an user's set
-    /// @param _account The user's address
-    /// @param _id The id of the token
-    /// @param _accountBalance The user's balance
-    /// @param _amount The amount of tokens
-    function _remove(
-        address _account,
-        uint256 _id,
-        uint256 _accountBalance,
-        uint256 _amount
-    ) internal {
-        if (_accountBalance == _amount && _amount != 0) {
-            _userIds[_account].remove(_id);
-        }
-    }
-
     /// @notice Hook that is called before any token transfer. This includes minting
     /// and burning.
     ///
diff --git a/src/interfaces/ILBToken.sol b/src/interfaces/ILBToken.sol
index 36b1fb7..49c6243 100644
--- a/src/interfaces/ILBToken.sol
+++ b/src/interfaces/ILBToken.sol
@@ -29,10 +29,6 @@ interface ILBToken {
         view
         returns (uint256[] memory batchBalances);

-    function userPositionAtIndex(address account, uint256 index) external view returns (uint256);
-
-    function userPositionNumber(address account) external view returns (uint256);
-
     function totalSupply(uint256 id) external view returns (uint256);

     function isApprovedForAll(address owner, address spender) external view returns (bool);
diff --git a/test/LBRouter.t.sol b/test/LBRouter.t.sol
index 4f2aa33..a38d9fb 100644
--- a/test/LBRouter.t.sol
+++ b/test/LBRouter.t.sol
@@ -410,10 +410,8 @@ contract LiquidityBinRouterTest is TestHelper {

         uint256[] memory amounts = new uint256[](5);
         for (uint256 i; i < 5; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }
-        assertEq(pair.userPositionNumber(DEV), 5);

         assertEq(pair.balanceOf(DEV, ID_ONE - 1), amountIn / 3);

diff --git a/test/LBToken.t.sol b/test/LBToken.t.sol
index d263153..b5aa7cc 100644
--- a/test/LBToken.t.sol
+++ b/test/LBToken.t.sol
@@ -34,10 +34,8 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](5);
         for (uint256 i; i < 5; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }
-        assertEq(pair.userPositionNumber(DEV), 5);

         assertEq(pair.balanceOf(DEV, ID_ONE - 1), amountIn / 3);
         vm.expectEmit(true, true, true, true);
@@ -67,10 +65,8 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](5);
         for (uint256 i; i < 5; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }
-        assertEq(pair.userPositionNumber(DEV), 5);

         assertEq(pair.balanceOf(DEV, ID_ONE - 1), amountIn / 3);
         vm.expectEmit(true, true, true, true);
@@ -99,7 +95,6 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](5);
         for (uint256 i; i < 5; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }

@@ -114,7 +109,6 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](5);
         for (uint256 i; i < 5; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }

@@ -130,7 +124,6 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](binAmount);
         for (uint256 i; i < binAmount; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }

@@ -163,7 +156,6 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](binAmount);
         for (uint256 i; i < binAmount; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }

@@ -195,7 +187,6 @@ contract LiquidityBinTokenTest is TestHelper {

         uint256[] memory amounts = new uint256[](binAmount - 1);
         for (uint256 i; i < binAmount - 1; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }

@@ -244,7 +235,6 @@ contract LiquidityBinTokenTest is TestHelper {
         (_ids, , , ) = addLiquidity(amountIn, _startId, binAmount, _gap);
         uint256[] memory amounts = new uint256[](binAmount);
         for (uint256 i; i < binAmount; i++) {
-            assertEq(pair.userPositionAtIndex(DEV, i), _ids[i]);
             amounts[i] = pair.balanceOf(DEV, _ids[i]);
         }
         batchBalances = pair.balanceOfBatch(accounts, _ids);
```

### Gas savings

Below is the output of `forge snapshot --diff | tail -65` converted to CSV. Especially see `testInternalBurn` and `testInternalMint` functions showing greater than 50% savings.

```csv
Test Function,Gas Cost Difference,Percent Difference
testSetLBPairImplementation(),-460002,-2.742%
testConstructor(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24),-153429,-3.014%
testGetSwapInOverflowReverts(),-67031,-9.004%
testGetSwapOutWithMultipleChoices(),-427507,-10.436%
testOracleSampleFromWith2Samples(),-67031,-12.911%
testSwapYtoXSingleBinFromGetSwapIn(),-66966,-13.118%
testSwapYtoXSingleBinFromGetSwapOut(),-67031,-13.449%
testSwapXtoYSingleBinFromGetSwapOut(),-67031,-13.450%
testSwapXtoYSingleBinFromGetSwapIn(),-67031,-13.501%
testOracleSampleFromEdgeCases(),-67009,-13.970%
testFuzzingAddLiquidity(uint256),-157150,-15.139%
testDistributionOverflowReverts(),-134018,-15.454%
testOracleSampleFromWith100Samples(),-4487757,-17.917%
testClaimFeesComplex(uint256,uint256),-247423,-18.921%
testForIdSlippageCaughtReverts(),-427485,-19.194%
testClaimProtocolFees(),-247401,-19.862%
testClaimFeesY(),-247335,-20.163%
testClaimFeesX(),-247335,-20.163%
testFeesOnTokenTransfer(),-284146,-20.361%
testSwapWithDifferentBinSteps(),-427529,-20.375%
testForAmountSlippageCaughtReverts(),-473364,-21.279%
testGetSwapInWrongAmountsReverts(),-427485,-21.326%
testFlawedCompositionFactor(),-359365,-21.362%
testGetSwapInMoreBins(),-427031,-21.706%
testInsufficientLiquidityMinted(),-359321,-21.806%
testGetSwapOutOnComplexRoute(),-427464,-22.719%
testGetSwapInOnComplexRoute(),-427507,-22.968%
testOracleSampleFromWith100SamplesNotAllInitialized(),-4484457,-22.991%
testAddLiquidityIgnored(),-428376,-23.297%
testGetSwapInWithMultipleChoices(),-427507,-23.756%
testSwapYtoXDistantBinsFromGetSwapOut(),-427421,-23.911%
testSwapYtoXDistantBinsFromGetSwapIn(),-427421,-23.933%
testBalanceOfBatch(),-256383,-24.074%
testFeeOnActiveBinReverse(),-213936,-24.331%
testFeeOnActiveBin(),-213936,-24.331%
testSwapXtoYDistantBinsFromGetSwapOut(),-427486,-24.412%
testSafeBatchTransferNotApprovedReverts(),-256343,-24.420%
testSwapXtoYDistantBinsFromGetSwapIn(),-427486,-24.425%
testSafeTransferNotApprovedReverts(),-256376,-24.488%
testFlashloan(),-427513,-24.826%
testSwapXtoYConsecutiveBinFromGetSwapOut(),-427486,-25.050%
testSwapXtoYConsecutiveBinFromGetSwapIn(),-427486,-25.064%
testSwapYtoXConsecutiveBinFromGetSwapOut(),-427486,-25.089%
testSwapYtoXConsecutiveBinFromGetSwapIn(),-427486,-25.104%
testBurnLiquidity(),-477535,-25.129%
testSafeTransferFrom(),-295891,-25.295%
testGetSwapOutOnV2Pair(),-427507,-26.931%
testGetSwapInOnV2Pair(),-427507,-26.953%
testSweepLBToken(),-489987,-27.188%
testModifierCheckLength(),-535964,-28.163%
testSafeTransferFromReverts(),-537662,-28.222%
testForceDecay(),-2319546,-28.916%
testSafeBatchTransferFromReverts(),-606907,-29.824%
testAddLiquidityTaxToken(),-1076244,-29.978%
testTLowerThanTimestamp(),-2319913,-30.143%
testRemoveLiquidityReverseOrder(),-709108,-33.958%
testAddLiquidityNoSlippage(),-709107,-33.960%
testAddLiquidityAVAXReversed(),-1608674,-35.141%
testAddLiquidityAVAX(),-1758599,-35.555%
testSafeBatchTransferFrom(),-570685,-36.100%
testRemoveLiquiditySlippageReverts(),-2670446,-42.380%
testInternalBurn(uint256,uint256),-67156,-53.633%
testInternalMint(uint256),-67231,-55.603%
testInternalExcessiveBurnAmountReverts(uint128,uint128),-66987,-56.306%
Overall,-39447398,-1550.248%
```

Note that there are other instances of enumeration in the protocol. However, they only cost gas in admin functions or during pair creation. Also they enumerate addresses. Therefore I believe them to be justified, hence I only focused on enumeration of this core protocol functionality (adding and removing liquidity). I think it is essential to remove this enumeration to improve the efficiency of the protocol. Reducing gas cost during adding or removing liquidity is of utmost importance for the optimization of this protocol, as it will make it feasible to do bin operations at greater scale.

## [G-02] Using Solidity version 0.8.17 will provide an overall gas optimization

Using at least `0.8.10` will save gas due to skipped `extcodesize` check if there is a return value. Currently the contracts are compiled using version `0.8.7` (Foundry default). It is easily changeable to `0.8.17` using the command `sed -i 's/0\.8\.7/^0.8.0/' test/*.sol && sed -i '4isolc = "0.8.17"' foundry.toml`. This will have the following total savings obtained by `forge snapshot --diff | tail -1`:

```csv
Test Function,Gas Cost Difference,Percent Difference
Overall,-582995,-88.032%
```

## [G-03] Ternary operation is cheaper than if-else statement

There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

```diff
diff --git a/src/libraries/BitMath.sol b/src/libraries/BitMath.sol
index d088fdf..29c4034 100644
--- a/src/libraries/BitMath.sol
+++ b/src/libraries/BitMath.sol
@@ -16,9 +16,7 @@ library BitMath {
         uint8 _bit,
         bool _rightSide
     ) internal pure returns (uint256) {
-        if (_rightSide) {
-            return closestBitRight(_integer, _bit - 1);
-        } else return closestBitLeft(_integer, _bit + 1);
+        return _rightSide ? closestBitRight(_integer, _bit - 1) : closestBitLeft(_integer, _bit + 1);
     }

     /// @notice Returns the most (or least) significant bit of `_integer`
@@ -26,9 +24,7 @@ library BitMath {
     /// @param _isMostSignificant Whether we want the most (true) or the least (false) significant bit
     /// @return The index of the most (or least) significant bit
     function significantBit(uint256 _integer, bool _isMostSignificant) internal pure returns (uint8) {
-        if (_isMostSignificant) {
-            return mostSignificantBit(_integer);
-        } else return leastSignificantBit(_integer);
+        return _isMostSignificant ? mostSignificantBit(_integer) : leastSignificantBit(_integer);
     }

     /// @notice Returns the index of the closest bit on the right of x that is non null
@@ -41,10 +37,8 @@ library BitMath {
             uint256 _shift = 255 - bit;
             x <<= _shift;

-            if (x == 0) return type(uint256).max;
-
-            // can't overflow as it's non-zero and we shifted it by `_shift`
-            return mostSignificantBit(x) - _shift;
+            // can't underflow as it's non-zero and we shifted it by `_shift`
+            return (x == 0) ? type(uint256).max : mostSignificantBit(x) - _shift;
         }
     }

@@ -57,9 +51,7 @@ library BitMath {
         unchecked {
             x >>= bit;

-            if (x == 0) return type(uint256).max;
-
-            return leastSignificantBit(x) + bit;
+            return (x == 0) ? type(uint256).max : leastSignificantBit(x) + bit;
         }
     }
```

Note that this optimization seems to be dependent on usage of a more recent Solidity version. The following gas savings are on version `0.8.17`.

```csv
Test Function,Gas Cost Difference,Percent Difference
Overall,-13065,-0.200%
```

## [G-04] Checking `msg.sender` to not be zero address is redundant

There is an instance where `msg.sender` is checked not to be zero address. This check is redundant as no private key is known for this address, hence there can be no transactions coming from the zero address. The following diff removes this redundant check.

```diff
diff --git a/src/libraries/PendingOwnable.sol b/src/libraries/PendingOwnable.sol
index f745362..97fb524 100644
--- a/src/libraries/PendingOwnable.sol
+++ b/src/libraries/PendingOwnable.sol
@@ -33,7 +33,7 @@ contract PendingOwnable is IPendingOwnable {

     /// @notice Throws if called by any account other than the pending owner.
     modifier onlyPendingOwner() {
-        if (msg.sender != _pendingOwner || msg.sender == address(0)) revert PendingOwnable__NotPendingOwner();
+        if (msg.sender != _pendingOwner) revert PendingOwnable__NotPendingOwner();
         _;
     }

 ```

This will save tiny amounts of gas when `PendingOwnable.becomeOwner()` is called.

## [G-05] An element is cached to memory after it is used

Caching a struct element locally should be done before using it to save gas. The following diff applies this optimization.

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..1d29c39 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -316,8 +316,8 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         if (_amountIn == 0) revert LBPair__InsufficientAmounts();

         FeeHelper.FeeParameters memory _fp = _feeParameters;
-        _fp.updateVariableFeeParameters(_pair.activeId);
         uint256 _startId = _pair.activeId;
+        _fp.updateVariableFeeParameters(_startId);

         uint256 _amountOut;
         // Performs the actual swap, bin per bin
```

This will save small amount of gas when swapping.

```csv
Test Function,Gas Cost Difference,Percent Difference
testSwapExactTokensForTokensSinglePair(),-30,-0.009%
```

## [G-06] Divisions by `2**n` can be replaced by right shift by `n`

There is an instance of division by `2`, which can be replaced by right shift by `1`. This simple bit operation is always cheaper than division. The following diff applies this optimization.

```diff
diff --git a/src/libraries/Oracle.sol b/src/libraries/Oracle.sol
index 974bc9f..fd9ca64 100644
--- a/src/libraries/Oracle.sol
+++ b/src/libraries/Oracle.sol
@@ -159,7 +159,7 @@ library Oracle {
         uint256 _sampleTimestamp;
         while (_high >= _low) {
             unchecked {
-                _middle = (_low + _high) / 2;
+                _middle = (_low + _high) >> 1;
                 assembly {
                     _id := addmod(_middle, _index, _activeSize)
                 }
```

Gas savings are obtained by `forge snapshot --diff`.

```csv
Test Function,Gas Cost Difference,Percent Difference
testOracleSampleFromWith2Samples(),-4,-0.001%
testOracleSampleFromWith100SamplesNotAllInitialized(),-452,-0.002%
testFuzzingAddLiquidity(uint256),30,0.003%
testOracleSampleFromWith100Samples(),-1320,-0.005%
Overall,-1746,-0.005%
```

## [G-07] Runtime cost can be optimized in detriment of the deploy cost

There are two optimization to improve runtime cost. Although the following optimizations will increase the gas cost of new pair creation and certain admin functions, it will decrease runtime cost of core protocol functions (swap, add/remove liquidity). Given that a pair is created once, but thousands of operations are made on it, optimizing for runtime can save a lot of gas in the long term.

### [G-07A] Storing `LBFactory._LBPairsInfo` info in both sorting order will save gas in runtime

When `LBFactory.createLBPair()` is called, the pair information can be stored in both sorting orders of its reserve tokens. This will allow skipping [`_sortTokens()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L607-L611), reducing the gas cost of [`_getLBPairInformation()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L593-L600).

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..7c66fbf 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -183,9 +183,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
         returns (LBPairInformation[] memory LBPairsAvailable)
     {
         unchecked {
-            (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
-
-            bytes32 _avLBPairBinSteps = _availableLBPairBinSteps[_tokenA][_tokenB];
+            bytes32 _avLBPairBinSteps = _availableLBPairBinSteps[_tokenX][_tokenY];
             uint256 _nbAvailable = _avLBPairBinSteps.decode(type(uint8).max, 248);
 
             if (_nbAvailable > 0) {
@@ -194,7 +192,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
                 uint256 _index;
                 for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
                     if (_avLBPairBinSteps.decode(1, i) == 1) {
-                        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];
+                        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenX][_tokenY][i];
 
                         LBPairsAvailable[_index] = LBPairInformation({
                             binStep: i.safe24(),
@@ -273,6 +271,12 @@ contract LBFactory is PendingOwnable, ILBFactory {
             createdByOwner: msg.sender == _owner,
             ignoredForRouting: false
         });
+        _LBPairsInfo[_tokenB][_tokenA][_binStep] = LBPairInformation({
+            binStep: _binStep,
+            LBPair: _LBPair,
+            createdByOwner: msg.sender == _owner,
+            ignoredForRouting: false
+        });
 
         allLBPairs.push(_LBPair);
 
@@ -286,6 +290,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
 
             // Save the changes
             _availableLBPairBinSteps[_tokenA][_tokenB] = _avLBPairBinSteps;
+            _availableLBPairBinSteps[_tokenB][_tokenA] = _avLBPairBinSteps;
         }
 
         emit LBPairCreated(_tokenX, _tokenY, _binStep, _LBPair, allLBPairs.length - 1);
@@ -315,14 +320,13 @@ contract LBFactory is PendingOwnable, ILBFactory {
         uint256 _binStep,
         bool _ignored
     ) external override onlyOwner {
-        (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
-
-        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
+        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenX][_tokenY][_binStep];
         if (address(_LBPairInformation.LBPair) == address(0)) revert LBFactory__AddressZero();
 
         if (_LBPairInformation.ignoredForRouting == _ignored) revert LBFactory__LBPairIgnoredIsAlreadyInTheSameState();
 
-        _LBPairsInfo[_tokenA][_tokenB][_binStep].ignoredForRouting = _ignored;
+        _LBPairsInfo[_tokenX][_tokenY][_binStep].ignoredForRouting = _ignored;
+        _LBPairsInfo[_tokenY][_tokenX][_binStep].ignoredForRouting = _ignored;
 
         emit LBPairIgnoredStateChanged(_LBPairInformation.LBPair, _ignored);
     }
@@ -595,7 +599,6 @@ contract LBFactory is PendingOwnable, ILBFactory {
         IERC20 _tokenB,
         uint256 _binStep
     ) private view returns (LBPairInformation memory) {
-        (_tokenA, _tokenB) = _sortTokens(_tokenA, _tokenB);
         return _LBPairsInfo[_tokenA][_tokenB][_binStep];
     }
 
```

### [G-07B] Using CREATE2 is cheaper than Clones

Using clone contracts requires extra proxy call, increasing the cost of all pair functions. Using CREATE2, although will increase cost of pair creation, will make all pair interactions cheaper.

## [G-08] Making constant variables private will save gas during deployment

When constants are marked public, extra getter functions are created, increasing the deployment cost. Marking these functions private will decrease gas cost. One can still read these variables through the source code. If they need to be accessed by an external contract, a separate single getter function can be used to return all constants as a tuple. There [are four instances of public constants](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L25-L30).

```solidity
src/LBFactory.sol:25:    uint256 public constant override MAX_FEE = 0.1e18; // 10%
src/LBFactory.sol:27:    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
src/LBFactory.sol:28:    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
src/LBFactory.sol:30:    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```

## [G-09] Using `bool`s for storage incurs overhead

*Credit: Description by [IllIllI000](https://gist.github.com/IllIllI000)*.

```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess ([**100 gas**](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past.

There are 2 instances of this issue:

```solidity
src/LBFactory.sol-38-    /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
src/LBFactory.sol:39:    bool public override creationUnlocked;
--
src/LBToken.sol-20-    /// @dev Mapping from account to spender approvals
src/LBToken.sol:21:    mapping(address => mapping(address => bool)) private _spenderApprovals;
```

## [G‑10] Functions guaranteed to revert when called by normal users can be marked `payable`

*Credit: Description by [IllIllI000](https://gist.github.com/IllIllI000)*.

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

There are 14 instances of this:

```solidity
src/libraries/PendingOwnable.sol:59:    function setPendingOwner(address pendingOwner_) public override onlyOwner {
src/libraries/PendingOwnable.sol:68:    function revokePendingOwner() public override onlyOwner {
src/libraries/PendingOwnable.sol:84:    function renounceOwnership() public override onlyOwner {
src/LBFactory.sol:215:    function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
src/LBFactory.sol:317:    ) external override onlyOwner {
src/LBFactory.sol:350:    ) external override onlyOwner {
src/LBFactory.sol:396:    function removePreset(uint16 _binStep) external override onlyOwner {
src/LBFactory.sol:434:    ) external override onlyOwner {
src/LBFactory.sol:468:    function setFeeRecipient(address _feeRecipient) external override onlyOwner {
src/LBFactory.sol:474:    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
src/LBFactory.sol:485:    function setFactoryLockedState(bool _locked) external override onlyOwner {
src/LBFactory.sol:493:    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
src/LBFactory.sol:502:    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
src/LBFactory.sol:520:    function forceDecay(ILBPair _LBPair) external override onlyOwner {
```