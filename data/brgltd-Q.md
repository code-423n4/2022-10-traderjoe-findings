# [01] Unhandled `amountsId[0] == msg.value` in `LBRouter.swapAVAXForExactTokens`

If `msg.value` equals `amountsIn[0]` in `LBRouter`, `swapAVAXForExactTokens()` will not revert and it will also not swap.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L512

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L520

## Recommendation

Since the transfer should not be executed if `msg.value` equals `amountsId[0]` (due to insufficient input funds), the correct behavior would be for the function to revert. E.g.

```
diff --git a/LBRouter.sol.orig b/LBRouter.sol
--- a/LBRouter.sol.orig
+++ b/LBRouter.sol
-        if (amountsIn[0] > msg.value) revert LBRouter__MaxAmountInExceeded(msg.value, amountsIn[0]);
+        if (amountsIn[0] >= msg.value) revert LBRouter__MaxAmountInExceeded(msg.value, amountsIn[0]);
```

# [02] Missing address(0) checks for constructor

Consider adding checks for address(0) in `LBQuoter.constructor()`.

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L40-L44

## Recommendation

```
diff --git a/LBQuoter.sol.orig b/LBQuoter.sol
--- a/LBQuoter.sol.orig
+++ b/LBQuoter.sol
+        if (
+            _routerV2 == address(0) ||
+            _factoryV1 == address(0) ||
+            _factoryV2 == address(0)
+        ) {
+            // Example of a new custom error to revert on address(0) for LBQuoter
+            revert LBQuoter__AddressZero();
+        }
         routerV2 = _routerV2;
         factoryV1 = _factoryV1;
         factoryV2 = _factoryV2;
```

# [03] Critical changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

## Recommendation

Consider adding a two-steps pattern on critical changes to avoid mistakenly transferring ownership of roles or critial functionalities to the wrong address.

```
function setFactoryLockedState(bool _locked) external override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485

# [04] Public functions not called by the contract should be declared external

```
function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
    public
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L54-L55

```
function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
    public
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L134-L135

# [05] Replace memory with calldata

Read-only array arguments should use calldata instead of memory.

```
function pendingFees(address _account, uint256[] memory _ids) 
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261

```
function mint(
    uint256[] memory _ids,
    uint256[] memory _distributionX,
    uint256[] memory _distributionY,
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L466-L469

```
function burn(
    uint256[] memory _ids,
    uint256[] memory _amounts,
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L616-L618

# [06] Missing event for critical parameter changes

Adding an event for `setFeeParameters()` would facilitade offchain monitoring.

```
function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L788

```
function _setFeesParameters(bytes32 _packedFeeParameters) internal {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L905

# [07] Missing NATSPEC/documentation

Consider adding NATSPEC on all functions to improve documentation.

```
function forceDecay() external override onlyFactory {
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L792

# [08] Mixing named variables and manually returning a value for the same function

For `LBPair.mint()`, consider manually returning all the values or using the named return variable feature for all variables to improve the explicitness and readability of the code.

```
returns (
    uint256,
    uint256,
    uint256[] memory liquidityMinted
)
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L476-L478

# [09] Replace magic numbers with constants to improve code readability

```
uint256 _amountOut = (_reserve1 * (_balance - _reserve1) * 997) / (_balance * 1_000);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L891

```
uint256 _amountOut = (_reserve0 * (_balance - _reserve1) * 997) / (_balance * 1_000);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L896

```
(quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L85

```
quote.fees[i] = 0.003e18; // 0.3%
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L89

```
(JoeLibrary.quote(quote.virtualAmountsWithoutSlippage[i], reserveOut, reserveIn) * 1000) /
997;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L163-L164

# [10] Replace assert with custom error

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). 

"The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix."

Even if the code is expected to be unreacheable, consider using a custom error instead of assert.

```
assert(_binStep == _preset.decode(type(uint16).max, _shift));
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141
