**LBRouter**
- L43/467/468/662/673/674/709/711/778/785/831/838/878/885/951 - When the length of an array is used more than once, it is less expensive to create a variable in length memory and use that variable.

- L43/711/712/713/715/725/727/783/785/836/838/841/883/885/953 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.


**LBQuoter**
- L59/65/69/70/139/144/148/149/177 - When the length of an array is used more than once, it is less expensive to create a variable in memory of the length and use that variable.

-L65/75/77/80/96/102/107/108/114/144/154/156/157/158/161/162/166/172/177/184/186/187/188/192/196 /201 - It is less expensive to do ++i or --i, rather than i++, i-- or i - 1.

- L79/82/99/154/157/160/176 - It is less expensive to validate with uint256 the operation uint256() != 0, instead of uint256() > 0.


**LBFactory**
- L161/191 - It is less expensive to validate with uint256 the operation uint256() != 0, instead of uint256() > 0.

- L543 - Instead of _binStep < MIN_BIN_STEP, since MIN_BIN_STEP == 1 and the values ​​are uint256 it is less expensive to directly validate _binStep == 0, since it is the only possible value.


**LBPair**
- L111/112 - After the first execution it would be less expensive to execute the second if first, that is, the address(tokenX) != address(0), since in the first deploy yes or yes all are executed and after the second , only the if of line 112 would be necessary.

- L274/481/493/496/623/701 - When the length of an array is used more than once, it is less expensive to create a variable in memory of the length and use that variable.


**libraries/FeeDistributionHelpe**
- L34/35/39 - When a variable is used more than once, it would be less expensive to create a variable in memory, this happens with _fees.total.


**libraries/FeeHelper**
- L56/58 - When a variable is used more than once, it would be less expensive to create a variable in memory, this happens with _fp.time.

- L38/39/40/51/52/53 - When a variable is only used once, it doesn't make much sense to create a variable, it could be used directly in the function that is needed. This is seen with the numerator and denominator variables.


**libraries/PendingOwnable**
- L62/70 - Instead of calling a function to edit a variable in storage without validation, it is less expensive to directly call storage and modify it. This is seen with the _setPendingOwner() function.


**libraries/Samples**
- L19 - It is not necessary to set a variable if you want to set its default value, since it is a parameter that has that value by default.
