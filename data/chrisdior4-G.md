## "\> 0" is less efficient than "!= 0" for unsigned integers

There are 9 instances of this issue:
 =================

### file: LBFactory.sol

1.if (_nbPresets > 0)
1.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L161

2.if (_nbAvailable > 0)
2.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L191

==================
### file: LBQuoter.sol

3.if (reserveIn > 0 && reserveOut > 0)
3.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L82

4.if (LBPairsAvailable.length > 0 && quote.amounts\[i\] > 0)
4.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L99

5.for (uint256 i = swapLength; i > 0; i--) {
5.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154

6.if (quote.pairs\[i - 1\] != address(0) && quote.amounts\[i\] > 0) {
6.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L157

7.if (reserveIn > 0 && reserveOut > quote.amounts\[i\]) {
7.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L160

8.if (LBPairsAvailable.length > 0 && quote.amounts\[i\] > 0)
8.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L176

9.if (quote.pairs\[i\] != address(0) && quote.amounts\[i\] > 0)
9.https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L79

===========================

## ARRAY.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

There are 3 instances of this issue:

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