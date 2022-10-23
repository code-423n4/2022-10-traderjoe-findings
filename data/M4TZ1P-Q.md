# Missing binStep validation in LBFactory.setPreset

The value of binStep is up to 247. Checking whether _binStep is lower than 248 in LBFactory.setPreset can prevents binStep from being set incorrectly.

adding require statement that checks _binStep is lower than 248 is recommended.

```
    function setPreset(
        uint16 _binStep,
        uint16 _baseFactor,
        uint16 _filterPeriod,
        uint16 _decayPeriod,
        uint16 _reductionFactor,
        uint24 _variableFeeControl,
        uint16 _protocolShare,
        uint24 _maxVolatilityAccumulated,
        uint16 _sampleLifetime
    ) external override onlyOwner {
        bytes32 _packedFeeParameters = _getPackedFeeParameters(
            _binStep,
            _baseFactor,
            _filterPeriod,
            _decayPeriod,
            _reductionFactor,
            _variableFeeControl,
            _protocolShare,
            _maxVolatilityAccumulated
        );

        // The last 16 bits are reserved for sampleLifetime
        bytes32 _preset = bytes32(
            (uint256(_packedFeeParameters) & type(uint144).max) | (uint256(_sampleLifetime) << 240)
        );

        _presets[_binStep] = _preset;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L340-L367

# There is no limit for FlashLoanFee

```
    function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
        uint256 _oldFlashLoanFee = flashLoanFee;

        if (_oldFlashLoanFee == _flashLoanFee) revert LBFactory__SameFlashLoanFee(_flashLoanFee);

        flashLoanFee = _flashLoanFee;
        emit FlashLoanFeeSet(_oldFlashLoanFee, _flashLoanFee);
    }
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L479

There is no limit for FlashLoanFee. So Owner of `LBFactory` could set FlashLoanFee extreamly high.

Check flashLoanFee is lower than MAX_FEE when `setFlashLoanFee` and constructor of `LBFactory`.