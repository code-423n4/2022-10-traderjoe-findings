# GAS OPTIMIZATIONS REPORT

## TRADERJOE

### Use x = x + y instead of x += y for storage variables

[LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol):

```solidity
line#L211: _totalSupplies[_id] += _amount;

line#L241: _totalSupplies[_id] -= _amount;
```

### Do not write default values to variables

If you declare a variables of type `uint256` it will automatically get assigned to its default value of 0. It is a gas wastage to assign it yourself.

[Samples.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol):

```solidity
line#L19:  uint256 private constant _OFFSET_INITIALIZED = 0;
```

### `x < y + 1` is cheaper than `x <= y`

[Math512Bits.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol):

```solidity
line#L68:  if (prod1 >= 1 << offset) revert Math512Bits__MulShiftOverflow(prod1, offset);

line#L200: if (prod1 >= denominator) revert Math512Bits__MulDivOverflow(prod1, denominator);
```

[SwapHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol):

```solidity
line#L61:  if (_maxAmountInToBin + fees.total <= amountIn) {
```

[FeeHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol):

```solidity
line#L58:  if (_deltaT >= _fp.filterPeriod || _fp.time == 0) {
```

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L165: for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

line#L195: for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

line#L549: if (_filterPeriod >= _decayPeriod) revert LBFactory__DecreasingPeriods(_filterPeriod, _decayPeriod);
```

[Math128x128.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol):

```solidity
line#L49:  if (x >= LOG_SCALE) {

line#L75:  if (y >= 1 << (LOG_SCALE_OFFSET + 1)) {
```

[BitMath.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol):

```solidity
line#L71:  if (x >= 1 << 128) {

line#L75:  if (x >= 1 << 64) {

line#L83:  if (x >= 1 << 16) {

line#L87:  if (x >= 1 << 8) {

line#L95:  if (x >= 1 << 2) {

line#L99:  if (x >= 1 << 1) {
```

[Oracle.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol):

```solidity
line#L118: if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {

line#L160: while (_high >= _low) {
```

[LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol):

```solidity
line#L278: if (_lastId >= _id && i != 0) revert LBPair__OnlyStrictlyIncreasingId();

line#L517: if (_mintInfo.id >= _pair.activeId) {

line#L643: if (_id >= _activeId) {

line#L924: if (_oracleSize >= _newSize) revert LBPair__NewSizeTooSmall(_newSize, _oracleSize);
```

### Pack storage variables in less slots to save gas

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol)

### Use `unchecked` for the counter in `for` loops

Since it is compared to a target value (usually the length of an array) there is no way the counter overflows

[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol):

```solidity
line#L674: for (uint256 i; i < depositIds.length; ++i) {

line#L778: for (uint256 i; i < _pairs.length; ++i) {

line#L878: for (uint256 i; i < _pairs.length; ++i) {

line#L951: for (uint256 i; i < pairs.length; ++i) {
```

[LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol):

```solidity
line#L75:  for (uint256 i; i < swapLength; i++) {

line#L100: for (uint256 j; j < LBPairsAvailable.length; j++) {

line#L177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L165: for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

line#L195: for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
```

[LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol):

```solidity
line#L274: for (uint256 i; i < _ids.length; ++i) {

line#L496: for (uint256 i; i < _ids.length; ++i) {

line#L701: for (uint256 i; i < _ids.length; ++i) {

line#L929: for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {
```

[LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol):

```solidity
line#L90:  for (uint256 i; i < _accounts.length; ++i) {

line#L163: for (uint256 i; i < _ids.length; ++i) {
```

### Use `if (x != 0)` instead of `if (x > 0)` for uints comparison

Saves 3 gas per `if`-check

[LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol):

```solidity
line#L82:  if (reserveIn > 0 && reserveOut > 0) {

line#L154: for (uint256 i = swapLength; i > 0; i--) {

line#L160: if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
```

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L161: if (_nbPresets > 0) {

line#L191: if (_nbAvailable > 0) {
```

### Use `uint256` instead of the smaller uints where possible

The word-size in ethereum is 32 bytes which means that every variables which is sized with less bytes will cost more to operate with

[IJoeRouter02.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeRouter02.sol):

```solidity
line#L27:  uint8 v,
```

[FeeHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol):

```solidity
line#L30:  uint16 binStep;

line#L31:  uint16 baseFactor;

line#L33:  uint16 decayPeriod;

line#L34:  uint16 reductionFactor;

line#L48:  uint128 total;

line#L49:  uint128 protocol;
```

[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol):

```solidity
line#L197: uint16 _binStep

line#L277: uint16 _binStep,

line#L310: uint16 _binStep,
```

[LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol):

```solidity
line#L108: uint16 _sampleLifetime,

line#L679: function increaseOracleLength(uint16 _newSize) external override {

line#L921: function _increaseOracle(uint16 _newSize) private {
```

[IJoeRouter01.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeRouter01.sol):

```solidity
line#L73:  uint8 v,

line#L86:  uint8 v,
```

[ILBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol):

```solidity
line#L43:  uint16 oracleSampleLifetime;

line#L44:  uint16 oracleSize;

line#L47:  uint16 oracleId;

line#L64:  uint128 tokenX;

line#L240: function increaseOracleLength(uint16 newSize) external;

line#L254: uint16 sampleLifetime,
```

[FeeDistributionHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol):

```solidity
line#L31:  uint128 _totalFees = _pairFees.total;
```

[ILBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBFactory.sol):

```solidity
line#L107: function getPreset(uint16 binStep)

line#L134: uint16 binStep

line#L146: uint16 baseFactor,

line#L147: uint16 filterPeriod,

line#L149: uint16 reductionFactor,

line#L151: uint16 protocolShare,

line#L156: function removePreset(uint16 binStep) external;

line#L161: uint16 binStep,

line#L163: uint16 filterPeriod,

line#L164: uint16 decayPeriod,

line#L167: uint16 protocolShare,
```

[SafeCast.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol):

```solidity
line#L91:  function safe160(uint256 x) internal pure returns (uint160 y) {

line#L119: function safe128(uint256 x) internal pure returns (uint128 y) {

line#L175: function safe64(uint256 x) internal pure returns (uint64 y) {

line#L203: function safe32(uint256 x) internal pure returns (uint32 y) {

line#L224: function safe8(uint256 x) internal pure returns (uint8 y) {
```

[ILBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBRouter.sol):

```solidity
line#L72:  uint16 binStep

line#L87:  uint16 binStep,

line#L98:  uint16 binStep,
```

[IJoePair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol):

```solidity
line#L44:  uint8 v,

line#L75:  uint32 blockTimestampLast
```

[LBErrors.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol):

```solidity
line#L57:  error LBFactory__DecreasingPeriods(uint16 filterPeriod, uint16 decayPeriod);

line#L58:  error LBFactory__BaseFactorOverflows(uint16 baseFactor, uint256 max);

line#L60:  error LBFactory__VariableFeeControlOverflows(uint16 variableFeeControl, uint256 max);

line#L63:  error LBFactory__BinStepRequirementsBreached(uint256 lowerBound, uint16 binStep, uint256 higherBound);
```

[BitMath.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol):

```solidity
line#L16:  uint8 _bit,

line#L39:  function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {

line#L69:  function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {

line#L108: function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {
```

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L120: function getPreset(uint16 _binStep)

line#L238: uint16 _binStep

line#L342: uint16 _baseFactor,

line#L343: uint16 _filterPeriod,

line#L345: uint16 _reductionFactor,

line#L347: uint16 _protocolShare,

line#L396: function removePreset(uint16 _binStep) external override onlyOwner {

line#L426: uint16 _binStep,

line#L428: uint16 _filterPeriod,

line#L429: uint16 _decayPeriod,

line#L432: uint16 _protocolShare,

line#L534: uint16 _binStep,

line#L536: uint16 _filterPeriod,

line#L537: uint16 _decayPeriod,

line#L540: uint16 _protocolShare,
```

### Mark functions as `payable` to avoid the low-level evm `is-a-payment-provided` check

The EVM checks whether a payment is provided to the function and this check costs additional gas. If the function is marked as `payable` there is no such check

[PendingOwnable.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol):

```solidity
line#L59:  function setPendingOwner(address pendingOwner_) public override onlyOwner {

line#L68:  function revokePendingOwner() public override onlyOwner {

line#L84:  function renounceOwnership() public override onlyOwner {
```

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L215: function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {

line#L396: function removePreset(uint16 _binStep) external override onlyOwner {

line#L474: function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

line#L485: function setFactoryLockedState(bool _locked) external override onlyOwner {

line#L502: function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

line#L520: function forceDecay(ILBPair _LBPair) external override onlyOwner {
```

### Use `1` and `2` instead of `true` and `false`

EVM word size is 32 bytes. The `uint256 1` and `uint256 2` will be cheaper to use than the `boolean true` and `boolean false` since the first are 32 bytes long and the last are only 1 byte

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L39:  bool public override creationUnlocked;
```

### Use `constant` and `immutable` keywords for storage varibles if they doesn't change through contract's lifecycle

That way the variable will go to the contract's bytecode and will not allocate a storage slot

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L39:  bool public override creationUnlocked;

line#L54:  EnumerableSet.AddressSet private _quoteAssetWhitelist;
```

[LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol):

```solidity
line#L72:  bytes32[65_535] private _oracle;
```

### `++i` costs less gas than `i++` (same for `--i`/`i--`)

Prefix increments are cheaper than postfix increments

[LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol):

```solidity
line#L75:  for (uint256 i; i < swapLength; i++) {

line#L100: for (uint256 j; j < LBPairsAvailable.length; j++) {

line#L154: for (uint256 i = swapLength; i > 0; i--) {

line#L177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol):

```solidity
line#L711: for (uint256 i = _pairs.length; i != 0; i--) {
```

### Function inlining

If a function is called only once it can be inlined in order to save 20 gas

[Oracle.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol):

```solidity
line#L145: function binarySearch(
```

[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol):

```solidity
line#L930: function _getPair(

line#L941: function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
```

### Use `calldata` where possible instead of `memory` to save gas

[FeeDistributionHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol):

```solidity
line#L26:  FeeHelper.FeesDistribution memory _fees,

line#L49:  function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
```

[LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol):

```solidity
           /// @audit Store `_accounts` in calldata.
line#L79:  function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)

           /// @audit Store `_ids` in calldata.
line#L79:  function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)

line#L152: uint256[] memory _ids,

line#L153: uint256[] memory _amounts
```

[TokenHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol):

```solidity
line#L74:  function _catchTransferError(bool success, bytes memory result) private pure {
```

[SwapHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol):

```solidity
line#L32:  ILBPair.Bin memory bin,

line#L33:  FeeHelper.FeeParameters memory fp,

line#L83:  FeeHelper.FeesDistribution memory pairFees,

line#L84:  FeeHelper.FeesDistribution memory fees,

line#L109: ILBPair.PairInformation memory pair,
```

[LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol):

```solidity
line#L54:  function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)

line#L134: function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
```

[LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol):

```solidity
line#L261: function pendingFees(address _account, uint256[] memory _ids)

line#L467: uint256[] memory _ids,

line#L469: uint256[] memory _distributionY,

line#L617: uint256[] memory _ids,

line#L688: function collectFees(address _account, uint256[] memory _ids)

line#L845: Bin memory _bin,

line#L881: Bin memory _bin,
```

[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol):

```solidity
line#L207: function addLiquidity(LiquidityParameters memory _liquidityParameters)

line#L230: function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)

line#L281: uint256[] memory _amounts,

line#L313: uint256[] memory _ids,

line#L355: uint256[] memory _pairBinSteps,

line#L356: IERC20[] memory _tokenPath,

line#L381: IERC20[] memory _tokenPath,

line#L409: uint256[] memory _pairBinSteps,

line#L434: uint256[] memory _pairBinSteps,

line#L435: IERC20[] memory _tokenPath,

line#L463: IERC20[] memory _tokenPath,

line#L495: uint256[] memory _pairBinSteps,

line#L534: uint256[] memory _pairBinSteps,

line#L535: IERC20[] memory _tokenPath,

line#L565: IERC20[] memory _tokenPath,

line#L596: uint256[] memory _pairBinSteps,

line#L645: uint256[] memory _ids,

line#L646: uint256[] memory _amounts

line#L702: uint256[] memory _pairBinSteps,

line#L703: address[] memory _pairs,

line#L745: uint256[] memory _ids,

line#L746: uint256[] memory _amounts,

line#L765: uint256[] memory _pairBinSteps,

line#L766: IERC20[] memory _tokenPath,

line#L818: uint256[] memory _pairBinSteps,

line#L819: IERC20[] memory _tokenPath,

line#L865: address[] memory _pairs,

line#L866: uint256[] memory _pairBinSteps,

           /// @audit Store `_pairBinSteps` in calldata.
line#L941: function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)

           /// @audit Store `_tokenPath` in calldata.
line#L941: function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
```

[FeeHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol):

```solidity
line#L55:  function updateVariableFeeParameters(FeeParameters memory _fp, uint256 _activeId) internal view {

line#L80:  function updateVolatilityAccumulated(FeeParameters memory _fp, uint256 _activeId) internal pure {

line#L100: function getVariableFee(FeeParameters memory _fp) internal pure returns (uint256 variableFee) {

line#L116: function getFeeAmount(FeeParameters memory _fp, uint256 _amount) internal pure returns (uint256) {

line#L133: function getFeeAmountForC(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {

line#L142: function getFeeAmountDistribution(FeeParameters memory _fp, uint256 _fees)
```

### Use `private` visibility modifiers instead of `public` for storage `constant`s

Saves 3000+ gas per variable when deploying contract

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol):

```solidity
line#L25:  uint256 public constant override MAX_FEE = 0.1e18;

line#L27:  uint256 public constant override MIN_BIN_STEP = 1;

line#L28:  uint256 public constant override MAX_BIN_STEP = 100;

line#L30:  uint256 public constant override MAX_PROTOCOL_SHARE = 2_500;
```
