# Misaligned parameters when calling `_beforeTokenTransfer()` in `LBToken:_burn()`
[LBToken.sol L237](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L237)
`address(0)` and `_account` should be switched on calling `_beforeTokenTransfer()` in `LBToken:_burn()`. There's no side effects currently because `_burn()` is always called with `address(this)` hence fee cache is updated.

# Slightly misleading comment
[LBPair.sol L97](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L97)
Slightly misleading comment as it implies it can be called by anyone while it can actually only be called by `factory`. The comment could be updated to reflect that.

# `LBPair:collectFees()` not restricted to fee recipients
[LBPair.sol L688](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L688)
`collectFees()` can claim for others. This may in some cases cause issues. For composability the `collectFees()` might be called by another contract which includes logic based on the amount of claimed fees, this can open up an attack vector. for an EOA a malicious user could cause some confusion when claiming on behalf of others, and may also be a taxable event where users would want to control their fee schedules. Maybe consider restricting to only fee recipients.