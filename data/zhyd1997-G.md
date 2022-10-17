1. `++i` use less gas than `i++`:

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177

**Before**:

![LBQuoter.sol before](https://ik.imagekit.io/1winv85cn8g/C4/2022/10/traderjoe/LBQuoter-gas-before_rTO3v-iVa.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665974780146)

**After**:

![LBQuoter.sol after](https://ik.imagekit.io/1winv85cn8g/C4/2022/10/traderjoe/LBQuoter-gas-after_3e4zxz5GN.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665974799698)