Table Of Content
================

* [QA REPORT](#qa-report)
        * [Contract should have pause/unpause functionality](#contract-should-have-pauseunpause-functionality)
        * [Array access is out of bounds](#array-access-is-out-of-bounds)
        * [Loss of precision: multiplications should be before divisions](#loss-of-precision-multiplications-should-be-before-divisions)
        * [Missing 0 address check at transfer](#missing-0-address-check-at-transfer)
        * [Missing zero address check in a state variable setter function](#missing-zero-address-check-in-a-state-variable-setter-function)
        * [Add event to the following functions](#add-event-to-the-following-functions)
        * [Some of the following function specification is missing](#some-of-the-following-function-specification-is-missing)

# QA REPORT

## Contract should have pause/unpause functionality
In case a hack is occuring or an exploit is discovered, the team (or validators in this case) should be able to pause
functionality until the necessary changes are made to the system. Additionally, the gravity.sol contract should be manged by proxy so that upgrades can be made by the validators.

Because an attack would probably span a number of blocks, a method for pausing the contract would be able to interrupt any such attack if discovered.)

### Code Instances:
- [LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol)
- [LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol)

## Array access is out of bounds
There is no check for the access to be in the array bounds.

### Code Instances:
- [LBRouter.sol#L600](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L600)
- [LBRouter.sol#L359](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L359)
- [LBRouter.sol#L538](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L538)
- [LBRouter.sol#L869](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L869)

## Loss of precision: multiplications should be before divisions
Consider changing the order of the following instances math operators such that multiplications comes before divisions to improve calculation precision with no cost.

### Code Instances:
- [LBRouter.sol#L793](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L793)
- [LBRouter.sol#L890](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L890)
- [LBRouter.sol#L895](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L895)

## Missing 0 address check at transfer
Some contracts does not support 0 transfer, then the transaction will revert with no explanation. We recommend to add a require statement that the amount is not 0.

### Code Instances:
- [LBRouter.sol#L513](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L513)
- [LBToken.sol#L208](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L208)
- [LBToken.sol#L140](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L140)
- [LBToken.sol#L245](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L245)

## Missing zero address check in a state variable setter function
A state variable of type 'address' is set without a non-zero verification. This can lead to undesired behavior.

### Code Instances:
- [LBFactory.sol#L510](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L510)
- [LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215)

## Add event to the following functions


### Code Instances:
- [LBQuoter.sol#L44](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L44)
- [LBRouter.sol#L56](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L56)

## Some of the following function specification is missing


### Code Instances:
- [LBToken.sol#L283](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L283)
- [LBToken.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L100)
- [LBQuoter.sol#L44](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L44)
- [LBRouter.sol#L72](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L72)
