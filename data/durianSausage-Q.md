## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
interfaces/ILBFactory.sol, 34, b'    event FeeRecipientSet(address oldRecipient, address newRecipient);'
interfaces/ILBFactory.sol, 36, b'    event FlashLoanFeeSet(uint256 oldFlashLoanFee, uint256 newFlashLoanFee);'
interfaces/ILBFactory.sol, 51, b'    event FactoryLockedStatusUpdated(bool unlocked);'
interfaces/ILBFactory.sol, 53, b'    event LBPairImplementationSet(address oldLBPairImplementation, address LBPairImplementation);'
interfaces/ILBPair.sol, 152, b'    event OracleSizeIncreased(uint256 previousSize, uint256 newSize);'
interfaces/IJoePair.sol, 59, b'    event Sync(uint112 reserve0, uint112 reserve1);'

### N02: NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
LBErrors.sol, 3, b'pragma solidity ^0.8.0;'
LBFactory.sol, 3, b'pragma solidity ^0.8.0;'
LBRouter.sol, 3, b'pragma solidity ^0.8.0;'
LBToken.sol, 3, b'pragma solidity ^0.8.0;'
LBQuoter.sol, 3, b'pragma solidity ^0.8.0;'
...


## low risks
### L01: the assert() function when false, uses up all the remaining gas and reverts all the changes made.
#### problem
assert(false) compiles to 0xfe, which is an invalid opcode, using up all remaining gas, and reverting all changes.
require(false) compiles to 0xfd which is the REVERT opcode, meaning it will refund the remaining gas. The opcode can also return a value (useful for debugging), but I don't believe that is supported in Solidity as of this moment. (2017-11-21)
#### prof
LBFactory.sol, 141, b'        assert(_binStep == _preset.decode(type(uint16).max, _shift));'

### L02: the assert() function when false, uses up all the remaining gas and reverts all the changes made.
#### problem
assert(false) compiles to 0xfe, which is an invalid opcode, using up all remaining gas, and reverting all changes.
require(false) compiles to 0xfd which is the REVERT opcode, meaning it will refund the remaining gas. The opcode can also return a value (useful for debugging), but I don't believe that is supported in Solidity as of this moment. (2017-11-21)
#### prof
LBFactory.sol, 141, b'        assert(_binStep == _preset.decode(type(uint16).max, _shift));'


### L03: INPUT ARRAY LENGTHS MAY DIFFER
#### problem
If the caller makes a copy-paste error, the lengths may be mismatchd and an operation believed to have been completed may not in fact have been completed
function withdrawMultipleERC721(address[] memory _tokens, uint256[] memory _tokenId, address _to) external override {
#### prof
LBRouter.sol, 292, b'    function removeLiquidity(\n        IERC20 _tokenX,\n        IERC20 _tokenY,\n        uint16 _binStep,\n        uint256 _amountXMin,\n        uint256 _amountYMin,\n        uint256[] memory _ids,\n        uint256[] memory _amounts,\n        address _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) '
LBRouter.sol, 342, b'    function removeLiquidityAVAX(\n        IERC20 _token,\n        uint16 _binStep,\n        uint256 _amountTokenMin,\n        uint256 _amountAVAXMin,\n        uint256[] memory _ids,\n        uint256[] memory _amounts,\n        address payable _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) '
LBRouter.sol, 367, b'    function swapExactTokensForTokens(\n        uint256 _amountIn,\n        uint256 _amountOutMin,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) '
LBRouter.sol, 398, b'    function swapExactTokensForAVAX(\n        uint256 _amountIn,\n        uint256 _amountOutMinAVAX,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address payable _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) '
LBRouter.sol, 421, b'    function swapExactAVAXForTokens(\n        uint256 _amountOutMin,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address _to,\n        uint256 _deadline\n    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) '
LBRouter.sol, 449, b'    function swapTokensForExactTokens(\n        uint256 _amountOut,\n        uint256 _amountInMax,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) '
LBRouter.sol, 483, b'    function swapTokensForExactAVAX(\n        uint256 _amountAVAXOut,\n        uint256 _amountInMax,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address payable _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) '
LBRouter.sol, 521, b'    function swapAVAXForExactTokens(\n        uint256 _amountOut,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address _to,\n        uint256 _deadline\n    )\n        external\n        payable\n        override\n        ensure(_deadline)\n        verifyInputs(_pairBinSteps, _tokenPath)\n        returns (uint256[] memory amountsIn)\n    '
LBRouter.sol, 551, b'    function swapExactTokensForTokensSupportingFeeOnTransferTokens(\n        uint256 _amountIn,\n        uint256 _amountOutMin,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) '
LBRouter.sol, 585, b'    function swapExactTokensForAVAXSupportingFeeOnTransferTokens(\n        uint256 _amountIn,\n        uint256 _amountOutMinAVAX,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address payable _to,\n        uint256 _deadline\n    ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) '
LBRouter.sol, 615, b'    function swapExactAVAXForTokensSupportingFeeOnTransferTokens(\n        uint256 _amountOutMin,\n        uint256[] memory _pairBinSteps,\n        IERC20[] memory _tokenPath,\n        address _to,\n        uint256 _deadline\n    ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) '
LBRouter.sol, 649, b'    function sweepLBToken(\n        ILBToken _lbToken,\n        address _to,\n        uint256[] memory _ids,\n        uint256[] memory _amounts\n    ) external override onlyFactoryOwner '
interfaces/ILBRouter.sol, 94, b'    function removeLiquidity(\n        IERC20 tokenX,\n        IERC20 tokenY,\n        uint16 binStep,\n        uint256 amountXMin,\n        uint256 amountYMin,\n        uint256[] memory ids,\n        uint256[] memory amounts,\n        address to,\n        uint256 deadline\n    ) external returns (uint256 amountX, uint256 amountY);'
interfaces/ILBRouter.sol, 105, b'    function removeLiquidityAVAX(\n        IERC20 token,\n        uint16 binStep,\n        uint256 amountTokenMin,\n        uint256 amountAVAXMin,\n        uint256[] memory ids,\n        uint256[] memory amounts,\n        address payable to,\n        uint256 deadline\n    ) external returns (uint256 amountToken, uint256 amountAVAX);'
interfaces/ILBRouter.sol, 114, b'    function swapExactTokensForTokens(\n        uint256 amountIn,\n        uint256 amountOutMin,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address to,\n        uint256 deadline\n    ) external returns (uint256 amountOut);'
interfaces/ILBRouter.sol, 123, b'    function swapExactTokensForAVAX(\n        uint256 amountIn,\n        uint256 amountOutMinAVAX,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address payable to,\n        uint256 deadline\n    ) external returns (uint256 amountOut);'
interfaces/ILBRouter.sol, 131, b'    function swapExactAVAXForTokens(\n        uint256 amountOutMin,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address to,\n        uint256 deadline\n    ) external payable returns (uint256 amountOut);'
interfaces/ILBRouter.sol, 140, b'    function swapTokensForExactTokens(\n        uint256 amountOut,\n        uint256 amountInMax,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address to,\n        uint256 deadline\n    ) external returns (uint256[] memory amountsIn);'
interfaces/ILBRouter.sol, 149, b'    function swapTokensForExactAVAX(\n        uint256 amountOut,\n        uint256 amountInMax,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address payable to,\n        uint256 deadline\n    ) external returns (uint256[] memory amountsIn);'
interfaces/ILBRouter.sol, 157, b'    function swapAVAXForExactTokens(\n        uint256 amountOut,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address to,\n        uint256 deadline\n    ) external payable returns (uint256[] memory amountsIn);'
interfaces/ILBRouter.sol, 166, b'    function swapExactTokensForTokensSupportingFeeOnTransferTokens(\n        uint256 amountIn,\n        uint256 amountOutMin,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address to,\n        uint256 deadline\n    ) external returns (uint256 amountOut);'
interfaces/ILBRouter.sol, 175, b'    function swapExactTokensForAVAXSupportingFeeOnTransferTokens(\n        uint256 amountIn,\n        uint256 amountOutMinAVAX,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address payable to,\n        uint256 deadline\n    ) external returns (uint256 amountOut);'
interfaces/ILBRouter.sol, 183, b'    function swapExactAVAXForTokensSupportingFeeOnTransferTokens(\n        uint256 amountOutMin,\n        uint256[] memory pairBinSteps,\n        IERC20[] memory tokenPath,\n        address to,\n        uint256 deadline\n    ) external payable returns (uint256 amountOut);'
interfaces/ILBRouter.sol, 196, b'    function sweepLBToken(\n        ILBToken _lbToken,\n        address _to,\n        uint256[] memory _ids,\n        uint256[] memory _amounts\n    ) external;'
interfaces/ILBPair.sol, 232, b'    function mint(\n        uint256[] memory ids,\n        uint256[] memory distributionX,\n        uint256[] memory distributionY,\n        address to\n    )\n        external\n        returns (\n            uint256 amountXAddedToPair,\n            uint256 amountYAddedToPair,\n            uint256[] memory liquidityMinted\n        );'
interfaces/ILBPair.sol, 238, b'    function burn(\n        uint256[] memory ids,\n        uint256[] memory amounts,\n        address to\n    ) external returns (uint256 amountX, uint256 amountY);'
interfaces/ILBToken.sol, 30, b'    function balanceOfBatch(address[] memory accounts, uint256[] memory ids)\n        external\n        view\n        returns (uint256[] memory batchBalances);'
interfaces/ILBToken.sol, 54, b'    function safeBatchTransferFrom(\n        address from,\n        address to,\n        uint256[] memory id,\n        uint256[] memory amount\n    ) external;'

### L04: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
LBFactory.sol, 223, b'        LBPairImplementation = _LBPairImplementation;'
LBFactory.sol, 516, b'        feeRecipient = _feeRecipient;'
LBRouter.sol, 57, b'        factory = _factory;'
LBRouter.sol, 58, b'        oldFactory = _oldFactory;'
LBRouter.sol, 59, b'        wavax = _wavax;'
LBQuoter.sol, 45, b'        routerV2 = _routerV2;'
LBQuoter.sol, 46, b'        factoryV1 = _factoryV1;'
LBQuoter.sol, 47, b'        factoryV2 = _factoryV2;'
libraries/PendingOwnable.sol, 93, b'        _owner = _newOwner;'
libraries/PendingOwnable.sol, 94, b'        _pendingOwner = address(0);'
libraries/PendingOwnable.sol, 102, b'        _pendingOwner = pendingOwner_;'