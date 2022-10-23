## [G-01] Magic number in operation
## code snipped
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L564
## recommend
Constant should be defined rather than using magic number.

## [G-02] Use calldata instead memory
## code snipped
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L467-L469
##recommend
In the external functions where the function argument is read-only, the function() has an inputed parameter that using memory, if this function didnt change the parameter, its cheaper to use calldata then memory. 

## [G-03] Constants.PRECISION Should be cached
## code snipped
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L506-L509
##recommend
caching Constants.PRECISION to the memory because its call multiple times. it can save more gas.