## [L-1] owner can renounce Ownership

Trader Joe contracts implements a renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

***File : PendingOwnable.sol***


## [N-2] Constants should be defined rather than using magic numbers.

Even assembly can benefit from using readable constants instead of hex/numeric literals.

***File : LBFactory.sol***


LBFactory.sol#L285
LBFactory.sol#L364
LBFactory.sol#L375
LBFactory.sol#L403


***File : LBPair.sol***

LBPair.sol#L899


***File : LBRouter.sol***

LBRouter.sol#L725
LBRouter.sol#L791
LBRouter.sol#L794
LBRouter.sol#L891
LBRouter.sol#L896

***File : LBQuoter.sol***

LBQuoter.sol#L85


## [N-3] Use a more recent version of solidity.

Use a solidity version of at least 0.8.2 to get compiler automatic inlining,Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads,Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings,Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value



## [N-4] Use of block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

***File : Oracle.sol***

Oracle.sol#L118

***File : LBPair.sol***

LBPair.sol#L208
LBPair.sol#L394

***File : FeeHelper.sol***

FeeHelper.sol#L56
FeeHelper.sol#L72

***File : Samples.sol***

Samples.sol#L46
Samples.sol#L58

***File : LBRouter.sol***

LBRouter.sol#L38
LBRouter.sol#L38




## [N-5] Require() should be used instead of assert().

The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

***File : LBFactory.sol***

LBFactory.sol#L141
