# Gas Optimizations Report for Trader Joe v2 contest

## Overview
During the audit, 2 gas issues were found.  
Total savings ~3535.

№ | Title | Instance Count
--- | --- | --- 
G-1 | [Use unchecked blocks for incrementing i](#g-1-use-unchecked-blocks-for-incrementing-i) | 5
G-2 | [Use ```calldata``` instead of ```memory for``` read-only arguments](#g-2-use-calldata-instead-of-memory-for-read-only-arguments) | 56

## Gas Optimizations Findings (2)
### G-1. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.

##### Instances
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L711

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*5 = 175

#
### G-2. Use ```calldata``` instead of ```memory for``` read-only arguments
##### Description
Since Solidity v0.6.9, memory and calldata are allowed in all functions regardless of their visibility type (See "Calldata Variables" section [here](https://blog.soliditylang.org/2020/06/05/Solidity-069-release-announcement/)).  
When function arguments should not be modified, it is cheaper to use calldata.

##### Instances 
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L49
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L91
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L100
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L116
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L124
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L133
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L142
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L157
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L32
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L84
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L467
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L468
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L469
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L618
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L688
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L881
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L281
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L314
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L355
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L380
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L409
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L434
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L462
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L534
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L564
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L596
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L645
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L646
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L656
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L702
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L703
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L704
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L745
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L746
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L764
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L765
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L766
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L817
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L818
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L819
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L820
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L865
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L866
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L867
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L941 (*2)
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L79 (*2)
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L152
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L153

##### Recommendation
Use calldata where possible.

##### Saved
This saves at least 60 gas per iteration.  
So, ~60*56 = 3360