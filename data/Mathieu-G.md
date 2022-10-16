# Tight Variable Packing
The Tight Variable Packing pattern in Solidity allows the packing of storage variables where possible so that they are stored within the same 32 bytes slot.

In the factory file `LBFactory.sol`, from line 32 to 39 : https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L32:L39 we can see 4 storage variables which are not optimally defined in order to benefits from this pattern: `LBPairImplementation` (address), `feeRecipient`(address), `flashLoanFee`(uint256), `creationUnlocked`(bool).

As per Solidity, here are the following bytes used for the these types:
- address: 20 bytes
- uint256: 32 bytes
- bool: 1 byte

So, with this `uint256` variable, we are using 4 slots with each of these variables occupying a slot.
The goal here is to ideally pack these 4 variables into 2 slots so we can benefit of lower gas consumption.

In order to achieve this, we would first change the type of the `flashLoanFee` to a `uint88` (11 bytes) because there is no need for this variable to be greater than `309,485,009,821,345,068,724,781,055` as it is expressed in basis point. Then, we just need to apply the change:

    address public override LBPairImplementation;

    address public override feeRecipient;

    uint88 public override flashLoanFee;

    /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
    bool public override creationUnlocked;

With this new declaration, `LBPairImplementation` is stored into 1 slot and `feeRecipient`, `flashLoanFee`, `creationUnlocked` are stored into a second slot of 32 bytes. (20 + 11 + 1)

Of course, the type of the `flashLoanFee` must be changed to `uint88` in every following function and in the event declaration:
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L63:L68 (constructor)
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474:L481 (setFlashLoanFee)
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#L36 (event FlashLoanFeeSet)

In addition to this, the `LBPair` contract must be modified when doing a flash loan (e.g. `flashLoan` function), the type of the variable related to the fees must be changed to `uint88`:
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L428
- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1044

And the content of the `_getFlashLoanFee` function requires a slight modification to properly handle the calculation:

    function _getFlashLoanFee(uint256 _amount, uint88 _fee) internal pure returns (uint256) {
        return (_amount * uint256(_fee)) / Constants.PRECISION;
    }

### Other possibility
In case the type of the `flashLoanFee` variable cannot be reduced to a `uint88` for some reason and should remain a `uint256`, I would just recommend to change the declaration's order of the variables this way:

    address public override LBPairImplementation;

    address public override feeRecipient;

    /// @notice Whether the createLBPair function is unlocked and can be called by anyone (true) or only by owner (false)
    bool public override creationUnlocked;

    uint256 public override flashLoanFee;

This would simply use 3 storage slots instead of 4.