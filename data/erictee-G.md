### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
src/LBFactory.sol:L265        bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));

```

### [G-02] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
src/LBToken.sol:L21    mapping(address => mapping(address => bool)) private _spenderApprovals;

src/LBFactory.sol:L39    bool public override creationUnlocked;

```

### [G-03] Cache Array Length Outside of Loop


#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.


#### Findings:
```
src/LBToken.sol:L90            for (uint256 i; i < _accounts.length; ++i) {

src/LBToken.sol:L163            for (uint256 i; i < _ids.length; ++i) {

src/LBQuoter.sol:L100                for (uint256 j; j < LBPairsAvailable.length; j++) {

src/LBRouter.sol:L674            for (uint256 i; i < depositIds.length; ++i) {

src/LBRouter.sol:L711        for (uint256 i = _pairs.length; i != 0; i--) {

src/LBRouter.sol:L778            for (uint256 i; i < _pairs.length; ++i) {

src/LBRouter.sol:L831            for (uint256 i; i < _pairs.length; ++i) {

src/LBRouter.sol:L878            for (uint256 i; i < _pairs.length; ++i) {

src/LBRouter.sol:L951            for (uint256 i; i < pairs.length; ++i) {

src/LBPair.sol:L274            for (uint256 i; i < _ids.length; ++i) {

src/LBPair.sol:L496            for (uint256 i; i < _ids.length; ++i) {

src/LBPair.sol:L623            for (uint256 i; i < _ids.length; ++i) {

src/LBPair.sol:L701            for (uint256 i; i < _ids.length; ++i) {

```

### [G-04] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/LBToken.sol:L326    ) internal virtual {}

src/LBQuoter.sol:L123                        } catch {}

src/LBQuoter.sol:L203                        } catch {}

```

### [G-05] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
src/LBToken.sol:L3      pragma solidity ^0.8.0;

src/LBQuoter.sol:L3      pragma solidity ^0.8.0;

src/LBErrors.sol:L3      pragma solidity ^0.8.0;

src/LBFactory.sol:L3      pragma solidity ^0.8.0;

src/LBRouter.sol:L3      pragma solidity ^0.8.0;

src/LBPair.sol:L3      pragma solidity ^0.8.0;

src/libraries/Samples.sol:L3      pragma solidity ^0.8.0;

src/libraries/Decoder.sol:L3      pragma solidity ^0.8.0;

src/libraries/FeeHelper.sol:L3      pragma solidity ^0.8.0;

src/libraries/TreeMath.sol:L3      pragma solidity ^0.8.0;

src/libraries/Buffer.sol:L3      pragma solidity ^0.8.0;

src/libraries/BitMath.sol:L3      pragma solidity ^0.8.0;

src/libraries/Math128x128.sol:L3      pragma solidity ^0.8.0;

src/libraries/JoeLibrary.sol:L3      pragma solidity ^0.8.0;

src/libraries/Oracle.sol:L3      pragma solidity ^0.8.0;

src/libraries/FeeDistributionHelper.sol:L3      pragma solidity ^0.8.0;

src/libraries/TokenHelper.sol:L3      pragma solidity ^0.8.0;

src/libraries/PendingOwnable.sol:L3      pragma solidity ^0.8.0;

src/libraries/Constants.sol:L3      pragma solidity ^0.8.0;

src/libraries/ReentrancyGuardUpgradeable.sol:L3      pragma solidity ^0.8.0;

src/libraries/SafeMath.sol:L3      pragma solidity ^0.8.0;

src/libraries/SafeCast.sol:L3      pragma solidity ^0.8.0;

src/libraries/Math512Bits.sol:L3      pragma solidity ^0.8.0;

src/libraries/SwapHelper.sol:L3      pragma solidity ^0.8.0;

src/libraries/BinHelper.sol:L3      pragma solidity ^0.8.0;

src/libraries/Encoder.sol:L3      pragma solidity ^0.8.0;

src/interfaces/IPendingOwnable.sol:L3      pragma solidity ^0.8.0;

src/interfaces/ILBPair.sol:L3      pragma solidity ^0.8.0;

src/interfaces/ILBRouter.sol:L3      pragma solidity ^0.8.0;

src/interfaces/IJoePair.sol:L3      pragma solidity ^0.8.0;

src/interfaces/ILBFlashLoanCallback.sol:L3      pragma solidity ^0.8.0;

src/interfaces/ILBFactory.sol:L3      pragma solidity ^0.8.0;

src/interfaces/IWAVAX.sol:L3      pragma solidity ^0.8.0;

src/interfaces/IJoeRouter02.sol:L3      pragma solidity ^0.8.0;

src/interfaces/IJoeRouter01.sol:L3      pragma solidity ^0.8.0;

src/interfaces/ILBToken.sol:L3      pragma solidity ^0.8.0;

src/interfaces/IJoeFactory.sol:L3      pragma solidity ^0.8.0;

```

### [G-06] Functions guaranteed to revert when called by normal users can be declared as payable.


#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.


#### Findings:
```

src/LBFactory.sol:L215    function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {

src/LBFactory.sol:L396    function removePreset(uint16 _binStep) external override onlyOwner {

src/LBFactory.sol:L468    function setFeeRecipient(address _feeRecipient) external override onlyOwner {

src/LBFactory.sol:L474    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

src/LBFactory.sol:L485    function setFactoryLockedState(bool _locked) external override onlyOwner {

src/LBFactory.sol:L493    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

src/LBFactory.sol:L502    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

src/LBFactory.sol:L520    function forceDecay(ILBPair _LBPair) external override onlyOwner {

src/LBPair.sol:L788    function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {

src/LBPair.sol:L792    function forceDecay() external override onlyFactory {

src/libraries/PendingOwnable.sol:L59    function setPendingOwner(address pendingOwner_) public override onlyOwner {

src/libraries/PendingOwnable.sol:L68    function revokePendingOwner() public override onlyOwner {

src/libraries/PendingOwnable.sol:L75    function becomeOwner() public override onlyPendingOwner {

src/libraries/PendingOwnable.sol:L84    function renounceOwnership() public override onlyOwner {

```

### [G-07] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/LBToken.sol:L211        _totalSupplies[_id] += _amount;

src/LBFactory.sol:L143        baseFactor = _preset.decode(type(uint16).max, _shift += 16);

src/LBFactory.sol:L144        filterPeriod = _preset.decode(type(uint16).max, _shift += 16);

src/LBFactory.sol:L145        decayPeriod = _preset.decode(type(uint16).max, _shift += 16);

src/LBFactory.sol:L146        reductionFactor = _preset.decode(type(uint16).max, _shift += 16);

src/LBFactory.sol:L147        variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);

src/LBFactory.sol:L148        protocolShare = _preset.decode(type(uint16).max, _shift += 24);

src/LBFactory.sol:L149        maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);

src/LBRouter.sol:L128                amountIn += _amountInWithFees;

src/LBRouter.sol:L129                feesIn += _fee;

src/LBRouter.sol:L174                feesIn += _fees.total;

src/LBRouter.sol:L175                amountOut += _amountOutOfBin;

src/LBPair.sol:L227                cumulativeId += _activeId * _deltaT;

src/LBPair.sol:L228                cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;

src/LBPair.sol:L287                    amountX += _amountX;

src/LBPair.sol:L288                    amountY += _amountY;

src/LBPair.sol:L337                _amountOut += _amountOutOfBin;

src/LBPair.sol:L452        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);

src/LBPair.sol:L453        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);

src/LBPair.sol:L508                    (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||

src/LBPair.sol:L509                    (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION

src/LBPair.sol:L540                            _mintInfo.activeFeeX += _fees.total;

src/LBPair.sol:L551                            _mintInfo.activeFeeY += _fees.total;

src/LBPair.sol:L572                _pair.reserveX += uint112(_mintInfo.amountX);

src/LBPair.sol:L573                _pair.reserveY += uint112(_mintInfo.amountY);

src/LBPair.sol:L575                _mintInfo.amountXAddedToPair += _mintInfo.amountX;

src/LBPair.sol:L576                _mintInfo.amountYAddedToPair += _mintInfo.amountY;

src/LBPair.sol:L639                    amountY += _amountY;

src/LBPair.sol:L646                    amountX += _amountX;

src/LBPair.sol:L711                    amountX += _amountX;

src/LBPair.sol:L712                    amountY += _amountY;

src/LBPair.sol:L896            (amountX += _amountX).safe128();

src/LBPair.sol:L897            (amountY += _amountY).safe128();

src/libraries/BitMath.sol:L77                msb += 64;

src/libraries/BitMath.sol:L81                msb += 32;

src/libraries/BitMath.sol:L85                msb += 16;

src/libraries/BitMath.sol:L89                msb += 8;

src/libraries/BitMath.sol:L93                msb += 4;

src/libraries/BitMath.sol:L97                msb += 2;

src/libraries/BitMath.sol:L100                msb += 1;

src/libraries/BitMath.sol:L116                lsb += 64;

src/libraries/BitMath.sol:L120                lsb += 32;

src/libraries/BitMath.sol:L124                lsb += 16;

src/libraries/BitMath.sol:L128                lsb += 8;

src/libraries/BitMath.sol:L132                lsb += 4;

src/libraries/BitMath.sol:L136                lsb += 2;

src/libraries/BitMath.sol:L139                lsb += 1;

src/libraries/Math128x128.sol:L77                        result += delta;

src/libraries/FeeDistributionHelper.sol:L42            _pairFees.protocol += _fees.protocol;

src/libraries/Math512Bits.sol:L71                result += prod1 << (256 - offset);

src/libraries/Math512Bits.sol:L99            if (mulmod(x, y, 1 << offset) != 0) result += 1;

src/libraries/Math512Bits.sol:L159            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;

src/libraries/SwapHelper.sol:L88        pairFees.total += fees.total;

src/libraries/SwapHelper.sol:L91            pairFees.protocol += fees.protocol;

src/libraries/SwapHelper.sol:L95            bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);

src/libraries/SwapHelper.sol:L97            bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);

src/libraries/SwapHelper.sol:L115            bin.reserveX += amountInToBin;

src/libraries/SwapHelper.sol:L119                pair.reserveX += uint136(amountInToBin);

src/libraries/SwapHelper.sol:L123            bin.reserveY += amountInToBin;

src/libraries/SwapHelper.sol:L128                pair.reserveY += uint136(amountInToBin);

```

### [G-08] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per loop.


#### Findings:
```
src/LBQuoter.sol:L75        for (uint256 i; i < swapLength; i++) {

src/LBQuoter.sol:L100                for (uint256 j; j < LBPairsAvailable.length; j++) {

src/LBQuoter.sol:L177                for (uint256 j; j < LBPairsAvailable.length; j++) {

```

### [G-09] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
src/LBFactory.sol:L25    uint256 public constant override MAX_FEE = 0.1e18; // 10%

src/LBFactory.sol:L27    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%

src/LBFactory.sol:L28    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons

src/LBFactory.sol:L30    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%

```

### [G-10] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
src/libraries/Samples.sol:L19    uint256 private constant _OFFSET_INITIALIZED = 0;

```

### [G-11] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
src/LBToken.sol:L90            for (uint256 i; i < _accounts.length; ++i) {

src/LBToken.sol:L163            for (uint256 i; i < _ids.length; ++i) {

src/LBQuoter.sol:L75        for (uint256 i; i < swapLength; i++) {

src/LBFactory.sol:L165                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

src/LBFactory.sol:L195                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

src/LBRouter.sol:L674            for (uint256 i; i < depositIds.length; ++i) {

src/LBRouter.sol:L778            for (uint256 i; i < _pairs.length; ++i) {

src/LBRouter.sol:L831            for (uint256 i; i < _pairs.length; ++i) {

src/LBRouter.sol:L878            for (uint256 i; i < _pairs.length; ++i) {

src/LBRouter.sol:L951            for (uint256 i; i < pairs.length; ++i) {

src/LBPair.sol:L274            for (uint256 i; i < _ids.length; ++i) {

src/LBPair.sol:L496            for (uint256 i; i < _ids.length; ++i) {

src/LBPair.sol:L623            for (uint256 i; i < _ids.length; ++i) {

src/LBPair.sol:L701            for (uint256 i; i < _ids.length; ++i) {

```

