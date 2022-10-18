## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `++i` Costs Less Gas Than `i++`, Especially When It’s Used In For-loops (`--i`/`i--` Too) | 4 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate | 5 |
| [GAS&#x2011;3](#GAS&#x2011;3) | Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas | 2 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Empty Blocks Should Be Removed Or Emit Something | 3 |
| [GAS&#x2011;5](#GAS&#x2011;5) | `require()` Should Be Used Instead Of `assert()` | 1 |
| [GAS&#x2011;7](#GAS&#x2011;6) | Using Private Rather Than Public For Constants, Saves Gas | 4 |
| [GAS&#x2011;8](#GAS&#x2011;7) | Using Bools For Storage Incurs Overhead | 2 |
| [GAS&#x2011;9](#GAS&#x2011;8) | `abi.encode()` Is Less Efficient Than `abi.encodepacked()` | 1 |
| [GAS&#x2011;10](#GAS&#x2011;9) | Use calldata instead of memory for function parameters | 29 |
| [GAS&#x2011;11](#GAS&#x2011;10) | Use assembly to check for `address(0)` | 2 |
| [GAS&#x2011;12](#GAS&#x2011;11) | Public Functions To External | 16 |
| [GAS&#x2011;13](#GAS&#x2011;12) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 16 |
| [GAS&#x2011;14](#GAS&#x2011;13) | Optimize names to save gas | 6 |

Total: 91 instances over 14 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `++i` Costs Less Gas Than `i++`, Especially When It's Used In For-loops (`--i`/`i--` Too)

Saves ~10 gas per loop

#### <ins>Proof Of Concept</ins>


```
75: for (uint256 i; i < swapLength; i++) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75

```
177: for (uint256 j; j < LBPairsAvailable.length; j++) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177

```
154: for (uint256 i = swapLength; i > 0; i--) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154

```
711: for (uint256 i = _pairs.length; i != 0; i--) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711

##### (LBQuoter.t.sol) Added test function to increase route.length to 100 to test out a larger case scenario.
```
function testGetSwapInOnV2Pair_bigLength() public {
    addLiquidityFromRouter(testWavax, usdc, 20_000e6, convertIdAvaxToUSD(DEFAULT_BIN_STEP), 9, 2, DEFAULT_BIN_STEP);
    address[] memory route;
    route = new address[](100);
    for (uint i; i < 100; ++i){
        if (i%2 == 0) {
            route[i] =  address(testWavax);
        }
        else {
            route[i] =  address(usdc);
        }
    }
    LBQuoter.Quote memory quote = quoter.findBestPathFromAmountOut(route, 20e6);
}
```
##### Original
```
Running 11 tests for test/LBQuoter.t.sol:LiquidityBinQuoterTest
[PASS] testConstructor() (gas: 11845)
[PASS] testGetSwapInOnComplexRoute() (gas: 1861401)
[PASS] testGetSwapInOnV1Pair() (gas: 288241)
[PASS] testGetSwapInOnV2Pair() (gas: 1586112)
[PASS] testGetSwapInOnV2Pair_bigLength() (gas: 4640823)
[PASS] testGetSwapInWithMultipleChoices() (gas: 1799517)
[PASS] testGetSwapOutOnComplexRoute() (gas: 1881523)
[PASS] testGetSwapOutOnV1Pair() (gas: 307138)
[PASS] testGetSwapOutOnV2Pair() (gas: 1587403)
[PASS] testGetSwapOutWithMultipleChoices() (gas: 4096617)
[PASS] testInvalidLength() (gas: 17741)
Test result: ok. 11 passed; 0 failed; finished in 413.44ms
╭────────────────────────────────────┬─────────────────┬────────┬────────┬─────────┬─────────╮
│ src/LBQuoter.sol:LBQuoter contract ┆                 ┆        ┆        ┆         ┆         │
╞════════════════════════════════════╪═════════════════╪════════╪════════╪═════════╪═════════╡
│ Deployment Cost                    ┆ Deployment Size ┆        ┆        ┆         ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 1899867                            ┆ 9873            ┆        ┆        ┆         ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                      ┆ min             ┆ avg    ┆ median ┆ max     ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ factoryV1                          ┆ 206             ┆ 206    ┆ 206    ┆ 206     ┆ 1       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ factoryV2                          ┆ 205             ┆ 205    ┆ 205    ┆ 205     ┆ 1       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ findBestPathFromAmountIn           ┆ 1033            ┆ 45266  ┆ 45438  ┆ 88927   ┆ 5       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ findBestPathFromAmountOut          ┆ 1011            ┆ 528333 ┆ 42129  ┆ 2979356 ┆ 6       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ routerV2                           ┆ 228             ┆ 228    ┆ 228    ┆ 228     ┆ 1       │
╰────────────────────────────────────┴─────────────────┴────────┴────────┴─────────┴─────────╯
```
##### Fixed i++ loops
```
Running 11 tests for test/LBQuoter.t.sol:LiquidityBinQuoterTest
[PASS] testConstructor() (gas: 11845)
[PASS] testGetSwapInOnComplexRoute() (gas: 1861381)
[PASS] testGetSwapInOnV1Pair() (gas: 288231)
[PASS] testGetSwapInOnV2Pair() (gas: 1586102)
[PASS] testGetSwapInOnV2Pair_bigLength() (gas: 4639833)
[PASS] testGetSwapInWithMultipleChoices() (gas: 1799507)
[PASS] testGetSwapOutOnComplexRoute() (gas: 1881503)
[PASS] testGetSwapOutOnV1Pair() (gas: 307128)
[PASS] testGetSwapOutOnV2Pair() (gas: 1587393)
[PASS] testGetSwapOutWithMultipleChoices() (gas: 4096607)
[PASS] testInvalidLength() (gas: 17741)
Test result: ok. 11 passed; 0 failed; finished in 220.37ms
╭────────────────────────────────────┬─────────────────┬────────┬────────┬─────────┬─────────╮
│ src/LBQuoter.sol:LBQuoter contract ┆                 ┆        ┆        ┆         ┆         │
╞════════════════════════════════════╪═════════════════╪════════╪════════╪═════════╪═════════╡
│ Deployment Cost                    ┆ Deployment Size ┆        ┆        ┆         ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ 1898267                            ┆ 9865            ┆        ┆        ┆         ┆         │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ Function Name                      ┆ min             ┆ avg    ┆ median ┆ max     ┆ # calls │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ factoryV1                          ┆ 206             ┆ 206    ┆ 206    ┆ 206     ┆ 1       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ factoryV2                          ┆ 205             ┆ 205    ┆ 205    ┆ 205     ┆ 1       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ findBestPathFromAmountIn           ┆ 1033            ┆ 45256  ┆ 45428  ┆ 88907   ┆ 5       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ findBestPathFromAmountOut          ┆ 1011            ┆ 528159 ┆ 42119  ┆ 2978366 ┆ 6       │
├╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┼╌╌╌╌╌╌╌╌╌┤
│ routerV2                           ┆ 228             ┆ 228    ┆ 228    ┆ 228     ┆ 1       │
╰────────────────────────────────────┴─────────────────┴────────┴────────┴─────────┴─────────╯
```


#### <ins>Recommended Mitigation Steps</ins>

For example, use `++i` instead of `i++`



### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

#### <ins>Proof Of Concept</ins>


```
68: mapping(address => bytes32) private _unclaimedFees;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L68

```
70: mapping(address => mapping(uint256 => Debts)) private _accruedDebts;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L70

```
18: mapping(uint256 => mapping(address => uint256)) private _balances;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L18

```
21: mapping(address => mapping(address => bool)) private _spenderApprovals;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L21

```
27: mapping(address => EnumerableSet.UintSet) private _userIds;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L27


### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Not Using The Named Return Variables When A Function Returns, Wastes Deployment Gas

#### <ins>Proof Of Concept</ins>

```
function update(
        bytes32 _lastSample,
        uint256 _activeId,
        uint256 _volatilityAccumulated,
        uint256 _binCrossed
    ) internal view returns (bytes32 packedSample) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L40

```
function pack(
        uint256 _cumulativeBinCrossed,
        uint256 _cumulativeVolatilityAccumulated,
        uint256 _cumulativeId,
        uint256 _timestamp,
        uint256 _initialized
    ) internal pure returns (bytes32 packedSample) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L69



### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>


```
123: } catch {}
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L123

```
203: } catch {}
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L203

```
326: ) internal virtual {}
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L326






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```
141: assert(_binStep == _preset.decode(type(uint16).max, _shift));
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L141



### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Using Private Rather Than Public For Constants, Saves Gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

#### <ins>Proof Of Concept</ins>


```
25: uint256 public constant override MAX_FEE = 0.1e18; // 10%
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#25

```
27: uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#27

```
28: uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#28

```
30: uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#30



#### <ins>Recommended Mitigation Steps</ins>

Set variable to private.





### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Using Bools For Storage Incurs Overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

#### <ins>Proof Of Concept</ins>

```
bool public override creationUnlocked;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L39

```
    mapping(address => mapping(address => bool)) private _spenderApprovals;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L21


### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> `abi.encode()` Is Less Efficient Than `abi.encodepacked()`

#### <ins>Proof Of Concept</ins>


```
bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L265





### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:

```
contract C {
    function add(uint[] memory arr) external returns (uint sum) {
        uint length = arr.length;
        for (uint i = 0; i < arr.length; i++) {
            sum += arr[i];
        }
    }
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:

```
contract C {
    function add(uint[] calldata arr) external returns (uint sum) {
        uint length = arr.length;
        for (uint i = 0; i < arr.length; i++) {
            sum += arr[i];
        }
    }
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
    (...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>

```
function pendingFees(address _account, uint256[] memory _ids)
        external
        view
        override
        returns (uint256 amountX, uint256 amountY)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L261

```
function mint(
        uint256[] memory _ids,
        uint256[] memory _distributionX,
        uint256[] memory _distributionY,
        address _to
    )
        external
        override
        nonReentrant
        returns (
            uint256,
            uint256,
            uint256[] memory liquidityMinted
        )
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L466

```
function burn(
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address _to
    ) external override nonReentrant returns (uint256 amountX, uint256 amountY) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L616

```
function collectFees(address _account, uint256[] memory _ids)
        external
        override
        nonReentrant
        returns (uint256 amountX, uint256 amountY)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L688

```
function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
        public
        view
        returns (Quote memory quote)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L54

```
function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
        public
        view
        returns (Quote memory quote)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L134

```
function addLiquidity(LiquidityParameters memory _liquidityParameters)
        external
        override
        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L207

```
function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)
        external
        payable
        override
        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L230

```
function removeLiquidity(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint16 _binStep,
        uint256 _amountXMin,
        uint256 _amountYMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address _to,
        uint256 _deadline
    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L274

```
function removeLiquidityAVAX(
        IERC20 _token,
        uint16 _binStep,
        uint256 _amountTokenMin,
        uint256 _amountAVAXMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address payable _to,
        uint256 _deadline
    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L308

```
function swapExactTokensForTokens(
        uint256 _amountIn,
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L352

```
function swapExactTokensForAVAX(
        uint256 _amountIn,
        uint256 _amountOutMinAVAX,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L377

```
function swapExactAVAXForTokens(
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L407

```
function swapTokensForExactTokens(
        uint256 _amountOut,
        uint256 _amountInMax,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L431

```
function swapTokensForExactAVAX(
        uint256 _amountAVAXOut,
        uint256 _amountInMax,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L459

```
function swapAVAXForExactTokens(
        uint256 _amountOut,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    )
        external
        payable
        override
        ensure(_deadline)
        verifyInputs(_pairBinSteps, _tokenPath)
        returns (uint256[] memory amountsIn)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L493

```
function swapExactTokensForTokensSupportingFeeOnTransferTokens(
        uint256 _amountIn,
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L531

```
function swapExactTokensForAVAXSupportingFeeOnTransferTokens(
        uint256 _amountIn,
        uint256 _amountOutMinAVAX,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address payable _to,
        uint256 _deadline
    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L561

```
function swapExactAVAXForTokensSupportingFeeOnTransferTokens(
        uint256 _amountOutMin,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to,
        uint256 _deadline
    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L594

```
function sweepLBToken(
        ILBToken _lbToken,
        address _to,
        uint256[] memory _ids,
        uint256[] memory _amounts
    ) external override onlyFactoryOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L642

```
function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair)
        private
        ensure(_liq.deadline)
        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L656

```
function _getAmountsIn(
        uint256[] memory _pairBinSteps,
        address[] memory _pairs,
        IERC20[] memory _tokenPath,
        uint256 _amountOut
    ) private view returns (uint256[] memory amountsIn) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L701

```
function _removeLiquidity(
        ILBPair _LBPair,
        uint256 _amountXMin,
        uint256 _amountYMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address _to
    ) private returns (uint256 amountX, uint256 amountY) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L741

```
function _swapExactTokensForTokens(
        uint256 _amountIn,
        address[] memory _pairs,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to
    ) private returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L762

```
function _swapTokensForExactTokens(
        address[] memory _pairs,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        uint256[] memory _amountsIn,
        address _to
    ) private returns (uint256 amountOut) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L816

```
function _swapSupportingFeeOnTransferTokens(
        address[] memory _pairs,
        uint256[] memory _pairBinSteps,
        IERC20[] memory _tokenPath,
        address _to
    ) private {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L864

```
function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
        private
        view
        returns (address[] memory pairs)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L941

```
function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
        public
        view
        virtual
        override
        checkLength(_accounts.length, _ids.length)
        returns (uint256[] memory batchBalances)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L79

```
function safeBatchTransferFrom(
        address _from,
        address _to,
        uint256[] memory _ids,
        uint256[] memory _amounts
    )
        public
        virtual
        override
        checkLength(_ids.length, _amounts.length)
        checkAddresses(_from, _to)
        checkApproval(_from, msg.sender)
    {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L149






### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Use assembly to check for `address(0)`

Save 6 gas per instance if using assembly to check for address(0)

```
e.g.
assembly {
    if iszero(_addr) {
        mstore(0x00, "AddressZero")
        revert(0x00, 0x20)
    }
}
```

#### <ins>Proof Of Concept</ins>


```
function _setFeeRecipient(address _feeRecipient) internal {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L510

```
function setPendingOwner(address pendingOwner_) public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L59






### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```
function getSwapIn(
        ILBPair _LBPair,
        uint256 _amountOut,
        bool _swapForY
    ) public view override returns (uint256 amountIn, uint256 feesIn) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L90

```
function name() public pure virtual override returns (string memory) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L49

```
function symbol() public pure virtual override returns (string memory) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L55

```
function totalSupply(uint256 _id) public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L63

```
function balanceOf(address _account, uint256 _id) public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L71

```
function userPositionAtIndex(address _account, uint256 _index) public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L100

```
function userPositionNumber(address _account) public view virtual override returns (uint256) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L107

```
function isApprovedForAll(address _owner, address _spender) public view virtual override returns (bool) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L115

```
function setApprovalForAll(address _spender, bool _approved) public virtual override {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L122

```
function safeTransferFrom(
        address _from,
        address _to,
        uint256 _id,
        uint256 _amount
    ) public virtual override checkAddresses(_from, _to) checkApproval(_from, msg.sender) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L131

```
function owner() public view override returns (address) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L47

```
function pendingOwner() public view override returns (address) {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L53

```
function setPendingOwner(address pendingOwner_) public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L59

```
function revokePendingOwner() public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L68

```
function becomeOwner() public override onlyPendingOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L75

```
function renounceOwnership() public override onlyOwner {
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol#L84






### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```
uint24 _id = _ids[i].safe24();
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L624

```
uint128 _totalFees = _pairFees.total;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeDistributionHelper.sol#L31

```
uint16 binStep;
uint16 baseFactor;
uint16 filterPeriod;
uint16 decayPeriod;
uint16 reductionFactor;
uint24 variableFeeControl;
uint16 protocolShare;
uint24 maxVolatilityAccumulated;
uint24 volatilityAccumulated;
uint24 volatilityReference;
uint24 indexRef;
uint40 time;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L30-L41

```
uint128 total;
uint128 protocol;
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/FeeHelper.sol#L48-L49

```


### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://github.com/enzosv/solidity-optimize-name) link for an example of how it works. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### <ins>Proof Of Concept</ins>

```
File: \2022-10-traderjoe\src\LBPair.sol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol

```
File: \2022-10-traderjoe\src\LBQuoter.sol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol

```
File: \2022-10-traderjoe\src\LBRouter.sol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol

```
File: \2022-10-traderjoe\src\LBToken.sol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol

```
File: \2022-10-traderjoe\src\libraries\PendingOwnable.sol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/PendingOwnable.sol

```
File: \2022-10-traderjoe\src\libraries\ReentrancyGuardUpgradeable.sol
```

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/ReentrancyGuardUpgradeable.sol






