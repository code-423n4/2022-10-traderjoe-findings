## [L-01] Must be immutable
## Code Snipped
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L34
## Recommendation
the variable state can't be initialize by constructor due to missing immutable tag. the constructor parameter mention some variables state to initialize the value. so i suggest to add immutable to the variables .


## [L-02] Missing zero address for constructor 
## Code Snipped
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L40-L43 
## Recommendation
Checking addresses against zero-address during initialization in constructor is a security best-practice. However, such checks are missing in multiple constructors.  Allowing zero-addresses will lead to contract reverts and force redeployments if there are no setters for such address variables. So we recommend to Add zero-address checks in the constructors.

## [L-03] Prevent div by 0
## Code Snipped
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L49
## Recommendation
Division by 0 can lead to accidentally revert. so add check value can't be zero
