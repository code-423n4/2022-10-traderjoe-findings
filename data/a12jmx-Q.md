1.

Grammer issues in comments in:

1.1

Contract: ILBFactory.sol

1.1.1

	line 18

	"routes" should be "route"
	
Modified Comment:
	
	/// - ignoredForRouting: Whether the pair is ignored for routing or not. An ignored pair will not be explored during route finding
	
1.2	
	
Contract: ILBFlashLoanCallback.sol

1.2.1

	line 7

	"flashloans" should be two words "flash loans"
	
Modified Comment:
	
	/// @notice Required interface to interact with LB flash loans
	
1.3	
	
Contract: ILBPair.sol

1.3.1

	line 31

	"increase" should be "increased"
	
Modified Comment:
	
	/// - oracleSize: The current size of the oracle, can be increased by users

1.3.2

	line 68

	"informations" should be "information"
	
Modified Comment:
	
	/// @dev Structure to minting information:

1.3.3

	line 71 & 72

	"have" should be "has"
	
Modified Comments:
	
	/// - amountXAddedToPair: The amount of token X that has been actually added to the pair
	
	/// - amountYAddedToPair: The amount of token Y that has been actually added to the pair
	
1.4	
	
Contract: ILBRouter.sol

1.4.1

	line 23

	"id" should be "ids"
	
Modified Comment:
	
	/// - idSlippage: The number of ids that are allowed to slip
	
1.5	
	
Contract: BitMath.sol

1.5.1

	line 9

	"is" should be "are"

Modified Comment:
	
	/// @notice Returns the closest non-zero bit of `integer` to the right (of left) of the `bit` bits that are not `bit`

1.5.2

	line 12

	"in" should be "on"

Modified Comment:
	
	/// @param _rightSide Whether we're searching on the right side of the tree (true) or the left side (false)

1.5.3

	line 34, 37, 51, & 54

	"non null" should be hyphened to "non-null"

Modified Comments:
	
	/// @notice Returns the index of the closest bit on the right of x that is non-null
	
	/// @return id The index of the closest non-null bit on the right of x.
	
	/// @notice Returns the index of the closest bit on the left of x that is non-null
	
	/// @return id The index of the closest non-null bit on the left of x.
	
1.6
	
Contract: FeeHelper.sol

1.6.1

	line 19

	"stays" should be "stay"
	
Modified Comment:
	
	/// - filterPeriod: The filter period, where the fees stay constant

1.6.2

	line 44

	"distributes" should be "distribute"

Modified Comment:
	
	/// @dev Structure used during swaps to distribute the fees:

1.6.3

	line 114

	"token" should be "tokens"

Modified Comment:
	
	/// @param _amount The amount of tokens sent

1.6.4

	line 129

	"an" should be "a"

Modified Comment:
	
	/// @notice Return the fees added when a user adds liquidity and change the ratio in the active bin
	
1.7	
	
Contract: JoeLibrary.sol

1.7.1

	line 9

	"V1 related" should be hyphened to "V1-related"

Modified Comment:
	
	/// @notice Helper contract used for Joe V1-related calculations
	
1.8	
	
Contract: Math128x128.sol

1.8.1

	line 36

	"a" should be "an"

Modified Comment:
	
	// Convert x to an unsigned 129.127-binary fixed-point number to optimize the multiplication.

1.8.2

	line 37

	there should be a comma after the second bits

	after "y**2" there are 2 "would" words, the latter of the 2 should be removed for the sentence to make sense

Modified Comment:
	
	// If we use an offset of 128 bits, y would need 129 bits, and y**2 would overflow and we would have to

1.8.3

	line 61

	before "maximum" there should be an "a"

	before "sign" there should be a "the"

Modified Comment:
	
	// because n is a maximum 255, LOG_SCALE_OFFSET is 127 bits and the sign is either 1 or -1.
	
1.9	
	
Contract: Math512Bits.sol

1.9.1

	line 116 & 149

	"bit" should be "bits"

Modified Comments:
	
	/// @param offset The number of bits to shift x as an uint256
	
	the comment above is the same for all 2 lines

1.9.2

	line 170

	"512 bit" should be hyphened to "512-bit"
	
Modified Comment:
	
	// use the Chinese Remainder Theorem to reconstruct the 512-bit result. The result is stored in two 256

1.9.3

	line 208

	"256 bit" and "512 bit" should be hyphened to "256-bit" and "512-bit"

Modified Comment:
	
	// Subtract 256-bit number from 512-bit number.

1.9.4

	line 213

	"divisor" should be "divisors"

Modified Comment:
	
	// Factor powers of two out of denominator and compute largest power of two divisors of denominator. Always >= 1
	
1.10
	
Contract: Oracle.sol

1.10.1

	line 105

	the "it" before "is" is unnecessary

Modified Comment:
	
	/// @return updatedIndex The oracle updated index, is either the same as before, or the next one

1.10.2

	line 136

	"aren't" should be "isn't"

	"samples that are initialized" can be refactored to "initialized samples" for better reading

Modified Comment:
	
	/// The sample that isn't initialized yet will be skipped as _activeSize only contains the initialized samples.

1.10.3

	line 138

	"needs" should be "need"

Modified Comment:
	
	/// The edge cases need to be handled before
	
1.11	
	
Contract: PendingOwnable.sol

1.11.1

	line 15

	"comes" should be "come"
	
Modified Comment:	

	/// owner of that contract. The main logic and comments come from OpenZeppelin's
	
1.12	
	
Contract: ReentrancyGuardUpgradeable.sol

1.12.1

	line 12

	"write" should be "writes"

Modified Comment:
	
	// word because each writes operation emits an extra SLOAD to first read the

1.12.2

	line 17

	"value" should be "values"

Modified Comment:
	
	// The value being non-zero value makes deployment a bit more expensive,

1.12.3

	line 19

	"to" should be "at"

Modified Comment:
	
	// amount. Since refunds are capped at a percentage of the total

1.13
	
Contract: Samples.sol

1.13.1

	line 49

	"elapsed" should be "elapse"

Modified Comment:
	
	// It would be an issue if 2 overflows would happen but way too much time should elapse for it to happen.

1.14
	
Contract: SwapHelper.sol

1.14.1

	line 15

	there should be a comma after "fees"

Modified Comment:
	
	/// @notice Helper contract used for calculating swaps, fees, and reserves changes

1.14.2

	line 78

	"amounts" should be "amount"

Modified Comment:
	
	/// @param fees The fees amount added to the pairFees
	
1.15	
	
Contract: TreeMath.sol

1.15.1

	line 14, 19, & 20

	"non zero" should be hyphened into "non-zero"

Modified Comments:
	
	/// @notice Returns the first id that is non-zero, corresponding to a bin with
	
	/// for the closest non-zero bit on the right or the left
	
	/// @return The closest non-zero bit on the right (or left) side of the tree

1.15.2

	line 18

	"in" should be "on"
	
Modified Comment:	
	
	/// @param _rightSide Whether we're searching on the right side of the tree (true) or the left side (false)
	
1.16	
	
Contract: LBFactory.sol

1.16.1

	line 18

	"flashloan" should be 2 words "flash loan"

	there should be a comma after "and"

Modified Comment:

	/// Enables setting fee parameters, flash loan fees, and LBPair implementation.

1.16.2

	line 38

	there should be a "the" in front of "owner"

Modified Comment:

	/// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by the owner (false)

1.16.3

	line 51

	"presets" should be "preset"

Modified Comment:

	// The parameters preset

1.16.4

	line 62 & 473

	there should be a "the" in front of "flash"

Modified Comments:

	/// @param _flashLoanFee The value of the fee for the flash loan
	
	the comment above is the same for all 2 lines
    
1.16.5    
    
	line 310

	there should be a "the" in front of "basis"

Modified Comment:

	/// @param _binStep The bin step in the basis point of the pair

1.16.6

	line 333, 417, & 527

	"prevent" should be "prevents"

	if the above is followed the comma after "untouched" is unnecessary

Modified Comments:

	/// @param _filterPeriod The period where the accumulator value is untouched prevents spam
	
	the comment above is the same for all 3 lines
	
1.17	
	
Contract: LBPair.sol

1.17.1

	line 65

	"non zero" should be hyphened "non-zero"

Modified Comment:

	/// @dev Tree to find bins with non-zero liquidity

1.17.2

	line 96

	"needs" should be "need"

Modified Comment:

	/// @dev The different parameters need to be validated very cautiously.

1.17.3

	line 145

	there should be a comma after "fees"

Modified Comment:

	/// @notice View function to get the global fees information, the total fees, and those for protocol

1.17.4

	line 242

	"non empty" should be hyphened "non-empty"

Modified Comment:

	/// @return The id of the non-empty bin

1.17.5

	line 256

	"increasing" should be "increased"

Modified Comment:

	/// @dev The array must be strictly increased to ensure uniqueness

1.17.6

	line 298. 458, & 610

	"low level" should be "low-level"

	"which" should be "that"

Modified Comments:

	/// @notice Performs a low-level swap, this needs to be called from a contract that performs important safety checks

	/// @notice Performs a low-level add, this needs to be called from a contract that performs important safety checks.

	/// @notice Performs a low-level remove, this needs to be called from a contract that performs important safety checks

1.17.7

	line 611

	"want" should be "wants"

Modified Comment:

	/// @param _ids The ids the user wants to remove its liquidity

1.17.8

	line 612

	"token" should be "tokens"

Modified Comment:

	/// @param _amounts The amount of tokens to burn

1.17.9

	line 683

	"an" should be "a"

Modified Comment:

	/// @notice Collect fees of a user

1.17.10

	line 803

	there should be a comma after "mint"

Modified Comment:

	/// @notice Collect and update fees before any token transfer, mint, or burn

1.17.11

	line 997

	"low level" should be hyphened "low-level"

Modified Comment:

	// low-level read of mapping to only load 1 storage slot

1.17.12

	line 1010

	there should be a comma after "fees"

Modified Comment:

	/// @notice Internal view function to get the global fees information, the total fees, and those for protocol
1.17.13

	line 1040 & 1041

	"flashloan" should be "flash loan"

Modified Comments:

	/// @notice Internal pure function to return the flash loan fee amount

	/// @param _amount The amount to flash loan
	
1.18	
	
Contract: LBQuoter.sol

1.18.1

	line 16

	there should be a "the" in front of "best"

Modified Comment:

	/// @notice Helper contract to determine the best path through multiple markets

1.18.2

	line 112 & 190

	there should be a "the" in front of "current"

Modified Comments:

	// Getting the current price

	the comment above is the same for all 2 lines

1.18.3

	line 229

	"considred" should be "considered"

Modified Comment:

	/// @param _activeId Current active Id of the considered pair
	
1.19	
	
Contract: LBRouter.sol

1.19.1

	line 86, 144, 146, 298, 345, 346, 370, 401, 424, 524, 525, 554, 588, & 756

	"token" should be "tokens"

Modified Comments:

	/// @param _amountOut The amount of tokens to receive

	/// @param _amountIn The amount of tokens sent

	/// @return amountOut The amount of tokens received if _amountIn tokenX are sent

	/// @param _amountIn The amount of tokens to send

	/// @param _amountOutMin The min amount of tokens to receive

	/// @param _amountIn The amount of tokens to send

	/// @param _amountOutMin The min amount of tokens to receive

	/// @param _amountOut The amount of tokens to receive

	/// @param _amountIn The amount of tokens to send

	/// @param _amountOutMin The min amount of tokens to receive

	/// @param _amountIn The amount of tokens to send

	/// @param _amountOutMin The min amount of tokens to receive

	/// @param _amountIn The amount of tokens sent

1.19.2

	line 298

	there should be a "the" in front of "token"

Modified Comment:

	/// @param _token The address of the token

1.19.3

	line 203, 226, 262, 295, 297, 523, 587, 859

	there should be an "a" in front of "fee"

Modified Comments:

	/// @dev This function is compliant with a fee on transfer tokens

	/// @dev This function is compliant with a fee on transfer tokens

	/// @dev This function is compliant with a fee on transfer tokens

	/// @dev This function is **NOT** compliant with a fee on transfer tokens.

	/// use the `removeLiquidity` function to remove liquidity with a fee on transfer tokens.

	/// @notice Swaps exact tokens for tokens while performing safety checks supporting for a fee on transfer tokens

	/// @notice Swaps exact AVAX for tokens while performing safety checks supporting for a fee on transfer tokens

	/// @notice Helper function to swap exact tokens supporting for a fee on transfer tokens

1.19.4

	line 296

	"pays" should be "pay"

Modified Comment:

	/// This is wanted as it would make users pay the fee on transfer twice,
	
1.20	
	
Contract: LBToken.sol

1.20.1

	line 13

	consider rephrasing "to create" to "creating"

Modified Comment:

	/// It allows creating multi-ERC20 represented by their ids

1.20.2

	line 26

	there should be a "the" in front of "user"

	"have" should be "has"

Modified Comment:

	/// @dev  Mapping from account to set of ids, where the user currently has a non-zero balance

1.20.3

	line 59

	"token" should be "tokens"

Modified Comment:

	/// @notice Returns the total supply of tokens of type `id`

1.20.4

	line 273 & 289

	"an" should be "a"

Modified Comments:

	/// @notice Internal function to add an id to a user's set
 
	/// @notice Internal function to remove an id from a user's set

1.20.5

	line 311
	
	there is an unnecessary extra space between "be" and "transferred"

Modified Comment:

	/// of token type `id` will be transferred to `to`.
	
2. 

Comments overflow in the GitHub repository which interferes with the cleanliness of code and makes readability harder:
As a best practice, if there is a high probability of comment overflow in the GitHub repository the respective comment/s
should be above the code it gives the description/insight

2.1

Contract: Oracle.sol

2.1.1

	line 69 & 70
	
Modified Code/Comment Layout:

	// _next.timestamp() - _sample.timestamp() - (_lookUpTimestamp - _sample.timestamp())
	uint256 _weightPrev = _next.timestamp() - _lookUpTimestamp; 
	// _next.timestamp() - _sample.timestamp() - (_next.timestamp() - _lookUpTimestamp)
	uint256 _weightNext = _lookUpTimestamp - _sample.timestamp(); 

	
2.2	
	
Contract: LBRouter.sol

2.2.1

	line 98
	
Modified Code/Comment Layout:

	// If this is wrong, then we're sure the amounts sent are wrong
	revert LBRouter__WrongAmounts(_amountOut, _swapForY ? _pairReserveY : _pairReserveX); 