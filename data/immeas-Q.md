## NC-1 unclear documentation

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L300
Unclear documentation

```javascript
    /// @param _swapForY whether the token sent was Y (true) or X (false)
```
I interpret "sent" as what the contract received and in that case it is the other way around, if `swapForY` is true, the token the contract recived was X. If it refers to what the contract will transfer the tempus is wrong, should be "whether token sent _will_ by Y". If the latter is the case, it's still a bit confusing. It's easiest to understand if it says which token the contract received which would be X if `swapForY` is true.


## NC-2 `testInvalidTokenPathReverts` in wrong test class

https://github.com/code-423n4/2022-10-traderjoe/blob/main/test/LBPair.Swaps.t.sol#L287-L322

It only tests the router but sits in `LBPair.Swaps.t.sol`, better placed in `LBRouter.Swaps.t.sol` as it only interacts with the router
