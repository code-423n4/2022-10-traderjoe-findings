-> MISSING INDEXED FIELDS IN EVENT

If there are three or more fields in an event you should use at least 3 or more indexed fields.

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#:~:text=event%20PairCreated(address%20indexed%20token0%2C%20address%20indexed%20token1%2C%20address%20pair%2C%20uint256)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#:~:text=event%20Approval(address%20indexed%20owner%2C%20address%20indexed%20spender%2C%20uint256%20value)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#:~:text=event%20Transfer(address%20indexed%20from%2C%20address%20indexed%20to%2C%20uint256%20value)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#:~:text=event%20Mint(address%20indexed%20sender%2C%20uint256%20amount0%2C%20uint256%20amount1)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#:~:text=event%20Burn(address%20indexed%20sender%2C%20uint256%20amount0%2C%20uint256%20amount1%2C%20address%20indexed%20to)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoePair.sol#:~:text=event%20Swap(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#:~:text=event%20LBPairCreated(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#:~:text=event%20FeeParametersSet(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBFactory.sol#:~:text=event%20PresetSet(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20Swap(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20FlashLoan(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20LiquidityAdded(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20CompositionFee(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20LiquidityRemoved(,)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20FeesCollected(address%20indexed%20sender%2C%20address%20indexed%20recipient%2C%20uint256%20amountX%2C%20uint256%20amountY)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBPair.sol#:~:text=event%20ProtocolFeesCollected(address%20indexed%20sender%2C%20address%20indexed%20recipient%2C%20uint256%20amountX%2C%20uint256%20amountY)%3B
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/ILBToken.sol#:~:text=event%20ApprovalForAll(address%20indexed%20account%2C%20address%20indexed%20sender%2C%20bool%20approved)%3B


-> _MINT SHOULD BE AVOID WHERE EVER POSSIBLE

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=function-,mint,-(
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#:~:text=id%5D%20%3D%20_bin%3B-,_mint,-(_to%2C%20_mintInfo.id


->USE NEWER VERSION OF SOLIDITY

Use a newer solidity version of at least 0.8.10, so you have at least external calls skip contract existence, that checks if the external call has a return value

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/interfaces/IJoeFactory.sol#:~:text=pragma%20solidity%20%5E0.8.0%3B
