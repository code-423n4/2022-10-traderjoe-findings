## Low & QA
1. Missing sanity checks on `to` addresses in `LBRouter.sol`
2. Rug vectors by the owner
3. All tokens send to a pair that are not immediately used can be stolen
4. Potential loss of funds on tokens with big supplies
5. In `TokenHelper.sol` the `safeTransfer` function does not check for potentially self-destroyed tokens 

### 1. Missing sanity checks on `to` addresses in `LBRouter.sol`

All the public/external functions in `LBRouter.sol` require an address `to` as a parameter to which to send either tokens, LBtokens or ETH.
When tokens or LBtokens are sent the protocol should check that if the `to` address is contract then that contract should is able to manage `ERC20/LBTokens`, otherwise funds would be lost.

### 2. Rug vectors by the owner

A malicious owner can call `setLBPairImplementation()`, `setFeeRecipient()`, `setFlashLoanFee()` , `setFeesParameters()` and `forceDecay()` to advantage himself at expenses of the users.

- `setLBPairImplementation()`: can be used to silently frontun a pair creation by swapping the implementation with a malicious one and stealing potentially any deposit.
- `setFeeRecipient()`: can be used to steal all of the protocol fees not yet collected.
- `setFlashLoanFee()`: can be used to frontrun a flashloan by increasing the fee, if the flashloan returns the fee based on the callback parameters.
- `setFeesParameters()`: can set the protocol fee to the max 25% and gets the funds for himself in combination with `setFeeRecipient()`.
- `forceDecay()`: can be used to advantage himself in trades.

As a mitigation add a timelock and make sure the owner is a multisig and not an EOA.

### 3. All tokens send to a pair that are not immediately used can be stolen

If extra tokens are sent the a pair contract either by mistake or intentionally and they are not used immetiately (calling either `mint()`, `burn()` or `swap()`) they become available for anybody to frontrun and claim by simply calling `mint()` and `burn()`.

### 4. Potential loss of funds on tokens with big supplies

`swap()` and `mint()` both reverts if either `2^112` or `2^128` tokens are sent to the pair. This would result in the funds being stuck and nobody being able to mint or swap. Submitting as low because the cost of attack is extremely high, but it's good to be aware of it.

### 5. In `TokenHelper.sol` the `safeTransfer` function does not check for potentially self-destroyed tokens.

If a pair gets created and after a while one of the tokens gets self-destroyed (maybe because of a bug) then `safeTransfer` would still succeed. It's probably a good idea to check if the contract still exists by checking the bytecode length.
