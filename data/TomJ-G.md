## Table of Contents
Total of 11 issues found.
- Should Use Unchecked Block where Over/Underflow is not Possible
- Minimize the Number of SLOADs by Caching State Variable
- Defined Variables Used Only Once
- Redundant Use of Variable
- Storage Variables can be Packed into Fewer Storage Slots
- Unchanging State Variable Should be Immutable
- Change Function Visibility Public to External
- Internal Function Called Only Once can be Inlined
- Use Calldata instead of Memory for Read Only Function Parameters
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- ++i Costs Less Gas than i++

&ensp;
### Should Use Unchecked Block where Over/Underflow is not Possible

#### Issue
Since Solidity 0.8.0, all arithmetic operations revert on overflow and underflow by default.
However in places where overflow and underflow is not possible, it is better to use unchecked block to reduce the gas usage.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#checked-or-unchecked-arithmetic

#### PoC
1. 3 instances found in _getAmountsIn() of LBRouter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711-L715
```solidity
Wrap line 712, 713, 715 with unchecked since underflow is not possible due to line 711 check
711:        for (uint256 i = _pairs.length; i != 0; i--) {
712:            IERC20 _token = _tokenPath[i - 1];
713:            uint256 _binStep = _pairBinSteps[i - 1];
715:            address _pair = _pairs[i - 1];
```

2. 1 instance found in findBestPathFromAmountIn() of LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L59-65
```solidity
Wrap line 65 with unchecked since underflow is not possible due to line 59 check
59:        if (_route.length < 2) {
65:        uint256 swapLength = _route.length - 1;
```

3. 19 instances found in findBestPathFromAmountOut() of LBQuoter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L139-144
```solidity
Wrap line 144 with unchecked since underflow is not possible due to line 139 check
139:        if (_route.length < 2) {
144:        uint256 swapLength = _route.length - 1;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154-L201
```solidity
Wrap line 156-158, 161-162, 166, 172, 184, 186-188, 192, 196, 201 with unchecked since underflow is not possible due to line 154 check
154:        for (uint256 i = swapLength; i > 0; i--) { 
156:            quote.pairs[i - 1] = IJoeFactory(factoryV1).getPair(_route[i - 1], _route[i]); // 2 instances
157:            if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) { // 1 instance
158:                (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i - 1], _route[i - 1], _route[i]); // 2 instances
161:                    quote.amounts[i - 1] = JoeLibrary.getAmountIn(quote.amounts[i], reserveIn, reserveOut); // 1 instance
162:                    quote.virtualAmountsWithoutSlippage[i - 1] = // 1 instance
166:                    quote.fees[i - 1] = 0.003e18; // 0.3% // 1 instance
172:                IERC20(_route[i - 1]), // 1 instance
184:                                swapAmountIn != 0 && (swapAmountIn < quote.amounts[i - 1] || quote.amounts[i - 1] == 0) // 2 instances
186:                                quote.amounts[i - 1] = swapAmountIn; // 1 instance
187:                                quote.pairs[i - 1] = address(LBPairsAvailable[j].LBPair); // 1 instance
188:                                quote.binSteps[i - 1] = LBPairsAvailable[j].binStep; // 1 instance
192:                                quote.virtualAmountsWithoutSlippage[i - 1] = // 1 instance
196:                                        quote.binSteps[i - 1], // 1 instance
201:                                quote.fees[i - 1] = (fees * 1e18) / quote.amounts[i - 1]; // fee percentage in amountIn // 2 instances
```

#### Mitigation
Wrap the code with uncheck like described in above PoC. 

&ensp;
### Minimize the Number of SLOADs by Caching State Variable

#### Issue
SLOADs cost 100 gas where MLOADs/MSTOREs cost only 3 gas.
Whenever function reads storage value more than once, it should be cached to save gas.

#### PoC
Total of 6 instances found.

1. Cache tokenX of swap() of LBPair.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L313
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L411

2. Cache tokenX of flashLoan() of LBPair.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L435
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L447

3. Cache tokenX of mint() of LBPair.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L490
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L598

4. Cache tokenY of swap() of LBPair.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L314
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L408

5. Cache tokenY of flashLoan() of LBPair.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L436
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L448

6. Cache tokenX of mint() of LBPair.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L491
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L603

#### Mitigation
When certain state variable is read more than once, cache it to local variable to save gas.

&ensp;
### Defined Variables Used Only Once

#### Issue
Certain variable is defined even though they are used only once. Remove these unnecessary variables to save gas.
For cases where it will reduce the readability, one can use comments to help describe what the code is doing.

#### PoC
1. Remove `_salt` variable of createLBPair() of LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L265-266
Mitigation:
Delete line 265 and replace line 266 with code below. 
```solidity
        _LBPair = ILBPair(Clones.cloneDeterministic(_LBPairImplementation, keccak256(abi.encode(_tokenA, _tokenB, _binStep))));
```

2. Remove `_preset` variable of setPreset() of LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L363-L367
Mitigation:
Delete line 363-365 and replace line 367 with code below. 
```solidity
        _presets[_binStep] = bytes32((uint256(_packedFeeParameters) & type(uint144).max) | (uint256(_sampleLifetime) << 240));
```

#### Mitigation
Don't define variable that is used only once.

&ensp;
### Redundant Use of Variable

#### Issue
Below has redundant use of variables. 
Instead of defining a new variable, emit the event first and then update the variable.

#### PoC
Total of 1 instance found.

1. `_owner` of _transferOwnership() of PendingOwnable.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L91-L96
Change it to
```
    function _transferOwnership(address _newOwner) internal virtual {
        emit OwnershipTransferred(_owner, _newOwner);
        _owner = _newOwner;
        _pendingOwner = address(0);
    }
```

#### Mitigation
Instead of defining a new variable, emit the event first and then update the variable like shown in above PoC.

&ensp;
### Storage Variables can be Packed into Fewer Storage Slots

#### Issue
The order of storage variables can be reordered in a way to reduce the usage amount of storage slots.
```
Reference from solidity documentation:
Finally, in order to allow the EVM to optimize for this, ensure that you try to order your storage 
variables and struct members such that they can be packed tightly. For example, declaring your 
storage variables in the order of uint128, uint128, uint256 instead of uint128, uint256, uint128, 
as the former will only take up two slots of storage whereas the latter will take up three.
```
https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html#layout-of-state-variables-in-storage

#### PoC
Total of 1 instance found.

1. LBFactory.sol
We can save 1 storage slot by reordering it like below.
Move bool variable on line 39 (1 byte size) to line 35 so that it will be packed with address variable (20 bytes size).
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L34-L39
```solidity
34:    address public override feeRecipient;
35:
36:    uint256 public override flashLoanFee;
37:
38:    /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
39:    bool public override creationUnlocked;
```

#### Mitigation
Reorder storage variables like shown in above PoC.

&ensp;
### Unchanging State Variable Should be Immutable

#### Issue
State variable that is only set in the constructor and can't be changed afterwards,
should be declared as immutable.

#### PoC
Total of 2 instances found.

1. tokenX variable of LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L54

2. tokenY variable of LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L55

#### Mitigation
Change to immutable.

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 4 instances found.

1. LBToken.sol:name()
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L49

2. LBToken.sol:symbol()
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L55

3. LBToken.sol:userPositionAtIndex()
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L100

4. LBToken.sol:userPositionNumber()
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L107

#### Mitigation
Change the function visibility to external.

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 1 instances found.

1. _setApprovalForAll() of LBToken.sol
This function called only once at line 123
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L253
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L123

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only and external function.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 33 instances found.

```solidity
LBPair.sol
261:    function pendingFees(address _account, uint256[] memory _ids)
467:        uint256[] memory _ids,
468:        uint256[] memory _distributionX,
469:        uint256[] memory _distributionY,
617:        uint256[] memory _ids,
618:        uint256[] memory _amounts,
688:    function collectFees(address _account, uint256[] memory _ids)

LBRouter.sol
207:    function addLiquidity(LiquidityParameters memory _liquidityParameters)
230:    function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)
280:        uint256[] memory _ids,
281:        uint256[] memory _amounts,
313:        uint256[] memory _ids,
314:        uint256[] memory _amounts,
355:        uint256[] memory _pairBinSteps,
356:        IERC20[] memory _tokenPath,
380:        uint256[] memory _pairBinSteps,
381:        IERC20[] memory _tokenPath,
409:        uint256[] memory _pairBinSteps,
410:        IERC20[] memory _tokenPath,
434:        uint256[] memory _pairBinSteps,
435:        IERC20[] memory _tokenPath,
462:        uint256[] memory _pairBinSteps,
463:        IERC20[] memory _tokenPath,
495:        uint256[] memory _pairBinSteps,
496:        IERC20[] memory _tokenPath,
534:        uint256[] memory _pairBinSteps,
535:        IERC20[] memory _tokenPath,
564:        uint256[] memory _pairBinSteps,
565:        IERC20[] memory _tokenPath,
596:        uint256[] memory _pairBinSteps,
597:        IERC20[] memory _tokenPath,
645:        uint256[] memory _ids,
646:        uint256[] memory _amounts
```

#### Mitigation
Change memory to calldata

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 47 instances found.
```
BitMath.sol:
16:        uint8 _bit,

SwapHelper.sol:
111:        uint112 amountInToBin,
112:        uint112 amountOutOfBin

TreeMath.sol:
23:        uint24 _binId,
25:    ) internal view returns (uint24) {
104:    function _getIdsFromAbove(uint24 _id) private pure returns (uint24, uint24) {
113:    function _getBottomId(uint24 _branchId, uint24 _leafId) private pure returns (uint24) {

LBFactory.sol:
120:    function getPreset(uint16 _binStep)
237:        uint24 _activeId,
238:        uint16 _binStep
341:        uint16 _binStep,
342:        uint16 _baseFactor,
343:        uint16 _filterPeriod,
344:        uint16 _decayPeriod,
345:        uint16 _reductionFactor,
346:        uint24 _variableFeeControl,
347:        uint16 _protocolShare,
348:        uint24 _maxVolatilityAccumulated,
349:        uint16 _sampleLifetime
396:    function removePreset(uint16 _binStep) external override onlyOwner {
426:        uint16 _binStep,
427:        uint16 _baseFactor,
428:        uint16 _filterPeriod,
429:        uint16 _decayPeriod,
430:        uint16 _reductionFactor,
431:        uint24 _variableFeeControl,
432:        uint16 _protocolShare,
433:        uint24 _maxVolatilityAccumulated
534:        uint16 _binStep,
535:        uint16 _baseFactor,
536:        uint16 _filterPeriod,
537:        uint16 _decayPeriod,
538:        uint16 _reductionFactor,
539:        uint24 _variableFeeControl,
540:        uint16 _protocolShare,
541:        uint24 _maxVolatilityAccumulated

LBPair.sol:
107:        uint24 _activeId,
108:        uint16 _sampleLifetime,
243:    function findFirstNonEmptyBinId(uint24 _id, bool _swapForY) external view override returns (uint24) {
624:                uint24 _id = _ids[i].safe24();
679:    function increaseOracleLength(uint16 _newSize) external override {
921:    function _increaseOracle(uint16 _newSize) private {

LBRouter.sol:
196:        uint24 _activeId,
197:        uint16 _binStep
277:        uint16 _binStep,
310:        uint16 _binStep,
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 5 instances found.
```
./LBQuoter.sol:75:        for (uint256 i; i < swapLength; i++) {
./LBQuoter.sol:100:                for (uint256 j; j < LBPairsAvailable.length; j++) {
./LBQuoter.sol:177:                for (uint256 j; j < LBPairsAvailable.length; j++) {
./LBRouter.sol:711:        for (uint256 i = _pairs.length; i != 0; i--) {
./LBQuoter.sol:154:        for (uint256 i = swapLength; i > 0; i--) {
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.

&ensp;