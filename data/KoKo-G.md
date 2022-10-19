# GAS OPTIMIZATIONS FOR TRADER JOE V2

# FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L59 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L68

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L396 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L468 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L485 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L502 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L520

# ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154

# <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L211 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L241

# INLINE FUNCTION WHERE POSSIBLE

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L145

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L930 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L941

# ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L165 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L195

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L90 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L163

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L274 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L623 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L701 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L929

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L674 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L831 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L878 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L951

# PACK STORAGE VARIABLES IN FEWER SLOTS

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L25

# USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L161 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L191

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L82 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L160

# DO NOT USE BOOLEANS, USE UINTS INSTEAD (0 AND 1 FOR EXAMPLE)

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L39

# CONSTANTS SHOULD BE MARKED AS `IMMUTABLE` OR `CONSTANT`

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L72

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L39 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L54

# USE CALLDATA FOR FUNCTION PARAMETERS

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L55 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L91 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L100 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L116 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L124 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L142 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L157

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L79 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L152 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L153

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L54 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L134

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L26 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L49

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L74

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L32 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L33 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L82 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L83 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L84

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L207 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L230 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L280 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L314 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L355 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L380 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L409 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L410 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L434 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L435 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L462 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L463 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L495 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L534 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L565 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L597 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L645 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L656 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L704 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L745 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L765 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L766 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L817 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L818 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L819 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L820 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L865 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L866 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L867 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L941

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L261 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L467 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L468 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L469 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L617 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L618 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L688

# <X < Y + 1> is cheaper than <X <= Y>

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L118 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L160

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L165 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L549

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L58

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L68 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L200

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L61

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L278 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L517 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L636 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L643

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L75 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L83 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L87 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L91 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L95 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L99

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L49 \
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L75
