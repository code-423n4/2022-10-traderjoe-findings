-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y); SO TRY TO AVOID +=/-= AND USE X=X+Y OR X=X-Y INSTEAD

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3E%3E%3D%2064%3B-,msb%20%2B%3D%2064%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3E%3E%3D%2032%3B-,msb%20%2B%3D%2032%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3E%3E%3D%2016%3B-,msb%20%2B%3D%2016%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3E%3E%3D%208%3B-,msb%20%2B%3D%208%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3E%3E%3D%204%3B-,msb%20%2B%3D%204%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3E%3E%3D%202%3B-,msb%20%2B%3D%202%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%201)%20%7B-,msb%20%2B%3D%201%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%3D%2064%3B-,lsb%20%2B%3D%2064%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%3D%2032%3B-,lsb%20%2B%3D%2032%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%3D%2016%3B-,lsb%20%2B%3D%2016%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%3D%208%3B-,lsb%20%2B%3D%208%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%3D%204%3B-,lsb%20%2B%3D%204%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=%3C%3C%3D%202%3B-,lsb%20%2B%3D%202%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=!%3D%200)%20%7B-,lsb%20%2B%3D%201%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#:~:text=_pairFees.protocol%20%2B%3D%20_fees.protocol%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#:~:text=to%20the%20logarithm.-,result%20%2B%3D%20delta%3B,-//%20Corresponds%20to%20z
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#:~:text=result-,%2B%3D,-prod1%20%3C%3C%20(
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#:~:text=offset)%20!%3D%200)-,result%20%2B%3D%201%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#:~:text=denominator)%20!%3D%200)-,result%20%2B%3D%201%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=pairFees.total%20%2B%3D%20fees.total%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=pairFees.protocol%20%2B%3D%20fees.protocol%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=bin.accTokenXPerShare%20%2B%3D%20fees.getTokenPerShare(totalSupply)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=bin.accTokenYPerShare%20%2B%3D%20fees.getTokenPerShare(totalSupply)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=bin.reserveY%20%2D%3D,uint136(amountOutOfBin)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=bin.reserveY%20%2B%3D%20amountInToBin%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#:~:text=bin.reserveX%20%2D%3D,uint136(amountInToBin)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint16).max%2C-,_shift%20%2B%3D%2016)%3B,-filterPeriod%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint16).max%2C-,_shift%20%2B%3D%2016)%3B,-decayPeriod%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint16).max%2C-,_shift%20%2B%3D%2016)%3B,-reductionFactor%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint16).max%2C-,_shift%20%2B%3D%2016)%3B,-variableFeeControl%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint24).max%2C-,_shift%20%2B%3D%2016)%3B,-protocolShare%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint16).max%2C-,_shift%20%2B%3D%2024)%3B,-maxVolatilityAccumulated%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=uint24).max%2C-,_shift%20%2B%3D%2016)%3B,-sampleLifetime%20%3D%20_preset
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=_getPendingFees(_bin%2C%20_account%2C%20_id%2C%20_balance)%3B-,amountX%20%2B%3D%20_amountX%3B,amountY%20%2B%3D%20_amountY%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=_bins%5B_id%5D.accTokenXPerShare%20%2B%3D,_feesY.getTokenPerShare(_totalSupply)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=_mintInfo.amountY%20%2D%3D,%2B%3D%20_fees.total%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=_pair.reserveX%20%2B%3D,(_mintInfo.amountY)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=_mintInfo.amountXAddedToPair%20%2B%3D,%2B%3D%20_mintInfo.amountY%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=(_reserveY%2C%20_totalSupply)%3B-,amountY%20%2B%3D%20_amountY%3B,_pairReserveY%20%2D%3D%20_amountY%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=(_reserveX%2C%20_totalSupply)%3B-,amountX%20%2B%3D%20_amountX%3B,_pairReserveX%20%2D%3D%20_amountX%3B,-%7D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=_pairInformation.feesX.total%20%2D%3D%20uint128(amountX)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#:~:text=feesIn%20%2B%3D%20_fee,_amountOut%20%2D%3D%20_amountOutOfBin%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#:~:text=_balances%5B_id%5D%5B_account%5D%20%3D%20_accountBalance%20%2D,_totalSupplies%5B_id%5D%20%2D%3D%20_amount%3B


-> IN GENERALLY USING ++i COST LESS GAS THAN i++ OR += 1 (the same applies to i-- or i -=1)

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#:~:text=i%20%3C%20swapLength%3B-,i%2B%2B),-%7B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#:~:text=%3E%200%3B-,i%2D%2D),-%7B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#:~:text=!%3D%200%3B-,i%2D%2D),-%7B


-> DONT USE INTS/UINTS SMALLER THAN 32BYTE
If you use elements smaller than 32 bytes, the useage of gas from your contract could be higher. Thats because the EVM operates on 32 bytes at the moment. When there is an element smaller than 32 bytes, the EVM needs to do more to reduce the size from 32 Bytes to the specific size. 

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=uint136%20reserveY%3B-,uint16,-oracleSampleLifetime%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=uint16%20oracleSampleLifetime%3B-,uint16,-oracleSize%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=uint16%20oracleSize%3B-,uint16,-oracleActiveSize%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=uint40%20oracleLastTimestamp%3B-,uint16,-oracleId%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=uint24%20activeId%2C-,uint16,-sampleLifetime%2C
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=uint256%20_integer%2C-,uint8,-_bit%2C
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#:~:text=pure%20returns%20(-,uint8,-msb)%20%7B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#:~:text=struct%20FeeParameters%20%7B-,uint16,-binStep%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#:~:text=uint16%20binStep%3B-,uint16,-baseFactor%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#:~:text=uint16%20baseFactor%3B-,uint16,-filterPeriod%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#:~:text=uint16%20filterPeriod%3B-,uint16,-decayPeriod%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#:~:text=uint16%20decayPeriod%3B-,uint16,-reductionFactor%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#:~:text=uint24%20variableFeeControl%3B-,uint16,-protocolShare%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#:~:text=((y%20%3D-,uint16,-(x))%20!%3D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SafeCast.sol#:~:text=((y%20%3D-,uint8,-(x))%20!%3D
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#:~:text=(type(-,uint16,-).max%2C%2016


->WHEN YOU USE BOOLS FOR STORAGE IT WILL COST MORE GAS AND INCURS OVERHEAD

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#:~:text=(address%20%3D%3E-,bool),-)%20private%20_spenderApprovals
