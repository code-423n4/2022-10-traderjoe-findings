# Q-01: Event PresetSet is emitted regardless of whether binStep is already set.

[LBFactory.sol#L381](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L381)
```solidity
        if (_avPresets.decode(1, _binStep) == 0) {
            // We add a 1 at bit `_binStep` as this binStep is now set
            _avPresets = bytes32(uint256(_avPresets) | (1 << _binStep));

            // Increase the number of preset by 1
            _avPresets = bytes32(uint256(_avPresets) + (1 << 248));

            // Save the changes
            _availablePresets = _avPresets;
        }
        emit PresetSet(
            _binStep,
            _baseFactor,
            _filterPeriod,
            _decayPeriod,
            _reductionFactor,
            _variableFeeControl,
            _protocolShare,
            _maxVolatilityAccumulated,
            _sampleLifetime
        );
```

Consider reverting this function when `_avPresets.decode(1, _binStep) != 0`, since no updates are made to state when the particular `binStep` already has its preset set.
