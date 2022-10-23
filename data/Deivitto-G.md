
# GAS

## Public function visibility can be made external
### Summary
Functions should have the strictest visibility possible. Public functions may lead to more gas usage by forcing the copy of their parameters to memory from calldata.
### Details
If a function is never called from the contract it should be marked as external. This will save gas.
### Github Permalinks

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L134-L208

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L54-L128

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L122-L124

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L107-L109

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L100-L102

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L115-L117

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L131-L142

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L55-L57

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L49-L51

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L149-L169

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L79-L94

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L63-L65

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L122-L124

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L107-L109

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L100-L102

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L115-L117

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L131-L142

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L55-L57

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L49-L51

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L149-L169

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L79-L94

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L53-L55

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L84-L86

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L75-L77

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L59-L63

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L68-L71

### Mitigation
Consider changing visibility from public to external.


## SafeMath can be not used
### Summary
No need of using safemath after 0.8.0, removing it will improve gas costs
### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L17
import "./libraries/SafeMath.sol";

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L33
    using SafeMath for uint256;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L7
import "./SafeMath.sol";

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L14
    using SafeMath for uint256;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/SafeMath.sol#L8
library SafeMath {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/SwapHelper.sol#L10
import "./SafeMath.sol";

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/SwapHelper.sol#L19
    using SafeMath for uint256;

### Mitigation
Consider removing it

## Empty blocks should be removed or emit something
### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation.

### Details
If the block is an empty if -statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified ( if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}} )
### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L123
                        } catch {}

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L203
                        } catch {}

### Mitigation
Remove empty blocked or emit something.

## Using bools for storage incurs overhead { 
### Summary
Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

### Details
Here is one example of OpenZeppelin about this optimization
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L39
    bool public override creationUnlocked;
### Mitigation
Consider using uint256 with values 0 and 1 rather than bool


## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L64-L66
- - - 
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L67-L70
- - -
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L18
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L24
https://github.com/code-423n4/2022-10-traderjoe/blob/
- - -
37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L21
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L27
### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.

## Pack structs tightly
### Summary
Gas efficiency can be achieved by tightly packing the struct. Struct variables are stored in 32 bytes each so you can group smaller types to occupy less storage. 

### Details
You can read more here: https://fravoll.github.io/solidity-patterns/tight_variable_packing.html or in the official documentation: https://docs.soliditylang.org/en/v0.4.21/miscellaneous.html
### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L39-L50
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L29-L42

### Mitigation
Order structs to reduce gas usage.

## Using private rather than public for constants saves gas
### Summary
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

### Github Permalinks

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L25
    uint256 public constant override MAX_FEE = 0.1e18; // 10%

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L27
    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L28
    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L30
    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%

### Mitigation
Consider replacing public for private in constants for gas saving.

## ++i costs less gas compared to i++ or i+=1, the same happens with --i and i-- or i-=1
### Summary
++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). 
This statement is true even with the optimizer enabled. 

### Details
i++ increments i and returns the initial value of i . 
Which means:
```
uint i = 1;
i++; // == 1 but i == 2
```

But ++i returns the actual incremented value:

```
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

### Github Permalinks
`var++`

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L75
        for (uint256 i; i < swapLength; i++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L100
                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L177
                for (uint256 j; j < LBPairsAvailable.length; j++) {

`+= 1`
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L100
                msb += 1;
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L139
                lsb += 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L99
            if (mulmod(x, y, 1 << offset) != 0) result += 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L159
            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;

`var--`
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L154
        for (uint256 i = swapLength; i > 0; i--) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L711
        for (uint256 i = _pairs.length; i != 0; i--) {


### Mitigation
Replace to `++i` or `--i` as needed.


## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L165
                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L195
                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L274
            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L496
            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L623
            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L701
            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L929
            for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L75
        for (uint256 i; i < swapLength; i++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L100
                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L154
        for (uint256 i = swapLength; i > 0; i--) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L177
                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L674
            for (uint256 i; i < depositIds.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L711
        for (uint256 i = _pairs.length; i != 0; i--) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L778
            for (uint256 i; i < _pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L831
            for (uint256 i; i < _pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L878
            for (uint256 i; i < _pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L951
            for (uint256 i; i < pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L90
            for (uint256 i; i < _accounts.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L163
            for (uint256 i; i < _ids.length; ++i) {
### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header

## Functions guaranteed to revert when called by normal users can be marked `payable`
### Summary
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function.

Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Details
The extra opcodes avoided are:
CALLVALUE (2), DUP1 (3), ISZERO (3), PUSH2 (3), JUMPI (10), PUSH1 (3), DUP1 (3), REVERT(0), JUMPDEST (1), POP (2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

### Github Permalinks
github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L317
    ) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L350
    ) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L396
    function removePreset(uint16 _binStep) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L434
    ) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L468
    function setFeeRecipient(address _feeRecipient) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L474
    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L485
    function setFactoryLockedState(bool _locked) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L493
    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L502
    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L520
    function forceDecay(ILBPair _LBPair) external override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L22
/// `onlyOwner`, which can be applied to your functions to restrict their use to

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L29
    modifier onlyOwner() {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L59
    function setPendingOwner(address pendingOwner_) public override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L68
    function revokePendingOwner() public override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L80
    /// `onlyOwner` functions anymore. Can only be called by the current owner.

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L84
    function renounceOwnership() public override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L241
        if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L30
        if (msg.sender != _owner) revert PendingOwnable__NotOwner();

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L273
            createdByOwner: msg.sender == _owner,



### Mitigation
Consider adding payable to save gas


## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables

### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L452-L453
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L211
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeDistributionHelper.sol#L42

- Same but -=
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L241

### Mitigation
Don't use `+=` for state variables as it cost more gas.
