## 1) Missing 0-address check

Not checking if an address is 0-address or not, may break the protocol, when by accident if the address provided is 0-addressed. So to prevent such catastrophe, presence of 0-address check is important.
 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63 :  `_feeRecipient`
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215 : `_LBPairImplementation`
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L41-L43: `_routerV2`,`_factoryV1`,`_factoryV2`

## 2) Admin rug vectors

Some admin/onlyowner functions have the capability to cripple the protocol. A malicious owner/admin can possibly retrieve all the fees generated, can blacklist/whitelist any token at it's will, and can also lock the factory. Such power given to the admin/owner function is dangerous. 
This may also impact the reputation of the project if not properly documented and not token proper care.
Like multisig wallets should be used for admin/owner address, and timelocks should be implemented on admin functions, so that users get enough time to take action on malicious admin transactions.
 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L312 : `setLBPairIgnored`
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468 : `setFeeRecipient`
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485 : `setFactoryLockedState`
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L493: `addQuoteAsset`


## 3) Mapping values needs to be locked or set their value to false, instead of using `delete` on them(which don't actually deletes the data structure)

This may cause unintended issues, when the deleted mapping values are re-used, possibly breaking the logic.
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L407
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L696

## 4) Missing storage gap in upgradeable contract

Storage gaps are important for upgradeable contracts to allows future development. If the gap is not provided , then the possibility of vulnerabilities like storage collision arises.
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L27

## 5) Dependence on block.timestamp

`block.timestamp` is riskier to use, as they can be manipulated by miners. So we should avoid such attributes.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L208
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L394
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L38

## 6) Use the latest version of Openzeppelin library instead of an outdated library

The current library used is 4.6.0 which is not the latest version. So to avoid any security issue, update to the latest version

## 7) No max fee is set on `setFlashLoanFee` function

There is no max fee set. This allows a malicious owner to set a very high amount of flashloan fee , which allows the malicious admin to grief other users. He can also favour selected users, by setting the `flashLoanFee` to 0. 
It is recommended to place an upper limit on the fee.
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474-L481

