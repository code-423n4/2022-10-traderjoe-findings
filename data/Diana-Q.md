
## L-01  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

There are multiple instances of this issue

Some instances include:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L3

```
File: src/LBFactory.sol

3: pragma solidity ^0.8.0;
```


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L3

```
File: src/LBPair.sol

3: pragma solidity ^0.8.0;
```

------------

## L-02  REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

### Proof of Concept

There is 1 instance of this issue

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141

```
File: src/LBFactory.sol

141: assert(_binStep == _preset.decode(type(uint16).max, _shift));
```

-----

## N-01  EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

### Proof of Concept

There are 19 instances of this issue

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#L8

```
File: src/interfaces/IJoeFactory.sol

8: event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
```


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol

```
File: src/interfaces/IJoePair.sol

8:     event Approval(address indexed owner, address indexed spender, uint256 value);
9:     event Transfer(address indexed from, address indexed to, uint256 value);
49:    event Mint(address indexed sender, uint256 amount0, uint256 amount1);
50:    event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
51-58: event Swap(
        address indexed sender,
        uint256 amount0In,
        uint256 amount1In,
        uint256 amount0Out,
        uint256 amount1Out,
        address indexed to
    );
59:   event Sync(uint112 reserve0, uint112 reserve1); 
```


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol

```
File: src/interfaces/ILBFactory.sol

34:    event FeeRecipientSet(address oldRecipient, address newRecipient);
36:    event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);
38-49: event FeeParametersSet(
        address indexed sender,
        ILBPair indexed LBPair,
        uint256 binStep,
        uint256 baseFactor,
        uint256 filterPeriod,
        uint256 decayPeriod,
        uint256 reductionFactor,
        uint256 variableFeeControl,
        uint256 protocolShare,
        uint256 maxVolatilityAccumulated
    );
51:    event FactoryLockedStatusUpdated(bool unlocked);
53:    event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);
55:    event LBPairIgnoredStateChanged(ILBPair indexed LBPair, bool ignored);
57-67: event PresetSet(
        uint256 indexed binStep,
        uint256 baseFactor,
        uint256 filterPeriod,
        uint256 decayPeriod,
        uint256 reductionFactor,
        uint256 variableFeeControl,
        uint256 protocolShare,
        uint256 maxVolatilityAccumulated,
        uint256 sampleLifetime
    );
```


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol

```
File: src/interfaces/ILBPair.sol

111-118: event FlashLoan(
        address indexed sender,
        address indexed recipient,
        uint256 amountX,
        uint256 amountY,
        uint256 feesX,
        uint256 feesY
    );
148: event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
150: event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
152: event OracleSizeIncreased(uint256 previousSize, uint256 newSize);
```


https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol#L19

```
File: src/interfaces/ILBToken.sol

19: event ApprovalForAll(address indexed account, address indexed sender, bool approved);
```


--------
