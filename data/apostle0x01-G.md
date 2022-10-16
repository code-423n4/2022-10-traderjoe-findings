## [G-01] Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. 
If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. 
Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. 
The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

## POC

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L234

```solidity
src\LBRouter.sol:234:        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)

```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L706

```solidity
src\LBRouter.sol:706:    ) private view returns (uint256[] memory amountsIn) {

```


https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L944

```solidity
src\LBRouter.sol:944        returns (address[] memory pairs)

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L85

```solidity
src\LBToken.sol:85        returns (uint256[] memory batchBalances)

```

## [G-02]  abi.encode() is less efficient than abi.encodePacked()

## POC

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L265-L266

```solidity
        bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));

```

## [G-03] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

## POC
```solidity
src/LBFactory.sol:25:    uint256 public constant override MAX_FEE = 0.1e18; // 10%
src/LBFactory.sol:27:    uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
src/LBFactory.sol:28:    uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
src/LBFactory.sol:30:    uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%

```


