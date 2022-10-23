# Gas Savings

## Upgrade openzeppelin-contracts to latest version and save up to 12% in gas
You are currently using version 4.6.0 of openzeppelin-contracts and the latest is 4.7.0

Gas diff snapshot
```
testForIdDesiredOverflowReverts() (gas: -838 (-0.245%)) 
testSwapExactTokensForTokensSupportingFeeOnTransferTokens() (gas: -1287 (-0.251%)) 
testSwapExactAVAXForTokensSupportingFeeOnTransferTokens() (gas: -978 (-0.253%)) 
testSetRequestCooldown() (gas: -544 (-0.270%)) 
testTaxTokenEqualOnlyV2Swap() (gas: -1684 (-0.276%)) 
testAddLiquidityTaxToken() (gas: -17728 (-0.494%)) 
testAddToken() (gas: -14948 (-1.778%)) 
testQuoteAssets() (gas: -14842 (-2.007%)) 
Overall gas change: -177473 (-12.791%)
```

## Use solidity version 0.8.17 to gain some gas boost
Make sure you use latest solidity version;
```diff
diff --git a/foundry.toml b/foundry.toml
index 4f8acd4..83ec53e 100644
--- a/foundry.toml
+++ b/foundry.toml
@@ -4,6 +4,8 @@ out = 'out'
 libs = ['lib']
 optimizer = true
 optimizer_runs = 800
+solc = "0.8.17"

diff --git a/test/LBPair.t.sol b/test/LBPair.t.sol
index d84bb5b..b7778ba 100644
--- a/test/LBPair.t.sol
+++ b/test/LBPair.t.sol
@@ -1,5 +1,5 @@
 // SPDX-License-Identifier: UNLICENSED
-pragma solidity 0.8.7;
+pragma solidity 0.8.17;
 
 import "./TestHelper.sol";
```

Gas diff snapshot
```
testAddRemovePresets() (gas: -1425 (-1.772%)) 
testAvailableBinSteps() (gas: -2222 (-1.781%)) 
testSwappingOnNotExistingV1PairReverts() (gas: -4083 (-1.967%)) 
testSwappingOnNotExistingV2PairReverts() (gas: -4091 (-2.074%)) 
testgetAllLBPairs() (gas: -130452 (-2.294%)) 
testConstructor() (gas: -256 (-2.340%)) 
testForInvalidBinStepUnderflowReverts() (gas: -401 (-2.643%)) 
testInvalidTokenPathReverts() (gas: -928 (-2.667%)) 
testForInvalidBinStepOverflowReverts() (gas: -404 (-2.671%)) 
testConstructor() (gas: -384 (-3.242%)) 
testPrivateViewFunctions() (gas: -456 (-3.628%)) 
testModifierEnsure() (gas: -2222 (-3.645%)) 
testConstructor() (gas: -384 (-3.858%)) 
testModifierVerifyInputs() (gas: -3153 (-4.017%)) 
Overall gas change: -582995 (-88.032%)
```

## Use `calldata` instead of `memory` to save gas

```diff
diff --git a/src/LBToken.sol b/src/LBToken.sol
index 47aa528..e5bb07f 100644
--- a/src/LBToken.sol
+++ b/src/LBToken.sol
@@ -76,7 +76,7 @@ contract LBToken is ILBToken {
     /// @param _accounts The addresses of the owners
     /// @param _ids The token ids
     /// @return batchBalances The balance for each (account, id) pair
-    function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
+    function balanceOfBatch(address[] calldata _accounts, uint256[] calldata _ids)
         public
         view
         virtual
@@ -149,8 +149,8 @@ contract LBToken is ILBToken {
     function safeBatchTransferFrom(
         address _from,
         address _to,
-        uint256[] memory _ids,
-        uint256[] memory _amounts
+        uint256[] calldata _ids,
+        uint256[] calldata _amounts
     )
         public
         virtual
```

Gas Snapshot
```
estFeesOnTokenTransfer() (gas: -2281 (-0.163%)) 
testSweepLBToken() (gas: -3262 (-0.181%)) 
testFeeOnActiveBinReverse() (gas: -1642 (-0.187%)) 
testFeeOnActiveBin() (gas: -1642 (-0.187%)) 
testRemoveLiquiditySlippageReverts() (gas: -12103 (-0.192%)) 
testSafeBatchTransferFrom() (gas: -3263 (-0.206%)) 
testSetLBPairImplementation() (gas: 42084 (0.251%)) 
testModifierCheckLength() (gas: -5018 (-0.264%)) 
testConstructor(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24) (gas: 14036 (0.276%)) 
testPendingFeesNotIncreasingReverts() (gas: -62 (-0.356%)) 
testInsufficientLiquidityBurnedReverts() (gas: -124 (-0.516%)) 
testSafeBatchTransferFromReverts() (gas: -11112 (-0.546%)) 
testMintWrongLengthsReverts() (gas: -606 (-1.358%)) 
Overall gas change: -13933 (-5.264%)
```

