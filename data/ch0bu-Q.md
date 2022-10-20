# NON-CRITICAL

## 1. Use a more recent version of Solidity

- Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)` 
- Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`
- Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

```
All contracts
```

## 2. Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```
8		event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol
```
8		event Approval(address indexed owner, address indexed spender, uint256 value);
9		event Transfer(address indexed from, address indexed to, uint256 value);
49		event Mint(address indexed sender, uint256 amount0, uint256 amount1);
50		event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
51		event Swap(
52			address indexed sender,
53			uint256 amount0In,
54			uint256 amount1In,
55			uint256 amount0Out,
56			uint256 amount1Out,
57			address indexed to
58		);
59		event Sync(uint112 reserve0, uint112 reserve1);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol
```
34		event FeeRecipientSet(address oldRecipient, address newRecipient);
36		event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);
38		event FeeParametersSet(
39			address indexed sender,
40			ILBPair indexed LBPair,
41			uint256 binStep,
42			uint256 baseFactor,
43			uint256 filterPeriod,
44			uint256 decayPeriod,
45			uint256 reductionFactor,
46			uint256 variableFeeControl,
47			uint256 protocolShare,
48			uint256 maxVolatilityAccumulated
49		);
51		event FactoryLockedStatusUpdated(bool unlocked);
53		event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);
55		event LBPairIgnoredStateChanged(ILBPair indexed LBPair, bool ignored);
57		event PresetSet(
58			uint256 indexed binStep,
59			uint256 baseFactor,
60			uint256 filterPeriod,
61			uint256 decayPeriod,
62			uint256 reductionFactor,
63			uint256 variableFeeControl,
64			uint256 protocolShare,
65			uint256 maxVolatilityAccumulated,
66			uint256 sampleLifetime
67		);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol
```
111		event FlashLoan(
112			address indexed sender,
113			address indexed recipient,
114			uint256 amountX,
115			uint256 amountY,
116			uint256 feesX,
117			uint256 feesY
118		);
148		event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
150		event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
152		event OracleSizeIncreased(uint256 previousSize, uint256 newSize);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol
```
19		event ApprovalForAll(address indexed account, address indexed sender, bool approved);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol

## 3. Missing event for critical parameter change


Events help non-contract tools to track changes, and events prevent users from being surprised by changes

```
59		function setPendingOwner(address pendingOwner_) public override onlyOwner {
60			if (pendingOwner_ == address(0)) revert PendingOwnable__AddressZero();
61			if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();
62			_setPendingOwner(pendingOwner_);
63		}
...
68		function revokePendingOwner() public override onlyOwner {
69			if (_pendingOwner == address(0)) revert PendingOwnable__NoPendingOwner();
70			_setPendingOwner(address(0));
71		}
...
75		function becomeOwner() public override onlyPendingOwner {
76			_transferOwnership(msg.sender);
77		}
...
84		function renounceOwnership() public override onlyOwner {
85			_transferOwnership(address(0));
86		}
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol
```
788		function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory {
789			_setFeesParameters(_packedFeeParameters);
790		}
```
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol


