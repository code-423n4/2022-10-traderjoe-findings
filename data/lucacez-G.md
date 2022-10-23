### ++I COSTS LESS GAS COMPARED TO I++ OR I += 1 IN FOR LOOPS (~5 GAS PER ITERATION)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.
i++ increments i and returns the initial value of i. Which means:
```
uint i = 1;  
i++; // == 1 but i == 2  
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2
Instances include:

File: LBQuoter.sol [Line 75](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)
```
        for (uint256 i; i < swapLength; i++) {
```
File: LBQuoter.sol [Line 100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)
```
                for (uint256 j; j < LBPairsAvailable.length; j++) {
```
File: LBQuoter.sol [Line 177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)
```
                for (uint256 j; j < LBPairsAvailable.length; j++) {
```

### X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
File: LBFactory.sol [Line 143](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L143)
```
        baseFactor = _preset.decode(type(uint16).max, _shift += 16);
```
File: LBFactory.sol [Line 144](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L144)
```
        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
```
File: LBFactory.sol [Line 145](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L145)
```
        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
```
File: LBFactory.sol [Line 146](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L146)
```
        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
```
File: LBFactory.sol [Line 147](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L147)
```
        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
```
File: LBFactory.sol [Line 148](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L148)
```
        protocolShare = _preset.decode(type(uint16).max, _shift += 24);
```
File: LBFactory.sol [Line 149](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L149)
```
        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
```
File: LBPair.sol [Line 227](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L227)
```
                cumulativeId += _activeId * _deltaT;
```
File: LBPair.sol [Line 228](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L228)
```
                cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
```
File: LBPair.sol [Line 287](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L287)
```
                    amountX += _amountX;
```
File: LBPair.sol [Line 288](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L288)
```
                    amountY += _amountY;
```
File: LBPair.sol [Line 337](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L337)
```
                _amountOut += _amountOutOfBin;
```
File: LBPair.sol [Line 452](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L452)
```
        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
```
File: LBPair.sol [Line 453](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L453)
```
        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
```
File: LBPair.sol [Line 508](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L508)
```
                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||
```
File: LBPair.sol [Line 509](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L509)
```
                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION
```
File: LBPair.sol [Line 540](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L540)
```
                            _mintInfo.activeFeeX += _fees.total;
```
File: LBPair.sol [Line 551](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L551)
```
                            _mintInfo.activeFeeY += _fees.total;
```
File: LBPair.sol [Line 572](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L572)
```
                _pair.reserveX += uint112(_mintInfo.amountX);
```
File: LBPair.sol [Line 573](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L573)
```
                _pair.reserveY += uint112(_mintInfo.amountY);
```
File: LBPair.sol [Line 575](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L575)
```
                _mintInfo.amountXAddedToPair += _mintInfo.amountX;
```
File: LBPair.sol [Line 576](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L576)
```
                _mintInfo.amountYAddedToPair += _mintInfo.amountY;
```
File: LBPair.sol [Line 639](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L639)
```
                    amountY += _amountY;
```
File: LBPair.sol [Line 639](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L639)
```
                    amountY += _amountY;
```
File: LBPair.sol [Line 646](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L646)
```
                    amountX += _amountX;
```
File: LBPair.sol [Line 711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L711)
```
                    amountX += _amountX;
```
File: LBPair.sol [Line 712](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L712)
```
                    amountY += _amountY;
```
File: LBPair.sol [Line 896](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L896)
```
            (amountX += _amountX).safe128();
```
File: LBPair.sol [Line 897](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L897)
```
            (amountY += _amountY).safe128();
```
File: LBRouter [Line 128](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L128)
```
                amountIn += _amountInWithFees;
```
File: LBRouter [Line 129](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L129)
```
                feesIn += _fee;
```
File: LBRouter [Line 174](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L174)
```
                feesIn += _fees.total;
```
File: LBRouter [Line 175](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L175)
```
                amountOut += _amountOutOfBin;
```
File: LBToken [Line 211](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L211)
```
        _totalSupplies[_id] += _amount;
```
File: BitMath [Line 77](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L77)
```
                msb += 64;
```
File: BitMath [Line 81](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L81)
```
                msb += 32;
```
File: BitMath [Line 85](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L85)
```
                msb += 16;
```
File: BitMath [Line 89](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L89)
```
                msb += 8;
```
File: BitMath [Line 93](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L93)
```
                msb += 4;
```
File: BitMath [Line 97](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L97)
```
                msb += 2;
```
File: BitMath [Line 100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L100)
```
                msb += 1;
```
File: BitMath [Line 116](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L116)
```
                lsb += 64;
```
File: BitMath [Line 120](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L120)
```
                lsb += 32;
```
File: BitMath [Line 124](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L124)
```
                lsb += 16;
```
File: BitMath [Line 128](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L128)
```
                lsb += 8;
```
File: BitMath [Line 132](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L132)
```
                lsb += 4;
```
File: BitMath [Line 136](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L136)
```
                lsb += 2;
```
File: BitMath [Line 139](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L139)
```
                lsb += 1;
```
File: FeeDistributionHelper [Line 42](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L42)
```
            _pairFees.protocol += _fees.protocol;
```
File: Math128x128 [Line 77](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L77)
```
                        result += delta;
```
File: Math512Bits [Line 71](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L71)
```
                result += prod1 << (256 - offset);
```
File: Math512Bits [Line 99](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L99)
```
            if (mulmod(x, y, 1 << offset) != 0) result += 1;
```
File: Math512Bits [Line 159](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L159)
```
            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;
```
File: SwapHelper [Line 88](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L88)
```
        pairFees.total += fees.total;
```
File: SwapHelper [Line 91](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L91)
```
            pairFees.protocol += fees.protocol;
```
File: SwapHelper [Line 95](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L95)
```
            bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);
```
File: SwapHelper [Line 97](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L97)
```
            bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);
```
File: SwapHelper [Line 115](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L115)
```
            bin.reserveX += amountInToBin;
```
File: SwapHelper [Line 119](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L119)
```
                pair.reserveX += uint136(amountInToBin);
```
File: SwapHelper [Line 123](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L123)
```
            bin.reserveY += amountInToBin;
```
File: SwapHelper [Line 128](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L128)
```
                pair.reserveY += uint136(amountInToBin);
```

### USING BOOLS FOR STORAGE INCURS OVERHEAD
See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

Instances affected include
File: LBFactory [Line 39](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L39)
```
    bool public override creationUnlocked;
```
File: LBToken [Line 21](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L21)
```
    mapping(address => mapping(address => bool)) private _spenderApprovals;
```
### USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

File: LBFactory [Line 25](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L25)
```
    uint256 public constant override MAX_FEE = 0.1e18; // 10%
```
File: LBFactory [Line 27](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L27)
```
    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
```
File: LBFactory [Line 28](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L28)
```
    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
```
File: LBFactory [Line 30](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L30)
```
    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```
### OPTIMIZE NAMES TO SAVE GAS

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)