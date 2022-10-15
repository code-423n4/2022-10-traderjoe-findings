
### [G01] State variables only set in the constructor should be declared `immutable`


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::32 => address public override LBPairImplementation;
2022-10-traderjoe/src/LBFactory.sol::34 => address public override feeRecipient;
2022-10-traderjoe/src/LBFactory.sol::36 => uint256 public override flashLoanFee;
2022-10-traderjoe/src/LBFactory.sol::41 => ILBPair[] public override allLBPairs;
2022-10-traderjoe/src/LBFactory.sol::49 => bytes32 private _availablePresets;
2022-10-traderjoe/src/LBFactory.sol::54 => EnumerableSet.AddressSet private _quoteAssetWhitelist;
2022-10-traderjoe/src/LBPair.sol::54 => IERC20 public override tokenX;
2022-10-traderjoe/src/LBPair.sol::55 => IERC20 public override tokenY;
2022-10-traderjoe/src/LBPair.sol::59 => PairInformation private _pairInformation;
2022-10-traderjoe/src/LBPair.sol::60 => FeeHelper.FeeParameters private _feeParameters;
2022-10-traderjoe/src/LBPair.sol::72 => bytes32[65_535] private _oracle;
2022-10-traderjoe/src/libraries/PendingOwnable.sol::25 => address private _owner;
2022-10-traderjoe/src/libraries/PendingOwnable.sol::26 => address private _pendingOwner;
2022-10-traderjoe/src/libraries/ReentrancyGuardUpgradeable.sol::25 => uint256 private _status;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor, avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::32 => address public override LBPairImplementation;
2022-10-traderjoe/src/LBFactory.sol::34 => address public override feeRecipient;
```



### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2022-10-traderjoe/src/LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
2022-10-traderjoe/src/LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
2022-10-traderjoe/src/LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
2022-10-traderjoe/src/LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::165 => for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
2022-10-traderjoe/src/LBFactory.sol::195 => for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
2022-10-traderjoe/src/LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe/src/LBPair.sol::929 => for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {
2022-10-traderjoe/src/LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {
2022-10-traderjoe/src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe/src/LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
2022-10-traderjoe/src/LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
2022-10-traderjoe/src/LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```


### [G05] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::79 => return _quoteAssetWhitelist.length();
2022-10-traderjoe/src/LBFactory.sol::93 => return _quoteAssetWhitelist.contains(address(_token));
2022-10-traderjoe/src/LBFactory.sol::107 => return _getLBPairInformation(_tokenA, _tokenB, _binStep);
2022-10-traderjoe/src/LBPair.sol::142 => return _getReservesAndId();
2022-10-traderjoe/src/LBPair.sol::162 => return _getGlobalFees();
2022-10-traderjoe/src/LBPair.sol::236 => return _feeParameters;
2022-10-traderjoe/src/LBPair.sol::244 => return _tree.findFirstBin(_id, _swapForY);
2022-10-traderjoe/src/LBPair.sol::252 => return _getBin(_id);
2022-10-traderjoe/src/LBRouter.sol::921 => return _LBPair;
2022-10-traderjoe/src/LBToken.sol::50 => return _name;
2022-10-traderjoe/src/LBToken.sol::56 => return _symbol;
2022-10-traderjoe/src/LBToken.sol::64 => return _totalSupplies[_id];
2022-10-traderjoe/src/LBToken.sol::72 => return _balances[_id][_account];
2022-10-traderjoe/src/LBToken.sol::101 => return _userIds[_account].at(_index);
2022-10-traderjoe/src/LBToken.sol::108 => return _userIds[_account].length();
2022-10-traderjoe/src/LBToken.sol::116 => return _isApprovedForAll(_owner, _spender);
2022-10-traderjoe/src/LBToken.sol::270 => return _owner == _spender || _spenderApprovals[_owner][_spender];
2022-10-traderjoe/src/libraries/BinHelper.sol::44 => return _getBPValue(_binStep).power(_realId);
2022-10-traderjoe/src/libraries/Math512Bits.sol::37 => return _getEndOfDivRoundDown(x, y, denominator, prod0, prod1);
2022-10-traderjoe/src/libraries/Math512Bits.sol::133 => return _getEndOfDivRoundDown(x, 1 << offset, denominator, prod0, prod1);
2022-10-traderjoe/src/libraries/PendingOwnable.sol::48 => return _owner;
2022-10-traderjoe/src/libraries/PendingOwnable.sol::54 => return _pendingOwner;
2022-10-traderjoe/src/libraries/Samples.sol::91 => return _packedSample.decode(_MASK_INITIALIZED, _OFFSET_INITIALIZED);
2022-10-traderjoe/src/libraries/Samples.sol::98 => return _packedSample.decode(_MASK_TIMESTAMP, _OFFSET_TIMESTAMP);
2022-10-traderjoe/src/libraries/Samples.sol::105 => return _packedSample.decode(_MASK_CUMULATIVE_ID, _OFFSET_CUMULATIVE_ID);
2022-10-traderjoe/src/libraries/Samples.sol::112 => return _packedSample.decode(_MASK_CUMULATIVE_VolatilityAccumulated, _OFFSET_CUMULATIVE_VolatilityAccumulated);
2022-10-traderjoe/src/libraries/Samples.sol::119 => return _packedSample.decode(_MASK_CUMULATIVE_BIN_CROSSED, _OFFSET_CUMULATIVE_BIN_CROSSED);
2022-10-traderjoe/src/libraries/TreeMath.sol::38 => return _getBottomId(_binId, uint24(bit));
2022-10-traderjoe/src/libraries/TreeMath.sol::53 => return _getBottomId(_binId, current.significantBit(_rightSide));
2022-10-traderjoe/src/libraries/TreeMath.sol::66 => return _getBottomId(_binId, current.significantBit(_rightSide));
```


### [G06] It costs more gas to initialize variables to zero than to let the default of zero be applied



#### Findings:
```
2022-10-traderjoe/src/LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
2022-10-traderjoe/src/libraries/FeeHelper.sol::68 => _fp.volatilityReference = 0;
2022-10-traderjoe/src/libraries/Samples.sol::19 => uint256 private constant _OFFSET_INITIALIZED = 0;
```



### [G07] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::120 => function getPreset(uint16 _binStep)
2022-10-traderjoe/src/LBFactory.sol::141 => assert(_binStep == _preset.decode(type(uint16).max, _shift));
2022-10-traderjoe/src/LBFactory.sol::143 => baseFactor = _preset.decode(type(uint16).max, _shift += 16);
2022-10-traderjoe/src/LBFactory.sol::144 => filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
2022-10-traderjoe/src/LBFactory.sol::145 => decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
2022-10-traderjoe/src/LBFactory.sol::146 => reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
2022-10-traderjoe/src/LBFactory.sol::147 => variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
2022-10-traderjoe/src/LBFactory.sol::148 => protocolShare = _preset.decode(type(uint16).max, _shift += 24);
2022-10-traderjoe/src/LBFactory.sol::149 => maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
2022-10-traderjoe/src/LBFactory.sol::151 => sampleLifetime = _preset.decode(type(uint16).max, 240);
2022-10-traderjoe/src/LBFactory.sol::237 => uint24 _activeId,
2022-10-traderjoe/src/LBFactory.sol::238 => uint16 _binStep
2022-10-traderjoe/src/LBFactory.sol::297 => _preset.decode(type(uint16).max, 16),
2022-10-traderjoe/src/LBFactory.sol::298 => _preset.decode(type(uint16).max, 32),
2022-10-traderjoe/src/LBFactory.sol::299 => _preset.decode(type(uint16).max, 48),
2022-10-traderjoe/src/LBFactory.sol::300 => _preset.decode(type(uint16).max, 64),
2022-10-traderjoe/src/LBFactory.sol::301 => _preset.decode(type(uint24).max, 80),
2022-10-traderjoe/src/LBFactory.sol::302 => _preset.decode(type(uint16).max, 104),
2022-10-traderjoe/src/LBFactory.sol::303 => _preset.decode(type(uint24).max, 120)
2022-10-traderjoe/src/LBFactory.sol::341 => uint16 _binStep,
2022-10-traderjoe/src/LBFactory.sol::342 => uint16 _baseFactor,
2022-10-traderjoe/src/LBFactory.sol::343 => uint16 _filterPeriod,
2022-10-traderjoe/src/LBFactory.sol::344 => uint16 _decayPeriod,
2022-10-traderjoe/src/LBFactory.sol::345 => uint16 _reductionFactor,
2022-10-traderjoe/src/LBFactory.sol::346 => uint24 _variableFeeControl,
2022-10-traderjoe/src/LBFactory.sol::347 => uint16 _protocolShare,
2022-10-traderjoe/src/LBFactory.sol::348 => uint24 _maxVolatilityAccumulated,
2022-10-traderjoe/src/LBFactory.sol::349 => uint16 _sampleLifetime
2022-10-traderjoe/src/LBFactory.sol::426 => uint16 _binStep,
2022-10-traderjoe/src/LBFactory.sol::427 => uint16 _baseFactor,
2022-10-traderjoe/src/LBFactory.sol::428 => uint16 _filterPeriod,
2022-10-traderjoe/src/LBFactory.sol::429 => uint16 _decayPeriod,
2022-10-traderjoe/src/LBFactory.sol::430 => uint16 _reductionFactor,
2022-10-traderjoe/src/LBFactory.sol::431 => uint24 _variableFeeControl,
2022-10-traderjoe/src/LBFactory.sol::432 => uint16 _protocolShare,
2022-10-traderjoe/src/LBFactory.sol::433 => uint24 _maxVolatilityAccumulated
2022-10-traderjoe/src/LBFactory.sol::534 => uint16 _binStep,
2022-10-traderjoe/src/LBFactory.sol::535 => uint16 _baseFactor,
2022-10-traderjoe/src/LBFactory.sol::536 => uint16 _filterPeriod,
2022-10-traderjoe/src/LBFactory.sol::537 => uint16 _decayPeriod,
2022-10-traderjoe/src/LBFactory.sol::538 => uint16 _reductionFactor,
2022-10-traderjoe/src/LBFactory.sol::539 => uint24 _variableFeeControl,
2022-10-traderjoe/src/LBFactory.sol::540 => uint16 _protocolShare,
2022-10-traderjoe/src/LBFactory.sol::541 => uint24 _maxVolatilityAccumulated
2022-10-traderjoe/src/LBFactory.sol::575 => uint136(_maxVolatilityAccumulated), // The first 112 bits are reserved for the dynamic parameters
2022-10-traderjoe/src/LBPair.sol::107 => uint24 _activeId,
2022-10-traderjoe/src/LBPair.sol::108 => uint16 _sampleLifetime,
2022-10-traderjoe/src/LBPair.sol::243 => function findFirstNonEmptyBinId(uint24 _id, bool _swapForY) external view override returns (uint24) {
2022-10-traderjoe/src/LBPair.sol::269 => amountX = _unclaimedData.decode(type(uint128).max, 0);
2022-10-traderjoe/src/LBPair.sol::270 => amountY = _unclaimedData.decode(type(uint128).max, 128);
2022-10-traderjoe/src/LBPair.sol::393 => _pair.oracleId = uint16(_updatedOracleId);
2022-10-traderjoe/src/LBPair.sol::572 => _pair.reserveX += uint112(_mintInfo.amountX);
2022-10-traderjoe/src/LBPair.sol::573 => _pair.reserveY += uint112(_mintInfo.amountY);
2022-10-traderjoe/src/LBPair.sol::624 => uint24 _id = _ids[i].safe24();
2022-10-traderjoe/src/LBPair.sol::670 => _pairInformation.reserveX = uint136(_pairReserveX);
2022-10-traderjoe/src/LBPair.sol::671 => _pairInformation.reserveY = uint136(_pairReserveY);
2022-10-traderjoe/src/LBPair.sol::679 => function increaseOracleLength(uint16 _newSize) external override {
2022-10-traderjoe/src/LBPair.sol::698 => amountX = _unclaimedData.decode(type(uint128).max, 0);
2022-10-traderjoe/src/LBPair.sol::699 => amountY = _unclaimedData.decode(type(uint128).max, 128);
2022-10-traderjoe/src/LBPair.sol::717 => _pairInformation.feesX.total -= uint128(amountX);
2022-10-traderjoe/src/LBPair.sol::720 => _pairInformation.feesY.total -= uint128(amountY);
2022-10-traderjoe/src/LBPair.sol::794 => _feeParameters.volatilityReference = uint24(
2022-10-traderjoe/src/LBPair.sol::921 => function _increaseOracle(uint16 _newSize) private {
2022-10-traderjoe/src/LBPair.sol::958 => oracleSampleLifetime = _slot.decode(type(uint16).max, _OFFSET_ORACLE_SAMPLE_LIFETIME);
2022-10-traderjoe/src/LBPair.sol::959 => oracleSize = _slot.decode(type(uint16).max, _OFFSET_ORACLE_SIZE);
2022-10-traderjoe/src/LBPair.sol::960 => oracleActiveSize = _slot.decode(type(uint16).max, _OFFSET_ORACLE_ACTIVE_SIZE);
2022-10-traderjoe/src/LBPair.sol::961 => oracleLastTimestamp = _slot.decode(type(uint40).max, _OFFSET_ORACLE_LAST_TIMESTAMP);
2022-10-traderjoe/src/LBPair.sol::962 => oracleId = _slot.decode(type(uint24).max, _OFFSET_ORACLE_ID);
2022-10-traderjoe/src/LBPair.sol::1033 => feesXTotal = _slotX.decode(type(uint128).max, 0);
2022-10-traderjoe/src/LBPair.sol::1034 => feesYTotal = _slotY.decode(type(uint128).max, 0);
2022-10-traderjoe/src/LBPair.sol::1036 => feesXProtocol = _slotX.decode(type(uint128).max, _OFFSET_PROTOCOL_FEE);
2022-10-traderjoe/src/LBPair.sol::1037 => feesYProtocol = _slotY.decode(type(uint128).max, _OFFSET_PROTOCOL_FEE);
2022-10-traderjoe/src/LBRouter.sol::127 => if (_amountInWithFees + _reserve > type(uint112).max) revert LBRouter__SwapOverflows(_activeId);
2022-10-traderjoe/src/LBRouter.sol::134 => _activeId = _LBPair.findFirstNonEmptyBinId(uint24(_activeId), _swapForY);
2022-10-traderjoe/src/LBRouter.sol::171 => if (_amountInToBin > type(uint112).max) revert LBRouter__BinReserveOverflows(_activeId);
2022-10-traderjoe/src/LBRouter.sol::179 => _activeId = _LBPair.findFirstNonEmptyBinId(uint24(_activeId), _swapForY);
2022-10-traderjoe/src/LBRouter.sol::196 => uint24 _activeId,
2022-10-traderjoe/src/LBRouter.sol::197 => uint16 _binStep
2022-10-traderjoe/src/LBRouter.sol::277 => uint16 _binStep,
2022-10-traderjoe/src/LBRouter.sol::310 => uint16 _binStep,
2022-10-traderjoe/src/interfaces/IJoePair.sol::15 => function decimals() external pure returns (uint8);
2022-10-traderjoe/src/interfaces/IJoePair.sol::44 => uint8 v,
2022-10-traderjoe/src/interfaces/IJoePair.sol::59 => event Sync(uint112 reserve0, uint112 reserve1);
2022-10-traderjoe/src/interfaces/IJoePair.sol::73 => uint112 reserve0,
2022-10-traderjoe/src/interfaces/IJoePair.sol::74 => uint112 reserve1,
2022-10-traderjoe/src/interfaces/IJoePair.sol::75 => uint32 blockTimestampLast
2022-10-traderjoe/src/interfaces/ILBFactory.sol::20 => uint24 binStep;
2022-10-traderjoe/src/interfaces/ILBFactory.sol::107 => function getPreset(uint16 binStep)
2022-10-traderjoe/src/interfaces/ILBFactory.sol::133 => uint24 activeId,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::134 => uint16 binStep
2022-10-traderjoe/src/interfaces/ILBFactory.sol::145 => uint16 binStep,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::146 => uint16 baseFactor,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::147 => uint16 filterPeriod,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::148 => uint16 decayPeriod,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::149 => uint16 reductionFactor,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::150 => uint24 variableFeeControl,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::151 => uint16 protocolShare,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::152 => uint24 maxVolatilityAccumulated,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::153 => uint16 sampleLifetime
2022-10-traderjoe/src/interfaces/ILBFactory.sol::156 => function removePreset(uint16 binStep) external;
2022-10-traderjoe/src/interfaces/ILBFactory.sol::161 => uint16 binStep,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::162 => uint16 baseFactor,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::163 => uint16 filterPeriod,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::164 => uint16 decayPeriod,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::165 => uint16 reductionFactor,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::166 => uint24 variableFeeControl,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::167 => uint16 protocolShare,
2022-10-traderjoe/src/interfaces/ILBFactory.sol::168 => uint24 maxVolatilityAccumulated
2022-10-traderjoe/src/interfaces/ILBPair.sol::18 => uint112 reserveX;
2022-10-traderjoe/src/interfaces/ILBPair.sol::19 => uint112 reserveY;
2022-10-traderjoe/src/interfaces/ILBPair.sol::40 => uint24 activeId;
2022-10-traderjoe/src/interfaces/ILBPair.sol::41 => uint136 reserveX;
2022-10-traderjoe/src/interfaces/ILBPair.sol::42 => uint136 reserveY;
2022-10-traderjoe/src/interfaces/ILBPair.sol::43 => uint16 oracleSampleLifetime;
2022-10-traderjoe/src/interfaces/ILBPair.sol::44 => uint16 oracleSize;
2022-10-traderjoe/src/interfaces/ILBPair.sol::45 => uint16 oracleActiveSize;
2022-10-traderjoe/src/interfaces/ILBPair.sol::46 => uint40 oracleLastTimestamp;
2022-10-traderjoe/src/interfaces/ILBPair.sol::47 => uint16 oracleId;
2022-10-traderjoe/src/interfaces/ILBPair.sol::64 => uint128 tokenX;
2022-10-traderjoe/src/interfaces/ILBPair.sol::65 => uint128 tokenY;
2022-10-traderjoe/src/interfaces/ILBPair.sol::101 => uint24 indexed id,
2022-10-traderjoe/src/interfaces/ILBPair.sol::253 => uint24 activeId,
2022-10-traderjoe/src/interfaces/ILBPair.sol::254 => uint16 sampleLifetime,
2022-10-traderjoe/src/interfaces/ILBRouter.sol::71 => uint24 activeId,
2022-10-traderjoe/src/interfaces/ILBRouter.sol::72 => uint16 binStep
2022-10-traderjoe/src/interfaces/ILBRouter.sol::87 => uint16 binStep,
2022-10-traderjoe/src/libraries/BitMath.sol::16 => uint8 _bit,
2022-10-traderjoe/src/libraries/FeeHelper.sol::30 => uint16 binStep;
2022-10-traderjoe/src/libraries/FeeHelper.sol::31 => uint16 baseFactor;
2022-10-traderjoe/src/libraries/FeeHelper.sol::32 => uint16 filterPeriod;
2022-10-traderjoe/src/libraries/FeeHelper.sol::33 => uint16 decayPeriod;
2022-10-traderjoe/src/libraries/FeeHelper.sol::34 => uint16 reductionFactor;
2022-10-traderjoe/src/libraries/FeeHelper.sol::35 => uint24 variableFeeControl;
2022-10-traderjoe/src/libraries/FeeHelper.sol::36 => uint16 protocolShare;
2022-10-traderjoe/src/libraries/FeeHelper.sol::37 => uint24 maxVolatilityAccumulated;
2022-10-traderjoe/src/libraries/FeeHelper.sol::38 => uint24 volatilityAccumulated;
2022-10-traderjoe/src/libraries/FeeHelper.sol::39 => uint24 volatilityReference;
2022-10-traderjoe/src/libraries/FeeHelper.sol::40 => uint24 indexRef;
2022-10-traderjoe/src/libraries/FeeHelper.sol::41 => uint40 time;
2022-10-traderjoe/src/libraries/FeeHelper.sol::48 => uint128 total;
2022-10-traderjoe/src/libraries/FeeHelper.sol::49 => uint128 protocol;
2022-10-traderjoe/src/libraries/FeeHelper.sol::59 => _fp.indexRef = uint24(_activeId);
2022-10-traderjoe/src/libraries/FeeHelper.sol::63 => _fp.volatilityReference = uint24(
2022-10-traderjoe/src/libraries/FeeHelper.sol::85 => : uint24(volatilityAccumulated);
2022-10-traderjoe/src/libraries/FeeHelper.sol::150 => fees.protocol = uint128((_fees * _fp.protocolShare) / Constants.BASIS_POINT_MAX);
2022-10-traderjoe/src/libraries/SwapHelper.sol::111 => uint112 amountInToBin,
2022-10-traderjoe/src/libraries/SwapHelper.sol::112 => uint112 amountOutOfBin
2022-10-traderjoe/src/libraries/SwapHelper.sol::119 => pair.reserveX += uint136(amountInToBin);
2022-10-traderjoe/src/libraries/SwapHelper.sol::120 => pair.reserveY -= uint136(amountOutOfBin);
2022-10-traderjoe/src/libraries/SwapHelper.sol::127 => pair.reserveX -= uint136(amountOutOfBin);
2022-10-traderjoe/src/libraries/SwapHelper.sol::128 => pair.reserveY += uint136(amountInToBin);
2022-10-traderjoe/src/libraries/TreeMath.sol::23 => uint24 _binId,
2022-10-traderjoe/src/libraries/TreeMath.sol::25 => ) internal view returns (uint24) {
2022-10-traderjoe/src/libraries/TreeMath.sol::35 => bit = current.closestBit(uint8(bit), _rightSide);
2022-10-traderjoe/src/libraries/TreeMath.sol::38 => return _getBottomId(_binId, uint24(bit));
2022-10-traderjoe/src/libraries/TreeMath.sol::47 => bit = current.closestBit(uint8(bit), _rightSide);
2022-10-traderjoe/src/libraries/TreeMath.sol::50 => _binId = _getBottomId(_binId, uint24(bit));
2022-10-traderjoe/src/libraries/TreeMath.sol::59 => bit = current.closestBit(uint8(_binId), _rightSide);
2022-10-traderjoe/src/libraries/TreeMath.sol::63 => _binId = _getBottomId(uint24(bit), current.significantBit(_rightSide));
2022-10-traderjoe/src/libraries/TreeMath.sol::104 => function _getIdsFromAbove(uint24 _id) private pure returns (uint24, uint24) {
2022-10-traderjoe/src/libraries/TreeMath.sol::113 => function _getBottomId(uint24 _branchId, uint24 _leafId) private pure returns (uint24) {
2022-10-traderjoe/src/libraries/afeCast.sol::15 => if ((y = uint248(x)) != x) revert SafeCast__Exceeds248Bits(x);{
2022-10-traderjoe/src/libraries/afeCast.sol::22 => if ((y = uint240(x)) != x) revert SafeCast__Exceeds240Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::29 => if ((y = uint232(x)) != x) revert SafeCast__Exceeds232Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::36 => if ((y = uint224(x)) != x) revert SafeCast__Exceeds224Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::43 => if ((y = uint216(x)) != x) revert SafeCast__Exceeds216Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::50 => if ((y = uint208(x)) != x) revert SafeCast__Exceeds208Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::57 => if ((y = uint200(x)) != x) revert SafeCast__Exceeds200Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::64 => if ((y = uint192(x)) != x) revert SafeCast__Exceeds192Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::71 => if ((y = uint184(x)) != x) revert SafeCast__Exceeds184Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::78 => if ((y = uint176(x)) != x) revert SafeCast__Exceeds176Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::85 => if ((y = uint168(x)) != x) revert SafeCast__Exceeds168Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::92 => if ((y = uint160(x)) != x) revert SafeCast__Exceeds160Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::99 => if ((y = uint152(x)) != x) revert SafeCast__Exceeds152Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::106 => if ((y = uint144(x)) != x) revert SafeCast__Exceeds144Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::113 => if ((y = uint136(x)) != x) revert SafeCast__Exceeds136Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::120 => if ((y = uint128(x)) != x) revert SafeCast__Exceeds128Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::127 => if ((y = uint120(x)) != x) revert SafeCast__Exceeds120Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::134 => if ((y = uint112(x)) != x) revert SafeCast__Exceeds112Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::141 => if ((y = uint104(x)) != x) revert SafeCast__Exceeds104Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::148 => if ((y = uint96(x)) != x) revert SafeCast__Exceeds96Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::155 => if ((y = uint88(x)) != x) revert SafeCast__Exceeds88Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::162 => if ((y = uint80(x)) != x) revert SafeCast__Exceeds80Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::169 => if ((y = uint72(x)) != x) revert SafeCast__Exceeds72Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::176 => if ((y = uint64(x)) != x) revert SafeCast__Exceeds64Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::183 => if ((y = uint56(x)) != x) revert SafeCast__Exceeds56Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::190 => if ((y = uint48(x)) != x) revert SafeCast__Exceeds48Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::197 => if ((y = uint40(x)) != x) revert SafeCast__Exceeds40Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::204 => if ((y = uint32(x)) != x) revert SafeCast__Exceeds32Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::211 => if ((y = uint24(x)) != x) revert SafeCast__Exceeds24Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::218 => if ((y = uint16(x)) != x) revert SafeCast__Exceeds16Bits(x);
2022-10-traderjoe/src/libraries/afeCast.sol::225 => if ((y = uint8(x)) != x) revert SafeCast__Exceeds8Bits(x);
```










### [G08] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::215 => function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::396 => function removePreset(uint16 _binStep) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::468 => function setFeeRecipient(address _feeRecipient) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::474 => function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::485 => function setFactoryLockedState(bool _locked) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::493 => function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::502 => function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::520 => function forceDecay(ILBPair _LBPair) external override onlyOwner {
2022-10-traderjoe/src/LBPair.sol::788 => function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
2022-10-traderjoe/src/LBPair.sol::792 => function forceDecay() external override onlyFactory {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::59 => function setPendingOwner(address pendingOwner_) public override onlyOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::68 => function revokePendingOwner() public override onlyOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::75 => function becomeOwner() public override onlyPendingOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::84 => function renounceOwnership() public override onlyOwner {
```



### [G09] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.15 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
2022-10-traderjoe/src/LBErrors.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/LBFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/LBPair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/LBQuoter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/LBRouter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/LBToken.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/IJoeFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/IJoePair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/IJoeRouter01.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/IJoeRouter02.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/ILBFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/ILBFlashLoanCallback.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/ILBPair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/ILBRouter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/ILBToken.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/IPendingOwnable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/interfaces/IWAVAX.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/BinHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/BitMath.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Buffer.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Constants.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Decoder.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Encoder.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/FeeHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/JoeLibrary.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Math128x128.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Math512Bits.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Oracle.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/PendingOwnable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/ReentrancyGuardUpgradeable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/SafeMath.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/Samples.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/SwapHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/TokenHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/TreeMath.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe/src/libraries/afeCast.sol::3 => pragma solidity ^0.8.0;
```



### [G10] Bitshift for divide by 2

#### Impact
When multiplying or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.
#### Findings:
```
2022-10-traderjoe/src/libraries/Oracle.sol::162 => _middle = (_low + _high) / 2;
```





### [G11] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of calldata
#### Findings:
```
2022-10-traderjoe/src/LBPair.sol::688 => function collectFees(address _account, uint256[] memory _ids)
2022-10-traderjoe/src/LBQuoter.sol::54 => function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
2022-10-traderjoe/src/LBQuoter.sol::134 => function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
2022-10-traderjoe/src/LBRouter.sol::207 => function addLiquidity(LiquidityParameters memory _liquidityParameters)
2022-10-traderjoe/src/LBRouter.sol::230 => function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)
2022-10-traderjoe/src/LBRouter.sol::656 => function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair)
2022-10-traderjoe/src/LBRouter.sol::941 => function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
2022-10-traderjoe/src/LBToken.sol::79 => function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
2022-10-traderjoe/src/interfaces/ILBFactory.sol::121 => function getAllBinSteps() external view returns (uint256[] memory presetsBinStep);
2022-10-traderjoe/src/interfaces/ILBPair.sol::207 => function pendingFees(address account, uint256[] memory ids)
2022-10-traderjoe/src/interfaces/ILBPair.sol::242 => function collectFees(address account, uint256[] memory ids) external returns (uint256 amountX, uint256 amountY);
2022-10-traderjoe/src/interfaces/ILBRouter.sol::75 => function addLiquidity(LiquidityParameters memory liquidityParameters)
2022-10-traderjoe/src/interfaces/ILBRouter.sol::79 => function addLiquidityAVAX(LiquidityParameters memory liquidityParameters)
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol::49 => function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
2022-10-traderjoe/src/libraries/FeeHelper.sol::55 => function updateVariableFeeParameters(FeeParameters memory _fp, uint256 _activeId) internal view {
2022-10-traderjoe/src/libraries/FeeHelper.sol::80 => function updateVolatilityAccumulated(FeeParameters memory _fp, uint256 _activeId) internal pure {
2022-10-traderjoe/src/libraries/FeeHelper.sol::91 => function getBaseFee(FeeParameters memory _fp) internal pure returns (uint256) {
2022-10-traderjoe/src/libraries/FeeHelper.sol::100 => function getVariableFee(FeeParameters memory _fp) internal pure returns (uint256 variableFee) {
2022-10-traderjoe/src/libraries/FeeHelper.sol::116 => function getFeeAmount(FeeParameters memory _fp, uint256 _amount) internal pure returns (uint256) {
2022-10-traderjoe/src/libraries/FeeHelper.sol::124 => function getFeeAmountFrom(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {
2022-10-traderjoe/src/libraries/FeeHelper.sol::133 => function getFeeAmountForC(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {
2022-10-traderjoe/src/libraries/FeeHelper.sol::142 => function getFeeAmountDistribution(FeeParameters memory _fp, uint256 _fees)
2022-10-traderjoe/src/libraries/FeeHelper.sol::157 => function getTotalFee(FeeParameters memory _fp) private pure returns (uint256) {
2022-10-traderjoe/src/libraries/TokenHelper.sol::74 => function _catchTransferError(bool success, bytes memory result) private pure {
```




### [G12] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done at some places, it’s not consistently done in the solution.
#### Findings:
```
2022-10-traderjoe/src/LBPair.sol::408 => tokenY.safeTransfer(_to, _amountOut);
2022-10-traderjoe/src/LBPair.sol::411 => tokenX.safeTransfer(_to, _amountOut);
2022-10-traderjoe/src/LBPair.sol::435 => tokenX.safeTransfer(_to, _amountXOut);
2022-10-traderjoe/src/LBPair.sol::436 => tokenY.safeTransfer(_to, _amountYOut);
2022-10-traderjoe/src/LBPair.sol::598 => tokenX.safeTransfer(_to, _mintInfo.amountXIn - _amountAddedPlusFee);
2022-10-traderjoe/src/LBPair.sol::603 => tokenY.safeTransfer(_to, _mintInfo.amountYIn - _amountAddedPlusFee);
2022-10-traderjoe/src/LBPair.sol::673 => tokenX.safeTransfer(_to, amountX);
2022-10-traderjoe/src/LBPair.sol::674 => tokenY.safeTransfer(_to, amountY);
2022-10-traderjoe/src/LBPair.sol::723 => tokenX.safeTransfer(_account, amountX);
2022-10-traderjoe/src/LBPair.sol::724 => tokenY.safeTransfer(_account, amountY);
2022-10-traderjoe/src/LBPair.sol::761 => tokenX.safeTransfer(_feeRecipient, amountX);
2022-10-traderjoe/src/LBPair.sol::776 => tokenY.safeTransfer(_feeRecipient, amountY);
2022-10-traderjoe/src/LBRouter.sol::338 => _token.safeTransfer(_to, amountToken);
2022-10-traderjoe/src/LBRouter.sol::632 => _token.safeTransfer(_to, _amount);
2022-10-traderjoe/src/LBRouter.sol::973 => wavax.safeTransfer(_to, _amount);
```



### [G13] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::45 => mapping(IERC20 => mapping(IERC20 => mapping(uint256 => LBPairInformation))) private _LBPairsInfo;
2022-10-traderjoe/src/LBFactory.sol::58 => mapping(IERC20 => mapping(IERC20 => bytes32)) private _availableLBPairBinSteps;

2022-10-traderjoe/src/LBPair.sol::64 => mapping(uint256 => Bin) private _bins;
2022-10-traderjoe/src/LBPair.sol::66 => mapping(uint256 => uint256)[3] private _tree;
2022-10-traderjoe/src/LBPair.sol::68 => mapping(address => bytes32) private _unclaimedFees;
2022-10-traderjoe/src/LBPair.sol::70 => mapping(address => mapping(uint256 => Debts)) private _accruedDebts;

2022-10-traderjoe/src/LBToken.sol::18 => mapping(uint256 => mapping(address => uint256)) private _balances;
2022-10-traderjoe/src/LBToken.sol::21 => mapping(address => mapping(address => bool)) private _spenderApprovals;
2022-10-traderjoe/src/LBToken.sol::24 => mapping(uint256 => uint256) private _totalSupplies;
2022-10-traderjoe/src/LBToken.sol::27 => mapping(address => EnumerableSet.UintSet) private _userIds;
```



### [G14] Using `bools` for storage incurs overhead


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::39 => bool public override creationUnlocked;
2022-10-traderjoe/src/LBToken.sol::21 => mapping(address => mapping(address => bool)) private _spenderApprovals;
```


### [G15] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable 
getter functions for deployment call data, and not adding another entry 
to the method ID table
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::25 => uint256 public constant override MAX_FEE = 0.1e18; // 10%
2022-10-traderjoe/src/LBFactory.sol::27 => uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
2022-10-traderjoe/src/LBFactory.sol::28 => uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
2022-10-traderjoe/src/LBFactory.sol::30 => uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```



### [G16] Usage of `assert()` instead of `require()`

#### Impact
Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions).require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require, unless a Panic really is intended.
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::141 => assert(_binStep == _preset.decode(type(uint16).max, _shift));
```



### [G17] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2022-10-traderjoe/src/LBQuoter.sol::123 => } catch {}
2022-10-traderjoe/src/LBQuoter.sol::203 => } catch {}
2022-10-traderjoe/src/LBToken.sol::326 => ) internal virtual {}
```






### [G18] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::265 => bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
```



### [G19] Multiplication/division by two should use bit shifting

#### Impact
<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. 
#### Findings:
```
2022-10-traderjoe/src/libraries/Oracle.sol::162 => _middle = (_low + _high) / 2;
```




