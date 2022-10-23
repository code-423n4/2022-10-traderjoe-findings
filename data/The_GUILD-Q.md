BeforeTokenTransfer called in the the burn function misplaced parameter

The before token transfer argument in the burn function misplaced it's argument and won't work as the authour intended.

context
<https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L237>
        _beforeTokenTransfer(address(0), _account, _id, _amount);

Recommendation: You cannot burn from address(0) but rather burn to address(0), so the above line of code to be change to the code below.

        _beforeTokenTransfer(_account, address(0), _id, _amount);