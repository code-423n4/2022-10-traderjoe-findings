# QA Report

## [L-1]: Volatility accumulator can be be prevented from decaying by way of dust transactions

There is no required minimum swap amount for updating the volatility accumulated. The `_fp.time` [is always updated during a swap](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L72).

```solidity
src/libraries/FeeHelper.sol-69-            }
src/libraries/FeeHelper.sol-70-        }
src/libraries/FeeHelper.sol-71-
src/libraries/FeeHelper.sol:72:        _fp.time = (block.timestamp).safe40(); /// @audit LOW: This can be updated with dust txs.
src/libraries/FeeHelper.sol-73-
src/libraries/FeeHelper.sol-74-        updateVolatilityAccumulated(_fp, _activeId);
src/libraries/FeeHelper.sol-75-    }
```

By making dust transactions within the filter period interval, one can keep the volatility accumulator from decaying. This can be used to manipulate the volatility fees. This can be a profitable attack after the active bin moves by few hundred bins, hence `_activeId.absSub(_fp.indexRef)` is high enough when [updating the volatility fee](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L81-L82).

Consider requiring a minimum amount when updating the `_fp.time`.

## [L-2]: A stricter `code.length` check can be used instead of zero address check

During construction or pair creation, checking external contract `ADDRESS.code.length != 0` check will catch more invalid addresses than just checking `ADDRESS != address(0)`. Consider replacing the four checks below accordingly.

```solidity
src/LBPair.sol-88-    /// @notice Set the factory address
src/LBPair.sol-89-    /// @param _factory The address of the factory
src/LBPair.sol-90-    constructor(ILBFactory _factory) LBToken() {
src/LBPair.sol:91:        if (address(_factory) == address(0)) revert LBPair__AddressZero();
src/LBPair.sol-92-        factory = _factory;
src/LBPair.sol-93-    }
src/LBPair.sol-94-
--
src/LBPair.sol-108-        uint16 _sampleLifetime,
src/LBPair.sol-109-        bytes32 _packedFeeParameters
src/LBPair.sol-110-    ) external override onlyFactory {
src/LBPair.sol:111:        if (address(_tokenX) == address(0) || address(_tokenY) == address(0)) revert LBPair__AddressZero();
src/LBPair.sol-112-        if (address(tokenX) != address(0)) revert LBPair__AlreadyInitialized();
src/LBPair.sol-113-
src/LBPair.sol-114-        __ReentrancyGuard_init();
--
src/LBFactory.sol-242-
src/LBFactory.sol-243-        address _LBPairImplementation = LBPairImplementation;
src/LBFactory.sol-244-
src/LBFactory.sol:245:        if (_LBPairImplementation == address(0)) revert LBFactory__ImplementationNotSet();
src/LBFactory.sol-246-
src/LBFactory.sol-247-        if (!_quoteAssetWhitelist.contains(address(_tokenY))) revert LBFactory__QuoteAssetNotWhitelisted(_tokenY);
src/LBFactory.sol-248-
--
src/LBFactory.sol-251-        // We sort token for storage efficiency, only one input needs to be stored
src/LBFactory.sol-252-        (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
src/LBFactory.sol-253-        // single check is sufficient
src/LBFactory.sol:254:        if (address(_tokenA) == address(0)) revert LBFactory__AddressZero();
src/LBFactory.sol-255-        if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))
src/LBFactory.sol-256-            revert LBFactory__LBPairAlreadyExists(_tokenX, _tokenY, _binStep);
src/LBFactory.sol-257-
```

## [L-3]: Explicit return when there is a named return

There is an instance where there is a named return, but another value is explicity returned. Consider using unnamed returns in these cases.

```solidity
src/LBPair.sol-990-    /// @notice Internal view function to get the bin at `id`
src/LBPair.sol-991-    /// @param _id The bin id
src/LBPair.sol-992-    /// @return reserveX The reserve of tokenX of the bin
src/LBPair.sol-993-    /// @return reserveY The reserve of tokenY of the bin
src/LBPair.sol-994-    function _getBin(uint24 _id) internal view returns (uint256 reserveX, uint256 reserveY) {
src/LBPair.sol-995-        bytes32 _data;
src/LBPair.sol-996-        uint256 _mask112 = type(uint112).max;
src/LBPair.sol-997-        // low level read of mapping to only load 1 storage slot
src/LBPair.sol-998-        assembly {
src/LBPair.sol-999-            mstore(0, _id)
src/LBPair.sol-1000-            mstore(32, _bins.slot)
src/LBPair.sol-1001-            _data := sload(keccak256(0, 64))
src/LBPair.sol-1002-
src/LBPair.sol-1003-            reserveX := and(_data, _mask112)
src/LBPair.sol-1004-            reserveY := shr(_OFFSET_BIN_RESERVE_Y, _data)
src/LBPair.sol-1005-        }
src/LBPair.sol-1006-
src/LBPair.sol:1007:        return (reserveX.safe112(), reserveY.safe112()); /// @audit INFO: Return on named return.
src/LBPair.sol-1008-    }
```

## [I-1]: Inconsistent behaviour in `PendingOwnable`

`PendingOwnable` [requires that there is no pending owner when setting a new pending owner](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/PendingOwnable.sol#L57-L63). However, this behaviour is not enforced when renouncing ownership. Consider ensuring that there is no pending owner when renouncing ownership.

```diff
diff --git a/src/LBErrors.sol b/src/LBErrors.sol
index 092ede5..ff0feef 100644
--- a/src/LBErrors.sol
+++ b/src/LBErrors.sol
@@ -123,6 +123,7 @@ error Oracle__NotInitialized();
 error PendingOwnable__NotOwner();
 error PendingOwnable__NotPendingOwner();
 error PendingOwnable__PendingOwnerAlreadySet();
+error PendingOwnable__ThereIsPendingOwner();
 error PendingOwnable__NoPendingOwner();
 error PendingOwnable__AddressZero();

diff --git a/src/libraries/PendingOwnable.sol b/src/libraries/PendingOwnable.sol
index f745362..39bce22 100644
--- a/src/libraries/PendingOwnable.sol
+++ b/src/libraries/PendingOwnable.sol
@@ -82,6 +82,7 @@ contract PendingOwnable is IPendingOwnable {
     /// NOTE: Renouncing ownership will leave the contract without an owner,
     /// thereby removing any functionality that is only available to the owner.
     function renounceOwnership() public override onlyOwner {
+        if (_pendingOwner != address(0)) revert PendingOwnable__ThereIsPendingOwner();
         _transferOwnership(address(0));
     }

```

## [I-2]: Using OpenZeppelin's `AccessControl` library would allow relinquishing certain admin powers

There are many admin functions in the Liquidity Book protocol. Some of these functions can be considered to increase the centralization of the protocol. For example `setLBPairIgnored()` and `setLBPairImplementation()` are too powerful. Trader Joe team might in the feature want to irreversibly relinquish the usage of these functions without relinquishing the ability to execute other admin functions. Using AccessControl and having separate self-managed roles have access to certain functions would allow renouncing those roles selectively when the time comes.

## [I-3]: Typos or misleading comments

I have encountered the following issues in the comments. Please refer to the `/// @audit` tags.

```solidity
src/libraries/BitMath.sol-6-/// @author Trader Joe
src/libraries/BitMath.sol-7-/// @notice Helper contract used for bit calculations
src/libraries/BitMath.sol-8-library BitMath {
src/libraries/BitMath.sol:9:    /// @notice Returns the closest non-zero bit of `integer` to the right (of left) of the `bit` bits that is not `bit` /// @audit INFO: typo: of -> or
src/libraries/BitMath.sol-10-    /// @param _integer The integer as a uint256
src/libraries/BitMath.sol-11-    /// @param _bit The bit index
src/libraries/BitMath.sol-12-    /// @param _rightSide Whether we're searching in the right side of the tree (true) or the left side (false)
--
src/libraries/FeeHelper.sol-17-    /// - binStep: The bin step
src/libraries/FeeHelper.sol-18-    /// - baseFactor: The base factor
src/libraries/FeeHelper.sol-19-    /// - filterPeriod: The filter period, where the fees stays constant
src/libraries/FeeHelper.sol:20:    /// - decayPeriod: The decay period, where the fees are halved /// @audit INFO: Fees are decayed by the decay factor, they are not halved.
src/libraries/FeeHelper.sol-21-    /// - reductionFactor: The reduction factor, used to calculate the reduction of the accumulator
src/libraries/FeeHelper.sol-22-    /// - variableFeeControl: The variable fee control, used to control the variable fee, can be 0 to disable them
src/libraries/FeeHelper.sol-23-    /// - protocolShare: The share of fees sent to protocol
--
src/libraries/Math512Bits.sol-51-    ///
src/libraries/Math512Bits.sol-52-    /// @param x The multiplicand as an uint256
src/libraries/Math512Bits.sol-53-    /// @param y The multiplier as an uint256
src/libraries/Math512Bits.sol:54:    /// @param offset The offset as an uint256, can't be greater than 256 /// @audit INFO: typo: 256 -> 255
src/libraries/Math512Bits.sol-55-    /// @return result The result as an uint256
src/libraries/Math512Bits.sol-56-    function mulShiftRoundDown(
src/libraries/Math512Bits.sol-57-        uint256 x,
--
src/libraries/Math512Bits.sol-87-    ///
src/libraries/Math512Bits.sol-88-    /// @param x The multiplicand as an uint256
src/libraries/Math512Bits.sol-89-    /// @param y The multiplier as an uint256
src/libraries/Math512Bits.sol:90:    /// @param offset The offset as an uint256, can't be greater than 256 /// @audit INFO: typo: 256 -> 255
src/libraries/Math512Bits.sol-91-    /// @return result The result as an uint256
src/libraries/Math512Bits.sol-92-    function mulShiftRoundUp(
src/libraries/Math512Bits.sol-93-        uint256 x,
--
src/libraries/FeeHelper.sol-127-    }
src/libraries/FeeHelper.sol-128-
src/libraries/FeeHelper.sol:129:    /// @notice Return the fees added when an user adds liquidity and change the ratio in the active bin /// @audit INFO: typo: an user -> a user
src/libraries/FeeHelper.sol-130-    /// @param _fp The current fee parameter
src/libraries/FeeHelper.sol-131-    /// @param _amountPlusFee The amount of token sent
src/libraries/FeeHelper.sol-132-    /// @return The fee amount
--
src/libraries/FeeDistributionHelper.sol-45-
src/libraries/FeeDistributionHelper.sol-46-    /// @notice Calculate the tokenPerShare when fees are added
src/libraries/FeeDistributionHelper.sol-47-    /// @param _fees The fees received by the pair
src/libraries/FeeDistributionHelper.sol:48:    /// @param _totalSupply the total supply of a specific bin /// @audit INFO: Missing NATSPEC for return value.
src/libraries/FeeDistributionHelper.sol-49-    function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
src/libraries/FeeDistributionHelper.sol-50-        internal
src/libraries/FeeDistributionHelper.sol-51-        pure
--
src/LBRouter.sol-67-    /// @notice Returns the approximate id corresponding to the inputted price.
src/LBRouter.sol-68-    /// Warning, the returned id may be inaccurate close to the start price of a bin
src/LBRouter.sol-69-    /// @param _LBPair The address of the LBPair
src/LBRouter.sol:70:    /// @param _price The price of y per x (multiplied by 1e36) /// @audit INFO: It is not multiplied, it is a 128.128 fixed-point number.
src/LBRouter.sol-71-    /// @return The id corresponding to this price
src/LBRouter.sol-72-    function getIdFromPrice(ILBPair _LBPair, uint256 _price) external view override returns (uint24) {
src/LBRouter.sol-73-        return BinHelper.getIdFromPrice(_price, _LBPair.feeParameters().binStep);
--
src/LBToken.sol-23-    /// @dev Mapping from token id to total supplies
src/LBToken.sol-24-    mapping(uint256 => uint256) private _totalSupplies;
src/LBToken.sol-25-
src/LBToken.sol:26:    /// @dev  Mapping from account to set of ids, where user currently have a non-zero balance /// @audit INFO: Double space between `@dev` and `Mapping`.
src/LBToken.sol-27-    mapping(address => EnumerableSet.UintSet) private _userIds;
src/LBToken.sol-28-
src/LBToken.sol-29-    string private constant _name = "Liquidity Book Token";
--
src/LBToken.sol-270-        return _owner == _spender || _spenderApprovals[_owner][_spender];
src/LBToken.sol-271-    }
src/LBToken.sol-272-
src/LBToken.sol:273:    /// @notice Internal function to add an id to an user's set /// @audit INFO: typo: an user's -> a user's
src/LBToken.sol-274-    /// @param _account The user's address
src/LBToken.sol-275-    /// @param _id The id of the token
src/LBToken.sol-276-    /// @param _accountBalance The user's balance
--
src/LBToken.sol-286-        }
src/LBToken.sol-287-    }
src/LBToken.sol-288-
src/LBToken.sol:289:    /// @notice Internal function to remove an id from an user's set /// @audit INFO: typo: an user's -> a user's
src/LBToken.sol-290-    /// @param _account The user's address
src/LBToken.sol-291-    /// @param _id The id of the token
src/LBToken.sol-292-    /// @param _accountBalance The user's balance
```