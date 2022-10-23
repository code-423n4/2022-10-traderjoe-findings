## USING PRIVATE INSTEAD OF PUBLIC FOR CONSTANTS SAVES GAS
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30

## USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.2 to get compiler automatic inlining

Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads

Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value


## UINT256 IS MORE EFFICIENT THAN BOOL
bool is more expensive uint256 so instead of using bool for true false you can use uint(0) for false and uint256(1) for true
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L39

## STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE
Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L66

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L149

## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L317
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L340-L350
## USE UNCHECKED {} FOR CALCULATIONS THAT CANNOT OVERFLOW
AS COMMENT SAYS:
 // Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
  // It returns 18 decimals as:
  // decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
SO THIS SHOULD BE WRITTEN IN UNCHECK BLOCK:
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L563-L564

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
Caching the length saves gas so it dosen't  have to lookup in every iteration
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711

## USING` --I` INSTEAD OF `I--` SAVES GAS
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711

## USING UNCHECK BLOCK FOR `I--/I++` SAVES GAS
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711

## USING BYTES32 INSTEAD OF STRING SAVES GAS
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29-L30

## REMOVING EMPTY FUNCTION SAVE GAS
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L321-L326



