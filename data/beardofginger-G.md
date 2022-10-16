# > 0 is less efficient than != 0 for unsigned integers
The gas cost can be reduced by using != 0 instead of > 0 in the following locations:


LBFactory.sol, Line 161:

		if (_nbPresets > 0) {

LBFactory.sol, Line 191:

		if (_nbAvailable > 0) {

LBQuoter.sol, Line 79:

		if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {

LBQuoter.sol, Line 82:

		if (reserveIn > 0 && reserveOut > 0) {

LBQuoter.sol, Line 99:

		if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {

LBQuoter.sol, Line 154:

		for (uint256 i = swapLength; i > 0; i--) {

LBQuoter.sol, Line 157:

		if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0) {

LBQuoter.sol, Line 160:

		if (reserveIn > 0 && reserveOut > quote.amounts[i]) {

LBQuoter.sol, Line 176:

		if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {

# i++ is less efficient than ++i
The gas cost can be reduced by using ++i or i += 1 instead of i++ in the following locations:

LBQuoter.sol, Line 75:

		for (uint256 i; i < swapLength; i++) {

LBQuoter.sol, Line 100:

		for (uint256 j; j < LBPairsAvailable.length; j++) {

LBQuoter.sol, Line 177:

		for (uint256 j; j < LBPairsAvailable.length; j++) {