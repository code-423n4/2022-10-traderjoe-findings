# Tight Variable Packing
The Tight Variable Packing pattern in Solidity allows the packing of storage variables where possible so that they are stored within the same 32 bytes slot.

In the factory file `LBFactory.sol`, from line 32 to 39 : https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L32:L39 we can see 4 storage variables which are not defined in an optimal order to benefits from this pattern: `LBPairImplementation` (address), `feeRecipient`(address), `flashLoanFee`(uint256), `creationUnlocked`(bool).

As per Solidity, here are the following bytes used for the theses types:
- address: 20 bytes
- uint256: 32 bytes
- bool: 1 byte

So, with this order of declaration, we are using 4 slots with each of these variables occupying a slot.
The goal here is to ideally pack these 4 variables into 2 slots so we can benefit of lower gas consumption.

In order to achieve this, we would first change the type of the `flashLoanFee` to a `uint64` (8 bytes) because there is no need for this variable to be greater than `18446744073709551615`. Then, we just need to change the declaration's order into this:

    address public override LBPairImplementation;

    address public override feeRecipient;

    uint64 public override flashLoanFee;

    /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
    bool public override creationUnlocked;

With this new order, `LBPairImplementation` is stored into 1 slot and `feeRecipient`, `flashLoanFee`, `creationUnlocked` are stored into a second slot of 29 bytes. (20 + 8 + 1)

Of course, the type of the `flashLoanFee` must be changed to `uint64` in every following function and in the event declaration:
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63:L68 (constructor)
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474:L481 (setFlashLoanFee)
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L36 (event FlashLoanFeeSet)