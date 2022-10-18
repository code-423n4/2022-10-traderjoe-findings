## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 20 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Missing Contract-existence Checks Before Low-level Calls | 3 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Critical Changes Should Use Two-step Procedure | 10 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug | 2 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing parameter validation | 1 |

Total: 36 instances over 5 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Use a more recent version of Solidity | 28 |
| [NC&#x2011;2](#NC&#x2011;2) | Public Functions Not Called By The Contract Should Be Declared External Instead | 6 |
| [NC&#x2011;3](#NC&#x2011;3) | Constants Should Be Defined Rather Than Using Magic Numbers | 13 |
| [NC&#x2011;4](#NC&#x2011;4) | Missing event for critical parameter change | 1 |
| [NC&#x2011;5](#NC&#x2011;5) | Implementation contract may not be initialized | 5 |
| [NC&#x2011;6](#NC&#x2011;6) | Use of Block.Timestamp | 8 |
| [NC&#x2011;7](#NC&#x2011;7) | Non-usage of specific imports | 78 |
| [NC&#x2011;8](#NC&#x2011;8) | Lines are too long | 64 |
| [NC&#x2011;9](#NC&#x2011;9) | Use `bytes.concat()` | 1 |

Total: 204 instances over 9 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```
function setLBPairImplementation: address _LBPairImplementation
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215

```
function setFeeRecipient: address _feeRecipient
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L468

```
function swap: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L304

```
function flashLoan: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L420

```
function mint: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L466

```
function burn: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L616

```
function collectFees: address _account
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L688

```
function removeLiquidity: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L274

```
function removeLiquidityAVAX: address payable _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L308

```
function swapExactTokensForTokens: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L352

```
function swapExactTokensForAVAX: address payable _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L377

```
function swapExactAVAXForTokens: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L407

```
function swapTokensForExactTokens: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L431

```
function swapTokensForExactAVAX: address payable _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L459

```
function swapAVAXForExactTokens: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L493

```
function swapExactTokensForTokensSupportingFeeOnTransferTokens: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L531

```
function swapExactTokensForAVAXSupportingFeeOnTransferTokens: address payable _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L561

```
function swapExactAVAXForTokensSupportingFeeOnTransferTokens: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L594

```
function sweep: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L622

```
function sweepLBToken: address _to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L642



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.


### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```
(bool success, ) = _to.call{value: _amount}("");
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L964

```
(bool success, bytes memory result) = address(token).call(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L28

```
(bool success, bytes memory result) = address(token).call(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L46




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```
function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215

```
function setLBPairIgnored(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L312

```
function setPreset(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L340

```
function setFeesParametersOnPair(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L423

```
function setFeeRecipient(address _feeRecipient) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L468

```
function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L474

```
function setFactoryLockedState(bool _locked) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L485

```
function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L788

```
function setApprovalForAll(address _spender, bool _approved) public virtual override {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L122

```
function setPendingOwner(address pendingOwner_) public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L59



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Low Level Calls With Solidity Version 0.8.14 Can Result In Optimiser Bug

The project contracts in scope are using low level calls with solidity version before 0.8.14 which can result in optimizer bug.
https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

Simliar findings in Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#5-low-level-calls-with-solidity-version-0814-can-result-in-optimiser-bug

#### <ins>Proof Of Concept</ins>

POC can be found in the above medium reference url.

Functions that execute low level calls in contracts with solidity version under 0.8.14

```
assembly {
    mstore(0, _id)
    mstore(32, _bins.slot)
    let slot := keccak256(0, 64)

    let reserves := add(shl(_OFFSET_BIN_RESERVE_Y, _reserveY), _reserveX)
    sstore(slot, reserves)
}
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L654-L661


```
function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {
assembly {
    mstore(0, _id)
    mstore(32, _bins.slot)
    _data := sload(keccak256(0, 64))

    reserveX := and(_data, _mask112)
    reserveY := shr(_OFFSET_BIN_RESERVE_Y, _data)
}
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L998-L1005


#### <ins>Recommended Mitigation Steps</ins>

Consider upgrading to at least solidity v0.8.15.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing parameter validation

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```
address _routerV2
address _factoryV1
address _factoryV2

```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L41-L43


#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.


## Non Critical Issues


### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Use a more recent version of Solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

#### <ins>Proof Of Concept</ins>


```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/lib/openzeppelin-contracts/contracts/utils/math/SafeCast.sol#L4

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/lib/openzeppelin-contracts/contracts/utils/math/SafeMath.sol#L4

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BinHelper.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Buffer.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Constants.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Decoder.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Encoder.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeMath.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L3

```
pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol#L3



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```
function setApprovalForAll(address _spender, bool _approved) public virtual override {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L122

```
function safeBatchTransferFrom(
        address _from,
        address _to,
        uint256[] memory _ids,
        uint256[] memory _amounts
    )
        public
        virtual
        override
        checkLength(_ids.length, _amounts.length)
        checkAddresses(_from, _to)
        checkApproval(_from, msg.sender)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L149

```
function setPendingOwner(address pendingOwner_) public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L59

```
function revokePendingOwner() public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L68

```
function becomeOwner() public override onlyPendingOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L75

```
function renounceOwnership() public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L84





### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Constants Should Be Defined Rather Than Using Magic Numbers

#### <ins>Proof Of Concept</ins>

```
baseFactor = _preset.decode(type(uint16).max, _shift += 16);
filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
protocolShare = _preset.decode(type(uint16).max, _shift += 24);
maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);

sampleLifetime = _preset.decode(type(uint16).max, 240);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L143-L151

```
uint256 _nbPresets = _avPresets.decode(type(uint8).max, 248);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L159

```
uint256 _nbAvailable = _avLBPairBinSteps.decode(type(uint8).max, 248);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L189

```
uint256 _sampleLifetime = _preset.decode(type(uint16).max, 240);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L261

```
(uint256(_packedFeeParameters) & type(uint144).max) | (uint256(_sampleLifetime) << 240)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L364

```
_avPresets = bytes32(uint256(_avPresets) - (1 << 248));
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L403

```
uint256 _baseFee = (uint256(_baseFactor) * _binStep) * 1e10;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L558

```
amountY = _unclaimedData.decode(type(uint128).max, 128);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L270


```
amountY = _unclaimedData.decode(type(uint128).max, 128);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L699


```
uint256 amountY = _unclaimedData.decode(type(uint128).max, 128);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L891


```
amountsIn[i - 1] = (_reserveIn * amountOut_ * 1_000) / (_reserveOut - amountOut_ * 997) + 1;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L725

```
    amountOut = (_reserve1 * amountOut * 997) / (_reserve0 * 1_000 + amountOut * 997);
    IJoePair(_pair).swap(0, amountOut, _recipient, "");
} else {
    amountOut = (_reserve0 * amountOut * 997) / (_reserve1 * 1_000 + amountOut * 997);
    IJoePair(_pair).swap(amountOut, 0, _recipient, "");
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L791-L795

```
uint256 _amountOut = (_reserve1 * (_balance - _reserve0) * 997) / (_balance * 1_000);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L891



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>

```
function setFeesParameters(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L788



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```
constructor(address _feeRecipient, uint256 _flashLoanFee) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L63

```
constructor(ILBFactory _factory) LBToken() {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L90

```
constructor(
    address _routerV2,
    address _factoryV1,
    address _factoryV2
) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L40-L44

```
constructor(
    ILBFactory _factory,
    IJoeFactory _oldFactory,
    IWAVAX _wavax
) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L52-L56

```
constructor() {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L41


### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```
uint256 _lookUpTimestamp = block.timestamp - _timeDelta;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L208

```
_pair.oracleLastTimestamp = block.timestamp.safe40();
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L394

```
if (block.timestamp > _deadline) revert LBRouter__DeadlineExceeded(_deadline, block.timestamp);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L38

```
uint256 _deltaT = block.timestamp - _fp.time;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L56

```
_fp.time = (block.timestamp).safe40();
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L72

```
if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L118

```
uint256 _deltaTime = block.timestamp - timestamp(_lastSample);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L46

```
return pack(_cumulativeBinCrossed, _cumulativeVolatilityAccumulated, _cumulativeId, block.timestamp, 1);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L58



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### <ins>Proof Of Concept</ins>


```
import "./interfaces/ILBPair.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol#L5

```
import "./LBErrors.sol";
import "./libraries/Constants.sol";
import "./libraries/Decoder.sol";
import "./libraries/PendingOwnable.sol";
import "./libraries/SafeCast.sol";
import "./interfaces/ILBFactory.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L8-L13

```
import "./LBErrors.sol";
import "./LBToken.sol";
import "./libraries/BinHelper.sol";
import "./libraries/Constants.sol";
import "./libraries/Decoder.sol";
import "./libraries/FeeDistributionHelper.sol";
import "./libraries/Math512Bits.sol";
import "./libraries/Oracle.sol";
import "./libraries/ReentrancyGuardUpgradeable.sol";
import "./libraries/SafeCast.sol";
import "./libraries/SafeMath.sol";
import "./libraries/SwapHelper.sol";
import "./libraries/TokenHelper.sol";
import "./libraries/TreeMath.sol";
import "./interfaces/ILBFlashLoanCallback.sol";
import "./interfaces/ILBPair.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L7-L22

```
import "./LBErrors.sol";
import "./libraries/BinHelper.sol";
import "./libraries/JoeLibrary.sol";
import "./libraries/Math512Bits.sol";
import "./interfaces/IJoeFactory.sol";
import "./interfaces/IJoePair.sol";
import "./interfaces/ILBFactory.sol";
import "./interfaces/ILBRouter.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L5-L12

```
import "./LBErrors.sol";
import "./libraries/BinHelper.sol";
import "./libraries/Constants.sol";
import "./libraries/FeeHelper.sol";
import "./libraries/Math512Bits.sol";
import "./libraries/SwapHelper.sol";
import "./libraries/TokenHelper.sol";
import "./interfaces/IJoePair.sol";
import "./interfaces/ILBToken.sol";
import "./interfaces/ILBRouter.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L7-L16

```
import "./LBErrors.sol";
import "./interfaces/ILBToken.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L7-L8

```
import "../LBErrors.sol";
import "./Math128x128.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BinHelper.sol#L5-L6

```
import "../LBErrors.sol";
import "./Constants.sol";
import "./FeeHelper.sol";
import "./SafeCast.sol";
import "./TokenHelper.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L7-L11

```
import "./Constants.sol";
import "./SafeCast.sol";
import "./SafeMath.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L5-L7

```
import "../LBErrors.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L5

```
import "../LBErrors.sol";
import "./BitMath.sol";
import "./Constants.sol";
import "./Math512Bits.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L5-L8

```
import "../LBErrors.sol";
import "./BitMath.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L5-L6

```
import "../LBErrors.sol";
import "./Buffer.sol";
import "./Samples.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L5-L7

```
import "../LBErrors.sol";
import "../interfaces/IPendingOwnable.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L5-L6

```
import "../LBErrors.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol#L5

```
import "../LBErrors.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol#L5

```
import "./Decoder.sol";
import "./Encoder.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L5

```
import "./BinHelper.sol";
import "./Constants.sol";
import "./FeeDistributionHelper.sol";
import "./FeeHelper.sol";
import "./Math512Bits.sol";
import "./SafeMath.sol";
import "../interfaces/ILBPair.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L5-L11

```
import "../LBErrors.sol";
import "./BitMath.sol";
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol#L5-L6


#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```
/// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L38

```
/// @dev Mapping from a (tokenA, tokenB, binStep) to a LBPair. The tokens are ordered to save gas, but they can be
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L43

```
/// in the reverse order in the actual pair. Always query one of the 2 tokens of the pair to assert the order of the 2 tokens
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L44

```
// Whether a preset was set or not, if the bit at `index` is 1, it means that the binStep `index` was set
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L47

```
// The max binStep set is 247. We use this method instead of an array to keep it ordered and to reduce gas
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L48

```
// Whether a LBPair was created with a bin step, if the bit at `index` is 1, it means that the LBPair with binStep `index` exists
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L56

```
/// @notice Function to set whether the pair is ignored or not for routing, it will make the pair unusable by the router
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L307

```
/// @param _baseFactor The base factor, used to calculate the base fee, baseFee = baseFactor * binStep
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L332

```
/// @param _variableFeeControl The variable fee control, used to control the variable fee, can be 0 to disable them
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L336

```
/// @notice Function to set the recipient of the fees. This address needs to be able to receive ERC20s
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L466

```
// decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L562

```
/// @notice The implementation of Liquidity Book Pair that also acts as the receipt token for liquidity positions
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L26

```
/// @dev Mapping from account to user's unclaimed fees. The first 128 bits are tokenX and the last are for tokenY
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L67

```
/// @return oracleSampleLifetime The lifetime of a sample, it accumulates information for up to this timestamp
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L166

```
/// @param _swapForY Whether you've swapping token X for token Y (true) or token Y for token X (false)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L241

```
/// @notice Performs a low level swap, this needs to be called from a contract which performs important safety checks
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L298

```
/// @notice Performs a low level add, this needs to be called from a contract which performs important safety checks.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L458

```
// The addition or the cast can't overflow as it would have reverted during the L568 and L569 if amounts were greater than uint112
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L571

```
/// @notice Performs a low level remove, this needs to be called from a contract which performs important safety checks
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L610

```
/// @notice Internal view function to get the global fees information, the total fees and those for protocol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L1010

```
// We update the fee, but we don't store the new volatility reference, volatility accumulated and indexRef to not penalize traders
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L122

```
/// @notice Swaps exact tokens for tokens while performing safety checks supporting for fee on transfer tokens
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L523

```
/// @notice Swaps exact tokens for AVAX while performing safety checks supporting for fee on transfer tokens
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L553

```
/// @notice Swaps exact AVAX for tokens while performing safety checks supporting for fee on transfer tokens
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L587

```
/// @notice Grants or revokes permission to `spender` to transfer the caller's tokens, according to `approved`
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L119

```
/// @notice Returns the closest non-zero bit of `integer` to the right (of left) of the `bit` bits that is not `bit`
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L9

```
/// @param _rightSide Whether we're searching in the right side of the tree (true) or the left side (false)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L12

```
/// @return The index of the closest non-zero bit. If there is no closest bit, it returns max(uint256)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L13

```
/// - variableFeeControl: The variable fee control, used to control the variable fee, can be 0 to disable them
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L22

```
// decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L104

```
// given an input amount of an asset and pair reserves, returns the maximum output amount of the other asset
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L29

```
// given an output amount of an asset and pair reserves, returns a required input amount of the other asset
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L43

```
/// - The results are not perfectly accurate to the last decimal, due to the lossy precision of the iterative approximation
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L30

```
/// Also because x is converted to an unsigned 129.127-binary fixed-point number during the operation to optimize the multiplication
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L31

```
/// @param x The unsigned 128.128-binary fixed-point number for which to calculate the binary logarithm.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L33

```
// If we use an offset of 128 bits, y would need 129 bits and y**2 would would overflow and we would have to
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L37

```
// use mulDiv, by reducing x to 129.127-binary fixed-point number we assert that y will use 128 bits, and we
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L38

```
// Calculate the integer part of the logarithm and add it to the result and finally calculate y = x * 2^(-n).
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L57

```
// The integer part of the logarithm as a signed 129.127-binary fixed-point number. The operation can't overflow
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L60

```
// use the Chinese Remainder Theorem to reconstruct the 512 bit result. The result is stored in two 256
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L170

```
// Factor powers of two out of denominator and compute largest power of two divisor of denominator. Always >= 1
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L213

```
// Invert denominator mod 2^256. Now that denominator is an odd number, it has an inverse modulo 2^256 such
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L232

```
// that denominator * inv = 1 mod 2^256. Compute the inverse by starting with a seed that is correct for
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L233

```
// Use the Newton-Raphson iteration to improve the precision. Thanks to Hensel's lifting lemma, this also works
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L237

```
// Because the division is now exact we can divide by multiplying with the modular inverse of denominator.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L246

```
// This will give us the correct result modulo 2^256. Since the preconditions guarantee that the outcome is
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L247

```
// less than 2^256, this is the final result. We don't need to compute the high bits of the result and prod1
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L248

```
/// @param _sampleLifetime The lifetime of a sample, it accumulates information for up to this timestamp
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L99

```
/// @notice Binary search on oracle samples and return the 2 samples (as bytes32) that surrounds the `lookUpTimestamp`
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L134

```
/// @dev The oracle needs to be in increasing order `{_index + 1, _index + 2 ..., _index + _activeSize} % _activeSize`.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L135

```
/// The sample that aren't initialized yet will be skipped as _activeSize only contains the samples that are initialized.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L136

```
/// This function works only if `timestamp(_oracle[_index + 1 % _activeSize] <= _lookUpTimestamp <= timestamp(_oracle[_index]`.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L137

```
///  [ cumulativeBinCrossed | cumulativeVolatilityAccumulated | cumulativeId | timestamp | initialized ]
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L15

```
///  [        uint87        |              uint64             |    uint64    |   uint40  |    bool1    ]
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L16

```
/// MSB                                                                                               LSB
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L17

```
// It would be an issue if 2 overflows would happen but way too much time should elapsed for it to happen.
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L49

```
/// @param _rightSide Whether we're searching in the right side of the tree (true) or the left side (false)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol#L18





### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```
abi.encodePacked(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#574


#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 







