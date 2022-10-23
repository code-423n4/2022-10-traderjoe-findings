## [G001] >= costs less gas than >

compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

#### Instances:
```
2022-10-traderjoe/src/LBFactory.sol::161 => if (_nbPresets > 0) {
2022-10-traderjoe/src/LBFactory.sol::191 => if (_nbAvailable > 0) {
2022-10-traderjoe/src/LBPair.sol::749 => if (_feesXProtocol > 1) {
2022-10-traderjoe/src/LBPair.sol::764 => if (_feesYProtocol > 1) {
2022-10-traderjoe/src/LBQuoter.sol::82 => if (reserveIn > 0 && reserveOut > 0) {
2022-10-traderjoe/src/LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {
2022-10-traderjoe/src/LBQuoter.sol::157 => if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
2022-10-traderjoe/src/LBQuoter.sol::160 => if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
2022-10-traderjoe/src/LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
```

## [G002] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

#### Instances:
```
2022-10-traderjoe/src/interfaces/ILBPair.sol::242 => function collectFees(address account, uint256[] memory ids) external returns (uint256 amountX, uint256 amountY);

2022-10-traderjoe/src/LBRouter.sol::274-284
    function removeLiquidity(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint16 _binStep,
        uint256 _amountXMin,
        uint256 _amountYMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::308-317
	function removeLiquidityAVAX(
        IERC20 _token,
        uint16 _binStep,
        uint256 _amountTokenMin,
        uint256 _amountAVAXMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::357-359
	function swapExactTokensForTokens(
        uint256 _amountIn,
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::377-384
function swapExactTokensForAVAX(
        uint256 _amountIn,
        uint256 _amountOutMinAVAX,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::407-413
	function swapExactAVAXForTokens(
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::431-438
    function swapTokensForExactTokens(
        uint256 _amountOut,
        uint256 _amountInMax,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::459-466
    function swapTokensForExactAVAX(
        uint256 _amountAVAXOut,
        uint256 _amountInMax,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::493-499
    function swapAVAXForExactTokens(
        uint256 _amountOut,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::531-538
    function swapExactTokensForTokensSupportingFeeOnTransferTokens(
        uint256 _amountIn,
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )
2022-10-traderjoe/src/LBRouter.sol::561-568
    function swapExactTokensForAVAXSupportingFeeOnTransferTokens(
        uint256 _amountIn,
        uint256 _amountOutMinAVAX,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::594-600
    function swapExactAVAXForTokensSupportingFeeOnTransferTokens(
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

```

## [G003] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

#### Instances:
```
2022-10-traderjoe/src/LBPair.sol::227 => cumulativeId += _activeId * _deltaT;
2022-10-traderjoe/src/LBPair.sol::228 => cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
2022-10-traderjoe/src/LBPair.sol::287 => amountX += _amountX;
2022-10-traderjoe/src/LBPair.sol::288 => amountY += _amountY;
2022-10-traderjoe/src/LBPair.sol::336 => _amountIn -= _amountInToBin + _fees.total;
2022-10-traderjoe/src/LBPair.sol::337 => _amountOut += _amountOutOfBin;
2022-10-traderjoe/src/LBPair.sol::452 => _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
2022-10-traderjoe/src/LBPair.sol::453 => _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
2022-10-traderjoe/src/LBPair.sol::539 => _mintInfo.amountX -= _fees.total;
2022-10-traderjoe/src/LBPair.sol::540 => _mintInfo.activeFeeX += _fees.total;
2022-10-traderjoe/src/LBPair.sol::550 => _mintInfo.amountY -= _fees.total;
2022-10-traderjoe/src/LBPair.sol::551 => _mintInfo.activeFeeY += _fees.total;
2022-10-traderjoe/src/LBPair.sol::572 => _pair.reserveX += uint112(_mintInfo.amountX);
2022-10-traderjoe/src/LBPair.sol::573 => _pair.reserveY += uint112(_mintInfo.amountY);
2022-10-traderjoe/src/LBPair.sol::575 => _mintInfo.amountXAddedToPair += _mintInfo.amountX;
2022-10-traderjoe/src/LBPair.sol::576 => _mintInfo.amountYAddedToPair += _mintInfo.amountY;
2022-10-traderjoe/src/LBPair.sol::639 => amountY += _amountY;
2022-10-traderjoe/src/LBPair.sol::640 => _reserveY -= _amountY;
2022-10-traderjoe/src/LBPair.sol::641 => _pairReserveY -= _amountY;
2022-10-traderjoe/src/LBPair.sol::646 => amountX += _amountX;
2022-10-traderjoe/src/LBPair.sol::647 => _reserveX -= _amountX;
2022-10-traderjoe/src/LBPair.sol::648 => _pairReserveX -= _amountX;
2022-10-traderjoe/src/LBPair.sol::711 => amountX += _amountX;
2022-10-traderjoe/src/LBPair.sol::712 => amountY += _amountY;
2022-10-traderjoe/src/LBPair.sol::717 => _pairInformation.feesX.total -= uint128(amountX);
2022-10-traderjoe/src/LBPair.sol::720 => _pairInformation.feesY.total -= uint128(amountY);
2022-10-traderjoe/src/LBPair.sol::751 => _feesXTotal -= amountX;
2022-10-traderjoe/src/LBPair.sol::766 => _feesYTotal -= amountY;
2022-10-traderjoe/src/LBRouter.sol::128 => amountIn += _amountInWithFees;
2022-10-traderjoe/src/LBRouter.sol::129 => feesIn += _fee;
2022-10-traderjoe/src/LBRouter.sol::130 => _amountOut -= _amountOutOfBin;
2022-10-traderjoe/src/LBRouter.sol::173 => _amountIn -= _amountInToBin + _fees.total;
2022-10-traderjoe/src/LBRouter.sol::174 => feesIn += _fees.total;
2022-10-traderjoe/src/LBRouter.sol::175 => amountOut += _amountOutOfBin;
2022-10-traderjoe/src/LBToken.sol::211 => _totalSupplies[_id] += _amount;
2022-10-traderjoe/src/LBToken.sol::241 => _totalSupplies[_id] -= _amount;
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol::42 => _pairFees.protocol += _fees.protocol;
2022-10-traderjoe/src/libraries/Math128x128.sol::77 => result += delta;
2022-10-traderjoe/src/libraries/Math512Bits.sol::71 => result += prod1 << (256 - offset);
2022-10-traderjoe/src/libraries/Math512Bits.sol::99 => if (mulmod(x, y, 1 << offset) != 0) result += 1;
2022-10-traderjoe/src/libraries/Math512Bits.sol::159 => if (mulmod(x, 1 << offset, denominator) != 0) result += 1;
2022-10-traderjoe/src/libraries/SwapHelper.sol::88 => pairFees.total += fees.total;
2022-10-traderjoe/src/libraries/SwapHelper.sol::91 => pairFees.protocol += fees.protocol;
2022-10-traderjoe/src/libraries/SwapHelper.sol::95 => bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);
2022-10-traderjoe/src/libraries/SwapHelper.sol::97 => bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);
2022-10-traderjoe/src/libraries/SwapHelper.sol::115 => bin.reserveX += amountInToBin;
2022-10-traderjoe/src/libraries/SwapHelper.sol::118 => bin.reserveY -= amountOutOfBin;
2022-10-traderjoe/src/libraries/SwapHelper.sol::119 => pair.reserveX += uint136(amountInToBin);
2022-10-traderjoe/src/libraries/SwapHelper.sol::120 => pair.reserveY -= uint136(amountOutOfBin);
2022-10-traderjoe/src/libraries/SwapHelper.sol::123 => bin.reserveY += amountInToBin;
2022-10-traderjoe/src/libraries/SwapHelper.sol::126 => bin.reserveX -= amountOutOfBin;
2022-10-traderjoe/src/libraries/SwapHelper.sol::127 => pair.reserveX -= uint136(amountOutOfBin);
2022-10-traderjoe/src/libraries/SwapHelper.sol::128 => pair.reserveY += uint136(amountInToBin);
```

## [G004] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### Instances:
```
2022-10-traderjoe/src/LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {
2022-10-traderjoe/src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe/src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
```

## [G005] Using `bools` for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.Refer https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 . Use uint256(1) and uint256(2) for true/false

#### Instances:
```
2022-10-traderjoe/src/LBFactory.sol::39 => bool public 
2022-10-traderjoe/src/LBToken.sol::21 => mapping(address => mapping(address => bool)) private _spenderApprovals;
```

## [G006] abi.encode() is less efficient than abi.encodePacked()

#### Instances:
```
2022-10-traderjoe/src/LBFactory.sol::265 => bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
```

## [G007] Don't use SafeMath by using the solidity version is 0.8.0 or greater

Version 0.8.0 introduces internal overflow checks, so using SafeMath is redundant and adds overhead

#### Instances:
```
2022-10-traderjoe/src/LBPair.sol::33 => using SafeMath for uint256;
2022-10-traderjoe/src/libraries/FeeHelper.sol::14 => using SafeMath for uint256;
2022-10-traderjoe/src/libraries/SafeMath.sol::8 => library SafeMath {
2022-10-traderjoe/src/libraries/SwapHelper.sol::19 => using SafeMath for uint256;
```

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)

#### Instances:
```
2022-10-traderjoe/src/LBQuoter.sol::123 => } catch {}
2022-10-traderjoe/src/LBQuoter.sol::203 => } catch {}
2022-10-traderjoe/src/LBToken.sol::326 => ) internal virtual {}
```

## [G008] Functions with access control cheaper if payable

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

#### Instances:
```
2022-10-traderjoe/src/LBFactory.sol::215 => function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::317 => ) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::350 => ) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::396 => function removePreset(uint16 _binStep) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::434 => ) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::468 => function setFeeRecipient(address _feeRecipient) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::474 => function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::485 => function setFactoryLockedState(bool _locked) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::493 => function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::502 => function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
2022-10-traderjoe/src/LBFactory.sol::520 => function forceDecay(ILBPair _LBPair) external override onlyOwner {
2022-10-traderjoe/src/LBPair.sol::110 => ) external override onlyFactory {
2022-10-traderjoe/src/LBPair.sol::788 => function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
2022-10-traderjoe/src/LBPair.sol::792 => function forceDecay() external override onlyFactory {
2022-10-traderjoe/src/LBRouter.sol::626 => ) external override onlyFactoryOwner {
2022-10-traderjoe/src/LBRouter.sol::647 => ) external override onlyFactoryOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::59 => function setPendingOwner(address pendingOwner_) public override onlyOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::68 => function revokePendingOwner() public override onlyOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::75 => function becomeOwner() public override onlyPendingOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::84 => function renounceOwnership() public override onlyOwner {
```

## [G009] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

#### Instances:
```
2022-10-traderjoe/src/LBFactory.sol::237 => uint24 _activeId
2022-10-traderjoe/src/LBFactory.sol::238 => uint16 _binStep
		
2022-10-traderjoe/src/LBFactory.sol::341-349	
		uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated,
        uint16 _sampleLifetime

2022-10-traderjoe/src/LBFactory.sol::534-541
		uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated

2022-10-traderjoe/src/LBFactory.sol::107-108
        uint24 _activeId,
        uint16 _sampleLifetime,

2022-10-traderjoe/src/LBFactory.sol::921 => function _increaseOracle(uint16 _newSize) private {

2022-10-traderjoe/src/LBRouter.sol::196-197
		uint24 _activeId,
        uint16 _binStep

2022-10-traderjoe/src/LBRouter.sol::227	=> uint16 _binStep,
```

## [G010] Variable Packing

solidity contracts have contiguous 32 byte (256 bit) slots used for storage. When we arrange variables so multiple fit in a single slot, it is called variable packing.
Variable packing is like a game of Tetris. If a variable we are trying to pack exceeds the 32 byte limit of the current slot, it gets stored in a new one. We must figure out which variables fit together the best to minimize wasted space.

Because each storage slot costs gas, variable packing helps us optimize our gas usage by reducing the number of slots our contract requires.

#### Instances:

```
2022-10-traderjoe/src/LBRouter.sol::274-284
    function removeLiquidity(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint16 _binStep,
        uint256 _amountXMin,
        uint256 _amountYMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::308-317
	function removeLiquidityAVAX(
        IERC20 _token,
        uint16 _binStep,
        uint256 _amountTokenMin,
        uint256 _amountAVAXMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::357-359
	function swapExactTokensForTokens(
        uint256 _amountIn,
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::377-384
function swapExactTokensForAVAX(
        uint256 _amountIn,
        uint256 _amountOutMinAVAX,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::407-413
	function swapExactAVAXForTokens(
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::431-438
    function swapTokensForExactTokens(
        uint256 _amountOut,
        uint256 _amountInMax,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::459-466
    function swapTokensForExactAVAX(
        uint256 _amountAVAXOut,
        uint256 _amountInMax,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::493-499
    function swapAVAXForExactTokens(
        uint256 _amountOut,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::531-538
    function swapExactTokensForTokensSupportingFeeOnTransferTokens(
        uint256 _amountIn,
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )
2022-10-traderjoe/src/LBRouter.sol::561-568
    function swapExactTokensForAVAXSupportingFeeOnTransferTokens(
        uint256 _amountIn,
        uint256 _amountOutMinAVAX,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    )

2022-10-traderjoe/src/LBRouter.sol::594-600
    function swapExactAVAXForTokensSupportingFeeOnTransferTokens(
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )
```