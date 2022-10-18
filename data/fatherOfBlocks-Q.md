**LBRouter**
- L72/73/80/81 - The inputs in the function are passed in a different order than how it is used in the library, the inputs should be passed in another order for a logical question of use.

- L360/362/545/576 - In the function swapExactTokensForTokens() it is not validated before performing the safeTransferFrom() that the recipient is != 0, this is important since it would avoid transferring tokens to address 0.


**LBQuoter**
- L121/201 - Within a cycle for a division by citation.amounts[i] is performed, the problem with this is that a value within the citation could be 0, if this happens it would revert without explaining the reason. It should be validated before, to revert with a correct message so that users know the reason for the revert.


**LBErrors**
- L5 - The ILBPair interface is imported, but it is never used in the contract.


**libraries/FeeDistributionHelpe**
- L58 - A division is made by an input, _totalSupply, this should be validated with a require or an if with a custom error so that users understand the reason for the revert, if it happens.


**libraries/PendingOwnable**
- L36 - It is validated that msg.sender is == 0, this does not make sense since address 0 does not have a private key, therefore a contract could never be called from that address.

