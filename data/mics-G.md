Table Of Content
================

* [GAS REPORT](#gas-report)
        * [Using abiEncodePacked() is more efficient that abiEncode()](#using-abiencodepacked-is-more-efficient-that-abiencode)
        * [Caching array size](#caching-array-size)
        * [Don't cache msg.sender](#dont-cache-msgsender)
        * [Replace transferFrom(this, ...) with transfer(...)](#replace-transferfromthis--with-transfer)

# GAS REPORT

## Using abiEncodePacked() is more efficient that abiEncode()


For instance, [LBFactory.sol#L264](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L264)

## Caching array size
In the following for loops consider caching the array size instead of loading it every iteration.

### Code Instances:
- [LBToken.sol#L89](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L89)
- [LBQuoter.sol#L176](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L176)
- [LBRouter.sol#L877](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L877)
- [LBRouter.sol#L673](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L673)

## Don't cache msg.sender
reading msg.sender is 2 gas units which is less than a read of a local var + the unnecessary store operation.

### Code Instances:
- [LBRouter.sol#L238](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L238)
- [LBRouter.sol#L509](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L509)
- [LBToken.sol#L50](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L50)
- [LBRouter.sol#L252](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L252)

## Replace transferFrom(this, ...) with transfer(...)
transferFrom(this, ...) and transfer(...) has the same meaning while transfer cost less gas.

For instance, [LBRouter.sol#L647](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L647)
