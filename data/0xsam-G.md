# Gas Optimization


## ++i costs less gas than i++ and i+=1 (--i/i--/i-=1 too)

        File: src/LBQuoter.sol

        for (uint256 i; i < swapLength; i++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75

        File: src/LBQuoter.sol

                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100

        File: src/LBQuoter.sol

                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177

        File: src/LBRouter.sol

        for (uint256 i = _pairs.length; i != 0; i--) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711

        File: src/LBQuoter.sol#L177

        for (uint256 i = swapLength; i > 0; i--) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154

        File: src/libraries/BitMath.sol

                msb += 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L100

        File: src/libraries/BitMath.sol

                lsb += 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BitMath.sol#L139

        File: src/libraries/Math512Bits.sol

            if (mulmod(x, y, 1 << offset) != 0) result += 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L99

        File: src/libraries/Math512Bits.sol

            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math512Bits.sol#L159


## Else case costs more gas than assigning default value.

        File: src/libraries/FeeHelper.sol

            } else {
                _fp.volatilityReference = 0;
            }

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L67-L69


## Array length should not be looked up in every loop. Instead, use a variable to store the length before the loop starts.

        File: src/LBToken.sol

            for (uint256 i; i < _accounts.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90

        File: src/LBToken.sol

            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L163

        File: src/LBRouter.sol

            for (uint256 i; i < depositIds.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L674

        File: src/LBRouter.sol

        for (uint256 i = _pairs.length; i != 0; i--) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711

        File: src/LBRouter.sol

            for (uint256 i; i < _pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L778

        File: src/LBRouter.sol

            for (uint256 i; i < _pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L831

        File: src/LBRouter.sol

            for (uint256 i; i < _pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L878

        File: src/LBRouter.sol

            for (uint256 i; i < pairs.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L951

        File: src/LBQuoter.sol

                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100

        File: src/LBQuoter.sol

                for (uint256 j; j < LBPairsAvailable.length; j++) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177

        File: src/LBPair.sol

            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274

        File: src/LBPair.sol

            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L496

        File: src/LBPair.sol

            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L623

        File: src/LBPair.sol

            for (uint256 i; i < _ids.length; ++i) {

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L701











