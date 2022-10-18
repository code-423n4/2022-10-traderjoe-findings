## STRUCTS CAN BE PACKED INTO FEWER STORAGE SLOTS
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. 
Subsequent reads as well as writes have smaller gas savings

### Proof of Concept
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L19-L24

### Recommended Mitigation Steps
```solidity
struct LBPairInformation {
    ILBPair LBPair;
    uint24 binStep;
    bool createdByOwner;
    bool ignoredForRouting;
}
```

## Can use msg.sender directly
`LBToken.safeTransferFrom`
Declare _spender but it only use once

### Proof of Concept
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L137

### Recommended Mitigation Steps
```solidity
function safeTransferFrom(
    address _from,
    address _to,
    uint256 _id,
    uint256 _amount
) public virtual override checkAddresses(_from, _to) checkApproval(_from, msg.sender) {
    _transfer(_from, _to, _id, _amount);
    emit TransferSingle(msg.sender, _from, _to, _id, _amount);
}
```
