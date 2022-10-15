

### [L01] require() should be used instead of assert()


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::141 => assert(_binStep == _preset.decode(type(uint16).max, _shift));
```





### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::66 => flashLoanFee = _flashLoanFee;
2022-10-traderjoe/src/LBFactory.sol::367 => _presets[_binStep] = _preset;
2022-10-traderjoe/src/LBFactory.sol::479 => flashLoanFee = _flashLoanFee;
2022-10-traderjoe/src/LBPair.sol::92 => factory = _factory;
2022-10-traderjoe/src/LBPair.sol::116 => tokenX = _tokenX;
2022-10-traderjoe/src/LBPair.sol::117 => tokenY = _tokenY;
2022-10-traderjoe/src/LBQuoter.sol::45 => routerV2 = _routerV2;
2022-10-traderjoe/src/LBQuoter.sol::46 => factoryV1 = _factoryV1;
2022-10-traderjoe/src/LBQuoter.sol::47 => factoryV2 = _factoryV2;
2022-10-traderjoe/src/LBQuoter.sol::63 => quote.route = _route;
2022-10-traderjoe/src/LBRouter.sol::57 => factory = _factory;
2022-10-traderjoe/src/LBRouter.sol::58 => oldFactory = _oldFactory;
2022-10-traderjoe/src/LBRouter.sol::59 => wavax = _wavax;
2022-10-traderjoe/src/LBRouter.sol::775 => amountOut = _amountIn;
2022-10-traderjoe/src/LBRouter.sol::779 => _pair = _pairs[i];
```

### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-10-traderjoe/src/LBPair.sol::104 => function initialize(
2022-10-traderjoe/src/interfaces/IJoePair.sol::99 => function initialize(
2022-10-traderjoe/src/interfaces/ILBPair.sol::250 => function initialize(
2022-10-traderjoe/src/libraries/Oracle.sol::130 => function initialize(
2022-10-traderjoe/src/libraries/Samples.sol::90 => function initialized(
```






### [L04] `_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
_mint() is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and solmate have versions of this function
#### Findings:
```
2022-10-traderjoe/src/LBPair.sol::579 => _mint(_to, _mintInfo.id, _liquidity);
```


### [L05] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
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



#### Non-Critical Issues


### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2022-10-traderjoe/src/LBPair.sol::236 => return _feeParameters;
2022-10-traderjoe/src/LBRouter.sol::921 => return _LBPair;
2022-10-traderjoe/src/LBToken.sol::50 => return _name;
2022-10-traderjoe/src/LBToken.sol::56 => return _symbol;
2022-10-traderjoe/src/libraries/PendingOwnable.sol::48 => return _owner;
2022-10-traderjoe/src/libraries/PendingOwnable.sol::54 => return _pendingOwner;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings



#### Findings:
```
2022-10-traderjoe/src/libraries/TokenHelper.sol::78 => revert(add(32, result), mload(result))
```



### [N03] constants should be defined rather than using magic numbers


#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::25 => uint256 public constant override MAX_FEE = 0.1e18; // 10%
2022-10-traderjoe/src/LBFactory.sol::30 => uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
2022-10-traderjoe/src/LBFactory.sol::564 => uint256 _maxVariableFee = (_prod * _prod * _variableFeeControl) / 100;
2022-10-traderjoe/src/LBPair.sol::77 => uint256 private constant _OFFSET_PROTOCOL_FEE = 128;
2022-10-traderjoe/src/LBPair.sol::83 => uint256 private constant _OFFSET_ORACLE_LAST_TIMESTAMP = 184;
2022-10-traderjoe/src/LBPair.sol::269 => amountX = _unclaimedData.decode(type(uint128).max, 0);
2022-10-traderjoe/src/LBPair.sol::270 => amountY = _unclaimedData.decode(type(uint128).max, 128);
2022-10-traderjoe/src/LBPair.sol::698 => amountX = _unclaimedData.decode(type(uint128).max, 0);
2022-10-traderjoe/src/LBPair.sol::699 => amountY = _unclaimedData.decode(type(uint128).max, 128);
2022-10-traderjoe/src/LBPair.sol::891 => uint256 amountY = _unclaimedData.decode(type(uint128).max, 128);
2022-10-traderjoe/src/LBPair.sol::899 => _unclaimedFees[_user] = bytes32((amountY << 128) | amountX);
2022-10-traderjoe/src/LBQuoter.sol::85 => (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
2022-10-traderjoe/src/LBQuoter.sol::89 => quote.fees[i] = 0.003e18; // 0.3%
2022-10-traderjoe/src/LBQuoter.sol::121 => quote.fees[i] = (fees * 1e18) / quote.amounts[i]; // fee percentage in amountIn
2022-10-traderjoe/src/LBQuoter.sol::163 => (JoeLibrary.quote(quote.virtualAmountsWithoutSlippage[i], reserveOut, reserveIn) * 1000) /
2022-10-traderjoe/src/LBQuoter.sol::166 => quote.fees[i - 1] = 0.003e18; // 0.3%
2022-10-traderjoe/src/LBQuoter.sol::201 => quote.fees[i - 1] = (fees * 1e18) / quote.amounts[i - 1]; // fee percentage in amountIn
2022-10-traderjoe/src/LBRouter.sol::725 => amountsIn[i - 1] = (_reserveIn * amountOut_ * 1_000) / (_reserveOut - amountOut_ * 997) + 1;
2022-10-traderjoe/src/LBRouter.sol::791 => amountOut = (_reserve1 * amountOut * 997) / (_reserve0 * 1_000 + amountOut * 997);
2022-10-traderjoe/src/LBRouter.sol::794 => amountOut = (_reserve0 * amountOut * 997) / (_reserve1 * 1_000 + amountOut * 997);
2022-10-traderjoe/src/LBRouter.sol::891 => uint256 _amountOut = (_reserve1 * (_balance - _reserve0) * 997) / (_balance * 1_000);
2022-10-traderjoe/src/LBRouter.sol::896 => uint256 _amountOut = (_reserve0 * (_balance - _reserve1) * 997) / (_balance * 1_000);
2022-10-traderjoe/src/libraries/BitMath.sol::71 => if (x >= 1 << 128) {
2022-10-traderjoe/src/libraries/BitMath.sol::72 => x >>= 128;
2022-10-traderjoe/src/libraries/BitMath.sol::73 => msb = 128;
2022-10-traderjoe/src/libraries/BitMath.sol::110 => if (x << 128 != 0) {
2022-10-traderjoe/src/libraries/BitMath.sol::111 => x <<= 128;
2022-10-traderjoe/src/libraries/BitMath.sol::112 => lsb = 128;
2022-10-traderjoe/src/libraries/Constants.sol::9 => uint256 internal constant SCALE_OFFSET = 128;
2022-10-traderjoe/src/libraries/Constants.sol::12 => uint256 internal constant PRECISION = 1e18;
2022-10-traderjoe/src/libraries/FeeHelper.sol::107 => variableFee = (_prod * _prod * _fp.variableFeeControl) / 100;
2022-10-traderjoe/src/libraries/JoeLibrary.sol::37 => uint256 amountInWithFee = amountIn * 997;
2022-10-traderjoe/src/libraries/JoeLibrary.sol::38 => uint256 numerator = amountInWithFee * reserveOut;
2022-10-traderjoe/src/libraries/JoeLibrary.sol::39 => uint256 denominator = reserveIn * 1000 + amountInWithFee;
2022-10-traderjoe/src/libraries/JoeLibrary.sol::51 => uint256 numerator = reserveIn * amountOut * 1000;
2022-10-traderjoe/src/libraries/JoeLibrary.sol::52 => uint256 denominator = (reserveOut - amountOut) * 997;
2022-10-traderjoe/src/libraries/Math128x128.sol::108 => if (absY < 0x100000) {
2022-10-traderjoe/src/libraries/Math128x128.sol::118 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::120 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::122 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::124 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::126 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::128 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::130 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::132 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::134 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::136 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::138 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::140 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::142 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::144 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::146 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::148 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::150 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::152 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::154 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::156 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::158 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::160 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::162 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::164 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::166 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::168 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::170 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::172 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::174 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::176 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::178 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::180 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::181 => if and(absY, 0x10000) {
2022-10-traderjoe/src/libraries/Math128x128.sol::182 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::184 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::186 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::188 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::190 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::192 => pow := shr(128, mul(pow, pow))
2022-10-traderjoe/src/libraries/Math128x128.sol::194 => result := shr(128, mul(result, pow))
2022-10-traderjoe/src/libraries/Math512Bits.sol::235 => uint256 inverse = (3 * denominator) ^ 2;
2022-10-traderjoe/src/libraries/Math512Bits.sol::239 => inverse *= 2 - denominator * inverse; // inverse mod 2^8
2022-10-traderjoe/src/libraries/Math512Bits.sol::240 => inverse *= 2 - denominator * inverse; // inverse mod 2^16
2022-10-traderjoe/src/libraries/Math512Bits.sol::241 => inverse *= 2 - denominator * inverse; // inverse mod 2^32
2022-10-traderjoe/src/libraries/Math512Bits.sol::242 => inverse *= 2 - denominator * inverse; // inverse mod 2^64
2022-10-traderjoe/src/libraries/Math512Bits.sol::243 => inverse *= 2 - denominator * inverse; // inverse mod 2^128
2022-10-traderjoe/src/libraries/Math512Bits.sol::244 => inverse *= 2 - denominator * inverse; // inverse mod 2^256
```



### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
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



### [N05] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::49 => bytes32 private _availablePresets;
```



### [N06] Use a more recent version of solidity

#### Impact
se a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions
#### Findings:
```
2022-10-traderjoe/src/LBFactory.sol::21 => using SafeCast for uint256;
2022-10-traderjoe/src/LBFactory.sol::22 => using Decoder for bytes32;
2022-10-traderjoe/src/LBFactory.sol::23 => using EnumerableSet for EnumerableSet.AddressSet;
2022-10-traderjoe/src/LBPair.sol::30 => using Math512Bits for uint256;
2022-10-traderjoe/src/LBPair.sol::31 => using TreeMath for mapping(uint256 => uint256)[3];
2022-10-traderjoe/src/LBPair.sol::32 => using SafeCast for uint256;
2022-10-traderjoe/src/LBPair.sol::33 => using SafeMath for uint256;
2022-10-traderjoe/src/LBPair.sol::34 => using TokenHelper for IERC20;
2022-10-traderjoe/src/LBPair.sol::35 => using FeeHelper for FeeHelper.FeeParameters;
2022-10-traderjoe/src/LBPair.sol::36 => using SwapHelper for Bin;
2022-10-traderjoe/src/LBPair.sol::37 => using Decoder for bytes32;
2022-10-traderjoe/src/LBPair.sol::38 => using FeeDistributionHelper for FeeHelper.FeesDistribution;
2022-10-traderjoe/src/LBPair.sol::39 => using Oracle for bytes32[65_535];
2022-10-traderjoe/src/LBQuoter.sol::18 => using Math512Bits for uint256;
2022-10-traderjoe/src/LBRouter.sol::22 => using TokenHelper for IERC20;
2022-10-traderjoe/src/LBRouter.sol::23 => using TokenHelper for IWAVAX;
2022-10-traderjoe/src/LBRouter.sol::24 => using FeeHelper for FeeHelper.FeeParameters;
2022-10-traderjoe/src/LBRouter.sol::25 => using Math512Bits for uint256;
2022-10-traderjoe/src/LBRouter.sol::26 => using SwapHelper for ILBPair.Bin;
2022-10-traderjoe/src/LBToken.sol::15 => using EnumerableSet for EnumerableSet.UintSet;
2022-10-traderjoe/src/libraries/BinHelper.sol::12 => using Math128x128 for uint256;
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol::17 => using TokenHelper for IERC20;
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol::18 => using SafeCast for uint256;
2022-10-traderjoe/src/libraries/FeeHelper.sol::13 => using SafeCast for uint256;
2022-10-traderjoe/src/libraries/FeeHelper.sol::14 => using SafeMath for uint256;
2022-10-traderjoe/src/libraries/Math128x128.sol::14 => using Math512Bits for uint256;
2022-10-traderjoe/src/libraries/Math128x128.sol::15 => using BitMath for uint256;
2022-10-traderjoe/src/libraries/Math512Bits.sol::12 => using BitMath for uint256;
2022-10-traderjoe/src/libraries/Oracle.sol::13 => using Samples for bytes32;
2022-10-traderjoe/src/libraries/Oracle.sol::14 => using Buffer for uint256;
2022-10-traderjoe/src/libraries/Samples.sol::12 => using Encoder for uint256;
2022-10-traderjoe/src/libraries/Samples.sol::13 => using Decoder for bytes32;
2022-10-traderjoe/src/libraries/SwapHelper.sol::17 => using Math512Bits for uint256;
2022-10-traderjoe/src/libraries/SwapHelper.sol::18 => using FeeHelper for FeeHelper.FeeParameters;
2022-10-traderjoe/src/libraries/SwapHelper.sol::19 => using SafeMath for uint256;
2022-10-traderjoe/src/libraries/SwapHelper.sol::20 => using FeeDistributionHelper for FeeHelper.FeesDistribution;
2022-10-traderjoe/src/libraries/TreeMath.sol::12 => using BitMath for uint256;
```



### [N07] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```

2022-10-traderjoe/src/interfaces/IJoeFactory.sol::8 => event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
2022-10-traderjoe/src/interfaces/IJoePair.sol::8 => event Approval(address indexed owner, address indexed spender, uint256 value);
2022-10-traderjoe/src/interfaces/IJoePair.sol::9 => event Transfer(address indexed from, address indexed to, uint256 value);
2022-10-traderjoe/src/interfaces/IJoePair.sol::49 => event Mint(address indexed sender, uint256 amount0, uint256 amount1);
2022-10-traderjoe/src/interfaces/IJoePair.sol::50 => event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
2022-10-traderjoe/src/interfaces/IJoePair.sol::59 => event Sync(uint112 reserve0, uint112 reserve1);
2022-10-traderjoe/src/interfaces/ILBFactory.sol::34 => event FeeRecipientSet(address oldRecipient, address newRecipient);
2022-10-traderjoe/src/interfaces/ILBFactory.sol::36 => event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);
2022-10-traderjoe/src/interfaces/ILBFactory.sol::53 => event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);
2022-10-traderjoe/src/interfaces/ILBFactory.sol::55 => event LBPairIgnoredStateChanged(ILBPair indexed LBPair, bool ignored);
2022-10-traderjoe/src/interfaces/ILBPair.sol::148 => event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
2022-10-traderjoe/src/interfaces/ILBPair.sol::150 => event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
2022-10-traderjoe/src/interfaces/ILBPair.sol::152 => event OracleSizeIncreased(uint256 previousSize, uint256 newSize);
2022-10-traderjoe/src/interfaces/ILBToken.sol::9 => event TransferSingle(address indexed sender, address indexed from, address indexed to, uint256 id, uint256 amount);
2022-10-traderjoe/src/interfaces/ILBToken.sol::19 => event ApprovalForAll(address indexed account, address indexed sender, bool approved);
2022-10-traderjoe/src/interfaces/IPendingOwnable.sol::9 => event PendingOwnerSet(address indexed pendingOwner);
2022-10-traderjoe/src/interfaces/IPendingOwnable.sol::10 => event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
```



### [N08] Use of sensitive/NC-inclusive terms


#### Findings:
```
2022-10-traderjoe/src/interfaces/ILBFactory.sol::22 => bool createdByOwner;
2022-10-traderjoe/src/interfaces/ILBFactory.sol::23 => bool ignoredForRouting;
2022-10-traderjoe/src/libraries/Math128x128.sol::95 => bool invert;
```


### [N09] Unused file


#### Findings:
```
2022-10-traderjoe/src/LBErrors.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/LBFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/LBPair.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/LBQuoter.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/LBRouter.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/LBToken.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/ILBFactory.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/ILBFlashLoanCallback.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/ILBPair.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/ILBRouter.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/ILBToken.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/IPendingOwnable.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/interfaces/IWAVAX.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/BinHelper.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/BitMath.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Buffer.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Constants.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Decoder.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Encoder.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/FeeDistributionHelper.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/FeeHelper.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Math128x128.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Math512Bits.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Oracle.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/PendingOwnable.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/ReentrancyGuardUpgradeable.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/SafeMath.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/Samples.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/SwapHelper.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/TokenHelper.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/TreeMath.sol::1 => // SPDX-License-Identifier: MIT
2022-10-traderjoe/src/libraries/afeCast.sol::1 => // SPDX-License-Identifier: MIT
```




### [N10] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from public to external.
#### Findings:
```
2022-10-traderjoe/src/LBToken.sol::49 => function name() public pure virtual override returns (string memory) {
2022-10-traderjoe/src/LBToken.sol::55 => function symbol() public pure virtual override returns (string memory) {
2022-10-traderjoe/src/LBToken.sol::63 => function totalSupply(uint256 _id) public view virtual override returns (uint256) {
2022-10-traderjoe/src/LBToken.sol::71 => function balanceOf(address _account, uint256 _id) public view virtual override returns (uint256) {
2022-10-traderjoe/src/LBToken.sol::100 => function userPositionAtIndex(address _account, uint256 _index) public view virtual override returns (uint256) {
2022-10-traderjoe/src/LBToken.sol::107 => function userPositionNumber(address _account) public view virtual override returns (uint256) {
2022-10-traderjoe/src/LBToken.sol::115 => function isApprovedForAll(address _owner, address _spender) public view virtual override returns (bool) {
2022-10-traderjoe/src/LBToken.sol::122 => function setApprovalForAll(address _spender, bool _approved) public virtual override {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::47 => function owner() public view override returns (address) {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::53 => function pendingOwner() public view override returns (address) {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::59 => function setPendingOwner(address pendingOwner_) public override onlyOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::68 => function revokePendingOwner() public override onlyOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::75 => function becomeOwner() public override onlyPendingOwner {
2022-10-traderjoe/src/libraries/PendingOwnable.sol::84 => function renounceOwnership() public override onlyOwner {
```




### [N11] NC-library/interface files should use fixed compiler versions, not floating ones


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





