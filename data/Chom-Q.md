(Automated finding from c4udit omitted)

## Find the best path should be reverted on an invalid swap route rather than skipping

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L101-L124
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L178-L204

```solidity
                    if (!LBPairsAvailable[j].ignoredForRouting) {
                        bool swapForY = address(LBPairsAvailable[j].LBPair.tokenY()) == _route[i + 1];

                        try
                            ILBRouter(routerV2).getSwapOut(LBPairsAvailable[j].LBPair, quote.amounts[i], swapForY)
                        returns (uint256 swapAmountOut, uint256 fees) {
                            if (swapAmountOut > quote.amounts[i + 1]) {
                                quote.amounts[i + 1] = swapAmountOut;
                                quote.pairs[i] = address(LBPairsAvailable[j].LBPair);
                                quote.binSteps[i] = LBPairsAvailable[j].binStep;

                                // Getting current price
                                (, , uint256 activeId) = LBPairsAvailable[j].LBPair.getReservesAndId();
                                quote.virtualAmountsWithoutSlippage[i + 1] = _getV2Quote(
                                    quote.virtualAmountsWithoutSlippage[i] - fees,
                                    activeId,
                                    quote.binSteps[i],
                                    swapForY
                                );

                                quote.fees[i] = (fees * 1e18) / quote.amounts[i]; // fee percentage in amountIn
                            }
                        } catch {}
                    }
```

These if and try-catch blocks are skipping invalid routes. quote.xxx[i] will be the default value which causes the quote to can't be used due to having a null quote.