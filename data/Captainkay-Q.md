
Issues found:

## Unspecific Compiler Version Pragma
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter02.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFlashLoanCallback.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IPendingOwnable.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IWAVAX.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Buffer.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Constants.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Decoder.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Encoder.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeMath.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TokenHelper.sol::3 => pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol::3 => pragma solidity ^0.8.0;

## Cache Array Length Outside of Loop
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::481 => if (_ids.length == 0 || _ids.length != _distributionX.length || _ids.length != _distributionY.length)
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {

## Use != 0 instead of > 0 for Unsigned Integer Comparison
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol::161 => if (_nbPresets > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol::191 => if (_nbAvailable > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::79 => if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::82 => if (reserveIn > 0 && reserveOut > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::99 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::154 => for (uint256 i = swapLength; i > 0; i--) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::157 => if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::160 => if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol::176 => if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol::71 => for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta > 0; delta >>= 1) {

## Use immutable for OpenZeppelin AccessControl's Roles Declarations
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol::265 => bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::657 => let slot := keccak256(0, 64)
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::1001 => _data := sload(keccak256(0, 64))

## Long Revert Strings
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol::6 => import "openzeppelin/utils/structs/EnumerableSet.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::12 => import "./libraries/FeeDistributionHelper.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::15 => import "./libraries/ReentrancyGuardUpgradeable.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol::21 => import "./interfaces/ILBFlashLoanCallback.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol::5 => import "openzeppelin/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol::5 => import "openzeppelin/utils/structs/EnumerableSet.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol::5 => import "openzeppelin/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol::5 => import "openzeppelin/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IWAVAX.sol::5 => import "openzeppelin/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol::5 => import "openzeppelin/token/ERC20/IERC20.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol::70 => // The "delta >>= 1" part is equivalent to "delta /= 2", but shifting bits is faster.
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol::6 => import "../interfaces/IPendingOwnable.sol";
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TokenHelper.sol::5 => import "openzeppelin/token/ERC20/IERC20.sol";

## Use Shift Right/Left instead of Division/Multiplication if possible
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol::162 => _middle = (_low + _high) / 2;