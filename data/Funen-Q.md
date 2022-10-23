1. `TokenY` needed check validation if as initialized 

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L112

the same as `TokenX`, `TokenY` needed to check if was initialized too if the address was not zero.

```
if (address(_tokenX) == address(0) || address(_tokenY) == address(0)) revert LBPair__AlreadyInitialized();
```

2. Wrong Naming File 

File : 

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Samples.sol

the titles it said that [HERE](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L8):

```
/// @title Liquidity Book Sample Helper Library
```
so i dont know it was intendeed or was minor miss but is the name should be SampleHelper.sol or you can remove helper in the title information

3. Missing Indexed 

File :
 
1.) https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFlashLoanCallback.sol#L9-L17

2.) https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L34


4. Locked Pragma Compiler 

Since some of the contracts it was used ^0.8.0. As the compiler can be use for example 0.8.16 and consider locking at this version the same as another. It can be consider using  locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. Since it can be problematic, if there are publicly disclosed bugs and issues that affect the current compiler version used.

Locked pragma can be seen from [HERE](https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-versions-of-solidity) 

5. Consistency of spacing in Natspec

Files :

1.) https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L26

need deleting one spacing

6. Missing Natspec 

Files : 

1.) https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L792-L799

missing @notice

2.) https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L20-L21

missing @accTokenXPerShare & @accTokenYPerShare

7. Wrong Natspec 

File :

1.) https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TokenHelper.sol#L7

```
/// @title Safe Transfer
```

the title it should `Token Helper`  than `Safe Transfer` 




