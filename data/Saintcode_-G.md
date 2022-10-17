## USE CALLDATA INSTEAD OF MEMORY

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution.

When arguments are read-only on external functions, the data location should be `calldata`

Instances: 
LBPair.sol lines 261, 467, 468, 469, 617, 618, 688, 845, 862, 881
LBQuoter.sol lines  54, 134
LBRouter.sol lines 42, 207, 230, 280, 281, 313, 314, 355, 356, 380, 381, 409, 410, 434, 435, 462, 463, 495, 496, 534, 535, 564, 565, 596, 597, 645, 646, 656, 702, 703, 704, 745, 746, 764, 765, 766, 817, 818, 819, 820


## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

14 Instances:
-LBPair.sol lines 274,  496, 623, 701
-LBQuoter.sol lines 100, 177
-LBRouter.sol lines 674, 711, 778, 831, 878, 951
-LBToken.sol lines 90, 163

## USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another memory array/struct

8 Instances:
-LBFactory.sol lines 197, 320
-LBPair.sol lines 283, 327, 498, 706, 817, 850

## <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES

8 Instances:
-LBPair.sol lines 447, 448, 717, 720
-LBToken.sol line 211, 241
-FeeDistributor.sol line 39, 42


## `++I` INSTEAD OF `I++` (OR USE ASSEMBLY WHEN APPLICABLE)

Use `++i` instead of ` i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. 

5 Instances:
LBQuoter.sol lines 75, 100, 177, 154
LBRouter.sol line 711


## `>=` COSTS LESS GAS THAN `>`

The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which saves 3 gas

11 Instances:
LBFactory.sol lines 161, 191
LBPair.sol lines 749, 764
LBQuoter.sol lines 79, 82, 99, 154, 157, 160, 176

## `++I/I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
`
   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}  `

5 Instances:
-LBQuoter.sol lines 75, 100, 154, 177
-LBRouter.sol line 711


## USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

4 Instances:
LBFactory.sol lines 25, 27, 28, 30

## DON’T USE SAFEMATH ONCE THE SOLIDITY VERSION IS 0.8.0 OR GREATER
Version 0.8.0 introduces internal overflow checks, so using `SafeMath` is redundant and adds overhead

Used in LBPair.sol, FeeHelper.sol, SafeMath.sol, SwapHelper.sol


