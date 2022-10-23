## Summary

Risk | Title
---|---
L00 | `_beforeTokenTransfer` function called with wrong params in LBToken 
L01 | `LBQuoter:constructor` missing zero address checks
L02 | LBQuoter.sol line 79 not reverting when quote.pairs[i] is 0 address
## Low

### L00: `_beforeTokenTransfer` function called with wrong params in LBToken

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L237

 Line 237 seems to be a copy pasta mistake from line 209 in LBToken.sol.
 On line 237 when burning tokens, `to` should be zero, and `amount` of ``from``'s tokens of token type `id` will be burned, so it should be:

```solidity
 _beforeTokenTransfer(_account, address(0), _id, _amount);
```

See the comments for the `_beforeTokenTransfer` function lines 310-315 in LBToken.sol.

### L01: `LBQuoter:constructor` missing zero address checks

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L40-L48

The parameters for the `LBQuoter:constructor` `_routerV2`, `_factoryV1`, `_factoryV2` are not checked for 0 address. Due to buggy front end or user error, the `routerV2`, `factoryV1`, `factoryV2` can be set to 0 address in the `LBQuoter:constructor`.

### L02: LBQuoter.sol line 79 not reverting when quote.pairs[i] is 0 address

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L79-L99

On LBQuoter.sol line 79 the if statement is checking if quote.pairs[i] != address(0). But if quote.pairs[i] is a 0 address there is no revert for this case.

Solution: Add an else block that reverts, see code snippet below:

```solidity
if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
    (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i], _route[i], _route[i + 1]);

    if (reserveIn > 0 && reserveOut > 0) {
        quote.amounts[i + 1] = JoeLibrary.getAmountOut(quote.amounts[i], reserveIn, reserveOut);
        quote.virtualAmountsWithoutSlippage[i + 1] = JoeLibrary.quote(
            (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
            reserveIn,
            reserveOut
        );
        quote.fees[i] = 0.003e18; // 0.3%
    }
}
else {
    // revert here
}