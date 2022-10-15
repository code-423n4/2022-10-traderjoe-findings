1. Use != 0 instead of > 0 for uintX variable comparison

    Using `i > 0` costs more gas than `i != 0`.
    Consider replacing every instance of `i > 0` with `i != 0` for uintX variables as they do not go below 0

    Instances:
    https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L191
    https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L79
    https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L82
    https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L160    

2. Don’t calculate .length for every iteration of the loop
    
    Reading the value of memory variable costs 3 gas. If this is done for calculating the length of the array every for loop of a never changing.  
    (lengthwise) array, it will cost more gas.

     You save by not reading `array.length` for every instance of for loop. Instead just cache the value before the for loop. Ex: `uint256 length = 
     arr.length`

     Instances:
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163

3. Use <var>=<var>+<val/var> instad of <var>+=<var/val>
    
     Gas can be saved if we migrate from `<var>+=<var/val> to `<var>=<var>+<val/var> `. The gas saved can go about 35 gas
      Same goes for subtraction, multiplication and division too.

     Instances:
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L766
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L211
     https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L241
     
4. Use ++i in increasing value

    Using i++ costs 4 operations while ++i costs 2 operations, thus saving gas. Consider use ++i instead of i++. Same goes for decrement 
    operation too.
 
    Instances:
    https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75
    https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154
    
