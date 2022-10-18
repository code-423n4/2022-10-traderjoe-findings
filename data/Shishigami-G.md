# **Gas optimization report**


## [G-01] Usage of uint256 instead of boolean state variable in storage and/or save a slot
You can save one slot by packing variable `creationUnlocked` with address state variables `LBPairImplementation` (L32) or `feeRecipient` (L34).

Or (resp and) you can change boolean state variable into a uint256 (resp uint64) to save gas from `Gwarmaccess` (100 gas) for the extra `SLOAD` plus it avoids `Gsset` (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

- [LBFactory.sol#32](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L32)
- [LBFactory.sol#34](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L34)
- [LBFactory.sol#39](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L39)



## [G-02] Set to `payable` permissionened functions that are guaranteed to revert for normal users
For example, this change only on function sweep in contract LBRouter.sol saves 2609 gas on deployement cost of the contract LBRouter and on average 22gas in function sweep usage.

I. Case for modifier `onlyFactoryOwner`


1. Function sweep in LBRouter.sol and change in interface ILBRouter.sol
  - [LBRouter.sol#626](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L626)
  - [ILBRouter.sol#189](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L189)


2. Function sweepLBToken in LBRouter.sol and change in interface ILBRouter.sol
  - [LBRouter.sol#647](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L647)
  - [ILBRouter.sol#196](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBRouter.sol#L196)


II. Case for modifier `onlyFactory`


1. Function setFeesParameters in LBRPair.sol and change in interface ILBRouter.sol
  - [LBRPair.sol#788](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L788)
  - [ILBPair.sol#246](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L246)


2. Function forceDecay in LBRPair.sol and change in interface ILBRouter.sol
  - [LBRPair.sol#792](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L792)
  - [ILBPair.sol#246](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L248)


## [G-03] Postfixed index in loops are less gas efficient (i++/j++ and i--/j--) compared to prefixed index in loops
Saves 5 gas for each loop using postfixed indexes.

1. LBQuoter.sol
  - [LBQuoter.sol#75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)
  - [LBQuoter.sol#100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)
  - [LBQuoter.sol#154](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154)
  - [LBQuoter.sol#177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)


2. LBRouter.sol
  - [LBRouter.sol#711](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711)
