## ARRAY.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

There are 7 instances of this issue:

======================

### File: LBPair.sol

1.for (uint256 i; i < _ids.length; ++i)

There are several instances of _ids.length:

1.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274
2.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L496
3.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L623
4.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L701

Should create new state variable then use it in the for loop:

1.uint256 idsLength = _ids.length; 
for(uint256. i =0; i < idsLength; ++i)

========================

### file: LBPQuoter.sol

2.for (uint256 j; j < LBPairsAvailable.length; j++)
2.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100
   https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177

Should create new state variable then use it in the for loop:

2.uint256 LBPairsAvailableLength = LBPairsAvailable.length;
   for (uint256 j; j < LBPairsAvailableLength; j++)


=========================

### ile: LBRouter.sol

3.for (uint256 i; i < depositIds.length; ++i) {

3.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L674

Should create new state variable then use it in the for loop:

3.uint256  depositIdsLength = depositIds.length;   
   for (uint256 i; i < depositIdsLength; ++i)


4.for (uint256 i = _pairs.length; i !=  0; i--)

4.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711
 https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L778
 https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L831
 https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L878

Should create new state variable then use it in the for loop:

4.uint256 pairsLength = _pairs.length;
   for (uint256 i = pairsLength; i !=  0; i--)


5.for (uint256 i; i < pairs.length; ++i)

5.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L951

Should create new state variable then use it in the for loop:

5.uint256 \_pairsLength = \_pairs.length;
  for (uint256 i = _pairsLength; i !=  0; i--)


=======================================

### file:LBToken.sol

6.for (uint256 i; i < _accounts.length; ++i) {

6.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90


Should create new state variable then use it in the for loop:

6.uint256 accountsLength = _accounts.length;
   for (uint256 i; i < accountsLength; ++i)

7.for (uint256 i; i < _ids.length; ++i)

7.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L163

Should create new state variable then use it in the for loop:

7.uint256 idsLength = _ids.length; 
  for(uint256. i =0; i < idsLength; ++i)

==========================================

## USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

### If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

### File: LBFactory.sol

1.uint256 public constant override MAX_FEE = 0.1e18; // 10%
2.uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
3.uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
4.uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%

1.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L25
2.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L27
3.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L28
4.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L30