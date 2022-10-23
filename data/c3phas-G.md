## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Note on gas estimates
The gas estimates are the exact results from running the tests included .The Average gas before the changes and after the changes is documented with an exception of internal functions and functions that were not covered by the tests included in the contest.

The optimizer is set to run with the default runs(200).

## Using storage instead of memory  saves gas
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct


https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L466-L608
### LBPair.sol.mint(): use storage for \_bin(Save 3737 gas)
```bash
forge test --gas-report --optimize
```

```
Average Gas Before: 1402645
Average Gas After: 1398908
```

```solidity
File: /src/LBPair.sol
498:                 Bin memory _bin = _bins[_mintInfo.id];
```
**After changing to storage we can then modify some parts of the code as below**
```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..fcf5b88 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -495,7 +495,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         unchecked {
             for (uint256 i; i < _ids.length; ++i) {
                 _mintInfo.id = _ids[i].safe24();
-                Bin memory _bin = _bins[_mintInfo.id];
+                Bin storage _bin = _bins[_mintInfo.id];

                 if (_bin.reserveX == 0 && _bin.reserveY == 0) _tree.addToTree(_mintInfo.id);

@@ -575,7 +575,6 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
                 _mintInfo.amountXAddedToPair += _mintInfo.amountX;
                 _mintInfo.amountYAddedToPair += _mintInfo.amountY;

-                _bins[_mintInfo.id] = _bin;
                 _mint(_to, _mintInfo.id, _liquidity);

                 emit LiquidityAdded(
```
**Note We nolonger need the line `_bins[_mintInfo.id] = _bin;` as storage references the real value thus any update will be done to the stored variable**

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L808-L833
### LBPair.sol.\_beforeTokenTransfer(): \_bin should use storage
```solidity
File: /src/LBPair.sol
817:            Bin memory _bin = _bins[_id];
```

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..7f28059 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -814,7 +814,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         unchecked {
             super._beforeTokenTransfer(_from, _to, _id, _amount);

-            Bin memory _bin = _bins[_id];
+            Bin storage _bin = _bins[_id];

             if (_from != _to) {
                 if (_from != address(0) && _from != address(this)) {
```


https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L844-L854
### LBPair.sol.\_getPendingFees(): \_debts should use storage
```solidity
File: /src/LBPair.sol
850:        Debts memory _debts = _accruedDebts[_account][_id];
```

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..46e7fe6 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -847,7 +847,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         uint256 _id,
         uint256 _balance
     ) private view returns (uint256 amountX, uint256 amountY) {
-        Debts memory _debts = _accruedDebts[_account][_id];
+        Debts storage _debts = _accruedDebts[_account][_id];

         amountX = _bin.accTokenXPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET) - _debts.debtX;
         amountY = _bin.accTokenYPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET) - _debts.debtY;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L320

### LBFactory.sol.setLBPairIgnored(): We can save 248 gas
```bash
forge test --optimize --gas-report
```

```
Average Gas Before: 4565
Average Gas After   : 4317
```

```solidity
File: /src/LBFactory.sol
320:        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
```

We can then rewrite the function as follows
```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..725018a 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -317,12 +317,12 @@ contract LBFactory is PendingOwnable, ILBFactory {
     ) external override onlyOwner {
         (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);

-        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
+        LBPairInformation storage _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
         if (address(_LBPairInformation.LBPair) == address(0)) revert LBFactory__AddressZero();

         if (_LBPairInformation.ignoredForRouting == _ignored) revert LBFactory__LBPairIgnoredIsAlreadyInTheSameState();

-        _LBPairsInfo[_tokenA][_tokenB][_binStep].ignoredForRouting = _ignored;
+        _LBPairInformation.ignoredForRouting = _ignored;

         emit LBPairIgnoredStateChanged(_LBPairInformation.LBPair, _ignored);
     }
```


## Use Short Circuiting rules to your advantage
The operators `||` and `&&` apply the common short-circuiting rules. This means that in the expression `f(x) || g(y)`, if `f(x)` evaluates to `true`, `g(y)` will not be evaluated even if it may have side-effects.
So setting less costly function to “f(x)” and setting costly function to “g(x)” is efficient.

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L234-L305

### LBFactory.sol.createLBPair(): reorder the expressions (Saves  1045 gas)
```bash
forge test --optimize --gas-report
```

```
Average Gas Before: 302135
Average Gas After: 301090
```

```solidity
File: /src/LBFactory.sol
241:        if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);

```

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..17c7d5d 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -238,7 +238,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
         uint16 _binStep
     ) external override returns (ILBPair _LBPair) {
         address _owner = owner();
-        if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);
+        if (msg.sender != _owner && !creationUnlocked ) revert LBFactory__FunctionIsLockedForUsers(msg.sender);

         address _LBPairImplementation = LBPairImplementation;
```

### Avoid contract existence checks by using solidity version 0.8.10 or later(Number of instances  14: ~1400 gas)

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L33
```solidity
File: /src/LBRouter.sol
33:        if (msg.sender != factory.owner()) revert LBRouter__NotFactoryOwner();

124:        uint256 _fee = _fp.getFeeAmount(_amountInToBin);

199:        pair = factory.createLBPair(_tokenX, _tokenY, _activeId, _binStep);

320:        bool _isAVAXTokenY = IERC20(wavax) == _LBPair.tokenY();

543:        uint256 _balanceBefore = _targetToken.balanceOf(_to);

574:        uint256 _balanceBefore = wavax.balanceOf(address(this));

607:        uint256 _balanceBefore = _targetToken.balanceOf(_to);

918:        ILBPair _LBPair = factory.getLBPairInformation(_tokenX, _tokenY, _binStep).LBPair;

```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L428
```solidity
File: /src/LBPair.sol
428:        uint256 _fee = factory.flashLoanFee();

737:            address _feeRecipient = factory.feeRecipient();
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L77
```solidity
File:/src/LBQuoter.sol
77:            quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);

94:            ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(

156:            quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]);

171:            ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(

```

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L304-L413
### LBPair.sol.swap(): tokenX and tokenY should be cached
```solidity
File: /src/LBPair.sol
304:    function swap(bool _swapForY, address _to)

312:        uint256 _amountIn = _swapForY
313:            ? tokenX.received(_pair.reserveX, _pair.feesX.total)//@audit: tokenX SLOAD 1
314:            : tokenY.received(_pair.reserveY, _pair.feesY.total); //@audit: tokenY SLOAD 1


406:        if (_swapForY) {
407:            amountYOut = _amountOut;
408:            tokenY.safeTransfer(_to, _amountOut); //@audit: tokenY SLOAD 2
409:        } else {
410:            amountXOut = _amountOut;
411:            tokenX.safeTransfer(_to, _amountOut); //@audit: tokenX SLOAD 2
412:        }
413:    }
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L420-L456
### LBPair.sol.flashLoan(): tokenX and tokenY should be cached
```solidity
File: /src/LBPair.sol
420:    function flashLoan(

435:        tokenX.safeTransfer(_to, _amountXOut);//@audit: tokenX SLOAD 1
436:        tokenY.safeTransfer(_to, _amountYOut);//@audit: tokenY SLOAD 1


447:        _feesX.flashLoanHelper(_pairInformation.feesX, tokenX, _reserveX);//@audit: tokenX SLOAD 1
448:        _feesY.flashLoanHelper(_pairInformation.feesY, tokenY, _reserveY);//@audit: tokenY SLOAD 1


456:    }
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L520
```solidity
File: /src/LBRouter.sol
520:        if (msg.value > amountsIn[0]) _safeTransferAVAX(_to, amountsIn[0] - msg.value);
```
## Using unchecked blocks to save gas - Increments in for loop can be unchecked 
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75
### LBQuoter.sol.findBestPathFromAmountIn(): unchecked blocks(Save 148 gas)
```bash
forge test --optimize --gas-report
```

```
Average Gas Before: 45266
Average Gas After: 45118
```

```solidity
File: /src/LBQuoter.sol
75:        for (uint256 i; i < swapLength; i++) { 

100:       for (uint256 j; j < LBPairsAvailable.length; j++) { 
```

```diff
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..137adb2 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -72,7 +72,7 @@ contract LBQuoter {
         quote.amounts[0] = _amountIn;
         quote.virtualAmountsWithoutSlippage[0] = _amountIn;

-        for (uint256 i; i < swapLength; i++) {
+        for (uint256 i; i < swapLength;) {
             // Fetch swap for V1
             quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);

@@ -97,7 +97,7 @@ contract LBQuoter {
             );

             if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
-                for (uint256 j; j < LBPairsAvailable.length; j++) {
+                for (uint256 j; j < LBPairsAvailable.length; ) {
                     if (!LBPairsAvailable[j].ignoredForRouting) {
                         bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i + 1];

@@ -122,8 +122,14 @@ contract LBQuoter {
                             }
                         } catch {}
                     }
+                    unchecked {
+                        ++j;
+                    }
                 }
             }
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154
### LBQuoter.sol.findBestPathFromAmountOut(): unchecked blocks(Save 136 gas)
```bash
forge test --optimize --gas-report
```

```
Average Gas Before: 38128
Average Gas After: 37992
```

```solidity
File: /src/LBQuoter.sol
154:        for (uint256 i = swapLength; i > 0; i--) { 

177:         for (uint256 j; j < LBPairsAvailable.length; j++) {  
```

```diff
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..cf2ed44 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -151,7 +151,7 @@ contract LBQuoter {
         quote.amounts[swapLength] = _amountOut;
         quote.virtualAmountsWithoutSlippage[swapLength] = _amountOut;

-        for (uint256 i = swapLength; i > 0; i--) {
+        for (uint256 i = swapLength; i > 0; ) {
             // Fetch swap for V1
             quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]);
             if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
@@ -174,7 +174,7 @@ contract LBQuoter {
             );

             if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
-                for (uint256 j; j < LBPairsAvailable.length; j++) {
+                for (uint256 j; j < LBPairsAvailable.length;) {
                     if (!LBPairsAvailable[j].ignoredForRouting) {
                         bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i];
                         try
@@ -202,8 +202,14 @@ contract LBQuoter {
                             }
                         } catch {}
                     }
+                    unchecked {
+                        ++j;
+                    }
                 }
             }
+            unchecked {
+                --i;
+            }
         }
     }

```
[see resource](https://github.com/ethereum/solidity/issues/10695)

### Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**
To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L234-L305
### LBFactory.sol.createLBPair(): \_LBPairsInfo[\_tokenA]\[\_tokenB][\_binStep] should be cached
```solidity
File: /src/LBFactory.sol
234:    function createLBPair(

255:        if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))

270:        _LBPairsInfo[_tokenA][_tokenB][_binStep] = LBPairInformation({
271:            binStep: _binStep,
272:            LBPair: _LBPair,
273:            createdByOwner: msg.sender == _owner,
274:            ignoredForRouting: false
275:        });

305:    }
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L420-L456
### LBPair.sol.flashLoan(): \_bins[\_id] can be cached(Also note the += has been refactored)

```solidity
File: /src/LBPair.sol
420:    function flashLoan(

452:       _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
453:       _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);

455:       emit FlashLoan(msg.sender, _to, _amountXOut, _amountYOut, _feesX.total, _feesY.total);
456:    }
```
**We can introduce `Bin storage temp = _bins[_id];` as shown below**
```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..21980f2 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -448,9 +448,10 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
         _feesY.flashLoanHelper(_pairInformation.feesY, tokenY, _reserveY);

         uint256 _totalSupply = totalSupply(_id);
+         Bin storage temp = _bins[_id];

-        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
-        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
+        temp.accTokenXPerShare = temp.accTokenXPerShare + _feesX.getTokenPerShare(_totalSupply);
+        temp.accTokenYPerShare = temp.accTokenYPerShare + _feesY.getTokenPerShare(_totalSupply);

         emit FlashLoan(msg.sender, _to, _amountXOut, _amountYOut, _feesX.total, _feesY.total);
     }
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L861-L872
### LBPair.sol.\_updateUserDebts(): \_accruedDebts[\_account][\_id] should be cached
```solidity
File: /src/LBPair.sol
861:    function _updateUserDebts(

870:        _accruedDebts[_account][_id].debtX = _debtX;
871:        _accruedDebts[_account][_id].debtY = _debtY;
872:    }
```

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..493e9d6 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -866,9 +866,9 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
     ) private {
         uint256 _debtX = _bin.accTokenXPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET);
         uint256 _debtY = _bin.accTokenYPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET);
-
-        _accruedDebts[_account][_id].debtX = _debtX;
-        _accruedDebts[_account][_id].debtY = _debtY;
+        Debts storage _debts = _accruedDebts[_account][_id];
+        _debts.debtX = _debtX;
+        _debts.debtY = _debtY;
     }
```

### x += y costs more gas than x = x + y for state variables
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L211
```solidity
File: /src/LBToken.sol
211:        _totalSupplies[_id] += _amount;

241:        _totalSupplies[_id] -= _amount;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L42
```solidity
File: /src/libraries/FeeDistributionHelper.sol
42:            _pairFees.protocol += _fees.protocol;
```
### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L921
```solidity
File: /src/LBPair.sol

//@audit: uint16 _newSize
921:    function _increaseOracle(uint16 _newSize) private {


//@audit: uint24 _id
994:    function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {

```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L533-L542
```solidity
File: /src/LBFactory.sol

//@audit: _binStep,_baseFactor,_filterPeriod,_decayPeriod,_reductionFactor,_variableFeeControl,_protocolShare,_maxVolatilityAccumulated
533:    function _getPackedFeeParameters(
534:        uint16 _binStep,
535:        uint16 _baseFactor,
536:        uint16 _filterPeriod,
537:        uint16 _decayPeriod,
538:        uint16 _reductionFactor,
539:        uint24 _variableFeeControl,
540:        uint16 _protocolShare,
541:        uint24 _maxVolatilityAccumulated
542:    ) private pure returns (bytes32) {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L130-L135
```solidity
File: /src/interfaces/ILBFactory.sol

//@audit: uint24 activeId,uint16 binStep
130:    function createLBPair(
131:        IERC20 tokenX,
132:        IERC20 tokenY,
133:        uint24 activeId,
134:        uint16 binStep
135:    ) external returns (ILBPair pair);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L144-L154
```solidity
File: /src/interfaces/ILBFactory.sol
144:    function setPreset(
145:        uint16 binStep,
146:        uint16 baseFactor,
147:        uint16 filterPeriod,
148:        uint16 decayPeriod,
149:        uint16 reductionFactor,
150:        uint24 variableFeeControl,
151:        uint16 protocolShare,
152:        uint24 maxVolatilityAccumulated,
153:        uint16 sampleLifetime
154:    ) external;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L156
```solidity
File: /src/interfaces/ILBFactory.sol

//@audit: uint16 binStep
156:    function removePreset(uint16 binStep) external;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L158-L169
```solidity
File: /src/interfaces/ILBFactory.sol
158:    function setFeesParametersOnPair(
159:        IERC20 tokenX,
160:        IERC20 tokenY,
161:        uint16 binStep,
162:        uint16 baseFactor,
163:        uint16 filterPeriod,
164:        uint16 decayPeriod,
165:        uint16 reductionFactor,
166:        uint24 variableFeeControl,
167:        uint16 protocolShare,
168:        uint24 maxVolatilityAccumulated
169:    ) external;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L203
```solidity
File: /src/interfaces/ILBPair.sol

//@audit: uint24 id_
203:    function findFirstNonEmptyBinId(uint24 id_, bool sentTokenY) external view returns (uint24 id);

//@audit: uint24 id
205:    function getBin(uint24 id) external view returns (uint256 reserveX, uint256 reserveY);

//@audit: uint16 newSize
240:    function increaseOracleLength(uint16 newSize) external;


//@audit: uint24 activeId,uint16 sampleLifetime
250:    function initialize(
251:        IERC20 tokenX,
252:        IERC20 tokenY,
253:        uint24 activeId,
254:        uint16 sampleLifetime,
255:        bytes32 packedFeeParameters
256:    ) external;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L68-L73
```solidity
File: /src/interfaces/ILBRouter.sol

//@audit: //@audit: uint24 activeId,uint16 binStep
68:    function createLBPair(
69:        IERC20 tokenX,
70:        IERC20 tokenY,
71:        uint24 activeId,
72:        uint16 binStep
73:    ) external returns (ILBPair pair);

98:        uint16 binStep,
```

## Cached storage reference used only once
Saving a reference to a storage value that is called several times or needs to be updated is usefull in saving gas. If the variable is only accessed once, it's cheaper to use the state variable directly that one time, and save the **3 gas** the extra stack assignment would spend

### No need to cache \_bins[\_id] as it's only used once
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L261-L294

```solidity
File: /src/LBPair.sol
261:    function pendingFees(address _account, uint256[] memory _ids)

282:                if (_balance != 0) {
283:                    Bin memory _bin = _bins[_id];

285:                    (uint256 _amountX, uint256 _amountY) = _getPendingFees(_bin, _account, _id, _balance);

294:    }
```

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..3d19315 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -280,9 +280,9 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
                 uint256 _balance = balanceOf(_account, _id);

                 if (_balance != 0) {
-                    Bin memory _bin = _bins[_id];

-                    (uint256 _amountX, uint256 _amountY) = _getPendingFees(_bin, _account, _id, _balance);
+
+                    (uint256 _amountX, uint256 _amountY) = _getPendingFees(_bins[_id], _account, _id, _balance);
```

### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154
```solidity
File: /src/LBQuoter.sol
154:        for (uint256 i = swapLength; i > 0; i--) {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711
```solidity
File: /src/LBRouter.sol
711:        for (uint256 i = _pairs.length; i != 0; i--) {
```


### Using bools for storage incurs overhead
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

**Instances affected include**
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L21
```solidity
File: /src/LBToken.sol
21:    mapping(address => mapping(address => bool)) private _spenderApprovals;
```

### Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads . Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L3
```solidity
File: /src/LBToken.sol
3:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L3
```solidity
File: /src/LBRouter.sol
3:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L3
```solidity
File: /src/LBQuoter.sol
3:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L3
```solidity
File: /src/LBPair.sol
3:pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L3
```solidity
File: /src/LBFactory.sol
3:pragma solidity ^0.8.0;
```





