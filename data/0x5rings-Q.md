Findings:  Lack of zero address heck for _feeRecipient

Source: https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L64

Mitigation: add zero address validation in constructor for _feeRecipient


-----