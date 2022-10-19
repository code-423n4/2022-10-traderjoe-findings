- [Low](#low)
    - [**1. Outdated compiler**](#1-outdated-compiler)
    - [**2. Lack of checks supportsInterface**](#2-lack-of-checks-supportsinterface)
    - [**3. Integer overflow by unsafe casting**](#3-integer-overflow-by-unsafe-casting)
    - [**4. Avoid the possibility of an evil factory**](#4-avoid-the-possibility-of-an-evil-factory)
- [Non critical](#non-critical)
    - [**5. Avoid hardcoded values**](#5-avoid-hardcoded-values)

# Low

## **1. Outdated compiler**

The pragma version used are:

```
pragma solidity ^0.8.0;
```

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.3](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/):

- Optimizer: Fix bug on incorrect caching of Keccak-256 hashes.

[0.8.4](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/):

- ABI Decoder V2: For two-dimensional arrays and specially crafted data in memory, the result of abi.decode can depend on data elsewhere in memory. Calldata decoding is not affected.

[0.8.9](https://blog.soliditylang.org/2021/09/29/solidity-0.8.9-release-announcement/):

- Immutables: Properly perform sign extension on signed immutables.
- User Defined Value Type: Fix storage layout of user defined value types for underlying types shorter than 32 bytes.

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

[0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)

- Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

Apart from these, there are several minor bug fixes and improvements.

## **2. Lack of checks `supportsInterface`**

The `EIP-165` standard helps detect that a smart contract implements the expected logic, prevents human error when configuring smart contract bindings, so it is recommended to check that the received argument is a contract and supports the expected interface.

**Reference:**

- https://eips.ethereum.org/EIPS/eip-165

**Affected source code:**

- [LBQuoter.sol:45-47](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L45-L47)

## **3. Integer overflow by unsafe casting**

Keep in mind that the version of solidity used, despite being greater than `0.8`, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

**Recommendation:**

Use a [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) from Open Zeppelin.

```javascript
        checkpoint.votes = uint192(_newTotalVotes);
```

**Affected source code:**

- [LBPair.sol:393](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L393)
- [LBPair.sol:670](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L670)
- [LBPair.sol:671](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L671)
- [LBRouter.sol:111](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L111)
- [LBRouter.sol:134](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L134)
- [LBRouter.sol:164](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L164)
- [LBRouter.sol:165](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L165)
- [LBRouter.sol:179](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L179)
- [FeeHelper.sol:59](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L59)
- [FeeHelper.sol:85](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L85)
- [FeeHelper.sol:150](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L150)

## **4. Avoid the possibility of an evil factory**

The method  `removeLiquidity` in `LBRouter` allows to change the tokens in an insecure way if they are out of order, which allows a factory that returns a pair of totally different tokens for two given tokens, to eliminate liquidity despite using different tokens.

```diff
    function removeLiquidity(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint16 _binStep,
        uint256 _amountXMin,
        uint256 _amountYMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address _to,
        uint256 _deadline
    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {
        ILBPair _LBPair = _getLBPairInformation(_tokenX, _tokenY, _binStep);
        if (_tokenX != _LBPair.tokenX()) {
+           if (_tokenX != _LBPair.tokenY() || _tokenY != _LBPair.tokenX()) revert();
            (_tokenX, _tokenY) = (_tokenY, _tokenX);
            (_amountXMin, _amountYMin) = (_amountYMin, _amountXMin);
        }

        (amountX, amountY) = _removeLiquidity(_LBPair, _amountXMin, _amountYMin, _ids, _amounts, _to);
    }
```

**Affected source code:**

- [LBRouter.sol:286-289](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L286-L289)

---

# Non critical

## **5. Avoid hardcoded values**

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code.

**Affected source code:**

- [LBQuoter.sol:85-89](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L85-L89)
- [LBQuoter.sol:163-166](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L163-L166)
- [JoeLibrary.sol:37-39](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L37-L39)
- [JoeLibrary.sol:51-52](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/JoeLibrary.sol#L51-L52)

