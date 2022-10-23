|  | Issue | Instances |
| --- | --- | --- |
| [G-01] | Preincrements/Predecrements are cheaper especially  in for() loops | 30 |
| [G-02] | Replace require()/revert() statements with Custom Errors . | 9 |
| [G-03] | Unchecked Arithmetic | 5 |
| [G-04] | Using bools for storage incurs overhead | 1 |
| [G-05] | Using private rather than public for constants | 4 |
| [G-06] | abi.encode() is less efficient than abi.encodePacked() | 2 |
| [G-07] | Bytes constant are cheaper than string constants | 2 |

# [G-01] Preincrements/Predecrements are cheaper especially  in `for()` loops

1. `++i` costs less gas than `i++` , especially when it’s used in for-loops (--i/i--` too)
Pre-increments and pre-decrements are cheaper.
2. For a `uint256 i` variable, the following is true with the Optimizer enabled

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (**11 gas less than `i += 1`**)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `-i` costs 5 gas less than `i--` (**16 gas less than `i -= 1`**)
- 

`i++` increments `i` and returns the initial value of `i`. Which means:

```
uint i = 1;
i++; // == 1 but i == 2

```

But `++i` returns the actual incremented value:

```
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable

```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`

## Instances -

File: test\LBPair.Fees.t.sol

`45:         for (uint256 i; i < 5; i++) {`

`83:         for (uint256 i; i < 5; i++) {` 

`115:       for (uint256 i; i < 5; i++) {` 

File: test\LBPair.Liquidity.t.sol

`137:       for (uint256 i; i < 5; i++) {`

File: test\LBRouter.FeesOnLiquidityAdd.t.sol

`110:         for (uint256 i; i < _numberBins; i++) {`

`124:         for (uint256 i; i < _numberBins; i++) { 
212:         for (uint256 i; i < _numberBins; i++) {`

`223:         for (uint256 i; i < _numberBins; i++) {`

File: test\LBRouter.Liquidity.t.sol

`45:         for (uint256 i; i < _numberBins; i++) {`

`96:         for (uint256 i; i < _numberBins; i++) {`

`147:       for (uint256 i; i < _numberBins; i++) {`

`223:       for (uint256 i; i < _numberBins; i++) {`

`273:       for (uint256 i; i < _numberBins; i++) {`

`325:       for (uint256 i; i < _numberBins; i++) {`

File: test\LBRouter.t.sol

`331:         for (uint256 i; i < _numberBins; i++) {`

`412:         for (uint256 i; i < 5; i++) {`

`422:         for (uint256 i; i < 5; i++) {`

`432:         for (uint256 i; i < 5; i++) {`

File: test\LBToken.t.sol

`36:         for (uint256 i; i < 5; i++) {`

`69:         for (uint256 i; i < 5; i++) {`

`101:       for (uint256 i; i < 5; i++) {`

`116:       for (uint256 i; i < 5; i++) {
132:       for (uint256 i; i < binAmount; i++) {`

`165:       for (uint256 i; i < binAmount; i++) {`

`197:       for (uint256 i; i < binAmount - 1; i++) {`

`206:       for (uint256 i; i < binAmount - 1; i++) {`

`231:       for (uint256 i; i < binAmount / 2; i++) {`

`236:       for (uint256 i; i < binAmount; i++) {`

`240:       for (uint256 i; i < binAmount; i++) {`

`246:       for (uint256 i; i < binAmount; i++) {`

`251:       for (uint256 i; i < binAmount; i++) {`

File: test\TestHelper.sol

`159:         for (uint256 i; i < _numberBins; i++) {`

`261:         for (uint256 i; i < _numberBins; i++) {`

File: src\LBQuoter.sol

`75:         for (uint256 i; i < swapLength; i++) {`

NOTE - THE ABOVE REASONING IS ALSO VALID FOR ‘ x++’ IN GENERAL 

REPLACE  `i++/i--` with `++i/--i`  OR  x++ with ++x IN THE INSTANCES MENTIONED ABOVE . 

# [G-02] Replace `require()`/`revert()` statements with Custom Errors .

1. Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4) ,  **custom errors** were introduced in order to tell the user about any error . It was done through the use of strings to give any info about errors
2. Use of custom errors can save **[~50 gas](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746)** each time they are hit because it  dosen’t need  to allocate and store the revert string .
3. Not defining the strings also save deployment gas 
4. You can implement custom errors as shown in the link below . 
5. [https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth)
6. [https://solidity-by-example.org/error/](https://solidity-by-example.org/error/)

## Instances -

File: test\mocks\Faucet.sol

`39:         require(msg.sender == operator, "Only operator")
42:
44:      require(block.timestamp >= lastRequest[user] + requestCooldown, "Too many requests");
49:         require(tx.origin == msg.sender, "Only EOA");
54:         require(unlockedRequest, "Direct request is locked");`

`106:         require(index >= 2, "Not a faucet token");`

`180:         require(tokenToIndices[_token.ERC20] == 0, "Already a faucet token");`

`190:         require(_requestCooldown >= 1 hours, "Unsafe request cooldown");`

`201:         require(index != 0, "Not a faucet token");`

`211:         require(success, "AVAX transfer failed");`

CONSIDER CHANGING ALL `require()` AND  `revert()`  STATMENT WITH **CUSTOM ERRORS** 

# [G-03] Unchecked Arithmetic

1. If using solidity >= 0.8.0, safe arithmetic is 
built-in. In situations that overflow/underflow checks are redundant, 
code should be wrapped in an unchecked block. For example, the for loop 
increment can be unchecked as follows:
    
    `// pragma solidity ^0.8.0;
    
    for (uint i = 0; i < length; i = unchecked_inc(i)) {
        // do something that doesn't change the value of i
    }
    
    function unchecked_inc(uint i) returns (uint) {
        unchecked {
            return i + 1;
        }
    }`
    
2.   `unchecked` produces smaller bytecode compared to regular arthimetic operations, as         it doesn't contain the underflow/overflow validation.
3. The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of loop 

## Instances -

File: test\LBPair.Fees.t.sol

`45:         for (uint256 i; i < 5; i++) {
46:             orderedIds[i] = startId - 2 + i;
47:         }`

`83:         for (uint256 i; i < 5; i++) {          
84:             orderedIds[i] = startId - 2 + i;
85:         }`

File: test\LBPair.Oracle.t.sol

`161:         for (uint256 i; i < 99; ++i) { 
162:             uint256 _ago = ((block.timestamp - startTimestamp) * i) / 100;`

`205:         for (uint256 i; i < 49; ++i) {
206:             uint256 _ago = ((block.timestamp - startTimestamp) * i) / 50;`

File: test\LBToken.t.sol

`231:         for (uint256 i; i < binAmount / 2; i++) {
232:             _ids[i] = _startId - (binAmount / 2) * (1 + _gap) + i * (1 + _gap);
233:         }`

# [G-04] **Using bools for storage incurs overhead**

Booleans are more expensive than uint256 or any type that 
takes up a full word because each write operation emits an extra SLOAD 
to first read the slot's contents, replace the bits taken up by the 
boolean, and then write back. This is the compiler's defense against 
contract upgrades and pointer aliasing, and it cannot be disabled.

Consider doing like here: [https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) . They are using `uint256(1)` and `uint256(2)` for true/false to avoid the extra SLOAD (**100 gas**) and the extra SSTORE (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past:

## Instances -

File: test\mocks\Faucet.sol

`29:     bool public unlockedRequest;`

# [G-05] **Using `private` rather than `public` for constants**

If needed, the values can be read from the verified contract source 
code, or if there are multiple values there can be a single getter 
function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas**  in deployment gas due to the compiler not having to create non-payable 
getter functions for deployment calldata, not having to store the bytes 
of the value outside of where it's used, and not adding another entry to
 the method ID table

## **Instances -**

File: src\LBFactory.sol

`25:     uint256 public constant override MAX_FEE = 0.1e18; // 10%
26:
27:     uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
28:     uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
29:
30:     uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%`

### Recommended Mitigation Steps

Make the constants `private` instead of `public`.

# [G-06] **`abi.encode()` is less efficient than `abi.encodePacked()`**

Changing `abi.encode` function to `abi.encodePacked`
 can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked`
 is more gas-efficient (see [Solidity-Encode-Gas-Comparison](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)).

## Instances -

File: test\mocks\FlashloanBorrower.sol

`54:         bytes memory data = abi.encode(Action.NORMAL, amountXBorrowed, amountYBorrowed, false);`

`60:         bytes memory data = abi.encode(Action.NORMAL, amountXBorrowed, amountYBorrowed, true);`

# [G-07]**Bytes constant are cheaper than string constants**

If the string can fit into 32 bytes, then `bytes32` is cheaper than `string` . `string`is a dynamically sized-type, which has current limitations in Solidity compared to a statically sized variable.

## Instances -

File: src\LBToken.sol

`29:     string private constant _name = "Liquidity Book Token";
30:     string private constant _symbol = "LBT";`