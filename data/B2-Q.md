## Missing 0 address check 

If contract miss this zero check address validation there is chance that contract will loose some functionality, which can cause the redeployment of contract.

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L261
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L846
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L863
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L882
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485

## Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

Navigate to the following contracts.

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L38
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L208
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L394

## Avoid using outdated version Openzeppelin library

Current library used is 4.6.0, upgrade to latest version.

## Mapping values needs to be `locked` or set their value to `false`, instead of using `delete`

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L696
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L407

##  `Event` is missing `indexed` fields
    
 Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#L152
* https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#L59

##  Use a more recent version of solidity
  
Use a solidity version of at least 0.8.2 to get compiler automatic in lining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

* https://github.com/code-423n4/2022-10-traderjoe/tree/main/src

All contracts present inside the folder uses solidity version 0.8.0, upgrade to latest version.