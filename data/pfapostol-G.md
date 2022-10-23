##### Gas Optimizations

Gas savings are estimated using the gas report of existing `forge test --gas-report` tests (the sum of all deployment costs and the sum of the costs of calling methods) and may vary depending on the implementation of the fix.

|       | Issue                                                                                     | Instances | Estimated gas(deployments) | Estimated gas(min method call) | Estimated gas(avg method call) | Estimated gas(max method call) |
| :---: | :---------------------------------------------------------------------------------------- | :-------: | :------------------------: | :----------------------------: | :----------------------------: | :----------------------------: |
| **1** | Use functions instead of modifiers                                                        |     8     |         1 065 143          |              -974              |             -1 557             |             -2 027             |
| **2** | Use a more recent version of solidity                                                     |    37     |          821 258           |             4 612              |             16 393             |             37 384             |
| **3** | Duplicated require()/revert() checks should be refactored to a modifier or function       |    38     |          566 224           |              -424              |             -1 473             |             -2 818             |
| **4** | Unchecking arithmetics operations that can't underflow/overflow                           |     2     |           58 524           |               0                |              651               |             1 104              |
| **5** | Use of the memory keyword when storage pointer should be used                             |     4     |           53 523           |               55               |             13 580             |             15 945             |
| **6** | Using calldata instead of memory for read-only arguments in external functions saves gas  |     1     |           39 086           |             2 256              |             1 199              |             -2 165             |
| **7** | Do not cache msg.sender                                                                   |     1     |           13 843           |               0                |              -140              |               35               |
| **8** | Unnecessary else statement                                                                |     4     |           7 217            |               0                |              -88               |              338               |
| **9** | `++i` costs less gas than `i++`, especially when it's used in for-loops (`--i`/`i--` too) |     5     |           2 800            |               0                |               32               |               69               |
|       | **Overall Gas Saving**                                                                    |  **100**  |       **2 623 283**        |           **5 381**            |           **28 755**           |           **48 090**           |


**Total:  100 instances over 9 issues**

---

#### 1. **Use functions instead of modifiers (8 instances)**

Deployment. Gas Saved: **1 065 143**

Minimum Method Call. Gas Saved: **-974**

Average Method Call. Gas Saved: **-1 557**

Maximum Method Call. Gas Saved: **-2 027**

Overall gas change: **-477 008 (-0.302%)**

##### - src/LBPair.sol:[43](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L2743), [110](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27110), [788](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27788), [792](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27792),

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..edb50ac 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -40,9 +40,8 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
   40,  40: 
   41,  41:     /** Modifiers **/
   42,  42: 
-  43     :-    modifier onlyFactory() {
+       43:+    function onlyFactory() private {
   44,  44:         if (msg.sender != address(factory)) revert LBPair__OnlyFactory();
-  45     :-        _;
   46,  45:     }
   47,  46: 
   48,  47:     /** Public immutable variables **/
@@ -107,7 +106,8 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  107, 106:         uint24 _activeId,
  108, 107:         uint16 _sampleLifetime,
  109, 108:         bytes32 _packedFeeParameters
- 110     :-    ) external override onlyFactory {
+      109:+    ) external override {
+      110:+        onlyFactory();
  111, 111:         if (address(_tokenX) == address(0) || address(_tokenY) == address(0)) revert LBPair__AddressZero();
  112, 112:         if (address(tokenX) != address(0)) revert LBPair__AlreadyInitialized();
  113, 113: 
@@ -785,11 +785,13 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  785, 785:     /// The bin step will not change
  786, 786:     /// Only callable by the factory
  787, 787:     /// @param _packedFeeParameters The packed fee parameters
- 788     :-    function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
+      788:+    function setFeesParameters(bytes32 _packedFeeParameters) external override {
+      789:+        onlyFactory();
  789, 790:         _setFeesParameters(_packedFeeParameters);
  790, 791:     }
  791, 792: 
- 792     :-    function forceDecay() external override onlyFactory {
+      793:+    function forceDecay() external override {
+      794:+        onlyFactory();
  793, 795:         unchecked {
  794, 796:             _feeParameters.volatilityReference = uint24(
  795, 797:                 (uint256(_feeParameters.reductionFactor) * _feeParameters.volatilityReference) /
```

##### - src/LBRouter.sol:[32](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L32), [626](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L626), [647](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L647)

```diff
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..2cf7514 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -29,9 +29,8 @@ contract LBRouter is ILBRouter {
   29,  29:     IJoeFactory public immutable override oldFactory;
   30,  30:     IWAVAX public immutable override wavax;
   31,  31: 
-  32     :-    modifier onlyFactoryOwner() {
+       32:+    function onlyFactoryOwner() private {
   33,  33:         if (msg.sender != factory.owner()) revert LBRouter__NotFactoryOwner();
-  34     :-        _;
   35,  34:     }
   36,  35: 
   37,  36:     modifier ensure(uint256 _deadline) {
@@ -623,7 +622,8 @@ contract LBRouter is ILBRouter {
  623, 622:         IERC20 _token,
  624, 623:         address _to,
  625, 624:         uint256 _amount
- 626     :-    ) external override onlyFactoryOwner {
+      625:+    ) external override {
+      626:+        onlyFactoryOwner();
  627, 627:         if (address(_token) == address(0)) {
  628, 628:             if (_amount == type(uint256).max) _amount = address(this).balance;
  629, 629:             _safeTransferAVAX(_to, _amount);
@@ -644,7 +644,8 @@ contract LBRouter is ILBRouter {
  644, 644:         address _to,
  645, 645:         uint256[] memory _ids,
  646, 646:         uint256[] memory _amounts
- 647     :-    ) external override onlyFactoryOwner {
+      647:+    ) external override {
+      648:+        onlyFactoryOwner();
  648, 649:         _lbToken.safeBatchTransferFrom(address(this), _to, _ids, _amounts);
  649, 650:     }
  650, 651: 
```

##### - src/LBRouter.sol:[36](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L36), [41](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L41)

```diff
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 2cf7514..200c7e2 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -33,15 +33,13 @@ contract LBRouter is ILBRouter {
   33,  33:         if (msg.sender != factory.owner()) revert LBRouter__NotFactoryOwner();
   34,  34:     }
   35,  35: 
-  36     :-    modifier ensure(uint256 _deadline) {
+       36:+    function ensure(uint256 _deadline) private {
   37,  37:         if (block.timestamp > _deadline) revert LBRouter__DeadlineExceeded(_deadline, block.timestamp);
-  38     :-        _;
   39,  38:     }
   40,  39: 
-  41     :-    modifier verifyInputs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath) {
+       40:+    function verifyInputs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath) private {
   42,  41:         if (_pairBinSteps.length == 0 || _pairBinSteps.length + 1 != _tokenPath.length)
   43,  42:             revert LBRouter__LengthsMismatch();
-  44     :-        _;
   45,  43:     }
   46,  44: 
   47,  45:     /// @notice Constructor
@@ -280,7 +278,8 @@ contract LBRouter is ILBRouter {
  280, 278:         uint256[] memory _amounts,
  281, 279:         address _to,
  282, 280:         uint256 _deadline
- 283     :-    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {
+      281:+    ) external override returns (uint256 amountX, uint256 amountY) {
+      282:+        ensure(_deadline);
  284, 283:         ILBPair _LBPair = _getLBPairInformation(_tokenX, _tokenY, _binStep);
  285, 284:         if (_tokenX != _LBPair.tokenX()) {
  286, 285:             (_tokenX, _tokenY) = (_tokenY, _tokenX);
@@ -313,7 +312,8 @@ contract LBRouter is ILBRouter {
  313, 312:         uint256[] memory _amounts,
  314, 313:         address payable _to,
  315, 314:         uint256 _deadline
- 316     :-    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {
+      315:+    ) external override returns (uint256 amountToken, uint256 amountAVAX) {
+      316:+        ensure(_deadline);
  317, 317:         ILBPair _LBPair = _getLBPairInformation(_token, IERC20(wavax), _binStep);
  318, 318: 
  319, 319:         bool _isAVAXTokenY = IERC20(wavax) == _LBPair.tokenY();
@@ -355,7 +355,9 @@ contract LBRouter is ILBRouter {
  355, 355:         IERC20[] memory _tokenPath,
  356, 356:         address _to,
  357, 357:         uint256 _deadline
- 358     :-    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
+      358:+    ) external override returns (uint256 amountOut) {
+      359:+        ensure(_deadline);
+      360:+        verifyInputs(_pairBinSteps, _tokenPath);
  359, 361:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  360, 362: 
  361, 363:         _tokenPath[0].safeTransferFrom(msg.sender, _pairs[0], _amountIn);
@@ -380,7 +382,9 @@ contract LBRouter is ILBRouter {
  380, 382:         IERC20[] memory _tokenPath,
  381, 383:         address payable _to,
  382, 384:         uint256 _deadline
- 383     :-    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
+      385:+    ) external override returns (uint256 amountOut) {
+      386:+        ensure(_deadline);
+      387:+        verifyInputs(_pairBinSteps, _tokenPath);
  384, 388:         if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
  385, 389:             revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
  386, 390: 
@@ -409,7 +413,9 @@ contract LBRouter is ILBRouter {
  409, 413:         IERC20[] memory _tokenPath,
  410, 414:         address _to,
  411, 415:         uint256 _deadline
- 412     :-    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
+      416:+    ) external payable override returns (uint256 amountOut) {
+      417:+        ensure(_deadline);
+      418:+        verifyInputs(_pairBinSteps, _tokenPath);
  413, 419:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  414, 420: 
  415, 421:         _wavaxDepositAndTransfer(_pairs[0], msg.value);
@@ -434,7 +440,9 @@ contract LBRouter is ILBRouter {
  434, 440:         IERC20[] memory _tokenPath,
  435, 441:         address _to,
  436, 442:         uint256 _deadline
- 437     :-    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {
+      443:+    ) external override returns (uint256[] memory amountsIn) {
+      444:+        ensure(_deadline);
+      445:+        verifyInputs(_pairBinSteps, _tokenPath);
  438, 446:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  439, 447:         amountsIn = _getAmountsIn(_pairBinSteps, _pairs, _tokenPath, _amountOut);
  440, 448: 
@@ -462,7 +470,9 @@ contract LBRouter is ILBRouter {
  462, 470:         IERC20[] memory _tokenPath,
  463, 471:         address payable _to,
  464, 472:         uint256 _deadline
- 465     :-    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {
+      473:+    ) external override returns (uint256[] memory amountsIn) {
+      474:+        ensure(_deadline);
+      475:+        verifyInputs(_pairBinSteps, _tokenPath);
  466, 476:         if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
  467, 477:             revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
  468, 478: 
@@ -499,10 +509,10 @@ contract LBRouter is ILBRouter {
  499, 509:         external
  500, 510:         payable
  501, 511:         override
- 502     :-        ensure(_deadline)
- 503     :-        verifyInputs(_pairBinSteps, _tokenPath)
  504, 512:         returns (uint256[] memory amountsIn)
  505, 513:     {
+      514:+        ensure(_deadline);
+      515:+        verifyInputs(_pairBinSteps, _tokenPath);
  506, 516:         if (_tokenPath[0] != IERC20(wavax)) revert LBRouter__InvalidTokenPath(address(_tokenPath[0]));
  507, 517: 
  508, 518:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
@@ -534,7 +544,9 @@ contract LBRouter is ILBRouter {
  534, 544:         IERC20[] memory _tokenPath,
  535, 545:         address _to,
  536, 546:         uint256 _deadline
- 537     :-    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
+      547:+    ) external override returns (uint256 amountOut) {
+      548:+        ensure(_deadline);
+      549:+        verifyInputs(_pairBinSteps, _tokenPath);
  538, 550:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  539, 551: 
  540, 552:         IERC20 _targetToken = _tokenPath[_pairs.length];
@@ -564,7 +576,9 @@ contract LBRouter is ILBRouter {
  564, 576:         IERC20[] memory _tokenPath,
  565, 577:         address payable _to,
  566, 578:         uint256 _deadline
- 567     :-    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
+      579:+    ) external override returns (uint256 amountOut) {
+      580:+        ensure(_deadline);
+      581:+        verifyInputs(_pairBinSteps, _tokenPath);
  568, 582:         if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
  569, 583:             revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
  570, 584: 
@@ -596,7 +610,9 @@ contract LBRouter is ILBRouter {
  596, 610:         IERC20[] memory _tokenPath,
  597, 611:         address _to,
  598, 612:         uint256 _deadline
- 599     :-    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
+      613:+    ) external payable override returns (uint256 amountOut) {
+      614:+        ensure(_deadline);
+      615:+        verifyInputs(_pairBinSteps, _tokenPath);
  600, 616:         if (_tokenPath[0] != IERC20(wavax)) revert LBRouter__InvalidTokenPath(address(_tokenPath[0]));
  601, 617: 
  602, 618:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
@@ -656,9 +672,10 @@ contract LBRouter is ILBRouter {
  656, 672:     /// @return liquidityMinted Amounts of LBToken minted for each bin
  657, 673:     function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair)
  658, 674:         private
- 659     :-        ensure(_liq.deadline)
+      675:+        
  660, 676:         returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
  661, 677:     {
+      678:+        ensure(_liq.deadline);
  662, 679:         unchecked {
  663, 680:             if (_liq.deltaIds.length != _liq.distributionX.length && _liq.deltaIds.length != _liq.distributionY.length)
  664, 681:                 revert LBRouter__LengthsMismatch();
```

##### - src/LBToken.sol:[32](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L32), [37](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L37), [42](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L42)

```diff
diff --git a/src/LBToken.sol b/src/LBToken.sol
index 47aa528..8a184be 100644
--- a/src/LBToken.sol
+++ b/src/LBToken.sol
@@ -29,19 +29,16 @@ contract LBToken is ILBToken {
   29,  29:     string private constant _name = "Liquidity Book Token";
   30,  30:     string private constant _symbol = "LBT";
   31,  31: 
-  32     :-    modifier checkApproval(address _from, address _spender) {
+       32:+    function checkApproval(address _from, address _spender) private view {
   33,  33:         if (!_isApprovedForAll(_from, _spender)) revert LBToken__SpenderNotApproved(_from, _spender);
-  34     :-        _;
   35,  34:     }
   36,  35: 
-  37     :-    modifier checkAddresses(address _from, address _to) {
+       36:+    function checkAddresses(address _from, address _to) private pure {
   38,  37:         if (_from == address(0) || _to == address(0)) revert LBToken__TransferFromOrToAddress0();
-  39     :-        _;
   40,  38:     }
   41,  39: 
-  42     :-    modifier checkLength(uint256 _lengthA, uint256 _lengthB) {
+       40:+    function checkLength(uint256 _lengthA, uint256 _lengthB) private pure {
   43,  41:         if (_lengthA != _lengthB) revert LBToken__LengthMismatch(_lengthA, _lengthB);
-  44     :-        _;
   45,  42:     }
   46,  43: 
   47,  44:     /// @notice Returns the name of the token
@@ -81,9 +78,9 @@ contract LBToken is ILBToken {
   81,  78:         view
   82,  79:         virtual
   83,  80:         override
-  84     :-        checkLength(_accounts.length, _ids.length)
   85,  81:         returns (uint256[] memory batchBalances)
   86,  82:     {
+       83:+        checkLength(_accounts.length, _ids.length);
   87,  84:         batchBalances = new uint256[](_accounts.length);
   88,  85: 
   89,  86:         unchecked {
@@ -133,7 +130,9 @@ contract LBToken is ILBToken {
  133, 130:         address _to,
  134, 131:         uint256 _id,
  135, 132:         uint256 _amount
- 136     :-    ) public virtual override checkAddresses(_from, _to) checkApproval(_from, msg.sender) {
+      133:+    ) public virtual override {
+      134:+        checkAddresses(_from, _to);
+      135:+        checkApproval(_from, msg.sender);
  137, 136:         address _spender = msg.sender;
  138, 137: 
  139, 138:         _transfer(_from, _to, _id, _amount);
@@ -154,11 +153,11 @@ contract LBToken is ILBToken {
  154, 153:     )
  155, 154:         public
  156, 155:         virtual
- 157     :-        override
- 158     :-        checkLength(_ids.length, _amounts.length)
- 159     :-        checkAddresses(_from, _to)
- 160     :-        checkApproval(_from, msg.sender)
+      156:+        override   
  161, 157:     {
+      158:+        checkLength(_ids.length, _amounts.length);
+      159:+        checkAddresses(_from, _to);
+      160:+        checkApproval(_from, msg.sender);
  162, 161:         unchecked {
  163, 162:             for (uint256 i; i < _ids.length; ++i) {
  164, 163:                 _transfer(_from, _to, _ids[i], _amounts[i]);
```

##### - src/LBFactory.sol:[215](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L215), [317](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L317), [350](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L350), [396](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L396), [434](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L434), [468](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L468), [474](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L474), [485](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L485), [493](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L493), [502](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L502), [520](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L520)

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..6b6a58f 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -212,7 +212,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  212, 212:     /// @notice Set the LBPair implementation address
  213, 213:     /// @dev Needs to be called by the owner
  214, 214:     /// @param _LBPairImplementation The address of the implementation
- 215     :-    function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
+      215:+    function setLBPairImplementation(address _LBPairImplementation) external override {
+      216:+        onlyOwner();
  216, 217:         if (ILBPair(_LBPairImplementation).factory() != this)
  217, 218:             revert LBFactory__LBPairSafetyCheckFailed(_LBPairImplementation);
  218, 219: 
@@ -314,7 +315,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  314, 315:         IERC20 _tokenY,
  315, 316:         uint256 _binStep,
  316, 317:         bool _ignored
- 317     :-    ) external override onlyOwner {
+      318:+    ) external override {
+      319:+        onlyOwner();
  318, 320:         (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
  319, 321: 
  320, 322:         LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
@@ -347,7 +349,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  347, 349:         uint16 _protocolShare,
  348, 350:         uint24 _maxVolatilityAccumulated,
  349, 351:         uint16 _sampleLifetime
- 350     :-    ) external override onlyOwner {
+      352:+    ) external override {
+      353:+        onlyOwner();
  351, 354:         bytes32 _packedFeeParameters = _getPackedFeeParameters(
  352, 355:             _binStep,
  353, 356:             _baseFactor,
@@ -393,7 +396,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  393, 396: 
  394, 397:     /// @notice Remove the preset linked to a binStep
  395, 398:     /// @param _binStep The bin step to remove
- 396     :-    function removePreset(uint16 _binStep) external override onlyOwner {
+      399:+    function removePreset(uint16 _binStep) external override {
+      400:+        onlyOwner();
  397, 401:         if (_presets[_binStep] == bytes32(0)) revert LBFactory__BinStepHasNoPreset(_binStep);
  398, 402: 
  399, 403:         // Set the bit `_binStep` to 0
@@ -431,7 +435,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  431, 435:         uint24 _variableFeeControl,
  432, 436:         uint16 _protocolShare,
  433, 437:         uint24 _maxVolatilityAccumulated
- 434     :-    ) external override onlyOwner {
+      438:+    ) external override {
+      439:+        onlyOwner();
  435, 440:         ILBPair _LBPair = _getLBPairInformation(_tokenX, _tokenY, _binStep).LBPair;
  436, 441: 
  437, 442:         if (address(_LBPair) == address(0)) revert LBFactory__LBPairNotCreated(_tokenX, _tokenY, _binStep);
@@ -465,13 +470,15 @@ contract LBFactory is PendingOwnable, ILBFactory {
  465, 470: 
  466, 471:     /// @notice Function to set the recipient of the fees. This address needs to be able to receive ERC20s
  467, 472:     /// @param _feeRecipient The address of the recipient
- 468     :-    function setFeeRecipient(address _feeRecipient) external override onlyOwner {
+      473:+    function setFeeRecipient(address _feeRecipient) external override {
+      474:+        onlyOwner();
  469, 475:         _setFeeRecipient(_feeRecipient);
  470, 476:     }
  471, 477: 
  472, 478:     /// @notice Function to set the flash loan fee
  473, 479:     /// @param _flashLoanFee The value of the fee for flash loan
- 474     :-    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
+      480:+    function setFlashLoanFee(uint256 _flashLoanFee) external override {
+      481:+        onlyOwner();
  475, 482:         uint256 _oldFlashLoanFee = flashLoanFee;
  476, 483: 
  477, 484:         if (_oldFlashLoanFee == _flashLoanFee) revert LBFactory__SameFlashLoanFee(_flashLoanFee);
@@ -482,7 +489,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  482, 489: 
  483, 490:     /// @notice Function to set the creation restriction of the Factory
  484, 491:     /// @param _locked If the creation is restricted (true) or not (false)
- 485     :-    function setFactoryLockedState(bool _locked) external override onlyOwner {
+      492:+    function setFactoryLockedState(bool _locked) external override {
+      493:+        onlyOwner();
  486, 494:         if (creationUnlocked != _locked) revert LBFactory__FactoryLockIsAlreadyInTheSameState();
  487, 495:         creationUnlocked = !_locked;
  488, 496:         emit FactoryLockedStatusUpdated(_locked);
@@ -490,7 +498,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  490, 498: 
  491, 499:     /// @notice Function to add an asset to the whitelist of quote assets
  492, 500:     /// @param _quoteAsset The quote asset (e.g: AVAX, USDC...)
- 493     :-    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
+      501:+    function addQuoteAsset(IERC20 _quoteAsset) external override {
+      502:+        onlyOwner();
  494, 503:         if (!_quoteAssetWhitelist.add(address(_quoteAsset)))
  495, 504:             revert LBFactory__QuoteAssetAlreadyWhitelisted(_quoteAsset);
  496, 505: 
@@ -499,7 +508,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  499, 508: 
  500, 509:     /// @notice Function to remove an asset to the whitelist of quote assets
  501, 510:     /// @param _quoteAsset The quote asset (e.g: AVAX, USDC...)
- 502     :-    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
+      511:+    function removeQuoteAsset(IERC20 _quoteAsset) external override {
+      512:+        onlyOwner();
  503, 513:         if (!_quoteAssetWhitelist.remove(address(_quoteAsset))) revert LBFactory__QuoteAssetNotWhitelisted(_quoteAsset);
  504, 514: 
  505, 515:         emit QuoteAssetRemoved(_quoteAsset);
@@ -517,7 +527,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  517, 527:         emit FeeRecipientSet(_oldFeeRecipient, _feeRecipient);
  518, 528:     }
  519, 529: 
- 520     :-    function forceDecay(ILBPair _LBPair) external override onlyOwner {
+      530:+    function forceDecay(ILBPair _LBPair) external override {
+      531:+        onlyOwner();
  521, 532:         _LBPair.forceDecay();
  522, 533:     }
  523, 534: 
```

##### - src/libraries/PendingOwnable.sol:[29](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L29)

```diff
diff --git a/src/libraries/PendingOwnable.sol b/src/libraries/PendingOwnable.sol
index f745362..bb29ec8 100644
--- a/src/libraries/PendingOwnable.sol
+++ b/src/libraries/PendingOwnable.sol
@@ -26,9 +26,8 @@ contract PendingOwnable is IPendingOwnable {
   26,  26:     address private _pendingOwner;
   27,  27: 
   28,  28:     /// @notice Throws if called by any account other than the owner.
-  29     :-    modifier onlyOwner() {
+       29:+    function onlyOwner() internal view {
   30,  30:         if (msg.sender != _owner) revert PendingOwnable__NotOwner();
-  31     :-        _;
   32,  31:     }
   33,  32: 
   34,  33:     /// @notice Throws if called by any account other than the pending owner.
@@ -56,7 +55,8 @@ contract PendingOwnable is IPendingOwnable {
   56,  55: 
   57,  56:     /// @notice Sets the pending owner address. This address will be able to become
   58,  57:     /// the owner of this contract by calling {becomeOwner}
-  59     :-    function setPendingOwner(address pendingOwner_) public override onlyOwner {
+       58:+    function setPendingOwner(address pendingOwner_) public override {
+       59:+        onlyOwner();
   60,  60:         if (pendingOwner_ == address(0)) revert PendingOwnable__AddressZero();
   61,  61:         if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();
   62,  62:         _setPendingOwner(pendingOwner_);
@@ -65,7 +65,8 @@ contract PendingOwnable is IPendingOwnable {
   65,  65:     /// @notice Revoke the pending owner address. This address will not be able to
   66,  66:     /// call {becomeOwner} to become the owner anymore.
   67,  67:     /// Can only be called by the owner
-  68     :-    function revokePendingOwner() public override onlyOwner {
+       68:+    function revokePendingOwner() public override {
+       69:+        onlyOwner();
   69,  70:         if (_pendingOwner == address(0)) revert PendingOwnable__NoPendingOwner();
   70,  71:         _setPendingOwner(address(0));
   71,  72:     }
@@ -81,7 +82,8 @@ contract PendingOwnable is IPendingOwnable {
   81,  82:     ///
   82,  83:     /// NOTE: Renouncing ownership will leave the contract without an owner,
   83,  84:     /// thereby removing any functionality that is only available to the owner.
-  84     :-    function renounceOwnership() public override onlyOwner {
+       85:+    function renounceOwnership() public override {
+       86:+        onlyOwner();
   85,  87:         _transferOwnership(address(0));
   86,  88:     }
   87,  89: 
diff --git a/test/mocks/Faucet.sol b/test/mocks/Faucet.sol
index 108bde7..da1835f 100644
--- a/test/mocks/Faucet.sol
+++ b/test/mocks/Faucet.sol
@@ -93,14 +93,16 @@ contract Faucet is PendingOwnable {
   93,  93:     /// @dev Tokens need to be owned by the faucet, and only mintable by the owner
   94,  94:     /// @param _token The address of the token
   95,  95:     /// @param _amountPerRequest The amount per request
-  96     :-    function addFaucetToken(IERC20 _token, uint96 _amountPerRequest) external onlyOwner {
+       96:+    function addFaucetToken(IERC20 _token, uint96 _amountPerRequest) external {
+       97:+        onlyOwner();
   97,  98:         _addFaucetToken(FaucetToken({ERC20: _token, amountPerRequest: _amountPerRequest}));
   98,  99:     }
   99, 100: 
  100, 101:     /// @notice Remove a token from the faucet
  101, 102:     /// @dev Token needs to be in the set, and AVAX can't be removed
  102, 103:     /// @param _token The address of the token
- 103     :-    function removeFaucetToken(IERC20 _token) external onlyOwner {
+      104:+    function removeFaucetToken(IERC20 _token) external {
+      105:+        onlyOwner();
  104, 106:         uint256 index = tokenToIndices[_token];
  105, 107: 
  106, 108:         require(index >= 2, "Not a faucet token");
@@ -115,7 +117,8 @@ contract Faucet is PendingOwnable {
  115, 117:     /// @notice Set the request cooldown for every users
  116, 118:     /// @dev This function needs to be called by the owner
  117, 119:     /// @param _requestCooldown The new cooldown
- 118     :-    function setRequestCooldown(uint256 _requestCooldown) external onlyOwner {
+      120:+    function setRequestCooldown(uint256 _requestCooldown) external {
+      121:+        onlyOwner();
  119, 122:         _setRequestCooldown(_requestCooldown);
  120, 123:     }
  121, 124: 
@@ -123,7 +126,8 @@ contract Faucet is PendingOwnable {
  123, 126:     /// @dev This function needs to be called by the owner
  124, 127:     /// @param _token The address of the token
  125, 128:     /// @param _amountPerRequest The new amount per request
- 126     :-    function setAmountPerRequest(IERC20 _token, uint96 _amountPerRequest) external onlyOwner {
+      129:+    function setAmountPerRequest(IERC20 _token, uint96 _amountPerRequest) external {
+      130:+        onlyOwner();
  127, 131:         _setAmountPerRequest(_token, _amountPerRequest);
  128, 132:     }
  129, 133: 
@@ -136,20 +140,23 @@ contract Faucet is PendingOwnable {
  136, 140:         IERC20 _token,
  137, 141:         address _to,
  138, 142:         uint256 _amount
- 139     :-    ) external onlyOwner {
+      143:+    ) external {
+      144:+        onlyOwner();
  140, 145:         if (address(_token) == address(0)) _sendAvax(_to, _amount);
  141, 146:         else _token.safeTransfer(_to, _amount);
  142, 147:     }
  143, 148: 
  144, 149:     /// @notice Set the address of the operator
  145, 150:     /// @param _newOperator The address of the new operator
- 146     :-    function setOperator(address _newOperator) external onlyOwner {
+      151:+    function setOperator(address _newOperator) external {
+      152:+        onlyOwner();
  147, 153:         operator = _newOperator;
  148, 154:     }
  149, 155: 
  150, 156:     /// @notice Set whether the direct request is unlocked or not
  151, 157:     /// @param _unlockedRequest The address of the new operator
- 152     :-    function setUnlockedRequest(bool _unlockedRequest) external onlyOwner {
+      158:+    function setUnlockedRequest(bool _unlockedRequest) external {
+      159:+        onlyOwner();
  153, 160:         unlockedRequest = _unlockedRequest;
  154, 161:     }
  155, 162: 
```

---

#### 2. **Use a more recent version of solidity (37 instances)**

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

**NOTE**: compared to 0.8.7 which the project uses by default

Deployment. Gas Saved: **821 258**

Minimum Method Call. Gas Saved: **4 612**

Average Method Call. Gas Saved: **16 393**

Maximum Method Call. Gas Saved: **37 384**

Overall gas change: **-581 263 (-81.312%)**

2022-10-traderjoe/src/interfaces/IJoeFactory.sol
2022-10-traderjoe/src/interfaces/IJoePair.sol
2022-10-traderjoe/src/interfaces/IJoeRouter01.sol
2022-10-traderjoe/src/interfaces/IJoeRouter02.sol
2022-10-traderjoe/src/interfaces/ILBFactory.sol
2022-10-traderjoe/src/interfaces/ILBFlashLoanCallback.sol
2022-10-traderjoe/src/interfaces/ILBPair.sol
2022-10-traderjoe/src/interfaces/ILBRouter.sol
2022-10-traderjoe/src/interfaces/ILBToken.sol
2022-10-traderjoe/src/interfaces/IPendingOwnable.sol
2022-10-traderjoe/src/interfaces/IWAVAX.sol
2022-10-traderjoe/src/libraries/BinHelper.sol
2022-10-traderjoe/src/libraries/BitMath.sol
2022-10-traderjoe/src/libraries/Buffer.sol
2022-10-traderjoe/src/libraries/Constants.sol
2022-10-traderjoe/src/libraries/Decoder.sol
2022-10-traderjoe/src/libraries/Encoder.sol
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol
2022-10-traderjoe/src/libraries/FeeHelper.sol
2022-10-traderjoe/src/libraries/JoeLibrary.sol
2022-10-traderjoe/src/libraries/Math128x128.sol
2022-10-traderjoe/src/libraries/Math512Bits.sol
2022-10-traderjoe/src/libraries/Oracle.sol
2022-10-traderjoe/src/libraries/PendingOwnable.sol
2022-10-traderjoe/src/libraries/ReentrancyGuardUpgradeable.sol
2022-10-traderjoe/src/libraries/SafeCast.sol
2022-10-traderjoe/src/libraries/SafeMath.sol
2022-10-traderjoe/src/libraries/Samples.sol
2022-10-traderjoe/src/libraries/SwapHelper.sol
2022-10-traderjoe/src/libraries/TokenHelper.sol
2022-10-traderjoe/src/libraries/TreeMath.sol
2022-10-traderjoe/src/LBErrors.sol
2022-10-traderjoe/src/LBFactory.sol
2022-10-traderjoe/src/LBPair.sol
2022-10-traderjoe/src/LBQuoter.sol
2022-10-traderjoe/src/LBRouter.sol
2022-10-traderjoe/src/LBToken.sol

```solidity
  3,1: pragma solidity ^0.8.0;
```

---

#### 3. **Duplicated require()/revert() checks should be refactored to a modifier or function (38 instances)**

Deployment. Gas Saved: **566 224**

Minimum Method Call. Gas Saved: **-424**

Average Method Call. Gas Saved: **-1 473**

Maximum Method Call. Gas Saved: **-2 818**

Overall gas change: **-174 947 (-0.950%)**

##### - src/LBErrors.sol:[link](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol)

```diff
diff --git a/src/LBErrors.sol b/src/LBErrors.sol
index 092ede5..d8e5ddd 100644
--- a/src/LBErrors.sol
+++ b/src/LBErrors.sol
@@ -4,6 +4,12 @@ pragma solidity ^0.8.0;
    4,   4: 
    5,   5: import "./interfaces/ILBPair.sol";
    6,   6: 
+        7:+/** Common errors */
+        8:+
+        9:+error IdOverflows(int256 id);
+       10:+error AddressZero();
+       11:+error InsufficientLiquidity();
+       12:+
    7,  13: /** LBRouter errors */
    8,  14: 
    9,  15: error LBRouter__SenderIsNotWAVAX();
@@ -14,7 +20,6 @@ error LBRouter__BrokenSwapSafetyCheck();
   14,  20: error LBRouter__NotFactoryOwner();
   15,  21: error LBRouter__TooMuchTokensIn(uint256 excess);
   16,  22: error LBRouter__BinReserveOverflows(uint256 id);
-  17     :-error LBRouter__IdOverflows(int256 id);
   18,  23: error LBRouter__LengthsMismatch();
   19,  24: error LBRouter__WrongTokenOrder();
   20,  25: error LBRouter__IdSlippageCaught(uint256 activeIdDesired, uint256 idSlippage, uint256 activeId);
@@ -51,7 +56,6 @@ error LBToken__TransferExceedsBalance(address from, uint256 id, uint256 amount);
   51,  56: error LBFactory__IdenticalAddresses(IERC20 token);
   52,  57: error LBFactory__QuoteAssetNotWhitelisted(IERC20 quoteAsset);
   53,  58: error LBFactory__QuoteAssetAlreadyWhitelisted(IERC20 quoteAsset);
-  54     :-error LBFactory__AddressZero();
   55,  59: error LBFactory__LBPairAlreadyExists(IERC20 tokenX, IERC20 tokenY, uint256 _binStep);
   56,  60: error LBFactory__LBPairNotCreated(IERC20 tokenX, IERC20 tokenY, uint256 binStep);
   57,  61: error LBFactory__DecreasingPeriods(uint16 filterPeriod, uint16 decayPeriod);
@@ -75,7 +79,6 @@ error LBFactory__ImplementationNotSet();
   75,  79: /** LBPair errors */
   76,  80: 
   77,  81: error LBPair__InsufficientAmounts();
-  78     :-error LBPair__AddressZero();
   79,  82: error LBPair__BrokenSwapSafetyCheck();
   80,  83: error LBPair__CompositionFactorFlawed(uint256 id);
   81,  84: error LBPair__InsufficientLiquidityMinted(uint256 id);
@@ -93,7 +96,6 @@ error LBPair__NewSizeTooSmall(uint256 newSize, uint256 oracleSize);
   93,  96: /** BinHelper errors */
   94,  97: 
   95,  98: error BinHelper__BinStepOverflows(uint256 bp);
-  96     :-error BinHelper__IdOverflows(int256 id);
   97,  99: error BinHelper__IntOverflows(uint256 id);
   98, 100: 
   99, 101: /** FeeDistributionHelper errors */
@@ -124,7 +126,6 @@ error PendingOwnable__NotOwner();
  124, 126: error PendingOwnable__NotPendingOwner();
  125, 127: error PendingOwnable__PendingOwnerAlreadySet();
  126, 128: error PendingOwnable__NoPendingOwner();
- 127     :-error PendingOwnable__AddressZero();
  128, 129: 
  129, 130: /** ReentrancyGuardUpgradeable errors */
  130, 131: 
@@ -173,9 +174,7 @@ error TreeMath__ErrorDepthSearch();
  173, 174: /** JoeLibrary errors */
  174, 175: 
  175, 176: error JoeLibrary__IdenticalAddresses();
- 176     :-error JoeLibrary__AddressZero();
  177, 177: error JoeLibrary__InsufficientAmount();
- 178     :-error JoeLibrary__InsufficientLiquidity();
  179, 178: 
  180, 179: /** TokenHelper errors */
  181, 180: 
@@ -184,3 +183,49 @@ error TokenHelper__TransferFailed(IERC20 token, address recipient, uint256 amoun
  184, 183: /** LBQuoter errors */
  185, 184: 
  186, 185: error LBQuoter_InvalidLength();
+      186:+
+      187:+library Validation {
+      188:+    function valid_id(int256 _id) pure internal {
+      189:+        if (_id < 0 || uint256(_id) > type(uint24).max) revert IdOverflows(_id);
+      190:+    }
+      191:+
+      192:+    function is_address_zero(address a) pure internal {
+      193:+        if (a == address(0)) revert AddressZero();
+      194:+    }
+      195:+
+      196:+    function valid_liquidity(uint256 a, uint256 b) pure internal {
+      197:+        if (a == 0 || b == 0) revert InsufficientLiquidity();
+      198:+    }
+      199:+
+      200:+    function bin_step_has_no_preset(bytes32 preset, uint16 binStep) pure internal {
+      201:+        if (preset == bytes32(0)) revert LBFactory__BinStepHasNoPreset(binStep);
+      202:+    }
+      203:+
+      204:+    function is_valid_length(uint256 length) pure internal {
+      205:+        if (length < 2) {
+      206:+            revert LBQuoter_InvalidLength();
+      207:+        }
+      208:+    }
+      209:+
+      210:+    function is_valid_tokens(IERC20 a, IERC20 b) pure internal {
+      211:+        if (a != b) revert LBRouter__WrongTokenOrder();
+      212:+    }
+      213:+
+      214:+    function amount_out_is_sufficient(uint256 min, uint256 out) pure internal {
+      215:+        if (min > out) revert LBRouter__InsufficientAmountOut(min, out);
+      216:+    }
+      217:+
+      218:+    function is_valid_token_path(IERC20 pl, IERC20 pr) pure internal {
+      219:+        if (pl != pr)
+      220:+            revert LBRouter__InvalidTokenPath(address(pl));
+      221:+    }
+      222:+
+      223:+    function is_max_amount_exceeded(uint256 _in, uint256 max) pure internal {
+      224:+        if (_in > max) revert LBRouter__MaxAmountInExceeded(max, _in);
+      225:+    }
+      226:+
+      227:+    function caugth_amount_slippage(uint256 xAdded, uint256 minXAdded, uint256 yAdded, uint256 minYAdded) pure internal {
+      228:+        if (xAdded < minXAdded || yAdded < minYAdded)
+      229:+                revert LBRouter__AmountSlippageCaught(minXAdded, xAdded, minYAdded, yAdded);
+      230:+    }
+      231:+}
\ No newline at end of file
```

##### - src/LBFactory.sol:[136](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L136), [254](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L254), [259](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L259), [321](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L321), [397](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L397), [511](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L511)

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..b00b22a 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -133,7 +133,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
  133, 133:         )
  134, 134:     {
  135, 135:         bytes32 _preset = _presets[_binStep];
- 136     :-        if (_preset == bytes32(0)) revert LBFactory__BinStepHasNoPreset(_binStep);
+      136:+        Validation.bin_step_has_no_preset(_preset, _binStep);
  137, 137: 
  138, 138:         uint256 _shift;
  139, 139: 
@@ -251,12 +251,12 @@ contract LBFactory is PendingOwnable, ILBFactory {
  251, 251:         // We sort token for storage efficiency, only one input needs to be stored
  252, 252:         (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
  253, 253:         // single check is sufficient
- 254     :-        if (address(_tokenA) == address(0)) revert LBFactory__AddressZero();
+      254:+        Validation.is_address_zero(address(_tokenA));
  255, 255:         if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))
  256, 256:             revert LBFactory__LBPairAlreadyExists(_tokenX, _tokenY, _binStep);
  257, 257: 
  258, 258:         bytes32 _preset = _presets[_binStep];
- 259     :-        if (_preset == bytes32(0)) revert LBFactory__BinStepHasNoPreset(_binStep);
+      259:+        Validation.bin_step_has_no_preset(_preset, _binStep);
  260, 260: 
  261, 261:         uint256 _sampleLifetime = _preset.decode(type(uint16).max, 240);
  262, 262:         // We remove the bits that are not part of the feeParameters
@@ -318,7 +318,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
  318, 318:         (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
  319, 319: 
  320, 320:         LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
- 321     :-        if (address(_LBPairInformation.LBPair) == address(0)) revert LBFactory__AddressZero();
+      321:+        Validation.is_address_zero(address(_LBPairInformation.LBPair));
  322, 322: 
  323, 323:         if (_LBPairInformation.ignoredForRouting == _ignored) revert LBFactory__LBPairIgnoredIsAlreadyInTheSameState();
  324, 324: 
@@ -394,7 +394,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
  394, 394:     /// @notice Remove the preset linked to a binStep
  395, 395:     /// @param _binStep The bin step to remove
  396, 396:     function removePreset(uint16 _binStep) external override onlyOwner {
- 397     :-        if (_presets[_binStep] == bytes32(0)) revert LBFactory__BinStepHasNoPreset(_binStep);
+      397:+        Validation.bin_step_has_no_preset(_presets[_binStep], _binStep);
  398, 398: 
  399, 399:         // Set the bit `_binStep` to 0
  400, 400:         bytes32 _avPresets = _availablePresets;
@@ -508,7 +508,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
  508, 508:     /// @notice Internal function to set the recipient of the fee
  509, 509:     /// @param _feeRecipient The address of the recipient
  510, 510:     function _setFeeRecipient(address _feeRecipient) internal {
- 511     :-        if (_feeRecipient == address(0)) revert LBFactory__AddressZero();
+      511:+        Validation.is_address_zero(_feeRecipient);
  512, 512: 
  513, 513:         address _oldFeeRecipient = feeRecipient;
  514, 514:         if (_oldFeeRecipient == _feeRecipient) revert LBFactory__SameFeeRecipient(_feeRecipient);
```

##### - src/LBPair.sol:[91](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L2791), [111](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27111)

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..cad9f06 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -88,7 +88,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
   88,  88:     /// @notice Set the factory address
   89,  89:     /// @param _factory The address of the factory
   90,  90:     constructor(ILBFactory _factory) LBToken() {
-  91     :-        if (address(_factory) == address(0)) revert LBPair__AddressZero();
+       91:+        Validation.is_address_zero(address(_factory));
   92,  92:         factory = _factory;
   93,  93:     }
   94,  94: 
@@ -108,7 +108,8 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  108, 108:         uint16 _sampleLifetime,
  109, 109:         bytes32 _packedFeeParameters
  110, 110:     ) external override onlyFactory {
- 111     :-        if (address(_tokenX) == address(0) || address(_tokenY) == address(0)) revert LBPair__AddressZero();
+      111:+        Validation.is_address_zero(address(_tokenX));
+      112:+        Validation.is_address_zero(address(_tokenY));
  112, 113:         if (address(tokenX) != address(0)) revert LBPair__AlreadyInitialized();
  113, 114: 
  114, 115:         __ReentrancyGuard_init();
```

##### - src/LBQuoter.sol:[59-61](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L59-L61), [139-141](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L139-L141)

```diff
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..342ef5c 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -56,9 +56,7 @@ contract LBQuoter {
   56,  56:         view
   57,  57:         returns (Quote memory quote)
   58,  58:     {
-  59     :-        if (_route.length < 2) {
-  60     :-            revert LBQuoter_InvalidLength();
-  61     :-        }
+       59:+        Validation.is_valid_length(_route.length);
   62,  60: 
   63,  61:         quote.route = _route;
   64,  62: 
@@ -136,9 +134,7 @@ contract LBQuoter {
  136, 134:         view
  137, 135:         returns (Quote memory quote)
  138, 136:     {
- 139     :-        if (_route.length < 2) {
- 140     :-            revert LBQuoter_InvalidLength();
- 141     :-        }
+      137:+        Validation.is_valid_length(_route.length);
  142, 138:         quote.route = _route;
  143, 139: 
  144, 140:         uint256 swapLength = _route.length - 1;
```

##### - src/LBRouter.sol:[217](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L217), [241](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L241), [366](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L366), [385-386](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L385-L386), [394](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L394), [420](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L420), [442](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L442), [448](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L448), [467-468](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L467-L468), [473](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L473), [479](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L479), [507](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L507), [512](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L512), [518](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L518), [550](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L550), [569-570](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L569-L570), [581](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L581), [601](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L601), [614](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L614), [676](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L676), [690-691](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L690-L691), [751-752](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L751-L752) 

```diff
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..64c34d0 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -214,7 +214,7 @@ contract LBRouter is ILBRouter {
  214, 214:             _liquidityParameters.tokenY,
  215, 215:             _liquidityParameters.binStep
  216, 216:         );
- 217     :-        if (_liquidityParameters.tokenX != _LBPair.tokenX()) revert LBRouter__WrongTokenOrder();
+      217:+        Validation.is_valid_tokens(_liquidityParameters.tokenX, _LBPair.tokenX());
  218, 218: 
  219, 219:         _liquidityParameters.tokenX.safeTransferFrom(msg.sender, address(_LBPair), _liquidityParameters.amountX);
  220, 220:         _liquidityParameters.tokenY.safeTransferFrom(msg.sender, address(_LBPair), _liquidityParameters.amountY);
@@ -238,7 +238,7 @@ contract LBRouter is ILBRouter {
  238, 238:             _liquidityParameters.tokenY,
  239, 239:             _liquidityParameters.binStep
  240, 240:         );
- 241     :-        if (_liquidityParameters.tokenX != _LBPair.tokenX()) revert LBRouter__WrongTokenOrder();
+      241:+        Validation.is_valid_tokens(_liquidityParameters.tokenX, _LBPair.tokenX());
  242, 242: 
  243, 243:         if (_liquidityParameters.tokenX == wavax && _liquidityParameters.amountX == msg.value) {
  244, 244:             _wavaxDepositAndTransfer(address(_LBPair), msg.value);
@@ -363,7 +363,7 @@ contract LBRouter is ILBRouter {
  363, 363: 
  364, 364:         amountOut = _swapExactTokensForTokens(_amountIn, _pairs, _pairBinSteps, _tokenPath, _to);
  365, 365: 
- 366     :-        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
+      366:+        Validation.amount_out_is_sufficient(_amountOutMin, amountOut);
  367, 367:     }
  368, 368: 
  369, 369:     /// @notice Swaps exact tokens for AVAX while performing safety checks
@@ -382,8 +382,7 @@ contract LBRouter is ILBRouter {
  382, 382:         address payable _to,
  383, 383:         uint256 _deadline
  384, 384:     ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
- 385     :-        if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
- 386     :-            revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
+      385:+        Validation.is_valid_token_path(_tokenPath[_pairBinSteps.length], IERC20(wavax));
  387, 386: 
  388, 387:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  389, 388: 
@@ -391,7 +390,7 @@ contract LBRouter is ILBRouter {
  391, 390: 
  392, 391:         amountOut = _swapExactTokensForTokens(_amountIn, _pairs, _pairBinSteps, _tokenPath, address(this));
  393, 392: 
- 394     :-        if (_amountOutMinAVAX > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMinAVAX, amountOut);
+      393:+        Validation.amount_out_is_sufficient(_amountOutMinAVAX, amountOut);
  395, 394: 
  396, 395:         wavax.withdraw(amountOut);
  397, 396:         _safeTransferAVAX(_to, amountOut);
@@ -417,7 +416,7 @@ contract LBRouter is ILBRouter {
  417, 416: 
  418, 417:         amountOut = _swapExactTokensForTokens(msg.value, _pairs, _pairBinSteps, _tokenPath, _to);
  419, 418: 
- 420     :-        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
+      419:+        Validation.amount_out_is_sufficient(_amountOutMin, amountOut);
  421, 420:     }
  422, 421: 
  423, 422:     /// @notice Swaps tokens for exact tokens while performing safety checks
@@ -439,13 +438,13 @@ contract LBRouter is ILBRouter {
  439, 438:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  440, 439:         amountsIn = _getAmountsIn(_pairBinSteps, _pairs, _tokenPath, _amountOut);
  441, 440: 
- 442     :-        if (amountsIn[0] > _amountInMax) revert LBRouter__MaxAmountInExceeded(_amountInMax, amountsIn[0]);
+      441:+        Validation.is_max_amount_exceeded(amountsIn[0], _amountInMax);
  443, 442: 
  444, 443:         _tokenPath[0].safeTransferFrom(msg.sender, _pairs[0], amountsIn[0]);
  445, 444: 
  446, 445:         uint256 _amountOutReal = _swapTokensForExactTokens(_pairs, _pairBinSteps, _tokenPath, amountsIn, _to);
  447, 446: 
- 448     :-        if (_amountOutReal < _amountOut) revert LBRouter__InsufficientAmountOut(_amountOut, _amountOutReal);
+      447:+        Validation.amount_out_is_sufficient(_amountOut, _amountOutReal);
  449, 448:     }
  450, 449: 
  451, 450:     /// @notice Swaps tokens for exact AVAX while performing safety checks
@@ -464,19 +463,18 @@ contract LBRouter is ILBRouter {
  464, 463:         address payable _to,
  465, 464:         uint256 _deadline
  466, 465:     ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {
- 467     :-        if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
- 468     :-            revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
+      466:+        Validation.is_valid_token_path(_tokenPath[_pairBinSteps.length], IERC20(wavax));
  469, 467: 
  470, 468:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  471, 469:         amountsIn = _getAmountsIn(_pairBinSteps, _pairs, _tokenPath, _amountAVAXOut);
  472, 470: 
- 473     :-        if (amountsIn[0] > _amountInMax) revert LBRouter__MaxAmountInExceeded(_amountInMax, amountsIn[0]);
+      471:+        Validation.is_max_amount_exceeded(amountsIn[0], _amountInMax);
  474, 472: 
  475, 473:         _tokenPath[0].safeTransferFrom(msg.sender, _pairs[0], amountsIn[0]);
  476, 474: 
  477, 475:         uint256 _amountOutReal = _swapTokensForExactTokens(_pairs, _pairBinSteps, _tokenPath, amountsIn, address(this));
  478, 476: 
- 479     :-        if (_amountOutReal < _amountAVAXOut) revert LBRouter__InsufficientAmountOut(_amountAVAXOut, _amountOutReal);
+      477:+        Validation.amount_out_is_sufficient(_amountAVAXOut, _amountOutReal);
  480, 478: 
  481, 479:         wavax.withdraw(_amountOutReal);
  482, 480:         _safeTransferAVAX(_to, _amountOutReal);
@@ -504,18 +502,18 @@ contract LBRouter is ILBRouter {
  504, 502:         verifyInputs(_pairBinSteps, _tokenPath)
  505, 503:         returns (uint256[] memory amountsIn)
  506, 504:     {
- 507     :-        if (_tokenPath[0] != IERC20(wavax)) revert LBRouter__InvalidTokenPath(address(_tokenPath[0]));
+      505:+        Validation.is_valid_token_path(_tokenPath[0], IERC20(wavax));
  508, 506: 
  509, 507:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  510, 508:         amountsIn = _getAmountsIn(_pairBinSteps, _pairs, _tokenPath, _amountOut);
  511, 509: 
- 512     :-        if (amountsIn[0] > msg.value) revert LBRouter__MaxAmountInExceeded(msg.value, amountsIn[0]);
+      510:+        Validation.is_max_amount_exceeded(amountsIn[0], msg.value);
  513, 511: 
  514, 512:         _wavaxDepositAndTransfer(_pairs[0], amountsIn[0]);
  515, 513: 
  516, 514:         uint256 _amountOutReal = _swapTokensForExactTokens(_pairs, _pairBinSteps, _tokenPath, amountsIn, _to);
  517, 515: 
- 518     :-        if (_amountOutReal < _amountOut) revert LBRouter__InsufficientAmountOut(_amountOut, _amountOutReal);
+      516:+        Validation.amount_out_is_sufficient(_amountOut, _amountOutReal);
  519, 517: 
  520, 518:         if (msg.value > amountsIn[0]) _safeTransferAVAX(_to, amountsIn[0] - msg.value);
  521, 519:     }
@@ -547,7 +545,7 @@ contract LBRouter is ILBRouter {
  547, 545:         _swapSupportingFeeOnTransferTokens(_pairs, _pairBinSteps, _tokenPath, _to);
  548, 546: 
  549, 547:         amountOut = _targetToken.balanceOf(_to) - _balanceBefore;
- 550     :-        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
+      548:+        Validation.amount_out_is_sufficient(_amountOutMin, amountOut);
  551, 549:     }
  552, 550: 
  553, 551:     /// @notice Swaps exact tokens for AVAX while performing safety checks supporting for fee on transfer tokens
@@ -566,8 +564,7 @@ contract LBRouter is ILBRouter {
  566, 564:         address payable _to,
  567, 565:         uint256 _deadline
  568, 566:     ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
- 569     :-        if (_tokenPath[_pairBinSteps.length] != IERC20(wavax))
- 570     :-            revert LBRouter__InvalidTokenPath(address(_tokenPath[_pairBinSteps.length]));
+      567:+        Validation.is_valid_token_path(_tokenPath[_pairBinSteps.length], IERC20(wavax));
  571, 568: 
  572, 569:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  573, 570: 
@@ -578,7 +575,7 @@ contract LBRouter is ILBRouter {
  578, 575:         _swapSupportingFeeOnTransferTokens(_pairs, _pairBinSteps, _tokenPath, address(this));
  579, 576: 
  580, 577:         amountOut = wavax.balanceOf(address(this)) - _balanceBefore;
- 581     :-        if (_amountOutMinAVAX > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMinAVAX, amountOut);
+      578:+        Validation.amount_out_is_sufficient(_amountOutMinAVAX, amountOut);
  582, 579: 
  583, 580:         wavax.withdraw(amountOut);
  584, 581:         _safeTransferAVAX(_to, amountOut);
@@ -598,7 +595,7 @@ contract LBRouter is ILBRouter {
  598, 595:         address _to,
  599, 596:         uint256 _deadline
  600, 597:     ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
- 601     :-        if (_tokenPath[0] != IERC20(wavax)) revert LBRouter__InvalidTokenPath(address(_tokenPath[0]));
+      598:+        Validation.is_valid_token_path(_tokenPath[0], IERC20(wavax));
  602, 599: 
  603, 600:         address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
  604, 601: 
@@ -611,7 +608,7 @@ contract LBRouter is ILBRouter {
  611, 608:         _swapSupportingFeeOnTransferTokens(_pairs, _pairBinSteps, _tokenPath, _to);
  612, 609: 
  613, 610:         amountOut = _targetToken.balanceOf(_to) - _balanceBefore;
- 614     :-        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
+      611:+        Validation.amount_out_is_sufficient(_amountOutMin, amountOut);
  615, 612:     }
  616, 613: 
  617, 614:     /// @notice Unstuck tokens that are sent to this contract by mistake
@@ -673,7 +670,7 @@ contract LBRouter is ILBRouter {
  673, 670:             depositIds = new uint256[](_liq.deltaIds.length);
  674, 671:             for (uint256 i; i < depositIds.length; ++i) {
  675, 672:                 int256 _id = int256(_activeId) + _liq.deltaIds[i];
- 676     :-                if (_id < 0 || uint256(_id) > type(uint24).max) revert LBRouter__IdOverflows(_id);
+      673:+                Validation.valid_id(_id);
  677, 674:                 depositIds[i] = uint256(_id);
  678, 675:             }
  679, 676: 
@@ -687,8 +684,7 @@ contract LBRouter is ILBRouter {
  687, 684:                 _liq.to
  688, 685:             );
  689, 686: 
- 690     :-            if (_amountXAdded < _liq.amountXMin || _amountYAdded < _liq.amountYMin)
- 691     :-                revert LBRouter__AmountSlippageCaught(_liq.amountXMin, _amountXAdded, _liq.amountYMin, _amountYAdded);
+      687:+            Validation.caugth_amount_slippage(_amountXAdded, _liq.amountXMin, _amountYAdded, _liq.amountYMin);
  692, 688:         }
  693, 689:     }
  694, 690: 
@@ -748,8 +744,7 @@ contract LBRouter is ILBRouter {
  748, 744:     ) private returns (uint256 amountX, uint256 amountY) {
  749, 745:         ILBToken(address(_LBPair)).safeBatchTransferFrom(msg.sender, address(_LBPair), _ids, _amounts);
  750, 746:         (amountX, amountY) = _LBPair.burn(_ids, _amounts, _to);
- 751     :-        if (amountX < _amountXMin || amountY < _amountYMin)
- 752     :-            revert LBRouter__AmountSlippageCaught(_amountXMin, amountX, _amountYMin, amountY);
+      747:+        Validation.caugth_amount_slippage(amountX, _amountXMin, amountY, _amountYMin);
  753, 748:     }
  754, 749: 
  755, 750:     /// @notice Helper function to swap exact tokens for tokens
```

##### - src/libraries/BinHelper.sol:[29](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L29)

```diff
diff --git a/src/libraries/BinHelper.sol b/src/libraries/BinHelper.sol
index db0fef8..54ff604 100644
--- a/src/libraries/BinHelper.sol
+++ b/src/libraries/BinHelper.sol
@@ -26,7 +26,7 @@ library BinHelper {
   26,  26:             // can't overflow as `2**23 + log2(price) < 2**23 + 2**128 < max(uint256)`
   27,  27:             int256 _id = REAL_ID_SHIFT + _price.log2() / _binStepValue.log2();
   28,  28: 
-  29     :-            if (_id < 0 || uint256(_id) > type(uint24).max) revert BinHelper__IdOverflows(_id);
+       29:+            Validation.valid_id(_id);
   30,  30:             return uint24(uint256(_id));
   31,  31:         }
   32,  32:     }
```

##### - src/libraries/JoeLibrary.sol:[15](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L15), [25](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L25), [36](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L36), [50](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L50)

```diff
diff --git a/src/libraries/JoeLibrary.sol b/src/libraries/JoeLibrary.sol
index cce7d28..da7bbe9 100644
--- a/src/libraries/JoeLibrary.sol
+++ b/src/libraries/JoeLibrary.sol
@@ -12,7 +12,7 @@ library JoeLibrary {
   12,  12:     function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {
   13,  13:         if (tokenA == tokenB) revert JoeLibrary__IdenticalAddresses();
   14,  14:         (token0, token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
-  15     :-        if (token0 == address(0)) revert JoeLibrary__AddressZero();
+       15:+        Validation.is_address_zero(token0);
   16,  16:     }
   17,  17: 
   18,  18:     // given some amount of an asset and pair reserves, returns an equivalent amount of the other asset
@@ -22,7 +22,7 @@ library JoeLibrary {
   22,  22:         uint256 reserveB
   23,  23:     ) internal pure returns (uint256 amountB) {
   24,  24:         if (amountA == 0) revert JoeLibrary__InsufficientAmount();
-  25     :-        if (reserveA == 0 || reserveB == 0) revert JoeLibrary__InsufficientLiquidity();
+       25:+        Validation.valid_liquidity(reserveA, reserveB);
   26,  26:         amountB = (amountA * reserveB) / reserveA;
   27,  27:     }
   28,  28: 
@@ -33,7 +33,7 @@ library JoeLibrary {
   33,  33:         uint256 reserveOut
   34,  34:     ) internal pure returns (uint256 amountOut) {
   35,  35:         if (amountIn == 0) revert JoeLibrary__InsufficientAmount();
-  36     :-        if (reserveIn == 0 || reserveOut == 0) revert JoeLibrary__InsufficientLiquidity();
+       36:+        Validation.valid_liquidity(reserveOut, reserveIn);
   37,  37:         uint256 amountInWithFee = amountIn * 997;
   38,  38:         uint256 numerator = amountInWithFee * reserveOut;
   39,  39:         uint256 denominator = reserveIn * 1000 + amountInWithFee;
@@ -47,7 +47,7 @@ library JoeLibrary {
   47,  47:         uint256 reserveOut
   48,  48:     ) internal pure returns (uint256 amountIn) {
   49,  49:         if (amountOut == 0) revert JoeLibrary__InsufficientAmount();
-  50     :-        if (reserveIn == 0 || reserveOut == 0) revert JoeLibrary__InsufficientLiquidity();
+       50:+        Validation.valid_liquidity(reserveIn, reserveOut);
   51,  51:         uint256 numerator = reserveIn * amountOut * 1000;
   52,  52:         uint256 denominator = (reserveOut - amountOut) * 997;
   53,  53:         amountIn = numerator / denominator + 1;
```

##### - src/libraries/PendingOwnable.sol:[60](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L60)

```diff
diff --git a/src/libraries/PendingOwnable.sol b/src/libraries/PendingOwnable.sol
index f745362..6d9ae24 100644
--- a/src/libraries/PendingOwnable.sol
+++ b/src/libraries/PendingOwnable.sol
@@ -57,7 +57,7 @@ contract PendingOwnable is IPendingOwnable {
   57,  57:     /// @notice Sets the pending owner address. This address will be able to become
   58,  58:     /// the owner of this contract by calling {becomeOwner}
   59,  59:     function setPendingOwner(address pendingOwner_) public override onlyOwner {
-  60     :-        if (pendingOwner_ == address(0)) revert PendingOwnable__AddressZero();
+       60:+        Validation.is_address_zero(pendingOwner_);
   61,  61:         if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();
   62,  62:         _setPendingOwner(pendingOwner_);
   63,  63:     }
```

```diff
diff --git a/test/LBFactory.t.sol b/test/LBFactory.t.sol
index 74d841e..72c2643 100644
--- a/test/LBFactory.t.sol
+++ b/test/LBFactory.t.sol
@@ -122,7 +122,7 @@ contract LiquidityBinFactoryTest is TestHelper {
  122, 122:     }
  123, 123: 
  124, 124:     function testSetFeeRecipient() public {
- 125     :-        vm.expectRevert(LBFactory__AddressZero.selector);
+      125:+        vm.expectRevert(AddressZero.selector);
  126, 126:         factory.setFeeRecipient(address(0));
  127, 127: 
  128, 128:         factory.setFeeRecipient(ALICE);
@@ -164,10 +164,10 @@ contract LiquidityBinFactoryTest is TestHelper {
  164, 164: 
  165, 165:     function testForZeroAddressPairReverts() public {
  166, 166:         factory.addQuoteAsset(IERC20(address(0)));
- 167     :-        vm.expectRevert(LBFactory__AddressZero.selector);
+      167:+        vm.expectRevert(AddressZero.selector);
  168, 168:         factory.createLBPair(token6D, IERC20(address(0)), ID_ONE, DEFAULT_BIN_STEP);
  169, 169: 
- 170     :-        vm.expectRevert(LBFactory__AddressZero.selector);
+      170:+        vm.expectRevert(AddressZero.selector);
  171, 171:         factory.createLBPair(IERC20(address(0)), token6D, ID_ONE, DEFAULT_BIN_STEP);
  172, 172:     }
  173, 173: 
@@ -400,7 +400,7 @@ contract LiquidityBinFactoryTest is TestHelper {
  400, 400:     }
  401, 401: 
  402, 402:     function testForInvalidFeeRecipient() public {
- 403     :-        vm.expectRevert(LBFactory__AddressZero.selector);
+      403:+        vm.expectRevert(AddressZero.selector);
  404, 404:         factory = new LBFactory(address(0), 8e14);
  405, 405:     }
  406, 406: 
```

---

#### 4. **Unchecking arithmetics operations that can't underflow/overflow (2 instances)**

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block: https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic

Deployment. Gas Saved: **58 524**

Minimum Method Call. Gas Saved: **0**

Average Method Call. Gas Saved: **651**

Maximum Method Call. Gas Saved: **1 104**

Overall gas change: **-23 139 (-10.126%)**

##### - src/LBFactory.sol:[557](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L557)

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..38e0553 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -554,8 +554,8 @@ contract LBFactory is PendingOwnable, ILBFactory {
  554, 554:         if (_protocolShare > MAX_PROTOCOL_SHARE)
  555, 555:             revert LBFactory__ProtocolShareOverflows(_protocolShare, MAX_PROTOCOL_SHARE);
  556, 556: 
- 557     :-        {
- 558     :-            uint256 _baseFee = (uint256(_baseFactor) * _binStep) * 1e10;
+      557:+        unchecked {
+      558:+            uint256 _baseFee = (uint256(_baseFactor) * _binStep) * 1e10; 
  559, 559: 
  560, 560:             // Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
  561, 561:             // It returns 18 decimals as:
```

##### - src/LBQuoter.sol:[65](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L65)

```diff
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..62d24fa 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -62,7 +62,7 @@ contract LBQuoter {
   62,  62: 
   63,  63:         quote.route = _route;
   64,  64: 
-  65     :-        uint256 swapLength = _route.length - 1;
+       65:+        uint256 swapLength; unchecked { swapLength = _route.length - 1; }
   66,  66:         quote.pairs = new address[](swapLength);
   67,  67:         quote.binSteps = new uint256[](swapLength);
   68,  68:         quote.fees = new uint256[](swapLength);
```

---

#### 5. **Use of the memory keyword when storage pointer should be used (4 instances)**

**NOTE:** this optimization can potentially backfire if local changes should not affect storage and the code is not covered by tests

Deployment. Gas Saved: **53 523**

Minimum Method Call. Gas Saved: **55**

Average Method Call. Gas Saved: **13 580**

Maximum Method Call. Gas Saved: **15 945**

Overall gas change: **-285 125 (-22.690%)**

##### - src/LBFactory.sol:[197](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L197), [320](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L320)

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..25128cd 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -194,7 +194,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
  194, 194:                 uint256 _index;
  195, 195:                 for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
  196, 196:                     if (_avLBPairBinSteps.decode(1, i) == 1) {
- 197     :-                        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];
+      197:+                        LBPairInformation storage _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];
  198, 198: 
  199, 199:                         LBPairsAvailable[_index] = LBPairInformation({
  200, 200:                             binStep: i.safe24(),
@@ -317,7 +317,7 @@ contract LBFactory is PendingOwnable, ILBFactory {
  317, 317:     ) external override onlyOwner {
  318, 318:         (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
  319, 319: 
- 320     :-        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
+      320:+        LBPairInformation storage _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
  321, 321:         if (address(_LBPairInformation.LBPair) == address(0)) revert LBFactory__AddressZero();
  322, 322: 
  323, 323:         if (_LBPairInformation.ignoredForRouting == _ignored) revert LBFactory__LBPairIgnoredIsAlreadyInTheSameState();
```

##### - src/LBPair.sol:[486](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27486), [850](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27850)

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..1bd6bca 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -483,7 +483,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  483, 483: 
  484, 484:         PairInformation memory _pair = _pairInformation;
  485, 485: 
- 486     :-        FeeHelper.FeeParameters memory _fp = _feeParameters;
+      486:+        FeeHelper.FeeParameters storage _fp = _feeParameters;
  487, 487: 
  488, 488:         MintInfo memory _mintInfo;
  489, 489: 
@@ -847,7 +847,7 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  847, 847:         uint256 _id,
  848, 848:         uint256 _balance
  849, 849:     ) private view returns (uint256 amountX, uint256 amountY) {
- 850     :-        Debts memory _debts = _accruedDebts[_account][_id];
+      850:+        Debts storage _debts = _accruedDebts[_account][_id];
  851, 851: 
  852, 852:         amountX = _bin.accTokenXPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET) - _debts.debtX;
  853, 853:         amountY = _bin.accTokenYPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET) - _debts.debtY;
```

---

#### 6. **Using calldata instead of memory for read-only arguments in external functions saves gas (1 instance)**

Deployment. Gas Saved: **39 086**

Minimum Method Call. Gas Saved: **2 256**

Average Method Call. Gas Saved: **1 199**

Maximum Method Call. Gas Saved: **-2 165**

Overall gas change: **-59 491 (-3.744%)**

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 \* <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

##### - src/LBToken.sol:[79](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L79)

```diff
diff --git a/src/LBToken.sol b/src/LBToken.sol
index 47aa528..cd15461 100644
--- a/src/LBToken.sol
+++ b/src/LBToken.sol
@@ -76,7 +76,7 @@ contract LBToken is ILBToken {
   76,  76:     /// @param _accounts The addresses of the owners
   77,  77:     /// @param _ids The token ids
   78,  78:     /// @return batchBalances The balance for each (account, id) pair
-  79     :-    function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
+       79:+    function balanceOfBatch(address[] calldata _accounts, uint256[] memory _ids)
   80,  80:         public
   81,  81:         view
   82,  82:         virtual
```



**Optional: This will result in gas loss on deployment, but saves a lot of gas on user calls, which may be preferable in the long run.**

##### - src/LBPair.sol:[467](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27467), [468](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27468), [469](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27469), 

Deployment. Gas Saved: -58 330

Minimum Method Call. Gas Saved: 607

Average Method Call. Gas Saved: 8 836

Maximum Method Call. Gas Saved: 41 401

Overall gas change: -160 542 (-15.212%)

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..aa2eba6 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -464,9 +464,9 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  464, 464:     /// @return The amount of token Y that was added to the pair
  465, 465:     /// @return liquidityMinted Amount of LBToken minted
  466, 466:     function mint(
- 467     :-        uint256[] memory _ids,
- 468     :-        uint256[] memory _distributionX,
- 469     :-        uint256[] memory _distributionY,
+      467:+        uint256[] calldata _ids,
+      468:+        uint256[] calldata _distributionX,
+      469:+        uint256[] calldata _distributionY,
  470, 470:         address _to
  471, 471:     )
  472, 472:         external
```

Deployment. Gas Saved: -60 130

Minimum Method Call. Gas Saved: 736

Average Method Call. Gas Saved: 5 291

Maximum Method Call. Gas Saved: 10 353

Overall gas change: 56 953 (-3.050%)

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..68d24ed 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -614,8 +614,8 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  614, 614:     /// @return amountX The amount of token X sent to `_to`
  615, 615:     /// @return amountY The amount of token Y sent to `_to`
  616, 616:     function burn(
- 617     :-        uint256[] memory _ids,
- 618     :-        uint256[] memory _amounts,
+      617:+        uint256[] calldata _ids,
+      618:+        uint256[] calldata _amounts,
  619, 619:         address _to
  620, 620:     ) external override nonReentrant returns (uint256 amountX, uint256 amountY) {
  621, 621:         (uint256 _pairReserveX, uint256 _pairReserveY, uint256 _activeId) = _getReservesAndId();
```

##### - src/LBToken.sol:[]

Deployment. Gas Saved: -108 844

Minimum Method Call. Gas Saved: 2 132

Average Method Call. Gas Saved: 9 096

Maximum Method Call. Gas Saved: 20 270

Overall gas change: 83 837 (-1.102%)

```diff
diff --git a/src/LBToken.sol b/src/LBToken.sol
index 47aa528..f3c211a 100644
--- a/src/LBToken.sol
+++ b/src/LBToken.sol
@@ -149,8 +149,8 @@ contract LBToken is ILBToken {
  149, 149:     function safeBatchTransferFrom(
  150, 150:         address _from,
  151, 151:         address _to,
- 152     :-        uint256[] memory _ids,
- 153     :-        uint256[] memory _amounts
+      152:+        uint256[] calldata _ids,
+      153:+        uint256[] calldata _amounts
  154, 154:     )
  155, 155:         public
  156, 156:         virtual
```

---

#### 7. **Do not cache msg.sender (1 instance)**

Deployment. Gas Saved: **13 843**

Minimum Method Call. Gas Saved: **0**

Average Method Call. Gas Saved: **-140**

Maximum Method Call. Gas Saved: **35**

Overall gas change: **-18 529** (-0.180%)

##### - src/LBToken.sol:[137](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L137)

```diff
diff --git a/src/LBToken.sol b/src/LBToken.sol
index 47aa528..48c1853 100644
--- a/src/LBToken.sol
+++ b/src/LBToken.sol
@@ -134,11 +134,9 @@ contract LBToken is ILBToken {
  134, 134:         uint256 _id,
  135, 135:         uint256 _amount
  136, 136:     ) public virtual override checkAddresses(_from, _to) checkApproval(_from, msg.sender) {
- 137     :-        address _spender = msg.sender;
- 138     :-
  139, 137:         _transfer(_from, _to, _id, _amount);
  140, 138: 
- 141     :-        emit TransferSingle(_spender, _from, _to, _id, _amount);
+      139:+        emit TransferSingle(msg.sender, _from, _to, _id, _amount);
  142, 140:     }
  143, 141: 
  144, 142:     /// @notice Batch transfers `_amount` tokens of type `_id` from `_from` to `_to`
```

---

#### 8. **Unnecessary else statement (4 instances)**

Deployment. Gas Saved: **7 217**

Minimum Method Call. Gas Saved: **0**

Average Method Call. Gas Saved: **-88**

Maximum Method Call. Gas Saved: **338**

Overall gas change: **-9 171 (-0.210%)**

##### - src/LBPair.sol:[372](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27372)

```diff
diff --git a/src/LBPair.sol b/src/LBPair.sol
index 717270e..33965f8 100644
--- a/src/LBPair.sol
+++ b/src/LBPair.sol
@@ -367,11 +367,11 @@ contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
  367, 367:                 }
  368, 368:             }
  369, 369: 
- 370     :-            if (_amountIn != 0) {
- 371     :-                _pair.activeId = _tree.findFirstBin(_pair.activeId, _swapForY);
- 372     :-            } else {
+      370:+            if (_amountIn == 0) {
  373, 371:                 break;
- 374     :-            }
+      372:+                
+      373:+            } 
+      374:+            _pair.activeId = _tree.findFirstBin(_pair.activeId, _swapForY);
  375, 375:         }
  376, 376: 
  377, 377:         if (_amountOut == 0) revert LBPair__BrokenSwapSafetyCheck(); // Safety check
```

##### - src/LBRouter.sol:[135](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L135)

```diff
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..d5273b5 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -130,11 +130,10 @@ contract LBRouter is ILBRouter {
  130, 130:                 _amountOut -= _amountOutOfBin;
  131, 131:             }
  132, 132: 
- 133     :-            if (_amountOut != 0) {
- 134     :-                _activeId = _LBPair.findFirstNonEmptyBinId(uint24(_activeId), _swapForY);
- 135     :-            } else {
+      133:+            if (_amountOut == 0) {
  136, 134:                 break;
- 137     :-            }
+      135:+            } 
+      136:+            _activeId = _LBPair.findFirstNonEmptyBinId(uint24(_activeId), _swapForY);
  138, 137:         }
  139, 138:         if (_amountOut != 0) revert LBRouter__BrokenSwapSafetyCheck(); // Safety check, but should never be false as it would have reverted on transfer
  140, 139:     }
```

##### - src/libraries/BitMath.sol:[21](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L21), [31](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L31)

```diff
diff --git a/src/libraries/BitMath.sol b/src/libraries/BitMath.sol
index d088fdf..9b8445d 100644
--- a/src/libraries/BitMath.sol
+++ b/src/libraries/BitMath.sol
@@ -18,7 +18,8 @@ library BitMath {
   18,  18:     ) internal pure returns (uint256) {
   19,  19:         if (_rightSide) {
   20,  20:             return closestBitRight(_integer, _bit - 1);
-  21     :-        } else return closestBitLeft(_integer, _bit + 1);
+       21:+        }
+       22:+        return closestBitLeft(_integer, _bit + 1);
   22,  23:     }
   23,  24: 
   24,  25:     /// @notice Returns the most (or least) significant bit of `_integer`
@@ -28,7 +29,8 @@ library BitMath {
   28,  29:     function significantBit(uint256 _integer, bool _isMostSignificant) internal pure returns (uint8) {
   29,  30:         if (_isMostSignificant) {
   30,  31:             return mostSignificantBit(_integer);
-  31     :-        } else return leastSignificantBit(_integer);
+       32:+        } 
+       33:+        return leastSignificantBit(_integer);
   32,  34:     }
   33,  35: 
   34,  36:     /// @notice Returns the index of the closest bit on the right of x that is non null
```

---

#### 9. **`++i` costs less gas than `i++`, especially when it's used in for-loops (`--i`/`i--` too) (5 instances)**

Deployment. Gas Saved: **2 800**

Minimum Method Call. Gas Saved: **0**

Average Method Call. Gas Saved: **32**

Maximum Method Call. Gas Saved: **69**

Overall gas change: **-521** (-0.032%)

Saves 6 gas per loop

Pre-increments and pre-decrements are cheaper.

For a uint256 i variable, the following is true with the Optimizer enabled at 10k:

Increment:

i += 1 is the most expensive form
i++ costs 6 gas less than i += 1
++i costs 5 gas less than i++ (11 gas less than i += 1)
Decrement:

i -= 1 is the most expensive form
i-- costs 11 gas less than i -= 1
--i costs 5 gas less than i-- (16 gas less than i -= 1)

##### - src/LBQuoter.sol:[75](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75), [100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100), [154](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154), [177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)

```diff
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..65d105c 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -72,7 +72,7 @@ contract LBQuoter {
   72,  72:         quote.amounts[0] = _amountIn;
   73,  73:         quote.virtualAmountsWithoutSlippage[0] = _amountIn;
   74,  74: 
-  75     :-        for (uint256 i; i < swapLength; i++) {
+       75:+        for (uint256 i; i < swapLength; ++i) {
   76,  76:             // Fetch swap for V1
   77,  77:             quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);
   78,  78: 
@@ -97,7 +97,7 @@ contract LBQuoter {
   97,  97:             );
   98,  98: 
   99,  99:             if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
- 100     :-                for (uint256 j; j < LBPairsAvailable.length; j++) {
+      100:+                for (uint256 j; j < LBPairsAvailable.length; ++j) {
  101, 101:                     if (!LBPairsAvailable[j].ignoredForRouting) {
  102, 102:                         bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i + 1];
  103, 103: 
@@ -151,7 +151,7 @@ contract LBQuoter {
  151, 151:         quote.amounts[swapLength] = _amountOut;
  152, 152:         quote.virtualAmountsWithoutSlippage[swapLength] = _amountOut;
  153, 153: 
- 154     :-        for (uint256 i = swapLength; i > 0; i--) {
+      154:+        for (uint256 i = swapLength; i > 0; --i) {
  155, 155:             // Fetch swap for V1
  156, 156:             quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]);
  157, 157:             if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
@@ -174,7 +174,7 @@ contract LBQuoter {
  174, 174:             );
  175, 175: 
  176, 176:             if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
- 177     :-                for (uint256 j; j < LBPairsAvailable.length; j++) {
+      177:+                for (uint256 j; j < LBPairsAvailable.length; ++j) {
  178, 178:                     if (!LBPairsAvailable[j].ignoredForRouting) {
  179, 179:                         bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i];
  180, 180:                         try
```

##### - src/LBRouter.sol:[711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)

```diff
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..00275f9 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -708,7 +708,7 @@ contract LBRouter is ILBRouter {
  708, 708:         // Avoid doing -1, as `_pairs.length == _pairBinSteps.length-1`
  709, 709:         amountsIn[_pairs.length] = _amountOut;
  710, 710: 
- 711     :-        for (uint256 i = _pairs.length; i != 0; i--) {
+      711:+        for (uint256 i = _pairs.length; i != 0; --i) {
  712, 712:             IERC20 _token = _tokenPath[i - 1];
  713, 713:             uint256 _binStep = _pairBinSteps[i - 1];
  714, 714: 
```

---

#### 99. **Overall Gas Saving**

Deployment. Gas Saved: **2 623 283**

Minimum Method Call. Gas Saved: **5 381**

Average Method Call. Gas Saved: **28 755**

Maximum Method Call. Gas Saved: **48 090**

Overall gas change: **-1 625 048 (-119.427%)**

```diff
diff --git a/original.txt b/fixed.txt
old mode 100644
new mode 100755
 ╭──────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
 │ src/LBFactory.sol:LBFactory contract ┆                 ┆        ┆        ┆        ┆         │
 ╞══════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
 │ Deployment Cost                      ┆ Deployment Size ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 2086657                              ┆ 10631           ┆        ┆        ┆        ┆         │
+│ 1933732                              ┆ 9892            ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                        ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -378,19 +410,19 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ MIN_BIN_STEP                         ┆ 296             ┆ 296    ┆ 296    ┆ 296    ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addQuoteAsset                        ┆ 2960            ┆ 53078  ┆ 46613  ┆ 68513  ┆ 476     │
+│ addQuoteAsset                        ┆ 2984            ┆ 53102  ┆ 46637  ┆ 68537  ┆ 476     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ createLBPair                         ┆ 2107            ┆ 302135 ┆ 299901 ┆ 334301 ┆ 273     │
+│ createLBPair                         ┆ 2133            ┆ 302259 ┆ 300029 ┆ 334429 ┆ 273     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ feeRecipient                         ┆ 449             ┆ 1306   ┆ 449    ┆ 2449   ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ flashLoanFee                         ┆ 330             ┆ 1930   ┆ 2330   ┆ 2330   ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ forceDecay                           ┆ 3694            ┆ 3694   ┆ 3694   ┆ 3694   ┆ 1       │
+│ forceDecay                           ┆ 3742            ┆ 3742   ┆ 3742   ┆ 3742   ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getAllBinSteps                       ┆ 8914            ┆ 9644   ┆ 9106   ┆ 10914  ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getAllLBPairs                        ┆ 3125            ┆ 8407   ┆ 8434   ┆ 20585  ┆ 21      │
+│ getAllLBPairs                        ┆ 3125            ┆ 8152   ┆ 8281   ┆ 20127  ┆ 21      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getLBPairInformation                 ┆ 1600            ┆ 2245   ┆ 1609   ┆ 3609   ┆ 256     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -398,7 +430,7 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getNumberOfQuoteAssets               ┆ 2427            ┆ 2427   ┆ 2427   ┆ 2427   ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getPreset                            ┆ 1641            ┆ 2651   ┆ 2671   ┆ 3641   ┆ 3       │
+│ getPreset                            ┆ 1674            ┆ 2679   ┆ 2691   ┆ 3674   ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getQuoteAsset                        ┆ 782             ┆ 2382   ┆ 2782   ┆ 2782   ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -406,52 +438,52 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ owner                                ┆ 421             ┆ 1278   ┆ 421    ┆ 2421   ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removePreset                         ┆ 810             ┆ 5909   ┆ 5796   ┆ 11236  ┆ 4       │
+│ removePreset                         ┆ 858             ┆ 5958   ┆ 5845   ┆ 11285  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeQuoteAsset                     ┆ 2331            ┆ 3639   ┆ 3639   ┆ 4948   ┆ 2       │
+│ removeQuoteAsset                     ┆ 2350            ┆ 3661   ┆ 3661   ┆ 4972   ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setFactoryLockedState                ┆ 4658            ┆ 20793  ┆ 25827  ┆ 25827  ┆ 5       │
+│ setFactoryLockedState                ┆ 4682            ┆ 20817  ┆ 25851  ┆ 25851  ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setFeeRecipient                      ┆ 826             ┆ 3796   ┆ 2592   ┆ 9173   ┆ 4       │
+│ setFeeRecipient                      ┆ 879             ┆ 3836   ┆ 2621   ┆ 9226   ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setFeesParametersOnPair              ┆ 1316            ┆ 3530   ┆ 2440   ┆ 8365   ┆ 6       │
+│ setFeesParametersOnPair              ┆ 1331            ┆ 3470   ┆ 2464   ┆ 7897   ┆ 6       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setFlashLoanFee                      ┆ 630             ┆ 4722   ┆ 4722   ┆ 8815   ┆ 2       │
+│ setFlashLoanFee                      ┆ 654             ┆ 4746   ┆ 4746   ┆ 8839   ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setLBPairIgnored                     ┆ 1522            ┆ 4565   ┆ 3450   ┆ 8259   ┆ 7       │
+│ setLBPairIgnored                     ┆ 1544            ┆ 4666   ┆ 3568   ┆ 8377   ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setLBPairImplementation              ┆ 1524            ┆ 24536  ┆ 25108  ┆ 25108  ┆ 147     │
+│ setLBPairImplementation              ┆ 1420            ┆ 24432  ┆ 25004  ┆ 25004  ┆ 147     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setPreset                            ┆ 3405            ┆ 47455  ┆ 50432  ┆ 50432  ┆ 159     │
+│ setPreset                            ┆ 3429            ┆ 46972  ┆ 49940  ┆ 49940  ┆ 159     │
 ╰──────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
 ╭────────────────────────────────┬─────────────────┬─────────┬─────────┬──────────┬─────────╮
 │ src/LBPair.sol:LBPair contract ┆                 ┆         ┆         ┆          ┆         │
 ╞════════════════════════════════╪═════════════════╪═════════╪═════════╪══════════╪═════════╡
 │ Deployment Cost                ┆ Deployment Size ┆         ┆         ┆          ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 4835641                        ┆ 24410           ┆         ┆         ┆          ┆         │
+│ 4756336                        ┆ 24018           ┆         ┆         ┆          ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                  ┆ min             ┆ avg     ┆ median  ┆ max      ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ balanceOf                      ┆ 677             ┆ 677     ┆ 677     ┆ 677      ┆ 212     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ balanceOfBatch                 ┆ 3766            ┆ 7101    ┆ 5769    ┆ 11769    ┆ 3       │
+│ balanceOfBatch                 ┆ 1852            ┆ 6268    ┆ 5477    ┆ 11477    ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ burn                           ┆ 11235           ┆ 111199  ┆ 107074  ┆ 244666   ┆ 14      │
+│ burn                           ┆ 11065           ┆ 111071  ┆ 106950  ┆ 244531   ┆ 14      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ collectFees                    ┆ 6720            ┆ 27696   ┆ 20481   ┆ 44969    ┆ 7       │
+│ collectFees                    ┆ 6617            ┆ 27338   ┆ 20017   ┆ 44504    ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ collectProtocolFees            ┆ 5620            ┆ 8817    ┆ 9349    ┆ 10952    ┆ 4       │
+│ collectProtocolFees            ┆ 5517            ┆ 8708    ┆ 9247    ┆ 10824    ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ factory                        ┆ 283             ┆ 283     ┆ 283     ┆ 283      ┆ 150     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ feeParameters                  ┆ 1710            ┆ 2296    ┆ 1710    ┆ 3710     ┆ 75      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ findFirstNonEmptyBinId         ┆ 1545            ┆ 2302    ┆ 1568    ┆ 9013     ┆ 42      │
+│ findFirstNonEmptyBinId         ┆ 1545            ┆ 2298    ┆ 1560    ┆ 9013     ┆ 42      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ flashLoan                      ┆ 24674           ┆ 55799   ┆ 24674   ┆ 118050   ┆ 3       │
+│ flashLoan                      ┆ 24546           ┆ 55415   ┆ 24546   ┆ 117154   ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ forceDecay                     ┆ 652             ┆ 652     ┆ 652     ┆ 652      ┆ 1       │
+│ forceDecay                     ┆ 676             ┆ 676     ┆ 676     ┆ 676      ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ getBin                         ┆ 931             ┆ 1149    ┆ 931     ┆ 2931     ┆ 211     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -465,25 +497,25 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ increaseOracleLength           ┆ 46655           ┆ 128799  ┆ 46655   ┆ 2183823  ┆ 52      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ initialize                     ┆ 180121          ┆ 180121  ┆ 180121  ┆ 180121   ┆ 266     │
+│ initialize                     ┆ 180189          ┆ 180189  ┆ 180189  ┆ 180189   ┆ 266     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ isApprovedForAll               ┆ 922             ┆ 1922    ┆ 1922    ┆ 2922     ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ mint                           ┆ 6892            ┆ 1402644 ┆ 1389693 ┆ 14398553 ┆ 216     │
+│ mint                           ┆ 6673            ┆ 1398422 ┆ 1385432 ┆ 14393817 ┆ 216     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ name                           ┆ 575             ┆ 575     ┆ 575     ┆ 575      ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ pendingFees                    ┆ 4041            ┆ 12049   ┆ 12853   ┆ 22185    ┆ 8       │
+│ pendingFees                    ┆ 3938            ┆ 11638   ┆ 12389   ┆ 21406    ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ safeBatchTransferFrom          ┆ 3251            ┆ 408394  ┆ 319740  ┆ 1394600  ┆ 25      │
+│ safeBatchTransferFrom          ┆ 2994            ┆ 407796  ┆ 319342  ┆ 1392964  ┆ 25      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ safeTransferFrom               ┆ 578             ┆ 23071   ┆ 3076    ┆ 86692    ┆ 8       │
+│ safeTransferFrom               ┆ 593             ┆ 23071   ┆ 3119    ┆ 86581    ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ setApprovalForAll              ┆ 626             ┆ 22826   ┆ 24676   ┆ 24676    ┆ 13      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setFeesParameters              ┆ 535             ┆ 535     ┆ 535     ┆ 535      ┆ 1       │
+│ setFeesParameters              ┆ 559             ┆ 559     ┆ 559     ┆ 559      ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swap                           ┆ 22475           ┆ 50433   ┆ 30217   ┆ 341090   ┆ 318     │
+│ swap                           ┆ 22347           ┆ 50298   ┆ 30085   ┆ 340846   ┆ 318     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ symbol                         ┆ 591             ┆ 591     ┆ 591     ┆ 591      ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -502,7 +534,7 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ╞════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
 │ Deployment Cost                    ┆ Deployment Size ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1899867                            ┆ 9873            ┆       ┆        ┆       ┆         │
+│ 1837350                            ┆ 9542            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                      ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -510,9 +542,9 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ factoryV2                          ┆ 205             ┆ 205   ┆ 205    ┆ 205   ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ findBestPathFromAmountIn           ┆ 1033            ┆ 45266 ┆ 45438  ┆ 88927 ┆ 5       │
+│ findBestPathFromAmountIn           ┆ 1039            ┆ 43917 ┆ 44010  ┆ 86194 ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ findBestPathFromAmountOut          ┆ 1011            ┆ 38128 ┆ 40133  ┆ 68750 ┆ 5       │
+│ findBestPathFromAmountOut          ┆ 1017            ┆ 36916 ┆ 39169  ┆ 66421 ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ routerV2                           ┆ 228             ┆ 228   ┆ 228    ┆ 228   ┆ 1       │
 ╰────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
@@ -521,13 +553,13 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ╞═══════════════════════════════════════════════════════╪═════════════════╪═════════╪═════════╪═════════╪═════════╡
 │ Deployment Cost                                       ┆ Deployment Size ┆         ┆         ┆         ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 4591267                                               ┆ 23440           ┆         ┆         ┆         ┆         │
+│ 4109698                                               ┆ 20982           ┆         ┆         ┆         ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                         ┆ min             ┆ avg     ┆ median  ┆ max     ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addLiquidity                                          ┆ 19672           ┆ 1271111 ┆ 1387846 ┆ 3343300 ┆ 132     │
+│ addLiquidity                                          ┆ 19152           ┆ 1266574 ┆ 1383010 ┆ 3338240 ┆ 132     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addLiquidityAVAX                                      ┆ 20036           ┆ 1466315 ┆ 1443921 ┆ 3391638 ┆ 41      │
+│ addLiquidityAVAX                                      ┆ 19474           ┆ 1461677 ┆ 1439085 ┆ 3386578 ┆ 41      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ createLBPair                                          ┆ 279323          ┆ 311452  ┆ 317427  ┆ 331632  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -535,41 +567,41 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ fallback                                              ┆ 95              ┆ 95      ┆ 95      ┆ 95      ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getIdFromPrice                                        ┆ 19069           ┆ 28334   ┆ 32951   ┆ 32983   ┆ 3       │
+│ getIdFromPrice                                        ┆ 18970           ┆ 28235   ┆ 32852   ┆ 32884   ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getPriceFromId                                        ┆ 4771            ┆ 5491    ┆ 5834    ┆ 5868    ┆ 3       │
+│ getPriceFromId                                        ┆ 4643            ┆ 5363    ┆ 5706    ┆ 5740    ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getSwapIn                                             ┆ 1980            ┆ 19023   ┆ 11202   ┆ 61718   ┆ 30      │
+│ getSwapIn                                             ┆ 1852            ┆ 18378   ┆ 10814   ┆ 59440   ┆ 30      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getSwapOut                                            ┆ 12368           ┆ 19897   ┆ 21854   ┆ 28366   ┆ 20      │
+│ getSwapOut                                            ┆ 11984           ┆ 19434   ┆ 21214   ┆ 27440   ┆ 20      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ oldFactory                                            ┆ 274             ┆ 274     ┆ 274     ┆ 274     ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeLiquidity                                       ┆ 2118            ┆ 579712  ┆ 408504  ┆ 1623758 ┆ 10      │
+│ removeLiquidity                                       ┆ 2083            ┆ 578640  ┆ 407635  ┆ 1621279 ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeLiquidityAVAX                                   ┆ 1941            ┆ 976395  ┆ 1115696 ┆ 1672247 ┆ 4       │
+│ removeLiquidityAVAX                                   ┆ 1906            ┆ 974893  ┆ 1113943 ┆ 1669780 ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapAVAXForExactTokens                                ┆ 1534            ┆ 28690   ┆ 8550    ┆ 166660  ┆ 8       │
+│ swapAVAXForExactTokens                                ┆ 1558            ┆ 28425   ┆ 8410    ┆ 165732  ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactAVAXForTokens                                ┆ 1578            ┆ 51907   ┆ 15354   ┆ 153705  ┆ 8       │
+│ swapExactAVAXForTokens                                ┆ 1602            ┆ 51661   ┆ 15087   ┆ 153223  ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactAVAXForTokensSupportingFeeOnTransferTokens   ┆ 1556            ┆ 51197   ┆ 14867   ┆ 163616  ┆ 9       │
+│ swapExactAVAXForTokensSupportingFeeOnTransferTokens   ┆ 1580            ┆ 50893   ┆ 14634   ┆ 162912  ┆ 9       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForAVAX                                ┆ 1630            ┆ 70017   ┆ 15522   ┆ 286498  ┆ 10      │
+│ swapExactTokensForAVAX                                ┆ 1654            ┆ 69752   ┆ 15289   ┆ 285517  ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForAVAXSupportingFeeOnTransferTokens   ┆ 1610            ┆ 56206   ┆ 15502   ┆ 167540  ┆ 10      │
+│ swapExactTokensForAVAXSupportingFeeOnTransferTokens   ┆ 1634            ┆ 55861   ┆ 15269   ┆ 166982  ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForTokens                              ┆ 1631            ┆ 105179  ┆ 67206   ┆ 538501  ┆ 13      │
+│ swapExactTokensForTokens                              ┆ 1655            ┆ 104696  ┆ 66826   ┆ 536406  ┆ 13      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForTokensSupportingFeeOnTransferTokens ┆ 1610            ┆ 55095   ┆ 45689   ┆ 136862  ┆ 10      │
+│ swapExactTokensForTokensSupportingFeeOnTransferTokens ┆ 1634            ┆ 54690   ┆ 45262   ┆ 136126  ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapTokensForExactAVAX                                ┆ 1588            ┆ 29628   ┆ 8610    ┆ 173805  ┆ 8       │
+│ swapTokensForExactAVAX                                ┆ 1612            ┆ 29385   ┆ 8470    ┆ 173063  ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapTokensForExactTokens                              ┆ 1632            ┆ 127916  ┆ 24242   ┆ 610276  ┆ 11      │
+│ swapTokensForExactTokens                              ┆ 1656            ┆ 126813  ┆ 23569   ┆ 606131  ┆ 11      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ sweep                                                 ┆ 6028            ┆ 26141   ┆ 26740   ┆ 36036   ┆ 5       │
+│ sweep                                                 ┆ 5915            ┆ 26023   ┆ 26554   ┆ 35932   ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ sweepLBToken                                          ┆ 3264            ┆ 156904  ┆ 156904  ┆ 310544  ┆ 2       │
+│ sweepLBToken                                          ┆ 3029            ┆ 156352  ┆ 156352  ┆ 309676  ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ wavax                                                 ┆ 252             ┆ 252     ┆ 252     ┆ 252     ┆ 1       │
 ╰───────────────────────────────────────────────────────┴─────────────────┴─────────┴─────────┴─────────┴─────────╯
@@ -578,7 +610,7 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ╞═══════════════════════════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
 │ Deployment Cost                                               ┆ Deployment Size ┆     ┆        ┆     ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 19179229                                                      ┆ 95589           ┆     ┆        ┆     ┆         │
+│ 18281795                                                      ┆ 91114           ┆     ┆        ┆     ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                                 ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -589,7 +621,7 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ╞═══════════════════════════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
 │ Deployment Cost                                               ┆ Deployment Size ┆     ┆        ┆     ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 17699454                                                      ┆ 88210           ┆     ┆        ┆     ┆         │
+│ 16824611                                                      ┆ 83847           ┆     ┆        ┆     ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                                 ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -619,7 +651,7 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ╞═══════════════════════════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
 │ Deployment Cost                                                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 693730                                                                    ┆ 4198            ┆       ┆        ┆       ┆         │
+│ 693718                                                                    ┆ 4161            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -653,11 +685,11 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ╞═══════════════════════════════════════╪═════════════════╪═══════╪════════╪════════╪═════════╡
 │ Deployment Cost                       ┆ Deployment Size ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1098332                               ┆ 5556            ┆       ┆        ┆        ┆         │
+│ 1035666                               ┆ 5243            ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                         ┆ min             ┆ avg   ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addFaucetToken                        ┆ 1000            ┆ 42012 ┆ 45518  ┆ 52318  ┆ 23      │
+│ addFaucetToken                        ┆ 1024            ┆ 42036 ┆ 45542  ┆ 52342  ┆ 23      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ faucetTokens                          ┆ 779             ┆ 1779  ┆ 1779   ┆ 2779   ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -665,38 +697,38 @@ Test result: [32mok[0m. 6 passed; 0 failed; finished in 3.70s
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ owner                                 ┆ 376             ┆ 376   ┆ 376    ┆ 376    ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeFaucetToken                     ┆ 825             ┆ 6224  ┆ 3684   ┆ 16706  ┆ 4       │
+│ removeFaucetToken                     ┆ 849             ┆ 6245  ┆ 3703   ┆ 16725  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ request()                             ┆ 340             ┆ 73455 ┆ 101517 ┆ 137977 ┆ 12      │
+│ request()                             ┆ 340             ┆ 73284 ┆ 101261 ┆ 137721 ┆ 12      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ request(address)                      ┆ 622             ┆ 64004 ┆ 58354  ┆ 138687 ┆ 4       │
+│ request(address)                      ┆ 622             ┆ 63876 ┆ 58226  ┆ 138431 ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ requestCooldown                       ┆ 351             ┆ 351   ┆ 351    ┆ 351    ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setAmountPerRequest                   ┆ 2595            ┆ 7792  ┆ 8192   ┆ 12192  ┆ 4       │
+│ setAmountPerRequest                   ┆ 2610            ┆ 7814  ┆ 8216   ┆ 12216  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setOperator                           ┆ 24694           ┆ 24694 ┆ 24694  ┆ 24694  ┆ 1       │
+│ setOperator                           ┆ 24718           ┆ 24718 ┆ 24718  ┆ 24718  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setRequestCooldown                    ┆ 447             ┆ 2905  ┆ 2905   ┆ 5364   ┆ 2       │
+│ setRequestCooldown                    ┆ 462             ┆ 2925  ┆ 2925   ┆ 5388   ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setUnlockedRequest                    ┆ 585             ┆ 18111 ┆ 22631  ┆ 22631  ┆ 13      │
+│ setUnlockedRequest                    ┆ 604             ┆ 18134 ┆ 22655  ┆ 22655  ┆ 13      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ withdrawToken                         ┆ 644             ┆ 17496 ┆ 18672  ┆ 34613  ┆ 6       │
+│ withdrawToken                         ┆ 659             ┆ 17517 ┆ 18696  ┆ 34637  ┆ 6       │
 ╰───────────────────────────────────────┴─────────────────┴───────┴────────┴────────┴─────────╯
 ╭─────────────────────────────────────────────────────────┬─────────────────┬───────┬────────┬────────┬─────────╮
 │ test/mocks/FlashloanBorrower.sol:FlashBorrower contract ┆                 ┆       ┆        ┆        ┆         │
 ╞═════════════════════════════════════════════════════════╪═════════════════╪═══════╪════════╪════════╪═════════╡
 │ Deployment Cost                                         ┆ Deployment Size ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 413779                                                  ┆ 2134            ┆       ┆        ┆        ┆         │
+│ 401767                                                  ┆ 2074            ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
 │ Function Name                                           ┆ min             ┆ avg   ┆ median ┆ max    ┆ # calls │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ LBFlashLoanCallback                                     ┆ 12455           ┆ 12455 ┆ 12455  ┆ 12455  ┆ 1       │
+│ LBFlashLoanCallback                                     ┆ 11943           ┆ 11943 ┆ 11943  ┆ 11943  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ flashBorrow                                             ┆ 33471           ┆ 77648 ┆ 77648  ┆ 121826 ┆ 2       │
+│ flashBorrow                                             ┆ 33343           ┆ 77136 ┆ 77136  ┆ 120930 ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ flashBorrowWithReentrancy                               ┆ 33427           ┆ 33427 ┆ 33427  ┆ 33427  ┆ 1       │
+│ flashBorrowWithReentrancy                               ┆ 33299           ┆ 33299 ┆ 33299  ┆ 33299  ┆ 1       │
 ╰─────────────────────────────────────────────────────────┴─────────────────┴───────┴────────┴────────┴─────────╯
 ╭─────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
 │ test/mocks/WAVAX.sol:WAVAX contract ┆                 ┆       ┆        ┆       ┆         │
```