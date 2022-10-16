[1]  USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 37 instances of this issue*

```
File: 2022-10-traderjoe/src/LBFactory.sol

120:  function getPreset(uint16 _binStep)

237:  uint24 _activeId,

238:   uint16 _binStep

341-349:  uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated,
        uint16 _sampleLifetime

426-433:  uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated


534-541:   uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L120](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L120)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L237-L238](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L237-L238)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L341-L349](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L341-L349)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L426-L433](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L426-L433)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L534-L541](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L534-L541)

```

File: 2022-10-traderjoe/src/LBPair.sol

107 : uint24 _activeId,

108:  uint16 _sampleLifetime

243:   function findFirstNonEmptyBinId(uint24 _id, bool _swapForY) external view override returns (uint24) {

251:  function getBin(uint24 _id) external view override returns (uint256 reserveX, uint256 reserveY) {

994:  function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L107-L108](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L107-L108)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L243](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L243)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L251](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L251)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994)


```

Fle: 2022-10-traderjoe/src/LBRouter.sol

196:   uint24 _activeId,
197:   uint16 _binStep
277:   uint16 _binStep
310:  uint16 _binStep,

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L196-L197](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L196-L197)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L277](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L277)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L310](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L310)


[2] ``<ARRAY>.LENGTH`` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A`` FOR``-LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

*There are 16 instances of this issue:*

```

File: 2022-10-traderjoe/src/LBPair.sol

274: for (uint256 i; i < _ids.length; ++i) {

496:  for (uint256 i; i < _ids.length; ++i) {

623:  for (uint256 i; i < _ids.length; ++i) {

701:  for (uint256 i; i < _ids.length; ++i) {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701)

```

File: 2022-10-traderjoe/src/LBQuoter.sol

75:  for (uint256 i; i < swapLength; i++) {

100:  for (uint256 j; j < LBPairsAvailable.length; j++) {

154:  for (uint256 i = swapLength; i > 0; i--) {

177:  for (uint256 j; j < LBPairsAvailable.length; j++) {

```

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)


```

File: 2022-10-traderjoe/src/LBToken.sol

90:  for (uint256 i; i < _accounts.length; ++i) {

163:  for (uint256 i; i < _ids.length; ++i) {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163)


```
File: 2022-10-traderjoe/src/LBRouter.sol

674: for (uint256 i; i < depositIds.length; ++i) {

711: for (uint256 i = _pairs.length; i != 0; i--) {

778:  for (uint256 i; i < _pairs.length; ++i) {

831:  for (uint256 i; i < _pairs.length; ++i) {

878:  for (uint256 i; i < _pairs.length; ++i) {

951:   for (uint256 i; i < pairs.length; ++i) {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L778](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L778)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L831](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L831)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L878](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L878)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951)

[3] ``ABI.ENCODE()`` IS LESS EFFICIENT THAN ``ABI.ENCODEPACKED()``

*There is 1 instance of this issue:*

```
File : 2022-10-traderjoe/src/LBFactory.sol

265:  bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265)


[4] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 13 instances of this issue:*

```
File : 2022-10-traderjoe/src/LBFactory.sol

215: function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {

312-317: function setLBPairIgnored(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint256 _binStep,
        bool _ignored
    ) external override onlyOwner {

340-350:  function setPreset(
        uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated,
        uint16 _sampleLifetime
    ) external override onlyOwner {

396:  function removePreset(uint16 _binStep) external override onlyOwner {

423-434:  function setFeesParametersOnPair(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated
    ) external override onlyOwner {

468:  function setFeeRecipient(address _feeRecipient) external override onlyOwner {

474:  function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

485: function setFactoryLockedState(bool _locked) external override onlyOwner {

493:  function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

502: function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

520:   function forceDecay(ILBPair _LBPair) external override onlyOwner {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L312-L317](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L312-L317)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L340-L350](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L340-L350)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L396](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L396)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L423-L434](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L423-L434)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L493](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L493)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L502](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L502)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520)


```

Fle: 2022-10-traderjoe/src/LBRouter.sol

622-626:  function sweep(
        IERC20 _token,
        address _to,
        uint256 _amount
    ) external override onlyFactoryOwner {

642-647:  function sweepLBToken(
        ILBToken _lbToken,
        address _to,
        uint256[] memory _ids,
        uint256[] memory _amounts
    ) external override onlyFactoryOwner {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L622-L626](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L622-L626)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L642-L647](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L642-L647)


[5] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There is 1 instance of this issue:*

```
File: 2022-10-traderjoe/src/LBFactory.sol

25-30:   uint256 public constant override MAX_FEE = 0.1e18; // 10%

    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons

    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30)

[6] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
 
The ``unchecked`` keyword is new in solidity 0.8.0,so this only applies to that version or higher, which these instances are. This saves 30-40 gas *[PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)*

*There are 3 instances of this issue:*

```
File: 2022-10-traderjoe/src/LBQuoter.sol

75:  for (uint256 i; i < swapLength; i++) {

100:  for (uint256 j; j < LBPairsAvailable.length; j++) {

177:  for (uint256 j; j < LBPairsAvailable.length; j++) {

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)

[7]  ``PUBLIC`` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED ``EXTERNAL`` INSTEAD

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from ``external`` to ``public`` and can save gas by doing so.

*There are 2 instances of this issue:*

```
File: 2022-10-traderjoe/src/LBQuoter.sol

54-57: function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
        public
        view
        returns (Quote memory quote)

134-137:  function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
        public
        view
        returns (Quote memory quote)

```
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54-L57](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54-L57)

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134-L137](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134-L137)



