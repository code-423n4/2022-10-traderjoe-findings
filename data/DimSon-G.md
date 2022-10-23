## [G-01] Using `private` rather than `public` for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

_There are 4 instance(s) of this issue:_

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L25)

```
File: src/LBFactory.sol   #1

25:  uint256 public constant override MAX_FEE = 0.1e18; // 10%   
```

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L27)

```
File: src/LBFactory.sol   #2

27:  uint256 public constant override MIN_BIN_STEP = 1; // 0.01%  
```

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L28)

```
File: src/LBFactory.sol   #3

28:  uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons 
```

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L30)

```
File: src/LBFactory.sol   #4

30:  uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%   
```

## [G-02] Using `bool`s for storage incurs overhead

Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past

_There are 2 instance(s) of this issue:_

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L39)

```
File: src/LBFactory.sol   #1

39:  bool public override creationUnlocked;
```

[LBToken](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L21)

```
File: src/LBToken.sol   #2

21:  mapping(address => mapping(address => bool)) private _spenderApprovals;
```

## [G-03] Multiple accesses of a mapping should use a local variable cache

The instances below point to the second+ access of a value inside a mapping, within a function. Caching a mapping's value in a local `storage` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

_There are 4 instance(s) of this issue:_

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L270)

```
File: src/LBFactory.sol   #1

270:  _LBPairsInfo[_tokenA][_tokenB][_binStep] = LBPairInformation({ 
```

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L325)

```
File: src/LBFactory.sol   #2

325:  _LBPairsInfo[_tokenA][_tokenB][_binStep].ignoredForRouting = _ignored; 
```

[LBPair](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L453)

```
File: src/LBPair.sol   #3

453:  _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply); 
```

[LBPair](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L453)

```
File: src/LBPair.sol   #4

871:  _accruedDebts[_account][_id].debtY = _debtY; 
```