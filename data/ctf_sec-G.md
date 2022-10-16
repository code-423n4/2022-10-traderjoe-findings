## Function can be marked as external instead of public

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L54


```solidity
function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
```
            

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L134


```solidity
function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
```
            

## ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT�S USED IN FOR-LOOPS (--I/I-- TOO)
    
Saves 6 gas per loop

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75


```solidity
for (uint256 i; i < swapLength; i++) {
```
            

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154


```solidity
for (uint256 i = swapLength; i > 0; i--) {
```