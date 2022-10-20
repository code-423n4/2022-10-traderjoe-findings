1. Missing check if the last bin in the bin tree level 0
The function 'findFirstBin' in TreeMath.sol checks if the last bin for level 2  and level 1, but missing check for level 0.
Functionally there will be no problem，since coincidentally in this case an arithmetic overflow/underflow error will occur while calling function 'closestBit'. But it will revert with an unexpected reason rather than 'TreeMath__ErrorDepthSearch'.
```
   function findFirstBin(
        mapping(uint256 => uint256)[3] storage _tree,
        uint24 _binId,
        bool _rightSide
    ) internal view returns (uint24) {
        unchecked {
            uint256 current;
            uint256 bit;

            (_binId, bit) = _getIdsFromAbove(_binId);

            // Search in depth 2
            if ((_rightSide && bit != 0) || (!_rightSide && bit != 255)) {
                current = _tree[2][_binId];
                bit = current.closestBit(uint8(bit), _rightSide);

                if (bit != type(uint256).max) {
                    return _getBottomId(_binId, uint24(bit));
                }
            }

            (_binId, bit) = _getIdsFromAbove(_binId);

            // Search in depth 1
            if ((_rightSide && bit != 0) || (!_rightSide && bit != 255)) {
                current = _tree[1][_binId];
                bit = current.closestBit(uint8(bit), _rightSide);

                if (bit != type(uint256).max) {
                    _binId = _getBottomId(_binId, uint24(bit));
                    current = _tree[2][_binId];

                    return _getBottomId(_binId, current.significantBit(_rightSide));
                }
            }

            // @audit missing check here
            // Search in depth 0
            current = _tree[0][0];
            bit = current.closestBit(uint8(_binId), _rightSide);
            if (bit == type(uint256).max) revert TreeMath__ErrorDepthSearch();

            current = _tree[1][bit];
            _binId = _getBottomId(uint24(bit), current.significantBit(_rightSide));

            current = _tree[2][_binId];
            return _getBottomId(_binId, current.significantBit(_rightSide));
        }
    }
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/libraries/TreeMath.sol#L56