# **Low and Non-Critical**

Serial No. | Issue Name | Instances
--- | --- | ---
Q-1 | Use of Block.Timestamp | 8

-----
***Total instances found in this contest: 8 | Among all files in scope***

##  Q-01 Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Instances:
[src/LBRouter.sol:38:        if (block.timestamp > _deadline) revert LBRouter__DeadlineExceeded(_deadline, block.timestamp);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L38)
[src/libraries/Oracle.sol:118:        if (block.timestamp - _lastTimestamp >= _sampleLifetime && _lastTimestamp != 0) {](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Oracle.sol#L118)
[src/libraries/Samples.sol:46:        uint256 _deltaTime = block.timestamp - timestamp(_lastSample);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L46)
[src/libraries/Samples.sol:58:            return pack(_cumulativeBinCrossed, _cumulativeVolatilityAccumulated, _cumulativeId, block.timestamp, 1);](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/Samples.sol#L58)
[src/libraries/FeeHelper.sol:56:        uint256 _deltaT = block.timestamp - _fp.time;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L56)
[src/libraries/FeeHelper.sol:72:        _fp.time = (block.timestamp).safe40();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/FeeHelper.sol#L72)
[src/LBPair.sol:208:        uint256 _lookUpTimestamp = block.timestamp - _timeDelta;](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L208)
[src/LBPair.sol:394:            _pair.oracleLastTimestamp = block.timestamp.safe40();](https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L394)
### References:

https://github.com/code-423n4/2022-04-dualityfocus-findings/issues/33


-----

