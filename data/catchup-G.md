

## G01 Change 2 bool state variables to uint8 (each ~20K gas, ~40K total)
```    
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Consider changing the bool's ```creationUnlocked``` and ```_spenderApprovals``` to uint and use values ```1``` and ```2``` rather than ```true``` and ```false```.
This would save an extra SLOAD(100), and an additional Gsset(20K) when changing from ```false` to ```true```.

### Lines of code
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L39
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L21
