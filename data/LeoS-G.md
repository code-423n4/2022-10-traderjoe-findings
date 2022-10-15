## [G-01] Length of an array can be cached to save gas.
4 instances:

 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L274
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L623
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L701
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L673

Consider caching it.

With those change, these evolutions in gas average report can be observed:

    LBPair: Deployment: 4835641 -> 4837241 (+1600)
    LBPair: collectFees: 27696 -> 27689 (-7)
    LBPair: pendingFees: 12049 ->  12039 (-10)
    LBPair: burn: 111199 -> 111183 (-16)
    LBRouter: Deployment: 4591267 -> 4599875 (+8608)
    LBRouter: addLiquidity: 1271111 -> 1271093 (-18)
    LBRouter: addLiquidityAVAX: 1466315 -> 1466295 (-20)
    LBRouter: removeLiquidity: 579712 -> 579699 (-13)
    LBRouter: removeLiquidityAVAX: 976395 -> 976368 (-27)

## [G-02] Using `calldata` instead of `memory` for read only argument in external function
If a function parameter is read only, it is cheaper in gas to use `calldata` instead of `memory`.

7 instances:

 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L261
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L467-L469
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L617-L618
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L688

Consider changing `memory` to `calldata` in these lines.

With these changes, these evolutions in gas average report can be observed:

    LBPair: Deployment: 4835641 -> 4857477 (+21836)
    burn: 111199 -> 109537 (-1662)
    collectFees: 27696 -> 27076 (-620)
    mint: 1402767 -> 1399695 (-3072)
    pendingFees: 12049 -> 11401 (-648)

## [G-03] Using `storage` instead of `memory`  can be cheaper.

A `storage` structure is pre allocated by the contract, by contrast, a `memory` one is newly created. Depending on the case both can be used to optimize the gas cost because simply, a `storage` is cheaper to create but more expensive to read from and to return and a `memory` on the other hand is more expensive to create but cheaper to read from and to return. 

Following this, we can deduce 3 cases that can be swapped to optimize runtime cost and deployment cost:

 - https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L197
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L426
 - https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L850

Consider changing `memory` to `storage` in these lines

With these changes, these evolutions in gas average report can be observed:

    LBFactory: Deployment: 2086657 -> 2075843 (-10814)
    LBFactory: getAllLBPairs: 8407 -> 8152 (-255)
    LBPair: Deployment:   4835641 -> 4870495 (+34854)
    LBPair: collectFees: 27696 -> 27438 (-258)
    LBPair: flashLoan: 55799 ->  56356 (+557)
    LBPair: mint: 1402767 -> 1402079 (-688)
    LBPair: pendingFees: 12049 -> 11742 (-307)
    LBPair: safeBatchTransferFrom: 408394 -> 407957 (-437)
    LBPair: safeTransferFrom: 23071 -> 23042 (-29)

## [G-04] Some variable can be cached.

1 instances:
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBQuoter.sol#L102-L113

Consider caching `LBPairsAvailable[j]`.

With this changes, these evolutions in gas average report can be observed:

    LBQuoter: Deployment:   1899867 -> 1876038 (-23829)
    LBQuoter: findBestPathFromAmountIn: 45266 -> 45122 (-144)
