# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1      |  Error when calling `_beforeTokenTransfer` in the LBToken `_burn` function | Low | 1 |
| 2      | `FlashLoanFee` should be bounded  | Low | 1 |
| 3      | Immutable state variables lack zero address checks | Low | 6 |

## Findings

### 1- Error when calling `_beforeTokenTransfer` in the LBToken `_burn` function :

#### Risk : Low

#### Proof of Concept

In the `_burn` function from the LBToken.sol contract when calling the hook `_beforeTokenTransfer` the passed parameters are in the wrong order as you can see in the code below :

```
function _burn(
    address _account,
    uint256 _id,
    uint256 _amount
) internal virtual {
    if (_account == address(0)) revert LBToken__BurnFromAddress0();

    uint256 _accountBalance = _balances[_id][_account];
    if (_accountBalance < _amount) revert LBToken__BurnExceedsBalance(_account, _id, _amount);

    /* 
        @audit _beforeTokenTransfer should be in the burn case :
        _beforeTokenTransfer(_account, address(0), _id, _amount);
    */
    _beforeTokenTransfer(address(0), _account, _id, _amount);

    unchecked {
        _balances[_id][_account] = _accountBalance - _amount;
        _totalSupplies[_id] -= _amount;
    }

    _remove(_account, _id, _accountBalance, _amount);

    emit TransferSingle(msg.sender, _account, address(0), _id, _amount);
}
```

The _account and address(0) parameters are inverted which will cause the `_beforeTokenTransfer` hook to perform a mint operation instead of a burn as it stated here :
```
/// - When `from` is zero, `amount` tokens of token type `id` will be minted
/// for `to`.
/// - when `to` is zero, `amount` of ``from``'s tokens of token type `id`
/// will be burned.
```

This can cause vulnerabilities and problems in all the contract that inherit from the LBToken and use the `_beforeTokenTransfer` hook in their code logic.

#### Mitigation
The `_burn` function should be modified as follow :

```
function _burn(
    address _account,
    uint256 _id,
    uint256 _amount
) internal virtual {
    if (_account == address(0)) revert LBToken__BurnFromAddress0();

    uint256 _accountBalance = _balances[_id][_account];
    if (_accountBalance < _amount) revert LBToken__BurnExceedsBalance(_account, _id, _amount);

    /*  @audit 
        Replace : _beforeTokenTransfer(address(0), _account, _id, _amount);
        With : 
        _beforeTokenTransfer(_account, address(0), _id, _amount);
    */
    _beforeTokenTransfer(_account, address(0), _id, _amount);
    
    unchecked {
        _balances[_id][_account] = _accountBalance - _amount;
        _totalSupplies[_id] -= _amount;
    }

    _remove(_account, _id, _accountBalance, _amount);

    emit TransferSingle(msg.sender, _account, address(0), _id, _amount);
}
```

### 2- `FlashLoanFee` should have a maximum value :

#### Risk : Low

#### Proof of Concept

When setting a new FlashLoanFee in the LBFactory contract the setFlashLoanFee function doesn't check if the new value is too big or not so the owner can set a big flashloan fee and users wanting to perform a flashloan will have to pay this big fee, this should not be possible the FlashLoanFee should have a well known maximal value which it can not exceed.

#### Mitigation

Add a maximum value for the FlashLoanFee and use it to check any new fee set, the setFlashLoanFee function should be modified as follow :

```
uint256 constant MaxFlashLoanFee = 100; // 100 just for example

function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
    if (_flashLoanFee > MaxFlashLoanFee) revert LBFactory__MaxFlashLoanFeeExceeded(_flashLoanFee);

    uint256 _oldFlashLoanFee = flashLoanFee;

    if (_oldFlashLoanFee == _flashLoanFee) revert LBFactory__SameFlashLoanFee(_flashLoanFee);

    flashLoanFee = _flashLoanFee;
    emit FlashLoanFeeSet(_oldFlashLoanFee, _flashLoanFee);
}
```

### 3- Immutable state variables lack zero address checks  :

Constructors should check the values written in an immutable state variables(address, uint, int) is not the zero value (address(0) or 0)

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: src/LBRouter.sol [Line 57](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L57)
```
factory = _factory;
```

File: src/LBRouter.sol [Line 58](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L58)
```
oldFactory = _oldFactory;
```

File: src/LBRouter.sol [Line 59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L59)
```
wavax = _wavax;
```

File: src/LBQuoter.sol [Line 45](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L45)
```
routerV2 = _routerV2;
```

File: src/LBQuoter.sol [Line 46](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L45)
```
factoryV1 = _factoryV1;
```

File: src/LBQuoter.sol [Line 47](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L47)
```
factoryV2 = _factoryV2;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.
