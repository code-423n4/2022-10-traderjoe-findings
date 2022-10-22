# Trader Joe v2 Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (26 instances)
2. Cache `<array>.length` (14 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (17 instances)
4. Using `bool`s for storage incurs overhead (19 instances)
5. Use `!= 0` instead of `> 0` when comparing uint (9 instances)
6. Empty blocks should be removed or emit something (3 instances)
7. Use `++i`/`--i` instead of `i++`/`i--` (3 instances)
8. Use `abi.encodePacked()` instead of `abi.encode()` (1 instance)
9. Use `private` instead of `public` for constants (4 instances)

Total 96 instances of 9 issues.

## 1. Use `calldata` instead of `memory` (26 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
LBFactory.sol::156 => function getAllBinSteps() external view override returns (uint256[] memory presetsBinStep) {

LBPair.sol::235 => function feeParameters() external view override returns (FeeHelper.FeeParameters memory) {

LBPair.sol::261 => function pendingFees(address _account, uint256[] memory _ids)

LBPair.sol::688 => function collectFees(address _account, uint256[] memory _ids) // @audit check this function

LBQuoter.sol::54 => function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)

LBQuoter.sol::134 => function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)

LBRouter.sol::207 => function addLiquidity(LiquidityParameters memory _liquidityParameters) // @audit check this function

LBRouter.sol::230 => function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters) // @audit check this function

LBRouter.sol::656 => function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair) // @audit check this function

LBRouter.sol::941 => function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)

LBToken.sol::49 => function name() public pure virtual override returns (string memory) {

LBToken.sol::55 => function symbol() public pure virtual override returns (string memory) {

LBToken.sol::79 => function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)

interfaces/IJoePair.sol::11 => function name() external pure returns (string memory);

interfaces/IJoePair.sol::13 => function symbol() external pure returns (string memory);

interfaces/IJoeRouter01.sol::155 => function getAmountsOut(uint256 amountIn, address[] calldata path) external view returns (uint256[] memory amounts);

interfaces/IJoeRouter01.sol::157 => function getAmountsIn(uint256 amountOut, address[] calldata path) external view returns (uint256[] memory amounts);

interfaces/ILBFactory.sol::121 => function getAllBinSteps() external view returns (uint256[] memory presetsBinStep);

interfaces/ILBPair.sol::201 => function feeParameters() external view returns (FeeHelper.FeeParameters memory);

interfaces/ILBPair.sol::207 => function pendingFees(address account, uint256[] memory ids)

interfaces/ILBPair.sol::242 => function collectFees(address account, uint256[] memory ids) external returns (uint256 amountX, uint256 amountY);

interfaces/ILBRouter.sol::75 => function addLiquidity(LiquidityParameters memory liquidityParameters)

interfaces/ILBRouter.sol::79 => function addLiquidityAVAX(LiquidityParameters memory liquidityParameters)

interfaces/ILBToken.sol::21 => function name() external view returns (string memory);

interfaces/ILBToken.sol::23 => function symbol() external view returns (string memory);

interfaces/ILBToken.sol::27 => function balanceOfBatch(address[] memory accounts, uint256[] memory ids)
```

## 2. Cache `<array>.length` (14 instances)

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {

LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {

LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {

LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {

LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {

LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {

LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {

LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {

LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {

LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {

LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (17 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
LBFactory.sol::165 => for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

LBFactory.sol::195 => for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {

LBPair.sol::929 => for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {

LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {

LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {

LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {

LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {

LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {

LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {

LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {

LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {

LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {

LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```

## 4. Using `bool`s for storage incurs overhead (19 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
LBFactory.sol::39 => bool public override creationUnlocked;

LBFactory.sol::316 => bool _ignored

LBQuoter.sol::102 => bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i + 1];

LBQuoter.sol::179 => bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i];

LBQuoter.sol::237 => bool _swapForY

LBRouter.sol::93 => bool _swapForY

LBRouter.sol::151 => bool _swapForY

LBRouter.sol::320 => bool _isAVAXTokenY = IERC20(wavax) == _LBPair.tokenY();

LBRouter.sol::798 => bool _swapForY = _tokenNext == ILBPair(_pair).tokenY();

LBRouter.sol::848 => bool _swapForY = _tokenNext == ILBPair(_pair).tokenY();

LBToken.sol::256 => bool _approved

interfaces/IJoeRouter01.sol::72 => bool approveMax,

interfaces/IJoeRouter01.sol::85 => bool approveMax,

interfaces/IJoeRouter02.sol::26 => bool approveMax,

interfaces/ILBFactory.sol::22 => bool createdByOwner;

interfaces/ILBFactory.sol::23 => bool ignoredForRouting;

interfaces/ILBFactory.sol::141 => bool ignored

interfaces/ILBRouter.sol::59 => bool swapForY

interfaces/ILBRouter.sol::65 => bool swapForY
```

## 5. Use `!= 0` instead of `> 0` when comparing uint (9 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
LBFactory.sol::161 => if (_nbPresets > 0) {

LBFactory.sol::191 => if (_nbAvailable > 0) {

LBQuoter.sol::79 => if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {

LBQuoter.sol::82 => if (reserveIn > 0 && reserveOut > 0) {

LBQuoter.sol::99 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {

LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {

LBQuoter.sol::157 => if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {

LBQuoter.sol::160 => if (reserveIn > 0 && reserveOut > quote.amounts[i]) {

LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
```

## 6. Empty blocks should be removed or emit something (3 instances)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
LBQuoter.sol::123 => } catch {}

LBQuoter.sol::203 => } catch {}

LBToken.sol::326 => ) internal virtual {}
```

## 7. Use `++i`/`--i` instead of `i++`/`i--` (3 instances)

Using `++i`/`--i` saves 6 gas per loop.

```solidity
LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {

LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {

LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
```

## 8. Use `abi.encodePacked()` instead of `abi.encode()` (1 instance)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
LBFactory.sol::265 => bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
```

## 9. Use `private` instead of `public` for constants (4 instances)

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
LBFactory.sol::25 => uint256 public constant override MAX_FEE = 0.1e18; // 10%

LBFactory.sol::27 => uint256 public constant override MIN_BIN_STEP = 1; // 0.01%

LBFactory.sol::28 => uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons

LBFactory.sol::30 => uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```
