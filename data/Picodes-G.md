[GAS-01] Intermediate variable is not needed

[Here](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L137) `msg.sender` is used only once so there is no need for `_spender`
