# c4udit QA Report

## Files Analyzed

## Issued Found:

### 1. Unspecific Compiler Version Pragma

 It is recommended to specify the latest compiler version for all the solidity files.

### 2. Should use Compiler Verions > 0.8.4 to Use Custom Errors

Custom Errors can only be used if the compiler version is `0.8.4` and above. The compiler version presently specified in the smart contracts is `0.8.0`. It is recommended to use the latest compiler version(`0.8.17`) for all the smart contracts.

###  3. Redundant/Unnecessary File Imports

**Lines of Code :**

**LBPair.sol** [Line7](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L7)
LBErrors.sol is already imported by the LBToken.sol file. 

**LBPair.sol** [Line 22](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L22)
ILBPair.sol is already imported by the LBErrors.sol file. When LBErrors.sol is imported, ILBPair.sol is imported automatically.

**Math128x128.sol** [Line 6](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L6)
BitMath.sol is already imported by the Math512Bits.sol

**FeeDistributionHelper.sol** [Line7,8,10](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L7-L8)
LBErrors.sol, Constants, SafeCast.sol are already imported by the FeeHelper.sol file.

### 4. No Access Specifier Given for Some of the State Variables

**Lines of Code :**

**Math128x128.sol** [Line 17-19](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L17-L19)
It is recommended to explicitly specify the access specifier for the state variables.

