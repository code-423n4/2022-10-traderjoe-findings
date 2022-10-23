### Gas Optimizations List
| Number | Optimization Details | Per gas saved | Context |
|:--:|:-------| :-----:| :-----:|
| [G-01] | Gas can be saved by changing the way the constructor defines its arguments | | 1 |
| [G-02] | Packing Storage |  | 1 |
| [G-03] | `Sample` struct can be packed with lower values |  |  |
| [G-04] | Multiple address mappings can be combined into a single mapping of an address to a struct |  |  |
| [G-05] | Sort Solidity operations using short-circuit mode |  | 2 |
| [G-06] | Define the value directly instead of getting it from the state variable |  | 4 |
| [G-07] | Using storage instead of memory for struct saves gas  |  | 2 |
| [G-08] | Usage of uints/ints smaller than 32 bytes (256 bits) saves gas |  |1 |
| [G-09] | Using ```private``` rather than ```public``` _for constants_, saves gas | 10 |1 |
| [G-10] | _Functions guaranteed to revert_ when callled by normal users can be marked `payable`| 24 | 17 |
| [G-11] | ```x -= y (x += y) ``` costs more gas than ```x = x – y (x = x + y)``` for state variables| 32 | 21 |
| [G-12] | WAWAX address definition can be use directly | ~500 | 1 |
| [G-13] |Optimize names to save gas | 22 | All contracts |
| [G-14] | Use assembly to check for ```address(0)``` | 6  | 18 |
| [G-15] | Use ``assembly`` to write _address storage values_ | 33  | 10 |
| [G-16] | The solady Library's Ownable contract is significantly gas-optimized, which can be used |   |  |
| [G-17] | Use ```++index ```  instead of ```index++``` to increment a loop counter| 5  | 3 |
| [G-18] | Removing unused functions saves gas |   | 1 |
| [G-19] |Avoid contract existence checks by using solidity version 0.8.10 or later |   |  |
| [G-20] | Using bools for storage incurs overhead |   | 1 |
| [G-21] | Setting the _constructor_ to `payable` | 13  | 5 |
| [G-22] | It is more efficient to use `abi.encodePacked` instead of `abi.encode` |  164 | 1 |
| [G-23] | Free gas savings for using solidity 0.8.10+ |   | All contracts |

Total 23 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Use `v4.8.0 OpenZeppelin` contracts |
| [S-02] |Missing `zero-address` check in `constructor |

Total 2 suggestions


### [G-01] Gas can be saved by changing the way the constructor defines its arguments

**Context:**
[LBPair.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L90)

**Description:**
When the constructor is inherited, the constructor arguments in the contract we inherit can be updated in two ways.

1 - Either specify directly in the inheritance list. (New use with Gas Optimization)
2- Or via a "modifier" of the derived constructor. (standard use)

In the contexts mentioned above, we get gas saved with this usage

```diff

- contract LBPair is LBToken {
+ contract LBPair is LBToken() {

- constructor(ILBFactory _factory) LBToken() {
+ constructor(ILBFactory _factory) {

        if (address(_factory) == address(0)) revert LBPair__AddressZero();
        factory = _factory;
    }
```
**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs

```js
    contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;

    function setUp() public {
        c0 = new Contract0(7);
        c1 = new Contract1();

    }

        function testGas() public {
            c0.set();
            c1.set();            
        }
}

contract LBToken {   
    uint public x; 
    uint public _x; 
    constructor() { x = _x; }
}

contract Contract0 is LBToken {
    constructor(uint _factory) LBToken() {}

    function set() public {
        
    }
}


contract Contract1 is LBToken() {
    constructor() {}

    function set() public {

    }
}    
```

**Gas Report:**
```js
Deployment cost difference can be checked
╭──────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞══════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 35523                                ┆ 285             ┆     ┆        ┆     ┆         │
╰──────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞══════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 35387                                ┆ 191             ┆     ┆        ┆     ┆         │
╰──────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯ 
```

### [G-02] Packing Storage

**Context:**
[LBFactory.sol#L25-L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30)

**Recommendation:**
Storing state variables packaging is more gas efficient.

_Current Code:_
```js
uint256 public constant override MAX_FEE = 0.1e18; 
uint256 public constant override MIN_BIN_STEP = 1; 
uint256 public constant override MAX_BIN_STEP = 100; 
uint256 public constant override MAX_PROTOCOL_SHARE = 2_500
```
_Recommendation Code:_
```js
uint64 public constant override MAX_FEE = 0.1e18; 
uint64 public constant override MIN_BIN_STEP = 1; 
uint64 public constant override MAX_BIN_STEP = 100; 
uint64 public constant override MAX_PROTOCOL_SHARE = 2_500
```

| Storage Variable | Slot | Offset | Length |
|:-------:|:-------:|:-------:|:-------:|
| MAX_FEE |  0 | 0 | 8 |
| MIN_BIN_STEP | 0 | 8 | 8 |
| MAX_BIN_STEP | 0 | 16 | 8 |
| MAX_PROTOCOL_SHARE | 0 | 24 | 8 |

### [G-03] `Sample` struct can be packed with lower values

**Description:**
While it's theoretically possible for values like timestamp and cumulativeId to get the maximum value of uint256, it won't be possible in practice.
For example ; timestamp in uint128 is sufficient
By packing the struct as below, 1 slot is used less.

uint128 = mx value ; 24 April 3048 (1026 years)

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

**Context:**
```js
Src/libraries/Oracle.sol:

 struct Sample {
        uint256 timestamp;        			// Slot 0
        uint256 cumulativeId; 			// Slot 1
        uint256 cumulativeVolatilityAccumulated;	// Slot 2
        uint256 cumulativeBinCrossed;		// Slot 3
    }
```

**Recommendation:**
```js
 struct Sample {
        uint128 timestamp;			// Slot 0
        uint128 cumulativeId;			// Slot 0
        uint256 cumulativeVolatilityAccumulated;	// Slot 1
        uint256 cumulativeBinCrossed;		// Slot 2
    }
```

### [G-04] Multiple address mappings can be combined into a single mapping of an address to a struct

**Context:**
```js
src\LBFactory.sol:
  45:     mapping(IERC20 => mapping(IERC20 => mapping(uint256 => LBPairInformation))) private _LBPairsInfo;

  58:     mapping(IERC20 => mapping(IERC20 => bytes32)) private _availableLBPairBinSteps;
```

**Description:**
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

 Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.


### [G-05] Sort Solidity operations using short-circuit mode

**Context:**
[LBFactory.sol#L241](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L241)
[LBQuoter.sol#L184](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L184)

**Description:**
Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.
```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```
**Recommendation:**
```diff
src/LBFactory.sol:
+ 240 address _owner = owner();
- 241: if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);
+ 241: if (msg.sender != _owner && !creationUnlocked) revert LBFactory__FunctionIsLockedForUsers(msg.sender);
+ 242 
```
_Gas Report before recommendation:_
```js
╭──────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
│ src/LBFactory.sol:LBFactory contract ┆                 ┆        ┆        ┆        ┆         │
╞══════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 2086657                              ┆ 10631           ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ createLBPair                         ┆ 2107            ┆ 302016 ┆ 299901 ┆ 334301 ┆ 272     │
╰──────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
``` 
_Gas report after recommendation:_
```js
╭──────────────────────────────────────┬─────────────────┬────────┬────────┬────────┬─────────╮
│ src/LBFactory.sol:LBFactory contract ┆                 ┆        ┆        ┆        ┆         │
╞══════════════════════════════════════╪═════════════════╪════════╪════════╪════════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 2086457                              ┆ 10630           ┆        ┆        ┆        ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg    ┆ median ┆ max    ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ createLBPair                         ┆ 1990            ┆ 300976 ┆ 299784 ┆ 332184 ┆ 272     │
╰──────────────────────────────────────┴─────────────────┴────────┴────────┴────────┴─────────╯
```

### [G-06] Define the value directly instead of getting it from the state variable

**Context:**
[LBFactory.sol#L165](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L165)
[LBFactory.sol#L195](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195)
[LBFactory.sol#L543](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L543)
[LBFactory.sol#L554](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L554)

**Description:**
It consumes less gas to define the value directly instead of getting it from the state variable.

**Recommendation:**

Current Code:
```js
…code
    uint256 public constant override MIN_BIN_STEP = 1;
    uint256 public constant override MAX_BIN_STEP = 100;

if (_binStep < MIN_BIN_STEP || _binStep > MAX_BIN_STEP)
            revert LBFactory__BinStepRequirementsBreached(MIN_BIN_STEP, _binStep, MAX_BIN_STEP);
code…
```
Recommendation Code:
```js
...code

if (_binStep < 1 || _binStep > 100)
            revert LBFactory__BinStepRequirementsBreached(1, _binStep, 100);
code…
```

### [G-07] Using storage instead of memory for struct saves gas

**Context:**
[LBPair.sol#L310](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L310)
[LBPair.sol#L318](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L318)


**Description:**
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. 


**Recommendation:**
Current Code:
```js
LBPair.sol#L310
PairInformation memory _pair = _pairInformation; 

LBPair.sol#L318
FeeHelper.FeeParameters memory _fp = _feeParameters;
```
Recommendation Code:
```js
LBPair.sol#L310
PairInformation storage _pair = _pairInformation; 

LBPair.sol#L318
FeeHelper.FeeParameters storage _fp = _feeParameters;
```
### [G-08] Using uints/ints smaller than 32 bytes (256 bits) increases gas usage

**Context:**
[FeeDistributionHelper.sol#L31](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L31)


**Description:**
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

**Recommendation:**

Current Code:
```js
uint128 _totalFees = _pairFees.total;
```
Recommendation Code:
```js
uint256 _totalFees = _pairFees.total;
```

### [G-09] Using ```private``` rather than ```public``` _for constants_, saves gas [ 10 gas per instance]

**Context:**
[LBFactory.sol#L25-L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L25-L30)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        
        c0.gas();
        c1.gas();
    }
}

contract Contract0 {

    uint256 public constant BASE_GAS = 36000;
   
    function gas() external returns(uint256 gasPrice) {
        gasPrice = gasleft() + BASE_GAS;
    }
}

contract Contract1 {

    uint256 private constant BASE_GAS = 36000;

    function gas() external returns(uint256 gasPrice) {
        gasPrice = gasleft() + BASE_GAS;
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 43693                                     ┆ 249             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ gas                                       ┆ 263             ┆ 263 ┆ 263    ┆ 263 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 40893                                     ┆ 235             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ gas                                       ┆ 253             ┆ 253 ┆ 253    ┆ 253 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```

### [G-10] _Functions guaranteed to revert_ when callled by normal users can be marked `payable` [24 gas per instance]

**Context:** 
[LBFactory.sol#L215](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215), [LBFactory.sol#L317](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L317), [LBFactory.sol#L350](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L350), [LBFactory.sol#L396](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L396), [LBFactory.sol#L434](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L434), [LBFactory.sol#L468](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468), [LBFactory.sol#L474](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474), [LBFactory.sol#L485](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485), [LBFactory.sol#L493](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L493), [LBFactory.sol#L502](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L502), [LBFactory.sol#L520](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520), [PendingOwnable.sol#L59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L59), [PendingOwnable.sol#L68](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L68), [PendingOwnable.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L75), [PendingOwnable.sol#L84](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L84), [LBRouter.sol#L626](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L626), [LBRouter.sol#L647](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L647)

**Description:**
If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only ```onlyowner or admin``` functions)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0.foo();
        c1.foo();
    }
}

contract Contract0 {
    uint256 versionNFTDropCollection;
    
    function foo() external {
        versionNFTDropCollection++;
    }
}

contract Contract1 {
    uint256 versionNFTDropCollection;
    
    function foo() external payable {
        versionNFTDropCollection++;
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 44293                                     ┆ 252             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                       ┆ 22308           ┆ 22308 ┆ 22308  ┆ 22308 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 41893                                     ┆ 240             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                       ┆ 22284           ┆ 22284 ┆ 22284  ┆ 22284 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```

### [G-11] ```x -= y (x += y) ``` costs more gas than ```x = x – y (x = x + y)``` for state variables [32 gas per instance]

**Context:**
[LBPair.sol#L287-L288](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L287-L288), [LBPair.sol#L337](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L337), [LBFactory.sol#L143-L149](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L143-L149), [LBPair.sol#L227-L228](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L227-L228), [LBPair.sol#L336-L337](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L336-L337), [LBPair.sol#L539-L540](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L539-L540), [LBPair.sol#L550-L551](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L550-L551), [LBPair.sol#L572-L576](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L572-L576), [LBPair.sol#L639-L641](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L639-L641), [LBPair.sol#L646-L648](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L646-L648), [LBPair.sol#L711-L712](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L711-L712), [LBRouter.sol#L128-L130](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L128-L130), [LBRouter.sol#L173-L175](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L173-L175), [LBToken.sol#L211](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L211)
[BitMath.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BitMath.sol), [FeeDistributionHelper.sol#L42](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeDistributionHelper.sol#L42), [SwapHelper.sol#L88](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L88), [SwapHelper.sol#L91](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L91), [SwapHelper.sol#L115](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L115), [SwapHelper.sol#L118-L120](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L118-L120), [SwapHelper.sol#L126-L128](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/SwapHelper.sol#L126-L128)

**Description:**
```x -= y``` costs more gas than ```x = x – y``` for state variables.

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
  
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        c0.swap(2,3);
        c1.swap1(2,3);
    }
}
contract Contract0 {
    uint256 public amountIn = 10;
    
    
    function swap(uint256 amountInToBin, uint256 fee)  external returns (uint256 ) {
       
       return amountIn -= amountInToBin + fee;
    }
}

contract Contract1 {
    uint256 public amountIn = 10;
    
    function swap1(uint256 amountInToBin, uint256 fee)  external returns (uint256 result1) {
       return (amountIn = amountIn - (amountInToBin + fee));
    }
}
```
**Gas Report:**
```js
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 83017                                ┆ 341             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ swap                                 ┆ 5454            ┆ 5454 ┆ 5454   ┆ 5454 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆      ┆        ┆      ┆         │
╞══════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 83017                                ┆ 341             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ swap1                                ┆ 5431            ┆ 5431 ┆ 5431   ┆ 5431 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
```
### [G-12] WAWAX address definition can be use directly [~500 gas]

**Context:**
[LBRouter.sol#L59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L59)

**Description:**
Wawax is a wrap avax contract with a specific address in the Avalanche network, giving the option to define it may cause false recognition, it is healthier to define it directly.

Advantages of defining a specific contract directly:
- It saves gas 
- Prevents incorrect argument definition 
- Prevents execution on a different chain and re-signature issues

WAWAX Address : 0xB31f66AA3C1e785363F0875A1B74E27b85FD66c7


### [G-13] Optimize names to save gas [22 gas per instance]

**Context:** 
All Contracts

**Description:** 
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ```LBFactory.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

LBFactory.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
4e937c3a  =>  getNumberOfLBPairs()
80c5061e  =>  getNumberOfQuoteAssets()
f89a4cd5  =>  getQuoteAsset(uint256)
3d2d47d6  =>  isQuoteAsset(IERC20)
2e64870d  =>  getLBPairInformation(IERC20,IERC20,uint256)
935ea51b  =>  getPreset(uint16)
5b35875c  =>  getAllBinSteps()
6d54a35f  =>  getAllLBPairs(IERC20,IERC20)
b0384781  =>  setLBPairImplementation(address)
186ad25e  =>  createLBPair(IERC20,IERC20,uint24,uint16)
239042b5  =>  setLBPairIgnored(IERC20,IERC20,uint256,bool)
0af97c9a  =>  setPreset(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24,uint16)
e203a31f  =>  removePreset(uint16)
2887d003  =>  setFeesParametersOnPair(IERC20,IERC20,uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24)
e74b981b  =>  setFeeRecipient(address)
e92d0d5d  =>  setFlashLoanFee(uint256)
22f3fe14  =>  setFactoryLockedState(bool)
669b7ea2  =>  addQuoteAsset(IERC20)
933c7308  =>  removeQuoteAsset(IERC20)
fd8ec968  =>  _setFeeRecipient(address)
ee695ed7  =>  forceDecay(ILBPair)
f8c8def1  =>  _getPackedFeeParameters(uint16,uint16,uint16,uint16,uint16,uint24,uint16,uint24)
7bfa8e84  =>  _getLBPairInformation(IERC20,IERC20,uint256)
472be3ef  =>  _sortTokens(IERC20,IERC20)
```

### [G-14] Use assembly to check for ```address(0)``` [6 gas per instance]

**Context:** 
[LBFactory.sol#L245](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L245), [LBFactory.sol#L254](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L254), [LBFactory.sol#L321](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L321), [LBFactory.sol#L437](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L437), [LBFactory.sol#L511](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L511), [LBPair.sol#L91](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L91), [LBPair.sol#L11](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L112), [LBRouter.sol#L627](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L627), [LBRouter.sol#L919](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L919), [LBRouter.sol#L937 LBRouter.sol#L937](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L937), [LBToken.sol#L207](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L207), [LBToken.sol#L232](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L232), [JoeLibrary.sol#L15](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L15), [PendingOwnable.sol#L60-L61](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L60-L61), [PendingOwnable.sol#L69](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L69), [LBToken.sol#L207](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L207), [LBToken.sol#L232](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L232), [JoeLibrary.sol#L15](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L15)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTest is DSTest {
    Contract0 c0;
    Contract1 c1;
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    function testGas() public view {
        c0.setOperator(address(this));
        c1.setOperator(address(this));
    }
}
contract Contract0 {
    function setOperator(address operator_) public pure {
        require(operator_) != address(0), "INVALID_RECIPIENT");    }
}
contract Contract1 {
    function setOperator(address operator_) public pure {
        assembly {
            if iszero(operator_) {
                mstore(0x00, "Callback_InvalidParams")
                revert(0x00, 0x20)
            }
        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setOperator                               ┆ 258             ┆ 258 ┆ 258    ┆ 258 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 44893                                     ┆ 255             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setOperator                               ┆ 252             ┆ 252 ┆ 252    ┆ 252 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```
### [G-15] Use ``assembly`` to write _address storage values_ [33 gas per instance]

**Context:**
[LBFactory.sol#L66](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L66), [LBFactory.sol#L223](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L223), [LBFactory.sol#L479](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L479), [LBFactory.sol#L516](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L516)
[LBPair.sol#L92](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L92), [LBQuoter.sol#L45-L47](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L45-L47), [LBRouter.sol#L57-L59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L57-L59), [PendingOwnable.sol#L93-L94](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L93-L94), [PendingOwnable.sol#L102](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L102), [LBPair.sol#L116-L117](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L116-L117)

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.

```js
contract GasTestFoundry is DSTest {
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
        c2.setRewardTokenAndAmount(0x5B38Da6a701c568545dCfcB03FcB875f56beddC4,356);
    }
}

contract Contract1  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        rewardToken = token_;
        reward = reward_;
    }
}

contract Contract2  {
    address rewardToken ;
    uint256 reward;

    function setRewardTokenAndAmount(address token_, uint256 reward_) external {
        assembly {
            sstore(rewardToken.slot, token_)
            sstore(reward.slot, reward_)           

        }
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 50899                                     ┆ 285             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44490           ┆ 44490 ┆ 44490  ┆ 44490 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬───────┬────────┬───────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆       ┆        ┆       ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═══════╪════════╪═══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 38087                                     ┆ 221             ┆       ┆        ┆       ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg   ┆ median ┆ max   ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ setRewardTokenAndAmount                   ┆ 44457           ┆ 44457 ┆ 44457  ┆ 44457 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴───────┴────────┴───────┴─────────╯
```
### [G-16] The solady Library's Ownable contract is significantly gas-optimized, which can be used

**Description:**
The project uses the `onlyOwner` authorization model with the `PendingOwnable.sol` contract. I recommend using _Solady's highly gas optimized contract._

https://github.com/Vectorized/solady/blob/main/src/auth/OwnableRoles.sol

### [G-17] Use ```++index ```  instead of ```index++``` to increment a loop counter [5 gas per instance]

**Context:**
[LBQuoter.sol#L75](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75)
[LBQuoter.sol#L100](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100)
[LBQuoter.sol#L177](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177)


**Description:**
Due to reduced stack operations, using ++index saves 5 gas per iteration.

**Recommendation:**
Use ++index to increment a loop counter.

**Proof Of Concept:**
The optimizer was turned on and set to 10000 runs.
```js
contract GasTest is DSTest {

    Contract0 c0;
    Contract1 c1;

   function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }

   function testGas() public {

        c0.foo();
        c1.foo();
    }
}

contract Contract0 {

    function foo() external {
        uint256 versionNFTDropCollection;
        ++versionNFTDropCollection;
    }
}

contract Contract1 {

    function foo() external {
        uint256 versionNFTDropCollection;
        versionNFTDropCollection++;
    }
}
```
**Gas Report:**
```js
╭──────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/test.sol:Contract0 contract ┆                 ┆     ┆        ┆     ┆         │
╞══════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 42893                                ┆ 245             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                  ┆ 193             ┆ 193 ┆ 193    ┆ 193 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
╭──────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/test.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞══════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                      ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 43293                                ┆ 247             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                        ┆ min             ┆ avg ┆ median ┆ max ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ foo                                  ┆ 198             ┆ 198 ┆ 198    ┆ 198 ┆ 1       │
╰──────────────────────────────────────┴─────────────────┴─────┴────────┴─────┴─────────╯
```
### [G-18] Removing unused functions saves gas

**Context:**
[LBToken.sol#L321-L327](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L321-L327)

**Description:**
Removing unused functions saves gas in deploying the project. The `_beforeTokenTransfer` function in the `LBToken.sol` contract is a pattern. But since this contract is not upgradable, it cannot be used later, removing this function will save gas during deploy.

```js
 function _beforeTokenTransfer(
        address from,
        address to,
        uint256 id,
        uint256 amount
    ) internal virtual {}
}
```

### [G-19] Avoid contract existence checks by using solidity version 0.8.10 or later

**Description:**
Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value.

### [G-20] Using bools for storage incurs overhead

**Context:**
[LBToken.sol#L21](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L21)

**Description:**
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from false to true, after having been true in the past.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

### [G-21] Setting The Constructor To Payable [13 gas per instance]

**Context:**
[LBFactory.sol#L63](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63)
[LBPair.sol#L90](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L90)
[LBQuoter.sol#L40](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L40)
[LBRouter.sol#L52](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L52)
[PendingOwnable.sol#L41](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L41)

**Description:**
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

**Recommendation:**
Set the constructor to ```payable```

**Proof Of Concept:**
https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5?u=pcaversaccio

The optimizer was turned on and set to 10000 runs

```js
contract GasTestFoundry is DSTest {
    
    Contract1 c1;
    Contract2 c2;
    
    function setUp() public {
        c1 = new Contract1();
        c2 = new Contract2();
    }
    
    function testGas() public {
        c1.x();
        c2.x();
    }
}

contract Contract1 {
    
    uint256 public dummy;
    constructor() payable {
        dummy = 1;
    }
    
    function x() public {

    }
}

contract Contract2 {
    
    uint256 public dummy;
    constructor() {
        dummy = 1;
    }
    
    function x() public {
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49563                                     ┆ 159             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤

╭───────────────────────────────────────────┬─────────────────┬─────┬────────┬─────┬─────────╮
│ src/test/GasTest.t.sol:Contract2 contract ┆                 ┆     ┆        ┆     ┆         │
╞═══════════════════════════════════════════╪═════════════════╪═════╪════════╪═════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 49587                                     ┆ 172             ┆     ┆        ┆     ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
```

### [G-22] It is more efficient to use `abi.encodePacked` instead of `abi.encode` [164 gas saved]

**Context:**
[LBFactory.sol#L265](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265)

**Description:**
 It is more efficient to use `abi.encodePacked` instead of `abi.encode`.

_When are these used?_
```abi.encode:```
abi.encode encode its parameters using the ABI specs. The ABI was designed to make calls to contracts. Parameters are padded to 32 bytes. If you are making calls to a contract you likely have to use abi.encode
If you are dealing with more than one dynamic data type as it prevents collision.

```abi.encodePacked:```
abi.encodePacked encode its parameters using the minimal space required by the type. Encoding an uint8 it will use 1 byte. It is used when you want to save some space, and not calling a contract.Takes all types of data and any amount of input.

```js
contract GasTest is DSTest {
  
    Contract0 c0;
    Contract1 c1;
    
    function setUp() public {
        c0 = new Contract0();
        c1 = new Contract1();
    }
    
    function testGas() public {
        
        c0.collision("AAA","BBB");
        c1.hard("AAA","BBB");
    }
}

contract Contract0 {

    // abi.encode
    // (AAA,BBB) keccak = 0xd6da8b03238087e6936e7b951b58424ff2783accb08af423b2b9a71cb02bd18b
    // (AA,ABBB) keccak = 0x54bc5894818c61a0ab427d51905bc936ae11a8111a8b373e53c8a34720957abb
    function collision(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encode(_text, _anotherText));
    }
}

contract Contract1 {

    // abi.encodePacked
    // (AAA,BBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358
    // (AA,ABBB) keccak = 0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358

    function hard(string memory _text, string memory _anotherText)
        public pure returns (bytes32)
    {
        return keccak256(abi.encodePacked(_text, _anotherText));
    }
}
```
**Gas Report:**
```js
╭───────────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/GasTest.t.sol:Contract0 contract ┆                 ┆      ┆        ┆      ┆         │
╞═══════════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 132377                                    ┆ 693             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ collision                                 ┆ 1737            ┆ 1737 ┆ 1737   ┆ 1737 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
╭───────────────────────────────────────────┬─────────────────┬──────┬────────┬──────┬─────────╮
│ src/test/GasTest.t.sol:Contract1 contract ┆                 ┆      ┆        ┆      ┆         │
╞═══════════════════════════════════════════╪═════════════════╪══════╪════════╪══════╪═════════╡
│ Deployment Cost                           ┆ Deployment Size ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 119365                                    ┆ 628             ┆      ┆        ┆      ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                             ┆ min             ┆ avg  ┆ median ┆ max  ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ hard                                      ┆ 1573            ┆ 1573 ┆ 1573   ┆ 1573 ┆ 1       │
╰───────────────────────────────────────────┴─────────────────┴──────┴────────┴──────┴─────────╯
```

###  [G-23] Free gas savings for using solidity 0.8.10+

**Context:**
All Contracts

**Description:**
Solidity 0.8.10 has a useful change which reduced gas costs of external calls which expect a return value: https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/

Code Generator: Skip existence check for external contract if return data is expected. In this case, the ABI decoder will revert if the contract does not exist.

**Recommendation:**
Consider to upgrade pragma to at least 0.8.10
### [S-01] Use `v4.8.0 OpenZeppelin` contracts

**Description:**
The upcoming v4.8.0 version of OpenZeppelin provides many small gas optimizations.

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0-rc.0

```js
v4.8.0-rc.0
⛽ Many small optimizations
```

###  [S-02] Missing `zero-address` check in `constructor`

**Description:**
Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly. It also wast gas as it requires the redeployment of the contract.

```js
src/LBQuoter.sol:
39     /// @param _factoryV2 Dex V2 factory address
40:     constructor(
41   	address _routerV2,
42     	address _routerV2,
43         	address _routerV2,
44       ) {
45 	routerV2 = _routerV2;
46 	factoryV1 = _factoryV1;
47 	factoryV2 = _factoryV2;
48          }
```
