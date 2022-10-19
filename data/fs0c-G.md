## Gas optimizations

|  | Issue | Instances |
| --- | --- | --- |
| [G‑01] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate | 2 |
| [G‑02] | Using calldata instead of memory for read-only arguments in external functions saves gas | 33 |
| [G‑03] | Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement | 2 |
| [G‑04] | ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too) | 3 |
| [G‑05] | Using private rather than public for constants, saves gas | 4 |

### **[G‑01] Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate**

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There are 2 instances of this issue:*

```solidity
File: src/LBPair.sol

67:  /// @dev Mapping from account to user's unclaimed fees. The first 128 bits are tokenX and the last are for tokenY
68:    mapping(address => bytes32) private _unclaimedFees;
69:    /// @dev Mapping from account to id to user's accruedDebt.
70:    mapping(address => mapping(uint256 => Debts)) private _accruedDebts;
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L67](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L67)

```solidity
File: src/LBToken.sol

20:    /// @dev Mapping from account to spender approvals
21:    mapping(address => mapping(address => bool)) private _spenderApprovals;
26:    /// @dev  Mapping from account to set of ids, where user currently have a non-zero balance
27:    mapping(address => EnumerableSet.UintSet) private _userIds;
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L20](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L20)

### **[G‑02] Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas**

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 33 instances of this issue:*

```solidity
File: src/LBPair.sol

261:    function pendingFees(address _account, uint256[] memory _ids)
262:        external
263:        view
264:        override
265:        returns (uint256 amountX, uint256 amountY)

466:    function mint(
467:        uint256[] memory _ids,
468:        uint256[] memory _distributionX,
469:        uint256[] memory _distributionY,
470:        address _to
471:    )
472:        external

616:    function burn(
617:        uint256[] memory _ids,
618:        uint256[] memory _amounts,
619:        address _to
620:    ) external override nonReentrant returns (uint256 amountX, uint256 amountY) {

688:    function collectFees(address _account, uint256[] memory _ids)
689:        external
690:        override
691:        nonReentrant
692:        returns (uint256 amountX, uint256 amountY)
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol)

```solidity
File: src/LBQuoter.sol

54:    function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
55:        public
56:        view

134:    function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
135:        public
136:        view
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol)

```solidity
File: src/LBRouter.sol

207:    function addLiquidity(LiquidityParameters memory _liquidityParameters)
208:        external
209:        override
210:        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
211:    {

230:    function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)
231:        external
232:        payable
233:        override
234:        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)

274:    function removeLiquidity(
275:        IERC20 _tokenX,
276:        IERC20 _tokenY,
277:        uint16 _binStep,
278:        uint256 _amountXMin,
279:        uint256 _amountYMin,
280:        uint256[] memory _ids,
281:        uint256[] memory _amounts,
282:        address _to,
283:        uint256 _deadline
284:    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {

308:    function removeLiquidityAVAX(
309:        IERC20 _token,
310:        uint16 _binStep,
311:        uint256 _amountTokenMin,
312:        uint256 _amountAVAXMin,
313:        uint256[] memory _ids,
314:        uint256[] memory _amounts,
315:        address payable _to,
316:        uint256 _deadline
317:    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {

352:    function swapExactTokensForTokens(
353:        uint256 _amountIn,
354:        uint256 _amountOutMin,
355:        uint256[] memory _pairBinSteps,
356:        IERC20[] memory _tokenPath,
357:        address _to,
358:        uint256 _deadline
359:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {

377:    function swapExactTokensForAVAX(
378:        uint256 _amountIn,
379:        uint256 _amountOutMinAVAX,
380:        uint256[] memory _pairBinSteps,
381:        IERC20[] memory _tokenPath,
382:        address payable _to,
383:        uint256 _deadline
384:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {

531:    function swapExactTokensForTokensSupportingFeeOnTransferTokens(
532:        uint256 _amountIn,
533:        uint256 _amountOutMin,
534:        uint256[] memory _pairBinSteps,
535:        IERC20[] memory _tokenPath,
536:        address _to,
537:        uint256 _deadline
538:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {

561:    function swapExactTokensForAVAXSupportingFeeOnTransferTokens(
562:        uint256 _amountIn,
563:        uint256 _amountOutMinAVAX,
564:        uint256[] memory _pairBinSteps,
565:        IERC20[] memory _tokenPath,
566:        address payable _to,
567:        uint256 _deadline
568:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {

407:    function swapExactAVAXForTokens(
408:        uint256 _amountOutMin,
409:        uint256[] memory _pairBinSteps,
410:        IERC20[] memory _tokenPath,
411:        address _to,
412:        uint256 _deadline
413:    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {

594:    function swapExactAVAXForTokensSupportingFeeOnTransferTokens(
595:        uint256 _amountOutMin,
596:        uint256[] memory _pairBinSteps,
597:        IERC20[] memory _tokenPath,
598:        address _to,
599:        uint256 _deadline
600:    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {

431:    function swapTokensForExactTokens(
432:        uint256 _amountOut,
433:        uint256 _amountInMax,
434:        uint256[] memory _pairBinSteps,
435:        IERC20[] memory _tokenPath,
436:        address _to,
437:        uint256 _deadline
438:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {

459:    function swapTokensForExactAVAX(
460:        uint256 _amountAVAXOut,
461:        uint256 _amountInMax,
462:        uint256[] memory _pairBinSteps,
463:        IERC20[] memory _tokenPath,
464:        address payable _to,
465:        uint256 _deadline
466:    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {

642:    function sweepLBToken(
643:        ILBToken _lbToken,
644:        address _to,
645:        uint256[] memory _ids,
646:        uint256[] memory _amounts
647:    ) external override onlyFactoryOwner {
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol)

****[G‑03] Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement**

`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 2 instance of this issue:*

```solidity
File: src/LBQuoter.sol
59:        if (_route.length < 2) {
60:            revert LBQuoter_InvalidLength();
61:         }
65:        uint256 swapLength = _route.length - 1;
144:        uint256 swapLength = _route.length - 1;
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L65](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L65)

****[G‑04] `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)**

Saves **5 gas per loop**

*There are 3 instances of this issue:*

```solidity
File: src/LBQuoter.sol

75:        for (uint256 i; i < swapLength; i++) {
100:                for (uint256 j; j < LBPairsAvailable.length; j++) {
177:                for (uint256 j; j < LBPairsAvailable.length; j++) {
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)

****[G‑18] Using `private` rather than `public` for constants, saves gas**

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 4 instances of this issue:*

```solidity
File: src/LBFactory.sol

25:    uint256 public constant override MAX_FEE = 0.1e18; // 10%
27:    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
28:    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
30:    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25)