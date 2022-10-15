Gas optimisation :-

1.Try to cache this constant variables into memory it saves lot of gas :-
in https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol
Line 29 to 30 

 code snippet :-

    string private constant _name = "Liquidity Book Token";
    string private constant _symbol = "LBT";
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------2. <array>.length should not be looked up in every loop of a for-loop

LPToken.sol 
Line 90
 for (uint256 i; i < _accounts.length; ++i) {

Line 163 
for (uint256 i; i < _ids.length; ++i) 

In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.
Here, consider storing the array’s length in a variable before the for-loop, and use this new variable instead: