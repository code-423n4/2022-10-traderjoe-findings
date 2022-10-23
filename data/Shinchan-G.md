# **Gas Optimization**

Serial No. | Issue Name | Instances
--- | --- | ---
G-1 | Pre-increment costs less gas as compared to Post-increment | 17
G-2 | ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops | 6
G-3 | Usage of assert() instead of require() | 1
G-4 | Strict inequalities (>) are more expensive than non-strict ones (>=) | 7
G-5 | x += y costs more gas than x = x + y for state variables | 84
G-6 | Variables: No need to explicitly initialize variables with default values | 1
G-7 | abi.encode() is less efficient than abi.encodepacked() | 2
G-8 | Inline a modifier that’s only used once | 1
G-9 | Use calldata instead of memory in external functions | 12
G-10 | Using bools for storage incurs overhead | 11
G-11 | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead | 98
G-12 | Use a more recent version of solidity | 37
G-13 | Use assembly to check for address(0)  | 7

----
***Total instances found in this contest: 284 | Among all files in scope***

## G-01 Pre-increment costs less gas as compared to Post-increment :

++i costs less gas as compared to i++ for unsigned integer, as per-increment is cheaper(its about 5 gas per iteration cheaper)

i++ increments i and returns initial value of i. Which means

```
uint i =  1;
i++; // ==1 but i ==2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // ==2 and i ==2 , no need for temporary variable here
```

In the first case, the compiler has create a temporary variable (when used) for returning 1 instead of 2.

### Instances:
[src/LBQuoter.sol:75:        for (uint256 i; i < swapLength; i++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)
[src/LBQuoter.sol:100:                for (uint256 j; j < LBPairsAvailable.length; j++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)
[src/LBQuoter.sol:177:                for (uint256 j; j < LBPairsAvailable.length; j++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)
[src/LBQuoter.sol:154:        for (uint256 i = swapLength; i > 0; i--) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154)
[src/LBRouter.sol:711:        for (uint256 i = _pairs.length; i != 0; i--) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)
[src/LBFactory.sol:143:        baseFactor = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L143)
[src/LBFactory.sol:144:        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L144)
[src/LBFactory.sol:145:        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L145)
[src/LBFactory.sol:146:        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L146)
[src/LBFactory.sol:147:        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L147)
[src/LBFactory.sol:149:        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L149)
[src/libraries/Math512Bits.sol:99:            if (mulmod(x, y, 1 << offset) != 0) result += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L99)
[src/libraries/Math512Bits.sol:159:            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L159)
[src/libraries/BitMath.sol:85:                msb += 16;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L85)
[src/libraries/BitMath.sol:100:                msb += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L100)
[src/libraries/BitMath.sol:124:                lsb += 16;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L124)
[src/libraries/BitMath.sol:139:                lsb += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L139)


### Recommendations:
Change post-increment to pre-increment.

-----

## G-02 ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

### Instances:
[src/LBQuoter.sol:75:        for (uint256 i; i < swapLength; i++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)
[src/LBQuoter.sol:100:                for (uint256 j; j < LBPairsAvailable.length; j++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)
[src/LBQuoter.sol:177:                for (uint256 j; j < LBPairsAvailable.length; j++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)
[src/LBQuoter.sol:75:        for (uint256 i; i < swapLength; i++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)
[src/LBQuoter.sol:100:                for (uint256 j; j < LBPairsAvailable.length; j++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)
[src/LBQuoter.sol:177:                for (uint256 j; j < LBPairsAvailable.length; j++) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)
### Reference:

[https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops](https://code4rena.com/reports/2022-05-cally#g-11-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops)

-----



## G-03 Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded the remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas. (see [https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions](https://docs.soliditylang.org/en/v0.8.1/control-structures.html#error-handling-assert-require-revert-and-exceptions)).
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).
From the current usage of assert, my guess is that they can be replaced with require unless a Panic really is intended.

### Instances:
[src/LBFactory.sol:141:        assert(_binStep == _preset.decode(type(uint16).max, _shift));](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L141)

### References:

[https://code4rena.com/reports/2022-05-bunker/#g-08-usage-of-assert-instead-of-require](https://code4rena.com/reports/2022-05-bunker/#g-08-usage-of-assert-instead-of-require)

-----


## G-04 Strict inequalities (>) are more expensive than non-strict ones (>=)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas. I suggest using >= instead of > to avoid some opcodes here:

### Instances:
[src/LBRouter.sol:116:                _amountOutOfBin = _amountOut > _reserve ? _reserve : _amountOut;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L116)
[src/libraries/SafeMath.sol:15:            return x > y ? x - y : y - x;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SafeMath.sol#L15)
[src/libraries/FeeHelper.sol:83:        _fp.volatilityAccumulated = volatilityAccumulated > _fp.maxVolatilityAccumulated](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L83)
[src/LBPair.sol:506:                    _mintInfo.distributionX > Constants.PRECISION ||](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L506)
[src/LBPair.sol:507:                    _mintInfo.distributionY > Constants.PRECISION ||](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L507)
[src/LBPair.sol:508:                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L508)
[src/LBPair.sol:509:                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L509)
### References:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than](https://code4rena.com/reports/2022-04-badger-citadel/#g-31--is-cheaper-than)


-----

## G-05 x += y costs more gas than x = x + y for state variables

x += y costs more gas than x = x + y
### Instances:
[src/LBRouter.sol:128:                amountIn += _amountInWithFees;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L128)
[src/LBRouter.sol:129:                feesIn += _fee;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L129)
[src/LBRouter.sol:174:                feesIn += _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L174)
[src/LBRouter.sol:175:                amountOut += _amountOutOfBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L175)
[src/LBFactory.sol:143:        baseFactor = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L143)
[src/LBFactory.sol:144:        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L144)
[src/LBFactory.sol:145:        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L145)
[src/LBFactory.sol:146:        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L146)
[src/LBFactory.sol:147:        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L147)
[src/LBFactory.sol:148:        protocolShare = _preset.decode(type(uint16).max, _shift += 24);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L148)
[src/LBFactory.sol:149:        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L149)
[src/libraries/SwapHelper.sol:88:        pairFees.total += fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L88)
[src/libraries/SwapHelper.sol:91:            pairFees.protocol += fees.protocol;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L91)
[src/libraries/SwapHelper.sol:95:            bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L95)
[src/libraries/SwapHelper.sol:97:            bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L97)
[src/libraries/SwapHelper.sol:115:            bin.reserveX += amountInToBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L115)
[src/libraries/SwapHelper.sol:119:                pair.reserveX += uint136(amountInToBin);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L119)
[src/libraries/SwapHelper.sol:123:            bin.reserveY += amountInToBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L123)
[src/libraries/SwapHelper.sol:128:                pair.reserveY += uint136(amountInToBin);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L128)
[src/libraries/Math512Bits.sol:71:                result += prod1 << (256 - offset);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L71)
[src/libraries/Math512Bits.sol:99:            if (mulmod(x, y, 1 << offset) != 0) result += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L99)
[src/libraries/Math512Bits.sol:159:            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L159)
[src/libraries/Math128x128.sol:77:                        result += delta;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L77)
[src/libraries/FeeDistributionHelper.sol:42:            _pairFees.protocol += _fees.protocol;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L42)
[src/libraries/BitMath.sol:77:                msb += 64;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L77)
[src/libraries/BitMath.sol:81:                msb += 32;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L81)
[src/libraries/BitMath.sol:85:                msb += 16;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L85)
[src/libraries/BitMath.sol:89:                msb += 8;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L89)
[src/libraries/BitMath.sol:93:                msb += 4;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L93)
[src/libraries/BitMath.sol:97:                msb += 2;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L97)
[src/libraries/BitMath.sol:100:                msb += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L100)
[src/libraries/BitMath.sol:116:                lsb += 64;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L116)
[src/libraries/BitMath.sol:120:                lsb += 32;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L120)
[src/libraries/BitMath.sol:124:                lsb += 16;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L124)
[src/libraries/BitMath.sol:128:                lsb += 8;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L128)
[src/libraries/BitMath.sol:132:                lsb += 4;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L132)
[src/libraries/BitMath.sol:136:                lsb += 2;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L136)
[src/libraries/BitMath.sol:139:                lsb += 1;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L139)
[src/LBPair.sol:227:                cumulativeId += _activeId * _deltaT;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L227)
[src/LBPair.sol:228:                cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L228)
[src/LBPair.sol:287:                    amountX += _amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L287)
[src/LBPair.sol:288:                    amountY += _amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L288)
[src/LBPair.sol:337:                _amountOut += _amountOutOfBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L337)
[src/LBPair.sol:452:        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L452)
[src/LBPair.sol:453:        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L453)
[src/LBPair.sol:508:                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L508)
[src/LBPair.sol:509:                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L509)
[src/LBPair.sol:540:                            _mintInfo.activeFeeX += _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L540)
[src/LBPair.sol:551:                            _mintInfo.activeFeeY += _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L551)
[src/LBPair.sol:572:                _pair.reserveX += uint112(_mintInfo.amountX);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L572)
[src/LBPair.sol:573:                _pair.reserveY += uint112(_mintInfo.amountY);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L573)
[src/LBPair.sol:575:                _mintInfo.amountXAddedToPair += _mintInfo.amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L575)
[src/LBPair.sol:576:                _mintInfo.amountYAddedToPair += _mintInfo.amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L576)
[src/LBPair.sol:639:                    amountY += _amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L639)
[src/LBPair.sol:646:                    amountX += _amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L646)
[src/LBPair.sol:711:                    amountX += _amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L711)
[src/LBPair.sol:712:                    amountY += _amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L712)
[src/LBPair.sol:896:            (amountX += _amountX).safe128();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L896)
[src/LBPair.sol:897:            (amountY += _amountY).safe128();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L897)
[src/LBToken.sol:211:        _totalSupplies[_id] += _amount;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L211)

-----
x -= y costs more gas than x = x - y
### Instances:
[src/LBRouter.sol:130:                _amountOut -= _amountOutOfBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L130)
[src/LBRouter.sol:173:                _amountIn -= _amountInToBin + _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L173)
[src/libraries/SwapHelper.sol:118:                bin.reserveY -= amountOutOfBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L118)
[src/libraries/SwapHelper.sol:120:                pair.reserveY -= uint136(amountOutOfBin);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L120)
[src/libraries/SwapHelper.sol:126:                bin.reserveX -= amountOutOfBin;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L126)
[src/libraries/SwapHelper.sol:127:                pair.reserveX -= uint136(amountOutOfBin);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L127)
[src/LBPair.sol:336:                _amountIn -= _amountInToBin + _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L336)
[src/LBPair.sol:539:                            _mintInfo.amountX -= _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L539)
[src/LBPair.sol:550:                            _mintInfo.amountY -= _fees.total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L550)
[src/LBPair.sol:640:                    _reserveY -= _amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L640)
[src/LBPair.sol:641:                    _pairReserveY -= _amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L641)
[src/LBPair.sol:647:                    _reserveX -= _amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L647)
[src/LBPair.sol:648:                    _pairReserveX -= _amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L648)
[src/LBPair.sol:717:                _pairInformation.feesX.total -= uint128(amountX);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L717)
[src/LBPair.sol:720:                _pairInformation.feesY.total -= uint128(amountY);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L720)
[src/LBPair.sol:751:                _feesXTotal -= amountX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L751)
[src/LBPair.sol:766:                _feesYTotal -= amountY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L766)
[src/LBToken.sol:241:            _totalSupplies[_id] -= _amount;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L241)

-----
x *= y costs more gas than x = x * y
### Instances:
[src/libraries/Math512Bits.sol:239:                inverse *= 2 - denominator * inverse; // inverse mod 2^8](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L239)
[src/libraries/Math512Bits.sol:240:                inverse *= 2 - denominator * inverse; // inverse mod 2^16](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L240)
[src/libraries/Math512Bits.sol:241:                inverse *= 2 - denominator * inverse; // inverse mod 2^32](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L241)
[src/libraries/Math512Bits.sol:242:                inverse *= 2 - denominator * inverse; // inverse mod 2^64](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L242)
[src/libraries/Math512Bits.sol:243:                inverse *= 2 - denominator * inverse; // inverse mod 2^128](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L243)
[src/libraries/Math512Bits.sol:244:                inverse *= 2 - denominator * inverse; // inverse mod 2^256](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L244)

### References:

[https://github.com/code-423n4/2022-05-backd-findings/issues/108](https://github.com/code-423n4/2022-05-backd-findings/issues/108)


-----

## G-06 Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Instances:
[src/libraries/Samples.sol:19:    uint256 private constant _OFFSET_INITIALIZED = 0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L19)

### Recommendation:

I suggest removing explicit initializations for default values.


-----


## G-07 abi.encode() is less efficient than abi.encodepacked()

Use abi.encodepacked() instead of abi.encode()

### Instances:
[src/LBFactory.sol:265:        bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L265)
[src/LBFactory.sol:265:        bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L265)
### Reference:

[https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked](https://code4rena.com/reports/2022-06-notional-coop#15-abiencode-is-less-efficient-than-abiencodepacked)


-----
## G-08 Inline a modifier that’s only used once
### Description
As onlyPendingOwner() is only used once in this contract (in function proxiableUUID()), it should get inlined to save gas:
### Instance
[src/libraries/PendingOwnable.sol:35:    modifier onlyPendingOwner() {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L35)
### Instances where modifiers is used only once
[src/libraries/PendingOwnable.sol:75:    function becomeOwner() public override onlyPendingOwner {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L75)

-----

## G-09 Use calldata instead of memory in external functions

Use calldata instead of memory in external functions to save gas.

### Instances:
[src/LBRouter.sol:438:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L438)
[src/LBRouter.sol:466:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L466)
[src/interfaces/ILBRouter.sol:140:    ) external returns (uint256[] memory amountsIn);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L140)
[src/interfaces/ILBRouter.sol:149:    ) external returns (uint256[] memory amountsIn);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L149)
[src/interfaces/ILBRouter.sol:157:    ) external payable returns (uint256[] memory amountsIn);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L157)
[src/interfaces/IJoeRouter01.sol:97:    ) external returns (uint256[] memory amounts);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L97)
[src/interfaces/IJoeRouter01.sol:105:    ) external returns (uint256[] memory amounts);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L105)
[src/interfaces/IJoeRouter01.sol:112:    ) external payable returns (uint256[] memory amounts);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L112)
[src/interfaces/IJoeRouter01.sol:120:    ) external returns (uint256[] memory amounts);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L120)
[src/interfaces/IJoeRouter01.sol:128:    ) external returns (uint256[] memory amounts);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L128)
[src/interfaces/IJoeRouter01.sol:135:    ) external payable returns (uint256[] memory amounts);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L135)
[src/interfaces/ILBPair.sol:242:    function collectFees(address account, uint256[] memory ids) external returns (uint256 amountX, uint256 amountY);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L242)
### Reference:

[https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory](https://code4rena.com/reports/2022-04-badger-citadel/#g-12-stakedcitadeldepositall-use-calldata-instead-of-memory)


-----

## G-10 Using bools for storage incurs overhead

```
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
```

[Refer Here](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Instances:
[src/LBQuoter.sol:102:                        bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i + 1];](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L102)
[src/LBQuoter.sol:179:                        bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i];](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L179)
[src/LBRouter.sol:320:        bool _isAVAXTokenY = IERC20(wavax) == _LBPair.tokenY();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L320)
[src/LBRouter.sol:798:                    bool _swapForY = _tokenNext == ILBPair(_pair).tokenY();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L798)
[src/LBRouter.sol:848:                    bool _swapForY = _tokenNext == ILBPair(_pair).tokenY();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L848)
[src/LBRouter.sol:964:        (bool success, ) = _to.call{value: _amount}("");](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L964)
[src/LBFactory.sol:39:    bool public override creationUnlocked;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L39)
[src/libraries/Math128x128.sol:95:        bool invert;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L95)
[src/interfaces/ILBFactory.sol:22:        bool createdByOwner;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L22)
[src/interfaces/ILBFactory.sol:23:        bool ignoredForRouting;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L23)
[src/LBToken.sol:21:    mapping(address => mapping(address => bool)) private _spenderApprovals;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L21)
### References:

[https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead](https://code4rena.com/reports/2022-06-notional-coop#8-using-bools-for-storage-incurs-overhead)


-----

## G-11 Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed

### Instances:
[src/LBRouter.sol:196:        uint24 _activeId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L196)
[src/LBRouter.sol:197:        uint16 _binStep](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L197)
[src/LBRouter.sol:277:        uint16 _binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L277)
[src/LBRouter.sol:310:        uint16 _binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L310)
[src/LBFactory.sol:237:        uint24 _activeId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L237)
[src/LBFactory.sol:238:        uint16 _binStep](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L238)
[src/LBFactory.sol:341:        uint16 _binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L341)
[src/LBFactory.sol:342:        uint16 _baseFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L342)
[src/LBFactory.sol:343:        uint16 _filterPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L343)
[src/LBFactory.sol:344:        uint16 _decayPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L344)
[src/LBFactory.sol:345:        uint16 _reductionFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L345)
[src/LBFactory.sol:346:        uint24 _variableFeeControl,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L346)
[src/LBFactory.sol:347:        uint16 _protocolShare,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L347)
[src/LBFactory.sol:348:        uint24 _maxVolatilityAccumulated,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L348)
[src/LBFactory.sol:349:        uint16 _sampleLifetime](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L349)
[src/LBFactory.sol:426:        uint16 _binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L426)
[src/LBFactory.sol:427:        uint16 _baseFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L427)
[src/LBFactory.sol:428:        uint16 _filterPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L428)
[src/LBFactory.sol:429:        uint16 _decayPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L429)
[src/LBFactory.sol:430:        uint16 _reductionFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L430)
[src/LBFactory.sol:431:        uint24 _variableFeeControl,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L431)
[src/LBFactory.sol:432:        uint16 _protocolShare,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L432)
[src/LBFactory.sol:433:        uint24 _maxVolatilityAccumulated](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L433)
[src/LBFactory.sol:534:        uint16 _binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L534)
[src/LBFactory.sol:535:        uint16 _baseFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L535)
[src/LBFactory.sol:536:        uint16 _filterPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L536)
[src/LBFactory.sol:537:        uint16 _decayPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L537)
[src/LBFactory.sol:538:        uint16 _reductionFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L538)
[src/LBFactory.sol:539:        uint24 _variableFeeControl,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L539)
[src/LBFactory.sol:540:        uint16 _protocolShare,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L540)
[src/LBFactory.sol:541:        uint24 _maxVolatilityAccumulated](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L541)
[src/LBFactory.sol:575:                    uint136(_maxVolatilityAccumulated), // The first 112 bits are reserved for the dynamic parameters](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L575)
[src/libraries/SwapHelper.sol:111:        uint112 amountInToBin,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L111)
[src/libraries/SwapHelper.sol:112:        uint112 amountOutOfBin](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L112)
[src/libraries/TreeMath.sol:23:        uint24 _binId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol#L23)
[src/libraries/FeeHelper.sol:30:        uint16 binStep;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L30)
[src/libraries/FeeHelper.sol:31:        uint16 baseFactor;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L31)
[src/libraries/FeeHelper.sol:32:        uint16 filterPeriod;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L32)
[src/libraries/FeeHelper.sol:33:        uint16 decayPeriod;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L33)
[src/libraries/FeeHelper.sol:34:        uint16 reductionFactor;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L34)
[src/libraries/FeeHelper.sol:35:        uint24 variableFeeControl;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L35)
[src/libraries/FeeHelper.sol:36:        uint16 protocolShare;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L36)
[src/libraries/FeeHelper.sol:37:        uint24 maxVolatilityAccumulated;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L37)
[src/libraries/FeeHelper.sol:38:        uint24 volatilityAccumulated;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L38)
[src/libraries/FeeHelper.sol:39:        uint24 volatilityReference;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L39)
[src/libraries/FeeHelper.sol:40:        uint24 indexRef;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L40)
[src/libraries/FeeHelper.sol:41:        uint40 time;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L41)
[src/libraries/FeeHelper.sol:48:        uint128 total;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L48)
[src/libraries/FeeHelper.sol:49:        uint128 protocol;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L49)
[src/libraries/BitMath.sol:16:        uint8 _bit,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L16)
[src/interfaces/ILBRouter.sol:71:        uint24 activeId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L71)
[src/interfaces/ILBRouter.sol:72:        uint16 binStep](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L72)
[src/interfaces/ILBRouter.sol:87:        uint16 binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L87)
[src/interfaces/ILBRouter.sol:98:        uint16 binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L98)
[src/interfaces/IJoeRouter01.sol:73:        uint8 v,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L73)
[src/interfaces/IJoeRouter01.sol:86:        uint8 v,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L86)
[src/interfaces/ILBPair.sol:18:        uint112 reserveX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L18)
[src/interfaces/ILBPair.sol:19:        uint112 reserveY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L19)
[src/interfaces/ILBPair.sol:40:        uint24 activeId;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L40)
[src/interfaces/ILBPair.sol:41:        uint136 reserveX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L41)
[src/interfaces/ILBPair.sol:42:        uint136 reserveY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L42)
[src/interfaces/ILBPair.sol:43:        uint16 oracleSampleLifetime;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L43)
[src/interfaces/ILBPair.sol:44:        uint16 oracleSize;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L44)
[src/interfaces/ILBPair.sol:45:        uint16 oracleActiveSize;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L45)
[src/interfaces/ILBPair.sol:46:        uint40 oracleLastTimestamp;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L46)
[src/interfaces/ILBPair.sol:47:        uint16 oracleId;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L47)
[src/interfaces/ILBPair.sol:64:        uint128 tokenX;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L64)
[src/interfaces/ILBPair.sol:65:        uint128 tokenY;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L65)
[src/interfaces/ILBPair.sol:101:        uint24 indexed id,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L101)
[src/interfaces/ILBPair.sol:253:        uint24 activeId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L253)
[src/interfaces/ILBPair.sol:254:        uint16 sampleLifetime,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L254)
[src/interfaces/ILBFactory.sol:20:        uint24 binStep;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L20)
[src/interfaces/ILBFactory.sol:133:        uint24 activeId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L133)
[src/interfaces/ILBFactory.sol:134:        uint16 binStep](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L134)
[src/interfaces/ILBFactory.sol:145:        uint16 binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L145)
[src/interfaces/ILBFactory.sol:146:        uint16 baseFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L146)
[src/interfaces/ILBFactory.sol:147:        uint16 filterPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L147)
[src/interfaces/ILBFactory.sol:148:        uint16 decayPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L148)
[src/interfaces/ILBFactory.sol:149:        uint16 reductionFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L149)
[src/interfaces/ILBFactory.sol:150:        uint24 variableFeeControl,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L150)
[src/interfaces/ILBFactory.sol:151:        uint16 protocolShare,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L151)
[src/interfaces/ILBFactory.sol:152:        uint24 maxVolatilityAccumulated,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L152)
[src/interfaces/ILBFactory.sol:153:        uint16 sampleLifetime](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L153)
[src/interfaces/ILBFactory.sol:161:        uint16 binStep,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L161)
[src/interfaces/ILBFactory.sol:162:        uint16 baseFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L162)
[src/interfaces/ILBFactory.sol:163:        uint16 filterPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L163)
[src/interfaces/ILBFactory.sol:164:        uint16 decayPeriod,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L164)
[src/interfaces/ILBFactory.sol:165:        uint16 reductionFactor,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L165)
[src/interfaces/ILBFactory.sol:166:        uint24 variableFeeControl,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L166)
[src/interfaces/ILBFactory.sol:167:        uint16 protocolShare,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L167)
[src/interfaces/ILBFactory.sol:168:        uint24 maxVolatilityAccumulated](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L168)
[src/interfaces/IJoeRouter02.sol:27:        uint8 v,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter02.sol#L27)
[src/interfaces/IJoePair.sol:44:        uint8 v,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L44)
[src/interfaces/IJoePair.sol:73:            uint112 reserve0,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L73)
[src/interfaces/IJoePair.sol:74:            uint112 reserve1,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L74)
[src/interfaces/IJoePair.sol:75:            uint32 blockTimestampLast](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L75)
[src/LBPair.sol:107:        uint24 _activeId,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L107)
[src/LBPair.sol:108:        uint16 _sampleLifetime,](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L108)
### References:

[https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead](https://code4rena.com/reports/2022-04-phuture#g-14-usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)


-----

## G-12 Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

### Instances:
[src/LBQuoter.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L3)
[src/LBErrors.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBErrors.sol#L3)
[src/LBRouter.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L3)
[src/LBFactory.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L3)
[src/libraries/SwapHelper.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L3)
[src/libraries/SafeCast.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SafeCast.sol#L3)
[src/libraries/Decoder.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Decoder.sol#L3)
[src/libraries/Math512Bits.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L3)
[src/libraries/TreeMath.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol#L3)
[src/libraries/Encoder.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Encoder.sol#L3)
[src/libraries/ReentrancyGuardUpgradeable.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/ReentrancyGuardUpgradeable.sol#L3)
[src/libraries/JoeLibrary.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L3)
[src/libraries/Math128x128.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L3)
[src/libraries/TokenHelper.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TokenHelper.sol#L3)
[src/libraries/PendingOwnable.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L3)
[src/libraries/Buffer.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Buffer.sol#L3)
[src/libraries/Constants.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Constants.sol#L3)
[src/libraries/SafeMath.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SafeMath.sol#L3)
[src/libraries/Oracle.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L3)
[src/libraries/BinHelper.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L3)
[src/libraries/Samples.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L3)
[src/libraries/FeeDistributionHelper.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L3)
[src/libraries/FeeHelper.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L3)
[src/libraries/BitMath.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L3)
[src/interfaces/ILBRouter.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L3)
[src/interfaces/IJoeRouter01.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L3)
[src/interfaces/IWAVAX.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IWAVAX.sol#L3)
[src/interfaces/IJoeFactory.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeFactory.sol#L3)
[src/interfaces/ILBPair.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L3)
[src/interfaces/IPendingOwnable.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IPendingOwnable.sol#L3)
[src/interfaces/ILBFactory.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L3)
[src/interfaces/IJoeRouter02.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter02.sol#L3)
[src/interfaces/ILBFlashLoanCallback.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFlashLoanCallback.sol#L3)
[src/interfaces/IJoePair.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L3)
[src/interfaces/ILBToken.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBToken.sol#L3)
[src/LBPair.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L3)
[src/LBToken.sol:3:pragma solidity ^0.8.0;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L3)
### References:

[https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-06-notional-coop/#10-use-a-more-recent-version-of-solidity)


-----

## G-13 Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, 'zero address')
  revert(0x00, 0x20)
 }
}
```
### Instances
[src/LBQuoter.sol:79:            if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L79)
[src/LBQuoter.sol:157:            if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L157)
[src/LBFactory.sol:255:        if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L255)
[src/libraries/PendingOwnable.sol:61:        if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L61)
[src/LBPair.sol:112:        if (address(tokenX) != address(0)) revert LBPair__AlreadyInitialized();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L112)
[src/LBPair.sol:820:                if (_from != address(0) && _from != address(this)) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L820)
[src/LBPair.sol:826:                if (_to != address(0) && _to != address(this)) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L826)

-----

