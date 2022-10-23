## `<X> = <X> + <Y>` is more efficient than `<X> += <Y>` for state variables
Using `<X> = <X> + <Y>` for state variables save more gas than `<X> += <Y>`
There are some instances for this issue:
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBToken.sol#L211
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBToken.sol#L241
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L287-L288
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L337
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L452-L453
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L539-L540
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L550-L551
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L572-L576
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L639-L641
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L646-L648
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L711-L712
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L751
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L766
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L896-L897
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L128-L129
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L174-L175


## Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table
There are some instances for this issue:
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L27
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L28
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L30

 
