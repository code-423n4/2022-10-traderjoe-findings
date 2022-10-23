   1.- In [function burn in LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L616-L667) there is no check that _ids and _amounts arrays are equal in length. It is recommended to check lengths to avoid unintended user errors.
    2.- [swapExactAVAXForTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L407-L421) does not check that _tokenPath[0] == IERC20(wavax), like [swapAvaxForExactTokens](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L507) does.
    3.- Missing zero address checks for _to address in LBRouter's removeLiquidityAVAX function. Could lead to lost tokens.
    4.- Missing zero address checks for _to address in LBRouter's swapExactTokensForAVAX function. Could lead to lost tokens.
    5.- Missing zero address checks for _to address in LBRouter's swapTokensForExactAVAX function. Could lead to lost tokens.
    6.- Missing zero address checks for _to address in LBRouter's swapAVAXForExactTokens function. Could lead to lost tokens.
    7.- Missing zero address checks for _to address in LBRouter's swapExactTokensForAVAXSupportingFeeOnTransferTokens function. Could lead to lost tokens.
    8.- Missing zero address checks for _to address in LBRouter's sweep function. Could lead to lost tokens.
    9.- Missing zero address checks for _to address in LBRouter's sweepLBToken function. Could lead to lost tokens.
    10.- Missing zero address checks for _to address in LBRouter's _safeTransferAVAX function. Could lead to lost tokens.
    11.- Missing zero address checks for _to address in LBRouter's _wavaxDepositAndTransfer function. Could lead to lost tokens.
    12.- Missing zero address checks for _to address in LBPair's swap function. Could lead to lost tokens.
    13.- Missing zero address checks for _to address in LBPair's flashLoan function. Could lead to lost tokens.
    14.- Missing zero address checks for _to address in LBPair's mint function. Could lead to lost tokens.
    15.- Missing zero address checks for _to address in LBPair's burn function. Could lead to lost tokens.
    16.- Missing zero address checks for _account address in LBPair's collectFees function. Could lead to lost tokens.