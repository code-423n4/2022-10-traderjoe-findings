1. directly calling constant variable from storage.'MIN_BIN_STEP'
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L165
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L195


2.Check if a contract parameter address by default not calling zero address make require statement that point that address =! 0 this will reduce wastage of gas if a function set to zero address..

address public override LBPairImplementation;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L32

    address public override feeRecipient;
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L34

3.Use uint256 _id instead of using int256 _id as in below if condition check is performed where it is checking if _id less zero than revert will execute simply make it uint than it will always be a positve number and therefore mark it != instead of < will save you a gas.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L675-676


4.Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas).

if (amountX < _amountXMin || amountY < _amountYMin)
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L751

if (_amountXAdded < _liq.amountXMin || _amountYAdded < _liq.amountYMin)
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L690

if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L614

        if (_amountOutMinAVAX > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMinAVAX, amountOut);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L581

        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L581

        if (_amountOutReal < _amountOut) revert LBRouter__InsufficientAmountOut(_amountOut, _amountOutReal);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L518

        if (_amountOutReal < _amountAVAXOut) revert LBRouter__InsufficientAmountOut(_amountAVAXOut, _amountOutReal);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L479

        if (_amountOutReal < _amountOut) revert LBRouter__InsufficientAmountOut(_amountOut, _amountOutReal);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L448

        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L420


        if (_amountOutMinAVAX > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMinAVAX, amountOut);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L394

        if (_amountOutMin > amountOut) revert LBRouter__InsufficientAmountOut(_amountOutMin, amountOut);
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L366