https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol
Line 978, define _mask24 as a private constant (bytecode) to save gas

Line 979, define _mask136 as a private constant (bytecode) to save gas

Line 996, define _mask _mask112 as a private constant (bytecode) to save gas


Line 261, change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas.

also cache *_bins* to a local variable *_localbins* to avoid reading
from the state variable at line 283.

Line 467, change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Line 468, change argument *uint256[] memory _distributionX,* to
*uint256[] calldata _distributionX,* to save gas

Line 469, change argument *uint256[] memory _distributionY,* to
*uint256[] calldata _distributionY,* to save gas

Line 617, change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Line 618, change argument *uint256[] memory _amounts* to
*uint256[] calldata _amounts* to save gas

Line 688, change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Another file
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol

Line 54: change argument *address[] memory _route* to
*address[] calldata _route* to save gas

Line 134: change argument *address[] memory _route* to
*address[] calldata _route* to save gas

new file
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol
Line 207, change argument 
    uint256[] memory liquidityParameters 
to 
    uint256[] calldata liquidityParameters 


Line 280 change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Line 281 change argument *uint256[] memory _amounts* to
*uint256[] calldata _amounts* to save gas

Line 313 change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Line 314 change argument *uint256[] memory _amounts* to
*uint256[] calldata _amounts* to save gas

Line 355 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 356 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 380 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 381 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 409 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 410 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 434 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 435 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 462 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 463 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 495 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 496 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 534 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 535 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 564 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 565 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 596 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 597 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 645 change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Line 646 change argument *uint256[] memory _amounts* to
*uint256[] calldata _amounts* to save gas

Line 659 change argument *uint256[] memory depositIds* to
*uint256[] calldata depositIds* to save gas
change argument *uint256[] memory liquidityMinted* to
*uint256[] calldata liquidityMinted* to save gas

Line 702 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 703 change argument *address[] memory _pairs* to
*address[] calldata _pairs* to save gas

Line 704 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 745 change argument *uint256[] memory _ids* to
*uint256[] calldata _ids* to save gas

Line 746 change argument *uint256[] memory _amounts* to
*uint256[] calldata _amounts* to save gas


Line 764 change argument *address[] memory _pairs* to
*address[] calldata _pairs* to save gas

Line 765 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 766 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 817 change argument *address[] memory _pairs* to
*address[] calldata _pairs* to save gas

Line 818 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 819 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

Line 820 change argument *IERC20[] memory _amountsIn* to
*IERC20[] calldata _amountsIn* to save gas

Line 865 change argument *address[] memory _pairs* to
*address[] calldata _pairs* to save gas

Line 866 change argument *uint256[] memory _pairBinSteps* to
*uint256[] calldata _pairBinSteps* to save gas

Line 867 change argument *IERC20[] memory _tokenPath* to
*IERC20[] calldata _tokenPath* to save gas

