# QA Report (low / non-critical) for Trader Joe v2

# PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L49 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L63 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L100 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L107 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L149 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L54 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L134 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L47 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L53 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L68 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L80 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L83 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L84 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L38 

# UNUSED IMPORT

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol#L5 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L5 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L7 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L8 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L7 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L7 

# NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Encoder.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IPendingOwnable.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeMath.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Buffer.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Decoder.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L3 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L3 

# EVENT IS MISSING `INDEXED` FIELDS

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol#L148 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol#L150 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBToken.sol#L19 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L9 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L49 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L50 

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L8 

