## Gas-efficient cycle
The most efficient cycle for situtations where we don't mind about increasing sequence is:
```
unchecked {
    uint256 len;
    if (len != 0) {
        for (; --len != 0; ) {
            f(i);
        }
        f(0);
    }
}
```
Otherwise (where we need increasing one) is:
```
unchecked {
    uint256 len;
    for (uint256 i; i != len; ++i) {
        f(i);
    }
}
```
Examples:
```
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..ca6b278 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -271,7 +271,8 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {

         uint256 _lastId;
         unchecked {
-            for (uint256 i; i < _ids.length; ++i) {
+            uint256 len = _ids.length;
+            for (uint256 i; i != len; ++i) {
                 uint256 _id = _ids[i];

                 // Ensures uniqueness of ids
@@ -697,9 +698,24 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {

             amountX = _unclaimedData.decode(type(uint128).max, 0);
             amountY = _unclaimedData.decode(type(uint128).max, 128);
-
-            for (uint256 i; i < _ids.length; ++i) {
-                uint256 _id = _ids[i];
+
+            uint256 n = _ids.length;
+            if (n != 0) {
+                for (; --n != 0; ) {
+                    uint256 _id = _ids[n];
+                    uint256 _balance = balanceOf(_account, _id);
+
+                    if (_balance != 0) {
+                        Bin memory _bin = _bins[_id];
+
+                        (uint256 _amountX, uint256 _amountY) = _getPendingFees(_bin, _account, _id, _balance);
+                        _updateUserDebts(_bin, _account, _id, _balance);
+
+                        amountX += _amountX;
+                        amountY += _amountY;
+                    }
+                }
+                uint256 _id = _ids[0];
                 uint256 _balance = balanceOf(_account, _id);

                 if (_balance != 0) {
@@ -926,7 +942,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         _pairInformation.oracleSize = _newSize;

         unchecked {
-            for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {
+            for (uint256 _id = _oracleSize; _id != _newSize; ++_id) {
                 _oracle.initialize(_id);
             }
         }
```
Places that could be optimzied:
```
src/LBPair.sol#L274
src/LBPair.sol#L496
src/LBPair.sol#L623
src/LBPair.sol#L701
src/LBPair.sol#L929
src/LBQuoter.sol#L75
src/LBQuoter.sol#L100
src/LBQuoter.sol#L177
src/LBRouter.sol#L674
src/LBRouter.sol#L711
src/LBRouter.sol#L778
src/LBRouter.sol#L831
src/LBRouter.sol#L878
src/LBRouter.sol#L951
src/LBToken.sol#L90
src/LBToken.sol#L163
```
## Optimize ternary operators depends on situation
Examples:
```
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..af15bd9 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -185,7 +185,10 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         )
     {
         (oracleSampleLifetime, oracleSize, oracleActiveSize, oracleLastTimestamp, oracleId) = _getOracleParameters();
-        min = oracleActiveSize == 0 ? 0 : oracleSampleLifetime;
+
+        if (oracleActiveSize != 0) {
+            min = oracleSampleLifetime;
+        }
         max = oracleSampleLifetime * oracleActiveSize;
     }
 
@@ -330,7 +333,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
                     .getAmounts(_fp, _pair.activeId, _swapForY, _amountIn);
 
                 _bin.updateFees(_swapForY ? _pair.feesX : _pair.feesY, _fees, _swapForY, totalSupply(_pair.activeId));
-
+                
                 _bin.updateReserves(_pair, _swapForY, _amountInToBin.safe112(), _amountOutOfBin.safe112());
 
                 _amountIn -= _amountInToBin + _fees.total;
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..57c483f 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -220,8 +220,10 @@ contract LBQuoter {
         address _tokenB
     ) internal view returns (uint256 reserveA, uint256 reserveB) {
         (address token0, ) = JoeLibrary.sortTokens(_tokenA, _tokenB);
-        (uint256 reserve0, uint256 reserve1, ) = IJoePair(_pair).getReserves();
-        (reserveA, reserveB) = _tokenA == token0 ? (reserve0, reserve1) : (reserve1, reserve0);
+        (reserveB, reserveA, ) = IJoePair(_pair).getReserves();
+        if (_tokenA == token0) {
+            (reserveA, reserveB) = (reserveB, reserveA);
+        }
     }
 
     /// @dev Calculates a quote for a V2 pair
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..e1ae68d 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -323,7 +323,7 @@ contract LBRouter is ILBRouter {
                 (_amountTokenMin, _amountAVAXMin) = (_amountAVAXMin, _amountTokenMin);
             }
 
-            (uint256 _amountX, uint256 _amountY) = _removeLiquidity(
+            (amountAVAX, amountToken) = _removeLiquidity(
                 _LBPair,
                 _amountTokenMin,
                 _amountAVAXMin,
@@ -332,7 +332,9 @@ contract LBRouter is ILBRouter {
                 address(this)
             );
 
-            (amountToken, amountAVAX) = _isAVAXTokenY ? (_amountX, _amountY) : (_amountY, _amountX);
+            if (_isAVAXTokenY) {
+                (amountToken, amountAVAX) = (amountAVAX, amountToken);
+            }
         }
 
         _token.safeTransfer(_to, amountToken);
diff --git a/src/libraries/JoeLibrary.sol b/src/libraries/JoeLibrary.sol
index cce7d28..bc523e0 100644
--- a/src/libraries/JoeLibrary.sol
+++ b/src/libraries/JoeLibrary.sol
@@ -11,7 +11,10 @@ library JoeLibrary {
     // returns sorted token addresses, used to handle return values from pairs sorted in this order
     function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {
         if (tokenA == tokenB) revert JoeLibrary__IdenticalAddresses();
-        (token0, token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
+        (token0, token1) = (tokenB, tokenA);
+        if (tokenA < tokenB) {
+            (token0, token1) = (tokenA, tokenB);
+        }
         if (token0 == address(0)) revert JoeLibrary__AddressZero();
     }
 
diff --git a/src/libraries/SafeMath.sol b/src/libraries/SafeMath.sol
index 296eda4..5eae3fe 100644
--- a/src/libraries/SafeMath.sol
+++ b/src/libraries/SafeMath.sol
@@ -12,7 +12,8 @@ library SafeMath {
     /// @return The result of abs(x - y)
     function absSub(uint256 x, uint256 y) internal pure returns (uint256) {
         unchecked {
-            return x > y ? x - y : y - x;
+            if (x > y) return x - y;
+            return y - x;
         }
     }
 }
```
## Use private constants
Examples:
```
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..f9ffee2 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -22,12 +22,12 @@ contract LBFactory is PendingOwnable, ILBFactory {
     using Decoder for bytes32;
     using EnumerableSet for EnumerableSet.AddressSet;

-    uint256 public constant override MAX_FEE = 0.1e18; // 10%
+    uint256 private constant MAX_FEE = 0.1e18; // 10%

-    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
-    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
+    uint256 private constant MIN_BIN_STEP = 1; // 0.01%
+    uint256 private constant MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons

-    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
+    uint256 private constant MAX_PROTOCOL_SHARE = 2_500; // 25%

     address public override LBPairImplementation;

diff --git a/src/interfaces/ILBFactory.sol b/src/interfaces/ILBFactory.sol
index 1b2708f..e4e938b 100644
--- a/src/interfaces/ILBFactory.sol
+++ b/src/interfaces/ILBFactory.sol
@@ -72,14 +72,6 @@ interface ILBFactory is IPendingOwnable {

     event QuoteAssetRemoved(IERC20 indexed quoteAsset);

-    function MAX_FEE() external pure returns (uint256);
-
-    function MIN_BIN_STEP() external pure returns (uint256);
-
-    function MAX_BIN_STEP() external pure returns (uint256);
-
-    function MAX_PROTOCOL_SHARE() external pure returns (uint256);
-
     function LBPairImplementation() external view returns (address);

     function getNumberOfQuoteAssets() external view returns (uint256);
```
## Precalculate calculations in functions
Examples:
```
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..0b2d8df 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -282,7 +282,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
             _avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) | (1 << _binStep));

             // Increase the number of lb pairs by 1
-            _avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) + (1 << 248));
+            _avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) + 0x100000000000000000000000000000000000000000000000000000000000000);

             // Save the changes
             _availableLBPairBinSteps[_tokenA][_tokenB] = _avLBPairBinSteps;
@@ -372,7 +372,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
             _avPresets = bytes32(uint256(_avPresets) | (1 << _binStep));

             // Increase the number of preset by 1
-            _avPresets = bytes32(uint256(_avPresets) + (1 << 248));
+            _avPresets = bytes32(uint256(_avPresets) + 0x100000000000000000000000000000000000000000000000000000000000000);

             // Save the changes
             _availablePresets = _avPresets;
@@ -400,7 +400,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
         bytes32 _avPresets = _availablePresets;

         _avPresets &= bytes32(type(uint256).max - (1 << _binStep));
-        _avPresets = bytes32(uint256(_avPresets) - (1 << 248));
+        _avPresets = bytes32(uint256(_avPresets) - 0x100000000000000000000000000000000000000000000000000000000000000);

         // Save the changes
         _availablePresets = _avPresets;
diff --git a/src/libraries/BitMath.sol b/src/libraries/BitMath.sol
index d088fdf..77a54c4 100644
--- a/src/libraries/BitMath.sol
+++ b/src/libraries/BitMath.sol
@@ -68,35 +68,35 @@ library BitMath {
     /// @return msb The index of the most significant bit of x
     function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {
         unchecked {
-            if (x >= 1 << 128) {
+            if (x >= 0x100000000000000000000000000000000) {
                 x >>= 128;
                 msb = 128;
             }
-            if (x >= 1 << 64) {
+            if (x >= 0x10000000000000000) {
                 x >>= 64;
                 msb += 64;
             }
-            if (x >= 1 << 32) {
+            if (x >= 0x100000000) {
                 x >>= 32;
                 msb += 32;
             }
-            if (x >= 1 << 16) {
+            if (x >= 0x10000) {
                 x >>= 16;
                 msb += 16;
             }
-            if (x >= 1 << 8) {
+            if (x >= 0x100) {
                 x >>= 8;
                 msb += 8;
             }
-            if (x >= 1 << 4) {
+            if (x >= 0x10) {
                 x >>= 4;
                 msb += 4;
             }
-            if (x >= 1 << 2) {
+            if (x >= 0x4) {
                 x >>= 2;
                 msb += 2;
             }
-            if (x >= 1 << 1) {
+            if (x >= 0x2) {
                 msb += 1;
             }
         }
```
## Use payable to decrease gas fees (decide if you want to use it)
Examples:
```
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..4a8d72c 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -303,6 +303,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
     /// @return amountYOut The amount of token Y sent to `_to`
     function swap(bool _swapForY, address _to)
         external
+        payable
         override
         nonReentrant
         returns (uint256 amountXOut, uint256 amountYOut)
diff --git a/src/interfaces/ILBPair.sol b/src/interfaces/ILBPair.sol
index 9a96d8e..4cad97b 100644
--- a/src/interfaces/ILBPair.sol
+++ b/src/interfaces/ILBPair.sol
@@ -209,7 +209,7 @@ interface ILBPair {
         view
         returns (uint256 amountX, uint256 amountY);
 
-    function swap(bool sentTokenY, address to) external returns (uint256 amountXOut, uint256 amountYOut);
+    function swap(bool sentTokenY, address to) external payable returns (uint256 amountXOut, uint256 amountYOut);
 
     function flashLoan(
         address to,
```
## Replace << 1 with * 2 (where it gives optimization)
Examples:
```
diff --git a/src/libraries/BitMath.sol b/src/libraries/BitMath.sol
index d088fdf..2ad0cce 100644
--- a/src/libraries/BitMath.sol
+++ b/src/libraries/BitMath.sol
@@ -135,7 +135,7 @@ library BitMath {
                 x <<= 2;
                 lsb += 2;
             }
-            if (x << 1 != 0) {
+            if (x * 2 != 0) {
                 lsb += 1;
             }
```
## Replace < and > with >= and <= where it is possible
Examples:
```
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..f05a78f 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -715,8 +715,8 @@ contract LBRouter is ILBRouter {
             address _pair = _pairs[i - 1];
 
             if (_binStep == 0) {
-                (uint256 _reserveIn, uint256 _reserveOut, ) = IJoePair(_pair).getReserves();
-                if (_token > _tokenPath[i]) {
+                (uint256 _reserveOut, uint256 _reserveIn, ) = IJoePair(_pair).getReserves();
+                if (_token <= _tokenPath[i]) {
                     (_reserveIn, _reserveOut) = (_reserveOut, _reserveIn);
                 }
```
## Use anonymous events (decide if you want to use it)
It saves 400 gas per event emit and allows to use 4 indexed values.
Examples:
```
diff --git a/src/interfaces/ILBPair.sol b/src/interfaces/ILBPair.sol
index 9a96d8e..dd7c19f 100644
--- a/src/interfaces/ILBPair.sol
+++ b/src/interfaces/ILBPair.sol
@@ -149,7 +149,7 @@ interface ILBPair {
 
     event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
 
-    event OracleSizeIncreased(uint256 previousSize, uint256 newSize);
+    event OracleSizeIncreased(uint256 previousSize, uint256 newSize) anonymous;
 
     function tokenX() external view returns (IERC20);
```
## Replace += 1 and -= 1 with ++ and -- accordingly
Examples:
```
diff --git a/src/libraries/BitMath.sol b/src/libraries/BitMath.sol
index d088fdf..63ee90c 100644
--- a/src/libraries/BitMath.sol
+++ b/src/libraries/BitMath.sol
@@ -97,7 +97,7 @@ library BitMath {
                 msb += 2;
             }
             if (x >= 1 << 1) {
-                msb += 1;
+                ++msb;
             }
         }
     }
@@ -136,7 +136,7 @@ library BitMath {
                 lsb += 2;
             }
             if (x << 1 != 0) {
-                lsb += 1;
+                ++lsb;
             }
 
             return 255 - lsb;
diff --git a/src/libraries/Math512Bits.sol b/src/libraries/Math512Bits.sol
index c539639..0c00e2b 100644
--- a/src/libraries/Math512Bits.sol
+++ b/src/libraries/Math512Bits.sol
@@ -96,7 +96,7 @@ library Math512Bits {
     ) internal pure returns (uint256 result) {
         unchecked {
             result = mulShiftRoundDown(x, y, offset);
-            if (mulmod(x, y, 1 << offset) != 0) result += 1;
+            if (mulmod(x, y, 1 << offset) != 0) ++result;
         }
     }
 
@@ -156,7 +156,7 @@ library Math512Bits {
     ) internal pure returns (uint256 result) {
         result = shiftDivRoundDown(x, offset, denominator);
         unchecked {
-            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;
+            if (mulmod(x, 1 << offset, denominator) != 0) ++result;
         }
     }
```
## Replace > 0 with != 0
Examples:
```
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..eb3cde4 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -158,7 +158,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
             bytes32 _avPresets = _availablePresets;
             uint256 _nbPresets = _avPresets.decode(type(uint8).max, 248);
 
-            if (_nbPresets > 0) {
+            if (_nbPresets != 0) {
                 presetsBinStep = new uint256[](_nbPresets);
 
                 uint256 _index;
@@ -188,7 +188,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
             bytes32 _avLBPairBinSteps = _availableLBPairBinSteps[_tokenA][_tokenB];
             uint256 _nbAvailable = _avLBPairBinSteps.decode(type(uint8).max, 248);
 
-            if (_nbAvailable > 0) {
+            if (_nbAvailable != 0) {
                 LBPairsAvailable = new LBPairInformation[](_nbAvailable);
 
                 uint256 _index;
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..7626731 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -76,10 +76,10 @@ contract LBQuoter {
             // Fetch swap for V1
             quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);
 
-            if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
+            if (quote.pairs[i] != address(0) && quote.amounts[i] != 0) {
                 (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i], _route[i], _route[i + 1]);
 
-                if (reserveIn > 0 && reserveOut > 0) {
+                if (reserveIn != 0 && reserveOut != 0) {
                     quote.amounts[i + 1] = JoeLibrary.getAmountOut(quote.amounts[i], reserveIn, reserveOut);
                     quote.virtualAmountsWithoutSlippage[i + 1] = JoeLibrary.quote(
                         (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
@@ -96,7 +96,7 @@ contract LBQuoter {
                 IERC20(_route[i + 1])
             );
 
-            if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
+            if (LBPairsAvailable.length != 0 && quote.amounts[i] != 0) {
                 for (uint256 j; j < LBPairsAvailable.length; j++) {
                     if (!LBPairsAvailable[j].ignoredForRouting) {
                         bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i + 1];
@@ -151,13 +151,13 @@ contract LBQuoter {
         quote.amounts[swapLength] = _amountOut;
         quote.virtualAmountsWithoutSlippage[swapLength] = _amountOut;
 
-        for (uint256 i = swapLength; i > 0; i--) {
+        for (uint256 i = swapLength; i != 0; i--) {
             // Fetch swap for V1
             quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]);
-            if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
+            if (quote.pairs[i - 1] != address(0) && quote.amounts[i] != 0) {
                 (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i - 1], _route[i - 1], _route[i]);
 
-                if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
+                if (reserveIn != 0 && reserveOut > quote.amounts[i]) {
                     quote.amounts[i - 1] = JoeLibrary.getAmountIn(quote.amounts[i], reserveIn, reserveOut);
                     quote.virtualAmountsWithoutSlippage[i - 1] =
                         (JoeLibrary.quote(quote.virtualAmountsWithoutSlippage[i], reserveOut, reserveIn) * 1000) /
@@ -173,7 +173,7 @@ contract LBQuoter {
                 IERC20(_route[i])
             );
 
-            if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
+            if (LBPairsAvailable.length != 0 && quote.amounts[i] != 0) {
                 for (uint256 j; j < LBPairsAvailable.length; j++) {
                     if (!LBPairsAvailable[j].ignoredForRouting) {
                         bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i];
diff --git a/src/libraries/Math128x128.sol b/src/libraries/Math128x128.sol
index 4a58145..54cde7b 100644
--- a/src/libraries/Math128x128.sol
+++ b/src/libraries/Math128x128.sol
@@ -68,7 +68,7 @@ library Math128x128 {
             if (y != LOG_SCALE) {
                 // Calculate the fractional part via the iterative approximation.
                 // The "delta >>= 1" part is equivalent to "delta /= 2", but shifting bits is faster.
-                for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta > 0; delta >>= 1) {
+                for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta != 0; delta >>= 1) {
                     y = (y * y) >> LOG_SCALE_OFFSET;
 
                     // Is y^2 > 2 and so in the range [2,4)?
```