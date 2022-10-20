### [L-01] FLOATING PRAGMA IS USED

There are 12 instances of this issue:

> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L03
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L03
> ** File :  **  =>  https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Constants.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math128x128.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L03
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L03


#### Recommended Mitigation
its recommended to lock pragma. Contract should deploy with that compiler version with whom it was tested


### [L-02] Reading state variable but function visibility is Pure

There are 2 instances of this issue :
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L49
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L55

#### Recommended Mitigation
Make it view


### [L-03] Absence of Zero address check for critical state variable addresses

There are 5 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L57
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L58
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L59
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63
> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L68
 
#### Recommended Mitigation
There should be require() that validate a perticular address is zero address or not


### [L-04] Should be a upper-bound on Flash-loan fee

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63-L-68

#### Recommended Mitigation
There should be a upper bound of Flash-loan fee define in contract so that owner can't able to set fee more than that value.


### [L-05] Centralization :: Owner can change flashloan fee anytime, which can result cause of front-running

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63-L-68

#### Recommended Mitigation
There should be a DAO or a timelock system that help user to decide whether they are ok with fee or not. 



### [L-06] assert() used

When assert() condition fails, it consume all remain gas where on other had on failure require() / revert() sends back all remaining gas to caller. 

There are 1 instances of this issue:


> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141

#### Recommended Mitigation
So on gas saving point of view use of require() / revert() is more efficient.


### [L-07] _transferOwnerShip() doesn't check newOwner != oldOwner or not

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L91-L96

#### Recommended Mitigation
There should be a check that newOwner != oldOwner



### [N-01] Should emit Events on critical state variable change

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215

#### Recommended Mitigation
There should be event emission on important state variable change.


### [N-02] SafeMath library imported

There are 1 instances of this issue:

> ** File :  **  => https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L07

#### Recommended Mitigation
SafeMath library used for checking arithmatic over/underflow condition which come build in with solidity compiler version 0.8.0 and above it. So no need to import this library.

