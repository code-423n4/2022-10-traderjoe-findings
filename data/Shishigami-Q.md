# **Q&A report**


## [QA-1] Public function not called by contract should be external

1. LBToken.sol
  - function: `name` [LBToken.sol#49](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L49)
  - function: `symbol` [LBToken.sol#55](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L55)
  - function: `balanceOfBatch` [LBToken.sol#80](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L80)
  - function: `isApprovedForAll` [LBToken.sol#115](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L115)
  - function: `setApprovalForAll` [LBToken.sol#122](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L122)


2. PendingOwnable.sol
  - function: `pendingOwner` [PendingOwnable.sol#53](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L53)
  - function: `setPendingOwner` [PendingOwnable.sol#59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L59)
  - function: `revokePendingOwner` [PendingOwnable.sol#68](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L68)
  - function: `becomeOwner` [PendingOwnable.sol#75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L75)
  - function: `renounceOwnership` [PendingOwnable.sol#84](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L84)



## [QA-2] Typo

[LBRouter.sol#67](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L67) inputted into input
[LBRouter.sol#76](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L76) inputted into input
