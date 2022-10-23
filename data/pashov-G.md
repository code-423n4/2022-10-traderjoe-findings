### Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas

When a function with a `memory`array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

************Scope:************

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L100)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L131](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L131)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L149](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L149)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L107](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBToken.sol#L107)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L134](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L134)

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L54](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L54)

### Use `IERC20` as the custom error parameter type instead of always casting to `address`

If the custom errors `LBRouter__PairNotCreated()` and `LBRouter__WrongAvaxLiquidityParameters()` use `IERC20` as the type for their `tokenX` and `tokenY` params then in the code there won’t be a need to cast (and waste gas) the variables used as arguments to a type of `address`.

### Since `WAVAX` ’s `transfer`  & `transferFrom` functionality never return false there is no point in using `safeTransfer`

Just use `transfer` and `transferFrom` for `WAVAX` transfers to save gas from not needed safety checks.

### `x = x + y;` is cheaper than `x += y` , same for `x = x - y;` and `x += y`

To save gas, remove all occurrences of `+=` and `-=` since they waste more gas than the proposed solutions

### Use `private` instead of `public` for constants

To save gas, change all constants that are public to be private, since their getter is not needed as the value is known.

The public constants in the code are in `LBFactory.sol`

```solidity
uint256 public constant override MAX_FEE = 0.1e18; // 10%

    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons

    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```

### Division by two should use bit shifting

`x / 2` is the same as `x >> 1` in Solidity, but the latter costs less gas, so you can save some by using `x >> 1`

**********Scope**********

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/libraries/Oracle.sol#L162](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/libraries/Oracle.sol#L162)

### ****Structs can be packed into fewer storage slots****

Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

This can be certainly done in `ILBRouter::LiquidityParameters` for example with the `activeIdDesired` & `binStep` parameters

### `i--` from for loops can be moved to an `unchecked` block

The expression `i--` in for loops (in the way the are implemented in the codebase) can’t underflow since we also have the `i > 0` or `i != 0` check. Move `i--` to an `unchecked` block to save gas

**********Scope**********

[https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L154](https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L154)

[https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)