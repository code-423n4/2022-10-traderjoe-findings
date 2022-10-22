### G-01 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 78*

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol

```
143: baseFactor = _preset.decode(type(uint16).max, _shift += 16); 
144: filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
145: decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
146: reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
147: variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
148: protocolShare = _preset.decode(type(uint16).max, _shift += 24);
149: maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
```


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol

```
227: cumulativeId += _activeId * _deltaT;
228: cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
287: amountX += _amountX;
288: amountY += _amountY;
336: _amountIn -= _amountInToBin + _fees.total;
337: _amountOut += _amountOutOfBin;
452: _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
453: _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
508: _mintInfo.totalDistributionX += _mintInfo.distributionX
509: _mintInfo.totalDistributionY += _mintInfo.distributionY
539:  _mintInfo.amountX -= _fees.total;
540: _mintInfo.activeFeeX += _fees.total;
550: _mintInfo.amountY -= _fees.total;
551: _mintInfo.activeFeeY += _fees.total;
572: _pair.reserveX += uint112(_mintInfo.amountX);
573: _pair.reserveY += uint112(_mintInfo.amountY);
575: _mintInfo.amountXAddedToPair += _mintInfo.amountX;
576: mintInfo.amountYAddedToPair += _mintInfo.amountY;
639: amountY += _amountY;
640: _reserveY -= _amountY;
641: _pairReserveY -= _amountY;
646: amountX += _amountX;
647: _reserveX -= _amountX;
648: _pairReserveX -= _amountX;
711: amountX += _amountX;
712: amountY += _amountY;
717: _pairInformation.feesX.total -= uint128(amountX);
720: _pairInformation.feesY.total -= uint128(amountY);
751: _feesXTotal -= amountX;
766: _feesYTotal -= amountY;
896: (amountX += _amountX)
897: (amountY += _amountY)
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol

```
128: amountIn += _amountInWithFees
129: feesIn += _fee;
130: _amountOut -= _amountOutOfBin;
173: _amountIn -= _amountInToBin + _fees.total;
174: feesIn += _fees.total;
175: amountOut += _amountOutOfBin;
211: _totalSupplies[_id] += _amount;
241: _totalSupplies[_id] -= _amount;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol

```
42: _pairFees.protocol += _fees.protocol;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol

```
77: result += delta;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol

```
71: result += prod1 << (256 - offset);
99: if (mulmod(x, y, 1 << offset) != 0) result += 1;
159: if (mulmod(x, 1 << offset, denominator) != 0) result += 1;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol

```
88: pairFees.total += fees.total;
91: pairFees.protocol += fees.protocol;
95: bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);
97: bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);
115:  bin.reserveX += amountInToBin;
119: pair.reserveX += uint136(amountInToBin);
120: pair.reserveY -= uint136(amountOutOfBin);
123: bin.reserveY += amountInToBin;
126: bin.reserveX -= amountOutOfBin;
127: pair.reserveX -= uint136(amountOutOfBin);
128: pair.reserveY += uint136(amountInToBin);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol

```
77: msb += 64;
81: msb += 32;
85: msb += 16;
89: msb += 8;
93: msb += 4;
97: msb += 2;
100: msb += 1;
112: lsb = 128;
116: lsb += 64;
120: lsb += 32;
124: lsb += 16;
128: lsb += 8;
132: lsb += 4;
136: lsb += 2;
139: lsb += 1;
```


### G-02 ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 (SAME FOR --I VS I-- OR I -= 1)

*Number of Instances Identified: 5*

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

-   `i += 1` is the most expensive form
-   `i++` costs 6 gas less than `i += 1`
-   `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

-   `i -= 1` is the most expensive form
-   `i--` costs 11 gas less than `i -= 1`
-   `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol

```
75: for (uint256 i; i < swapLength; i++) {
100: for (uint256 j; j < LBPairsAvailable.length; j++) {
154: for (uint256 i = swapLength; i > 0; i--) {
177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol

```
711: for (uint256 i = _pairs.length; i != 0; i--) {
```


### G-03 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 5*

the `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol

```
75: for (uint256 i; i < swapLength; i++) {
100: for (uint256 j; j < LBPairsAvailable.length; j++) {
154: for (uint256 i = swapLength; i > 0; i--) {
177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol

```
711: for (uint256 i = _pairs.length; i != 0; i--) {
```


### G-04 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES - 256 BITS INCURS OVERHEAD

*Number of Instances Identified: 7*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol

```
57: error LBFactory__DecreasingPeriods(uint16 filterPeriod, uint16 decayPeriod);
58: error LBFactory__BaseFactorOverflows(uint16 baseFactor, uint256 max);
59: error LBFactory__ReductionFactorOverflows(uint16 reductionFactor, uint256 max);
60: error LBFactory__VariableFeeControlOverflows(uint16 variableFeeControl, uint256 max);
63: error LBFactory__BinStepRequirementsBreached(uint256 lowerBound, uint16 binStep, uint256 higherBound);
64: error LBFactory__ProtocolShareOverflows(uint16 protocolShare, uint256 max);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol

```
120: uint16 _binStep
```


### G-05 IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

*Number of Instances Identified: 1*

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol

```
19: uint256 private constant _OFFSET_INITIALIZED = 0;
```


### G-06 DIVISION BY TWO SHOULD USE BIT SHIFTING

*Number of Instances Identified: 1*

`<x> / 2` is the same as `<x> >> 1`. The  `DIV` opcode cost 5 gas, whereas `SHR` only cost 3 gas
 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol

```
162: _middle = (_low + _high) / 2;
```

### G-07 EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

*Number of Instances Identified: 1*

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol

```
326: internal virtual {}
```