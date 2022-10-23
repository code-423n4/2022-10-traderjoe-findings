## [G-001] Cache Array Length Outside of Loop

### Description:

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

### Findings:

[LBPair.sol#L274](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274)

```
274:    for (uint256 i; i < _ids.length; ++i) {
```

[LBPair.sol#L496](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496)

```
496:    for (uint256 i; i < _ids.length; ++i) {
```

[LBPair.sol#L623](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623)

```
623:    for (uint256 i; i < _ids.length; ++i) {
```

[LBPair.sol#L701](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701)

```
701:    for (uint256 i; i < _ids.length; ++i) {
```

[LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)

```
100:    for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)

```
177:    for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBRouter.sol#L674](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674)

```
674:    for (uint256 i; i < depositIds.length; ++i) {
```

[LBRouter.sol#L778](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L778)

```
778:    for (uint256 i; i < _pairs.length; ++i) {
```

[LBRouter.sol#L831](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L831)

```
831:    for (uint256 i; i < _pairs.length; ++i) {
```

[LBRouter.sol#L878](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L878)

```
878:    for (uint256 i; i < _pairs.length; ++i) {
```

[LBRouter.sol#L951](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951)

```
951:    for (uint256 i; i < pairs.length; ++i) {
```

[LBToken.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90)

```
90:    for (uint256 i; i < _accounts.length; ++i) {
```

[LBToken.sol#L163](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163)

```
163:    for (uint256 i; i < _ids.length; ++i) {
```

Total:13

## [G-002] Functions guaranteed to revert when called by normal users can be marked payable

### Description:

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Findings:

[LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215)

```
215:    function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
```

[LBFactory.sol#L317](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L317)

```
317:    ) external override onlyOwner {
```

[LBFactory.sol#L350](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L350)

```
350:    ) external override onlyOwner {
```

[LBFactory.sol#L396](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L396)

```
396:    function removePreset(uint16 _binStep) external override onlyOwner {
```

[LBFactory.sol#L434](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L434)

```
434:    ) external override onlyOwner {
```

[LBFactory.sol#L468](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468)

```
468:    function setFeeRecipient(address _feeRecipient) external override onlyOwner {
```

[LBFactory.sol#L474](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474)

```
474:    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
```

[LBFactory.sol#L485](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485)

```
485:    function setFactoryLockedState(bool _locked) external override onlyOwner {
```

[LBFactory.sol#L493](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L493)

```
493:    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
```

[LBFactory.sol#L502](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L502)

```
502:    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
```

[LBFactory.sol#L520](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520)

```
520:    function forceDecay(ILBPair _LBPair) external override onlyOwner {
```

[LBPair.sol#L110](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L110)

```
110:    ) external override onlyFactory {
```

[LBPair.sol#L788](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L788)

```
788:    function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
```

[LBPair.sol#L792](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L792)

```
792:    function forceDecay() external override onlyFactory {
```

[LBRouter.sol#L626](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L626)

```
626:    ) external override onlyFactoryOwner {
```

[LBRouter.sol#L647](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L647)

```
647:    ) external override onlyFactoryOwner {
```

Total:16

## [G-003] Empty blocks should be removed or emit something

### Description:

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### Findings:

[LBQuoter.sol#L123](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L123)

```
123:    } catch {}
```

[LBQuoter.sol#L203](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L203)

```
203:    } catch {}
```

[LBToken.sol#L326](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L326)

```
326:    ) internal virtual {}
```

Total:3

## [G-004] Use Shift Right/Left instead of Division/Multiplication if possible

### Description:

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.

### Findings:

[Oracle.sol#L162](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L162)

```
162:    _middle = (_low + _high) / 2;
```

Total:1

## [G-005] Unnecessary checked arithmetic in for loop

### Description:

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30~40 gas per loop.

### Findings:

[LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)

```
75:    for (uint256 i; i < swapLength; i++) {
```

[LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)

```
100:    for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)

```
154:    for (uint256 i = swapLength; i > 0; i--) {
```

[LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)

```
177:    for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBRouter.sol#L711](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)

```
711:    for (uint256 i = _pairs.length; i != 0; i--) {
```

Total:5

## [G-006] Use Prefix Increment instead of Postfix Increment if possible

### Description:

`++i` costs less gas than `i++`, especially when it's used in for-loops (`--i/i--`) Saves 5 gas per loop.

### Findings:

[LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)

```
75:    for (uint256 i; i < swapLength; i++) {
```

[LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)

```
100:    for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)

```
154:    for (uint256 i = swapLength; i > 0; i--) {
```

[LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)

```
177:    for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBRouter.sol#L711](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)

```
711:    for (uint256 i = _pairs.length; i != 0; i--) {
```

Total:5

## [G-007] Not using the named return variables when a function returns, wastes deployment gas

### Description:

It is not necessary to have both a named return and a return statement.

### Findings:

[LBFactory.sol#L106](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L106)

```
106:    ) external view override returns (LBPairInformation memory) {
```

[LBFactory.sol#L239](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L239)

```
239:    ) external override returns (ILBPair _LBPair) {
```

[LBFactory.sol#L597](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L597)

```
597:    ) private view returns (LBPairInformation memory) {
```

[LBPair.sol#L251](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L251)

```
251:    function getBin(uint24 _id) external view override returns (uint256 reserveX, uint256 reserveY) {
```

[LBPair.sol#L265](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L265)

```
265:    returns (uint256 amountX, uint256 amountY)
```

[LBPair.sol#L308](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L308)

```
308:    returns (uint256 amountXOut, uint256 amountYOut)
```

[LBPair.sol#L620](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L620)

```
620:    ) external override nonReentrant returns (uint256 amountX, uint256 amountY) {
```

[LBPair.sol#L692](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L692)

```
692:    returns (uint256 amountX, uint256 amountY)
```

[LBPair.sol#L735](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L735)

```
735:    function collectProtocolFees() external override nonReentrant returns (uint256 amountX, uint256 amountY) {
```

[LBPair.sol#L849](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L849)

```
849:    ) private view returns (uint256 amountX, uint256 amountY) {
```

[LBPair.sol#L994](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994)

```
994:    function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {
```

[LBQuoter.sol#L57](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L57)

```
57:    returns (Quote memory quote)
```

[LBQuoter.sol#L106](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L106)

```
106:    returns (uint256 swapAmountOut, uint256 fees) {
```

[LBQuoter.sol#L137](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L137)

```
137:    returns (Quote memory quote)
```

[LBQuoter.sol#L182](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L182)

```
182:    returns (uint256 swapAmountIn, uint256 fees) {
```

[LBQuoter.sol#L221](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L221)

```
221:    ) internal view returns (uint256 reserveA, uint256 reserveB) {
```

[LBQuoter.sol#L238](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L238)

```
238:    ) internal pure returns (uint256 quote) {
```

[LBRouter.sol#L94](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L94)

```
94:    ) public view override returns (uint256 amountIn, uint256 feesIn) {
```

[LBRouter.sol#L152](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L152)

```
152:    ) external view override returns (uint256 amountOut, uint256 feesIn) {
```

[LBRouter.sol#L198](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L198)

```
198:    ) external override returns (ILBPair pair) {
```

[LBRouter.sol#L284](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L284)

```
284:    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {
```

[LBRouter.sol#L317](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L317)

```
317:    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {
```

[LBRouter.sol#L359](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L359)

```
359:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

[LBRouter.sol#L384](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L384)

```
384:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

[LBRouter.sol#L413](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L413)

```
413:    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

[LBRouter.sol#L538](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L538)

```
538:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

[LBRouter.sol#L568](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L568)

```
568:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

[LBRouter.sol#L600](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L600)

```
600:    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

[LBRouter.sol#L748](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L748)

```
748:    ) private returns (uint256 amountX, uint256 amountY) {
```

[LBRouter.sol#L768](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L768)

```
768:    ) private returns (uint256 amountOut) {
```

[LBRouter.sol#L822](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L822)

```
822:    ) private returns (uint256 amountOut) {
```

[LBRouter.sol#L934](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L934)

```
934:    ) private view returns (address _pair) {
```

[LBToken.sol#L49](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49)

```
49:    function name() public pure virtual override returns (string memory) {
```

[LBToken.sol#L55](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55)

```
55:    function symbol() public pure virtual override returns (string memory) {
```

[IJoeFactory.sol#L16](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L16)

```
16:    function getPair(address tokenA, address tokenB) external view returns (address pair);
```

[IJoeFactory.sol#L18](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L18)

```
18:    function allPairs(uint256) external view returns (address pair);
```

[IJoeFactory.sol#L22](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L22)

```
22:    function createPair(address tokenA, address tokenB) external returns (address pair);
```

[IJoePair.sol#L11](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L11)

```
11:    function name() external pure returns (string memory);
```

[IJoePair.sol#L13](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L13)

```
13:    function symbol() external pure returns (string memory);
```

[IJoePair.sol#L84](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L84)

```
84:    function mint(address to) external returns (uint256 liquidity);
```

[IJoePair.sol#L86](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L86)

```
86:    function burn(address to) external returns (uint256 amount0, uint256 amount1);
```

[IJoeRouter01.sol#L53](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L53)

```
53:    ) external returns (uint256 amountA, uint256 amountB);
```

[IJoeRouter01.sol#L62](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L62)

```
62:    ) external returns (uint256 amountToken, uint256 amountAVAX);
```

[IJoeRouter01.sol#L76](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L76)

```
76:    ) external returns (uint256 amountA, uint256 amountB);
```

[IJoeRouter01.sol#L89](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L89)

```
89:    ) external returns (uint256 amountToken, uint256 amountAVAX);
```

[IJoeRouter01.sol#L141](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L141)

```
141:    ) external pure returns (uint256 amountB);
```

[IJoeRouter01.sol#L147](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L147)

```
147:    ) external pure returns (uint256 amountOut);
```

[IJoeRouter01.sol#L153](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol#L153)

```
153:    ) external pure returns (uint256 amountIn);
```

[IJoeRouter02.sol#L17](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter02.sol#L17)

```
17:    ) external returns (uint256 amountAVAX);
```

[IJoeRouter02.sol#L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter02.sol#L30)

```
30:    ) external returns (uint256 amountAVAX);
```

[ILBFactory.sol#L105](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L105)

```
105:    ) external view returns (LBPairInformation memory);
```

[ILBFactory.sol#L135](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L135)

```
135:    ) external returns (ILBPair pair);
```

[ILBPair.sol#L203](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L203)

```
203:    function findFirstNonEmptyBinId(uint24 id_, bool sentTokenY) external view returns (uint24 id);
```

[ILBPair.sol#L205](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L205)

```
205:    function getBin(uint24 id) external view returns (uint256 reserveX, uint256 reserveY);
```

[ILBPair.sol#L210](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L210)

```
210:    returns (uint256 amountX, uint256 amountY);
```

[ILBPair.sol#L212](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L212)

```
212:    function swap(bool sentTokenY, address to) external returns (uint256 amountXOut, uint256 amountYOut);
```

[ILBPair.sol#L238](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L238)

```
238:    ) external returns (uint256 amountX, uint256 amountY);
```

[ILBPair.sol#L242](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L242)

```
242:    function collectFees(address account, uint256[] memory ids) external returns (uint256 amountX, uint256 amountY);
```

[ILBPair.sol#L244](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L244)

```
244:    function collectProtocolFees() external returns (uint256 amountX, uint256 amountY);
```

[ILBRouter.sol#L60](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L60)

```
60:    ) external view returns (uint256 amountIn, uint256 feesIn);
```

[ILBRouter.sol#L66](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L66)

```
66:    ) external view returns (uint256 amountOut, uint256 feesIn);
```

[ILBRouter.sol#L73](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L73)

```
73:    ) external returns (ILBPair pair);
```

[ILBRouter.sol#L94](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L94)

```
94:    ) external returns (uint256 amountX, uint256 amountY);
```

[ILBRouter.sol#L105](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L105)

```
105:    ) external returns (uint256 amountToken, uint256 amountAVAX);
```

[ILBRouter.sol#L114](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L114)

```
114:    ) external returns (uint256 amountOut);
```

[ILBRouter.sol#L123](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L123)

```
123:    ) external returns (uint256 amountOut);
```

[ILBRouter.sol#L131](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L131)

```
131:    ) external payable returns (uint256 amountOut);
```

[ILBRouter.sol#L166](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L166)

```
166:    ) external returns (uint256 amountOut);
```

[ILBRouter.sol#L175](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L175)

```
175:    ) external returns (uint256 amountOut);
```

[ILBRouter.sol#L183](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L183)

```
183:    ) external payable returns (uint256 amountOut);
```

[ILBToken.sol#L21](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol#L21)

```
21:    function name() external view returns (string memory);
```

[ILBToken.sol#L23](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol#L23)

```
23:    function symbol() external view returns (string memory);
```

[BitMath.sol#L39](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L39)

```
39:    function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {
```

[BitMath.sol#L56](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L56)

```
56:    function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {
```

[BitMath.sol#L69](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L69)

```
69:    function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {
```

[BitMath.sol#L108](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L108)

```
108:    function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {
```

[Buffer.sol#L13](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Buffer.sol#L13)

```
13:    function before(uint256 x, uint256 n) internal pure returns (uint256 result) {
```

[Decoder.sol#L19](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Decoder.sol#L19)

```
19:    ) internal pure returns (uint256 value) {
```

[Encoder.sol#L19](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Encoder.sol#L19)

```
19:    ) internal pure returns (bytes32 sample) {
```

[FeeHelper.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L100)

```
100:    function getVariableFee(FeeParameters memory _fp) internal pure returns (uint256 variableFee) {
```

[FeeHelper.sol#L145](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L145)

```
145:    returns (FeesDistribution memory fees)
```

[JoeLibrary.sol#L12](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L12)

```
12:    function sortTokens(address tokenA, address tokenB) internal pure returns (address token0, address token1) {
```

[JoeLibrary.sol#L23](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L23)

```
23:    ) internal pure returns (uint256 amountB) {
```

[JoeLibrary.sol#L34](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L34)

```
34:    ) internal pure returns (uint256 amountOut) {
```

[JoeLibrary.sol#L48](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L48)

```
48:    ) internal pure returns (uint256 amountIn) {
```

[Math128x128.sol#L35](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L35)

```
35:    function log2(uint256 x) internal pure returns (int256 result) {
```

[Math128x128.sol#L94](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L94)

```
94:    function power(uint256 x, int256 y) internal pure returns (uint256 result) {
```

[Math512Bits.sol#L34](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L34)

```
34:    ) internal pure returns (uint256 result) {
```

[Math512Bits.sol#L60](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L60)

```
60:    ) internal pure returns (uint256 result) {
```

[Math512Bits.sol#L96](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L96)

```
96:    ) internal pure returns (uint256 result) {
```

[Math512Bits.sol#L123](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L123)

```
123:    ) internal pure returns (uint256 result) {
```

[Math512Bits.sol#L156](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L156)

```
156:    ) internal pure returns (uint256 result) {
```

[Math512Bits.sol#L168](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L168)

```
168:    function _getMulProds(uint256 x, uint256 y) private pure returns (uint256 prod0, uint256 prod1) {
```

[Math512Bits.sol#L192](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L192)

```
192:    ) private pure returns (uint256 result) {
```

[Oracle.sol#L115](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L115)

```
115:    ) internal returns (uint256 updatedIndex) {
```

[Oracle.sol#L150](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L150)

```
150:    ) private view returns (bytes32 prev, bytes32 next) {
```

[SafeCast.sol#L14](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L14)

```
14:    function safe248(uint256 x) internal pure returns (uint248 y) {
```

[SafeCast.sol#L21](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L21)

```
21:    function safe240(uint256 x) internal pure returns (uint240 y) {
```

[SafeCast.sol#L28](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L28)

```
28:    function safe232(uint256 x) internal pure returns (uint232 y) {
```

[SafeCast.sol#L35](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L35)

```
35:    function safe224(uint256 x) internal pure returns (uint224 y) {
```

[SafeCast.sol#L42](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L42)

```
42:    function safe216(uint256 x) internal pure returns (uint216 y) {
```

[SafeCast.sol#L49](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L49)

```
49:    function safe208(uint256 x) internal pure returns (uint208 y) {
```

[SafeCast.sol#L56](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L56)

```
56:    function safe200(uint256 x) internal pure returns (uint200 y) {
```

[SafeCast.sol#L63](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L63)

```
63:    function safe192(uint256 x) internal pure returns (uint192 y) {
```

[SafeCast.sol#L70](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L70)

```
70:    function safe184(uint256 x) internal pure returns (uint184 y) {
```

[SafeCast.sol#L77](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L77)

```
77:    function safe176(uint256 x) internal pure returns (uint176 y) {
```

[SafeCast.sol#L84](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L84)

```
84:    function safe168(uint256 x) internal pure returns (uint168 y) {
```

[SafeCast.sol#L91](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L91)

```
91:    function safe160(uint256 x) internal pure returns (uint160 y) {
```

[SafeCast.sol#L98](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L98)

```
98:    function safe152(uint256 x) internal pure returns (uint152 y) {
```

[SafeCast.sol#L105](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L105)

```
105:    function safe144(uint256 x) internal pure returns (uint144 y) {
```

[SafeCast.sol#L112](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L112)

```
112:    function safe136(uint256 x) internal pure returns (uint136 y) {
```

[SafeCast.sol#L119](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L119)

```
119:    function safe128(uint256 x) internal pure returns (uint128 y) {
```

[SafeCast.sol#L126](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L126)

```
126:    function safe120(uint256 x) internal pure returns (uint120 y) {
```

[SafeCast.sol#L133](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L133)

```
133:    function safe112(uint256 x) internal pure returns (uint112 y) {
```

[SafeCast.sol#L140](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L140)

```
140:    function safe104(uint256 x) internal pure returns (uint104 y) {
```

[SafeCast.sol#L147](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L147)

```
147:    function safe96(uint256 x) internal pure returns (uint96 y) {
```

[SafeCast.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L154)

```
154:    function safe88(uint256 x) internal pure returns (uint88 y) {
```

[SafeCast.sol#L161](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L161)

```
161:    function safe80(uint256 x) internal pure returns (uint80 y) {
```

[SafeCast.sol#L168](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L168)

```
168:    function safe72(uint256 x) internal pure returns (uint72 y) {
```

[SafeCast.sol#L175](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L175)

```
175:    function safe64(uint256 x) internal pure returns (uint64 y) {
```

[SafeCast.sol#L182](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L182)

```
182:    function safe56(uint256 x) internal pure returns (uint56 y) {
```

[SafeCast.sol#L189](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L189)

```
189:    function safe48(uint256 x) internal pure returns (uint48 y) {
```

[SafeCast.sol#L196](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L196)

```
196:    function safe40(uint256 x) internal pure returns (uint40 y) {
```

[SafeCast.sol#L203](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L203)

```
203:    function safe32(uint256 x) internal pure returns (uint32 y) {
```

[SafeCast.sol#L210](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L210)

```
210:    function safe24(uint256 x) internal pure returns (uint24 y) {
```

[SafeCast.sol#L217](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L217)

```
217:    function safe16(uint256 x) internal pure returns (uint16 y) {
```

[SafeCast.sol#L224](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#L224)

```
224:    function safe8(uint256 x) internal pure returns (uint8 y) {
```

[Samples.sol#L45](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol#L45)

```
45:    ) internal view returns (bytes32 packedSample) {
```

[Samples.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol#L75)

```
75:    ) internal pure returns (bytes32 packedSample) {
```

Total:129

## [G-008] Use Assembly to check for address(0)

### Description:

Saves 6 gas per instance if using assemlby to check for address(0)

### Findings:

[LBFactory.sol#L255](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L255)

```
255:    if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))
```

[LBPair.sol#L112](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L112)

```
112:    if (address(tokenX) != address(0)) revert LBPair__AlreadyInitialized();
```

[LBPair.sol#L820](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L820)

```
820:    if (_from != address(0) && _from != address(this)) {
```

[LBPair.sol#L826](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L826)

```
826:    if (_to != address(0) && _to != address(this)) {
```

[LBQuoter.sol#L79](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L79)

```
79:    if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
```

[LBQuoter.sol#L157](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L157)

```
157:    if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
```

[PendingOwnable.sol#L61](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L61)

```
61:    if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();
```

Total:7

## [G-009] Using storage instead of memory for structs/arrays saves gas

### Description:

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Goldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

### Findings:

[LBFactory.sol#L197](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L197)

```
197:    LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];
```

[LBFactory.sol#L320](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L320)

```
320:    LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
```

[LBPair.sol#L220](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L220)

```
220:    FeeHelper.FeeParameters memory _fp = _feeParameters;
```

[LBPair.sol#L283](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L283)

```
283:    Bin memory _bin = _bins[_id];
```

[LBPair.sol#L310](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L310)

```
310:    PairInformation memory _pair = _pairInformation;
```

[LBPair.sol#L318](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L318)

```
318:    FeeHelper.FeeParameters memory _fp = _feeParameters;
```

[LBPair.sol#L327](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L327)

```
327:    Bin memory _bin = _bins[_pair.activeId];
```

[LBPair.sol#L426](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L426)

```
426:    FeeHelper.FeeParameters memory _fp = _feeParameters;
```

[LBPair.sol#L430](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L430)

```
430:    FeeHelper.FeesDistribution memory _feesX = _fp.getFeeAmountDistribution(_getFlashLoanFee(_amountXOut, _fee));
```

[LBPair.sol#L431](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L431)

```
431:    FeeHelper.FeesDistribution memory _feesY = _fp.getFeeAmountDistribution(_getFlashLoanFee(_amountYOut, _fee));
```

[LBPair.sol#L484](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L484)

```
484:    PairInformation memory _pair = _pairInformation;
```

[LBPair.sol#L486](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L486)

```
486:    FeeHelper.FeeParameters memory _fp = _feeParameters;
```

[LBPair.sol#L498](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L498)

```
498:    Bin memory _bin = _bins[_mintInfo.id];
```

[LBPair.sol#L535](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L535)

```
535:    FeeHelper.FeesDistribution memory _fees = _fp.getFeeAmountDistribution(
```

[LBPair.sol#L546](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L546)

```
546:    FeeHelper.FeesDistribution memory _fees = _fp.getFeeAmountDistribution(
```

[LBPair.sol#L706](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L706)

```
706:    Bin memory _bin = _bins[_id];
```

[LBPair.sol#L817](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L817)

```
817:    Bin memory _bin = _bins[_id];
```

[LBPair.sol#L850](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L850)

```
850:    Debts memory _debts = _accruedDebts[_account][_id];
```

[LBQuoter.sol#L94](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L94)

```
94:    ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(
```

[LBQuoter.sol#L171](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L171)

```
171:    ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(
```

[LBRouter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L100)

```
100:    FeeHelper.FeeParameters memory _fp = _LBPair.feeParameters();
```

[LBRouter.sol#L155](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L155)

```
155:    FeeHelper.FeeParameters memory _fp = _LBPair.feeParameters();
```

[LBRouter.sol#L360](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L360)

```
360:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L388](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L388)

```
388:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L414](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L414)

```
414:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L439](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L439)

```
439:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L470](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L470)

```
470:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L509](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L509)

```
509:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L539](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L539)

```
539:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L572](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L572)

```
572:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

[LBRouter.sol#L603](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L603)

```
603:    address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
```

Total:31

## [G-010] internal function only called once can be inlined to save gas

### Description:

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls. (**Need to recheck all Wrapped lines internal function name in this file (Match 2 times)**)

### Findings:

[LBPair.sol#L994](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994)

```
994:    function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {
```

[LBToken.sol#L269](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L269)

```
269:    function _isApprovedForAll(address _owner, address _spender) internal view virtual returns (bool) {
```

[BinHelper.sol#L51](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol#L51)

```
51:    function _getBPValue(uint256 _binStep) internal pure returns (uint256) {
```

[ReentrancyGuardUpgradeable.sol#L27](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol#L27)

```
27:    function __ReentrancyGuard_init() internal {
```

Total:4

## [G-011] internal function not called by the contract should be removed to save deployment gas

### Description:

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.(**Need to recheck all Wrapped lines internal function name in this file (Match 1 times)**)

### Findings:

[LBFactory.sol#L510](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L510)

```
510:    function _setFeeRecipient(address _feeRecipient) internal {
```

[LBPair.sol#L905](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L905)

```
905:    function _setFeesParameters(bytes32 _packedFeeParameters) internal {
```

[LBPair.sol#L1044](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1044)

```
1044:    function _getFlashLoanFee(uint256 _amount, uint256 _fee) internal pure returns (uint256) {
```

[PendingOwnable.sol#L91](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L91)

```
91:    function _transferOwnership(address _newOwner) internal virtual {
```

[PendingOwnable.sol#L101](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L101)

```
101:    function _setPendingOwner(address pendingOwner_) internal virtual {
```

[ReentrancyGuardUpgradeable.sol#L31](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol#L31)

```
31:    function __ReentrancyGuard_init_unchained() internal {
```

Total:6