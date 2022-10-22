  # Gas Optimizations

## Summary Of Findings:

|  | Issue | Theoretical gas saved
-- | -- | -- 
1 | Use `uint256` instead of `bool`'s | 6000*2=12000
2 | Caching `msg.sender` is unnecessary and uses more gas | 23
3 | `for loop` in `_getAmountsIn` function in `LBRouter.sol` can be optimized | 70 * `array.length`
4 | Accessing multiple fields of a struct in a mapping can be gas optimized by creating a storage pointer to the struct field | 97+55=152
5 | Usage of `uint`'s smaller than 256 bits incurs overhead | 3*9 = 27
6 | Use bytes32 instead of strings when storing a constant character array | 295*2 = 590

## Detailed Report on Gas Optimization Findings:

### 1. <ins>Use `uint256` instead of `bool`'s</ins>
Bools are used when we just want to know if it is a `true` or `false`. Though it is convenient, it is not practical in solidity due to the high gas costs. EVM, in which your solidity code runs, charges a high amount of gas when a non-zero value is written to a zero value location. This means every time we change `false` to `true`, the gas cost is enormous. This is why it makes sense to use a typical data type like uint256 and use a value of `1` for `true` and `2` for `false`. 

 [This gist example](https://gist.github.com/El-Ku/df9db570d2037ba5f453d5e2c671e5d5) shows that when we used `uint256` instead of `bool` there was a gas saving of around 6000 gas per cycle. This is achieved by being able to avoid continuous writing of non-zero to zero content location. The example provides detailed explanation on how this mitigation works.
 
 There are 2 instances of this in two files:
 
 1. [LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L21)
 ```solidity
 mapping(address => mapping(address => bool)) private _spenderApprovals; 
 ```
 2. [LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L39)
 ```solidity
bool public override creationUnlocked;
 ```

**Mitigation:-**

Use `uint256(1)` and `uint256(2)` instead of `true`/ and `false` of a `bool`.

I couldn't do the mitigation directly in the codebase as this small change will require many further changes through out the project and tests. But I urge the developers to do this mitigation. 

**Diff File:-**  Copied from [main diff file](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550).
```diff
diff --git "a/.\\traderjoe-2022-10\\src\\/LBFactory.sol" "b/.\\traderjoe-gas\\src\\/LBFactory.sol"
index 32ee39c..1a61b38 100644
--- "a/.\\traderjoe-2022-10\\src\\/LBFactory.sol"
+++ "b/.\\traderjoe-gas\\src\\/LBFactory.sol"
@@ -36,7 +36,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
     uint256 public override flashLoanFee;
 
     /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
-    bool public override creationUnlocked;
+    bool public override creationUnlocked;  //@audit gas. use uint256 instead of bool.
 
     ILBPair[] public override allLBPairs;
```

### 2. <ins>Caching msg.sender is unnecessary and uses more gas</ins>
[This simple test](https://gist.github.com/El-Ku/48fd1ba0632fb35992ab89d26834846b) in remix shows that by not caching `msg.sender` and using it directly in the `emit` statement we can save 23 gas.

There is one instance of this in [LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L137-L141). 
```solidity
address _spender = msg.sender;
emit TransferSingle(_spender, _from, _to, _id, _amount);
```

**Mitigation:-**
Use `msg.sender` directly without caching.
```solidity
emit TransferSingle(msg.sender, _from, _to, _id, _amount);
```

**Diff File:-**  Copied from [main diff file](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550).
```diff
diff --git "a/.\\traderjoe-2022-10\\src\\/LBToken.sol" "b/.\\traderjoe-gas\\src\\/LBToken.sol"
index 47aa528..a0031d9 100644
--- "a/.\\traderjoe-2022-10\\src\\/LBToken.sol"
+++ "b/.\\traderjoe-gas\\src\\/LBToken.sol"

@@ -134,11 +134,9 @@ contract LBToken is ILBToken {
         uint256 _id,
         uint256 _amount
     ) public virtual override checkAddresses(_from, _to) checkApproval(_from, msg.sender) {
-        address _spender = msg.sender;
-
         _transfer(_from, _to, _id, _amount);
 
-        emit TransferSingle(_spender, _from, _to, _id, _amount);
+        emit TransferSingle(msg.sender, _from, _to, _id, _amount);  //@audit gas. No need to cache msg.sender
     }
	  
     /// @notice Batch transfers `_amount` tokens of type `_id` from `_from` to `_to`
```

### 3. <ins>for loop in `_getAmountsIn` function in `LBRouter.sol` can be optimized</ins>
The concerned for loop we can optimize is [this](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711-L729). I wrote a [simple test in remix](https://gist.github.com/El-Ku/dbfb90efa62900f9b89696b5bd89dd0b) to show how this optimization will work. As the remix test shows, for an input of array x, the gas saved = `x.length` * 70.

Basically, 
```solidity
for (uint256 i = x.length; i != 0; i--) {  
	//statements...
	}
```
can be re-written as:
```solidity
for (uint256 i = x.length; ; ) {
		//statements...
		unchecked{
    	if(--i == 0) break;
		}
	}
```

This optimization involves three things:
1. We used unchecked arithmetic for `--i`, because we are sure that it wont underflow with the following check.
2. `i--` was changed to `--i`. As pre-increment uses less gas.
3. The `i != 0;` condition in for loop was put inside the loop.

**Diff File:-**  Copied from [main diff file](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550).
```diff
diff --git "a/.\\traderjoe-2022-10\\src\\/LBRouter.sol" "b/.\\traderjoe-gas\\src\\/LBRouter.sol"
index 567c49a..4638048 100644
--- "a/.\\traderjoe-2022-10\\src\\/LBRouter.sol"
+++ "b/.\\traderjoe-gas\\src\\/LBRouter.sol"
@@ -708,7 +708,7 @@ contract LBRouter is ILBRouter {
         // Avoid doing -1, as `_pairs.length == _pairBinSteps.length-1`
         amountsIn[_pairs.length] = _amountOut;
 
-        for (uint256 i = _pairs.length; i != 0; i--) {
+        for (uint256 i = _pairs.length; ; ) {  //@audit gas. use unchecked on i-- and change i-- to --i.
             IERC20 _token = _tokenPath[i - 1];
             uint256 _binStep = _pairBinSteps[i - 1];
 
@@ -726,6 +726,9 @@ contract LBRouter is ILBRouter {
             } else {
                 (amountsIn[i - 1], ) = getSwapIn(ILBPair(_pair), amountsIn[i], ILBPair(_pair).tokenX() == _token);
             }
+            unchecked{
+                if( --i == 0) break;  //@audit gas optimized here.
+            }
         }
     }
```

### 4. <ins>Accessing multiple fields of a struct in a mapping can be gas optimized by creating a storage pointer to the struct</ins>
There are two instances of this in [LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol)
1. [flashLoan](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L452-L453) function
```solidity
Line 452:    _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
Line 453:    _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
```
2. [_updateUserDebts](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L870-L871) function
```solidity
Line 870:    _accruedDebts[_account][_id].debtX = _debtX;
Line 871:    _accruedDebts[_account][_id].debtY = _debtY;
```

To explain how the above instances could be gas optimized, I created a [simple test in remix](https://gist.github.com/El-Ku/cf17e3f546c962a16e3aa26727edd52b). You can see that the first instance can theoretically save 97 gas while the next instance can save 55 gas. Which means a total of 97+55 = 152 gas.

**Diff File:-**  Copied from [main diff file](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550).
```diff
diff --git "a/.\\traderjoe-2022-10\\src\\/LBPair.sol" "b/.\\traderjoe-gas\\src\\/LBPair.sol"
index 717270e..a68f533 100644
--- "a/.\\traderjoe-2022-10\\src\\/LBPair.sol"
+++ "b/.\\traderjoe-gas\\src\\/LBPair.sol"
@@ -449,8 +449,9 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
 
         uint256 _totalSupply = totalSupply(_id);
 
-        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
-        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
+        Bin storage binPointer = _bins[_id]; //@audit gas. create a reference for _bins[_id]
+        binPointer.accTokenXPerShare = binPointer.accTokenXPerShare + _feesX.getTokenPerShare(_totalSupply);  
+        binPointer.accTokenYPerShare = binPointer.accTokenXPerShare + _feesY.getTokenPerShare(_totalSupply);
 
         emit FlashLoan(msg.sender, _to, _amountXOut, _amountYOut, _feesX.total, _feesY.total);
     }
@@ -866,9 +867,10 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
     ) private {
         uint256 _debtX = _bin.accTokenXPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET);
         uint256 _debtY = _bin.accTokenYPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET);
-
-        _accruedDebts[_account][_id].debtX = _debtX;
-        _accruedDebts[_account][_id].debtY = _debtY;
+        //@audit gas. Create a reference for the state variable _accruedDebts.
+        Debts storage debtsCached = _accruedDebts[_account][_id];
+        debtsCached.debtX = _debtX;
+        debtsCached.debtY = _debtY;
     }
 
     /// @notice Update the unclaimed fees of a given user before a transfer
```

### 5. <ins>Usage of `uint`'s smaller than 256 bits incurs overhead</ins>

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

Source:- https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

To make sure this optimization actually works, I [created a simple test in remix](https://gist.github.com/El-Ku/4462bfc60d559d4567c22ff2d64823e4). It seems that a minimum of 3 gas was saved per usage of `uint256` instead of `uint8`.  

There are a total of 9 instances of this issue. 
1. 6 instances in [src/libraries/BitMath.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol)
```solidity
Line 16:    uint8 _bit,
Line 28:    function significantBit(uint256 _integer, bool _isMostSignificant) internal pure returns (uint8) {
Line 39:    function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {
Line 56:    function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {
Line 69:    function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {
Line 108:   function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {
```
2. 3 instances in [src/libraries/TreeMath.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol)
```solidity
Line 35:    bit = current.closestBit(uint8(bit), _rightSide);
Line 47:    bit = current.closestBit(uint8(bit), _rightSide);
Line 59:    bit = current.closestBit(uint8(_binId), _rightSide);
```

**Diff File:-**  Copied from [main diff file](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550).
```diff
diff --git "a/.\\traderjoe-2022-10\\src\\/libraries/BitMath.sol" "b/.\\traderjoe-gas\\src\\/libraries/BitMath.sol"
index d088fdf..f6afd4b 100644
--- "a/.\\traderjoe-2022-10\\src\\/libraries/BitMath.sol"
+++ "b/.\\traderjoe-gas\\src\\/libraries/BitMath.sol"
@@ -13,7 +13,7 @@ library BitMath {
     /// @return The index of the closest non-zero bit. If there is no closest bit, it returns max(uint256)
     function closestBit(
         uint256 _integer,
-        uint8 _bit,
+        uint256 _bit,  //@audit gas. make it uint256
         bool _rightSide
     ) internal pure returns (uint256) {
         if (_rightSide) {
@@ -36,7 +36,7 @@ library BitMath {
     /// @param bit The index of the bit to start searching at
     /// @return id The index of the closest non null bit on the right of x.
     /// If there is no closest bit, it returns max(uint256)
-    function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {
+    function closestBitRight(uint256 x, uint256 bit) internal pure returns (uint256 id) { //@audit gas. make `bit` uint256
         unchecked {
             uint256 _shift = 255 - bit;
             x <<= _shift;
@@ -53,7 +53,7 @@ library BitMath {
     /// @param bit The index of the bit to start searching at
     /// @return id The index of the closest non null bit on the left of x.
     /// If there is no closest bit, it returns max(uint256)
-    function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {
+    function closestBitLeft(uint256 x, uint256 bit) internal pure returns (uint256 id) {  //@audit gas. make `bit` uint256
         unchecked {
             x >>= bit;
 
@@ -66,7 +66,7 @@ library BitMath {
     /// @notice Returns the index of the most significant bit of x
     /// @param x The value as a uint256
     /// @return msb The index of the most significant bit of x
-    function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {
+    function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) { //@audit gas. return uint256 instead of uint8.
         unchecked {
             if (x >= 1 << 128) {
                 x >>= 128;
@@ -105,7 +105,7 @@ library BitMath {
     /// @notice Returns the index of the least significant bit of x
     /// @param x The value as a uint256
     /// @return lsb The index of the least significant bit of x
-    function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {
+    function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) { //@audit gas. return uint256 instead of uint8.
         unchecked {
             if (x << 128 != 0) {
                 x <<= 128;
diff --git "a/.\\traderjoe-2022-10\\src\\/libraries/TreeMath.sol" "b/.\\traderjoe-gas\\src\\/libraries/TreeMath.sol"
index 31d4d8b..9718f4c 100644
--- "a/.\\traderjoe-2022-10\\src\\/libraries/TreeMath.sol"
+++ "b/.\\traderjoe-gas\\src\\/libraries/TreeMath.sol"
@@ -32,7 +32,7 @@ library TreeMath {
             // Search in depth 2
             if ((_rightSide && bit != 0) || (!_rightSide && bit != 255)) {
                 current = _tree[2][_binId];
-                bit = current.closestBit(uint8(bit), _rightSide);
+                bit = current.closestBit(bit, _rightSide);
 
                 if (bit != type(uint256).max) {
                     return _getBottomId(_binId, uint24(bit));
@@ -44,7 +44,7 @@ library TreeMath {
             // Search in depth 1
             if ((_rightSide && bit != 0) || (!_rightSide && bit != 255)) {
                 current = _tree[1][_binId];
-                bit = current.closestBit(uint8(bit), _rightSide);
+                bit = current.closestBit(bit, _rightSide);
 
                 if (bit != type(uint256).max) {
                     _binId = _getBottomId(_binId, uint24(bit));
@@ -56,7 +56,7 @@ library TreeMath {
 
             // Search in depth 0
             current = _tree[0][0];
-            bit = current.closestBit(uint8(_binId), _rightSide);
+            bit = current.closestBit(_binId, _rightSide);
             if (bit == type(uint256).max) revert TreeMath__ErrorDepthSearch();
 
             current = _tree[1][bit];
```

### 6. <ins>Use bytes32 instead of strings when storing a constant character array</ins>
The [Solidity docs says](https://docs.soliditylang.org/en/v0.8.12/types.html#bytes-and-string-as-arrays):
> As a general rule, use bytes for arbitrary-length raw byte data and string for arbitrary-length string (UTF-8) data. 

To see if gas could be saved by this suggestion, I wrote this [simple test in remix](https://gist.github.com/El-Ku/9a1fc60ff58d776854f44b966b842668) and it was found out that when deploying, 25000 gas could be saved and the execution cost of getter function could be reduced by 295 gas. Note that this test uses the strings from the codebase. So the gas values saved in the test can be directly applied to account for the suggested optimization. 

There are two instances of this in [LBToken.sol]()
```solidity
Line 29:    string private constant _name = "Liquidity Book Token";
Line 30:    string private constant _symbol = "LBT";
```

Mitigation would be to simply convert these into the following:
```solidity
bytes32 private constant _name = "Liquidity Book Token";
bytes32 private constant _symbol = "LBT";
```
Make sure that along with the above change, the return types of getter functions `name()` and `symbol()` are changed to `bytes32` from `string` as well.

Note that we could save 3 more gas by using bytes20 and bytes3 respectively for the two strings. But that seems to make the code less readable and makes the getter function more complicated for saving just 3 gas.

**Diff File:-**  Copied from [main diff file](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550).
```diff
diff --git "a/.\\traderjoe-2022-10\\src\\/LBToken.sol" "b/.\\traderjoe-gas\\src\\/LBToken.sol"
index 47aa528..a0031d9 100644
--- "a/.\\traderjoe-2022-10\\src\\/LBToken.sol"
+++ "b/.\\traderjoe-gas\\src\\/LBToken.sol"
@@ -26,8 +26,8 @@ contract LBToken is ILBToken {
     /// @dev  Mapping from account to set of ids, where user currently have a non-zero balance
     mapping(address => EnumerableSet.UintSet) private _userIds;
 
-    string private constant _name = "Liquidity Book Token";
-    string private constant _symbol = "LBT";
+    string private constant _name = "Liquidity Book Token";  //@audit gas. use bytes32
+    string private constant _symbol = "LBT";        //@audit gas. use bytes32
 
     modifier checkApproval(address _from, address _spender) {
         if (!_isApprovedForAll(_from, _spender)) revert LBToken__SpenderNotApproved(_from, _spender);
```
## Diff between Original repo and Gas-optimized repo

Note that, not all issues listed are mitigated in the modified repo. As some changes require several other changes in different parts of the codebase. But I have left an `@audit` tag in such cases so that it reflects in the below diff.

[Diff file in gist](https://gist.github.com/El-Ku/8ba42bbd379e5e63bf25a8073cd95550)

## Diff between foundry gas reports of pre and post optimizations

The mitigation showed the following diff from the command `forge snapshot --diff .gas-snapshot_orig`:

```bash
testInversePriceForOppositeBins() (gas: 0 (0.000%))
testAddToken() (gas: 0 (0.000%))
testLockRequest() (gas: 0 (0.000%))
testRemoveToken() (gas: 0 (0.000%))
testRequestFaucetTokens() (gas: 0 (0.000%))
testRequestFaucetTokensByOperator() (gas: 0 (0.000%))
testRevertOnNonEOA() (gas: 0 (0.000%))
testSetRequestAmount() (gas: 0 (0.000%))
testSetRequestCooldown() (gas: 0 (0.000%))
testWithdrawAvax() (gas: 0 (0.000%))
testWithdrawToken() (gas: 0 (0.000%))
testAddRemovePresets() (gas: 0 (0.000%))
testAvailableBinSteps() (gas: 0 (0.000%))
testSetPresets() (gas: 0 (0.000%))
testConstructor() (gas: 0 (0.000%))
testCreateLBPair() (gas: 0 (0.000%))
testCreatePairWhenFactoryIsUnlocked() (gas: 0 (0.000%))
testFactoryLockedReverts() (gas: 0 (0.000%))
testFeesAboveMaxBaseFactorReverts(uint8) (gas: 0 (0.000%))
testFeesAboveMaxVolatilityReverts(uint8) (gas: 0 (0.000%))
testForDoubleIgnored() (gas: 0 (0.000%))
testForIdenticalAddressesReverts() (gas: 0 (0.000%))
testForInvalidBaseFactor() (gas: 0 (0.000%))
testForInvalidBinStepOverflowReverts() (gas: 0 (0.000%))
testForInvalidBinStepUnderflowReverts() (gas: 0 (0.000%))
testForInvalidFeeRecipient() (gas: 0 (0.000%))
testForInvalidFilterPeriod() (gas: 0 (0.000%))
testForInvalidProtocolShare() (gas: 0 (0.000%))
testForInvalidReductionFactor() (gas: 0 (0.000%))
testForSettingFlashloanFee() (gas: 0 (0.000%))
testForZeroAddressPairReverts() (gas: 0 (0.000%))
testIfPairAlreadyExistsReverts() (gas: 0 (0.000%))
testInvalidBinStepWhileCreatingLBPair() (gas: 0 (0.000%))
testQuoteAssets() (gas: 0 (0.000%))
testSetFeeRecipient() (gas: 0 (0.000%))
testSetFeeRecipientNotByOwnerReverts() (gas: 0 (0.000%))
testSetFeesParametersOnPair() (gas: 0 (0.000%))
testSetFeesParametersOnPairReverts() (gas: 0 (0.000%))
testsetFactoryLockedState() (gas: 0 (0.000%))
testFailFlashlaonWithReentrancy() (gas: 0 (0.000%))
testFailFlashloanMoreThanReserves() (gas: 0 (0.000%))
testIncreaseOracleLength() (gas: 0 (0.000%))
testVerifyOracleInitialParams() (gas: 0 (0.000%))
testInvalidTokenPathReverts() (gas: 0 (0.000%))
testSwapInsufficientAmountReverts() (gas: 0 (0.000%))
testCollectingFeesOnlyFeeRecipient() (gas: 0 (0.000%))
testInsufficientLiquidityBurnedReverts() (gas: 0 (0.000%))
testMintWrongLengthsReverts() (gas: 0 (0.000%))
testPendingFeesNotIncreasingReverts() (gas: 0 (0.000%))
testConstructor() (gas: 0 (0.000%))
testGetSwapInOnV1Pair() (gas: 0 (0.000%))
testInvalidLength() (gas: 0 (0.000%))
testAddLiquidityAVAXnotAVAXReverts() (gas: 0 (0.000%))
testForIdDesiredOverflowReverts() (gas: 0 (0.000%))
testForLengthsMismatchReverts() (gas: 0 (0.000%))
testWrongTokenOrderReverts() (gas: 0 (0.000%))
testSwapExactTokensForTokensMultiplePairsWithV1() (gas: 0 (0.000%))
testSwappingOnNotExistingV1PairReverts() (gas: 0 (0.000%))
testTaxTokenSwappedOnV1Pairs() (gas: 0 (0.000%))
testSwapExactAVAXForTokensSinglePair() (gas: 0 (0.000%))
testSwapExactAVAXForTokensSupportingFeeOnTransferTokens() (gas: 0 (0.000%))
testSwapExactTokensForAVAXSupportingFeeOnTransferTokens() (gas: 0 (0.000%))
testSwapExactTokensForAvaxSinglePair() (gas: 0 (0.000%))
testSwapExactTokensForTokensMultiplePairs() (gas: 0 (0.000%))
testSwapExactTokensForTokensSinglePair() (gas: 0 (0.000%))
testSwapExactTokensForTokensSupportingFeeOnTransferTokens() (gas: 0 (0.000%))
testSwappingOnNotExistingV2PairReverts() (gas: 0 (0.000%))
testTaxTokenEqualOnlyV2Swap() (gas: 0 (0.000%))
testConstructor() (gas: 0 (0.000%))
testCreateLBPair() (gas: 0 (0.000%))
testEnsureModifierLiquidity() (gas: 0 (0.000%))
testGetIdFromPrice() (gas: 0 (0.000%))
testGetPriceFromId() (gas: 0 (0.000%))
testModifierEnsure() (gas: 0 (0.000%))
testModifierVerifyInputs() (gas: 0 (0.000%))
testModifieronlyFactoryOwner() (gas: 0 (0.000%))
testSweep() (gas: 0 (0.000%))
testSweepMax() (gas: 0 (0.000%))
testPrivateViewFunctions() (gas: 0 (0.000%))
testSelfApprovalReverts() (gas: 0 (0.000%))
testInternalApproval() (gas: 0 (0.000%))
testInternalBurn(uint256,uint256) (gas: 0 (0.000%))
testInternalBurnFrom0AddressReverts() (gas: 0 (0.000%))
testInternalExcessiveBurnAmountReverts(uint128,uint128) (gas: 0 (0.000%))
testInternalMint(uint256) (gas: 0 (0.000%))
testInternalMintTo0AddressReverts() (gas: 0 (0.000%))
testGetSwapInOverflowReverts() (gas: -3 (-0.000%))
testOracleSampleFromWith2Samples() (gas: -3 (-0.001%))
testSwapYtoXSingleBinFromGetSwapOut() (gas: -3 (-0.001%))
testSwapXtoYSingleBinFromGetSwapOut() (gas: -3 (-0.001%))
testSwapXtoYSingleBinFromGetSwapIn() (gas: -3 (-0.001%))
testOracleSampleFromEdgeCases() (gas: -3 (-0.001%))
testGetSwapOutWithMultipleChoices() (gas: -27 (-0.001%))
testDistributionOverflowReverts() (gas: -6 (-0.001%))
testFuzzingAddLiquidity(uint256) (gas: -9 (-0.001%))
testFeeOnActiveBinReverse() (gas: -10 (-0.001%))
testFeeOnActiveBin() (gas: -10 (-0.001%))
testClaimFeesComplex(uint256,uint256) (gas: -15 (-0.001%))
testClaimProtocolFees() (gas: -15 (-0.001%))
testFlawedCompositionFactor() (gas: -21 (-0.001%))
testInsufficientLiquidityMinted() (gas: -21 (-0.001%))
testSwapWithDifferentBinSteps() (gas: -27 (-0.001%))
testGetSwapInWrongAmountsReverts() (gas: -27 (-0.001%))
testBalanceOfBatch() (gas: -15 (-0.001%))
testSafeBatchTransferNotApprovedReverts() (gas: -15 (-0.001%))
testSafeTransferNotApprovedReverts() (gas: -15 (-0.001%))
testGetSwapInOnComplexRoute() (gas: -27 (-0.001%))
testGetSwapInWithMultipleChoices() (gas: -27 (-0.002%))
testGetSwapOutOnV2Pair() (gas: -27 (-0.002%))
testGetSwapInOnV2Pair() (gas: -27 (-0.002%))
testModifierCheckLength() (gas: -33 (-0.002%))
testAddLiquidityTaxToken() (gas: -64 (-0.002%))
testBurnLiquidity() (gas: -36 (-0.002%))
testSafeBatchTransferFromReverts() (gas: -39 (-0.002%))
testSafeTransferFromReverts() (gas: -37 (-0.002%))
testAddLiquidityNoSlippage() (gas: -43 (-0.002%))
testRemoveLiquidityReverseOrder() (gas: -44 (-0.002%))
testAddLiquidityAVAXReversed() (gas: -101 (-0.002%))
testAddLiquidityAVAX() (gas: -111 (-0.002%))
testRemoveLiquiditySlippageReverts() (gas: -166 (-0.003%))
testOracleSampleFromWith100Samples() (gas: -694 (-0.003%))
testAddLiquidityIgnored() (gas: -54 (-0.003%))
testOracleSampleFromWith100SamplesNotAllInitialized() (gas: -632 (-0.003%))
testSweepLBToken() (gas: -60 (-0.003%))
testClaimFeesY() (gas: -45 (-0.004%))
testClaimFeesX() (gas: -45 (-0.004%))
testFlashloan() (gas: -64 (-0.004%))
testSafeBatchTransferFrom() (gas: -60 (-0.004%))
testFeesOnTokenTransfer() (gas: -60 (-0.004%))
testForAmountSlippageCaughtReverts() (gas: -97 (-0.004%))
testSwapXtoYDistantBinsFromGetSwapOut() (gas: -77 (-0.004%))
testSwapXtoYDistantBinsFromGetSwapIn() (gas: -77 (-0.004%))
testSwapXtoYConsecutiveBinFromGetSwapOut() (gas: -77 (-0.005%))
testSwapXtoYConsecutiveBinFromGetSwapIn() (gas: -77 (-0.005%))
testGetSwapOutOnComplexRoute() (gas: -114 (-0.006%))
testSwapYtoXConsecutiveBinFromGetSwapOut() (gas: -113 (-0.007%))
testSwapYtoXConsecutiveBinFromGetSwapIn() (gas: -113 (-0.007%))
testTLowerThanTimestamp() (gas: -513 (-0.007%))
testSafeTransferFrom() (gas: -115 (-0.010%))
testSwapYtoXDistantBinsFromGetSwapOut() (gas: -199 (-0.011%))
testSwapYtoXDistantBinsFromGetSwapIn() (gas: -199 (-0.011%))
testForceDecay() (gas: -947 (-0.012%))
testGetSwapInMoreBins() (gas: -317 (-0.016%))
testSwapYtoXSingleBinFromGetSwapIn() (gas: -89 (-0.017%))
testForIdSlippageCaughtReverts() (gas: -441 (-0.020%))
testGetSwapOutOnV1Pair() (gas: -87 (-0.028%))
testSwapTokensForExactAVAXSinglePair() (gas: -158 (-0.056%))
testSwapTokensForExactTokensMultiplePairs() (gas: -506 (-0.063%))
testSwapAVAXForExactTokensSinglePair() (gas: -158 (-0.063%))
testSwapTokensForExactTokensSinglePair() (gas: -158 (-0.067%))
testSwapTokensForExactTokensMultiplePairsWithV1() (gas: -386 (-0.067%))
testgetAllLBPairs() (gas: -4618 (-0.081%))
testSetLBPairImplementation() (gas: -57126 (-0.341%))
testConstructor(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24) (gas: -19054 (-0.374%))
Overall gas change: -88563 (-1.364%)
```

## Conclusions:

The tests showed a reduction of 1.364% in gas usage after the mitigations are applied. This is just for the mitigations which I was able to apply in the project. There were other suggestions which I couldn't test it out, as they affected other parts in the code as well.

Just by looking at the theoretical amount of gas saved (which we got from our sample tests in remix), we saved a total of 12000 + 23 + (70 * 5) + 152 + 27 + 590 = <ins>13142 gas</ins>.  We assumed here that the array passed to the for loop had an average length of 5. 
