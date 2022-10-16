## `constant` Variables Should be Capitalized
Constants should be named with all capital letters with underscores separating words if need be. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29-L30

Please visit the following style guide for more details:

https://docs.soliditylang.org/en/v0.8.14/style-guide.html

## Inadequately Commented Assembly Block
Assembly block is used in numerous contracts of Trader Joe. While this does not pose a security risk per se, it is at the same time a complicated and critical part of the system. Moreover, as this is a low-level language that is harder to parse by readers, consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code less safe and more error-prone.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L980-L987
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L998-L1005
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1028-L1031

## External Call in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Failed Transfer Could Occur Without Contract Existence Check
Performing transfers with a low-level call without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no transfer was executed. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L963-L966

## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L208
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L38

## Not Completely Using OpenZeppelin Contracts
OpenZeppelin maintains a library of standard, audited, community-reviewed, and battle-tested smart contracts. Instead of always importing these contracts, Trader Joe project re-implements them in some cases. This increases the amount of code that the Trader Joe team will have to maintain and miss all the improvements and bug fixes that the OpenZeppelin team is constantly implementing with the help of the community.

Consider importing the OpenZeppelin contracts instead of re-implementing or copying them. These contracts can be extended to add the extra functionalities required by Blur Exchange. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol

## Use `delete` to Clear Variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown).

The delete key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with delete statements. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L68

## OpenZeppelin's Initializable
`initialize()` of `LBPair.sol` uses `(address(tokenX) != address(0))` to prevent re-initialization of majority of the function parameters. 

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L112

Consider using OpenZeppelin's Initializable which will have this scenario better taken care of. When a contract is initialized, its `isInitialized` state variable is set to true.

## Lack of Events for Critical Operations
Critical operations not triggering events will make it difficult to review the correct behavior of the deployed contracts. Users and blockchain monitoring systems will not be able to detect suspicious behaviors at ease without events. Consider adding events where appropriate for all critical operations for better support of off-chain logging API. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L905
