## 1. typo 

considred --> considered

- [LBQuoter.sol#L229](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L229)


## 2. _safemint() should be used rather than _mint() wherever possible

- [LBPair.sol#L579](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L579)


## 3. constants should be defined rather than using magic numbers 

Even assembly can benefit from using readable constants instead of hex/numeric literals

- [LBFactory.sol#L143-151](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143)
- [LBFactory.sol#L159](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L159)
- [LBFactory.sol#L189](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L189)
- [LBFactory.sol#L261](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L261)
- [LBFactory.sol#L285](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L285)
- [LBFactory.sol#L297-303](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L297)
- [LBFactory.sol#L364](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L364)
- [LBFactory.sol#L375](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L375)
- [LBFactory.sol#L403](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L403)
- [LBFactory.sol#L285](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L285)
- [LBFactory.sol#L285](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L285)
- [LBFactory.sol#L285](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L285)

- [LBQuoter.sol#L85](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L85)
- [LBQuoter.sol#L89](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L89)
- [LBQuoter.sol#L121](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L121)
- [LBQuoter.sol#L163](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L163)
- [LBQuoter.sol#L166](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L166)
- [LBQuoter.sol#L201](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L201)

- [LBRouter.sol#L725](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L725)
- [LBRouter.sol#L791](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L791)
- [LBRouter.sol#L794](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L794)
- [LBRouter.sol#L891](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L891)
- [LBRouter.sol#L896](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L896)


## 4. incorrect functions visibility

Whenever a function is not being called internally in the code, it can be easily declared as external, saving also gas. While the entire code base have explicit visibilities for every function, some of them can be changed to be external.

- [LBQuoter.sol#L54](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54)
- [LBQuoter.sol#L134](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134)

- [LBRouter.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L90)

- [LBToken.sol#L49](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49)
- [LBToken.sol#L55](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55)
- [LBToken.sol#L63](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L63)
- [LBToken.sol#L79](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L79)
- [LBToken.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L100)
- [LBToken.sol#L107](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L107)
- [LBToken.sol#L115](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L115)
- [LBToken.sol#L122](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L122)
- [LBToken.sol#L131](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L131)


## 5. Code Structure Deviates From Best-Practice

The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier. Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

modifier is in the wrong place

- [LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol)

- [LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol)

- [LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol)


## 6. Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.
https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678

there is a library for constants but it doesnt have all of the constants, 

- [LBPair.sol#L76-84](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L76)

- [LBFactory.sol#L25-30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25)

- [LBToken.sol#L29-30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29)


## 7. event is missing old value 

throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. here event is missing the old value.

- [IJoeFactory.sol#L8](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L8)

- [IJoePair.sol#L8](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L8)
- [IJoePair.sol#L9](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L9)
- [IJoePair.sol#L49](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L49)
- [IJoePair.sol#L50](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L50)
- [IJoePair.sol#L51](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L51)

- [ILBFactory.sol#L38](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L38)
- [ILBFactory.sol#L57](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L57)

- [ILBPair.sol#L111](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L111)
- [ILBPair.sol#L148](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L148)
- [ILBPair.sol#L150](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L150)

- [ILBToken.sol#L19](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol#L19)


## 8. require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

- [LBFactory.sol#L141](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141)

## 9. lines are too long
Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Its advised to keep lines lower than 120 characters

- [Oracle.sol#L69](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L69)
- [Oracle.sol#L70](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L70)
