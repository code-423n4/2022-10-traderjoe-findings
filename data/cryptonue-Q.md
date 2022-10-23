# CONSTANTS SHOULD BE DEFINED RATHER THAN USING MAGIC NUMBERS

```
File: LBFactory.sol
151:         sampleLifetime = _preset.decode(type(uint16).max, 240);

159:             uint256 _nbPresets = _avPresets.decode(type(uint8).max, 248);

189:             uint256 _nbAvailable = _avLBPairBinSteps.decode(type(uint8).max, 248);

261:         uint256 _sampleLifetime = _preset.decode(type(uint16).max, 240);

285:             _avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) + (1 << 248));

364:             (uint256(_packedFeeParameters) & type(uint144).max) | (uint256(_sampleLifetime) << 240)

375:             _avPresets = bytes32(uint256(_avPresets) + (1 << 248));

403:         _avPresets = bytes32(uint256(_avPresets) - (1 << 248));

```