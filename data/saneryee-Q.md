## [NC-001]Adding a return statement to a function that returns a value is redundant

### Description:

It is not necessary to have both a named return and a return statement.

### Findings:

Total:129

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

## [L-001] `decimals()` not part of ERC20 standard

### Description:

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

### Findings:

Total:1

[IJoePair.sol#L15](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L15)

```
15:    function decimals() external pure returns (uint8);
```

## [L-002] `approve` should be replaced with `safeIncreaseAllowance()` or `safeDecreaseAllowance()`

### Description:

`approve` is subject to a known front-running attack. Consider using `safeIncreaseAllownce()` or `safeDecreaseAllowance()` instead

### Findings:

Total:1

[IJoePair.sol#L23](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L23)

```
23:    function approve(address spender, uint256 value) external returns (bool);
```

## [L-003] Events not emitted for important state changes / Missing event for critical parameter changes

### Description:

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

### Findings:

Total:23

[LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215)

```
215:    function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
```

[LBFactory.sol#L312](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L312)

```
312:    function setLBPairIgnored(
```

[LBFactory.sol#L340](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L340)

```
340:    function setPreset(
```

[LBFactory.sol#L423](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L423)

```
423:    function setFeesParametersOnPair(
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

[LBPair.sol#L788](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L788)

```
788:    function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
```

[LBToken.sol#L122](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122)

```
122:    function setApprovalForAll(address _spender, bool _approved) public virtual override {
```

[IJoeFactory.sol#L24](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L24)

```
24:    function setFeeTo(address) external;
```

[IJoeFactory.sol#L26](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L26)

```
26:    function setFeeToSetter(address) external;
```

[IJoeFactory.sol#L28](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L28)

```
28:    function setMigrator(address) external;
```

[ILBFactory.sol#L128](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L128)

```
128:    function setLBPairImplementation(address LBPairImplementation) external;
```

[ILBFactory.sol#L137](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L137)

```
137:    function setLBPairIgnored(
```

[ILBFactory.sol#L144](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L144)

```
144:    function setPreset(
```

[ILBFactory.sol#L158](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L158)

```
158:    function setFeesParametersOnPair(
```

[ILBFactory.sol#L171](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L171)

```
171:    function setFeeRecipient(address feeRecipient) external;
```

[ILBFactory.sol#L173](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L173)

```
173:    function setFlashLoanFee(uint256 flashLoanFee) external;
```

[ILBFactory.sol#L175](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L175)

```
175:    function setFactoryLockedState(bool locked) external;
```

[ILBPair.sol#L246](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L246)

```
246:    function setFeesParameters(bytes32 packedFeeParameters) external;
```

[ILBToken.sol#L40](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol#L40)

```
40:    function setApprovalForAll(address sender, bool approved) external;
```

[IPendingOwnable.sol#L16](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IPendingOwnable.sol#L16)

```
16:    function setPendingOwner(address pendingOwner) external;
```

[PendingOwnable.sol#L59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L59)

```
59:    function setPendingOwner(address pendingOwner_) public override onlyOwner {
```
