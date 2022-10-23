## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 1 | - |
| [G&#x2011;02] | State variables can be packed into fewer storage slots | 1 | - |
| [G&#x2011;03] | Using `storage` instead of `memory` for structs/arrays saves gas | 11 | 46200 |
| [G&#x2011;04] | Avoid contract existence checks by using low level calls | 28 | 2800 |
| [G&#x2011;05] | Multiple accesses of a mapping/array should use a local variable cache | 1 | 42 |
| [G&#x2011;06] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 5 | 425 |
| [G&#x2011;07] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 5 | 300 |
| [G&#x2011;08] | Optimize names to save gas | 11 | 242 |
| [G&#x2011;09] | Use a more recent version of solidity | 37 | - |
| [G&#x2011;10] | `>=` costs less gas than `>` | 1 | 3 |
| [G&#x2011;11] | Functions guaranteed to revert when called by normal users can be marked `payable` | 20 | 420 |

Total: 121 instances over 11 issues with **50432 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above and its gas values do not include any excluded findings.



## Gas Optimizations

### [G&#x2011;01]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
```solidity
File: src/LBPair.sol

68        mapping(address => bytes32) private _unclaimedFees;
69        /// @dev Mapping from account to id to user's accruedDebt.
70:       mapping(address => mapping(uint256 => Debts)) private _accruedDebts;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L68-L70

### [G&#x2011;02]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

*There is 1 instance of this issue:*
```solidity
File: src/LBFactory.sol

/// @audit Variable ordering with 8 slots instead of the current 9:
///           move bool(1):creationUnlocked to be right after address(20):feeRecipient
32:       address public override LBPairImplementation;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L32

### [G&#x2011;03]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 11 instances of this issue:*
```solidity
File: src/LBFactory.sol

197:                          LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L197

```solidity
File: src/LBPair.sol

220:              FeeHelper.FeeParameters memory _fp = _feeParameters;

283:                      Bin memory _bin = _bins[_id];

310:          PairInformation memory _pair = _pairInformation;

318:          FeeHelper.FeeParameters memory _fp = _feeParameters;

327:              Bin memory _bin = _bins[_pair.activeId];

426:          FeeHelper.FeeParameters memory _fp = _feeParameters;

484:          PairInformation memory _pair = _pairInformation;

486:          FeeHelper.FeeParameters memory _fp = _feeParameters;

498:                  Bin memory _bin = _bins[_mintInfo.id];

706:                      Bin memory _bin = _bins[_id];

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L220


### [G&#x2011;04]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 28 instances of this issue:*
```solidity
File: src/LBFactory.sol

/// @audit factory()
216:          if (ILBPair(_LBPairImplementation).factory() != this)

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L216

```solidity
File: src/LBQuoter.sol

/// @audit getPair()
77:               quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);

/// @audit getAllLBPairs()
94:               ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(

/// @audit getSwapOut()
105:                              ILBRouter(routerV2).getSwapOut(LBPairsAvailable[j].LBPair, quote.amounts[i], swapForY)

/// @audit getPair()
156:              quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]);

/// @audit getAllLBPairs()
171:              ILBFactory.LBPairInformation[] memory LBPairsAvailable = ILBFactory(factoryV2).getAllLBPairs(

/// @audit getSwapIn()
181:                              ILBRouter(routerV2).getSwapIn(LBPairsAvailable[j].LBPair, quote.amounts[i], swapForY)

/// @audit getReserves()
223:          (uint256 reserve0, uint256 reserve1, ) = IJoePair(_pair).getReserves();

/// @audit mulShiftRoundDown()
240:              quote = BinHelper.getPriceFromId(_activeId, _binStep).mulShiftRoundDown(_amount, Constants.SCALE_OFFSET);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L77

```solidity
File: src/LBRouter.sol

/// @audit getReserves()
718:                  (uint256 _reserveIn, uint256 _reserveOut, ) = IJoePair(_pair).getReserves();

/// @audit tokenX()
727:                  (amountsIn[i - 1], ) = getSwapIn(ILBPair(_pair), amountsIn[i], ILBPair(_pair).tokenX() == _token);

/// @audit getReserves()
788:                      (uint256 _reserve0, uint256 _reserve1, ) = IJoePair(_pair).getReserves();

/// @audit swap()
792:                          IJoePair(_pair).swap(0, amountOut, _recipient, "");

/// @audit swap()
795:                          IJoePair(_pair).swap(amountOut, 0, _recipient, "");

/// @audit tokenY()
798:                      bool _swapForY = _tokenNext == ILBPair(_pair).tokenY();

/// @audit swap()
800:                      (uint256 _amountXOut, uint256 _amountYOut) = ILBPair(_pair).swap(_swapForY, _recipient);

/// @audit swap()
843:                          IJoePair(_pair).swap(0, amountOut, _recipient, "");

/// @audit swap()
845:                          IJoePair(_pair).swap(amountOut, 0, _recipient, "");

/// @audit tokenY()
848:                      bool _swapForY = _tokenNext == ILBPair(_pair).tokenY();

/// @audit swap()
850:                      (uint256 _amountXOut, uint256 _amountYOut) = ILBPair(_pair).swap(_swapForY, _recipient);

/// @audit getReserves()
888:                      (uint256 _reserve0, uint256 _reserve1, ) = IJoePair(_pair).getReserves();

/// @audit swap()
893:                          IJoePair(_pair).swap(0, _amountOut, _recipient, "");

/// @audit swap()
898:                          IJoePair(_pair).swap(_amountOut, 0, _recipient, "");

/// @audit swap()
/// @audit tokenY()
901:                      ILBPair(_pair).swap(_tokenNext == ILBPair(_pair).tokenY(), _recipient);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L718

```solidity
File: src/libraries/BinHelper.sol

/// @audit power()
44:               return _getBPValue(_binStep).power(_realId);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L44

```solidity
File: src/libraries/TokenHelper.sol

/// @audit call()
28:               (bool success, bytes memory result) = address(token).call(

/// @audit call()
46:               (bool success, bytes memory result) = address(token).call(

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TokenHelper.sol#L28

```diff
diff --git a/src/LBFactory.sol b/src/LBFactory.sol
index 32ee39c..df9d1c7 100644
--- a/src/LBFactory.sol
+++ b/src/LBFactory.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/proxy/Clones.sol";
 import "openzeppelin/utils/structs/EnumerableSet.sol";
diff --git a/src/LBQuoter.sol b/src/LBQuoter.sol
index 53fdf0c..e9f9815 100644
--- a/src/LBQuoter.sol
+++ b/src/LBQuoter.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "./LBErrors.sol";
 import "./libraries/BinHelper.sol";
diff --git a/src/LBRouter.sol b/src/LBRouter.sol
index 567c49a..532cc13 100644
--- a/src/LBRouter.sol
+++ b/src/LBRouter.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/IERC20.sol";
 
diff --git a/src/libraries/BinHelper.sol b/src/libraries/BinHelper.sol
index db0fef8..c6cf99c 100644
--- a/src/libraries/BinHelper.sol
+++ b/src/libraries/BinHelper.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "../LBErrors.sol";
 import "./Math128x128.sol";
diff --git a/src/libraries/TokenHelper.sol b/src/libraries/TokenHelper.sol
index 17d8e23..3391a98 100644
--- a/src/libraries/TokenHelper.sol
+++ b/src/libraries/TokenHelper.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/IERC20.sol";
 
diff --git a/test/LBPair.t.sol b/test/LBPair.t.sol
index d84bb5b..550d55a 100644
--- a/test/LBPair.t.sol
+++ b/test/LBPair.t.sol
@@ -1,5 +1,5 @@
 // SPDX-License-Identifier: UNLICENSED
-pragma solidity 0.8.7;
+pragma solidity 0.8.10;
 
 import "./TestHelper.sol";
 
diff --git a/test/mocks/ERC20.sol b/test/mocks/ERC20.sol
index 0543ab8..4cabd37 100644
--- a/test/mocks/ERC20.sol
+++ b/test/mocks/ERC20.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/ERC20.sol";
 
diff --git a/test/mocks/ERC20MockDecimals.sol b/test/mocks/ERC20MockDecimals.sol
index b952b88..95d1797 100644
--- a/test/mocks/ERC20MockDecimals.sol
+++ b/test/mocks/ERC20MockDecimals.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/ERC20.sol";
 
diff --git a/test/mocks/ERC20MockDecimalsOwnable.sol b/test/mocks/ERC20MockDecimalsOwnable.sol
index 081940f..7ca3b95 100644
--- a/test/mocks/ERC20MockDecimalsOwnable.sol
+++ b/test/mocks/ERC20MockDecimalsOwnable.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/ERC20.sol";
 import "openzeppelin/access/Ownable.sol";
diff --git a/test/mocks/ERC20WithTransferTax.sol b/test/mocks/ERC20WithTransferTax.sol
index 7a7a1f4..269616d 100644
--- a/test/mocks/ERC20WithTransferTax.sol
+++ b/test/mocks/ERC20WithTransferTax.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/ERC20.sol";
 
diff --git a/test/mocks/Faucet.sol b/test/mocks/Faucet.sol
index 108bde7..e00e8fd 100644
--- a/test/mocks/Faucet.sol
+++ b/test/mocks/Faucet.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "../../src/libraries/PendingOwnable.sol";
 import "../../src/libraries/TokenHelper.sol";
diff --git a/test/mocks/FlashloanBorrower.sol b/test/mocks/FlashloanBorrower.sol
index 16817a8..2bfe335 100644
--- a/test/mocks/FlashloanBorrower.sol
+++ b/test/mocks/FlashloanBorrower.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/interfaces/IERC20.sol";
 
diff --git a/test/mocks/WAVAX.sol b/test/mocks/WAVAX.sol
index fd86df0..0ff59a0 100644
--- a/test/mocks/WAVAX.sol
+++ b/test/mocks/WAVAX.sol
@@ -1,6 +1,6 @@
 // SPDX-License-Identifier: MIT
 
-pragma solidity ^0.8.0;
+pragma solidity 0.8.10;
 
 import "openzeppelin/token/ERC20/ERC20.sol";
 
```

```diff
diff --git a/tmp/gas_before b/tmp/gas_after
index f6d4cb0..3e4d40b 100644
--- a/tmp/gas_before
+++ b/tmp/gas_after
@@ -5,3 +5,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 2086657                              ┆ 10631           ┆        ┆        ┆        ┆         │
+│ 2083650                              ┆ 10616           ┆        ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -59,3 +59,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ setLBPairImplementation              ┆ 1524            ┆ 24536  ┆ 25108  ┆ 25108  ┆ 147     │
+│ setLBPairImplementation              ┆ 1396            ┆ 24408  ┆ 24980  ┆ 24980  ┆ 147     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -68,3 +68,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 4835641                        ┆ 24410           ┆         ┆         ┆          ┆         │
+│ 4823808                        ┆ 24344           ┆         ┆         ┆          ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -74,9 +74,9 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ balanceOfBatch                 ┆ 3766            ┆ 7101    ┆ 5769    ┆ 11769    ┆ 3       │
+│ balanceOfBatch                 ┆ 3513            ┆ 6936    ┆ 5648    ┆ 11648    ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ burn                           ┆ 11235           ┆ 111199  ┆ 107074  ┆ 244666   ┆ 14      │
+│ burn                           ┆ 11185           ┆ 111009  ┆ 106900  ┆ 244223   ┆ 14      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ collectFees                    ┆ 6720            ┆ 27696   ┆ 20481   ┆ 44969    ┆ 7       │
+│ collectFees                    ┆ 6659            ┆ 27637   ┆ 20420   ┆ 44908    ┆ 7       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ collectProtocolFees            ┆ 5620            ┆ 8817    ┆ 9349    ┆ 10952    ┆ 4       │
+│ collectProtocolFees            ┆ 5517            ┆ 8708    ┆ 9247    ┆ 10824    ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -88,3 +88,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ flashLoan                      ┆ 24674           ┆ 55799   ┆ 24674   ┆ 118050   ┆ 3       │
+│ flashLoan                      ┆ 24546           ┆ 55415   ┆ 24546   ┆ 117154   ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -108,3 +108,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ mint                           ┆ 6892            ┆ 1402686 ┆ 1389693 ┆ 14398451 ┆ 216     │
+│ mint                           ┆ 6889            ┆ 1401974 ┆ 1389110 ┆ 14394628 ┆ 216     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -112,5 +112,5 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ pendingFees                    ┆ 4041            ┆ 12049   ┆ 12853   ┆ 22185    ┆ 8       │
+│ pendingFees                    ┆ 3980            ┆ 11987   ┆ 12792   ┆ 22076    ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ safeBatchTransferFrom          ┆ 3251            ┆ 408394  ┆ 319740  ┆ 1394600  ┆ 25      │
+│ safeBatchTransferFrom          ┆ 2997            ┆ 408204  ┆ 319642  ┆ 1394156  ┆ 25      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -122,3 +122,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swap                           ┆ 22475           ┆ 50419   ┆ 30217   ┆ 341090   ┆ 318     │
+│ swap                           ┆ 22347           ┆ 50291   ┆ 30089   ┆ 340962   ┆ 318     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -141,3 +141,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1899867                            ┆ 9873            ┆       ┆        ┆       ┆         │
+│ 1862779                            ┆ 9669            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -149,5 +149,5 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ findBestPathFromAmountIn           ┆ 1033            ┆ 45266 ┆ 45438  ┆ 88927 ┆ 5       │
+│ findBestPathFromAmountIn           ┆ 1021            ┆ 44110 ┆ 44256  ┆ 86575 ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ findBestPathFromAmountOut          ┆ 1011            ┆ 38128 ┆ 40133  ┆ 68750 ┆ 5       │
+│ findBestPathFromAmountOut          ┆ 999             ┆ 37100 ┆ 39335  ┆ 66782 ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -160,3 +160,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 4591267                                               ┆ 23440           ┆         ┆         ┆         ┆         │
+│ 4464246                                               ┆ 22787           ┆         ┆         ┆         ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -164,7 +164,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addLiquidity                                          ┆ 19672           ┆ 1271036 ┆ 1387744 ┆ 3343198 ┆ 132     │
+│ addLiquidity                                          ┆ 19104           ┆ 1269581 ┆ 1386214 ┆ 3340492 ┆ 132     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ addLiquidityAVAX                                      ┆ 20036           ┆ 1466238 ┆ 1443921 ┆ 3391536 ┆ 41      │
+│ addLiquidityAVAX                                      ┆ 19453           ┆ 1464700 ┆ 1442391 ┆ 3388830 ┆ 41      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ createLBPair                                          ┆ 279323          ┆ 311452  ┆ 317427  ┆ 331632  ┆ 4       │
+│ createLBPair                                          ┆ 279195          ┆ 311324  ┆ 317299  ┆ 331504  ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -174,9 +174,9 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getIdFromPrice                                        ┆ 19069           ┆ 28334   ┆ 32951   ┆ 32983   ┆ 3       │
+│ getIdFromPrice                                        ┆ 18941           ┆ 28206   ┆ 32823   ┆ 32855   ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getPriceFromId                                        ┆ 4771            ┆ 5491    ┆ 5834    ┆ 5868    ┆ 3       │
+│ getPriceFromId                                        ┆ 4643            ┆ 5363    ┆ 5706    ┆ 5740    ┆ 3       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getSwapIn                                             ┆ 1980            ┆ 18959   ┆ 11151   ┆ 61718   ┆ 30      │
+│ getSwapIn                                             ┆ 1852            ┆ 18336   ┆ 10767   ┆ 59542   ┆ 30      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ getSwapOut                                            ┆ 12368           ┆ 19836   ┆ 21752   ┆ 28264   ┆ 20      │
+│ getSwapOut                                            ┆ 11984           ┆ 19375   ┆ 21112   ┆ 27368   ┆ 20      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -184,27 +184,27 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeLiquidity                                       ┆ 2118            ┆ 579712  ┆ 408504  ┆ 1623758 ┆ 10      │
+│ removeLiquidity                                       ┆ 2068            ┆ 578983  ┆ 407904  ┆ 1622121 ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ removeLiquidityAVAX                                   ┆ 1941            ┆ 976395  ┆ 1115696 ┆ 1672247 ┆ 4       │
+│ removeLiquidityAVAX                                   ┆ 1891            ┆ 975385  ┆ 1114521 ┆ 1670610 ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapAVAXForExactTokens                                ┆ 1534            ┆ 28653   ┆ 8550    ┆ 166467  ┆ 8       │
+│ swapAVAXForExactTokens                                ┆ 1520            ┆ 28333   ┆ 8348    ┆ 165405  ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactAVAXForTokens                                ┆ 1578            ┆ 51884   ┆ 15354   ┆ 153614  ┆ 8       │
+│ swapExactAVAXForTokens                                ┆ 1564            ┆ 51591   ┆ 15036   ┆ 153064  ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactAVAXForTokensSupportingFeeOnTransferTokens   ┆ 1556            ┆ 51177   ┆ 14867   ┆ 163525  ┆ 9       │
+│ swapExactAVAXForTokensSupportingFeeOnTransferTokens   ┆ 1542            ┆ 50810   ┆ 14549   ┆ 162719  ┆ 9       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForAVAX                                ┆ 1630            ┆ 69984   ┆ 15522   ┆ 286316  ┆ 10      │
+│ swapExactTokensForAVAX                                ┆ 1616            ┆ 69659   ┆ 15204   ┆ 285230  ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForAVAXSupportingFeeOnTransferTokens   ┆ 1610            ┆ 56192   ┆ 15502   ┆ 167467  ┆ 10      │
+│ swapExactTokensForAVAXSupportingFeeOnTransferTokens   ┆ 1596            ┆ 55786   ┆ 15184   ┆ 166822  ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForTokens                              ┆ 1631            ┆ 105125  ┆ 67133   ┆ 538137  ┆ 13      │
+│ swapExactTokensForTokens                              ┆ 1617            ┆ 104587  ┆ 66693   ┆ 535979  ┆ 13      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapExactTokensForTokensSupportingFeeOnTransferTokens ┆ 1610            ┆ 55076   ┆ 45689   ┆ 136771  ┆ 10      │
+│ swapExactTokensForTokensSupportingFeeOnTransferTokens ┆ 1596            ┆ 54621   ┆ 45207   ┆ 135965  ┆ 10      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapTokensForExactAVAX                                ┆ 1588            ┆ 29596   ┆ 8610    ┆ 173651  ┆ 8       │
+│ swapTokensForExactAVAX                                ┆ 1574            ┆ 29302   ┆ 8408    ┆ 172801  ┆ 8       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ swapTokensForExactTokens                              ┆ 1632            ┆ 127782  ┆ 24140   ┆ 609504  ┆ 11      │
+│ swapTokensForExactTokens                              ┆ 1618            ┆ 126636  ┆ 23462   ┆ 605298  ┆ 11      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ sweep                                                 ┆ 6028            ┆ 26141   ┆ 26740   ┆ 36036   ┆ 5       │
+│ sweep                                                 ┆ 5900            ┆ 26003   ┆ 26535   ┆ 35908   ┆ 5       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ sweepLBToken                                          ┆ 3264            ┆ 156904  ┆ 156904  ┆ 310544  ┆ 2       │
+│ sweepLBToken                                          ┆ 3014            ┆ 156630  ┆ 156630  ┆ 310247  ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -217,3 +217,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 19179229                                                      ┆ 95589           ┆     ┆        ┆     ┆         │
+│ 18854334                                                      ┆ 93969           ┆     ┆        ┆     ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -228,3 +228,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 17699454                                                      ┆ 88210           ┆     ┆        ┆     ┆         │
+│ 17397071                                                      ┆ 86702           ┆     ┆        ┆     ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -249,3 +249,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ transfer                                                    ┆ 2581            ┆ 10121 ┆ 3226   ┆ 29926 ┆ 527     │
+│ transfer                                                    ┆ 2581            ┆ 10093 ┆ 3226   ┆ 29926 ┆ 526     │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -258,3 +258,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 693730                                                                    ┆ 4198            ┆       ┆        ┆       ┆         │
+│ 693718                                                                    ┆ 4161            ┆       ┆        ┆       ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -292,3 +292,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 1098332                               ┆ 5556            ┆       ┆        ┆        ┆         │
+│ 1095325                               ┆ 5541            ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -306,5 +306,5 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ request()                             ┆ 340             ┆ 73455 ┆ 101517 ┆ 137977 ┆ 12      │
+│ request()                             ┆ 340             ┆ 73284 ┆ 101261 ┆ 137721 ┆ 12      │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ request(address)                      ┆ 622             ┆ 64004 ┆ 58354  ┆ 138687 ┆ 4       │
+│ request(address)                      ┆ 622             ┆ 63876 ┆ 58226  ┆ 138431 ┆ 4       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -327,3 +327,3 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ 413779                                                  ┆ 2134            ┆       ┆        ┆        ┆         │
+│ 401767                                                  ┆ 2074            ┆       ┆        ┆        ┆         │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
@@ -331,7 +331,7 @@
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ LBFlashLoanCallback                                     ┆ 12455           ┆ 12455 ┆ 12455  ┆ 12455  ┆ 1       │
+│ LBFlashLoanCallback                                     ┆ 11943           ┆ 11943 ┆ 11943  ┆ 11943  ┆ 1       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ flashBorrow                                             ┆ 33471           ┆ 77648 ┆ 77648  ┆ 121826 ┆ 2       │
+│ flashBorrow                                             ┆ 33343           ┆ 77136 ┆ 77136  ┆ 120930 ┆ 2       │
 ├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
-│ flashBorrowWithReentrancy                               ┆ 33427           ┆ 33427 ┆ 33427  ┆ 33427  ┆ 1       │
+│ flashBorrowWithReentrancy                               ┆ 33299           ┆ 33299 ┆ 33299  ┆ 33299  ┆ 1       │
 ╰─────────────────────────────────────────────────────────┴─────────────────┴───────┴────────┴────────┴─────────╯
```

```
testInversePriceForOppositeBins() (gas: 0 (0.000%)) 
testRevertOnNonEOA() (gas: 0 (0.000%)) 
testForInvalidReductionFactor() (gas: 0 (0.000%)) 
testSetFeeRecipientNotByOwnerReverts() (gas: 0 (0.000%)) 
testsetFactoryLockedState() (gas: 0 (0.000%)) 
testSelfApprovalReverts() (gas: 0 (0.000%)) 
testInternalApproval() (gas: 0 (0.000%)) 
testInternalBurn(uint256,uint256) (gas: 0 (0.000%)) 
testInternalBurnFrom0AddressReverts() (gas: 0 (0.000%)) 
testInternalExcessiveBurnAmountReverts(uint128,uint128) (gas: 0 (0.000%)) 
testInternalMint(uint256) (gas: 0 (0.000%)) 
testInternalMintTo0AddressReverts() (gas: 0 (0.000%)) 
testAddToken() (gas: -19 (-0.002%)) 
testForInvalidFeeRecipient() (gas: -7 (-0.010%)) 
testIncreaseOracleLength() (gas: -256 (-0.012%)) 
testSetFeesParametersOnPairReverts() (gas: -128 (-0.036%)) 
testForDoubleIgnored() (gas: -128 (-0.036%)) 
testForInvalidBaseFactor() (gas: -128 (-0.036%)) 
testForInvalidFilterPeriod() (gas: -128 (-0.036%)) 
testCreatePairWhenFactoryIsUnlocked() (gas: -262 (-0.070%)) 
testSetFeesParametersOnPair() (gas: -256 (-0.071%)) 
testTLowerThanTimestamp() (gas: -5524 (-0.072%)) 
testIfPairAlreadyExistsReverts() (gas: -256 (-0.073%)) 
testGetSwapOutWithMultipleChoices() (gas: -3684 (-0.090%)) 
testForceDecay() (gas: -8084 (-0.101%)) 
testFuzzingAddLiquidity(uint256) (gas: -1066 (-0.103%)) 
testForInvalidProtocolShare() (gas: -384 (-0.109%)) 
testSwapXtoYDistantBinsFromGetSwapOut() (gas: -1970 (-0.112%)) 
testFlashloan() (gas: -1953 (-0.113%)) 
testInsufficientLiquidityMinted() (gas: -1876 (-0.114%)) 
testSwapXtoYConsecutiveBinFromGetSwapOut() (gas: -1970 (-0.115%)) 
testSwapYtoXConsecutiveBinFromGetSwapOut() (gas: -1970 (-0.116%)) 
testBurnLiquidity() (gas: -2272 (-0.120%)) 
testSwapXtoYDistantBinsFromGetSwapIn() (gas: -2098 (-0.120%)) 
testSwapXtoYConsecutiveBinFromGetSwapIn() (gas: -2098 (-0.123%)) 
testSwapYtoXConsecutiveBinFromGetSwapIn() (gas: -2098 (-0.123%)) 
testSwapYtoXDistantBinsFromGetSwapOut() (gas: -2226 (-0.125%)) 
testOracleSampleFromWith100SamplesNotAllInitialized() (gas: -24340 (-0.125%)) 
testSwapYtoXDistantBinsFromGetSwapIn() (gas: -2354 (-0.132%)) 
testTaxTokenSwappedOnV1Pairs() (gas: -6516 (-0.134%)) 
testFailFlashloanMoreThanReserves() (gas: -128 (-0.141%)) 
testFailFlashlaonWithReentrancy() (gas: -128 (-0.142%)) 
testFlawedCompositionFactor() (gas: -2724 (-0.162%)) 
testLockRequest() (gas: -256 (-0.162%)) 
testSafeBatchTransferFrom() (gas: -2642 (-0.167%)) 
testSafeTransferNotApprovedReverts() (gas: -1906 (-0.182%)) 
testEnsureModifierLiquidity() (gas: -1549 (-0.183%)) 
testAddLiquidityTaxToken() (gas: -6640 (-0.185%)) 
testForAmountSlippageCaughtReverts() (gas: -4296 (-0.193%)) 
testSafeBatchTransferNotApprovedReverts() (gas: -2028 (-0.193%)) 
testGetIdFromPrice() (gas: -896 (-0.195%)) 
testSafeTransferFromReverts() (gas: -3730 (-0.196%)) 
testOracleSampleFromEdgeCases() (gas: -948 (-0.198%)) 
testDistributionOverflowReverts() (gas: -1768 (-0.204%)) 
testGetSwapInMoreBins() (gas: -4018 (-0.204%)) 
testGetSwapInWithMultipleChoices() (gas: -3684 (-0.205%)) 
testForIdSlippageCaughtReverts() (gas: -4570 (-0.205%)) 
testOracleSampleFromWith2Samples() (gas: -1076 (-0.207%)) 
testSwapWithDifferentBinSteps() (gas: -4380 (-0.209%)) 
testGetSwapInOverflowReverts() (gas: -1586 (-0.213%)) 
testRemoveLiquiditySlippageReverts() (gas: -13559 (-0.215%)) 
testModifierCheckLength() (gas: -4108 (-0.216%)) 
testGetSwapOutOnV2Pair() (gas: -3556 (-0.224%)) 
testGetSwapInOnV2Pair() (gas: -3556 (-0.224%)) 
testClaimFeesY() (gas: -2792 (-0.228%)) 
testClaimFeesX() (gas: -2792 (-0.228%)) 
testGetPriceFromId() (gas: -896 (-0.230%)) 
testAddLiquidityIgnored() (gas: -4256 (-0.231%)) 
testSetLBPairImplementation() (gas: -39185 (-0.234%)) 
testFeesOnTokenTransfer() (gas: -3298 (-0.236%)) 
testBalanceOfBatch() (gas: -2522 (-0.237%)) 
testAddLiquidityAVAXReversed() (gas: -11019 (-0.241%)) 
testAddLiquidityAVAX() (gas: -11922 (-0.241%)) 
testQuoteAssets() (gas: -1792 (-0.242%)) 
testConstructor(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24) (gas: -12363 (-0.243%)) 
testGetSwapInOnComplexRoute() (gas: -4542 (-0.244%)) 
testSweepLBToken() (gas: -4475 (-0.248%)) 
testSafeBatchTransferFromReverts() (gas: -5060 (-0.249%)) 
testCreateLBPair() (gas: -896 (-0.251%)) 
testCreateLBPair() (gas: -1024 (-0.252%)) 
testSafeTransferFrom() (gas: -3058 (-0.261%)) 
testGetSwapOutOnComplexRoute() (gas: -4926 (-0.262%)) 
testAddLiquidityNoSlippage() (gas: -5707 (-0.273%)) 
testRemoveLiquidityReverseOrder() (gas: -5708 (-0.273%)) 
testOracleSampleFromWith100Samples() (gas: -69140 (-0.276%)) 
testSetRequestAmount() (gas: -512 (-0.280%)) 
testWithdrawAvax() (gas: -512 (-0.294%)) 
testClaimProtocolFees() (gas: -3828 (-0.307%)) 
testClaimFeesComplex(uint256,uint256) (gas: -4212 (-0.322%)) 
testGetSwapInWrongAmountsReverts() (gas: -6608 (-0.330%)) 
testRequestFaucetTokensByOperator() (gas: -1024 (-0.332%)) 
testSwapYtoXSingleBinFromGetSwapOut() (gas: -1714 (-0.344%)) 
testSwapXtoYSingleBinFromGetSwapOut() (gas: -1714 (-0.344%)) 
testSwapXtoYSingleBinFromGetSwapIn() (gas: -1714 (-0.345%)) 
testForLengthsMismatchReverts() (gas: -762 (-0.367%)) 
testFeeOnActiveBinReverse() (gas: -3274 (-0.372%)) 
testFeeOnActiveBin() (gas: -3274 (-0.372%)) 
testRequestFaucetTokens() (gas: -1024 (-0.381%)) 
testSwapYtoXSingleBinFromGetSwapIn() (gas: -1970 (-0.386%)) 
testGetSwapInOnV1Pair() (gas: -1168 (-0.405%)) 
testSwapExactTokensForAvaxSinglePair() (gas: -1597 (-0.415%)) 
testSwapExactTokensForTokensMultiplePairs() (gas: -2542 (-0.424%)) 
testForZeroAddressPairReverts() (gas: -256 (-0.434%)) 
testSweep() (gas: -512 (-0.456%)) 
testInvalidBinStepWhileCreatingLBPair() (gas: -128 (-0.463%)) 
testAddLiquidityAVAXnotAVAXReverts() (gas: -965 (-0.465%)) 
testGetSwapOutOnV1Pair() (gas: -1552 (-0.505%)) 
testWithdrawToken() (gas: -1024 (-0.524%)) 
testTaxTokenEqualOnlyV2Swap() (gas: -3196 (-0.525%)) 
testSwapExactTokensForAVAXSupportingFeeOnTransferTokens() (gas: -2007 (-0.534%)) 
testSetFeeRecipient() (gas: -128 (-0.539%)) 
testSwapExactAVAXForTokensSinglePair() (gas: -1996 (-0.544%)) 
testForIdDesiredOverflowReverts() (gas: -1906 (-0.556%)) 
testSweepMax() (gas: -640 (-0.565%)) 
testAvailableBinSteps() (gas: -730 (-0.585%)) 
testForIdenticalAddressesReverts() (gas: -128 (-0.622%)) 
testAddRemovePresets() (gas: -504 (-0.627%)) 
testSwapExactAVAXForTokensSupportingFeeOnTransferTokens() (gas: -2508 (-0.649%)) 
testForSettingFlashloanFee() (gas: -128 (-0.654%)) 
testSwapExactTokensForTokensSinglePair() (gas: -2124 (-0.672%)) 
testFactoryLockedReverts() (gas: -128 (-0.684%)) 
testWrongTokenOrderReverts() (gas: -1674 (-0.723%)) 
testInsufficientLiquidityBurnedReverts() (gas: -178 (-0.741%)) 
testSwapExactTokensForTokensMultiplePairsWithV1() (gas: -2812 (-0.749%)) 
testSwapTokensForExactTokensMultiplePairsWithV1() (gas: -4418 (-0.763%)) 
testModifieronlyFactoryOwner() (gas: -128 (-0.770%)) 
testFeesAboveMaxVolatilityReverts(uint8) (gas: -128 (-0.833%)) 
testFeesAboveMaxBaseFactorReverts(uint8) (gas: -128 (-0.837%)) 
testSwapInsufficientAmountReverts() (gas: -512 (-0.859%)) 
testSwapExactTokensForTokensSupportingFeeOnTransferTokens() (gas: -4423 (-0.862%)) 
testRemoveToken() (gas: -384 (-0.873%)) 
testPendingFeesNotIncreasingReverts() (gas: -153 (-0.877%)) 
testSetRequestCooldown() (gas: -1792 (-0.890%)) 
testSwapTokensForExactTokensMultiplePairs() (gas: -7446 (-0.926%)) 
testSwapTokensForExactAVAXSinglePair() (gas: -2658 (-0.945%)) 
testSwapAVAXForExactTokensSinglePair() (gas: -2658 (-1.066%)) 
testSetPresets() (gas: -384 (-1.104%)) 
testVerifyOracleInitialParams() (gas: -128 (-1.124%)) 
testCollectingFeesOnlyFeeRecipient() (gas: -256 (-1.127%)) 
testMintWrongLengthsReverts() (gas: -516 (-1.156%)) 
testSwapTokensForExactTokensSinglePair() (gas: -2786 (-1.174%)) 
testInvalidLength() (gas: -292 (-1.644%)) 
testSwappingOnNotExistingV1PairReverts() (gas: -4011 (-1.933%)) 
testSwappingOnNotExistingV2PairReverts() (gas: -4011 (-2.034%)) 
testPrivateViewFunctions() (gas: -256 (-2.037%)) 
testgetAllLBPairs() (gas: -128415 (-2.258%)) <------------------------------------
testConstructor() (gas: -256 (-2.340%)) 
testInvalidTokenPathReverts() (gas: -828 (-2.380%)) 
testForInvalidBinStepUnderflowReverts() (gas: -384 (-2.531%)) 
testForInvalidBinStepOverflowReverts() (gas: -384 (-2.539%)) 
testModifierEnsure() (gas: -1955 (-3.207%)) 
testConstructor() (gas: -384 (-3.242%)) 
testConstructor() (gas: -384 (-3.858%)) 
testModifierVerifyInputs() (gas: -3198 (-4.074%)) 
Overall gas change: -581485 (-81.334%) <------------------------------------
```


### [G&#x2011;05]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There is 1 instance of this issue:*
```solidity
File: src/LBPair.sol

/// @audit _bins[_id] on line 452
453:          _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L453

### [G&#x2011;06]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 5 instances of this issue:*
```solidity
File: src/LBPair.sol

/// @audit if-condition on line 219
225:                  uint256 _deltaT = _lookUpTimestamp - timestamp;

/// @audit if-condition on line 534
536:                                  _fp.getFeeAmountForC(_mintInfo.amountX - _receivedX)

/// @audit if-condition on line 545
547:                                  _fp.getFeeAmountForC(_mintInfo.amountY - _receivedY)

/// @audit if-condition on line 597
598:                  tokenX.safeTransfer(_to, _mintInfo.amountXIn - _amountAddedPlusFee);

/// @audit if-condition on line 602
603:                  tokenY.safeTransfer(_to, _mintInfo.amountYIn - _amountAddedPlusFee);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L225

### [G&#x2011;07]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 5 instances of this issue:*
```solidity
File: src/LBQuoter.sol

75:           for (uint256 i; i < swapLength; i++) {

100:                  for (uint256 j; j < LBPairsAvailable.length; j++) {

154:          for (uint256 i = swapLength; i > 0; i--) {

177:                  for (uint256 j; j < LBPairsAvailable.length; j++) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75

```solidity
File: src/LBRouter.sol

711:          for (uint256 i = _pairs.length; i != 0; i--) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711

### [G&#x2011;08]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 11 instances of this issue:*
```solidity
File: src/interfaces/IJoeFactory.sol

/// @audit feeTo(), feeToSetter(), migrator(), getPair(), allPairs(), allPairsLength(), createPair(), setFeeTo(), setFeeToSetter(), setMigrator()
7:    interface IJoeFactory {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeFactory.sol#L7

```solidity
File: src/interfaces/IJoePair.sol

/// @audit MINIMUM_LIQUIDITY(), factory(), token0(), token1(), getReserves(), price0CumulativeLast(), price1CumulativeLast(), kLast(), mint(), burn(), swap(), skim(), sync(), initialize()
7:    interface IJoePair {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L7

```solidity
File: src/interfaces/IJoeRouter01.sol

/// @audit factory(), WAVAX(), addLiquidity(), addLiquidityAVAX(), removeLiquidity(), removeLiquidityAVAX(), removeLiquidityWithPermit(), removeLiquidityAVAXWithPermit(), swapExactTokensForTokens(), swapTokensForExactTokens(), swapExactAVAXForTokens(), swapTokensForExactAVAX(), swapExactTokensForAVAX(), swapAVAXForExactTokens(), quote(), getAmountOut(), getAmountIn(), getAmountsOut(), getAmountsIn()
7:    interface IJoeRouter01 {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L7

```solidity
File: src/interfaces/IJoeRouter02.sol

/// @audit removeLiquidityAVAXSupportingFeeOnTransferTokens(), removeLiquidityAVAXWithPermitSupportingFeeOnTransferTokens(), swapExactTokensForTokensSupportingFeeOnTransferTokens(), swapExactAVAXForTokensSupportingFeeOnTransferTokens(), swapExactTokensForAVAXSupportingFeeOnTransferTokens()
9:    interface IJoeRouter02 is IJoeRouter01 {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter02.sol#L9

```solidity
File: src/interfaces/ILBFactory.sol

/// @audit MAX_FEE(), MIN_BIN_STEP(), MAX_BIN_STEP(), MAX_PROTOCOL_SHARE(), LBPairImplementation(), getNumberOfQuoteAssets(), getQuoteAsset(), isQuoteAsset(), feeRecipient(), flashLoanFee(), creationUnlocked(), allLBPairs(), getNumberOfLBPairs(), getLBPairInformation(), getPreset(), getAllBinSteps(), getAllLBPairs(), setLBPairImplementation(), createLBPair(), setLBPairIgnored(), setPreset(), removePreset(), setFeesParametersOnPair(), setFeeRecipient(), setFlashLoanFee(), setFactoryLockedState(), addQuoteAsset(), removeQuoteAsset(), forceDecay()
13:   interface ILBFactory is IPendingOwnable {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L13

```solidity
File: src/interfaces/ILBFlashLoanCallback.sol

/// @audit LBFlashLoanCallback()
8:    interface ILBFlashLoanCallback {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFlashLoanCallback.sol#L8

```solidity
File: src/interfaces/ILBPair.sol

/// @audit tokenX(), tokenY(), factory(), getReservesAndId(), getGlobalFees(), getOracleParameters(), getOracleSampleFrom(), feeParameters(), findFirstNonEmptyBinId(), getBin(), pendingFees(), swap(), flashLoan(), mint(), burn(), increaseOracleLength(), collectFees(), collectProtocolFees(), setFeesParameters(), forceDecay(), initialize()
13:   interface ILBPair {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L13

```solidity
File: src/interfaces/ILBRouter.sol

/// @audit factory(), oldFactory(), wavax(), getIdFromPrice(), getPriceFromId(), getSwapIn(), getSwapOut(), createLBPair(), addLiquidity(), addLiquidityAVAX(), removeLiquidity(), removeLiquidityAVAX(), swapExactTokensForTokens(), swapExactTokensForAVAX(), swapExactAVAXForTokens(), swapTokensForExactTokens(), swapTokensForExactAVAX(), swapAVAXForExactTokens(), swapExactTokensForTokensSupportingFeeOnTransferTokens(), swapExactTokensForAVAXSupportingFeeOnTransferTokens(), swapExactAVAXForTokensSupportingFeeOnTransferTokens(), sweep(), sweepLBToken()
13:   interface ILBRouter {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L13

```solidity
File: src/interfaces/ILBToken.sol

/// @audit userPositionAtIndex(), userPositionNumber(), totalSupply(), safeTransferFrom(), safeBatchTransferFrom()
8:    interface ILBToken {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBToken.sol#L8

```solidity
File: src/interfaces/IPendingOwnable.sol

/// @audit pendingOwner(), setPendingOwner(), revokePendingOwner(), becomeOwner()
8:    interface IPendingOwnable {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IPendingOwnable.sol#L8

```solidity
File: src/LBQuoter.sol

/// @audit findBestPathFromAmountIn(), findBestPathFromAmountOut()
17:   contract LBQuoter {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L17

### [G&#x2011;09]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 37 instances of this issue:*
```solidity
File: src/interfaces/IJoeFactory.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeFactory.sol#L3

```solidity
File: src/interfaces/IJoePair.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L3

```solidity
File: src/interfaces/IJoeRouter01.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter01.sol#L3

```solidity
File: src/interfaces/IJoeRouter02.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoeRouter02.sol#L3

```solidity
File: src/interfaces/ILBFactory.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L3

```solidity
File: src/interfaces/ILBFlashLoanCallback.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFlashLoanCallback.sol#L3

```solidity
File: src/interfaces/ILBPair.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L3

```solidity
File: src/interfaces/ILBRouter.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L3

```solidity
File: src/interfaces/ILBToken.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBToken.sol#L3

```solidity
File: src/interfaces/IPendingOwnable.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IPendingOwnable.sol#L3

```solidity
File: src/interfaces/IWAVAX.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IWAVAX.sol#L3

```solidity
File: src/LBErrors.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBErrors.sol#L3

```solidity
File: src/LBFactory.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L3

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
File: src/libraries/BitMath.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L3

```solidity
File: src/libraries/Buffer.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Buffer.sol#L3

```solidity
File: src/libraries/Constants.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Constants.sol#L3

```solidity
File: src/libraries/Decoder.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Decoder.sol#L3

```solidity
File: src/libraries/Encoder.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Encoder.sol#L3

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
File: src/libraries/JoeLibrary.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L3

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
File: src/libraries/PendingOwnable.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L3

```solidity
File: src/libraries/ReentrancyGuardUpgradeable.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/ReentrancyGuardUpgradeable.sol#L3

```solidity
File: src/libraries/SafeCast.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SafeCast.sol#L3

```solidity
File: src/libraries/SafeMath.sol

3:    pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SafeMath.sol#L3

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

### [G&#x2011;10]  `>=` costs less gas than `>`
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, [which saves **3 gas**](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

*There is 1 instance of this issue:*
```solidity
File: src/LBRouter.sol

116:                  _amountOutOfBin = _amountOut > _reserve ? _reserve : _amountOut;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L116

### [G&#x2011;11]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 20 instances of this issue:*
```solidity
File: src/LBFactory.sol

215:      function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {

312       function setLBPairIgnored(
313           IERC20 _tokenX,
314           IERC20 _tokenY,
315           uint256 _binStep,
316           bool _ignored
317:      ) external override onlyOwner {

340       function setPreset(
341           uint16 _binStep,
342           uint16 _baseFactor,
343           uint16 _filterPeriod,
344           uint16 _decayPeriod,
345           uint16 _reductionFactor,
346           uint24 _variableFeeControl,
347           uint16 _protocolShare,
348           uint24 _maxVolatilityAccumulated,
349           uint16 _sampleLifetime
350:      ) external override onlyOwner {

396:      function removePreset(uint16 _binStep) external override onlyOwner {

423       function setFeesParametersOnPair(
424           IERC20 _tokenX,
425           IERC20 _tokenY,
426           uint16 _binStep,
427           uint16 _baseFactor,
428           uint16 _filterPeriod,
429           uint16 _decayPeriod,
430           uint16 _reductionFactor,
431           uint24 _variableFeeControl,
432           uint16 _protocolShare,
433           uint24 _maxVolatilityAccumulated
434:      ) external override onlyOwner {

468:      function setFeeRecipient(address _feeRecipient) external override onlyOwner {

474:      function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

485:      function setFactoryLockedState(bool _locked) external override onlyOwner {

493:      function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

502:      function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

520:      function forceDecay(ILBPair _LBPair) external override onlyOwner {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L215

```solidity
File: src/LBPair.sol

104       function initialize(
105           IERC20 _tokenX,
106           IERC20 _tokenY,
107           uint24 _activeId,
108           uint16 _sampleLifetime,
109           bytes32 _packedFeeParameters
110:      ) external override onlyFactory {

788:      function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {

792:      function forceDecay() external override onlyFactory {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L104-L110

```solidity
File: src/LBRouter.sol

622       function sweep(
623           IERC20 _token,
624           address _to,
625           uint256 _amount
626:      ) external override onlyFactoryOwner {

642       function sweepLBToken(
643           ILBToken _lbToken,
644           address _to,
645           uint256[] memory _ids,
646           uint256[] memory _amounts
647:      ) external override onlyFactoryOwner {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L622-L626

```solidity
File: src/libraries/PendingOwnable.sol

59:       function setPendingOwner(address pendingOwner_) public override onlyOwner {

68:       function revokePendingOwner() public override onlyOwner {

75:       function becomeOwner() public override onlyPendingOwner {

84:       function renounceOwnership() public override onlyOwner {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L59


___


## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 13 instances of this issue:*
```solidity
File: src/LBPair.sol

/// @audit (valid but excluded finding)
274:              for (uint256 i; i < _ids.length; ++i) {

/// @audit (valid but excluded finding)
496:              for (uint256 i; i < _ids.length; ++i) {

/// @audit (valid but excluded finding)
623:              for (uint256 i; i < _ids.length; ++i) {

/// @audit (valid but excluded finding)
701:              for (uint256 i; i < _ids.length; ++i) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274

```solidity
File: src/LBQuoter.sol

/// @audit (valid but excluded finding)
100:                  for (uint256 j; j < LBPairsAvailable.length; j++) {

/// @audit (valid but excluded finding)
177:                  for (uint256 j; j < LBPairsAvailable.length; j++) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100

```solidity
File: src/LBRouter.sol

/// @audit (valid but excluded finding)
674:              for (uint256 i; i < depositIds.length; ++i) {

/// @audit (valid but excluded finding)
778:              for (uint256 i; i < _pairs.length; ++i) {

/// @audit (valid but excluded finding)
831:              for (uint256 i; i < _pairs.length; ++i) {

/// @audit (valid but excluded finding)
878:              for (uint256 i; i < _pairs.length; ++i) {

/// @audit (valid but excluded finding)
951:              for (uint256 i; i < pairs.length; ++i) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L674

```solidity
File: src/LBToken.sol

/// @audit (valid but excluded finding)
90:               for (uint256 i; i < _accounts.length; ++i) {

/// @audit (valid but excluded finding)
163:              for (uint256 i; i < _ids.length; ++i) {

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90

### [G&#x2011;02]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There is 1 instance of this issue:*
```solidity
File: src/libraries/Oracle.sol

/// @audit (valid but excluded finding)
162:                  _middle = (_low + _high) / 2;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L162

