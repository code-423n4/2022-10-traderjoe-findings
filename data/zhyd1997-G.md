1. `++i` use less gas than `i++`:

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177

![LBQuoter.sol diff](https://ik.imagekit.io/1winv85cn8g/C4/2022/10/traderjoe/increment-diff_h3D01mGnZ.png?ik-sdk-version=javascript-1.4.3&updatedAt=1666230821097)

2. sort state variables from smallest to biggest types can safe gas (~ 10%)

![LBToken.sol](https://ik.imagekit.io/1winv85cn8g/C4/2022/10/traderjoe/LBToken-state-variables-lift-up_uAN7-N7Pt.png?ik-sdk-version=javascript-1.4.3&updatedAt=1666229784811)

![LBFactory.sol](https://ik.imagekit.io/1winv85cn8g/C4/2022/10/traderjoe/LBFactory-state-variables-lift-up_KEoojd4Ub.png?ik-sdk-version=javascript-1.4.3&updatedAt=1666230297504)

![gas diff](https://ik.imagekit.io/1winv85cn8g/C4/2022/10/traderjoe/state-variables-sort-gas-diff_7URVIjoZK.png?ik-sdk-version=javascript-1.4.3&updatedAt=1666230580889)