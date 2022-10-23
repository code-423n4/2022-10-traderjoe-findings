# QA Report for Trader Joe v2 contest

## Overview
During the audit, 7 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | [Order of Functions](#nc-1-order-of-functions) | Non-Critical | 8
NC-2 | [Order of Layout](#nc-2-order-of-layout) | Non-Critical | 2
NC-3 | [Inconsistent comment location](#nc-3-inconsistent-comment-location) | Non-Critical | 10
NC-4 | [Inconsistency when using the number 1000](#nc-4-inconsistency-when-using-the-number-1000) | Non-Critical | 
NC-5 | [Public functions can be external](#nc-5-public-functions-can-be-external) | Non-Critical | 18
NC-6 | [Missing NatSpec](#nc-6-missing-natspec) | Non-Critical | 11
NC-7 | [Unused named return variables](#nc-7-unused-named-return-variables) | Non-Critical | 4

## Non-Critical Risk Findings (7)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private

##### Instances
1) [internal function between external](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L510)
2) [internal function between private](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L905) and not in [the ```/** Internal Functions **/```](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L801)
3) internal functions after private and not in [the ```/** Internal Functions **/```](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L801):
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L943
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L969
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1016
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1044

4) [public function between external](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L90)

##### Recommendation
Reorder functions where possible.

#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

##### Instances
Modifier after functions:
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol#L42

Modifier before variables:
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L43

##### Recommendation
Place modifiers before constructor.

#
### NC-3. Inconsistent comment location
##### Description
Some comments are above the line of code and some next to it.  
For example:
- [Link1:](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L708-L709)
```
        // Avoid doing -1, as `_pairs.length == _pairBinSteps.length-1`
        amountsIn[_pairs.length] = _amountOut;
```
and
- [Link2:](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L98)
```
revert LBRouter__WrongAmounts(_amountOut, _swapForY ? _pairReserveY : _pairReserveX); // If this is wrong, then we're sure the amounts sent are wrong
```
Most of the comments are above the code, so below are the cases where they are next to it.

##### Instances
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L128
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L130
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L69
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L70
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L71
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L377
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L121
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L201
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L98
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L139

##### Recommendation
Use consistent comment location.

#
### NC-4. Inconsistency when using the number 1000
##### Description
In some cases, ```1000``` is used, and in some - ```1_000```.

##### Instances
For example:
[Link1](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L85):
```
(quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
```
[Link2](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L725):
```
amountsIn[i - 1] = (_reserveIn * amountOut_ * 1_000) / (_reserveOut - amountOut_ * 997) + 1;
```

##### Recommendation
Stick to one style.

#
### NC-5. Public functions can be external
##### Description
If functions are not called by the contract where they are defined, they can be declared external

##### Instances
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L47
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L53
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L59
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L68
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L75
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L84
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L63
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L79
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L100
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L107
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L115
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L131
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L149

##### Recommendation
Make public functions external, where possible.

#
### NC-6. Missing NatSpec
##### Description
NatSpec is missing for 11 functions in 6 contracts.

##### Instances 
- [all 4 functions in JoeLibrary.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol)
- [all 2 functions in ReentrancyGuardUpgradeable.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol)
- [1 function in TreeMath.sol#L70](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L70)
- [1 function in TreeMath.sol#L80](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L80)
- [1 function in LBFactory.sol#L520](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520)
- [1 function in LBPair.sol#L792](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L792)
- [1 function in LBRouter.sol#L941](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L941)

##### Recommendation
Add NatSpec for all functions.

#
### NC-7. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.

##### Instances
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L142
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L162
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L252
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1007

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```