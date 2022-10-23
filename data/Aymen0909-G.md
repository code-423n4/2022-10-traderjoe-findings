# Gas Optimizations

## Findings

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  4 |
| 2      | Use `calldata` instead of `memory` for function parameters type  |  19 |
| 3      | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  6 |
| 4     | Functions guaranteed to revert when called by normal users can be marked payable |  9 |

## Findings

### 1- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 4 instances of this issue:

```
File: src/LBToken.sol

211     _totalSupplies[_id] += _amount;
241     _totalSupplies[_id] -= _amount;

File: src/LBPair.sol

452     _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
453     _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
```

### 2- Use `calldata` instead of `memory` for function parameters type :

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

There are 19 instances of this issue:

```
File: src/LBToken.sol

79      function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
149     function safeBatchTransferFrom(
            address _from,
            address _to,
            uint256[] memory _ids,
            uint256[] memory _amounts
        )

File: src/LBPair.sol

466     function mint(
            uint256[] memory _ids,
            uint256[] memory _distributionX,
            uint256[] memory _distributionY,
            address _to
        )
616     function burn(
            uint256[] memory _ids,
            uint256[] memory _amounts,
            address _to
        )
688     function collectFees(address _account, uint256[] memory _ids)

File: src/LBQuoter.sol

54      function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
134     function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)

File: src/LBRouter.sol

274     function removeLiquidity(
            IERC20 _tokenX,
            IERC20 _tokenY,
            uint16 _binStep,
            uint256 _amountXMin,
            uint256 _amountYMin,
            uint256[] memory _ids,
            uint256[] memory _amounts,
            address _to,
            uint256 _deadline
        )
308     function removeLiquidityAVAX(
            IERC20 _token,
            uint16 _binStep,
            uint256 _amountTokenMin,
            uint256 _amountAVAXMin,
            uint256[] memory _ids,
            uint256[] memory _amounts,
            address payable _to,
            uint256 _deadline
        )
352     function swapExactTokensForTokens(
            uint256 _amountIn,
            uint256 _amountOutMin,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address _to,
            uint256 _deadline
        )
377     function swapExactTokensForAVAX(
            uint256 _amountIn,
            uint256 _amountOutMinAVAX,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address payable _to,
            uint256 _deadline
        )
407     function swapExactAVAXForTokens(
            uint256 _amountOutMin,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address _to,
            uint256 _deadline
        )
431     function swapTokensForExactTokens(
            uint256 _amountOut,
            uint256 _amountInMax,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address _to,
            uint256 _deadline
        )
459     function swapTokensForExactAVAX(
            uint256 _amountAVAXOut,
            uint256 _amountInMax,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address payable _to,
            uint256 _deadline
        )
493     function swapAVAXForExactTokens(
            uint256 _amountOut,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address _to,
            uint256 _deadline
        )
531     function swapExactTokensForTokensSupportingFeeOnTransferTokens(
                uint256 _amountIn,
                uint256 _amountOutMin,
                uint256[] memory _pairBinSteps,
                IERC20[] memory _tokenPath,
                address _to,
                uint256 _deadline
            )
561     function swapExactTokensForAVAXSupportingFeeOnTransferTokens(
            uint256 _amountIn,
            uint256 _amountOutMinAVAX,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address payable _to,
            uint256 _deadline
        )
594     function swapExactAVAXForTokensSupportingFeeOnTransferTokens(
            uint256 _amountOutMin,
            uint256[] memory _pairBinSteps,
            IERC20[] memory _tokenPath,
            address _to,
            uint256 _deadline
        )
642     function sweepLBToken(
            ILBToken _lbToken,
            address _to,
            uint256[] memory _ids,
            uint256[] memory _amounts
        )
```

### 3- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 6 instances of this issue:
 
```
File: src/LBRouter.sol

674     for (uint256 i; i < depositIds.length; ++i) 
711     for (uint256 i = _pairs.length; i != 0; i--)

File: src/LBQuoter.sol

75      for (uint256 i; i < swapLength; i++) 
100     for (uint256 j; j < LBPairsAvailable.length; j++)
154     for (uint256 i = swapLength; i > 0; i--)
177     for (uint256 j; j < LBPairsAvailable.length; j++)
```

### 4- Functions guaranteed to revert when called by normal users can be marked `payable` :

If a function modifier such as `onlyAdmin` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 9 instances of this issue:

```
File: src/LBFactory.sol

215     function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner
312     function setLBPairIgnored(
            IERC20 _tokenX,
            IERC20 _tokenY,
            uint256 _binStep,
            bool _ignored
        ) external override onlyOwner
340     function setPreset(
            uint16 _binStep,
            uint16 _baseFactor,
            uint16 _filterPeriod,
            uint16 _decayPeriod,
            uint16 _reductionFactor,
            uint24 _variableFeeControl,
            uint16 _protocolShare,
            uint24 _maxVolatilityAccumulated,
            uint16 _sampleLifetime
        ) external override onlyOwner
396     function removePreset(uint16 _binStep) external override onlyOwner
423     function setFeesParametersOnPair(
            IERC20 _tokenX,
            IERC20 _tokenY,
            uint16 _binStep,
            uint16 _baseFactor,
            uint16 _filterPeriod,
            uint16 _decayPeriod,
            uint16 _reductionFactor,
            uint24 _variableFeeControl,
            uint16 _protocolShare,
            uint24 _maxVolatilityAccumulated
        ) external override onlyOwner
468     function setFeeRecipient(address _feeRecipient) external override onlyOwner
485     function setFactoryLockedState(bool _locked) external override onlyOwner
502     function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner
520     function forceDecay(ILBPair _LBPair) external override onlyOwner
```
