# QA (LOW & NON-CRITICAL)

## [L-01] Flashloan fee is not validated
`_flashLoanFee` is determined at LBFactory's constructor as;
```solidity
    constructor(address _feeRecipient, uint256 _flashLoanFee) {
        _setFeeRecipient(_feeRecipient);


        flashLoanFee = _flashLoanFee;
        emit FlashLoanFeeSet(0, _flashLoanFee);
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L63-L68)

and in `setFlashLoanFee()` as;
```solidity
    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
        uint256 _oldFlashLoanFee = flashLoanFee;


        if (_oldFlashLoanFee == _flashLoanFee) revert LBFactory__SameFlashLoanFee(_flashLoanFee);


        flashLoanFee = _flashLoanFee;
        emit FlashLoanFeeSet(_oldFlashLoanFee, _flashLoanFee);
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L474-L481)

However, if the the fee somehow is set to an arbitrary ratio such as 100% (even the intention is to set the fee to 10%), this will lead to flashloan causing loss of funds for the user who uses it. 
It would be the best if the flashloan fee is required to be in boundries which is set by the protocol. 

## [L-02] Address(0) can be whitelisted
LBFactory's addQuoteAsset() function does not check whether the parameter `_quoteAsset` is a zero address implementation of address(0). Hence, even behind the onlyowner modifier, it's possible to add an address(0) implementation in whitelist.

```solidity
    function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
        if (!_quoteAssetWhitelist.add(address(_quoteAsset)))
            revert LBFactory__QuoteAssetAlreadyWhitelisted(_quoteAsset);
        emit QuoteAssetAdded(_quoteAsset);
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L493-L498)

Once it's added, an LBPair with Y token being address(0) can be created;
```solidity
    function createLBPair(
        IERC20 _tokenX,
        IERC20 _tokenY,
        uint24 _activeId,
        uint16 _binStep
    ) external override returns (ILBPair _LBPair) {
        address _owner = owner();
        if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);


        address _LBPairImplementation = LBPairImplementation;


        if (_LBPairImplementation == address(0)) revert LBFactory__ImplementationNotSet();


        if (!_quoteAssetWhitelist.contains(address(_tokenY))) revert LBFactory__QuoteAssetNotWhitelisted(_tokenY);


        if (_tokenX == _tokenY) revert LBFactory__IdenticalAddresses(_tokenX);


        // We sort token for storage efficiency, only one input needs to be stored
        (IERC20 _tokenA, IERC20 _tokenB) = _sortTokens(_tokenX, _tokenY);
        // single check is sufficient
        if (address(_tokenA) == address(0)) revert LBFactory__AddressZero();
        if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))
            revert LBFactory__LBPairAlreadyExists(_tokenX, _tokenY, _binStep);


        bytes32 _preset = _presets[_binStep];
        if (_preset == bytes32(0)) revert LBFactory__BinStepHasNoPreset(_binStep);


        uint256 _sampleLifetime = _preset.decode(type(uint16).max, 240);
        // We remove the bits that are not part of the feeParameters
        _preset &= bytes32(uint256(type(uint144).max));


        bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
        _LBPair = ILBPair(Clones.cloneDeterministic(_LBPairImplementation, _salt));


        _LBPair.initialize(_tokenX, _tokenY, _activeId, uint16(_sampleLifetime), _preset);


        _LBPairsInfo[_tokenA][_tokenB][_binStep] = LBPairInformation({
            binStep: _binStep,
            LBPair: _LBPair,
            createdByOwner: msg.sender == _owner,
            ignoredForRouting: false
        });


        allLBPairs.push(_LBPair);


        {
            bytes32 _avLBPairBinSteps = _availableLBPairBinSteps[_tokenA][_tokenB];
            // We add a 1 at bit `_binStep` as this binStep is now set
            _avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) | (1 << _binStep));


            // Increase the number of lb pairs by 1
            _avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) + (1 << 248));


            // Save the changes
            _availableLBPairBinSteps[_tokenA][_tokenB] = _avLBPairBinSteps;
        }


        emit LBPairCreated(_tokenX, _tokenY, _binStep, _LBPair, allLBPairs.length - 1);


        emit FeeParametersSet(
            msg.sender,
            _LBPair,
            _binStep,
            _preset.decode(type(uint16).max, 16),
            _preset.decode(type(uint16).max, 32),
            _preset.decode(type(uint16).max, 48),
            _preset.decode(type(uint16).max, 64),
            _preset.decode(type(uint24).max, 80),
            _preset.decode(type(uint16).max, 104),
            _preset.decode(type(uint24).max, 120)
        );
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L234-L305)

## [L-03] Pair creators might be in an unadvantageous position
LBFactory's `removeQuoteAsset()` removes the asset from the whitelist of quote assets as below;
```solidity
    function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
        if (!_quoteAssetWhitelist.remove(address(_quoteAsset))) revert LBFactory__QuoteAssetNotWhitelisted(_quoteAsset);
        emit QuoteAssetRemoved(_quoteAsset);
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L502-L506)

However, it's not verified whether the asset to be removed is already being used. This might cause an unadvantageous issue to the users;
1. LBPair XY having an address of 0xABC
2. Alice sees the pair is pumping money so she wanted to deploy a pair of XY with a different bin.
3. Since the removeQuoteAsset() was called by the owner, she can't deploy the pair while the other pair makes money.



## [L-04] Rebasing tokens
The protocol supports deflationary/tokens supporting fees on transfer, however, there is no implementation for the rebasing tokens (Ampleforth e.g.).
As the rebasing tokens' process is different than the mentioned ones, a pair balance is subject to change once the rebasing token's `rebase()` function is triggered. It's not necessarily to be performed during the swap function, but a malicious actor who has the admin privilage of a rebasing token can trigger rebase() during the swap function. This may end up supplying less X tokens and getting more Y tokens in return. Especially when the rebasing tokens are negatively rebased.
There should be an implementation where these tokens are blacklisted.

## [L-05] Use of nonReentrant modifier
Non of the functions below in LBRouter contract has `nonReentrant` modifier. Recommend including in;
[swapExactTokensForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L352), [swapExactTokensForAVAX](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L377), [swapExactAVAXForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L407), [swapTokensForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L431), [swapTokensForExactAVAX](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L459), [swapAVAXForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L493), [swapExactTokensForTokensSupportingFeeOnTransferTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L531), [swapExactTokensForAVAXSupportingFeeOnTransferTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L561), [swapExactAVAXForTokensSupportingFeeOnTransferTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L594)

## [L-06] Rugging risk
The factory owner address is having too much authority. If somehow compromised, it can rug the router by the `sweep()`function;
```solidity
    function sweep(
        IERC20 _token,
        address _to,
        uint256 _amount
    ) external override onlyFactoryOwner {
        if (address(_token) == address(0)) {
            if (_amount == type(uint256).max) _amount = address(this).balance;
            _safeTransferAVAX(_to, _amount);
        } else {
            if (_amount == type(uint256).max) _amount = _token.balanceOf(address(this));
            _token.safeTransfer(_to, _amount);
        }
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L622-L634)

## [L-07] Zero address owner
We don't include selfdestruct() function in our contract for some bad experiences occurred in the past.
PendingOwnable's `renounceOwnership()` creates a similar effect leaving the contract without an owner.

## [L-08] Validating position index
LBToken's userPositionAtIndex() checks the user positions for the provided index as follows;

```solidity
    function userPositionAtIndex(address _account, uint256 _index) public view virtual override returns (uint256) {
        return _userIds[_account].at(_index);
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L100-L102)

However, there is no validation of the provided index being less than the index length. The index should be strictly less than the length as stated at OpenZeppelin's EnumerableSet.sol, else it will return as zero. 

## [NC-01] Tautology in LBRouter'S receive() function
LBRouter's `receive() `function requires that the received AVAX should be sent only from the WAVAX contract;
```solidity
    receive() external payable {
        if (msg.sender != address(wavax)) revert LBRouter__SenderIsNotWAVAX();
    }
```
[Permalink](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L63-L65)

However, if any contract, having AVAX > 0, selfdestructs and the recipient is the LBRouter's address, it will forcibly receive the AVAX. However, the sweep function considers to send back the AVAX to the sender who mistakenly sent AVAX to the contract resulting in tautology.
I recommend removing `if (msg.sender != address(wavax)) revert LBRouter__SenderIsNotWAVAX();` condition.olumePerLiquidityInBlock` variables are shadowed with the global ones.