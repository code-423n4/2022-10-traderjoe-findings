## `from` and `to` are in reverse order for `_beforeTokenTransfer()` during `_burn()` in `LBToken.sol`

In the `_burn()` function of LBToken.sol the [`_beforeTokenTransfer()` ](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L237) uses the wrong order for `from` and `to`.

The function is [defined](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L808-L813) in LBPair.sol and is defined as

```solidity
 function _beforeTokenTransfer(
        address _from,
        address _to,
        uint256 _id,
        uint256 _amount
    ) internal override(LBToken) {
```

In the [`_burn()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L227-L247) function of `LBTokens.sol` the call is made as 
```solidity
_beforeTokenTransfer(address(0), _account, _id, _amount);
```
Where `_account` is the address the tokens are burnt from (and fictitiously sent tot he 0 address).

Hence the call should be
```solidity
_beforeTokenTransfer(_account, address(0), _id, _amount);
```

However the only place the internal [`_burn()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L227-L247) function is being called from is the external [`burn()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L663) in LBPair.sol

In practice this means that `from` and `to` are the 0 address and the LBPair's `this` address (regardless of the order).
The resulting behavior of [`_beforeTokenTransfer()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L808-L833) in the current code base is effectively a noop.

Note that the [`super._beforeTokenTransfer()`](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L815) is also a noop as it's [defined](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L321-L326) in `LBPair.sol as an empty function.

In future code changes or forks this could change, so obviously it's highly advised to correct this.