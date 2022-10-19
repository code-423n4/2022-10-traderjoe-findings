## 1. Use a more recent version of solidity
  
- Use a solidity version of at least 0.8.2 to get compiler automatic inlining  
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads  
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings  
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
All contracts
```

## 2. `++i` costs less gas compared to `i++` or `i += 1`, same for `--i/i--`. Especially when used in for loops

Pre-increments and pre-decrements are cheaper.

For a `uint256` i variable, the following is true with the Optimizer enabled at 10k:

Increment:

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

Decrement:

- `i -= 1` is the most expensive form
- `i`--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:

```
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
However, pre-increments (or pre-decrements) return the new value:

```
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```


In the pre-increment case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2.

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).


```
75       for (uint256 i; i < swapLength; i++) {
100      for (uint256 j; j < LBPairsAvailable.length; j++) {
154      for (uint256 i = swapLength; i > 0; i--) {
177      for (uint256 j; j < LBPairsAvailable.length; j++) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol

```
711      for (uint256 i = _pairs.length; i != 0; i--) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol


## 3.Using `bool`s for storage incurs overhead


// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from `false` to `true`, after having been `true` in the past

```
39       bool public override creationUnlocked;
316      bool _ignored
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol
```
237      bool _swapForY
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol
```
93       bool _swapForY
151      bool _swapForY
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol
```
256      bool _approved
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol
```
72       bool approveMax,
85       bool approveMax,
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter01.sol
```
26       bool approveMax,
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeRouter02.sol
```
22       bool createdByOwner;
23       bool ignoredForRouting;
141      bool ignored
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol
```
59       bool swapForY
65       bool swapForY
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol
```
17       bool _rightSide
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol
```
95       bool invert;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol
```
35       bool swapForY,
85       bool swapForY,
110      bool swapForY,
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol
```
24       bool _rightSide
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol


## 4. Usage of `uint/int` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

```
In almost all contracts there are uint8/uint16/uint24/uint64/uint128 variables
```

## 5. Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback()` payable functions that are not used, can be removed to save deployment gas.

```
321	function _beforeTokenTransfer(
322		address from,
323		address to,
324		uint256 id,
325		uint256 amount
326	) internal virtual {}
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol

## 6. Not using the named return variables when a function returns, wastes deployment gas

```
30	return uint24(uint256(_id));
44	return _getBPValue(_binStep).power(_realId);
56	return Constants.SCALE + (_binStep << Constants.SCALE_OFFSET) / Constants.BASIS_POINT_MAX;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol
```
20       return closestBitRight(_integer, _bit - 1);
21       } else return closestBitLeft(_integer, _bit + 1);
30       return mostSignificantBit(_integer);
31       } else return leastSignificantBit(_integer);
47       return mostSignificantBit(x) - _shift;
62       return leastSignificantBit(x) + bit;
142      return 255 - lsb;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol
```
58	return ((uint256(_fees.total) - _fees.protocol) << Constants.SCALE_OFFSET) / _totalSupply;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol
```
93	return uint256(_fp.baseFactor) * _fp.binStep * 1e10;
117	return (_amount * getTotalFee(_fp)) / (Constants.PRECISION);
126	return (_amountPlusFee * _fee) / (Constants.PRECISION + _fee);
135	return (_amountPlusFee * _fee * (_fee + Constants.PRECISION)) / (Constants.PRECISION * Constants.PRECISION);
159	return getBaseFee(_fp) + getVariableFee(_fp);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol
```
37	return _getEndOfDivRoundDown(x, y, denominator, prod0, prod1);
133	return _getEndOfDivRoundDown(x, 1 << offset, denominator, prod0, prod1);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol
```
58	return pack(_cumulativeBinCrossed, _cumulativeVolatilityAccumulated, _cumulativeId, block.timestamp, 1);
91	return _packedSample.decode(_MASK_INITIALIZED, _OFFSET_INITIALIZED);
98	return _packedSample.decode(_MASK_TIMESTAMP, _OFFSET_TIMESTAMP);
105	return _packedSample.decode(_MASK_CUMULATIVE_ID, _OFFSET_CUMULATIVE_ID);
112	return _packedSample.decode(_MASK_CUMULATIVE_VolatilityAccumulated, _OFFSET_CUMULATIVE_VolatilityAccumulated);
119	return _packedSample.decode(_MASK_CUMULATIVE_BIN_CROSSED, _OFFSET_CUMULATIVE_BIN_CROSSED);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol
```
68	return token.balanceOf(address(this)) - _internalBalance;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TokenHelper.sol
```
38	return _getBottomId(_binId, uint24(bit));
53	return _getBottomId(_binId, current.significantBit(_rightSide));
66	return _getBottomId(_binId, current.significantBit(_rightSide));
106	return (_id >> 8, _id & 255);
117	return (_branchId << 8) + _leafId;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol
```
244	return _tree.findFirstBin(_id, _swapForY);
607	return (_mintInfo.amountXAddedToPair, _mintInfo.amountYAddedToPair, liquidityMinted);
1007	return (reserveX.safe112(), reserveY.safe112());
1045	return (_amount * _fee) / Constants.PRECISION;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol
```
73	return BinHelper.getIdFromPrice(_price, _LBPair.feeParameters().binStep);
81	return BinHelper.getPriceFromId(_id, _LBPair.feeParameters().binStep);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol

## 7. Bytes constants are more efficient than string constants


From the Solidity doc:

* If you can limit the length to a certain number of bytes, always use one of bytes1 to bytes32 because they are much cheaper.

Why do Solidity examples use bytes32 type instead of string (https://ethereum.stackexchange.com/questions/3795/why-do-solidity-examples-use-bytes32-type-instead-of-string)?

* bytes32 uses less gas because it fits in a single word of the EVM, and string is a dynamically sized-type which has current limitations in Solidity (such as can’t be returned from a function to a contract).

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity. Basically, any fixed size variable in solidity is cheaper than variable size. That will save gas on the contract.

Instances of string constant that can be replaced by bytes(1..32) constant

```
29       string private constant _name = "Liquidity Book Token";
30       string private constant _symbol = "LBT";
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol

## 8. Constructor parameters should be avoided when possible

Constructor parameters are expensive. The contract deployment will be cheaper in gas if they are hard coded instead of using constructor parameters.

```
45	routerV2 = _routerV2;
46	factoryV1 = _factoryV1;
47	factoryV2 = _factoryV2;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol
```
57	factory = _factory;
58	oldFactory = _oldFactory;
59	wavax = _wavax;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol






