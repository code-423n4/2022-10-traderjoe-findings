#### [G-01] Use storage instead of memory 
When caching storage variable, it's more gas efficient to use `storage` rather than `memory`

src/LBFactory.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L197
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L320

src/LBPair.sol
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L283
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L310
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L318
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L327
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L426
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L430-L431
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L484-L486
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L498
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L706
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L817
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L850

Recommend using `storage`
