1. Using 'calldata' instead of 'memory' for read-only arguments in external functions saves gas
(1) function 'mint' of LBPair.sol, 3 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L467-L469
(2) function 'burn' of LBPair.sol, 2 occurrences
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L617-L618
(3) function 'addLiquidity' of LBRouter.sol, 1 occurrence
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L207

2. Check if the flash loan amount is zero before gas expensive operations like transferring token
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
