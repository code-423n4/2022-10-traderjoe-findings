# GAS REPORT

## [GAS] Use abiEncodePacked()


Example: [LBFactory.sol#L264](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L264)

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L215)
- [LBFactory.sol#L520](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L520)

## [GAS] Do not cache msg.sender since loading msg.sender is more efficient than a local variable


### Proof of concept:
- [LBFactory.sol#L292](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L292)
- [LBFactory.sol#L189](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L189)

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [LBToken.sol#L283](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L283)
- [LBRouter.sol#L96](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L96)
