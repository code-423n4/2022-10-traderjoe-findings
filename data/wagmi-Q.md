# QA

# Summary

| Id | Title |
| -- | ----- |
| 1 | No upper limit check on flash loan fee |
| 2 | Low-level call not check contract existence |

## 1. No upper limit check on flash loan fee
No upper limit check on `setFlashLoanFee()`. Although only admin can set this, human error can lead to higher percente swap, leading to excess swap and gas fee. Or admin can rug pull users by front-running flash loan transaction and setting unreasonable high fee.
```solidity
function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
    uint256 _oldFlashLoanFee = flashLoanFee;

    if (_oldFlashLoanFee == _flashLoanFee) revert LBFactory__SameFlashLoanFee(_flashLoanFee);

    flashLoanFee = _flashLoanFee;
    emit FlashLoanFeeSet(_oldFlashLoanFee, _flashLoanFee);
}
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L474-L481


## 2. Low-level call not check contract existence

The `safeTransfer()` and `safeTransferFrom()` function calls make a low-level `call()` on the token address without checking if that is indeed a contract or not (EOA). EVM low-level calls return success even if the address used does not have a contract. So checking for success return value assumes the transfer happened successfully which it may have failed silently due to missing contract at token address for some reason (incorrect token address or selfdestruct).

OpenZeppelin’s implementation and comment highlighting this issue:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/6241995ad323952e38f8d405103ed994a2dcde8e/contracts/token/ERC20/utils/SafeERC20.sol#L87-L92

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/6241995ad323952e38f8d405103ed994a2dcde8e/contracts/utils/Address.sol#L129



```solidity
function safeTransfer(
    IERC20 token,
    address recipient,
    uint256 amount
) internal {
    if (amount != 0) {
        (bool success, bytes memory result) = address(token).call(
            abi.encodeWithSelector(token.transfer.selector, recipient, amount)
        );

        _catchTransferError(success, result);
    }
}
```

### Proof of Concept
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TokenHelper.sol#L28

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TokenHelper.sol#L46

### Recommendation
Consider either introducing a two-step process or making a test call to the new admin before updating it.



