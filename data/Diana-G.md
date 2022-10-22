## G-01  x += y costs more gas than x = x+y for state variables

There are 79 instances of this issue in [LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol) , [LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol), [LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol), [BitMath.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol), [FeeDistributionHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol), [Math128x128.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol), [Math512Bits.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol), [SwapHelper.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol)

Some instances include:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol

```
File: src/LBFactory.sol

143: baseFactor = _preset.decode(type(uint16).max, _shift += 16);
144: filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
145: decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
146: reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
147: variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
148: protocolShare = _preset.decode(type(uint16).max, _shift += 24);
149: maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
```

-------
## G-02  Usage of uints or ints smaller than 32 bytes (26 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

There are multiple instances of this issue

Some instances include:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol

```
File: src/LBErrors.sol

57: error LBFactory__DecreasingPeriods(uint16 filterPeriod, uint16 decayPeriod);
58: error LBFactory__BaseFactorOverflows(uint16 baseFactor, uint256 max);
59: error LBFactory__ReductionFactorOverflows(uint16 reductionFactor, uint256 max);
60: error LBFactory__VariableFeeControlOverflows(uint16 variableFeeControl, uint256 max);
63: error LBFactory__BinStepRequirementsBreached(uint256 lowerBound, uint16 binStep, uint256 higherBound);
64: error LBFactory__ProtocolShareOverflows(uint16 protocolShare, uint256 max);
```

--------------

## G-03  ++i costs less gas than i++ especially when it's used in for loops (Same applies for --i , i-- too)

Prefix increments are cheaper than postfix increments.  

Further more, using unchecked {++i} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change  

There is no risk of overflow caused by incrementing the iteration index in for loops (the `++i` in `for (uint256 i; i < swapLength; ++i)`).  

But increments perform overflow checks that are not necessary in this case.  

These functions use not using prefix increments (`++i`) and/or not using the unchecked keyword:

There are 5 instances of this issue:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol

```
File: src/LBQuoter.sol

75:  for (uint256 i; i < swapLength; i++) {
100: for (uint256 j; j < LBPairsAvailable.length; j++) {
154: for (uint256 i = swapLength; i > 0; i--) {
177: for (uint256 j; j < LBPairsAvailable.length; j++)
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol

```
File: src/LBRouter.sol

711: for (uint256 i = _pairs.length; i != 0; i--) {
```
-------------

## G-04  Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

There is 1 instance of this issue:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L321-L326

```
File: src/LBToken.sol    Lines: 321-326

    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 id,
        uint256 amount
    ) internal virtual {}
```

-------

## G-05  Multiplication or division by 2 should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L162

```
File: src/libraries/Oracle.sol

162: _middle = (_low + _high) / 2;
```

-----------

## G-06  It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol#L19

```
File: src/libraries/Samples.sol

19: uint256 private constant _OFFSET_INITIALIZED = 0;
```

---


