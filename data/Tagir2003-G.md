# GAS REPORT

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [LBRouter.sol#L284](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L284)
- [LBQuoter.sol#L105](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L105)

## [GAS] Cache the following arrays size before the loop


### Proof of concept:
- [LBToken.sol#L89](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L89)
- [LBQuoter.sol#L176](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L176)
