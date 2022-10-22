1.LBToken: In the burn function, _beforeTokenTransfer uses incorrect from and to.

Burning tokens should be transferred to 0 address.

Proof of Concept
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L237

Recommended Mitigation Steps
`_beforeTokenTransfer( _account, address(0), _id, _amount);`