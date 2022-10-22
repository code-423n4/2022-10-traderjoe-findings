# Low Impact
## [L-01] Use of `Block.timestamp`
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
```solidity
libraries/Oracle.sol:118:        if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {
libraries/FeeHelper.sol:56:        uint256 _deltaT = block.timestamp - _fp.time;
libraries/FeeHelper.sol:72:        _fp.time = (block.timestamp).safe40();
libraries/Samples.sol:46:        uint256 _deltaTime = block.timestamp - timestamp(_lastSample);
libraries/Samples.sol:58:            return pack(_cumulativeBinCrossed, _cumulativeVolatilityAccumulated, _cumulativeId, block.timestamp, 1);
LBRouter.sol:38:        if (block.timestamp > _deadline) revert LBRouter__DeadlineExceeded(_deadline, block.timestamp);
LBPair.sol:208:        uint256 _lookUpTimestamp = block.timestamp - _timeDelta;
LBPair.sol:394:            _pair.oracleLastTimestamp = block.timestamp.safe40();
```
# Non critical
## [N-1] Use `allowlist/denylist` rather than `blacklist/whitelist`
```solidity
LBFactory.sol:54:    EnumerableSet.AddressSet private _quoteAssetWhitelist;
LBFactory.sol:76:    /// @notice View function to return the number of quote assets whitelisted
LBFactory.sol:79:        return _quoteAssetWhitelist.length();
LBFactory.sol:82:    /// @notice View function to return the quote asset whitelisted at index `index`
LBFactory.sol:86:        return IERC20(_quoteAssetWhitelist.at(_index));
LBFactory.sol:93:        return _quoteAssetWhitelist.contains(address(_token));
LBFactory.sol:247:        if (!_quoteAssetWhitelist.contains(address(_tokenY))) revert LBFactory__QuoteAssetNotWhitelisted(_tokenY);
LBFactory.sol:491:    /// @notice Function to add an asset to the whitelist of quote assets
LBFactory.sol:494:        if (!_quoteAssetWhitelist.add(address(_quoteAsset)))
LBFactory.sol:495:            revert LBFactory__QuoteAssetAlreadyWhitelisted(_quoteAsset);
LBFactory.sol:500:    /// @notice Function to remove an asset to the whitelist of quote assets
LBFactory.sol:503:        if (!_quoteAssetWhitelist.remove(address(_quoteAsset))) revert LBFactory__QuoteAssetNotWhitelisted(_quoteAsset);
LBErrors.sol:52:error LBFactory__QuoteAssetNotWhitelisted(IERC20 quoteAsset);
LBErrors.sol:53:error LBFactory__QuoteAssetAlreadyWhitelisted(IERC20 quoteAsset);
```


