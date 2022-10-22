1.  The following function in `PendingOwnable.sol` can be declared as external (slither report)
```
pendingOwner() should be declared external:
	- PendingOwnable.pendingOwner() (src/libraries/PendingOwnable.sol#53-55)
setPendingOwner(address) should be declared external:
	- PendingOwnable.setPendingOwner(address) (src/libraries/PendingOwnable.sol#59-63)
revokePendingOwner() should be declared external:
	- PendingOwnable.revokePendingOwner() (src/libraries/PendingOwnable.sol#68-71)
becomeOwner() should be declared external:
	- PendingOwnable.becomeOwner() (src/libraries/PendingOwnable.sol#75-77)
renounceOwnership() should be declared external:
	- PendingOwnable.renounceOwnership() (src/libraries/PendingOwnable.sol#84-86)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#public-function-that-could-be-declared-external
```
Note: If you want to declare a constant whose value which always be 100.  you can decalre the varaible using a uint8. If you are worried if the calculations involving uint256 will be affected, check out the below example in remix
```
pragma solidity 0.8.7;
contract Test {
    uint256 valOne = 10000000000;
    uint8 valTwo = 1;
    uint16 valThree = 11111;
    uint256 public result = valOne + valTwo + valThree;
}
```

2. Rearrange the state variables to consumes less slots in `Constants.sol`

Originally 4 slots of storage was being consumed. Rearrange the variables and using uint8 and uint16 instead of uint256.

```
library Constants {
    uint8 internal constant SCALE_OFFSET = 128; //slot 0
    uint16 internal constant BASIS_POINT_MAX = 10_000; //slot 0
    uint256 internal constant SCALE = 1 << SCALE_OFFSET; //slot 1
    uint256 internal constant PRECISION = 1e18; //slot 2
}
```

3. Rearrange the state variables to consumes less slots in `LBFactory.sol` and minimize the slot length

```

    uint256 public constant override MAX_FEE = 0.1e18; // 10% -> SLOT0

    uint8 public constant override MIN_BIN_STEP = 1; // 0.01% -> SLOT1
    uint8 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons -> SLOT1

    uint16 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25% -> SLOT1

    /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
    bool public override creationUnlocked; -> SLOT1

    address public override LBPairImplementation; -> SLOT1

    address public override feeRecipient; -> SLOT2

    uint256 public override flashLoanFee; -> SLOT3

    ILBPair[] public override allLBPairs; 
```

4. Rearrange the state variables to consumes less slots in `LBPair.sol` and minimize the slot length
The following constants can be declared as uint8 which has a range(0,255) saving almost 8 slots of storage.

```
    uint8 private constant _OFFSET_PAIR_RESERVE_X = 24;
    uint8  private constant _OFFSET_PROTOCOL_FEE = 128;
    uint8 private constant _OFFSET_BIN_RESERVE_Y = 112;
    uint8 private constant _OFFSET_VARIABLE_FEE_PARAMETERS = 144;
    uint8 private constant _OFFSET_ORACLE_SAMPLE_LIFETIME = 136;
    uint8 private constant _OFFSET_ORACLE_SIZE = 152;
    uint8 private constant _OFFSET_ORACLE_ACTIVE_SIZE = 168;
    uint8 private constant _OFFSET_ORACLE_LAST_TIMESTAMP = 184;
    uint8 private constant _OFFSET_ORACLE_ID = 224;
```