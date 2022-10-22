#### Avoid emitting events inside loops

##### Severity: Gas optimization

##### Context:

- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L342
- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L581
- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L665

##### Description:

In `swap`, `mint` and `burn`, a loop is run to execute logic on each bin involved in the transaction. Within this loop, events are emitted. It is recommended that instead of emitting these duplicate events many times per transaction, the events are removed from the loop and modified to emit a total of all actions completed within the loop.

#### Don't attempt fee transfer if fees to collect in `collectFees` are 0

##### Severity: Gas optimization

##### Context:

- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L716
- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L723

##### Description:

In `LBPair.collectFees`, checks are performed to see if `amountX` and `amountY` are greater than 0. However, outside of these checks, a `safeTransfer` is attempted which will simply spend a small amount of gas if the amount to transfer is zero. This can easily be avoided by placing the `safeTransfer` methods within the checks that `amountX` and `amountY` are greater than 0.

#### Set `_mintInfo.totalDistributionX/Y` as the value of `_mintInfo.distributionX/Y` instead of incrementing from 0

##### Severity: Gas optimization

##### Context:

- https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L505

##### Description:

In `LBPair.mint`, `_mintInfo.totalDistributionX/Y` are initialized as 0, then subsequently incremented to the value `_mintInfo.distributionX/Y`. Since the value of `_mintInfo.totalDistributionX/Y` will always 0 at this stage, instead of incrementing we can simply do `_mintInfo.totalDistributionX/Y = _mintInfo.distributionX/Y`.
