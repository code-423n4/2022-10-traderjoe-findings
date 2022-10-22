### L-01 REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

*Number of Instances Identified: 1*

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol

```
141: assert(_binStep == _preset.decode(type(uint16).max, _shift));
```


### L-02 FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

This is applicable for all smart contracts


### N-01 EVENTS MISSING INDEXED FIELDS

*Number of Instances Identified: 18*

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol

```
8: event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol

```
8: event Approval(address indexed owner, address indexed spender, uint256 value);
9: event Transfer(address indexed from, address indexed to, uint256 value);
49: event Mint(address indexed sender, uint256 amount0, uint256 amount1);
50: event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
59: event Sync(uint112 reserve0, uint112 reserve1);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol

```
34: event FeeRecipientSet(address oldRecipient, address newRecipient);
36: event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);
41: uint256 binStep,
51: event FactoryLockedStatusUpdated(bool unlocked);
53: event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation)
55: event LBPairIgnoredStateChanged(ILBPair indexed LBPair, bool ignored);
57: event PresetSet
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol

```
111: event FlashLoan
148: event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
150: event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
152: event OracleSizeIncreased(uint256 previousSize, uint256 newSize);
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol

```
19: event ApprovalForAll(address indexed account, address indexed sender, bool approved);
```


### N-02 NO EVENT IS RAISED

*Number of Instances Identified: 5*

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol

```
59: function setPendingOwner(address pendingOwner_) public override onlyOwner
68: function revokePendingOwner() public override onlyOwner {
75: function becomeOwner() public override onlyPendingOwner {
84: function renounceOwnership() public override onlyOwner
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol

```
788: function setFeesParameters(bytes32 _packedFeeParameters) external override onlyFactory
```


### N-03 USE LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17