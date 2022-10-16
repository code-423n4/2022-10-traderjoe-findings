# 2022-10-TRADERJOE

## Low Risk And Non-Critical Issues

### `public` functions not called by the contract should be declared `external` instead

_There are **18** instances of this issue:_

```solidity
File: src/LBFactory.sol

38:   /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBQuoter.sol

54:   function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)

134:  function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```solidity
File: src/LBToken.sol

49:   function name() public pure virtual override returns (string memory) {

55:   function symbol() public pure virtual override returns (string memory) {

63:   function totalSupply(uint256 _id) public view virtual override returns (uint256) {

100:  function userPositionAtIndex(address _account, uint256 _index) public view virtual override returns (uint256) {

107:  function userPositionNumber(address _account) public view virtual override returns (uint256) {

131:  function safeTransferFrom(

149:  function safeBatchTransferFrom(
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

```solidity
File: src/libraries/PendingOwnable.sol

47:   function owner() public view override returns (address) {

53:   function pendingOwner() public view override returns (address) {

59:   function setPendingOwner(address pendingOwner_) public override onlyOwner {

68:   function revokePendingOwner() public override onlyOwner {

75:   function becomeOwner() public override onlyPendingOwner {

80:   /// `onlyOwner` functions anymore. Can only be called by the current owner.

83:   /// thereby removing any functionality that is only available to the owner.

84:   function renounceOwnership() public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol

### Non-library/interface files should use fixed compiler versions, not floating ones

_There are **24** instances of this issue:_

```solidity
File: src/LBFactory.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBPair.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```solidity
File: src/LBQuoter.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```solidity
File: src/LBRouter.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```solidity
File: src/LBToken.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

```solidity
File: src/interfaces/IPendingOwnable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IPendingOwnable.sol

```solidity
File: src/libraries/BitMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol

```solidity
File: src/libraries/Buffer.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Buffer.sol

```solidity
File: src/libraries/Decoder.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Decoder.sol

```solidity
File: src/libraries/Encoder.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Encoder.sol

```solidity
File: src/libraries/FeeDistributionHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol

```solidity
File: src/libraries/FeeHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol

```solidity
File: src/libraries/JoeLibrary.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol

```solidity
File: src/libraries/Math128x128.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol

```solidity
File: src/libraries/Math512Bits.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol

```solidity
File: src/libraries/Oracle.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol

```solidity
File: src/libraries/PendingOwnable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol

```solidity
File: src/libraries/ReentrancyGuardUpgradeable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol

```solidity
File: src/libraries/SafeCast.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol

```solidity
File: src/libraries/SafeMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeMath.sol

```solidity
File: src/libraries/Samples.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol

```solidity
File: src/libraries/SwapHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol

```solidity
File: src/libraries/TokenHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol

```solidity
File: src/libraries/TreeMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol

### Event is missing `indexed` fields

_There are **8** instances of this issue:_

```solidity
File: src/interfaces/IJoeFactory.sol

8:    event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol

```solidity
File: src/interfaces/IJoePair.sol

8:    event Approval(address indexed owner, address indexed spender, uint256 value);

9:    event Transfer(address indexed from, address indexed to, uint256 value);

49:   event Mint(address indexed sender, uint256 amount0, uint256 amount1);

50:   event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol

```solidity
File: src/interfaces/ILBPair.sol

148:  event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);

150:  event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol

```solidity
File: src/interfaces/ILBToken.sol

19:   event ApprovalForAll(address indexed account, address indexed sender, bool approved);
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBToken.sol

### Use a more recent version of solidity

- Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining \ - Use a solidity version of at least 0.8.3 to get cheaper multiple storage reads \ - Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings \ - Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are **37** instances of this issue:_

```solidity
File: src/LBErrors.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol

```solidity
File: src/LBFactory.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol

```solidity
File: src/LBPair.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```solidity
File: src/LBQuoter.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```solidity
File: src/LBRouter.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```solidity
File: src/LBToken.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

```solidity
File: src/interfaces/IJoeFactory.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol

```solidity
File: src/interfaces/IJoePair.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol

```solidity
File: src/interfaces/IJoeRouter01.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeRouter01.sol

```solidity
File: src/interfaces/IJoeRouter02.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeRouter02.sol

```solidity
File: src/interfaces/ILBFactory.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBFactory.sol

```solidity
File: src/interfaces/ILBFlashLoanCallback.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBFlashLoanCallback.sol

```solidity
File: src/interfaces/ILBPair.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol

```solidity
File: src/interfaces/ILBRouter.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBRouter.sol

```solidity
File: src/interfaces/ILBToken.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBToken.sol

```solidity
File: src/interfaces/IPendingOwnable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IPendingOwnable.sol

```solidity
File: src/interfaces/IWAVAX.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IWAVAX.sol

```solidity
File: src/libraries/BinHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BinHelper.sol

```solidity
File: src/libraries/BitMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/BitMath.sol

```solidity
File: src/libraries/Buffer.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Buffer.sol

```solidity
File: src/libraries/Constants.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Constants.sol

```solidity
File: src/libraries/Decoder.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Decoder.sol

```solidity
File: src/libraries/Encoder.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Encoder.sol

```solidity
File: src/libraries/FeeDistributionHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol

```solidity
File: src/libraries/FeeHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol

```solidity
File: src/libraries/JoeLibrary.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/JoeLibrary.sol

```solidity
File: src/libraries/Math128x128.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math128x128.sol

```solidity
File: src/libraries/Math512Bits.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Math512Bits.sol

```solidity
File: src/libraries/Oracle.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol

```solidity
File: src/libraries/PendingOwnable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol

```solidity
File: src/libraries/ReentrancyGuardUpgradeable.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol

```solidity
File: src/libraries/SafeCast.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeCast.sol

```solidity
File: src/libraries/SafeMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SafeMath.sol

```solidity
File: src/libraries/Samples.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol

```solidity
File: src/libraries/SwapHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/SwapHelper.sol

```solidity
File: src/libraries/TokenHelper.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TokenHelper.sol

```solidity
File: src/libraries/TreeMath.sol

3:    pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/TreeMath.sol