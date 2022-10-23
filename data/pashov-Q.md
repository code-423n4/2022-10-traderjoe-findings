### Remove unneeded variable

`LBToken::safeTransferFrom` has a not needed variable - `_spender` it is used only once, so you can just inline it

**********Scope**********

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L137](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L137)

### Add non-zero address checks for `address` arguments in constructors

Most constructors in the codebase check `address` arguments for a zero value, but some don’t. Add such checks there.

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L137](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L137)

[https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L52](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L52)

### Incomplete NatSpec missing to document the return value

In `LBFactory::_getPackedFeeParameters` the NatSpec is incomplete because it is missing the `returns` part. Add it

### Just use `address` instead of `address payable` since you are not using `send` or `transfer`

Adding `payable` to an `address` variable gives it the ability to call the `send` and `transfer` functions of `address payable`. They are not used in the codebase though so there is no need for the `payable` keyword there.

**********Scope**********

[https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L315](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L315)

[https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L382](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L382)

[https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L464](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L464)

[https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L566](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L566)

### Function has a misleading comment

The `initialize` function in `LBPair.sol` has the following comment in its NatSpec:

```solidity
/// It is highly recommended to never call this function directly, use the factory
    /// as it validates the different parameters
```

but it also has the `onlyFactory` modifier, while the comment makes you believe the method can be called from a non-factory address which is wrong. Remove the comment

### Method does not check if array arguments have the same length

`LBPair::burn` has two `uint256[]` parameters - `ids` and `_amounts` and they should be of the same length for the code to work as expected. Add a check that their lengths are the same at the beginning of the method.

### Change function visibility from `public` to `external`

External functions use calldata for method arguments, while public functions use memory, so when possible you should use `external` instead of `public` to save gas

************Scope:************

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L100)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L131](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L131)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L149](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L149)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L107](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L107)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L134](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L134)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L54](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L54)

### Use a more recent version of Solidity

If you use version that is at least 0.8.12 in the codebase you can use  `string.concat()`
 instead of `abi.encodePacked()` in LBFactory::_getPackedFeeParameters