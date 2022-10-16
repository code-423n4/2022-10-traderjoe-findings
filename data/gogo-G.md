# 2022-10-TRADERJOE

## Gas Optimizations Report

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.

This change would save up to 6 gas per instance/loop.

_There are **5** instances of this issue:_

```solidity
File: src/LBQuoter.sol

75:    for (uint256 i; i < swapLength; i++) {

100:          for (uint256 j; j < LBPairsAvailable.length; j++) {

154:  for (uint256 i = swapLength; i > 0; i--) {

177:          for (uint256 j; j < LBPairsAvailable.length; j++) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```solidity
File: src/LBRouter.sol

711:  for (uint256 i = _pairs.length; i != 0; i--) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

### `internal` and `private` functions that are called only once should be inlined.

The execution of a non-inlined function would cost up to 40 more gas because of two extra `jump`s as well as some other instructions.

_There are **3** instances of this issue:_

```solidity
File: src/LBRouter.sol

930:  function _getPair(

941:  function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```solidity
File: src/libraries/Oracle.sol

145:  function binarySearch(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **5** instances of this issue:_

```solidity
File: src/LBFactory.sol

161:      if (_nbPresets > 0) {

191:      if (_nbAvailable > 0) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBQuoter.sol

82:            if (reserveIn > 0 && reserveOut > 0) {

154:  for (uint256 i = swapLength; i > 0; i--) {

160:          if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **1** instances of this issue:_

```solidity
File: src/libraries/Samples.sol

19:   uint256 private constant _OFFSET_INITIALIZED = 0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol

### Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **4** instances of this issue:_

```solidity
File: src/LBFactory.sol

25:   uint256 public constant override MAX_FEE = 0.1e18;

27:   uint256 public constant override MIN_BIN_STEP = 1;

28:   uint256 public constant override MAX_BIN_STEP = 100;

30:   uint256 public constant override MAX_PROTOCOL_SHARE = 2_500;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **11** instances of this issue:_

```solidity
File: src/LBFactory.sol

215:  function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {

396:  function removePreset(uint16 _binStep) external override onlyOwner {

468:  function setFeeRecipient(address _feeRecipient) external override onlyOwner {

474:  function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

485:  function setFactoryLockedState(bool _locked) external override onlyOwner {

493:  function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

502:  function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

520:  function forceDecay(ILBPair _LBPair) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/libraries/PendingOwnable.sol

59:   function setPendingOwner(address pendingOwner_) public override onlyOwner {

68:   function revokePendingOwner() public override onlyOwner {

84:   function renounceOwnership() public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **17** instances of this issue:_

```solidity
File: src/LBFactory.sol

165:          for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

195:          for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBPair.sol

274:      for (uint256 i; i < _ids.length; ++i) {

496:      for (uint256 i; i < _ids.length; ++i) {

623:      for (uint256 i; i < _ids.length; ++i) {

701:      for (uint256 i; i < _ids.length; ++i) {

929:      for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```solidity
File: src/LBQuoter.sol

75:    for (uint256 i; i < swapLength; i++) {

100:          for (uint256 j; j < LBPairsAvailable.length; j++) {

177:          for (uint256 j; j < LBPairsAvailable.length; j++) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```solidity
File: src/LBRouter.sol

674:      for (uint256 i; i < depositIds.length; ++i) {

778:      for (uint256 i; i < _pairs.length; ++i) {

831:      for (uint256 i; i < _pairs.length; ++i) {

878:      for (uint256 i; i < _pairs.length; ++i) {

951:      for (uint256 i; i < pairs.length; ++i) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```solidity
File: src/LBToken.sol

90:        for (uint256 i; i < _accounts.length; ++i) {

163:      for (uint256 i; i < _ids.length; ++i) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

### `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

_There are **2** instances of this issue:_

```solidity
File: src/LBToken.sol

211:  _totalSupplies[_id] += _amount;

241:      _totalSupplies[_id] -= _amount;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **87** instances of this issue:_

```solidity
File: src/LBErrors.sol

57:   error LBFactory__DecreasingPeriods(uint16 filterPeriod, uint16 decayPeriod);

58:   error LBFactory__BaseFactorOverflows(uint16 baseFactor, uint256 max);

59:   error LBFactory__ReductionFactorOverflows(uint16 reductionFactor, uint256 max);

60:   error LBFactory__VariableFeeControlOverflows(uint16 variableFeeControl, uint256 max);

63:   error LBFactory__BinStepRequirementsBreached(uint256 lowerBound, uint16 binStep, uint256 higherBound);

64:   error LBFactory__ProtocolShareOverflows(uint16 protocolShare, uint256 max);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol

```solidity
File: src/LBFactory.sol

120:  function getPreset(uint16 _binStep)

238:  uint16 _binStep

341:  uint16 _binStep,

342:  uint16 _baseFactor,

343:  uint16 _filterPeriod,

344:  uint16 _decayPeriod,

345:  uint16 _reductionFactor,

347:  uint16 _protocolShare,

349:  uint16 _sampleLifetime

396:  function removePreset(uint16 _binStep) external override onlyOwner {

426:  uint16 _binStep,

427:  uint16 _baseFactor,

428:  uint16 _filterPeriod,

429:  uint16 _decayPeriod,

430:  uint16 _reductionFactor,

432:  uint16 _protocolShare,

534:  uint16 _binStep,

535:  uint16 _baseFactor,

536:  uint16 _filterPeriod,

537:  uint16 _decayPeriod,

538:  uint16 _reductionFactor,

540:  uint16 _protocolShare,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBPair.sol

108:  uint16 _sampleLifetime,

679:  function increaseOracleLength(uint16 _newSize) external override {

921:  function _increaseOracle(uint16 _newSize) private {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```solidity
File: src/LBRouter.sol

197:  uint16 _binStep

277:  uint16 _binStep,

310:  uint16 _binStep,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```solidity
File: src/interfaces/IJoePair.sol

44:    uint8 v,

75:        uint32 blockTimestampLast
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol

```solidity
File: src/interfaces/IJoeRouter01.sol

73:    uint8 v,

86:    uint8 v,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeRouter01.sol

```solidity
File: src/interfaces/IJoeRouter02.sol

27:    uint8 v,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeRouter02.sol

```solidity
File: src/interfaces/ILBFactory.sol

107:  function getPreset(uint16 binStep)

134:  uint16 binStep

145:  uint16 binStep,

146:  uint16 baseFactor,

147:  uint16 filterPeriod,

148:  uint16 decayPeriod,

149:  uint16 reductionFactor,

151:  uint16 protocolShare,

153:  uint16 sampleLifetime

156:  function removePreset(uint16 binStep) external;

161:  uint16 binStep,

162:  uint16 baseFactor,

163:  uint16 filterPeriod,

164:  uint16 decayPeriod,

165:  uint16 reductionFactor,

167:  uint16 protocolShare,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBFactory.sol

```solidity
File: src/interfaces/ILBPair.sol

43:    uint16 oracleSampleLifetime;

44:    uint16 oracleSize;

45:    uint16 oracleActiveSize;

47:    uint16 oracleId;

64:    uint128 tokenX;

65:    uint128 tokenY;

240:  function increaseOracleLength(uint16 newSize) external;

254:  uint16 sampleLifetime,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol

```solidity
File: src/interfaces/ILBRouter.sol

72:    uint16 binStep

87:    uint16 binStep,

98:    uint16 binStep,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBRouter.sol

```solidity
File: src/libraries/BitMath.sol

16:    uint8 _bit,

39:   function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {

56:   function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {

69:   function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {

108:  function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol

```solidity
File: src/libraries/FeeDistributionHelper.sol

31:    uint128 _totalFees = _pairFees.total;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol

```solidity
File: src/libraries/FeeHelper.sol

30:    uint16 binStep;

31:    uint16 baseFactor;

32:    uint16 filterPeriod;

33:    uint16 decayPeriod;

34:    uint16 reductionFactor;

36:    uint16 protocolShare;

48:    uint128 total;

49:    uint128 protocol;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol

```solidity
File: src/libraries/SafeCast.sol

91:   function safe160(uint256 x) internal pure returns (uint160 y) {

119:  function safe128(uint256 x) internal pure returns (uint128 y) {

140:  function safe104(uint256 x) internal pure returns (uint104 y) {

175:  function safe64(uint256 x) internal pure returns (uint64 y) {

203:  function safe32(uint256 x) internal pure returns (uint32 y) {

217:  function safe16(uint256 x) internal pure returns (uint16 y) {

224:  function safe8(uint256 x) internal pure returns (uint8 y) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **79** instances of this issue:_

```solidity
File: src/LBPair.sol

261:  function pendingFees(address _account, uint256[] memory _ids)

467:  uint256[] memory _ids,

468:  uint256[] memory _distributionX,

469:  uint256[] memory _distributionY,

617:  uint256[] memory _ids,

618:  uint256[] memory _amounts,

688:  function collectFees(address _account, uint256[] memory _ids)

845:  Bin memory _bin,

862:  Bin memory _bin,

881:  Bin memory _bin,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```solidity
File: src/LBQuoter.sol

54:   function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)

134:  function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```solidity
File: src/LBRouter.sol

207:  function addLiquidity(LiquidityParameters memory _liquidityParameters)

230:  function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)

280:  uint256[] memory _ids,

281:  uint256[] memory _amounts,

313:  uint256[] memory _ids,

314:  uint256[] memory _amounts,

355:  uint256[] memory _pairBinSteps,

356:  IERC20[] memory _tokenPath,

380:  uint256[] memory _pairBinSteps,

381:  IERC20[] memory _tokenPath,

409:  uint256[] memory _pairBinSteps,

410:  IERC20[] memory _tokenPath,

434:  uint256[] memory _pairBinSteps,

435:  IERC20[] memory _tokenPath,

462:  uint256[] memory _pairBinSteps,

463:  IERC20[] memory _tokenPath,

495:  uint256[] memory _pairBinSteps,

496:  IERC20[] memory _tokenPath,

534:  uint256[] memory _pairBinSteps,

535:  IERC20[] memory _tokenPath,

564:  uint256[] memory _pairBinSteps,

565:  IERC20[] memory _tokenPath,

596:  uint256[] memory _pairBinSteps,

597:  IERC20[] memory _tokenPath,

645:  uint256[] memory _ids,

646:  uint256[] memory _amounts

656:  function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair)

702:  uint256[] memory _pairBinSteps,

703:  address[] memory _pairs,

704:  IERC20[] memory _tokenPath,

745:  uint256[] memory _ids,

746:  uint256[] memory _amounts,

764:  address[] memory _pairs,

765:  uint256[] memory _pairBinSteps,

766:  IERC20[] memory _tokenPath,

817:  address[] memory _pairs,

818:  uint256[] memory _pairBinSteps,

819:  IERC20[] memory _tokenPath,

820:  uint256[] memory _amountsIn,

865:  address[] memory _pairs,

866:  uint256[] memory _pairBinSteps,

867:  IERC20[] memory _tokenPath,

      /// @audit Store `_pairBinSteps` in calldata.
941:  function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)

      /// @audit Store `_tokenPath` in calldata.
941:  function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```solidity
File: src/LBToken.sol

      /// @audit Store `_accounts` in calldata.
79:   function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)

      /// @audit Store `_ids` in calldata.
79:   function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)

152:  uint256[] memory _ids,

153:  uint256[] memory _amounts
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

```solidity
File: src/libraries/FeeDistributionHelper.sol

26:    FeeHelper.FeesDistribution memory _fees,

49:   function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol

```solidity
File: src/libraries/FeeHelper.sol

55:   function updateVariableFeeParameters(FeeParameters memory _fp, uint256 _activeId) internal view {

80:   function updateVolatilityAccumulated(FeeParameters memory _fp, uint256 _activeId) internal pure {

91:   function getBaseFee(FeeParameters memory _fp) internal pure returns (uint256) {

100:  function getVariableFee(FeeParameters memory _fp) internal pure returns (uint256 variableFee) {

116:  function getFeeAmount(FeeParameters memory _fp, uint256 _amount) internal pure returns (uint256) {

124:  function getFeeAmountFrom(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {

133:  function getFeeAmountForC(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {

142:  function getFeeAmountDistribution(FeeParameters memory _fp, uint256 _fees)

157:  function getTotalFee(FeeParameters memory _fp) private pure returns (uint256) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol

```solidity
File: src/libraries/SwapHelper.sol

32:    ILBPair.Bin memory bin,

33:    FeeHelper.FeeParameters memory fp,

82:    ILBPair.Bin memory bin,

83:    FeeHelper.FeesDistribution memory pairFees,

84:    FeeHelper.FeesDistribution memory fees,

108:  ILBPair.Bin memory bin,

109:  ILBPair.PairInformation memory pair,
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol

```solidity
File: src/libraries/TokenHelper.sol

74:   function _catchTransferError(bool success, bytes memory result) private pure {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **24** instances of this issue:_

```solidity
File: src/LBFactory.sol

165:          for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

195:          for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

549:  if (_filterPeriod >= _decayPeriod) revert LBFactory__DecreasingPeriods(_filterPeriod, _decayPeriod);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBPair.sol

278:          if (_lastId >= _id && i != 0) revert LBPair__OnlyStrictlyIncreasingId();

517:          if (_mintInfo.id >= _pair.activeId) {

636:          if (_id <= _activeId) {

643:          if (_id >= _activeId) {

924:  if (_oracleSize >= _newSize) revert LBPair__NewSizeTooSmall(_newSize, _oracleSize);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```solidity
File: src/libraries/BitMath.sol

71:        if (x >= 1 << 128) {

75:        if (x >= 1 << 64) {

79:        if (x >= 1 << 32) {

83:        if (x >= 1 << 16) {

87:        if (x >= 1 << 8) {

91:        if (x >= 1 << 4) {

95:        if (x >= 1 << 2) {

99:        if (x >= 1 << 1) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol

```solidity
File: src/libraries/FeeHelper.sol

58:    if (_deltaT >= _fp.filterPeriod || _fp.time == 0) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol

```solidity
File: src/libraries/Math128x128.sol

49:        if (x >= LOG_SCALE) {

75:                if (y >= 1 << (LOG_SCALE_OFFSET + 1)) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol

```solidity
File: src/libraries/Math512Bits.sol

68:        if (prod1 >= 1 << offset) revert Math512Bits__MulShiftOverflow(prod1, offset);

200:      if (prod1 >= denominator) revert Math512Bits__MulDivOverflow(prod1, denominator);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol

```solidity
File: src/libraries/Oracle.sol

118:  if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {

160:  while (_high >= _low) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol

```solidity
File: src/libraries/SwapHelper.sol

61:    if (_maxAmountInToBin + fees.total <= amountIn) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol

### Tight variable packing

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage. By arranging the variables, it is possible to minimize the number of slots used within a contract’s storage and therefore reduce deployment costs.

_There are **1** instances of this issue:_

```solidity
File: src/LBFactory.sol

25:   /// @audit Currently storage variables are packed in 10 slots but could fit in 9
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **3** instances of this issue:_

```solidity
File: src/LBFactory.sol

39:   bool public override creationUnlocked;

54:   EnumerableSet.AddressSet private _quoteAssetWhitelist;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBPair.sol

72:   bytes32[65_535] private _oracle;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

### Using bools for storage variables incurs overhead

Use `uint256(1)` and `uint256(2)` for `true`/`false` to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are **1** instances of this issue:_

```solidity
File: src/LBFactory.sol

39:   bool public override creationUnlocked;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol