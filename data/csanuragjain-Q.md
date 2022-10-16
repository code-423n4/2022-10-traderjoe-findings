## Flash loan fee can be set to 100%

Contract:
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474

Issue:
In setFlashLoanFee function, If Admin has set flashloan fee to 100% then user taking X amount as flashloan has to pay a fee equal to X which does not make sense

```
function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
        uint256 _oldFlashLoanFee = flashLoanFee;

        if (_oldFlashLoanFee == _flashLoanFee) revert LBFactory__SameFlashLoanFee(_flashLoanFee);

        flashLoanFee = _flashLoanFee;
        emit FlashLoanFeeSet(_oldFlashLoanFee, _flashLoanFee);
    }
```

Recommendation:
Implement a upper bound on flashloan fee

## setPreset might update Preset as well

Contract:
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L367

Issue:
setPreset should not set an already existing preset but that is not true. Currently without removing an existing preset, setPreset can be used to overwrite an existing preset

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
...
}
```

Recommendation:
If preset is already existing then revert. Owner should ideally first call removePreset