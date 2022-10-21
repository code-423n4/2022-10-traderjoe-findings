#### Earned interest to tokens in pool can be stolen

##### Severity: Low

##### Context:

- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L312

##### Description:

In `LBPair.swap`, the contract token balance is checked to confirm that the user executing the swap sent in a sufficient amount of tokens. It's possible that in the case of a non-standard ERC-20 token, an attacker may call a method on the token to accrue interest to the pool contract, such that the balance increases sufficiently for them to execute their swap without actually transferring any tokens.

##### Remediation:

It is recommended that the possible effects of non-standard ERC-20 tokens are well documented to minimize loss of user funds.