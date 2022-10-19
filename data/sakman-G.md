## 1. Use `constant` and `immutable` for constants

_src/LBPair.sol:_ [L72](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L72)

_src/LBFactory.sol:_ [L39](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L39)
[L54](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L54)

## 2. Prefix incrementing and decrementing costs around 6 gas less than the postfix ones

### e.g. ++var is cheaper than var++

_src/LBQuoter.sol:_ [L75](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75)
[L100](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100)
[L154](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154)

_src/LBRouter.sol:_ [L711](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711)

## 3. Function that are called only once can be inlined in the calling function

### This change will save around 30 gas units

_src/LBRouter.sol:_ [L930](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L930)
[L941](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L941)

_src/libraries/Oracle.sol:_ [L145](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L145)

## 4. `public` storage `constant`(and `immutable`) variable should be `private`

### saves tons of gas on deployment

_src/LBFactory.sol:_ [L27](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L27)
[L28](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L28)
[L30](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L30)

## 5. Use `x < y + 1` in stead of `x <= y`

_src/libraries/Oracle.sol:_ [L118](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L118)
[L160](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L160)

_src/libraries/BitMath.sol:_ [L71](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L71)
[L75](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L75)
[L83](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L83)
[L87](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L87)
[L95](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L95)
[L99](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol#L99)

_src/libraries/Math512Bits.sol:_ [L68](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L68)
[L200](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol#L200)

_src/libraries/SwapHelper.sol:_ [L61](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L61)

_src/libraries/FeeHelper.sol:_ [L58](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L58)

_src/LBPair.sol:_ [L278](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L278)
[L636](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L636)
[L643](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L643)
[L924](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L924)

_src/LBFactory.sol:_ [L165](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L165)
[L195](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L195)
[L549](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L549)

_src/libraries/Math128x128.sol:_ [L49](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L49)
[L75](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol#L75)

## 6. Use 1 and 2 for true and false

_src/LBFactory.sol:_ [L39](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L39)

## 7. Place `i++` in an `unchecked` blocks in for-loops

_src/LBToken.sol:_ [L90](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L90)
[L163](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L163)

_src/LBQuoter.sol:_ [L75](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75)
[L100](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100)
[L177](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177)

_src/LBPair.sol:_ [L274](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L274)
[L496](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L496)
[L623](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L623)
[L929](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L929)

_src/LBRouter.sol:_ [L674](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L674)
[L831](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L831)
[L878](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L878)
[L951](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L951)

_src/LBFactory.sol:_ [L165](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L165)
[L195](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L195)

## 8. Explicitly assingning default values to variables is a waste of gas

### Use `uint256 i;` instead of `uint256 i = 0;`

_src/libraries/Samples.sol:_ [L19](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L19)

## 9. Consider marking functions as `payable` if there is no risk of sending value through them

### This change will save gas each time a function is called

_src/libraries/PendingOwnable.sol:_ [L59](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L59)
[L68](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L68)
[L84](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L84)

_src/LBFactory.sol:_ [L396](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L396)
[L468](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L468)
[L474](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L474)
[L485](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L485)
[L493](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L493)
[L520](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L520)

## 10. When comparing variables of type `uint`, use `require(x != 0)` instead of `require(x > 0)`

_src/LBFactory.sol:_ [L161](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L161)
[L191](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L191)

_src/LBQuoter.sol:_ [L82](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L82)
[L154](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154)
[L160](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L160)

## 11. Calldata is cheaper than memory for function input

_src/libraries/TokenHelper.sol:_ [L74](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol#L74)

_src/libraries/FeeDistributionHelper.sol:_ [L26](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L26)
[L49](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L49)

_src/LBPair.sol:_ [L261](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L261)
[L467](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L467)
[L468](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L468)
[L617](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L617)
[L618](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L618)
[L688](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L688)
[L845](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L845)

_src/libraries/SwapHelper.sol:_ [L32](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L32)
[L33](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L33)
[L82](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L82)
[L83](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L83)
[L109](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol#L109)

_src/LBQuoter.sol:_ [L54](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L54)
[L134](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L134)

_src/LBRouter.sol:_ [L207](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L207)
[L230](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L230)
[L280](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L280)
[L281](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L281)
[L313](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L313)
[L314](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L314)
[L355](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L355)
[L380](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L380)
[L381](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L381)
[L409](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L409)
[L410](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L410)
[L435](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L435)
[L462](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L462)
[L495](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L495)
[L565](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L565)
[L596](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L596)
[L597](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L597)
[L645](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L645)
[L646](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L646)
[L656](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L656)
[L702](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L702)
[L703](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L703)
[L704](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L704)
[L746](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L746)
[L764](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L764)
[L766](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L766)
[L818](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L818)
[L820](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L820)
[L867](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L867)
[L941](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L941)

_src/libraries/FeeHelper.sol:_ [L55](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L55)
[L91](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L91)
[L116](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L116)
[L124](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L124)
[L133](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L133)
[L142](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L142)
[L157](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L157)

_src/LBToken.sol:_ [L79](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L79)
[L152](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L152)
[L153](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L153)

## 12. use x = x + y instead of x+= y

_src/LBToken.sol:_ [L211](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L211)
[L241](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L241)
