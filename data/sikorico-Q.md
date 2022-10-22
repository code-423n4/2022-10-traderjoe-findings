# QA REPORT

## [QA 00] Timelock is missing for the following functions


### Proof of concept:
- [LBFactory.sol#L350](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L350)
- [LBFactory.sol#L474](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L474)
- [LBFactory.sol#L434](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L434)
- [LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215)

## [QA 01] Mismatched solidity versions
The code is compiled with more than one solidity version which can cause undesired behavior.

## [QA 02] Remove the name from the following unused function parameters


### Proof of concept:
- [LBRouter.sol#L317](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L317)
- [LBRouter.sol#L568](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L568)
- [LBRouter.sol#L538](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L538)
- [LBRouter.sol#L438](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L438)
- [LBRouter.sol#L284](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L284)

## [QA 03] Magic number, consider using named constant instead.


### Proof of concept:
- [LBFactory.sol#L402](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L402)
- [LBQuoter.sol#L161](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L161)
- [LBQuoter.sol#L83](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L83)
- [LBRouter.sol#L724](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L724)
- [LBFactory.sol#L362](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L362)
