# Low Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|Use of `assert()` statements|3|
|2|Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions|1|
|3|Incorrect parameters for `_beforeTokenTransfer()` hook|1|

Total: **4** instances over **2** issues

## `require()` should be used instead of `assert()` statements
Prior to Solidity v0.8.0, hitting an `assert()` statement **consumes all the remaining of the transaction's available gas** rather than returning it, as `require()`/`revert()` do.

Even past Solidity v0.8.0, `assert()` statements should be avoided, as seen in Solidity's [documentation](https://docs.soliditylang.org/en/v0.8.0/control-structures.html#panic-via-assert-and-error-via-require):
> Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic.

Thus, consider using `require()` statements instead of `assert()`, or if-statements with `revert()`.

_There are **3** instances of this issue:_  
```solidity
src/LBFactory.sol:
 141:        assert(_binStep == _preset.decode(type(uint16).max, _shift));
```

## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this link](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

_There is **1** instance of this issue:_  
```solidity
src/LBPair.sol:
  27:        contract LBPair is LBToken, ReentrancyGuardUpgradeable, ILBPair {
```

## Incorrect parameters for `_beforeTokenTransfer()` hook 
In `LBToken.sol, the `_beforeTokenTransfer()` hook has the following parameters:
```solidity
 317:    /// @param from The address of the owner of the token
 318:    /// @param to The address of the recipient of the  token
 319:    /// @param id The id of the token
 320:    /// @param amount The amount of token of type `id`
 321:    function _beforeTokenTransfer(
 322:        address from,
 323:        address to,
 324:        uint256 id,
 325:        uint256 amount
 326:    ) internal virtual {}
```

Howver, in `_burn()`, it is called with the following parameters:
```solidity
src/LBToken.sol:
 237:        _beforeTokenTransfer(address(0), _account, _id, _amount);
```
This is incorrect as the positions of `address(0)` and `_account` should be swapped. 

Although this currently does not have any impact, it could potentially cause bugs to occur should `_beforeTokenTransfer()` overidden in future contracts.

# Non-Critical Report

|No.|Issue|Instances|
|:-|:-|:-:|
|1|Redundant `return` statements|1|
|2|Unused `override` function arguments|11|

Total: **12** instances over **2** issues

## Redundant `return` statements
If a function has defined return variables, they will be returned when the function terminates. Thus, there is no need to explicitly specify them in a return statement.

_There is **1** instance of this issue:_  
```solidity
src/LBPair.sol:
 607:        return (_mintInfo.amountXAddedToPair, _mintInfo.amountYAddedToPair, liquidityMinted);
```

## Unused `override` function arguments
For functions declared as `override`, unused arguments should have the variable name removed or commented out to avoid compiler warnings.

_There are **11** instances of this issue:_  
```solidity
src/LBRouter.sol:
 283:        uint256 _deadline
 316:        uint256 _deadline
 358:        uint256 _deadline
 383:        uint256 _deadline
 412:        uint256 _deadline
 437:        uint256 _deadline
 465:        uint256 _deadline
 498:        uint256 _deadline
 537:        uint256 _deadline
 567:        uint256 _deadline
 599:        uint256 _deadline
```