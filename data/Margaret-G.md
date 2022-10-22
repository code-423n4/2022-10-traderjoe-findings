# GAS REPORT

## [GAS 00] Caching of msg.sender is unnecessary in the following locations


### Proof of concept:
- [LBQuoter.sol#L238](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L238)
- [LBFactory.sol#L260](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L260)
- [LBRouter.sol#L414](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L414)
- [LBRouter.sol#L722](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L722)
- [LBRouter.sol#L81](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L81)
