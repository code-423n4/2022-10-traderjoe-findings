1.

Changing i++ , j++ , and i-- to ++i , ++j , and --i respectively in the 
for loops will save around 5 gas per iteration

1.1

Contract; LBQuoter.sol 

1.1.1

	line 75
	
Modified Code:

	for (uint256 i; i < swapLength; ++i) {

1.1.2	
	
	line 100
	
Modified Code:

	for (uint256 j; j < LBPairsAvailable.length; ++j) {
	
1.1.3	
	
	line 154
	
Modified Code:

	for (uint256 i = swapLength; i > 0; --i) {
	
1.1.4	
	
	line 177
	
Modified Code:

	for (uint256 j; j < LBPairsAvailable.length; ++j) {

1.2
		   
Contract: LBRouter.sol 

1.2.1

	line 711

Modified Code:

	for (uint256 i = _pairs.length; i != 0; --i) {
	
this will also provide consistency with the for loops of 

Contract: LBFactory.sol

	line 165
	line 195
	
Contract; LBPair.sol

	line 274
	line 496
	line 623
	line 701

Contract: LBRouter.sol

	line 674
	line 778
	line 831
	line 878
	line 951

Contract: LBToken.sol

	line 90
	line 163