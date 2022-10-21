## Impact
The internal `_burn()` function in LBToken.sol calls `_beforeTokenTransfer()` with incorrect `from` and `to` parameters. 

```solidity
    function _burn(
        address _account,
        uint256 _id,
        uint256 _amount
    ) internal virtual {
        ...

        // @audit-info invalid from/to
        _beforeTokenTransfer(address(0), _account, _id, _amount);

        ...
    }
```

The correct order for the hook call should be `_beforeTokenTransfer(_account, address(0), _id, _amount);`

As the `_burn()` function in LBPair.sol is only called on the pair address itself, and the current hook implementations explicitly ignore the pair address, this cannot be abused. Nonetheless, this should be fixed to avoid any issues that come with updates/changes down the line.

## Recommended Mitigation Steps

`_burn()` should call the `_beforeTokenTransfer()` hook with the correct parameters.

```solidity
    function _burn(
        address _account,
        uint256 _id,
        uint256 _amount
    ) internal virtual {
        ...

        _beforeTokenTransfer(_account, address(0), _id, _amount);

        ...
    }
```
