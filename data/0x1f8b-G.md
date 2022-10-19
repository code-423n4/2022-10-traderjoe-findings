- [Gas](#gas)
    - [**1. Use require instead of assert**](#1-use-require-instead-of-assert)
    - [**2. Don't use the length of an array for loops condition**](#2-dont-use-the-length-of-an-array-for-loops-condition)
    - [**3. Avoid compound assignment operator in state variables**](#3-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 5 = 65**](#total-gas-saved-13--5--65)
    - [**4. Remove empty blocks**](#4-remove-empty-blocks)
    - [**5. Use calldata instead of memory**](#5-use-calldata-instead-of-memory)
    - [**6. ++i costs less gas compared to i++ or i += 1**](#6-i-costs-less-gas-compared-to-i-or-i--1)
    - [**7. Shift right or left instead of dividing or multiply by 2**](#7-shift-right-or-left-instead-of-dividing-or-multiply-by-2)
        - [Total gas saved: **172 * 1 = 172**](#total-gas-saved-172--1--172)
    - [**8. There's no need to set default values for variables**](#8-theres-no-need-to-set-default-values-for-variables)
        - [Total gas saved: **8 * 1 = 8**](#total-gas-saved-8--1--8)
    - [**9. Use the unchecked keyword**](#9-use-the-unchecked-keyword)
    - [**10. Optimize LBFactory.getPreset**](#10-optimize-lbfactorygetpreset)
    - [**11. Optimize LBFactory._sortTokens**](#11-optimize-lbfactory_sorttokens)
    - [**12. Optimize PendingOwnable._transferOwnership**](#12-optimize-pendingownable_transferownership)

# Gas

## **1. Use `require` instead of `assert`**

The `assert()` and `require()` functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the `assert()` function when false, **uses up all the remaining gas and reverts all the changes made.**

Meanwhile, a `require()` function when false, also reverts back all the changes made to the contract but **does refund all the remaining gas fees we offered to pay**. This is the most common Solidity function used by developers for debugging and error handling.

**Affected source code:**

- [LBFactory.sol:141](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L141)

## **2. Don't use the length of an array for loops condition**

It's cheaper to store the length of the array inside a local variable and iterate over it.

**Affected source code:**

- [LBPair.sol:274](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274)
- [LBPair.sol:496](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L496)
- [LBPair.sol:623](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L623)
- [LBPair.sol:701](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L701)
- [LBQuoter.sol:100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)
- [LBQuoter.sol:177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)
- [LBRouter.sol:674](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L674)
- [LBRouter.sol:778](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L778)
- [LBRouter.sol:831](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L831)
- [LBRouter.sol:878](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L878)
- [LBRouter.sol:951](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L951)
- [LBToken.sol:90](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90)
- [LBToken.sol:163](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L163)

## **3. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
uint private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [FeeDistributionHelper.sol:42](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L42)
- [LBPair.sol:452](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L452)
- [LBPair.sol:453](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L453)
- [LBPair.sol:717](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L717)
- [LBPair.sol:720](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L720)

### Total gas saved: **13 * 5 = 65**

## **4. Remove empty blocks**

An empty block or an empty method generally indicates a lack of logic that it’s unnecessary and should be eliminated, call a method that literally does nothing only consumes gas unnecessarily, if it is a `virtual` method which is expects it to be filled by the class that implements it, it is better to use `abstract`  contracts with just the definition.

**Sample code:**

```javascript
pragma solidity >=0.4.0 <0.7.0;

abstract contract BaseContract {
    function totalSupply() public virtual returns (uint256);
}
```

**Reference:**
- https://docs.soliditylang.org/en/v0.6.0/contracts.html?highlight=abstract#abstract-contracts

**Affected source code:**

- [LBToken.sol:321-326](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L321-L326)

## **5. Use `calldata` instead of `memory`**

Some methods are declared as `external` but the arguments are defined as `memory` instead of as `calldata`.

By marking the function as `external` it is possible to use `calldata` in the arguments shown below and save significant gas.

**Recommended change:**

```diff
-   function addLiquidity(LiquidityParameters memory _liquidityParameters)
+   function addLiquidity(LiquidityParameters calldata _liquidityParameters)
        external
        override
        returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
    {
  ...
  }
```

**Affected source code:**

- [ILBPair.sol:201](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L201)
- [ILBPair.sol:207](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L207)
- [ILBPair.sol:218](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L218)
- [ILBPair.sol:222-224](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L222-L224)
- [ILBPair.sol:235-236](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L235-L236)
- [ILBPair.sol:242](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L242)
- [ILBFlashLoanCallback.sol:15](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFlashLoanCallback.sol#L15)
- [ILBRouter.sol:90-91](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L90-L91)
- [ILBRouter.sol:101-102](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L101-L102)
- [ILBRouter.sol:110-111](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L110-L111)
- [ILBRouter.sol:119-120](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L119-L120)
- [ILBRouter.sol:127-128](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L127-L128)
- [ILBRouter.sol:136-137](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L136-L137)
- [ILBRouter.sol:145-146](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L145-L146)
- [ILBRouter.sol:153-154](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L153-L154)
- [ILBRouter.sol:162-163](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L162-L163)
- [ILBRouter.sol:171-172](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L171-L172)
- [ILBRouter.sol:179-180](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L179-L180)
- [ILBRouter.sol:194-195](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBRouter.sol#L194-L195)
- [LBRouter.sol:207-223](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L207-L223)
- [LBRouter.sol:230-259](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L230-L259)

## **6. `++i` costs less gas compared to `i++` or `i += 1`**

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integers, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments `i` and returns the initial value of `i`. Which means:

```solidity
uint i = 1;
i++; // == 1 but i == 2
```

But `++i` returns the actual incremented value:

```solidity
uint i = 1;
++i; // == 2 and i == 2 too, so no need for a temporary variable
```

In the first case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`
I suggest using `++i` instead of `i++` to increment the value of an uint variable. Same thing for `--i` and `i--`

*Keep in mind that this change can only be made when we are not interested in the value returned by the operation, since the result is different, you only have to apply it when you only want to increase a counter.*

**Affected source code:**

- [LBQuoter.sol:75](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L75)
- [LBQuoter.sol:100](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L100)
- [LBQuoter.sol:154](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154)
- [LBQuoter.sol:177](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L177)
- [LBRouter.sol:711](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711)

## **7. Shift right or left instead of dividing or multiply by 2**

Shifting one to the right will calculate a division by two.

he `SHR` opcode only requires 3 gas, compared to the `DIV` opcode's consumption of 5. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testDiv(uint a) public returns (uint) { return a / 2; }
}

contract TesterB {
function testShift(uint a) public returns (uint) { return a >> 1; }
}
```

Gas saving executing: **172 per entry**

```
TesterA.testDiv:    21965 
TesterB.testShift:  21793   
```

The same optimization can be used to multiply by 2, using the left shift.

```javascript
pragma solidity 0.8.16;

contract TesterA {
function testMul(uint a) public returns (uint) { return a * 2; }
}

contract TesterB {
function testShift(uint a) public returns (uint) { return a << 1; }
}
```

Gas saving executing: **201 per entry**

```
TesterA.testMul:    21994
TesterB.testShift:  21793    
```

**Affected source code:**

`/`:
- [Oracle.sol:162](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L162)

### Total gas saved: **(172 * 1) = 172**

## **8. There's no need to set default values for variables**

If a variable is not set/initialized, the default value is assumed (0, `false`, 0x0 ... depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testInit() public view returns (uint) { uint a = 0; return a; }
}

contract TesterB {
function testNoInit() public view returns (uint) { uint a; return a; }
}
```

Gas saving executing: **8 per entry**

```
TesterA.testInit:   21392
TesterB.testNoInit: 21384
```

**Affected source code:**

- [Samples.sol:19](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L19)

### Total gas saved: **8 * 1 = 8**

## **9. Use the `unchecked` keyword**

When an underflow or overflow cannot occur, one might conserve gas by using the `unchecked` keyword to prevent unnecessary arithmetic underflow/overflow tests.

**Affected source code:**

- [LBFactory.sol:165](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L165)
- [LBFactory.sol:168](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L168)
- [LBFactory.sol:195](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L195)
- [LBFactory.sol:205](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L205)

## **10. Optimize `LBFactory.getPreset`**

It's possible to remove the `_shift` variable like this:

```diff
-       uint256 _shift;

        // Safety check
-       assert(_binStep == _preset.decode(type(uint16).max, _shift));
+       assert(_binStep == _preset.decode(type(uint16).max, 0));

-       baseFactor = _preset.decode(type(uint16).max, _shift += 16);
+       baseFactor = _preset.decode(type(uint16).max, 16);
-       filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
+       filterPeriod = _preset.decode(type(uint16).max, 32);
-       decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
+       decayPeriod = _preset.decode(type(uint16).max, 48);
-       reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
+       reductionFactor = _preset.decode(type(uint16).max, 64);
-       variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
+       variableFeeControl = _preset.decode(type(uint24).max, 80);
-       protocolShare = _preset.decode(type(uint16).max, _shift += 24);
+       protocolShare = _preset.decode(type(uint16).max, 104);
-       maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);
+       maxVolatilityAccumulated = _preset.decode(type(uint24).max, 120);

-       sampleLifetime = _preset.decode(type(uint16).max, 240);
+       sampleLifetime = _preset.decode(type(uint16).max, 240);
```

**Affected source code:**

- [LBFactory.sol:138-151](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L138-L151)

## **11. Optimize `LBFactory._sortTokens`**

It's possible to remove one variable assignation like this:

```diff
    function _sortTokens(IERC20 _tokenA, IERC20 _tokenB) private pure returns (IERC20, IERC20) {
-       if (_tokenA > _tokenB) (_tokenA, _tokenB) = (_tokenB, _tokenA);
+       if (_tokenA > _tokenB) return (_tokenB, _tokenA);
        return (_tokenA, _tokenB);
    }
```

**Affected source code:**

- [LBFactory.sol:607-610](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L607-L610)

## **12. Optimize `PendingOwnable._transferOwnership`**

It's possible to remove one variable like this:

```diff
    function _transferOwnership(address _newOwner) internal virtual {
-       address _oldOwner = _owner;
+       emit OwnershipTransferred(_owner, _newOwner);
        _owner = _newOwner;
        _pendingOwner = address(0);
-       emit OwnershipTransferred(_oldOwner, _newOwner);
    }
```

**Affected source code:**

- [PendingOwnable.sol:91-96](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L91-L96)
