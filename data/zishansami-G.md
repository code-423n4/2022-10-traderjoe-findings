## Gas Optimizations
 *** 


### G-01: pre-increment `++i/--i` costs less gas than post-increment `i++/i--`
Saves 6 gas per loop in a for loop

Total instances of this issue: 3

```solidity
src/LBRouter.sol

711:        for (uint256 i = _pairs.length; i != 0; i--) {


```
```solidity
src/LBQuoter.sol

75:        for (uint256 i; i < swapLength; i++) {

154:        for (uint256 i = swapLength; i > 0; i--) {


```
 *** 


### G-02: `++i/i++` should be placed in unchecked blocks to save gas as it is impossible for them to overflow in for and while loops
Unchecked keyword is available in solidity version `0.8.0` or higher and can be applied to iterator variables to save gas. 
Saves more than `30 gas` per loop.

Total instances of this issue: 7

```solidity
src/LBFactory.sol

165:                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

195:                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {


```
```solidity
src/LBPair.sol

701:            for (uint256 i; i < _ids.length; ++i) {


```
```solidity
src/LBRouter.sol

674:            for (uint256 i; i < depositIds.length; ++i) {

711:        for (uint256 i = _pairs.length; i != 0; i--) {


```
```solidity
src/LBQuoter.sol

75:        for (uint256 i; i < swapLength; i++) {

154:        for (uint256 i = swapLength; i > 0; i--) {


```
 *** 


### G-03: Length of the array (`<array>.length`) need not be looked up in every iteration of a for-loop
Reading array length at each iteration of the loop takes total 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the `array.length` saves around `3 gas` per iteration.

Total instances of this issue: 14

```solidity
src/LBPair.sol

274:            for (uint256 i; i < _ids.length; ++i) {

496:            for (uint256 i; i < _ids.length; ++i) {

623:            for (uint256 i; i < _ids.length; ++i) {

701:            for (uint256 i; i < _ids.length; ++i) {


```
```solidity
src/LBRouter.sol

674:            for (uint256 i; i < depositIds.length; ++i) {

711:        for (uint256 i = _pairs.length; i != 0; i--) {

778:            for (uint256 i; i < _pairs.length; ++i) {

831:            for (uint256 i; i < _pairs.length; ++i) {

878:            for (uint256 i; i < _pairs.length; ++i) {

951:            for (uint256 i; i < pairs.length; ++i) {


```
```solidity
src/LBToken.sol

90:            for (uint256 i; i < _accounts.length; ++i) {

163:            for (uint256 i; i < _ids.length; ++i) {


```
```solidity
src/LBQuoter.sol

100:                for (uint256 j; j < LBPairsAvailable.length; j++) {

177:                for (uint256 j; j < LBPairsAvailable.length; j++) {


```
 *** 


### G-04: `x += y` costs more gas than `x = x + y` for state variables

Total instances of this issue: 59

```solidity
src/LBFactory.sol

143:        baseFactor = _preset.decode(type(uint16).max, _shift += 16);

144:        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);

145:        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);

146:        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);

147:        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);

148:        protocolShare = _preset.decode(type(uint16).max, _shift += 24);

149:        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);


```
```solidity
src/LBPair.sol

227:                cumulativeId += _activeId * _deltaT;

228:                cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;

287:                    amountX += _amountX;

288:                    amountY += _amountY;

337:                _amountOut += _amountOutOfBin;

452:        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);

453:        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);

505:                if (
506:                    _mintInfo.distributionX > Constants.PRECISION ||
507:                    _mintInfo.distributionY > Constants.PRECISION ||
508:                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||
509:                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION
510:                ) revert LBPair__DistributionsOverflow();

540:                            _mintInfo.activeFeeX += _fees.total;

551:                            _mintInfo.activeFeeY += _fees.total;

572:                _pair.reserveX += uint112(_mintInfo.amountX);

573:                _pair.reserveY += uint112(_mintInfo.amountY);

575:                _mintInfo.amountXAddedToPair += _mintInfo.amountX;

576:                _mintInfo.amountYAddedToPair += _mintInfo.amountY;

639:                    amountY += _amountY;

646:                    amountX += _amountX;

711:                    amountX += _amountX;

712:                    amountY += _amountY;

896:            (amountX += _amountX).safe128();

897:            (amountY += _amountY).safe128();


```
```solidity
src/LBRouter.sol

128:                amountIn += _amountInWithFees;

129:                feesIn += _fee;

174:                feesIn += _fees.total;

175:                amountOut += _amountOutOfBin;


```
```solidity
src/LBToken.sol

211:        _totalSupplies[_id] += _amount;


```
```solidity
src/libraries/Math512Bits.sol

71:                result += prod1 << (256 - offset);

99:            if (mulmod(x, y, 1 << offset) != 0) result += 1;

159:            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;


```
```solidity
src/libraries/FeeDistributionHelper.sol

42:            _pairFees.protocol += _fees.protocol;


```
```solidity
src/libraries/BitMath.sol

77:                msb += 64;

81:                msb += 32;

85:                msb += 16;

89:                msb += 8;

93:                msb += 4;

97:                msb += 2;

100:                msb += 1;

116:                lsb += 64;

120:                lsb += 32;

124:                lsb += 16;

128:                lsb += 8;

132:                lsb += 4;

136:                lsb += 2;

139:                lsb += 1;


```
```solidity
src/libraries/SwapHelper.sol

88:        pairFees.total += fees.total;

91:            pairFees.protocol += fees.protocol;

95:            bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);

97:            bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);

115:            bin.reserveX += amountInToBin;

119:                pair.reserveX += uint136(amountInToBin);

123:            bin.reserveY += amountInToBin;

128:                pair.reserveY += uint136(amountInToBin);


```
```solidity
src/libraries/Math128x128.sol

77:                        result += delta;


```
 *** 


### G-05: No need to use SafeMath if the solidity version is 0.8.0 or higher
Solidity version 0.8.0 or higher has internal overflow/underflow checks, so using SafeMath adds overhead

Total instances of this issue: 7

```solidity
src/LBPair.sol

17:import "./libraries/SafeMath.sol";

33:    using SafeMath for uint256;


```
```solidity
src/libraries/SafeMath.sol

8:library SafeMath {


```
```solidity
src/libraries/SwapHelper.sol

10:import "./SafeMath.sol";

19:    using SafeMath for uint256;


```
```solidity
src/libraries/FeeHelper.sol

7:import "./SafeMath.sol";

14:    using SafeMath for uint256;


```
 *** 


### G-06: Adding `payable` to functions which are only meant to be called by specific actors like `onlyOwner` will save gas cost
Marking functions payable removes additional checks for whether a payment was provided, hence reducing gas cost

Total instances of this issue: 19

```solidity
src/LBFactory.sol

215:    function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {

312:    function setLBPairIgnored(
313:        IERC20 _tokenX,
314:        IERC20 _tokenY,
315:        uint256 _binStep,
316:        bool _ignored
317:    ) external override onlyOwner {

340:    function setPreset(
341:        uint16 _binStep,
342:        uint16 _baseFactor,
343:        uint16 _filterPeriod,
344:        uint16 _decayPeriod,
345:        uint16 _reductionFactor,
346:        uint24 _variableFeeControl,
347:        uint16 _protocolShare,
348:        uint24 _maxVolatilityAccumulated,
349:        uint16 _sampleLifetime
350:    ) external override onlyOwner {

396:    function removePreset(uint16 _binStep) external override onlyOwner {

423:    function setFeesParametersOnPair(
424:        IERC20 _tokenX,
425:        IERC20 _tokenY,
426:        uint16 _binStep,
427:        uint16 _baseFactor,
428:        uint16 _filterPeriod,
429:        uint16 _decayPeriod,
430:        uint16 _reductionFactor,
431:        uint24 _variableFeeControl,
432:        uint16 _protocolShare,
433:        uint24 _maxVolatilityAccumulated
434:    ) external override onlyOwner {

468:    function setFeeRecipient(address _feeRecipient) external override onlyOwner {

474:    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

485:    function setFactoryLockedState(bool _locked) external override onlyOwner {

493:    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

502:    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

520:    function forceDecay(ILBPair _LBPair) external override onlyOwner {


```
```solidity
src/LBPair.sol

104:    function initialize(
105:        IERC20 _tokenX,
106:        IERC20 _tokenY,
107:        uint24 _activeId,
108:        uint16 _sampleLifetime,
109:        bytes32 _packedFeeParameters
110:    ) external override onlyFactory {

788:    function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {

792:    function forceDecay() external override onlyFactory {


```
```solidity
src/LBRouter.sol

622:    function sweep(
623:        IERC20 _token,
624:        address _to,
625:        uint256 _amount
626:    ) external override onlyFactoryOwner {

642:    function sweepLBToken(
643:        ILBToken _lbToken,
644:        address _to,
645:        uint256[] memory _ids,
646:        uint256[] memory _amounts
647:    ) external override onlyFactoryOwner {


```
```solidity
src/libraries/PendingOwnable.sol

59:    function setPendingOwner(address pendingOwner_) public override onlyOwner {

68:    function revokePendingOwner() public override onlyOwner {

84:    function renounceOwnership() public override onlyOwner {


```
 *** 


### G-07: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 78

```solidity
src/LBErrors.sol

57:error LBFactory__DecreasingPeriods(uint16 filterPeriod, uint16 decayPeriod);

58:error LBFactory__BaseFactorOverflows(uint16 baseFactor, uint256 max);

59:error LBFactory__ReductionFactorOverflows(uint16 reductionFactor, uint256 max);

60:error LBFactory__VariableFeeControlOverflows(uint16 variableFeeControl, uint256 max);

63:error LBFactory__BinStepRequirementsBreached(uint256 lowerBound, uint16 binStep, uint256 higherBound);

64:error LBFactory__ProtocolShareOverflows(uint16 protocolShare, uint256 max);


```
```solidity
src/LBFactory.sol

120:    function getPreset(uint16 _binStep)
121:        external
122:        view
123:        override
124:        returns (
125:            uint256 baseFactor,
126:            uint256 filterPeriod,
127:            uint256 decayPeriod,
128:            uint256 reductionFactor,
129:            uint256 variableFeeControl,
130:            uint256 protocolShare,
131:            uint256 maxVolatilityAccumulated,
132:            uint256 sampleLifetime
133:        )
134:    {

234:    function createLBPair(
235:        IERC20 _tokenX,
236:        IERC20 _tokenY,
237:        uint24 _activeId,
238:        uint16 _binStep
239:    ) external override returns (ILBPair _LBPair) {

340:    function setPreset(
341:        uint16 _binStep,
342:        uint16 _baseFactor,
343:        uint16 _filterPeriod,
344:        uint16 _decayPeriod,
345:        uint16 _reductionFactor,
346:        uint24 _variableFeeControl,
347:        uint16 _protocolShare,
348:        uint24 _maxVolatilityAccumulated,
349:        uint16 _sampleLifetime
350:    ) external override onlyOwner {

396:    function removePreset(uint16 _binStep) external override onlyOwner {

423:    function setFeesParametersOnPair(
424:        IERC20 _tokenX,
425:        IERC20 _tokenY,
426:        uint16 _binStep,
427:        uint16 _baseFactor,
428:        uint16 _filterPeriod,
429:        uint16 _decayPeriod,
430:        uint16 _reductionFactor,
431:        uint24 _variableFeeControl,
432:        uint16 _protocolShare,
433:        uint24 _maxVolatilityAccumulated
434:    ) external override onlyOwner {

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
```solidity
src/LBPair.sol

104:    function initialize(
105:        IERC20 _tokenX,
106:        IERC20 _tokenY,
107:        uint24 _activeId,
108:        uint16 _sampleLifetime,
109:        bytes32 _packedFeeParameters
110:    ) external override onlyFactory {

243:    function findFirstNonEmptyBinId(uint24 _id, bool _swapForY) external view override returns (uint24) {

251:    function getBin(uint24 _id) external view override returns (uint256 reserveX, uint256 reserveY) {

624:                uint24 _id = _ids[i].safe24();

679:    function increaseOracleLength(uint16 _newSize) external override {

921:    function _increaseOracle(uint16 _newSize) private {

994:    function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {


```
```solidity
src/LBRouter.sol

80:    function getPriceFromId(ILBPair _LBPair, uint24 _id) external view override returns (uint256) {

193:    function createLBPair(
194:        IERC20 _tokenX,
195:        IERC20 _tokenY,
196:        uint24 _activeId,
197:        uint16 _binStep
198:    ) external override returns (ILBPair pair) {

274:    function removeLiquidity(
275:        IERC20 _tokenX,
276:        IERC20 _tokenY,
277:        uint16 _binStep,
278:        uint256 _amountXMin,
279:        uint256 _amountYMin,
280:        uint256[] memory _ids,
281:        uint256[] memory _amounts,
282:        address _to,
283:        uint256 _deadline
284:    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {

308:    function removeLiquidityAVAX(
309:        IERC20 _token,
310:        uint16 _binStep,
311:        uint256 _amountTokenMin,
312:        uint256 _amountAVAXMin,
313:        uint256[] memory _ids,
314:        uint256[] memory _amounts,
315:        address payable _to,
316:        uint256 _deadline
317:    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {


```
```solidity
src/libraries/FeeDistributionHelper.sol

31:        uint128 _totalFees = _pairFees.total;


```
```solidity
src/libraries/BitMath.sol

14:    function closestBit(
15:        uint256 _integer,
16:        uint8 _bit,
17:        bool _rightSide
18:    ) internal pure returns (uint256) {

39:    function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {

56:    function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {

69:    function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {

108:    function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {


```
```solidity
src/libraries/SwapHelper.sol

107:    function updateReserves(
108:        ILBPair.Bin memory bin,
109:        ILBPair.PairInformation memory pair,
110:        bool swapForY,
111:        uint112 amountInToBin,
112:        uint112 amountOutOfBin
113:    ) internal pure {


```
```solidity
src/libraries/FeeHelper.sol

30:        uint16 binStep;

31:        uint16 baseFactor;

32:        uint16 filterPeriod;

33:        uint16 decayPeriod;

34:        uint16 reductionFactor;

35:        uint24 variableFeeControl;

36:        uint16 protocolShare;

37:        uint24 maxVolatilityAccumulated;

38:        uint24 volatilityAccumulated;

39:        uint24 volatilityReference;

40:        uint24 indexRef;

41:        uint40 time;

48:        uint128 total;

49:        uint128 protocol;


```
```solidity
src/libraries/TreeMath.sol

21:    function findFirstBin(
22:        mapping(uint256 => uint256)[3] storage _tree,
23:        uint24 _binId,
24:        bool _rightSide
25:    ) internal view returns (uint24) {

104:    function _getIdsFromAbove(uint24 _id) private pure returns (uint24, uint24) {

113:    function _getBottomId(uint24 _branchId, uint24 _leafId) private pure returns (uint24) {


```
```solidity
src/libraries/SafeCast.sol

14:    function safe248(uint256 x) internal pure returns (uint248 y) {

21:    function safe240(uint256 x) internal pure returns (uint240 y) {

28:    function safe232(uint256 x) internal pure returns (uint232 y) {

35:    function safe224(uint256 x) internal pure returns (uint224 y) {

42:    function safe216(uint256 x) internal pure returns (uint216 y) {

49:    function safe208(uint256 x) internal pure returns (uint208 y) {

56:    function safe200(uint256 x) internal pure returns (uint200 y) {

63:    function safe192(uint256 x) internal pure returns (uint192 y) {

70:    function safe184(uint256 x) internal pure returns (uint184 y) {

77:    function safe176(uint256 x) internal pure returns (uint176 y) {

84:    function safe168(uint256 x) internal pure returns (uint168 y) {

91:    function safe160(uint256 x) internal pure returns (uint160 y) {

98:    function safe152(uint256 x) internal pure returns (uint152 y) {

105:    function safe144(uint256 x) internal pure returns (uint144 y) {

112:    function safe136(uint256 x) internal pure returns (uint136 y) {

119:    function safe128(uint256 x) internal pure returns (uint128 y) {

126:    function safe120(uint256 x) internal pure returns (uint120 y) {

133:    function safe112(uint256 x) internal pure returns (uint112 y) {

140:    function safe104(uint256 x) internal pure returns (uint104 y) {

147:    function safe96(uint256 x) internal pure returns (uint96 y) {

154:    function safe88(uint256 x) internal pure returns (uint88 y) {

161:    function safe80(uint256 x) internal pure returns (uint80 y) {

168:    function safe72(uint256 x) internal pure returns (uint72 y) {

175:    function safe64(uint256 x) internal pure returns (uint64 y) {

182:    function safe56(uint256 x) internal pure returns (uint56 y) {

189:    function safe48(uint256 x) internal pure returns (uint48 y) {

196:    function safe40(uint256 x) internal pure returns (uint40 y) {

203:    function safe32(uint256 x) internal pure returns (uint32 y) {

210:    function safe24(uint256 x) internal pure returns (uint24 y) {

217:    function safe16(uint256 x) internal pure returns (uint16 y) {

224:    function safe8(uint256 x) internal pure returns (uint8 y) {


```
