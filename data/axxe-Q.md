### createdByOwner is Useless

This field from the `LBPairInformation` struct, is not used on-chain. Otherwise, it can be added to the `LBPairCreated` event.

[Link](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L273)

### createLBPair missing check _tokenB

Need the same check as `_tokenA` with the right error, because it will fail with `LBFactory__QuoteAssetNotWhitelisted` .

[Link](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L254)