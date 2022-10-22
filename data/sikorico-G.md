# GAS REPORT

## [GAS 00] transferFrom(address(this), to, amount) can be changed to transfer(to, amount) to save gas


### Proof of concept:
- [LBRouter.sol#L647](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L647)

## [GAS 01] Cache the array size for the following loops over array


### Proof of concept:
- [LBRouter.sol#L877](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L877)
- [LBRouter.sol#L830](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L830)
- [LBRouter.sol#L777](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L777)
- [LBRouter.sol#L710](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L710)
- [LBRouter.sol#L950](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L950)
