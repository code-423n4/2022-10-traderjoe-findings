## should check for zero address
To make the protocol more safe, i suggest to add check for zero address for calling transfer function. 
There are some instances for this issue:
https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L629

https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L493

https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L407

https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBRouter.sol#L377