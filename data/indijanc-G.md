# Use calldata instead of memory
Several functions in `LBPair.sol` can use calldata for parameters instead of memory to reduce gas:

[LBPair.sol L261](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L261)
[LBPair.sol L467-L469](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L467-L469)
[LBPair.sol L617-L618](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L617-L618)
[LBPair.sol L688](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L688)

Here's the gas improvements measured by some tests:

```forge test --match-test testClaimFeesY```
before:
```[PASS] testClaimFeesY() (gas: 1226681)```
after:
```[PASS] testClaimFeesY() (gas: 1225323)```

```forge test --match-test testFlashloan```
before
```[PASS] testFlashloan() (gas: 1722024)```
after:
```[PASS] testFlashloan() (gas: 1721000)```

```forge test --match-test testPendingFeesNotIncreasingReverts```
before:
```[PASS] testPendingFeesNotIncreasingReverts()```
after:
```[PASS] testPendingFeesNotIncreasingReverts()```

```forge test --match-contract LiquidityBinPairLiquidityTest```
before:
```
[PASS] testBurnLiquidity() (gas: 1900336)
[PASS] testConstructor(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24) (runs: 1024, μ: 5117585, ~: 5117585)
[PASS] testFlawedCompositionFactor() (gas: 1682266)
[PASS] testFuzzingAddLiquidity(uint256) (runs: 1024, μ: 1052339, ~: 1063474)
[PASS] testInsufficientLiquidityMinted() (gas: 1647839)
```
after:
```
[PASS] testBurnLiquidity() (gas: 1895743)
[PASS] testConstructor(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24) (runs: 1024, μ: 5112376, ~: 5112376)
[PASS] testFlawedCompositionFactor() (gas: 1675789)
[PASS] testFuzzingAddLiquidity(uint256) (runs: 1024, μ: 1050254, ~: 1062001)
[PASS] testInsufficientLiquidityMinted() (gas: 1643128)
```

# checks for `address(0)` redundant in `LBPair:initialize()`
[LBPair.sol L111](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L111)
The `address(0)` checks on `_tokenX` and `_tokenY` can be removed because the function is only callable by `factory` from `createLBPair()` where that check is already done with:
```Solidity
        (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
        // single check is sufficient
        if (address(_tokenA) == address(0)) revert LBFactory__AddressZero();
```

# check for `address(0)` can be skipped in `LBFactory:createLBPair()`
[LBFactory.sol L255-L256](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L255-L256)
Check for `address(0)` can be removed to reduce gas. In this case create will revert in `cloneDeterministic()` with a string "ERC1167: create2 failed". Note that the tradeoff here is reduced clarity/supportability.



