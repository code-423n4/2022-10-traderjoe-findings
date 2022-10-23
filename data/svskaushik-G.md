# Gas Findings

## [G-01] Cache Array Length Outside of Loop
#### Impact
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack. Caching the array length in the stack saves around 3 gas per iteration.
#### Findings:
```solidity
2022-10-traderjoe\src\LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
2022-10-traderjoe\src\LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
2022-10-traderjoe\src\LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
2022-10-traderjoe\src\LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```
#### Recommendation
Store the array’s length in a variable before the for-loop.

## [G-02] `++i` costs less gas compared to `i++` or `i += 1`
#### Impact
`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.
#### Findings:
```solidity
2022-10-traderjoe\src\LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {
2022-10-traderjoe\src\LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {
2022-10-traderjoe\src\LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
```
#### Recommendation
Use `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`.

## [G-03] `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for/while` loops
#### Impact
This saves 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

```solidity
2022-10-traderjoe\src\LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {
2022-10-traderjoe\src\LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {
2022-10-traderjoe\src\LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {
2022-10-traderjoe\src\LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
2022-10-traderjoe\src\LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
2022-10-traderjoe\src\LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
2022-10-traderjoe\src\LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
2022-10-traderjoe\src\LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
2022-10-traderjoe\src\LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
2022-10-traderjoe\src\LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```

## [G-04] Use Shift Right/Left instead of Division/Multiplication if possible
#### Impact
A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.
#### Findings:
```solidity
2022-10-traderjoe\src\libraries\Oracle.sol::162 => _middle = (_low + _high) / 2;
```
#### Recommendation
Use SHR/SHL.
Bad
```solidity
uint256 b = a / 2
uint256 c = a / 4;
uint256 d = a * 8;
```
Good
```solidity
uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;
```

## [G-05] Use recent versions of solidity in place of unlocked pragma.
#### Impact
Contracts in scope use `pragma solidity ^0.X.Y` allowing wide enough range of versions.
#### Findings:
```solidity
2022-10-traderjoe\src\LBErrors.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBPair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBQuoter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBRouter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBToken.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoeFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoePair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoeRouter01.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoeRouter02.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBFlashLoanCallback.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBPair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBRouter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBToken.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IPendingOwnable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IWAVAX.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\BinHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\BitMath.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Buffer.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Constants.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Decoder.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Encoder.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\FeeDistributionHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\FeeHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\JoeLibrary.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Math128x128.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Math512Bits.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Oracle.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\PendingOwnable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\ReentrancyGuardUpgradeable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\SafeCast.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\SafeMath.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\Samples.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\SwapHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\TokenHelper.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\libraries\TreeMath.sol::3 => pragma solidity ^0.8.0;
```
#### Recommendation
Consider locking compiler version, for example `pragma solidity 0.8.6`. Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings. Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

## [G-06] Use `calldata` instead of `memory` for read-only arguments in `external` functions.
#### Impact
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, eliminates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it’s still valid for implementation contracs to use `calldata` arguments instead.
#### Findings:
```solidity
2022-10-traderjoe\src\interfaces\ILBPair.sol::242 => function collectFees(address account, uint256[] memory ids) external returns (uint256 amountX, uint256 amountY);
```
#### Recommendation
Use `calldata` instead of `memory`.

## [G-07] Use `storage` instead of `memory` for structs/arrays.
#### Impact
When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.
#### Findings:
```solidity
2022-10-traderjoe\src\LBFactory.sol::197 => LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];
2022-10-traderjoe\src\LBFactory.sol::320 => LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
2022-10-traderjoe\src\LBPair.sol::283 => Bin memory _bin = _bins[_id];
2022-10-traderjoe\src\LBPair.sol::327 => Bin memory _bin = _bins[_pair.activeId];
2022-10-traderjoe\src\LBPair.sol::498 => Bin memory _bin = _bins[_mintInfo.id];
2022-10-traderjoe\src\LBPair.sol::706 => Bin memory _bin = _bins[_id];
2022-10-traderjoe\src\LBPair.sol::817 => Bin memory _bin = _bins[_id];
2022-10-traderjoe\src\LBPair.sol::850 => Debts memory _debts = _accruedDebts[_account][_id];
```
#### Recommendation
Use `storage` instead of `memory` for findings above

## [G-08] `x += y` costs more gas than `x = x + y` for state variables.
#### Impact
Same thing applies for subtraction
#### Findings:
```solidity
2022-10-traderjoe\src\LBPair.sol::452 => _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
2022-10-traderjoe\src\LBPair.sol::453 => _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
2022-10-traderjoe\src\LBToken.sol::211 => _totalSupplies[_id] += _amount;
2022-10-traderjoe\src\LBToken.sol::241 => _totalSupplies[_id] -= _amount;
```
#### Recommendation
Use `x = x + y` instead of `x += y`