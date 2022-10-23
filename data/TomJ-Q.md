## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 
- Require should be used instead of Assert

### Non-critical Issues
- Define Magic Numbers to Constant
- Event is Missing Indexed Fields
- Naming Convention for Constants

&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 6 instances found.
1. LBQuoter.sol:constructor():  `routerV2` address 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L45

2. LBQuoter.sol:constructor():  `factoryV1` address 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L46

3. LBQuoter.sol:constructor():  `factoryV2` address 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L47

4. LBRouter.sol:constructor():  `factory` address 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L57

5. LBRouter.sol:constructor():  `oldFactory` address 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L58

6. LBRouter.sol:constructor():  `wavax` address 
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L59

#### Mitigation
Add 0-address check for above addresses.

&ensp;
### Require should be used instead of Assert

#### Issue
Solidity documents mention that properly functioning code should never reach a failing assert statement
and if this happens there is a bug in the contract which should be fixed.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require

#### PoC
Total of 1 instances found.
```solidity
./LBFactory.sol:141:        assert(_binStep == _preset.decode(type(uint16).max, _shift));
```

#### Mitigation
Replace assert by require.

&ensp;
## Non-critical Issues
### Define Magic Numbers to Constant

#### Issue
It is best practice to define magic numbers to constant rather than just using as a magic number.
This improves code readability and maintainability. 

#### PoC
1. Magic Number: 100
```solidity
./libraries/FeeHelper.sol:107:                variableFee = (_prod * _prod * _fp.variableFeeControl) / 100;
./LBFactory.sol:564:            uint256 _maxVariableFee = (_prod * _prod * _variableFeeControl) / 100;
```

2. Magic Number: 1000
```solidity
./libraries/JoeLibrary.sol:39:        uint256 denominator = reserveIn * 1000 + amountInWithFee;
./libraries/JoeLibrary.sol:51:        uint256 numerator = reserveIn * amountOut * 1000;
./LBRouter.sol:725:                amountsIn[i - 1] = (_reserveIn * amountOut_ * 1_000) / (_reserveOut - amountOut_ * 997) + 1;
./LBRouter.sol:791:                        amountOut = (_reserve1 * amountOut * 997) / (_reserve0 * 1_000 + amountOut * 997);
./LBRouter.sol:794:                        amountOut = (_reserve0 * amountOut * 997) / (_reserve1 * 1_000 + amountOut * 997);
./LBRouter.sol:891:                        uint256 _amountOut = (_reserve1 * (_balance - _reserve0) * 997) / (_balance * 1_000);
./LBRouter.sol:896:                        uint256 _amountOut = (_reserve0 * (_balance - _reserve1) * 997) / (_balance * 1_000);
./LBQuoter.sol:85:                        (quote.virtualAmountsWithoutSlippage[i] * 997) / 1000,
./LBQuoter.sol:163:                        (JoeLibrary.quote(quote.virtualAmountsWithoutSlippage[i], reserveOut, reserveIn) * 1000) /
```

3. Magic Number: 0.003e18
```solidity
./LBQuoter.sol:89:                    quote.fees[i] = 0.003e18; // 0.3%
./LBQuoter.sol:166:                    quote.fees[i - 1] = 0.003e18; // 0.3%
```

4. Magic Number: 1e18
```solidity
./LBQuoter.sol:121:                                quote.fees[i] = (fees * 1e18) / quote.amounts[i]; // fee percentage in amountIn
./LBQuoter.sol:201:                                quote.fees[i - 1] = (fees * 1e18) / quote.amounts[i - 1]; // fee percentage in amountIn
```

5. Magic Number: 1e10
```solidity
./libraries/FeeHelper.sol:93:            return uint256(_fp.baseFactor) * _fp.binStep * 1e10;
./LBFactory.sol:558:            uint256 _baseFee = (uint256(_baseFactor) * _binStep) * 1e10;
```

#### Mitigation
Define magic numbers to constant.

&ensp;
### Event is Missing Indexed Fields

#### Issue
Each event should have 3 indexed fields if there are 3 or more fields.

#### PoC
Total of 19 instances found.
```solidity
interfaces/ILBFactory.sol:34:    event FeeRecipientSet(address oldRecipient, address newRecipient);
interfaces/ILBFactory.sol:36:    event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);
interfaces/ILBFactory.sol:38:    event FeeParametersSet(
                                       address indexed sender,
                                       ILBPair indexed LBPair,
                                       uint256 binStep,
                                       uint256 baseFactor,
                                       uint256 filterPeriod,
                                       uint256 decayPeriod,
                                       uint256 reductionFactor,
                                       uint256 variableFeeControl,
                                       uint256 protocolShare,
                                       uint256 maxVolatilityAccumulated
                                   );
interfaces/ILBFactory.sol:51:    event FactoryLockedStatusUpdated(bool unlocked);
interfaces/ILBFactory.sol:53:    event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);
interfaces/ILBFactory.sol:55:    event LBPairIgnoredStateChanged(ILBPair indexed LBPair, bool ignored);
interfaces/ILBFactory.sol:57:    event PresetSet(
                                       uint256 indexed binStep,
                                       uint256 baseFactor,
                                       uint256 filterPeriod,
                                       uint256 decayPeriod,
                                       uint256 reductionFactor,
                                       uint256 variableFeeControl,
                                       uint256 protocolShare,
                                       uint256 maxVolatilityAccumulated,
                                       uint256 sampleLifetime
                                   );
interfaces/IJoeFactory.sol:8:    event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
interfaces/ILBPair.sol:111:    event FlashLoan(
                                     address indexed sender,
                                     address indexed recipient,
                                     uint256 amountX,
                                     uint256 amountY,
                                     uint256 feesX,
                                     uint256 feesY
                                 );
interfaces/ILBPair.sol:148:    event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
interfaces/ILBPair.sol:150:    event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
interfaces/ILBPair.sol:152:    event OracleSizeIncreased(uint256 previousSize, uint256 newSize);
interfaces/ILBToken.sol:19:    event ApprovalForAll(address indexed account, address indexed sender, bool approved);
interfaces/IJoePair.sol:8:    event Approval(address indexed owner, address indexed spender, uint256 value);
interfaces/IJoePair.sol:9:    event Transfer(address indexed from, address indexed to, uint256 value);
interfaces/IJoePair.sol:49:    event Mint(address indexed sender, uint256 amount0, uint256 amount1);
interfaces/IJoePair.sol:50:    event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
interfaces/IJoePair.sol:51:    event Swap(
                                     address indexed sender,
                                     uint256 amount0In,
                                     uint256 amount1In,
                                     uint256 amount0Out,
                                     uint256 amount1Out,
                                     address indexed to
                                 );
interfaces/IJoePair.sol:59:    event Sync(uint112 reserve0, uint112 reserve1);
```

#### Mitigation
Add up to 3 indexed fields when possible.

&ensp;
### Naming Convention for Constants

#### Issue
Constants should be named with all capital letters with underscores separating words.

Solidity documentation:
https://docs.soliditylang.org/en/v0.8.15/style-guide.html#constants

#### PoC
Below 2 constant variable should follow constants naming convention best practice.
```solidity
./LBToken.sol:29:    string private constant _name = "Liquidity Book Token";
./LBToken.sol:30:    string private constant _symbol = "LBT";
```

&ensp;