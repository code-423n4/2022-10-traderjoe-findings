# QA Report
## Found [1]

### [NC-01] Consistent Underscore Notation Improves Readability

The use of underscores for readability is not consistant. For example in [L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L30) of *LBFactory.sol* `MAX_PROTOCOL_SHARE` is initialized to `2_500`; however, as noted below _ notation is not used for decimal variables > 999 (namely `1000`).

#### Findings:

*/src/LBQuoter.sol*
Line(s): [85](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L85), [163](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L163)
```solidity
85:	(quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
163:	(JoeLibrary.quote(quote.virtualAmountsWithoutSlippage[i], reserveOut, reserveIn) * 1000) 
```

*/src/libraries/JoeLibrary.sol*
Line(s): [39](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L39), [51](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L51)
```solidity
39:	uint256 denominator = reserveIn * 1000 + amountInWithFee;
51:	uint256 numerator = reserveIn * amountOut * 1000;
```