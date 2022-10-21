# \[Non-Critical\] PendingOwnable#setPendingOwner allows to set _pendingOwner to _owner

## Impact
Because the ```setPendingOwner``` lacks input validation, the **_pendingOwner** can be updated to **_owner**.

This would allow calling function ```becomeOwner```, which will call function ```_transferOwnership``` without reverting. Although such an update wouldn’t change the contract state, it would emit an event falsely indicating the owner had been changed.

## POC
1. Alice create a PendingOwnable contract, becoming the contract owner
1. Alice calls function setPendingOwner with her address as parameter
1. Alice calls function becomeOwner and ```PendingOwnerSet````event is emited


## Tools used
Manual review

## Mitigation steps:
Add a custom error called ```PendingOwnable__PendingOwnerCannotBeOwner()``` and modify setPendingOwner to
```solidity
function setPendingOwner(address pendingOwner_) public override onlyOwner {
        if (pendingOwner_ == address(0)) revert PendingOwnable__AddressZero();
        if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();
        if (pendingOwner_ == _owner) revert PendingOwnable__PendingOwnerCannotBeOwner();
        _setPendingOwner(pendingOwner_);
    }
```

# \[LOW\] Multiple function allows to set recipient to address zero
This would allow to burn tokens

Compromised function:
* [LBRouter#removeLiquidity](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L274)
* [LBRouter#swapExactTokensForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L352)
* [LBRouter#swapExactAVAXForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L407)
* [LBRouter#swapTokensForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L431)
* [LBRouter#swapAVAXForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L493)
* [LBRouter#swapExactTokensForTokensSupportingFeeOnTransferTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L531)
* [LBRouter#swapExactAVAXForTokensSupportingFeeOnTransferTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L594)
* [LBRouter#sweep](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L622)
* [LBRouter#sweepLBToken](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L642)
* [LBRouter#removeLiquidityAVAX](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L308)
* [LBRouter#swapExactTokensForAVAX](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L377)
* [LBRouter#swapTokensForExactAVAX](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L459)
* [LBRouter#swapExactTokensForAVAXSupportingFeeOnTransferTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L561)
* [LBPair#swap](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L304)

Mitigation steps:
Add a modifier which checks that ```to != address(0)```

# \[LOW\] swapExactTokensForTokens and swapTokensForExactTokens do not check firstToken != finalToken
Functions affected:
* [LBRouter#swapExactTokensForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L352-L359)
* [LBRouter#swapTokensForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L431)

The contract should check that first token and last token of ```_tokenPath``` parameter are different.