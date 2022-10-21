## [G-1] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.
```solidity
LBToken.sol:18:    mapping(uint256 => mapping(address => uint256)) private _balances;
LBToken.sol:21:    mapping(address => mapping(address => bool)) private _spenderApprovals;
LBToken.sol:27:    mapping(address => EnumerableSet.UintSet) private _userIds;
LBPair.sol:68:    mapping(address => bytes32) private _unclaimedFees;
LBPair.sol:70:    mapping(address => mapping(uint256 => Debts)) private _accruedDebts;
```

## [G-2] Use preincrement to save gas
```solidity
LBQuoter.sol:75:        for (uint256 i; i < swapLength; i++) {
LBQuoter.sol:100:                for (uint256 j; j < LBPairsAvailable.length; j++) {
LBQuoter.sol:177:                for (uint256 j; j < LBPairsAvailable.length; j++) {
```
## [G-3] Cache `.length` in loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

```solidity
LBRouter.sol:674:            for (uint256 i; i < depositIds.length; ++i) {
LBRouter.sol:711:        for (uint256 i = _pairs.length; i != 0; i--) {
LBRouter.sol:778:            for (uint256 i; i < _pairs.length; ++i) {
LBRouter.sol:831:            for (uint256 i; i < _pairs.length; ++i) {
LBRouter.sol:878:            for (uint256 i; i < _pairs.length; ++i) {
LBRouter.sol:951:            for (uint256 i; i < pairs.length; ++i) {
LBQuoter.sol:100:                for (uint256 j; j < LBPairsAvailable.length; j++) {
LBQuoter.sol:177:                for (uint256 j; j < LBPairsAvailable.length; j++) {
LBToken.sol:90:            for (uint256 i; i < _accounts.length; ++i) {
LBToken.sol:163:            for (uint256 i; i < _ids.length; ++i) {
LBPair.sol:274:            for (uint256 i; i < _ids.length; ++i) {
LBPair.sol:496:            for (uint256 i; i < _ids.length; ++i) {
LBPair.sol:623:            for (uint256 i; i < _ids.length; ++i) {
LBPair.sol:701:            for (uint256 i; i < _ids.length; ++i) {
```
## [G-4] Use `Variable == false|0 -> !variable` or `variable ==  true -> variable` to save gas
```solidity
libraries/Oracle.sol:48:        if (_activeSize == 0) revert Oracle__NotInitialized();
libraries/BitMath.sol:44:            if (x == 0) return type(uint256).max;
libraries/BitMath.sol:60:            if (x == 0) return type(uint256).max;
libraries/Math128x128.sol:42:        if (x == 0) revert Math128x128__LogUnderflow();
libraries/Math128x128.sol:98:        if (y == 0) return Constants.SCALE;
libraries/Math128x128.sol:200:        if (result == 0) revert Math128x128__PowerUnderflow(x, y);
libraries/TokenHelper.sol:76:        if (!(success && (result.length == 0 || abi.decode(result, (bool))))) {
libraries/BinHelper.sol:52:        if (_binStep == 0 || _binStep > Constants.BASIS_POINT_MAX) revert BinHelper__BinStepOverflows(_binStep);
libraries/FeeHelper.sol:58:        if (_deltaT >= _fp.filterPeriod || _fp.time == 0) {
libraries/Math512Bits.sol:194:        if (prod1 == 0) {
libraries/Math512Bits.sol:199:            // Make sure the result is less than 2^256. Also prevents denominator == 0
libraries/TreeMath.sol:87:            if (_newLeafValue == 0) {
libraries/TreeMath.sol:92:                if (_newLeafValue == 0) {
libraries/JoeLibrary.sol:24:        if (amountA == 0) revert JoeLibrary__InsufficientAmount();
libraries/JoeLibrary.sol:25:        if (reserveA == 0 || reserveB == 0) revert JoeLibrary__InsufficientLiquidity();
libraries/JoeLibrary.sol:35:        if (amountIn == 0) revert JoeLibrary__InsufficientAmount();
libraries/JoeLibrary.sol:36:        if (reserveIn == 0 || reserveOut == 0) revert JoeLibrary__InsufficientLiquidity();
libraries/JoeLibrary.sol:49:        if (amountOut == 0) revert JoeLibrary__InsufficientAmount();
libraries/JoeLibrary.sol:50:        if (reserveIn == 0 || reserveOut == 0) revert JoeLibrary__InsufficientLiquidity();
LBRouter.sol:43:        if (_pairBinSteps.length == 0 || _pairBinSteps.length + 1 != _tokenPath.length)
LBRouter.sol:97:        if (_amountOut == 0 || (_swapForY ? _amountOut > _pairReserveY : _amountOut > _pairReserveX))
LBRouter.sol:717:            if (_binStep == 0) {
LBRouter.sol:787:                if (_binStep == 0) {
LBRouter.sol:840:                if (_binStep == 0) {
LBRouter.sol:887:                if (_binStep == 0) {
LBRouter.sol:935:        if (_binStep == 0) {
LBFactory.sol:370:        if (_avPresets.decode(1, _binStep) == 0) {
LBQuoter.sol:184:                                swapAmountIn != 0 && (swapAmountIn < quote.amounts[i - 1] || quote.amounts[i - 1] == 0)
LBToken.sol:284:        if (_accountBalance == 0 && _amount != 0) {
LBPair.sol:188:        min = oracleActiveSize == 0 ? 0 : oracleSampleLifetime;
LBPair.sol:316:        if (_amountIn == 0) revert LBPair__InsufficientAmounts();
LBPair.sol:377:        if (_amountOut == 0) revert LBPair__BrokenSwapSafetyCheck(); // Safety check
LBPair.sol:391:        if (_updatedOracleId != _pair.oracleId || _pair.oracleLastTimestamp == 0) {
LBPair.sol:481:        if (_ids.length == 0 || _ids.length != _distributionX.length || _ids.length != _distributionY.length)
LBPair.sol:500:                if (_bin.reserveX == 0 && _bin.reserveY == 0) _tree.addToTree(_mintInfo.id);
LBPair.sol:563:                if (_liquidity == 0) revert LBPair__InsufficientLiquidityMinted(_mintInfo.id);
LBPair.sol:627:                if (_amountToBurn == 0) revert LBPair__InsufficientLiquidityBurned(_id);
LBPair.sol:651:                if (_reserveX == 0 && _reserveY == 0) _tree.removeFromTree(_id);
```

## [G-05] Use `!= 0` instead of `> 0`
```solidity
libraries/Math128x128.sol:71:                for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta > 0; delta >>= 1) {
LBFactory.sol:161:            if (_nbPresets > 0) {
LBFactory.sol:191:            if (_nbAvailable > 0) {
LBQuoter.sol:79:            if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
LBQuoter.sol:82:                if (reserveIn > 0 && reserveOut > 0) {
LBQuoter.sol:99:            if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
LBQuoter.sol:154:        for (uint256 i = swapLength; i > 0; i--) {
LBQuoter.sol:157:            if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
LBQuoter.sol:160:                if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
LBQuoter.sol:176:            if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
```
## [G-06] <x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
libraries/BitMath.sol:77:                msb += 64;
libraries/BitMath.sol:81:                msb += 32;
libraries/BitMath.sol:85:                msb += 16;
libraries/BitMath.sol:89:                msb += 8;
libraries/BitMath.sol:93:                msb += 4;
libraries/BitMath.sol:97:                msb += 2;
libraries/BitMath.sol:100:                msb += 1;
libraries/BitMath.sol:116:                lsb += 64;
libraries/BitMath.sol:120:                lsb += 32;
libraries/BitMath.sol:124:                lsb += 16;
libraries/BitMath.sol:128:                lsb += 8;
libraries/BitMath.sol:132:                lsb += 4;
libraries/BitMath.sol:136:                lsb += 2;
libraries/BitMath.sol:139:                lsb += 1;
libraries/FeeDistributionHelper.sol:42:            _pairFees.protocol += _fees.protocol;
libraries/Math128x128.sol:70:                // The "delta >>= 1" part is equivalent to "delta /= 2", but shifting bits is faster.
libraries/Math128x128.sol:77:                        result += delta;
libraries/SwapHelper.sol:88:        pairFees.total += fees.total;
libraries/SwapHelper.sol:91:            pairFees.protocol += fees.protocol;
libraries/SwapHelper.sol:95:            bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);
libraries/SwapHelper.sol:97:            bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);
libraries/SwapHelper.sol:115:            bin.reserveX += amountInToBin;
libraries/SwapHelper.sol:118:                bin.reserveY -= amountOutOfBin;
libraries/SwapHelper.sol:119:                pair.reserveX += uint136(amountInToBin);
libraries/SwapHelper.sol:120:                pair.reserveY -= uint136(amountOutOfBin);
libraries/SwapHelper.sol:123:            bin.reserveY += amountInToBin;
libraries/SwapHelper.sol:126:                bin.reserveX -= amountOutOfBin;
libraries/SwapHelper.sol:127:                pair.reserveX -= uint136(amountOutOfBin);
libraries/SwapHelper.sol:128:                pair.reserveY += uint136(amountInToBin);
libraries/Math512Bits.sol:71:                result += prod1 << (256 - offset);
libraries/Math512Bits.sol:99:            if (mulmod(x, y, 1 << offset) != 0) result += 1;
libraries/Math512Bits.sol:159:            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;
libraries/Math512Bits.sol:239:                inverse *= 2 - denominator * inverse; // inverse mod 2^8
libraries/Math512Bits.sol:240:                inverse *= 2 - denominator * inverse; // inverse mod 2^16
libraries/Math512Bits.sol:241:                inverse *= 2 - denominator * inverse; // inverse mod 2^32
libraries/Math512Bits.sol:242:                inverse *= 2 - denominator * inverse; // inverse mod 2^64
libraries/Math512Bits.sol:243:                inverse *= 2 - denominator * inverse; // inverse mod 2^128
libraries/Math512Bits.sol:244:                inverse *= 2 - denominator * inverse; // inverse mod 2^256
LBRouter.sol:128:                amountIn += _amountInWithFees;
LBRouter.sol:129:                feesIn += _fee;
LBRouter.sol:130:                _amountOut -= _amountOutOfBin;
LBRouter.sol:173:                _amountIn -= _amountInToBin + _fees.total;
LBRouter.sol:174:                feesIn += _fees.total;
LBRouter.sol:175:                amountOut += _amountOutOfBin;
LBFactory.sol:143:        baseFactor = _preset.decode(type(uint16).max, _shift += 16);
LBFactory.sol:144:        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
LBFactory.sol:145:        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
LBFactory.sol:146:        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
LBFactory.sol:147:        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
LBFactory.sol:148:        protocolShare = _preset.decode(type(uint16).max, _shift += 24);
LBFactory.sol:149:        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
LBToken.sol:211:        _totalSupplies[_id] += _amount;
LBToken.sol:241:            _totalSupplies[_id] -= _amount;
LBPair.sol:227:                cumulativeId += _activeId * _deltaT;
LBPair.sol:228:                cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
LBPair.sol:287:                    amountX += _amountX;
LBPair.sol:288:                    amountY += _amountY;
LBPair.sol:336:                _amountIn -= _amountInToBin + _fees.total;
LBPair.sol:337:                _amountOut += _amountOutOfBin;
LBPair.sol:452:        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
LBPair.sol:453:        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
LBPair.sol:508:                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||
LBPair.sol:509:                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION
LBPair.sol:539:                            _mintInfo.amountX -= _fees.total;
LBPair.sol:540:                            _mintInfo.activeFeeX += _fees.total;
LBPair.sol:550:                            _mintInfo.amountY -= _fees.total;
LBPair.sol:551:                            _mintInfo.activeFeeY += _fees.total;
LBPair.sol:572:                _pair.reserveX += uint112(_mintInfo.amountX);
LBPair.sol:573:                _pair.reserveY += uint112(_mintInfo.amountY);
LBPair.sol:575:                _mintInfo.amountXAddedToPair += _mintInfo.amountX;
LBPair.sol:576:                _mintInfo.amountYAddedToPair += _mintInfo.amountY;
LBPair.sol:639:                    amountY += _amountY;
LBPair.sol:640:                    _reserveY -= _amountY;
LBPair.sol:641:                    _pairReserveY -= _amountY;
LBPair.sol:646:                    amountX += _amountX;
LBPair.sol:647:                    _reserveX -= _amountX;
LBPair.sol:648:                    _pairReserveX -= _amountX;
LBPair.sol:711:                    amountX += _amountX;
LBPair.sol:712:                    amountY += _amountY;
LBPair.sol:717:                _pairInformation.feesX.total -= uint128(amountX);
LBPair.sol:720:                _pairInformation.feesY.total -= uint128(amountY);
LBPair.sol:751:                _feesXTotal -= amountX;
LBPair.sol:766:                _feesYTotal -= amountY;
LBPair.sol:896:            (amountX += _amountX).safe128();
LBPair.sol:897:            (amountY += _amountY).safe128();
```

## [G‑07] `>=` COSTS LESS GAS THAN `>`
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde 
In some cases -1 can be used if it causes off by one error
```solidity
libraries/Oracle.sol:57:        if (timestamp > _lookUpTimestamp) revert Oracle__LookUpTimestampTooOld(timestamp, _lookUpTimestamp);
libraries/Oracle.sol:64:            if (timestamp > _lookUpTimestamp) {
libraries/Oracle.sol:170:                } else if (_sampleTimestamp > _lookUpTimestamp) {
libraries/FeeDistributionHelper.sol:34:        if (_fees.total > _amountReceived)
libraries/Math128x128.sol:71:                for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta > 0; delta >>= 1) {
libraries/Math128x128.sol:74:                    // Is y^2 > 2 and so in the range [2,4)?
libraries/BinHelper.sol:29:            if (_id < 0 || uint256(_id) > type(uint24).max) revert BinHelper__IdOverflows(_id);
libraries/BinHelper.sol:40:        if (_id > uint256(type(int256).max)) revert BinHelper__IntOverflows(_id);
libraries/BinHelper.sol:52:        if (_binStep == 0 || _binStep > Constants.BASIS_POINT_MAX) revert BinHelper__BinStepOverflows(_binStep);
libraries/FeeHelper.sol:83:        _fp.volatilityAccumulated = volatilityAccumulated > _fp.maxVolatilityAccumulated
libraries/SwapHelper.sol:71:            if (amountOutOfBin > _reserve) amountOutOfBin = _reserve;
libraries/Math512Bits.sol:61:        if (offset > 255) revert Math512Bits__OffsetOverflows(offset);
libraries/Math512Bits.sol:124:        if (offset > 255) revert Math512Bits__OffsetOverflows(offset);
libraries/SafeMath.sol:15:            return x > y ? x - y : y - x;
LBRouter.sol:38:        if (block.timestamp > _deadline) revert LBRouter__DeadlineExceeded(_deadline, block.timestamp);
LBRouter.sol:97:        if (_amountOut == 0 || (_swapForY ? _amountOut > _pairReserveY : _amountOut > _pairReserveX))
LBRouter.sol:116:                _amountOutOfBin = _amountOut > _reserve ? _reserve : _amountOut;
LBRouter.sol:127:                if (_amountInWithFees + _reserve > type(uint112).max) revert LBRouter__SwapOverflows(_activeId);
LBRouter.sol:171:                if (_amountInToBin > type(uint112).max) revert LBRouter__BinReserveOverflows(_activeId);
LBRouter.sol:366:        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
LBRouter.sol:394:        if (_amountOutMinAVAX > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMinAVAX, amountOut);
LBRouter.sol:420:        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
LBRouter.sol:442:        if (amountsIn[0] > _amountInMax) revert LBRouter__MaxAmountInExceeded(_amountInMax, amountsIn[0]);
LBRouter.sol:473:        if (amountsIn[0] > _amountInMax) revert LBRouter__MaxAmountInExceeded(_amountInMax, amountsIn[0]);
LBRouter.sol:512:        if (amountsIn[0] > msg.value) revert LBRouter__MaxAmountInExceeded(msg.value, amountsIn[0]);
LBRouter.sol:520:        if (msg.value > amountsIn[0]) _safeTransferAVAX(_to, amountsIn[0] - msg.value);
LBRouter.sol:550:        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
LBRouter.sol:581:        if (_amountOutMinAVAX > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMinAVAX, amountOut);
LBRouter.sol:614:        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
LBRouter.sol:665:            if (_liq.activeIdDesired > type(uint24).max || _liq.idSlippage > type(uint24).max)
LBRouter.sol:676:                if (_id < 0 || uint256(_id) > type(uint24).max) revert LBRouter__IdOverflows(_id);
LBRouter.sol:719:                if (_token > _tokenPath[i]) {
LBFactory.sol:161:            if (_nbPresets > 0) {
LBFactory.sol:191:            if (_nbAvailable > 0) {
LBFactory.sol:543:        if (_binStep < MIN_BIN_STEP || _binStep > MAX_BIN_STEP)
LBFactory.sol:546:        if (_baseFactor > Constants.BASIS_POINT_MAX)
LBFactory.sol:551:        if (_reductionFactor > Constants.BASIS_POINT_MAX)
LBFactory.sol:554:        if (_protocolShare > MAX_PROTOCOL_SHARE)
LBFactory.sol:566:            if (_baseFee + _maxVariableFee > MAX_FEE)
LBFactory.sol:608:        if (_tokenA > _tokenB) (_tokenA, _tokenB) = (_tokenB, _tokenA);
LBQuoter.sol:79:            if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
LBQuoter.sol:82:                if (reserveIn > 0 && reserveOut > 0) {
LBQuoter.sol:99:            if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
LBQuoter.sol:107:                            if (swapAmountOut > quote.amounts[i + 1]) {
LBQuoter.sol:154:        for (uint256 i = swapLength; i > 0; i--) {
LBQuoter.sol:157:            if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {
LBQuoter.sol:160:                if (reserveIn > 0 && reserveOut > quote.amounts[i]) {
LBQuoter.sol:176:            if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
LBPair.sol:62:    /// of tokenY if `id < _pairInformation.activeId`; of tokenX if `id > _pairInformation.activeId`
LBPair.sol:506:                    _mintInfo.distributionX > Constants.PRECISION ||
LBPair.sol:507:                    _mintInfo.distributionY > Constants.PRECISION ||
LBPair.sol:508:                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||
LBPair.sol:509:                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION
LBPair.sol:534:                        if (_mintInfo.amountX > _receivedX) {
LBPair.sol:545:                        } else if (_mintInfo.amountY > _receivedY) {
LBPair.sol:597:            if (_mintInfo.amountXIn > _amountAddedPlusFee) {
LBPair.sol:602:            if (_mintInfo.amountYIn > _amountAddedPlusFee) {
LBPair.sol:749:            if (_feesXProtocol > 1) {
LBPair.sol:764:            if (_feesYProtocol > 1) {
```


## [G-8] Use `private` for constants insted of `public`
```solidity
LBFactory.sol:25:    uint256 public constant override MAX_FEE = 0.1e18; // 10%
LBFactory.sol:27:    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
LBFactory.sol:28:    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
LBFactory.sol:30:    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```

