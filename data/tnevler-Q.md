# Report
## Low Risk ##

### [L-01]: Missing checks for address(0x0)
**Context:** 

1. ``` _owner = _newOwner; ``` [L93](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L93)
2. ``` _pendingOwner = pendingOwner_; ``` [L102](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L102)
3.  ``` LBPairImplementation = _LBPairImplementation; ``` [L219](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L223)

**Recommendation:**

Add non-zero address checks when set address state variables.

## Non-Critical Issues ##

### [N-01]: Function defines a named return variable but then it uses return statements  
**Context:** 

1. ``` return mostSignificantBit(x) - _shift; ``` [L47](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L47)
2. ``` return leastSignificantBit(x) + bit; ``` [L62](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L62)
3. ``` return 255 - lsb; ``` [L142](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L142)
4. ``` return invert ? type(uint256).max / result : result; ``` [L202](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L202)
5. ``` return _getEndOfDivRoundDown(x, y, denominator, prod0, prod1); ``` [L37](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L37)
6. ``` return _getEndOfDivRoundDown(x, 1 << offset, denominator, prod0, prod1); ``` L133
7. ``` return (_lookUpTimestamp, cumulativeId, cumulativeVolatilityAccumulated, cumulativeBinCrossed); ``` [L85](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L85)
8. ``` return (_sample, _sample); ``` [L173](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L173)
9. ``` return pack(_cumulativeBinCrossed, _cumulativeVolatilityAccumulated, _cumulativeId, block.timestamp, 1); ``` [L58](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol#L58)
10. ``` return _getReservesAndId(); ``` [L142](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L142)
11. ``` return _getGlobalFees(); ``` [L162](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L162)
12. ``` return _getBin(_id); ``` [L252](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L252)
13. ``` return (reserveX.safe112(), reserveY.safe112()); ``` [L1007](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1007)

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-02]: Wrong order of functions 
**Context:** 

1. [LBPair._getPendingFees](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L844), [LBPair._updateUserDebts](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L861), [LBPair._cacheFees](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L880), [LBPair._increaseOracle](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L921)  (private functions must be after all internal functions)
2. [LBRouter.getSwapIn](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L90) (public functions must be after all external functions)

**Description:**

According [official solidity documentation](https://docs.soliditylang.org/en/v0.8.6/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

**Recommendation:**

Put the functions in the correct order according to the documentation.


### [N-03]: NatSpec is missing

1. [JoeLibrary.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol) (NatSpec is missing in 4 functions)
2. [ReentrancyGuardUpgradeable.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol) (NatSpec is missing in 2 functions)
3. [TreeMath.addToTree](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L70)
4. [TreeMath.removeFromTree](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L80)
5. [LBFactory.forceDecay](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520)
6. [LBPair.forceDecay](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L792)
7. [LBRouter._getPairs](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L941)


### [N-04]: Public function can be external 
**Context:** 

1. [PendingOwnable.owner](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L47)
2. [PendingOwnable.pendingOwner](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L53)
3. [PendingOwnable.setPendingOwner](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L59)
4. [PendingOwnable.revokePendingOwner](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L68)
5. [PendingOwnable.becomeOwner](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L75)
6. [PendingOwnable.renounceOwnership](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L84)
7. [LBQuoter.findBestPathFromAmountIn](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54)
8. [LBQuoter.findBestPathFromAmountOut](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134)
9. [LBToken.name](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49)
10. [LBToken.symbol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55)
11. [LBToken.totalSupply](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L63)
12. [LBToken.balanceOfBatch](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L79)
13. [LBToken.userPositionAtIndex](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L100)
14. [LBToken.userPositionNumber](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L107)
15. [LBToken.isApprovedForAll](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L115)
16. [LBToken.setApprovalForAll](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122)
17. [LBToken.safeTransferFrom](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L131)
18. [LBToken.safeBatchTransferFrom](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L149)

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.