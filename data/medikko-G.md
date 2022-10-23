# Report

## Gas Optimizations

### [G-01] Functions that aren't called multiple times in contract can be inlined

Two `JUMP` instructions in EVM can be avoid with inlining.
This will cost

_There have **7** instance of this issues:_

File: /src/LBToken.sol

```solidity
253:    function _setApprovalForAll(
254:          address _owner,
255:          address _spender,
256:           bool _approved
257:     ) internal virtual {
```

File: /src/libraries/BitMath.sol

```solidity
39:     function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {
```

```solidity
56: function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {
```

File: /src/libraries/FeeHelper.sol

```solidity
 91: function getBaseFee(FeeParameters memory \_fp) internal pure returns (uint256) {
```

```solidity
100: function getVariableFee(FeeParameters memory \_fp) internal pure returns (uint256 variableFee) {
```

File; /src/libraries/ReentrancyGuardUpgradeable.sol

```solidity
 27: function **ReentrancyGuard_init() internal {
```

```solidity
 31: function **ReentrancyGuard_init_unchained() internal {
```

### [G-02] Remove empty blocks

Empty blocks should do something or be removed.

_There have **3** instance of this issues:_

File; /src/LBPair.sol

```solidity
321:    function _beforeTokenTransfer(
322:        address from,
323:        address to,
324:        uint256 id,
325:       uint256 amount
326:    ) internal virtual {} - empty block gas optimization
```

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L321

File; /src/LBQuoter.sol

```solidity
123:    } catch {}
```

https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L123

```solidity
203:    } catch {}
```

https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/LBQuoter.sol#L203


### [G-03] Using smaller uints and ints than 256 bits cost more expensive

if you use smaller integer than 256 bits this will cost more because EVM operate at that time at 256 bits and to convert to small uint/int will cost gas.

_There have **2** instance of this issues:_

File:  /src/libraries/FeeDistributionHelper.sol

```solidity
31:     uint128 _totalFees = _pairFees.total;
```

https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/libraries/FeeDistributionHelper.sol#L31

File: /src/libraries/FeeHelper.sol

```solidity
47:    struct FeesDistribution {
48:        uint128 total;
49:        uint128 protocol;
50:    }
```

https://github.com/code-423n4/2022-10-traderjoe/blob/07f84867a018cbfc591f9b5063bf6e59e1f2cb85/src/libraries/FeeHelper.sol#L47