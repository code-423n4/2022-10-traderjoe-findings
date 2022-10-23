## LACK OF REENTRANCY GUARDS

Whenever IERC1155(token).safeTransferFrom is called, the to address can re-enter back to the contracts due to the ERC1155TokenReceiver(to).onERC1155Received(msg.sender, from, id, amount, data) code (hook)

Instances: 
-LBRouter.sol `addLiquidity()` line 207
-LBRouter.sol `addLiquidityAVAX()` line 230
-LBRouter.sol `removeLiquidity()` line 308


## ARRAY LENGTH NOT VALIDATED

Function did not validate that the length of two parameter arrays is the same. If the array length is different, it might cause unexpected behavior.

Instance:
-LBPair.sol line 616 `burn()`











