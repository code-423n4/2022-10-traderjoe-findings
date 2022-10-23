# [2022-10-traderjoe] QA report 

###### tags: `c4`, `2022-10-traderjoe`, `QA`

## Summary 
| Id | Issues |
| -------- | -------- | 
| [Q-01]    | Function `LBQuoter.findBestPathFromAmountIn` doesn't support `_route` contains 2 identical consecutive pair of tokens     |
| [Q-02]    | Forget to checking `ids.length` and `_amounts.length` when call function `LBPair.burn()`    |
| [Q-03]    | Should revert when `_tokenPath[0] != wavax` in function `LBRouter.swapExactAVAXForTokens`     |
| [Q-04]    | Should revert when `uint256(_id) > type(uint24).max` in function `BinHelper.getPriceFromId`     |
| [Q-05]    | Should revert when `tokenX == tokenY` in function `LBPair.initialize`     |


## QA report 

### [Q-01] Function `LBQuoter.findBestPathFromAmountIn` doesn't support `_route` contains 2 identical consecutive pair of tokens

**Lines of code** 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L54

**Detail**  
For each 2 consecutive tokens in `_route`, function `LBQuoter.findBestPathFromAmountIn` will check which type of pair (`JoePair` / `LBPair`) will return the best rate. Unfortunately for `_route` which contains 2 identical consecutive pair of tokens it may return the wrong `quote`. 
For example, a arbitrage bot need to do arbitrage with `LBPair` and `JoePair` using path `_route` = [avax, joe, avax]. It call to function `LBQuoter.findBestPathFromAmountIn` to get the best `amountOut` it can get. But function will return the same type of pair for pair [avax, joe] (it will return both `JoePair` or both `LBPair`) cause function didn't cache any changes of pair when executing a swap.
**Recommend mitigation** 
Add another function which support for these type of swap by caching the changes after swapping 


### [Q-02] Forget to checking `ids.length` and `_amounts.length` when call function `LBPair.burn()` 

**Lines of code** 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L617-L618

**Detail** 
Function `LBPair.burn()` contains paramters 
* `_ids`: List of bin's id which the user want to remove its liquidity 
* `_amounts`: List of amount of token to burn for each bin 
Based on the purpose of these 2 arrays, it should have the same length. But in the function `burn` there is no requirement for this condition.

**Recommend Mitigation** 
Add new requirements for this condition with custom error.


### [Q-03] Should revert when `_tokenPath[0] != wavax` in function `LBRouter.swapExactAVAXForTokens`

**Lines of code**
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L407

**Detail** 
Function `LBRouter.swapExactAVAXForTokens` is used to swap exact input with tokenIn = wavax. So it should revert when `_tokenPath[0] != avax` like in function [`LBRouter.swapAVAXForExactTokens`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L507) and function [`swapExactAVAXForTokensSupportingFeeOnTransferTokens`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L601)


### [Q-04] Should revert when `uint256(_id) > type(uint24).max` in function `BinHelper.getPriceFromId` 

**Lines of code** 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L40

**Detail** 
The value of `_id` is always < 2^24 follow the [whitepaper](https://github.com/traderjoe-xyz/LB-Whitepaper/blob/main/Joe%20v2%20Liquidity%20Book%20Whitepaper.pdf) so it should revert when `_id > type(uint24).max` like in function [`BinHelper.getIdFromPrice`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L29).


### [Q-05] Should revert when `tokenX == tokenY` in function `LBPair.initialize`

**Lines of code** 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L104-L110

**Detail** 
Pair of `tokenX` and `tokenY` is allowed to create/intialize when `tokenX != tokenY` and both of them are not `address(0)`, but function `LBPair.initialize()` haven't checked `tokenX != tokenY` or not 

**Mitigation recommendation** 
Revert with custom error when `tokenX == tokenY`