# PTP - Gas Optimizations

## Summary Of Gas Savings:

|     | Contract              | Function                                              | Original Avg Gas Cost | Optimized Avg Gas Cost | Avg Gas Saved | % Avg Gas Saved |
| --- | --------------------- | ----------------------------------------------------- | --------------------- | ---------------------- | ------------- | --------------- |
| 1   | LBFactory.sol         | createLBPair                                          | 302135                | 301090                 | 1045          | -0.35           |
| 2   | LBFactory.sol         | getPreset                                             | 2651                  | 2259                   | 392           | -14.79          |
| 3   | LBFactory.sol         | removePreset                                          | 5909                  | 5826                   | 83            | -1.4            |
| 4   | LBFactory.sol         | setFeesParametersOnPair                               | 3530                  | 3444                   | 86            | -2.44           |
| 5   | LBFactory.sol         | setPreset                                             | 47455                 | 46882                  | 573           | -1.21           |
| 6   | LBpair.sol            | burn                                                  | 111199                | 111171                 | 28            | -0.03           |
| 7   | LBpair.sol            | collectFees                                           | 27696                 | 27686                  | 10            | -0.04           |
| 8   | LBpair.sol            | collectProtocolFees                                   | 8817                  | 8810                   | 7             | -0.08           |
| 9   | LBpair.sol            | flashLoan                                             | 55799                 | 55774                  | 25            | -0.04           |
| 10  | LBpair.sol            | mint                                                  | 1402766               | 1402749                | 17            | -0.001          |
| 11  | LBpair.sol            | name                                                  | 575                   | 250                    | 325           | -56.52          |
| 12  | LBpair.sol            | swap                                                  | 50433                 | 50192                  | 241           | -0.48           |
| 13  | LBpair.sol            | symbol                                                | 591                   | 255                    | 336           | -56.85          |
| 14  | LBQuoter.sol          | findBestPathFromAmountIn                              | 45266                 | 44513                  | 753           | -1.66           |
| 15  | LBQuoter.sol          | findBestPathFromAmountOut                             | 38128                 | 36958                  | 1170          | -3.07           |
| 16  | LBRouter.sol          | addLiquidity                                          | 1271111               | 1271094                | 17            | -0.001          |
| 17  | LBRouter.sol          | addLiquidityAVAX                                      | 1466315               | 1466296                | 19            | -0.001          |
| 18  | LBRouter.sol          | removeLiquidity                                       | 579712                | 579685                 | 27            | -0.005          |
| 19  | LBRouter.sol          | removeLiquidityAVAX                                   | 976395                | 976372                 | 23            | -0.002          |
| 20  | LBRouter.sol          | swapAVAXForExactTokens                                | 28690                 | 28587                  | 103           | -0.359          |
| 21  | LBRouter.sol          | swapExactAVAXForTokens                                | 51907                 | 51855                  | 52            | -0.1            |
| 22  | LBRouter.sol          | swapExactAVAXForTokensSupportingFeeOnTransferTokens   | 51197                 | 51151                  | 46            | -0.09           |
| 23  | LBRouter.sol          | swapExactTokensForAVAX                                | 70017                 | 69943                  | 74            | -0.1            |
| 24  | LBRouter.sol          | swapExactTokensForAVAXSupportingFeeOnTransferTokens   | 56206                 | 56140                  | 66            | -0.12           |
| 25  | LBRouter.sol          | swapExactTokensForTokens                              | 105179                | 105027                 | 152           | -0.14           |
| 26  | LBRouter.sol          | swapExactTokensForTokensSupportingFeeOnTransferTokens | 55095                 | 55020                  | 75            | -0.14           |
| 27  | LBRouter.sol          | swapTokensForExactAVAX                                | 29628                 | 29537                  | 91            | -0.31           |
| 28  | LBRouter.sol          | swapTokensForExactTokens                              | 127916                | 127282                 | 634           | -0.5            |
| 29  | FlashloanBorrower.sol | flashBorrow                                           | 77648                 | 77620                  | 28            | -0.04           |
| 30  | FlashloanBorrower.sol | flashBorrowWithReentrancy                             | 33427                 | 33408                  | 19            | -0.06           |

## Reductions in Deployment Costs:

|     | Contract                 | Original Deployment Cost | Optimized Deployment Cost | Avg Gas Saved | % Avg Gas Saved |
| --- | ------------------------ | ------------------------ | ------------------------- | ------------- | --------------- |
| 1   | LBFactory.sol            | 2086657                  | 2055021                   | 31636         | -1.52           |
| 2   | LBPair.sol               | 4835641                  | 4808605                   | 27036         | -0.56           |
| 3   | LBQuoter.sol             | 1899867                  | 1832388                   | 67479         | -3.55           |
| 4   | LBRouter.sol             | 4591267                  | 4569640                   | 21627         | -0.47           |
| 5   | LBRouter.Liquidity.t.sol | 19179229                 | 19098800                  | 80429         | -0.42           |
| 6   | LBRouter.Swaps.t.sol     | 17699454                 | 17619053                  | 80401         | -0.45           | 

----

## [G-01] Increments in loops can be unchecked
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for overflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default overflow check wastes gas in every iteration of virtually every for loop.

The following instances of this issue were found:  
File: LBQuoter.sol line 75  
File: LBQuoter.sol line 100  
File: LBQuoter.sol line 177  

## [G-02] ++i cost less than i++
Use pre-increment (++i) where possibile as it's cheaper than post-increment (i++). This statement is true even with the optimizer enabled.

The following instances of this issue were found:  
File: LBQuoter.sol line 75  
File: LBQuoter.sol line 100  
File: LBQuoter.sol line 177

## [G-03] Short-circuit with || and &&
For `||` and `&&` operators, the second case is not checked if the first case gives the result of the logical expression. Put the lower-cost expression first so the higher-cost expression may be skipped (short-circuit).

The following instances of this issue were found:  
File: LBFactory.sol line 241
```diff
File: LBFactory.sol
-        if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);
+        if (msg.sender != _owner && !creationUnlocked) revert LBFactory__FunctionIsLockedForUsers(msg.sender); // @audit gasop - short circuiting
```  

File: LBQuoter.sol line 79
```diff
File: LBQuoter.sol
- swapAmountIn != 0 && (swapAmountIn < quote.amounts[i - 1] || quote.amounts[i - 1] == 0)
+ (quote.amounts[i - 1] == 0 || swapAmountIn < quote.amounts[i - 1]) && swapAmountIn != 0  // @audit gasop - short circuiting
``` 

File: LBQuoter.sol line 184
```diff
File: LBQuoter.sol
- swapAmountIn != 0 && (swapAmountIn < quote.amounts[i - 1] || quote.amounts[i - 1] == 0)
+ (quote.amounts[i - 1] == 0 || swapAmountIn < quote.amounts[i - 1]) && swapAmountIn != 0  // @audit gasop - short circuiting
```  

## [G-04] Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

Example:   
File: LBFactory.sol line 143
```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -143,7 +143,9 @@ File: LBFactory.sol
-        baseFactor = _preset.decode(type(uint16).max, _shift += 16);
-        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
-        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
-        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
-        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
-        protocolShare = _preset.decode(type(uint16).max, _shift += 24);
-        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
+        unchecked {  // @audit gasop - can be unchecked
+            baseFactor = _preset.decode(type(uint16).max, _shift += 16);
+            filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
+            decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
+            reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
+            variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
+            protocolShare = _preset.decode(type(uint16).max, _shift += 24);
+            maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
+        }
```

List of other instances found of this issue:  
File: LBFactory.sol line 144  
File: LBFactory.sol line 145  
File: LBFactory.sol line 146  
File: LBFactory.sol line 147  
File: LBFactory.sol line 148  
File: LBFactory.sol line 149  
File: LBFactory.sol line 375  
File: LBFactory.sol line 402  
File: LBFactory.sol line 403  
File: LBFactory.sol line 562  
File: LBPair.sol line 336  
File: LBQuoter.sol line 65  
File: LBQuoter.sol line 77  
File: LBQuoter.sol line 80  
File: LBQuoter.sol line 102  
File: LBQuoter.sol line 144  
File: LBQuoter.sol line 154  
File: LBQuoter.sol line 156  
File: LBQuoter.sol line 158  
File: LBQuoter.sol line 184  
File: LBQuoter.sol line 201  
File: LBRouter.sol line 708  
File: LBRouter.sol line 711  
File: LBRouter.sol line 712  
File: LBRouter.sol line 713  
File: LBRouter.sol line 715  
File: LBRouter.sol line 725  

## [G-05] bytes32 is cheaper than string
A string or bytes variable is dynamically sized; we should use a bytes32 if our string is short enough to fit.

The following instances of this issue were found:  
File: LBToken.sol line 29  
File: LBToken.sol line 30  
```diff
@@ -29,2 +29,2 @@ contract LBToken is ILBToken {
-    string private constant _name = "Liquidity Book Token";
-    string private constant _symbol = "LBT";
+    bytes32 private constant _name = "Liquidity Book Token";  // @audit - use fixed-size bytes32 for constant strings
+    bytes32 private constant _symbol = "LBT";  // @audit - use fixed-size bytes32 for constant strings
```

File: LBToken.sol line 49  
File: LBToken.sol line 55  

## [G-06] Booleans use 8 bits while you only need 1 bit
Under the hood of solidity, Booleans (bool) are uint8 which means they use 8 bits of storage. A Boolean can only have two values: True or False. This means that you can store a boolean in only a single bit. You can pack 256 booleans in a single word. Check out https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/#2a76 for more info.

The following instances of this issue were found:  
File: LBFactory.sol line 39  
File: LBFactory.sol line 92  
File: LBFactory.sol line 316  
File: LBFactory.sol line 485  
File: LBPair.sol line 243  
File: LBPair.sol line 304  
File: LBQuoter.sol line 102  
File: LBQuoter.sol line 179  
File: LBQuoter.sol line 237  
File: LBRouter.sol line 93  
File: LBRouter.sol line 151  
File: LBRouter.sol line 320  
File: LBRouter.sol line 798  
File: LBRouter.sol line 848  
File: LBRouter.sol line 964  
File: LBToken.sol line 21  
File: LBToken.sol line 115  
File: LBToken.sol line 122  
File: LBToken.sol line 256  
File: LBToken.sol line 269

## [G-07] Use external visibility modifier when possible
Functions with the public visibility modifier are costlier than external. Default to using the external modifier until you need to expose it to other functions within your contract.

The following instances of this issue were found:  
File: LBToken.sol line 49  
File: LBToken.sol line 55  
File: LBToken.sol line 63  
File: LBToken.sol line 71  
File: LBToken.sol line 100  
File: LBToken.sol line 107  
File: LBToken.sol line 115  
File: LBToken.sol line 122

```diff
@@ -49 +49 @@ File: LBToken.sol
-    function name() public pure virtual override returns (string memory) {
+    function name() external pure virtual override returns (bytes32) {  // @audit gasop - use external modifier
@@ -55 +55 @@ File: LBToken.sol
-    function symbol() public pure virtual override returns (string memory) {
+    function symbol() external pure virtual override returns (bytes32) {  // @audit gasop - use external modifier
@@ -100 +100 @@ File: LBToken.sol
-    function userPositionAtIndex(address _account, uint256 _index) public view virtual override returns (uint256) {
+    function userPositionAtIndex(address _account, uint256 _index) external view virtual override returns (uint256) {  // @audit gasop - use external modifier
@@ -107 +107 @@ File: LBToken.sol
-    function userPositionNumber(address _account) public view virtual override returns (uint256) {
+    function userPositionNumber(address _account) external view virtual override returns (uint256) {  // @audit gasop - use external modifier
@@ -115 +115 @@ File: LBToken.sol
-    function isApprovedForAll(address _owner, address _spender) public view virtual override returns (bool) {
+    function isApprovedForAll(address _owner, address _spender) external view virtual override returns (bool) {  // @audit gasop - use external modifier
@@ -122 +122 @@ File: LBToken.sol
-    function setApprovalForAll(address _spender, bool _approved) public virtual override {
+    function setApprovalForAll(address _spender, bool _approved) external virtual override {  // @audit gasop - use external modifier
```