## Trader Joe v2 Report

### Gas Optimization Summary

In general, the code is of good quality but mostly unoptimized for gas. This review of gas optimizations focused on main cases and quick wins. A couple of corner case optimizations were found that introduce extra gas costs for main case uses. It's always better to optimize for main-cases before corner cases.

Some instances have been combined to improve readability. Some checks were excluded due to overlap with c4udit scan results, whose findings are considered publicly known.

### Gas Optimizations
 
| |Issue|Instances|
|-|:-|:-:|
| [ G-01 ] | Using += is less gas efficient than X = X + Y. | 55 |
| [ G-02 ] | Overhead incurred when using uints/ints smaller than 32 bytes (256 bits). | 20 |
| [ G-03 ] | Combining multiple address mappings into a single mapping to a struct can save gas. | 2 |
| [ G-04 ] | G000X - Bytes32 is cheaper than string. EVM treats string state variables as a dynamically sized type, but bytes32 is fixed. Because of this, bytes32 has a lower gas overhead. Use bytes32 for shorter strings to save gas. | 2 |
| [ G-05 ] | Using a separate function to return a tuple of constants is cheaper where multiple constants are used. | 4 |
| [ G-06 ] | Corner-case transfer optimization is inefficient in main use cases. | 2 |
  
### G-01 Using += is less gas efficient than X = X + Y.

Adding a variable using += is 22 gas more expensive than using the X = X + Y route. See [this Gist](https://gist.github.com/0xmattdrag/8521f2b225bf00199b038b57268eb581) for example code and test results. Under some circumstances the saving can be 44 gas due to stack operations. It's a small gas saving but adds up when encountered in loops or commonly used libraries (such as maths functions).

While 55 instances were identified, nearby code has been merged for readability purposes.

*55 instances were found.*

```solidity
	src/libraries/BitMath.sol:
  77:|                msb += 64;
  78:|            }
  79:|            if (x >= 1 << 32) {
  80:|                x >>= 32;
  81:|                msb += 32;
  82:|            }
  83:|            if (x >= 1 << 16) {
  84:|                x >>= 16;
  85:|                msb += 16;
  86:|            }
  87:|            if (x >= 1 << 8) {
  88:|                x >>= 8;
  89:|                msb += 8;
  90:|            }
  91:|            if (x >= 1 << 4) {
  92:|                x >>= 4;
  93:|                msb += 4;
  94:|            }
  95:|            if (x >= 1 << 2) {
  96:|                x >>= 2;
  97:|                msb += 2;
  98:|            }
  99:|            if (x >= 1 << 1) {
 100:|                msb += 1;
 101:|            }
 102:|        }
 103:|    }
 104:|
 105:|    /// @notice Returns the index of the least significant bit of x
 106:|    /// @param x The value as a uint256
 107:|    /// @return lsb The index of the least significant bit of x
 108:|    function leastSignificantBit(uint256 x) internal pure returns (uint8 lsb) {
 109:|        unchecked {
 110:|            if (x << 128 != 0) {
 111:|                x <<= 128;
 112:|                lsb = 128;
 113:|            }
 114:|            if (x << 64 != 0) {
 115:|                x <<= 64;
 116:|                lsb += 64;
 117:|            }
 118:|            if (x << 32 != 0) {
 119:|                x <<= 32;
 120:|                lsb += 32;
 121:|            }
 122:|            if (x << 16 != 0) {
 123:|                x <<= 16;
 124:|                lsb += 16;
 125:|            }
 126:|            if (x << 8 != 0) {
 127:|                x <<= 8;
 128:|                lsb += 8;
 129:|            }
 130:|            if (x << 4 != 0) {
 131:|                x <<= 4;
 132:|                lsb += 4;
 133:|            }
 134:|            if (x << 2 != 0) {
 135:|                x <<= 2;
 136:|                lsb += 2;
 137:|            }
 138:|            if (x << 1 != 0) {
 139:|                lsb += 1;
```
*14 Instances were identified above.*
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L77-L139

```solidity
	src/LBRouter.sol:
 128:|                amountIn += _amountInWithFees;
 129:|                feesIn += _fee;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L128-L129

```solidity
	src/LBRouter.sol:
 174:|                feesIn += _fees.total;
 175:|                amountOut += _amountOutOfBin;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L174-L175

```solidity
	src/libraries/Math512Bits.sol:
  71:|                result += prod1 << (256 - offset);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L71

```solidity
	src/libraries/Math512Bits.sol:
  99:|            if (mulmod(x, y, 1 << offset) != 0) result += 1;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L99

```solidity
	src/libraries/Math512Bits.sol:
 159:|            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L159

```solidity
	src/LBToken.sol:
 211:|        _totalSupplies[_id] += _amount;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L211

```solidity
	src/LBPair.sol:
 227:|                cumulativeId += _activeId * _deltaT;
 228:|                cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L227-L228

```solidity
	src/LBPair.sol:
 287:|                    amountX += _amountX;
 288:|                    amountY += _amountY;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L287-L288

```solidity
	src/LBPair.sol:
 337:|                _amountOut += _amountOutOfBin;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L337

```solidity
	src/LBPair.sol:
 452:|        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
 453:|        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L452-L453

```solidity
	src/LBPair.sol:
 540:|                            _mintInfo.activeFeeX += _fees.total;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L540

```solidity
	src/LBPair.sol:
 551:|                            _mintInfo.activeFeeY += _fees.total;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L551

```solidity
	src/LBPair.sol:
 572:|                _pair.reserveX += uint112(_mintInfo.amountX);
 573:|                _pair.reserveY += uint112(_mintInfo.amountY);
 574:|
 575:|                _mintInfo.amountXAddedToPair += _mintInfo.amountX;
 576:|                _mintInfo.amountYAddedToPair += _mintInfo.amountY;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L572-L576

```solidity
	src/LBPair.sol:
 639:|                    amountY += _amountY;
 640:|                    _reserveY -= _amountY;
 641:|                    _pairReserveY -= _amountY;
 642:|                }
 643:|                if (_id >= _activeId) {
 644:|                    _amountX = _amountToBurn.mulDivRoundDown(_reserveX, _totalSupply);
 645:|
 646:|                    amountX += _amountX;
```
*2 Instances were identified above.*
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L639-L646

```solidity
	src/LBPair.sol:
 711:|                    amountX += _amountX;
 712:|                    amountY += _amountY;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L711-L712

```solidity
	src/LBPair.sol:
 896:|            (amountX += _amountX).safe128();
 897:|            (amountY += _amountY).safe128();
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L896-L897

```solidity
	src/libraries/SwapHelper.sol:
  88:|        pairFees.total += fees.total;
  89:|        // unsafe math is fine because total >= protocol
  90:|        unchecked {
  91:|            pairFees.protocol += fees.protocol;
  92:|        }
  93:|
  94:|        if (swapForY) {
  95:|            bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);
  96:|        } else {
  97:|            bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);
```
*4 Instances were identified above.*
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L88-L97

```solidity
	src/libraries/SwapHelper.sol:
 115:|            bin.reserveX += amountInToBin;
 116:|
 117:|            unchecked {
 118:|                bin.reserveY -= amountOutOfBin;
 119:|                pair.reserveX += uint136(amountInToBin);
 120:|                pair.reserveY -= uint136(amountOutOfBin);
 121:|            }
 122:|        } else {
 123:|            bin.reserveY += amountInToBin;
 124:|
 125:|            unchecked {
 126:|                bin.reserveX -= amountOutOfBin;
 127:|                pair.reserveX -= uint136(amountOutOfBin);
 128:|                pair.reserveY += uint136(amountInToBin);
```
*4 Instances were identified above.*
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L115-L128

```solidity
	src/LBFactory.sol:
 143:|        baseFactor = _preset.decode(type(uint16).max, _shift += 16);
 144:|        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
 145:|        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
 146:|        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
 147:|        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
 148:|        protocolShare = _preset.decode(type(uint16).max, _shift += 24);
 149:|        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
```
*7 Instances were identified above.*
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L149

```solidity
	src/libraries/FeeDistributionHelper.sol:
  42:|            _pairFees.protocol += _fees.protocol;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L42

### G-02 Overhead incurred when using uints/ints smaller than 32 bytes (256 bits).

The EVM operates 32 bytes at a time. For elements smaller than 32 bytes, the EVM needs to clear higher bits, which involves stack operations. This means that smaller integers are less gas efficient than 256 bit integers, for example.

It may not be feasible to implement in all cases. For example Trader Joe v2 uses 128.128 fixed-point arithmetic. Where no unwanted effects will occur, consider using 256 bit integers where practical.

Instances have been merged for readability purposes.

*20 instances were found.*

```solidity
src/libraries/FeeHelper.sol:
30: | uint16 binStep;
31: | uint16 baseFactor;
32: | uint16 filterPeriod;
33: | uint16 decayPeriod;
34: | uint16 reductionFactor;
35: | uint24 variableFeeControl;
36: | uint16 protocolShare;
37: | uint24 maxVolatilityAccumulated;
38: | uint24 volatilityAccumulated;
39: | uint24 volatilityReference;
40: | uint24 indexRef;
41: | uint40 time;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L30-L41

```solidity
src/interfaces/ILBPair.sol:
43: | uint16 oracleSampleLifetime;
44: | uint16 oracleSize;
45: | uint16 oracleActiveSize;
46: | uint40 oracleLastTimestamp;
47: | uint16 oracleId;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L43-L47

```solidity
src/interfaces/ILBPair.sol:
64: | uint128 tokenX;
65: | uint128 tokenY;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L64-L65

```solidity
src/libraries/FeeDistributionHelper.sol:
31: | uint128 _totalFees = _pairFees.total;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L31-L31

### G-03 Combining multiple address mappings into a single mapping to a struct can save gas. 

Instead of using multiple address mappings, consider using a single address mapping that maps to a struct. This saves a storage slot and up to 20k in gas depending on whether a variable is assigned a zero (2900 gas), non-zero (20k gas) value or is unassigned but public (non-payable getter deployment cost is saved).

*4 instances were found.*

```solidity
src/LBToken.sol:
21: | mapping(address => mapping(address => bool)) private _spenderApprovals;
22: |
23: | /// @dev Mapping from token id to total supplies
24: | mapping(uint256 => uint256) private _totalSupplies;
25: |
26: | /// @dev Mapping from account to set of ids, where user currently have a non-zero balance
27: | mapping(address => EnumerableSet.UintSet) private _userIds;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L21-L27

```solidity
src/LBPair.sol:
68: | mapping(address => bytes32) private _unclaimedFees;
69: | /// @dev Mapping from account to id to user's accruedDebt.
70: | mapping(address => mapping(uint256 => Debts)) private _accruedDebts;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L68-L70

### G-04 Bytes32 is cheaper than string.

EVM treats string state variables as a dynamically sized type, but bytes32 is fixed. Because of this, bytes32 has a lower gas overhead. Use bytes32 for shorter strings to save gas.

*2 instances were found.*

```solidity
src/LBToken.sol:
29: | string private constant _name = "Liquidity Book Token";
30: | string private constant _symbol = "LBT";
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29-L30

### G-05 Using a separate function to return a tuple of constants is cheaper where multiple constants are used.

The solc compiler creates a getter function for each public constant. This adds extra deployment gas costs, and an entry in the method ID table. This doesn't happen with private constants. It's often more gas-efficient to write a public function returning a tuple of private constants than define individual public constants. Consider doing this for the following constants.

*4 instances were found.*

```solidity
src/LBFactory.sol:
25: | uint256 public constant override MAX_FEE = 0.1e18; // 10%
26: |
27: | uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
28: | uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
29: |
30: | uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25% 
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30

### G-06 Corner-case transfer optimization is inefficient in main use cases

The safeTransfer() and safeTransferFrom() functions in TokenHelper.sol check to see that the supplied amount is not 0 before transferring. When the user-supplied amount is 0 this saves a substantial amount of gas. However, the if-statement requires gas to execute in more common cases (when amount > 0).

ERC20 Tokens behave differently, as ERC20 is little more than a guide to an interface than an implementation. Some ERC20 tokens will revert if 0 is being transferred, while others will execute the order. Either way, the gas cost for such checks can probably be passed on to individual ERC20 tokens rather than here.

*2 instances were found.*

```solidity
if (amount != 0) {
	(bool success, bytes memory result) = address(token).call(
		abi.encodeWithSelector(token.transferFrom.selector, owner, recipient, amount)
	);
	_catchTransferError(success, result);
}
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TokenHelper.sol#L27-L33

```solidity
if (amount != 0) {
	(bool success, bytes memory result) = address(token).call(
		abi.encodeWithSelector(token.transfer.selector, recipient, amount)
	);
	_catchTransferError(success, result);
}
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TokenHelper.sol#L45-L51