## Table of Contents 

- [G-01] ++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)
- [G-02] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops
- [G-03] Using bools for storage incurs overhead
- [G-04] Using private rather than public for constants, saves gas
- [G-05] Functions guaranteed to revert when called by normal users can be marked payable

### [G-01] ++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 5 gas per loop.

[LBQuoter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)

### [G-02] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L496
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L623
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L701

LBRouter.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L778
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L831
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L878
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951

LBToken.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90

### [G-03] Using bools for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelincontracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use uint256(1)and uint256(2)for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000gas) when changing from false to true, after having been true in the past.

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L274)

### [G-04] Using private rather than public for constants, saves gas

[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L25-L30)

### [G-05] Functions guaranteed to revert when called by normal users can be marked payable

[LBFactory](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L423) onlyOwner, [LBPair](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L788) onlyFactory