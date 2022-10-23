1. Cheaper foor loops

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

```
for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
// Do the thing
// Unchecked pre-increment is cheapest
unchecked { ++i; }
}     
```

POC :

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674

2. Useage of uint / int smaller than 32 bytes incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

POC :

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L107
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L108
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L426
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L427
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L428
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L429
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L430
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L431
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L432
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L433

3. use `abi.encodepacked` for gas optimization

Changing the `abi.encode` function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.

POC :

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265
