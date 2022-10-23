# i++ is less efficient than ++i
The gas cost can be reduced by using ++i or i += 1 instead of i++ in the following locations:

LBQuoter.sol, Line 75:

		for (uint256 i; i < swapLength; i++) {

LBQuoter.sol, Line 100:

		for (uint256 j; j < LBPairsAvailable.length; j++) {

LBQuoter.sol, Line 177:

		for (uint256 j; j < LBPairsAvailable.length; j++) {