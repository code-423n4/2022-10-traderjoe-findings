## 1) Use `require` statement instead of `assert` to save gas

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141

## 2) Avoid redundant return name variable

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L151-L163
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L466-L608
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L994-L1008

## 3) Multiple mappings with same key can be combined to, a mapping to struct, to save gas while accessing their values

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L18 with https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L24

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L21 with https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L27

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L64 with https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L66

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L68 with 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L70

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L45 with https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L58

## 4) Use `unchecked` to save gas by avoiding implicit overflow/underflow checks

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L130

## 5) Use `calldata` instead  of `memory` for read-only arguments for external functions to save gas

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L210](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L210)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280-L281](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280-L281)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313-L314](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L313-L314)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L355-L356](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L355-L356)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495-L496](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L495-L496)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L596-L597](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L596-L597)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L702-L704](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L702-L704)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L617-L618](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L617-L618)
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L688](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L688)

## 6) Cache array/mapping values that are used multiple times, to save gas

[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L43](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L43) -  `_pairBinSteps.length`
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230-L259](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230-L259) - `_liquidityParameters.tokenX`, `_liquidityParameters.tokenY`, `_liquidityParameters.amountX`, `_liquidityParameters.amountY`
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L385-L386](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L385-L386) - `_pairBinSteps.length`
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L65](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L65) - `_route.length`
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L151](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L151) - `type(uint16).max`
[https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L297-L303](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L297-L303) - `type(uint16).max`

## 7) Emitting storage variable can cost more than emitting memory variable 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L291