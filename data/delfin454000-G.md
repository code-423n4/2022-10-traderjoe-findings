### Inline a function/modifier that’s only used once
It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.
___
[PendingOwnable.sol: L35-38](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L35-L38)
```solidity
    modifier onlyPendingOwner() {
        if (msg.sender != _pendingOwner || msg.sender == address(0)) revert PendingOwnable__NotPendingOwner();
        _;
    }
```
Since `onlyPendingOwner()` is used only once in this contract (in `function becomeOwner()`) as shown below, it should be inlined to save gas

[PendingOwnable.sol: L75-77](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L75-L77)
```solidity
    function becomeOwner() public override onlyPendingOwner {
        _transferOwnership(msg.sender);
    }
```
___
___

### Avoid using `booleans` for storage
Using a `bool` for storage incurs more overhead than using `uint256` or any type that takes up a full word
___
[LBFactory.sol: L39](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L39)
```solidity
    bool public override creationUnlocked;
```
___
___


### Array length should not be looked up during every iteration of a `for` loop
Since calculating the array length costs gas, it's best to read the length of the array from memory before executing the loop, as demonstrated below:

___
[LBToken.sol: L90-92](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90-L92)
```solidity
            for (uint256 i; i < _accounts.length; ++i) {
                batchBalances[i] = balanceOf(_accounts[i], _ids[i]);
            }
```
Suggestion:
```solidity
            uint256 totalAccountsLength = _accounts.length;
            for (uint256 i; i < totalAccountsLength; ++i) {
                batchBalances[i] = balanceOf(_accounts[i], _ids[i]);
            }
```
___
Similarly for the 12 additional `for` loops referenced below:

[LBPair.sol: L274](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274)
 
[LBPair.sol: L496](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L496)

[LBPair.sol: L623](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L623)

[LBPair.sol: L701](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L701)

[LBQuoter.sol: L100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)

[LBRouter.sol: L674](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L674)

[LBRouter.sol: L711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)

[LBRouter.sol: L778](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L778)

[LBRouter.sol: L831](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L831)

[LBRouter.sol: L878](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L878)

[LBRouter.sol: L951](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L951)

[LBToken.sol: L163](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L163)
___
___


### Use `++i` instead of `i++` to increase count in a `for` loop
Since use of  `i++` (or `j++` or equivalent counter) costs more gas, use `++i`/`++j` in the `for` loops below. Similarly for `i--`/`j--`.

[LBQuoter.sol: L75](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)

[LBQuoter.sol: L100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)

[LBQuoter.sol: L154](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154)

[LBQuoter.sol: L177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)

[LBRouter.sol: L711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)

___
___


### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or `i++` or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}`/`unchecked {i++}` instead. Most of the `for` loops in this contest use `unchecked` counters. However, that is not the case for the five loops below:

[LBQuoter.sol: L75](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)

[LBQuoter.sol: L100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)

[LBQuoter.sol: L154](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154)

[LBQuoter.sol: L177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)

[LBRouter.sol: L711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)
___
___