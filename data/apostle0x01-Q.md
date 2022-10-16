### [L-01]  Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

### POC

```solidity
src/interfaces/IJoePair.sol:49:    event Mint(address indexed sender, uint256 amount0, uint256 amount1);
src/interfaces/IJoePair.sol:50:    event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
src/interfaces/ILBFactory.sol:34:    event FeeRecipientSet(address oldRecipient, address newRecipient);
src/interfaces/ILBPair.sol:152:    event OracleSizeIncreased(uint256 previousSize, uint256 newSize);

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L49
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/IJoePair.sol#L50
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L34
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L152



