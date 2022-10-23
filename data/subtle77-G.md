1 -
++i COSTS LESS GAS COMPARED TO i++ OR i += 1 IN FOR LOOPS 
++i costs less gas compared to i++ or for unsigned integer.
 -/blob/main/src/LBQuoter.sol

  75 for (uint256 i; i < swapLength; i++) {

100 for (uint256 j; j < LBPairsAvailable.length; j++) {

175 for (uint256 j; j < LBPairsAvailable.length; j++) {

2 -Cache storage values in memory to minimize SLOADs

The code can be optimized by minimising the number of SLOADs. SLOADs are expensive 100 gas compared to MLOADs/MSTOREs(3gas)
Storage value should get cached in memory

- /blob/main/src/LBFactory.sol

566                if (_baseFee + _maxVariableFee > MAX_FEE)
577                revert LBFactory__FeesAboveMax(_baseFee + _maxVariableFee, MAX_FEE);

Cache MAX_FEE

543           if (_binStep < MIN_BIN_STEP || _binStep > MAX_BIN_STEP)
544           revert LBFactory__BinStepRequirementsBreached(MIN_BIN_STEP, _binStep, MAX_BIN_STEP);

Cache MIN_BIN_STEP and  MAX_BIN_STEP

- blob/main/src/libraries/Math128x128.sol
58           uint256 n = (x >> LOG_SCALE_OFFSET).mostSignificantBit();
59
60           // The integer part of the logarithm as a signed 129.127-binary fixed-point number. The operation can't overflow
61           // because n is maximum 255, LOG_SCALE_OFFSET is 127 bits and sign is either 1 or -1.
62           result = int256(n) << LOG_SCALE_OFFSET;
63
64           // This is y = x * 2^(-n).
65            uint256 y = x >> n;
66
67            // If y = 1, the fractional part is zero.
68           if (y != LOG_SCALE) {
69               // Calculate the fractional part via the iterative approximation.
70               // The "delta >>= 1" part is equivalent to "delta /= 2", but shifting bits is faster.
71               for (int256 delta = int256(1 << (LOG_SCALE_OFFSET - 1)); delta > 0; delta >>= 1) {
72                   y = (y * y) >> LOG_SCALE_OFFSET;
73
74                    // Is y^2 > 2 and so in the range [2,4)?
75                    if (y >= 1 << (LOG_SCALE_OFFSET + 1)) {
76                        // Add the 2^(-m) factor to the logarithm.
77                       result += delta;
78
79                       // Corresponds to z/2 on Wikipedia.
80                       y >>= 1;
81                  }
82                }
83          }

cache LOG_SCALE_OFFSET and LOG_SCALE

3 - Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

- blob/main/src/LBPair.sol

274 for (uint256 i; i < _ids.length; ++i) {

496 for (uint256 i; i < _ids.length; ++i) {

701 for (uint256 i; i < _ids.length; ++i)

623 for (uint256 i; i < _ids.length; ++i) {

-/blob/main/src/LBRouter.sol

951        for (uint256 i; i < pairs.length; ++i) {

