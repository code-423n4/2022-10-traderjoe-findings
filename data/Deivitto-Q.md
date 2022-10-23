# QA
# Low
## Prevent div by 0
### Impact
On several locations in the code precautions are taken not to divide by `0`, because this will revert the code. However on some locations this isn’t done.

### Proof of Concept
Navigate to the following contracts,

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeDistributionHelper.sol#L58
            return ((uint256(_fees.total) - _fees.protocol) << Constants.SCALE_OFFSET) / _totalSupply;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L196
                result = prod0 / denominator;

- Wrong returning value on  IJoePair(_pair).getReserves(); and similars may give revert
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L725
                amountsIn[i - 1] = (_reserveIn * amountOut_ * 1_000) / (_reserveOut - amountOut_ * 997) + 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L791
                        amountOut = (_reserve1 * amountOut * 997) / (_reserve0 * 1_000 + amountOut * 997);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L794
                        amountOut = (_reserve0 * amountOut * 997) / (_reserve1 * 1_000 + amountOut * 997);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L891
                        uint256 _amountOut = (_reserve1 * (_balance - _reserve0) * 997) / (_balance * 1_000);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L896
                        uint256 _amountOut = (_reserve0 * (_balance - _reserve1) * 997) / (_balance * 1_000);


### Recommended Mitigation Steps
Recommend making sure division by `0` won’t occur by checking the variables beforehand and handling this edge case.



## Risky including a renounce Owner 
### Impact
Risky including a renounce Owner  as this can lead the contract without Owner and therefore all the onlyOwner functions will be blocked

If called by error, following functions would get lost and forced to redeploy for getting them back:
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L317
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

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L59
    function setPendingOwner(address pendingOwner_) public override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L68
    function revokePendingOwner() public override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L84
    function renounceOwnership() public override onlyOwner {

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L241
        if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L30
        if (msg.sender != _owner) revert PendingOwnable__NotOwner();

### Github Permalink
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L84-L86
### Mitigation
Remove it or at least include a multisig method / 2 steps for not calling it accidentally.


## Incorrect shift
### Proof of concept
```
 sstore(_slotX, add(shl(_OFFSET_PROTOCOL_FEE, 1), _feesXTotal))
```
```
 sstore(_slotY, add(shl(_OFFSET_PROTOCOL_FEE, 1), _feesYTotal))
```
### Github permalink
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L758
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L773
### Tools
Slither
### Recommendation
Swap the order of parameters.




### Mitigation
Consider removing virtual for these functions.




## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L45
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L46
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L47
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L64
https://github.com/code-423n4/2022-10-traderjoe/blob/e81b78ddb7cc17f0ece921fbaef2c2521727094b/src/LBRouter.sol#L56-L60

### Mitigation
Check zero address before assigning or using it


## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L38
        if (block.timestamp > _deadline) revert LBRouter__DeadlineExceeded(_deadline, block.timestamp);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L118
        if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {

### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision

## Use of `asserts()`
### Impact

From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
You have reachable asserts in the following locations (which should be replaced by `require` / are mistakenly left from development phase):

### Details
The Solidity `assert()` function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

A bug exists in the contract that allows it to enter an invalid state;
The assert statement is used incorrectly, e.g. to validate inputs.

### References
SWC ID: 110

### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L141

### Recommended Mitigation Steps
Substitute `asserts` with `require`/`revert`.

## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/Error are being emitted if that fails, so functions would be called even of not being working as expect as for [example `_addCollateral` what would interact with the ether.]

### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L864-L905

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L134-L208

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L54-L128


https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L278-L287

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L294-L303

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L880-L901

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L616-L675

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L278-L287

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L880-L901

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L921-L935

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L466-L608

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L294-L303


### Mitigation
Check values and `revert`/`emit` events if needed

# Informational

##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks
`1e10`
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L558
            uint256 _baseFee = (uint256(_baseFactor) * _binStep) * 1e10;
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L93
            return uint256(_fp.baseFactor) * _fp.binStep * 1e10;

`1e18`
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L121
                                quote.fees[i] = (fees * 1e18) / quote.amounts[i]; // fee percentage in amountIn

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L201
                                quote.fees[i - 1] = (fees * 1e18) / quote.amounts[i - 1]; // fee percentage in amountIn

`1000` , `100`, `0.003e18`
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L564
            uint256 _maxVariableFee = (_prod * _prod * _variableFeeControl) / 100;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L85
                        (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,


https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L89
                    quote.fees[i] = 0.003e18; // 0.3%

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L163
                        (JoeLibrary.quote(quote.virtualAmountsWithoutSlippage[i], reserveOut, reserveIn) * 1000) /

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L166
                    quote.fees[i - 1] = 0.003e18; // 0.3%

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L725
                amountsIn[i - 1] = (_reserveIn * amountOut_ * 1_000) / (_reserveOut - amountOut_ * 997) + 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L791
                        amountOut = (_reserve1 * amountOut * 997) / (_reserve0 * 1_000 + amountOut * 997);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L794
                        amountOut = (_reserve0 * amountOut * 997) / (_reserve1 * 1_000 + amountOut * 997);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L891
                        uint256 _amountOut = (_reserve1 * (_balance - _reserve0) * 997) / (_balance * 1_000);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L896
                        uint256 _amountOut = (_reserve0 * (_balance - _reserve1) * 997) / (_balance * 1_000);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L107
                variableFee = (_prod * _prod * _fp.variableFeeControl) / 100;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/JoeLibrary.sol#L39
        uint256 denominator = reserveIn * 1000 + amountInWithFee;

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/JoeLibrary.sol#L51
        uint256 numerator = reserveIn * amountOut * 1000;

### Mitigation
Replace magic hardcoded numbers with declared constants.

## Missing indexed event parameters
### Summary
Events without indexed event parameters make it harder and
inefficient for off-chain tools to analyze them.
### Details
Indexed parameters (“topics”) are searchable event parameters.
They are stored separately from unindexed event parameters in an efficient manner to allow for faster access. This is useful for efficient off-chain-analysis, but it is also more costly gas-wise.
 
### Github Permalinks

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/IJoePair.sol#L59
    event Sync(uint112 reserve0, uint112 reserve1);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBFactory.sol#L34
    event FeeRecipientSet(address oldRecipient, address newRecipient);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBFactory.sol#L36
    event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBFactory.sol#L51
    event FactoryLockedStatusUpdated(bool unlocked);

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBFactory.sol#L53
    event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);

### Mitigation
Consider which event parameters could be particularly useful to off-chain tools and should be indexed.

## Naming convention of constants
### Summary
Constant naming convention is all upper case. 
### Details
Some constants are not using proper style.
Constant should be in `UPPER_CASE_WITH_UNDERSCORES` as per Solidity Style Guide. 
### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L28-L30
### Mitigation
Rename the constant to uppercase style: `CONSTANTS_WITH_UNDERSCORES`. 


## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.0. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
All files

### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting >= 0.8.0 into 0.8.7
Consider converting ^ 0.8.0 into 0.8.7

## Implementation of the comment spec confusing
### Summary
It says 
  - // Can't overflow as the updatedOracleId < oracleSize
  -  // Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
However, since pragma 0.8.0 overflow is by default controlled. Also there is not a unchecked operation there what would explain why the comment about overflow is there. 
### Github Permalinks

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L392
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L560

### Mitigation
Consider removing confusing comments and or implement an unchecked scenario for gas optimization if non overflow is expected, in that case the comment would make sense.

## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 


https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBErrors.sol#L21

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBErrors.sol#L63

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L28

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L38

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L43

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L44

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L45

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L47

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L48

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L56

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L57

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L197

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L241

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L247

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L307

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L323

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L332

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L335

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L336

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L416

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L419

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L420

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L437

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L466

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L503

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L526

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L529

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L530

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L549

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L552

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L560

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L562

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L575

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L26

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L62

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L67

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L102

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L111

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L145

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L166

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L187

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L196

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L213

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L241

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L243

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L251

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L285

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L298

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L329

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L332

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L334

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L430

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L431

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L458

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L460

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L461

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L481

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L508

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L512

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L513

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L514

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L521

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L528

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L529

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L557

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L558

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L560

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L567

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L571

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L610

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L708

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L735

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L739

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L754

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L769

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L852

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L853

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L867

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L868

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L893

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L911

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L938

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBPair.sol#L1010

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L50

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L80

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L83

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L94

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L102

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L105

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L113

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L121

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L130

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L158

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L161

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L163

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L171

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L181

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L184

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L191

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L201

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L240

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBQuoter.sol#L242

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L38

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L72

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L95

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L97

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L98

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L107

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L122

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L127

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L139

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L160

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L168

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L171

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L219

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L220

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L245

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L246

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L247

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L291

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L335

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L359

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L366

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L384

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L392

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L394

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L413

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L420

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L438

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L442

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L446

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L448

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L466

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L473

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L477

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L479

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L507

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L512

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L516

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L518

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L523

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L538

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L550

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L553

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L568

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L581

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L587

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L600

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L601

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L614

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L662

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L670

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L691

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L725

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L727

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L749

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L791

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L794

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L800

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L850

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L891

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L896

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L924

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L926

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBRouter.sol#L937

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L33

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L71

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L100

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L115

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L119

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L249

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBToken.sol#L269

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/IJoeRouter01.sol#L155

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/IJoeRouter01.sol#L157

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBFactory.sol#L18

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L32

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L33

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L148

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L150

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L212

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBPair.sol#L242

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBRouter.sol#L25

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBRouter.sol#L26

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/interfaces/ILBToken.sol#L9

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BinHelper.sol#L17

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BinHelper.sol#L34

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BinHelper.sol#L52

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BinHelper.sol#L56

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L9

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L12

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L13

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L26

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/BitMath.sol#L28

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeDistributionHelper.sol#L58

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L22

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L55

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L64

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L80

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L81

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L102

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L104

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L116

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L124

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L129

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L133

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/FeeHelper.sol#L135

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/JoeLibrary.sol#L12

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/JoeLibrary.sol#L18

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/JoeLibrary.sol#L29

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/JoeLibrary.sol#L43

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L30

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L31

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L33

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L36

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L37

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L38

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L57

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L60

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math128x128.sol#L70

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L168

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L169

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L170

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L213

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L216

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L225

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L232

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L233

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L237

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L246

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L247

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Math512Bits.sol#L248

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L31

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L57

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L69

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L70

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L71

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L74

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L83

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L85

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L99

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L105

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L116

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L134

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L135

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L136

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Oracle.sol#L137

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/PendingOwnable.sol#L36

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L15

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L16

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L17

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L49

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L53

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L56

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L58

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L77

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L111

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/Samples.sol#L112

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/SwapHelper.sol#L26

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/TreeMath.sol#L18

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/TreeMath.sol#L89

https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/libraries/TreeMath.sol#L90


### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 

## State variables that do not change should be constant and written in UPPERCASE
### Summary
`constant` keyword helps with readability of the code and to make sure that they do not change. 

### Details
Code contains state variables that do not change and so they can be declared `constant`

### Github Permalinks
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L34
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L49
https://github.com/code-423n4/2022-10-traderjoe/blob/37258d595d596c195507234f795fa34e319b0a68/src/LBFactory.sol#L39


### Mitigation
Add constant and change `case_camelcase` to `CASE_UPPERCASE`]]]
