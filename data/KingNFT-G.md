1. Check if the flash loan amount is zero before gas expensive operations like transferring token
function 'flashLoan' in LBPair.sol allows users to loan both tokens meanwhile, but the scenario is very common that users loan only one of them. It's better to check if  '_amountXOut != 0' / '_amountYOut != 0' before any subsequent operations.
```
    function flashLoan(
        address _to,
        uint256 _amountXOut,
        uint256 _amountYOut,
        bytes calldata _data
    ) external override nonReentrant {
       // ...
        
        // @audit: expensive operation
        tokenX.safeTransfer(_to, _amountXOut);
        tokenY.safeTransfer(_to, _amountYOut);

       // ...

       
       // @audit: expensive operation
        _feesX.flashLoanHelper(_pairInformation.feesX, tokenX, _reserveX);
        _feesY.flashLoanHelper(_pairInformation.feesY, tokenY, _reserveY);

        // @audit: expensive operation
        _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
        _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);

    }
```

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L420-L456

2. Using 'calldata' instead of 'memory' for read-only arguments in external functions saves gas
(1) function 'mint' of LBPair.sol, 3 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L467-L469
(2) function 'burn' of LBPair.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L617-L618
(3) function 'addLiquidity' of LBRouter.sol, 1 occurrence
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L207
(4) function 'addLiquidityAVAX' of LBRouter.sol, 1 occurrence
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L230
(5) function 'removeLiquidity' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L280-L281
(6) function 'removeLiquidityAVAX' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L313-L314
(7) function 'swapExactTokensForTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L355-L356
(8) function 'swapExactTokensForAVAX ' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L380-L381
(9) function 'swapExactAVAXForTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L409-L410
(10) function 'swapTokensForExactTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L434-L435
(11) function 'swapTokensForExactAVAX' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L462-L463
(12) function 'swapAVAXForExactTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L495-L496
(13) function 'swapExactTokensForTokensSupportingFeeOnTransferTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L534-L535
(14) function 'swapExactTokensForAVAXSupportingFeeOnTransferTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L564-L565
(16) function 'swapExactAVAXForTokensSupportingFeeOnTransferTokens' of LBRouter.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L596-L597


