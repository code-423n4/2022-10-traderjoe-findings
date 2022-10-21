
## Safety check on `_binStep` is too indirect
In `LBFactory.createLBPair()` a lack of check on `_binStep` would enable incorrect accounting of the number of LB pairs in `_availableLBPairBinSteps`. This in turn would cause `LBQuoter.findBestPathFromAmountIn()` and `LBQuoter.findBestPathFromAmountOut()` to always revert for that LB pair. This check is however performed by requiring that there be a preset ([LBFactory.sol#259](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L259)), which have to be set in `setPreset()` which calls `_getPackedFeeParameters()`, and only here is `_binStep` actually checked to be within the correct bounds ([LBFactory.sol#L543](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L543)).
Any change in this chain might deprive `createLBPair()` of this check. Consider adding a safety check for `_binStep` directly in `createPair()` or at least clearly comment how this check is currently done.

## Unused code
The result of [LBRouter.sol#L287](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L287) is not used and can be removed.

## Compiler version should be fixed in non-library contracts
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

## Poorly commented code
The code is in general heavily compartmentalized into functions, often in external library contracts. The names of these are sufficient to explain what the code is doing only if you have a good grasp of all of them, otherwise each function adds a small contribution of obfuscation and it can be quite difficult to tease out what a block of code is doing, having to wade through every single function call within it.
The code would be much easier to understand if it were thoroughly commented, instead of relying on a web of function names and a comprehensive knowledge thereof.

## Reliance on atomic calls for safety is deceptively not described
In `LBPair.sol`, `swap()`, `mint()` and `burn()` are all calls that are said to need "to be called from a contract which performs important safety checks". However, it is not enough to simply perform safety checks. All of these require some transfer of value to the contract before they are called, which is completely unsafe on its own. It only becomes safe if this transfer is immediately followed by one of these functions, i.e. atomically in the same transaction. This should be mentioned in the comments. If not for the safety of the user, then at least for the developer.

## Unclear comments
[`only one input needs to be stored`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L251) seems to mean rather that only one input is indexed as the key in the mappings. 
[`single check is sufficient`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L253) because the two have just been ordered and so the potential `address(0)` would at least be in the one checked. But in light of the previous `only one input needs to be stored` one is misled to look for other reasons. That it is "because they are sorted" should be added to the comment.

## Incorrect comment
[LBRouter.sol#L811](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L811), `/// @param _pairBinSteps The versions of each pair (1: DexV1, 2: dexV2)`, gives incorrect values for `_pairBinSteps`. It should be the same as [LBRouter.sol#L811](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L811): `/// @param _pairBinSteps The bin step of the pairs (0: V1, other values will use V2)`.

## Typos
["them -> it"](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L336)
["them -> it"](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L420)
["@param amountX" -> "@return amountX"](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L739)
["@param amountY" -> "@return amountY"](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L740)
Change ["The ids the user want to remove its liquidity"](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L611) into "The IDs of which the user wants to remove the liquidity" or "The IDs for which the user wants to remove his liquidity", or similar.