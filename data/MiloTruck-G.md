# Gas Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|For-Loops: Index increments can be left unchecked|5|
|2|Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`|9|
|3|Visibility: `public` functions can be set to `external`|2|
|4|Unnecessary initialization of variables with default values|1|
|5|State variables can be packed into fewer storage slots|1|
|6|Using `bool` for storage incurs overhead|2|
|7|Use `calldata` instead of `memory` for read-only arguments in external functions|33|
|8|Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead|107|
|9|Not using the named return variables when a function returns, wastes deployment gas|18|
|10|Multiple accesses of a mapping/array should use a local variable cache|1|

Total: **179** instances over **10** issues

## For-Loops: Index increments can be left unchecked
From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks. 

In for-loops, as it is impossible for the index to overflow, index increments can be left unchecked to save **[30-40 gas](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)** per loop iteration. 

For example, the code below:
```js
for (uint256 i; i < numIterations; ++i) {  
    // ...  
}  
```
can be changed to:
```js
for (uint256 i; i < numIterations;) {  
    // ...  
    unchecked { ++i; }  
}  
```

_There are **5** instances of this issue:_  
```solidity
src/LBRouter.sol:
 711:        for (uint256 i = _pairs.length; i != 0; i--) {

src/LBQuoter.sol:
  75:        for (uint256 i; i < swapLength; i++) {
 100:        for (uint256 j; j < LBPairsAvailable.length; j++) {
 154:        for (uint256 i = swapLength; i > 0; i--) {
 177:        for (uint256 j; j < LBPairsAvailable.length; j++) {
```

## Arithmetics: `++i` costs less gas compared to `i++` or `i += 1`
`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about **5 gas** per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:
```js
uint i = 1;  
i++; // == 1 but i == 2  
```
But `++i` returns the actual incremented value:
```js
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`, thus it costs more gas.

The same logic applies for `--i` and `i--`.

_There are **9** instances of this issue:_  
```solidity
src/LBRouter.sol:
 711:        for (uint256 i = _pairs.length; i != 0; i--) {

src/LBQuoter.sol:
  75:        for (uint256 i; i < swapLength; i++) {
 100:        for (uint256 j; j < LBPairsAvailable.length; j++) {
 154:        for (uint256 i = swapLength; i > 0; i--) {
 177:        for (uint256 j; j < LBPairsAvailable.length; j++) {

src/libraries/Math512Bits.sol:
  99:        if (mulmod(x, y, 1 << offset) != 0) result += 1;
 159:        if (mulmod(x, 1 << offset, denominator) != 0) result += 1;

src/libraries/BitMath.sol:
 100:        msb += 1;
 139:        lsb += 1;
```

## Visibility: `public` functions can be set to `external`
Calls to `external` functions are cheaper than `public` functions. Thus, if a function is not used internally in any contract, it should be set to `external` to save gas and improve code readability.

_There are **2** instances of this issue:_  
```solidity
src/LBQuoter.sol:
  54:        function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
  55:            public
  56:            view
  57:            returns (Quote memory quote)
  58:        {


 134:        function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
 135:            public
 136:            view
 137:            returns (Quote memory quote)
 138:        {
```

## Unnecessary initialization of variables with default values
Uninitialized variables are assigned with a default value depending on its type:
* `uint`: `0`
* `bool`: `false`
* `address`: `address(0)`

Thus, explicitly initializing a variable with its default value costs unnecesary gas. For example, the following code:
```js
bool b = false;
address c = address(0);
uint256 a = 0;
```
can be changed to:
```js
uint256 a;
bool b;
address c;
```

_There is **1** instance of this issue:_  
```solidity
src/libraries/Samples.sol:
  19:        uint256 private constant _OFFSET_INITIALIZED = 0;
```

## State variables can be packed into fewer storage slots
With reference to the [Solidity Docs](https://docs.soliditylang.org/en/v0.8.16/internals/layout_in_storage.html):

> For each variable, a size in bytes is determined according to its type. Multiple, contiguous items that need less than 32 bytes are packed into a single storage slot if possible.

By manipulating the order of state variables, contracts can use less storage slots which in turn saves gas.

Futhermore, if multiple state variables occupying the same slot are written by the same function or  constructor, it avoids a separate Gsset (**20000 gas**). Reads of these state variables may also be cheaper.

_There is **1** instance of this issue:_  
Variable ordering with 9 slots instead of the current 10 in `LBFactory`:
```solidity
// Constants
uint256 public constant override MAX_FEE = 0.1e18;
uint256 public constant override MIN_BIN_STEP = 1;
uint256 public constant override MAX_BIN_STEP = 100;
uint256 public constant override MAX_PROTOCOL_SHARE = 2_500;

// uint256: 32
uint256 public override flashLoanFee;

// ILBPair[]: 32
ILBPair[] public override allLBPairs;

// mapping(IERC20 => mapping(IERC20 => mapping(uint256 => LBPairInformation))): 32
mapping(IERC20 => mapping(IERC20 => mapping(uint256 => LBPairInformation))) private _LBPairsInfo;

// bytes32: 32
bytes32 private _availablePresets;

// mapping(uint256 => bytes32): 32
mapping(uint256 => bytes32) private _presets;

// EnumerableSet.AddressSet: 32
EnumerableSet.AddressSet private _quoteAssetWhitelist;

// mapping(IERC20 => mapping(IERC20 => bytes32)): 32
mapping(IERC20 => mapping(IERC20 => bytes32)) private _availableLBPairBinSteps;

// address: 20, bool: 1
address public override LBPairImplementation;
bool public override creationUnlocked;

// address: 20
address public override feeRecipient;

src/LBFactory.sol:
  25:        uint256 public constant override MAX_FEE = 0.1e18; // 10%
```

## Using `bool` for storage incurs overhead
Declaring storage variables as `bool` is more expensive compared to `uint256`, as explained [here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol#L23-L27):

> Booleans are more expensive than `uint256` or any type that takes up a full word because each write operation emits an extra `SLOAD` to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

Use `uint256(1)` and `uint256(2)` rather than true/false to avoid a Gwarmaccess ([**100 gas**](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)) for the extra `SLOAD`, and to avoid Gsset (**20000 gas**) when changing from 'false' to 'true', after having been 'true' in the past.

_There are **2** instances of this issue:_  
```solidity
src/LBToken.sol:
  21:        mapping(address => mapping(address => bool)) private _spenderApprovals;

src/LBFactory.sol:
  39:        bool public override creationUnlocked;
```

## Use `calldata` instead of `memory` for read-only arguments in external functions
When an external function with a `memory` array is called, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). 

Using `calldata` directly instead of `memory` helps to save gas as values are read directly from `calldata` using `calldataload`, thus removing the need for such a loop in the contract code during runtime execution. 

Also, structs have the same overhead as an array of length one.

_There are **33** instances of this issue:_  
```solidity
src/LBRouter.sol:
 207:        function addLiquidity(LiquidityParameters memory _liquidityParameters)
 230:        function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)
 280:        uint256[] memory _ids,
 281:        uint256[] memory _amounts,
 313:        uint256[] memory _ids,
 314:        uint256[] memory _amounts,
 355:        uint256[] memory _pairBinSteps,
 356:        IERC20[] memory _tokenPath,
 380:        uint256[] memory _pairBinSteps,
 381:        IERC20[] memory _tokenPath,
 409:        uint256[] memory _pairBinSteps,
 410:        IERC20[] memory _tokenPath,
 434:        uint256[] memory _pairBinSteps,
 435:        IERC20[] memory _tokenPath,
 462:        uint256[] memory _pairBinSteps,
 463:        IERC20[] memory _tokenPath,
 495:        uint256[] memory _pairBinSteps,
 496:        IERC20[] memory _tokenPath,
 534:        uint256[] memory _pairBinSteps,
 535:        IERC20[] memory _tokenPath,
 564:        uint256[] memory _pairBinSteps,
 565:        IERC20[] memory _tokenPath,
 596:        uint256[] memory _pairBinSteps,
 597:        IERC20[] memory _tokenPath,
 645:        uint256[] memory _ids,
 646:        uint256[] memory _amounts

src/LBPair.sol:
 261:        function pendingFees(address _account, uint256[] memory _ids)
 467:        uint256[] memory _ids,
 468:        uint256[] memory _distributionX,
 469:        uint256[] memory _distributionY,
 617:        uint256[] memory _ids,
 618:        uint256[] memory _amounts,
 688:        function collectFees(address _account, uint256[] memory _ids)
```

## Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
As seen from [here](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html):
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

However, this does not apply to storage values as using reduced-size types might be beneficial to pack multiple elements into a single storage slot. Thus, where appropriate, use `uint256`/`int256` and downcast when needed.

_There are **107** instances of this issue:_  
```solidity
src/LBRouter.sol:
  72:        function getIdFromPrice(ILBPair _LBPair, uint256 _price) external view override returns (uint24) {
  80:        function getPriceFromId(ILBPair _LBPair, uint24 _id) external view override returns (uint256) {
 196:        uint24 _activeId,
 197:        uint16 _binStep
 277:        uint16 _binStep,
 310:        uint16 _binStep,

src/LBPair.sol:
 107:        uint24 _activeId,
 108:        uint16 _sampleLifetime,
 243:        function findFirstNonEmptyBinId(uint24 _id, bool _swapForY) external view override returns (uint24) {
 243:        function findFirstNonEmptyBinId(uint24 _id, bool _swapForY) external view override returns (uint24) {
 251:        function getBin(uint24 _id) external view override returns (uint256 reserveX, uint256 reserveY) {
 624:        uint24 _id = _ids[i].safe24();
 679:        function increaseOracleLength(uint16 _newSize) external override {
 921:        function _increaseOracle(uint16 _newSize) private {
 994:        function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {

src/LBFactory.sol:
 120:        function getPreset(uint16 _binStep)
 237:        uint24 _activeId,
 238:        uint16 _binStep
 341:        uint16 _binStep,
 342:        uint16 _baseFactor,
 343:        uint16 _filterPeriod,
 344:        uint16 _decayPeriod,
 345:        uint16 _reductionFactor,
 346:        uint24 _variableFeeControl,
 347:        uint16 _protocolShare,
 348:        uint24 _maxVolatilityAccumulated,
 349:        uint16 _sampleLifetime
 396:        function removePreset(uint16 _binStep) external override onlyOwner {
 426:        uint16 _binStep,
 427:        uint16 _baseFactor,
 428:        uint16 _filterPeriod,
 429:        uint16 _decayPeriod,
 430:        uint16 _reductionFactor,
 431:        uint24 _variableFeeControl,
 432:        uint16 _protocolShare,
 433:        uint24 _maxVolatilityAccumulated
 534:        uint16 _binStep,
 535:        uint16 _baseFactor,
 536:        uint16 _filterPeriod,
 537:        uint16 _decayPeriod,
 538:        uint16 _reductionFactor,
 539:        uint24 _variableFeeControl,
 540:        uint16 _protocolShare,
 541:        uint24 _maxVolatilityAccumulated

src/libraries/FeeHelper.sol:
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

src/libraries/SafeCast.sol:
  14:        function safe248(uint256 x) internal pure returns (uint248 y) {
  21:        function safe240(uint256 x) internal pure returns (uint240 y) {
  28:        function safe232(uint256 x) internal pure returns (uint232 y) {
  35:        function safe224(uint256 x) internal pure returns (uint224 y) {
  42:        function safe216(uint256 x) internal pure returns (uint216 y) {
  49:        function safe208(uint256 x) internal pure returns (uint208 y) {
  56:        function safe200(uint256 x) internal pure returns (uint200 y) {
  63:        function safe192(uint256 x) internal pure returns (uint192 y) {
  70:        function safe184(uint256 x) internal pure returns (uint184 y) {
  77:        function safe176(uint256 x) internal pure returns (uint176 y) {
  84:        function safe168(uint256 x) internal pure returns (uint168 y) {
  91:        function safe160(uint256 x) internal pure returns (uint160 y) {
  98:        function safe152(uint256 x) internal pure returns (uint152 y) {
 105:        function safe144(uint256 x) internal pure returns (uint144 y) {
 112:        function safe136(uint256 x) internal pure returns (uint136 y) {
 119:        function safe128(uint256 x) internal pure returns (uint128 y) {
 126:        function safe120(uint256 x) internal pure returns (uint120 y) {
 133:        function safe112(uint256 x) internal pure returns (uint112 y) {
 140:        function safe104(uint256 x) internal pure returns (uint104 y) {
 147:        function safe96(uint256 x) internal pure returns (uint96 y) {
 154:        function safe88(uint256 x) internal pure returns (uint88 y) {
 161:        function safe80(uint256 x) internal pure returns (uint80 y) {
 168:        function safe72(uint256 x) internal pure returns (uint72 y) {
 175:        function safe64(uint256 x) internal pure returns (uint64 y) {
 182:        function safe56(uint256 x) internal pure returns (uint56 y) {
 189:        function safe48(uint256 x) internal pure returns (uint48 y) {
 196:        function safe40(uint256 x) internal pure returns (uint40 y) {
 203:        function safe32(uint256 x) internal pure returns (uint32 y) {
 210:        function safe24(uint256 x) internal pure returns (uint24 y) {
 217:        function safe16(uint256 x) internal pure returns (uint16 y) {
 224:        function safe8(uint256 x) internal pure returns (uint8 y) {

src/libraries/SwapHelper.sol:
 111:        uint112 amountInToBin,
 112:        uint112 amountOutOfBin

src/libraries/TreeMath.sol:
  23:        uint24 _binId,
  25:        ) internal view returns (uint24) {
 104:        function _getIdsFromAbove(uint24 _id) private pure returns (uint24, uint24) {
 104:        function _getIdsFromAbove(uint24 _id) private pure returns (uint24, uint24) {
 104:        function _getIdsFromAbove(uint24 _id) private pure returns (uint24, uint24) {
 113:        function _getBottomId(uint24 _branchId, uint24 _leafId) private pure returns (uint24) {
 113:        function _getBottomId(uint24 _branchId, uint24 _leafId) private pure returns (uint24) {
 113:        function _getBottomId(uint24 _branchId, uint24 _leafId) private pure returns (uint24) {

src/libraries/BinHelper.sol:
  22:        function getIdFromPrice(uint256 _price, uint256 _binStep) internal pure returns (uint24) {

src/libraries/BitMath.sol:
  16:        uint8 _bit,
  28:        function significantBit(uint256 _integer, bool _isMostSignificant) internal pure returns (uint8) {
  39:        function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {
  56:        function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {
  69:        function mostSignificantBit(uint256 x) internal pure returns (uint8 msb) {
 108:        function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {

src/libraries/FeeDistributionHelper.sol:
  31:        uint128 _totalFees = _pairFees.total;
```

## Not using the named return variables when a function returns, wastes deployment gas  

_There are **18** instances of this issue:_  
```solidity
src/LBPair.sol:
 142:        return _getReservesAndId();
 162:        return _getGlobalFees();
 252:        return _getBin(_id);
1007:        return (reserveX.safe112(), reserveY.safe112());

src/libraries/Math128x128.sol:
  41:        if (x == 1) return 0;
  98:        if (y == 0) return Constants.SCALE;
 202:        return invert ? type(uint256).max / result : result;

src/libraries/Math512Bits.sol:
  37:        return _getEndOfDivRoundDown(x, y, denominator, prod0, prod1);
 133:        return _getEndOfDivRoundDown(x, 1 << offset, denominator, prod0, prod1);

src/libraries/Samples.sol:
  58:        return pack(_cumulativeBinCrossed, _cumulativeVolatilityAccumulated, _cumulativeId, block.timestamp, 1);

  76:        return
  77:            _cumulativeBinCrossed.encode(_MASK_CUMULATIVE_BIN_CROSSED, _OFFSET_CUMULATIVE_BIN_CROSSED) |
  78:            _cumulativeVolatilityAccumulated.encode(
  79:                _MASK_CUMULATIVE_VolatilityAccumulated,
  80:                _OFFSET_CUMULATIVE_VolatilityAccumulated
  81:            ) |
  82:            _cumulativeId.encode(_MASK_CUMULATIVE_ID, _OFFSET_CUMULATIVE_ID) |
  83:            _timestamp.encode(_MASK_TIMESTAMP, _OFFSET_TIMESTAMP) |
  84:            _initialized.encode(_MASK_INITIALIZED, _OFFSET_INITIALIZED);

src/libraries/BitMath.sol:
  44:        if (x == 0) return type(uint256).max;
  47:        return mostSignificantBit(x) - _shift;
  60:        if (x == 0) return type(uint256).max;
  62:        return leastSignificantBit(x) + bit;
 142:        return 255 - lsb;

src/libraries/Oracle.sol:
  85:        return (_lookUpTimestamp, cumulativeId, cumulativeVolatilityAccumulated, cumulativeBinCrossed);
 173:        return (_sample, _sample);
```

## Multiple accesses of a mapping/array should use a local variable cache
If a mapping/array is read with the same key/index multiple times in a function, it should be cached in a stack variable.

 Caching a mapping's value in a local `storage` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory.

_There is **1** instance of this issue:_  
`_balances[_id]` in `_transfer()`:
```solidity
src/LBToken.sol:
 182:        uint256 _fromBalance = _balances[_id][_from];
 187:        uint256 _toBalance = _balances[_id][_to];
```
