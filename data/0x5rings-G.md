 --- 
 ## `--i` cost less gas compared to `i--`. Consider using `--{variable}` instead of `{variable}--` 
 ### Files Found: 
 There are 4 instances of this issue. 
 - File: src/LBQuoter.sol - line 154 
 - File: src/LBQuoter.sol - line 154 
 - File: src/LBRouter.sol - line 711 
 - File: src/LBRouter.sol - line 711 
 
 
 Mitigation: 
Consider using `--i` instead of `i--` 

---

 ## `++i` cost less gas compared to `i++`. Consider using `++{variable}` instead of `{variable}++` 
 ### Files Found: 
 There are 4 instances of this issue. 
 - File: src/LBQuoter.sol - line 75 
 - File: src/LBQuoter.sol - line 75 
 - File: src/LBQuoter.sol - line 100 
 - File: src/LBQuoter.sol - line 177 

 Mitigation: 
 Consider using `++i` instead of `i++` 

 --- 
 ## `Abi.Encode()` is less efficient than `abi.Encodepacked()` 
 ### Files Found: 
 There are 1 instances of this issue. 
 - File: src/LBFactory.sol - line 265 
 
Mitigation:  
Use Abi.encode packed where necessary 
 --- 

 ## `_safemint()` should be used rather than `_mint()` wherever possible 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: src/LBPair.sol - line 579 
- File: src/LBToken.sol - line 202 
 
 ### Mitigation: 
 `_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. 

 --- 
 ## `Require()` should be used instead of `assert()` 
 ### Files Found: 
 There are 1 instances of this issue. 
 - File: src/LBFactory.sol - line 141 
 
Mitigation: 

 Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it. assert() should be avoided even past solidity version 0.8.0 as its documentation states that. 
 
 --- 
 ## Using private rather than public for constants, saves gas 
 ### Files Found: 
 There are 4 instances of this issue. 
 - File: src/LBFactory.sol - line 25 
 - File: src/LBFactory.sol - line 27 
 - File: src/LBFactory.sol - line 28 
 - File: src/LBFactory.sol - line 30 
 
 Mitigation: 
 
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata. 

