https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol
Line 3
Suggestion: Lock the solidity version and specify it without ^.

Lines 29 and 30, _name and _symbol can be declare as public, in this way, two public getter functions can be generated without the explicit implementation of name() and symbol(). 

Line 71, function balanceOf(address _account, uint256 _id) should be redefined as 
 function balanceOf(uint256 _id, address _account). This can greatly improve the readability and thus trustworthiness of the code. This will be consistent with the state variable _balances also. 


Line 79, function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids) should be redefined as
function balanceOfBatch(, uint256[] memory _ids, address[] memory _accounts). This can greatly improve the readability and thus trustworthiness of the code. This will be consistent with the state variable _balances also.

Line 115, isApprovedForAll can be implemented directly without wrapping around _isApprovedForAll. Such wrapping added unnecessary complexity.

Line 122 setApprovalForAll can be implemented directly without wrapping around _setApprovalForAll. Such wrapping added unnecessary complexity.

Line 137, this assignment is unnecessary, the first argument for Line 141 can be simply changed to msg.sender.

Line 237, _beforeTokenTransfer(address(0), _account, _id, _amount); 
should be
_beforeTokenTransfer(_account, address(0),  _id, _amount);




 