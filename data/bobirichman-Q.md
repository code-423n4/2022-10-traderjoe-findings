# QA REPORT

## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215)
- [LBFactory.sol#L510](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L510)

## [LOW] Use mult before div
To improve the following calculations precision consider changing the order of the operations such that multiplications come before divisions.

### Proof of concept:
- [LBRouter.sol#L895](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L895)
- [LBRouter.sol#L793](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L793)

## [LOW] The project is compiled with different solidity versions


## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [LBToken.sol#L136](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L136)
- [LBRouter.sol#L198](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L198)

## [LOW] In the following functions consider verifying the fee parameter
Where the fee parameter validation is checking greater than 0% (which may happen by mistake) and less than 100%

### Proof of concept:
- [LBFactory.sol#L434](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L434)
- [LBFactory.sol#L542](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L542)

## [LOW] Consider replacing assert with require
Assertions are a bad practice, use require instead.

Example: [LBFactory.sol#L140](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L140)

## [LOW] Missing pause functionality


### Proof of concept:
- [LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol)
- [LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol)

## [LOW] Approve 0 first
At some tokens you can approve an amount (at USDT for instance) only after approving to 0. Consider using increase/decrease approve notation instead.

Example: [LBToken.sol#L122](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L122)

## [LOW] Add timelock for the following functions
Using a timelock in the following type of functions is common among defi protocols.

### Proof of concept:
- [LBFactory.sol#L468](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L468)
- [LBToken.sol#L122](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L122)

## [NON CRITICAL] Consider emitting an event at the following functions


### Proof of concept:
- [LBRouter.sol#L56](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L56)
- [LBQuoter.sol#L44](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L44)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [LBRouter.sol#L768](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L768)
- [LBRouter.sol#L647](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L647)

## [NON CRITICAL] Floating pragma
Floating pragma is a bad practice, since it does not guaranty the same version at future deployments.

### Proof of concept:
- [LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol)
- [LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol)

## [NON CRITICAL] Unused function parameters should have name removed
If for any reason the following unused parameters are necessary then remove their naming (since only the type matters for function signature)

### Proof of concept:
- [LBRouter.sol#L384](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L384)
- [LBRouter.sol#L538](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L538)

