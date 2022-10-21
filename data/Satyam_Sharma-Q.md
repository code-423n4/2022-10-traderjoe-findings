1.You can make a require condition in both function's getSwapin and getSwepout for checking uint256 _amountOut and uint256 _amountIn respectively as both are interdependent on eachother on their respective functions call as stated below:
In function getSwapIn it is stated that ///@return amountIn The amount of token to send in order to receive _amountOut token hence amountIn token to send is directly dependent on how much token is to receive in _amountOut.
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L90

similarly in below github repo:
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L148

recommendation: Require condition should be at the top of both the functions before any condition check,implemetation is performed or a if condition with an custom error stating that in both function's _amountOut and _amountIn should not be equal to zero. OR furthermore at last you can match the receive and send token to eachother for conforming in case of extra check.

2.function getSwapIn can be mark as external rather than public to save gas
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L94

3.check '_amountIn' The amount of token to send should not equal to zero can cause to save gas , when a user unwillingly/willingly send zero amount of token.
In the below functions these checks are missing.

function swapExactTokensForTokensSupportingFeeOnTransferTokens
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L532

function swapExactTokensForAVAXSupportingFeeOnTransferTokens
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L562

Insure to make checks!!

4.No zero address check is performed on a address recepient(address _to) on several function's some are marked below this can be lead to an critical issue if an malicious user willingly/unwillingly put an address equal to zero!!


function removeLiquidity 
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L282

function removeLiquidityAVAX
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L315

function swapExactTokensForTokens
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L357

function swapExactTokensForAVAX
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L382

function swapExactAVAXForTokens
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L411

 function swapTokensForExactTokens
 https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L436

 function swapTokensForExactAVAX
 https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L464

 function swapAVAXForExactTokens
 https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L497

 function swapExactTokensForTokensSupportingFeeOnTransferTokens
 https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L536

 function swapExactTokensForAVAXSupportingFeeOnTransferTokens
 https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L566

 function swapExactAVAXForTokensSupportingFeeOnTransferTokens
 https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L598

Make a safety require/if condition that insures that zero address check is performed 