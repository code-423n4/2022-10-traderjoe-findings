# C4udit QA Report

## Files Analyzed

All files specified in scope.

## Summary

The overall code is well-commented. The unit tests are provided. The logic is split into the corresponding files. The logic is clear when referring to the docs/information given in the code4rena contest page. 
However, the conflicting file imports and the outdated compiler versions resulted in the code getting unnecessarily complicated. There were many errors which did cause confusion at first, however after trying out ways to get the code to compile and generate tests, there was a way that worked to mitigate the file imports. This has been described in the "Conflicting file imports" section below.

## Issues Found:

### 1. Unspecific Compiler Version Pragma

 It is recommended to specify the latest compiler version for all the solidity files.

### 2. Should use Compiler Verions > 0.8.4 to Use Custom Errors

Custom Errors can only be used if the compiler version is `0.8.4` and above. The compiler version presently specified in the smart contracts is `0.8.0`. ALthough it is recommended to use the latest compiler version(`0.8.17`) for all the smart contracts, however the latest compiler version was not compatible in running the tests for all the files in scope. Hence `pragma solidity ^0.8.7` had to be used for all the files in scope so that the tests would run without any problems.

###  3. Redundant/Unnecessary File Imports

**Lines of Code :**

**LBPair.sol** [Line7](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L7)
LBErrors.sol is already imported by the LBToken.sol file. 

**LBPair.sol** [Line9-10](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L9-L10), [Line12-13](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L12-L13), [Line17](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L17)
These files are already imported by SwapHelper.sol

**LBPair.sol** [Line 22](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L22)
The ILBPair interface will be present in the same file as ILBFactory.sol(reason given in the 5th section below). Importing ILBFactory.sol will be sufficient.

**Math128x128.sol** [Line 6](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L6)
BitMath.sol is already imported by the Math512Bits.sol

**FeeDistributionHelper.sol** [Line7,8,10](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L7-L8)
LBErrors.sol, Constants, SafeCast.sol are already imported by the FeeHelper.sol file.

**SwapHelper.sol** [Line 6](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L6) [Line10](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/SwapHelper.sol#L9-10)
Constants.sol and SafeMath.sol are already imported by the FeeHelper.sol file. Math512Bits.sol is already imported by BinHelper.sol

### 4. No Access Specifier Given for Some of the State Variables

**Lines of Code :**

**Math128x128.sol** [Line 17-19](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L17-L19)
It is recommended to explicitly specify the access specifier for the state variables. The access specifier for these three variables can be `private`

### 5. Conflicting File Imports

**Lines of Code :**

**ILBPair.sol** [Line 8](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L8)
There is a conflicting file import here. The ILBFactory.sol ([Line 7](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L7)) imports the ILBPair.sol file. And in turn, ILBPair.sol also imports the ILBFactory.sol file. This causes a conflicting import which causes other errors and complications as well. 

**Mitigation steps for this conflicting file import:**

- Shift all the code from `ILBPair.sol` to the `ILBFactory.sol` file. Use both the ILBPair and ILBFactory interfaces in the ILBFactory.sol file itself to avoid the conflicting import. Also import the `FeeHelper` and `IERC20` in the ILBFactory.sol file so that there are no errors in using the ILBPair interface.

- In the LBErrors.sol file, change `import "./interfaces/ILBPair.sol"` to `import "openzeppelin/token/ERC20/IERC20.sol";`

- Remove the `override` keyword in the [LBPair.sol #L50](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L50) for the immutable `factory` variable.
This sequence of steps followed resulted in smooth testing without any errors. 

Trying out any other way to solve this comflicting import caused errors like the factory function not being recognised in [LBFactory.sol #L216](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L216)
