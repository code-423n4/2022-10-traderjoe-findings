# LBPair.ming()SHOULD USE MEMORY INSTEAD OF STORAGE VARIABLE
```        _mintInfo.amountXIn = tokenX.received(_pair.reserveX, _pair.feesX.total).safe128();
        _mintInfo.amountYIn = tokenY.received(_pair.reserveY, _pair.feesY.total).safe128();
                tokenX.safeTransfer(_to, _mintInfo.amountXIn - _amountAddedPlusFee);
                tokenY.safeTransfer(_to, _mintInfo.amountYIn - _amountAddedPlusFee);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L490-L491
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L598
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L603
Fixed by using memory variable instead of  (storage variable).

