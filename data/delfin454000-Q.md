## QA Report: Low risk

### Missing checks for address(0x0) when assigning values to address state variables
Checks are missing for all three variables below:

[LBQuoter.sol: L45-47](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L45-L47)
```solidity
        routerV2 = _routerV2;
        factoryV1 = _factoryV1;
        factoryV2 = _factoryV2;
```
___
___


## QA Report: Non-critical

### Typos
___
[LBQuoter.sol: L229](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L229)
```solidity
    /// @param _activeId Current active Id of the considred pair
```
Change `considred` to `considered`
___
[Math128x128.sol: L37](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L37)
```solidity
        // If we use an offset of 128 bits, y would need 129 bits and y**2 would would overflow and we would have to
```
Remove repeated word `would`
___
[ILBPair.sol: L68](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBPair.sol#L68)
```solidity
    /// @dev Structure to minting informations:
```
Change `informations` to `information`
___
___

### Open warnings or concerns
Issues that prompt warnings should be addressed, if possible. Below are three instances:
___
[LBPair.sol: L97-98](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L97-L98)
```solidity
    /// It is highly recommended to never call this function directly, use the factory
    /// as it validates the different parameters
```
___
[LBRouter.sol: L68](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L68)
```solidity
    /// Warning, the returned id may be inaccurate close to the start price of a bin
```
___
[BinHelper.sol: L17](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L17)
```solidity
    /// @dev The id may be inaccurate due to rounding issues, always trust getPriceFromId rather than
```
This pairs with the following:

[BinHelper.sol: L35](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/BinHelper.sol#L35)
```solidity
    /// @dev This is the trusted function to link id to price, the other way may be inaccurate
```
___
___


### NatSpec is incomplete for the following `functions`:
___
[FeeDistributionHelper.sol: L46-60](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeDistributionHelper.sol#L46-L60)
```solidity
    /// @notice Calculate the tokenPerShare when fees are added
    /// @param _fees The fees received by the pair
    /// @param _totalSupply the total supply of a specific bin
    function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
        internal
        pure
        returns (uint256)
    {
        unchecked {
            // This can't overflow as `totalFees >= protocolFees`,
            // shift can't overflow as we shift fees that are a uint128, by 128 bits.
            // The result will always be smaller than max(uint256)
            return ((uint256(_fees.total) - _fees.protocol) << Constants.SCALE_OFFSET) / _totalSupply;
        }
    }
```
Missing: `@return` 
___
[PendingOwnable.sol: L57-63](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L57-L63)
```solidity
    /// @notice Sets the pending owner address. This address will be able to become
    /// the owner of this contract by calling {becomeOwner}
    function setPendingOwner(address pendingOwner_) public override onlyOwner {
        if (pendingOwner_ == address(0)) revert PendingOwnable__AddressZero();
        if (_pendingOwner != address(0)) revert PendingOwnable__PendingOwnerAlreadySet();
        _setPendingOwner(pendingOwner_);
    }
```
Missing: `@param pendingOwner_`
___
___

### NatSpec is completely missing for the following `functions`:
___
[LBFactory.sol: L520-522](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L520-L522)
```solidity
    function forceDecay(ILBPair _LBPair) external override onlyOwner {
        _LBPair.forceDecay();
    }
```
___
[TreeMath.sol: L70-78](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol#L70-L78)
```solidity
    function addToTree(mapping(uint256 => uint256)[3] storage _tree, uint256 _id) internal {
        // add 1 at the right indices
        uint256 _idDepth2 = _id >> 8;
        uint256 _idDepth1 = _id >> 16;


        _tree[2][_idDepth2] |= 1 << (_id & 255);
        _tree[1][_idDepth1] |= 1 << (_idDepth2 & 255);
        _tree[0][0] |= 1 << _idDepth1;
    }
```
___
[TreeMath.sol: L80-98](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol#L80-L98)
```solidity
    function removeFromTree(mapping(uint256 => uint256)[3] storage _tree, uint256 _id) internal {
        unchecked {
            // removes 1 at the right indices
            uint256 _idDepth2 = _id >> 8;
            // Optimization of `_tree[2][_idDepth2] & (type(uint256).max - (1 << (_id & 255)))`
            uint256 _newLeafValue = _tree[2][_idDepth2] & (type(uint256).max ^ (1 << (_id & 255)));
            _tree[2][_idDepth2] = _newLeafValue;
            if (_newLeafValue == 0) {
                uint256 _idDepth1 = _id >> 16;
                // Optimization of `_tree[1][_idDepth1] & (type(uint256).max - (1 << (_idDepth2 & 255)))`
                _newLeafValue = _tree[1][_idDepth1] & (type(uint256).max ^ (1 << (_idDepth2 & 255)));
                _tree[1][_idDepth1] = _newLeafValue;
                if (_newLeafValue == 0) {
                    // Optimization of `type(uint256).max - (1 << _idDepth1)`
                    _tree[0][0] &= type(uint256).max ^ (1 << _idDepth1);
                }
            }
        }
    }
```
___
___


### Long single line comments 
In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments are acceptable and a scroll bar is provided, very long comments can interfere with readability. Below are five examples of lines or set of lines over 120 characters long that could be improved by wrapping, as shown:
___
[LBPair.sol: L571](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L571)
```solidity
                // The addition or the cast can't overflow as it would have reverted during the L568 and L569 if amounts were greater than uint112
```
Suggestion:
```solidity
                // The addition or the cast can't overflow as it would have reverted
                //    during the L568 and L569 if amounts were greater than uint112.
```
___
[Math128x128.sol: L30-31](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Math128x128.sol#L30-L31)
```solidity
    /// - The results are not perfectly accurate to the last decimal, due to the lossy precision of the iterative approximation
    /// Also because x is converted to an unsigned 129.127-binary fixed-point number during the operation to optimize the multiplication
```
Suggestion:
```solidity
    /// - The results are not perfectly accurate to the last decimal, due to 
    ///   the lossy precision of the iterative approximation and also because 
    ///   x is converted to an unsigned 129.127-binary fixed-point number during
    ///   the operation to optimize the multiplication.
```
___
[ILBFactory.sol: L18](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/interfaces/ILBFactory.sol#L18)
```solidity
    /// - ignoredForRouting: Whether the pair is ignored for routing or not. An ignored pair will not be explored during routes finding
```
Suggestion:
```solidity
    /// - ignoredForRouting: Whether the pair is ignored for routing or not —
    ///   an ignored pair will not be explored during routes finding.
```
___
[LBFactory.sol: L56](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L56)
```solidity
    // Whether a LBPair was created with a bin step, if the bit at `index` is 1, it means that the LBPair with binStep `index` exists
```
Suggestion:
```solidity
    // Whether a LBPair was created with a bin step, if the bit at `index` is 1,
    //   it means that the LBPair with binStep `index` exists.
```
___
[LBRouter.sol: L122](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L122)
```solidity
                // We update the fee, but we don't store the new volatility reference, volatility accumulated and indexRef to not penalize traders
```
Suggestion:
```solidity
                // We update the fee, but we don't store the new volatility reference, 
                //   volatility accumulated and indexRef to not penalize traders.
```
___
___

### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/). `Trader Joe v2` currently incorporates `whitelist` and related terms, as the example below shows:
___
[LBFactory.sol: L76-80](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L76-L80)
```solidity
    /// @notice View function to return the number of quote assets whitelisted
    /// @return The number of quote assets
    function getNumberOfQuoteAssets() external view override returns (uint256) {
        return _quoteAssetWhitelist.length();
    }
```
Suggestion: Change `whitelisted` to `allowlisted` throughout. Similarly for other variants of `whitelist`.
___
___
