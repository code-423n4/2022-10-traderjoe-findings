## Use Fixed-size `bytes32` instead of `string`
Fitting your data in fixed-size 32 byte words is much cheaper than using arbitrary-length types. Remember that bytes32 uses less gas because it fits in a single EVM word. Typically, any fixed size variable in solidity is cheaper than dynamically sized ones. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29-L30

Please visit the following link for more details on favoring bytes32 over string:

https://ethereum.stackexchange.com/questions/3795/why-do-solidity-examples-use-bytes32-type-instead-of-string


## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through an internal or private (if no contracts inheriting) function to reduce contract size if need be. For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L43-L46

```
    function _onlyFactory() private view {
        if (msg.sender != address(factory)) revert LBPair__OnlyFactory();
    }

    modifier onlyFactory() {
        _onlyFactory();
        _;
    }
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop. As an example, the following line of code could be rewritten as:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L211

```
        _totalSupplies[_id] =  _totalSupplies[_id] + _amount;
```
## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.0",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI

Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter, we should pass it as calldata because calldata is more gas efficient than memory.

Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. However, it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, bypassing the cost of 60 gas on each iteration. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L152-L153
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L467-L469

## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =). As an example, consider replacing >= with the strict counterpart > in the following line of code:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L643

```
                if (_id > _activeId - 1) {
```
Similarly, as an example, consider replacing <= with the strict counterpart < in the following line of code:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L636

```
                if (_id < _activeId + 1) {
```
## `||` Costs Less Gas Than Its Equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the following code line may be rewritten as:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L651

```
                if (!(_reserveX != 0 || _reserveY != 0)) _tree.removeFromTree(_id);
```
Similarly, the following line of code may be rewritten as:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L284

```
                if (!(_accountBalance != 0 || _amount == 0)) {
```
The following line of code may also be rewritten as:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L278

```
                if (!(_lastId < _id || i == 0)) revert LBPair__OnlyStrictlyIncreasingId();
```
Note that this would kill two birds with one stone considering >= has been replaced with <.

## Ternary Over `if ... else`
Using ternary operator instead of the if else statement saves gas. For instance the following code block may be rewritten as:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L406-L412

```
        _swapForY
            ? {
                amountYOut = _amountOut;
                tokenY.safeTransfer(_to, _amountOut);
            }
            : {
                amountXOut = _amountOut;
                tokenX.safeTransfer(_to, _amountOut);
            }
``` 
## ++i/--i costs less gas compared to i++/i--
++i/--1 costs less gas compared to i++/i-- for unsigned integers considering the pre-increment/decrement operation is cheaper (about 5 GAS per iteration).

i++/i-- increments/decrements i and makes the compiler create a temporary variable for returning the initial value of i. In contrast, ++i/--i returns the actual incremented/decremented value without making the compiler do extra job.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L154
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177

## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas in the for loop below as an example:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75-L91

```
        for (uint256 i; i < swapLength;) {
            // Fetch swap for V1
            quote.pairs[i] = IJoeFactory(factoryV1).getPair(_route[i], _route[i + 1]);

            if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) {
                (uint256 reserveIn, uint256 reserveOut) = _getReserves(quote.pairs[i], _route[i], _route[i + 1]);

                if (reserveIn > 0 && reserveOut > 0) {
                    quote.amounts[i + 1] = JoeLibrary.getAmountOut(quote.amounts[i], reserveIn, reserveOut);
                    quote.virtualAmountsWithoutSlippage[i + 1] = JoeLibrary.quote(
                        (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
                        reserveIn,
                        reserveOut
                    );
                    quote.fees[i] = 0.003e18; // 0.3%
                }
                unchecked {
                    ++i;
                }

            }
```



