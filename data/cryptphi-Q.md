1. Missing zero value check
The LBFactory constructor and setFlashLoanFee() function allow zero value input. This could set the flashloan fee to 0, allowing users to borrow tokens for free.
LBFactory.constructor() - _flashLoanFee argument
LBFactory.setFlashLoanFee() - _flashLoanFee argument

2.  Possible overflow - There is a possible overflow in TokenHelper.received() due to the unchecked block,