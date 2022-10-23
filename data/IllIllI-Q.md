## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `require()` should be used instead of `assert()` | 1 |
| [L&#x2011;02] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 5 |

Total: 6 instances over 2 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 1 |
| [N&#x2011;02] | Missing `initializer` modifier on constructor | 1 |
| [N&#x2011;03] | Missing `initializer` modifier | 1 |
| [N&#x2011;04] | Unused file | 1 |
| [N&#x2011;05] | `public` functions not called by the contract should be declared `external` instead | 2 |
| [N&#x2011;06] | Use bit shifts in an imutable variable rather than long bit masks of a single bit, for readability | 1 |
| [N&#x2011;07] | Use a more recent version of solidity | 14 |
| [N&#x2011;08] | Use a more recent version of solidity | 1 |
| [N&#x2011;09] | Constant redefined elsewhere | 1 |
| [N&#x2011;10] | Lines are too long | 2 |
| [N&#x2011;11] | File is missing NatSpec | 1 |
| [N&#x2011;12] | NatSpec is incomplete | 3 |
| [N&#x2011;13] | Event is missing `indexed` fields | 19 |
| [N&#x2011;14] | Not using the named return variables anywhere in the function is confusing | 12 |

Total: 60 instances over 14 issues

## Low Risk Issues

### [L&#x2011;01]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There is 1 instance of this issue:*
```solidity
File: src/LBFactory.sol

141:          assert(_binStep == _preset.decode(type(uint16).max, _shift));

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L141

### [L&#x2011;02]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 5 instances of this issue:*
```solidity
File: src/LBQuoter.sol

45:           routerV2 = _routerV2;

46:           factoryV1 = _factoryV1;

47:           factoryV2 = _factoryV2;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L45

```solidity
File: src/libraries/PendingOwnable.sol

93:           _owner = _newOwner;

102:          _pendingOwner = pendingOwner_;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L93

## Non-critical Issues

### [N&#x2011;01]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There is 1 instance of this issue:*
```solidity
File: src/LBPair.sol

27:   contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27

### [N&#x2011;02]  Missing `initializer` modifier on constructor
OpenZeppelin [recommends](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5) that the `initializer` modifier be applied to constructors in order to avoid potential griefs, [social engineering](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/4), or exploits. Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.

*There is 1 instance of this issue:*
```solidity
File: src/LBPair.sol

90:       constructor(ILBFactory _factory) LBToken() {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L90

### [N&#x2011;03]  Missing `initializer` modifier
The contract extends `Initializable`/`ReentrancyGuardUpgradeable` but does not use the `initializer` modifier anywhere

*There is 1 instance of this issue:*
```solidity
File: src/LBPair.sol

27:   contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L27

### [N&#x2011;04]  Unused file
The file is never imported by any other file

*There is 1 instance of this issue:*
```solidity
File: src/interfaces/IJoeRouter02.sol

0:    // SPDX-License-Identifier: GPL-3.0

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter02.sol#L0

### [N&#x2011;05]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 2 instances of this issue:*
```solidity
File: src/LBQuoter.sol

54        function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
55            public
56            view
57:           returns (Quote memory quote)

134       function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
135           public
136           view
137:          returns (Quote memory quote)

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L54-L57

### [N&#x2011;06]  Use bit shifts in an imutable variable rather than long bit masks of a single bit, for readability

*There is 1 instance of this issue:*
```solidity
File: src/libraries/Math128x128.sol

108:          if (absY < 0x100000) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L108

### [N&#x2011;07]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There are 14 instances of this issue:*
```solidity
File: src/LBPair.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L3

```solidity
File: src/LBQuoter.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L3

```solidity
File: src/LBRouter.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L3

```solidity
File: src/LBToken.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L3

```solidity
File: src/libraries/BinHelper.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L3

```solidity
File: src/libraries/FeeDistributionHelper.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L3

```solidity
File: src/libraries/FeeHelper.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L3

```solidity
File: src/libraries/Math128x128.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L3

```solidity
File: src/libraries/Math512Bits.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L3

```solidity
File: src/libraries/Oracle.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L3

```solidity
File: src/libraries/Samples.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L3

```solidity
File: src/libraries/SwapHelper.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L3

```solidity
File: src/libraries/TokenHelper.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TokenHelper.sol#L3

```solidity
File: src/libraries/TreeMath.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol#L3

### [N&#x2011;08]  Use a more recent version of solidity
Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)`
Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*
```solidity
File: src/LBFactory.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L3

### [N&#x2011;09]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There is 1 instance of this issue:*
```solidity
File: src/LBRouter.sol

/// @audit seen in src/LBPair.sol 
28:       ILBFactory public immutable override factory;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L28

### [N&#x2011;10]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 2 instances of this issue:*
```solidity
File: src/libraries/Oracle.sol

69:                       uint256 _weightPrev = _next.timestamp() - _lookUpTimestamp; // _next.timestamp() - _sample.timestamp() - (_lookUpTimestamp - _sample.timestamp())

70:                       uint256 _weightNext = _lookUpTimestamp - _sample.timestamp(); // _next.timestamp() - _sample.timestamp() - (_next.timestamp() - _lookUpTimestamp)

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L69

### [N&#x2011;11]  File is missing NatSpec

*There is 1 instance of this issue:*
```solidity
File: src/LBErrors.sol


```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBErrors.sol

### [N&#x2011;12]  NatSpec is incomplete

*There are 3 instances of this issue:*
```solidity
File: src/LBFactory.sol

/// @audit Missing: '@return'
531       /// @param _protocolShare The share of the fees received by the protocol
532       /// @param _maxVolatilityAccumulated The max value of volatility accumulated
533       function _getPackedFeeParameters(
534           uint16 _binStep,
535           uint16 _baseFactor,
536           uint16 _filterPeriod,
537           uint16 _decayPeriod,
538           uint16 _reductionFactor,
539           uint24 _variableFeeControl,
540           uint16 _protocolShare,
541           uint24 _maxVolatilityAccumulated
542:      ) private pure returns (bytes32) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L531-L542

```solidity
File: src/LBRouter.sol

/// @audit Missing: '@return'
739       /// @param amountX The amount of token X sent by the pair
740       /// @param amountY The amount of token Y sent by the pair
741       function _removeLiquidity(
742           ILBPair _LBPair,
743           uint256 _amountXMin,
744           uint256 _amountYMin,
745           uint256[] memory _ids,
746           uint256[] memory _amounts,
747           address _to
748:      ) private returns (uint256 amountX, uint256 amountY) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L739-L748

```solidity
File: src/libraries/FeeDistributionHelper.sol

/// @audit Missing: '@return'
47        /// @param _fees The fees received by the pair
48        /// @param _totalSupply the total supply of a specific bin
49        function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
50            internal
51            pure
52:           returns (uint256)

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L47-L52

### [N&#x2011;13]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 19 instances of this issue:*
```solidity
File: src/interfaces/IJoeFactory.sol

8:        event PairCreated(address indexed token0, address indexed token1, address pair, uint256);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeFactory.sol#L8

```solidity
File: src/interfaces/IJoePair.sol

8:        event Approval(address indexed owner, address indexed spender, uint256 value);

9:        event Transfer(address indexed from, address indexed to, uint256 value);

49:       event Mint(address indexed sender, uint256 amount0, uint256 amount1);

50:       event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);

51        event Swap(
52            address indexed sender,
53            uint256 amount0In,
54            uint256 amount1In,
55            uint256 amount0Out,
56            uint256 amount1Out,
57            address indexed to
58:       );

59:       event Sync(uint112 reserve0, uint112 reserve1);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L8

```solidity
File: src/interfaces/ILBFactory.sol

34:       event FeeRecipientSet(address oldRecipient, address newRecipient);

36:       event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);

38        event FeeParametersSet(
39            address indexed sender,
40            ILBPair indexed LBPair,
41            uint256 binStep,
42            uint256 baseFactor,
43            uint256 filterPeriod,
44            uint256 decayPeriod,
45            uint256 reductionFactor,
46            uint256 variableFeeControl,
47            uint256 protocolShare,
48            uint256 maxVolatilityAccumulated
49:       );

51:       event FactoryLockedStatusUpdated(bool unlocked);

53:       event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);

55:       event LBPairIgnoredStateChanged(ILBPair indexed LBPair, bool ignored);

57        event PresetSet(
58            uint256 indexed binStep,
59            uint256 baseFactor,
60            uint256 filterPeriod,
61            uint256 decayPeriod,
62            uint256 reductionFactor,
63            uint256 variableFeeControl,
64            uint256 protocolShare,
65            uint256 maxVolatilityAccumulated,
66            uint256 sampleLifetime
67:       );

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L34

```solidity
File: src/interfaces/ILBPair.sol

111       event FlashLoan(
112           address indexed sender,
113           address indexed recipient,
114           uint256 amountX,
115           uint256 amountY,
116           uint256 feesX,
117           uint256 feesY
118:      );

148:      event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);

150:      event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);

152:      event OracleSizeIncreased(uint256 previousSize, uint256 newSize);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L111-L118

```solidity
File: src/interfaces/ILBToken.sol

19:       event ApprovalForAll(address indexed account, address indexed sender, bool approved);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBToken.sol#L19

### [N&#x2011;14]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 12 instances of this issue:*
```solidity
File: src/LBPair.sol

/// @audit reserveX
/// @audit reserveY
/// @audit activeId
132       function getReservesAndId()
133           external
134           view
135           override
136           returns (
137               uint256 reserveX,
138               uint256 reserveY,
139:              uint256 activeId

/// @audit feesXTotal
/// @audit feesYTotal
/// @audit feesXProtocol
/// @audit feesYProtocol
151       function getGlobalFees()
152           external
153           view
154           override
155           returns (
156               uint256 feesXTotal,
157               uint256 feesYTotal,
158               uint256 feesXProtocol,
159:              uint256 feesYProtocol

/// @audit reserveX
/// @audit reserveY
251:      function getBin(uint24 _id) external view override returns (uint256 reserveX, uint256 reserveY) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L132-L139

```solidity
File: src/libraries/Math512Bits.sol

/// @audit result
30        function mulDivRoundDown(
31            uint256 x,
32            uint256 y,
33            uint256 denominator
34:       ) internal pure returns (uint256 result) {

/// @audit result
119       function shiftDivRoundDown(
120           uint256 x,
121           uint256 offset,
122           uint256 denominator
123:      ) internal pure returns (uint256 result) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L30-L34

```solidity
File: src/libraries/Samples.sol

/// @audit packedSample
69        function pack(
70            uint256 _cumulativeBinCrossed,
71            uint256 _cumulativeVolatilityAccumulated,
72            uint256 _cumulativeId,
73            uint256 _timestamp,
74            uint256 _initialized
75:       ) internal pure returns (bytes32 packedSample) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L69-L75


___


## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness


## Non-critical Issues

### [N&#x2011;01]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 6 instances of this issue:*
```solidity
File: src/LBFactory.sol

/// @audit (valid but excluded finding)
3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L3

```solidity
File: src/LBPair.sol

/// @audit (valid but excluded finding)
3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L3

```solidity
File: src/LBQuoter.sol

/// @audit (valid but excluded finding)
3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L3

```solidity
File: src/LBRouter.sol

/// @audit (valid but excluded finding)
3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L3

```solidity
File: src/LBToken.sol

/// @audit (valid but excluded finding)
3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L3

```solidity
File: src/libraries/PendingOwnable.sol

/// @audit (valid but excluded finding)
3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L3



