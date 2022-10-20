# Disclaimer

- Dear, judges and sponsors! The gas optimization report consists of the general optimization patterns and unique optimization cases. I'll attach every single occurance of unique cases `[GQ-xx]`, buf for general optimization patterns `[G-xx]` - only the random one. This is done, cuz I wanna focus more on the quality side. Enjoy!

# Table of contents


- **[[GQ-01] Use `calldataload` for read-only arguments in functions](#gq-01-use-calldataload-for-read-only-arguments-in-functions)**
- **[[GQ-02] Unused the named return variables](#gq-02-unused-the-named-return-variables)**
- **[[GQ-03] Consider `a = a + b` instead of `a += b`, in a case of `a` being a state variable](#gq-03-consider-a--a--b-instead-of-a--b-in-case-of-a-being-state-variable)**
- **[[GQ-04] Re-arrange `require/if` statements according to the gas usage](#gq-04-re-arrange-requireif-statements-according-to-the-gas-usage)**
- **[[GQ-05] Consider `a++/++a` instead of a += 1](#gq-05-consider-aa-instead-of-a--1)**
- **[[GQ-06] Redundant checks](#gq-06-redundant-checks)**
- **[[GQ-07] Add `require()/reverts` before some computations](#gq-07-add-requirereverts-before-some-computations)**
- **[[GQ-08] Cache only the needless field of any heavy object instead of caching all attributes](#gq-08-cache-only-the-needless-field-of-any-heavy-object-instead-of-caching-all-attributes)**
- **[[GQ-09] Use `private/internal` for `public` `const/immutable/state` variables](#gq-09-use-privateinternal-for-public-constimmutablestate-variables)**
- **[[GQ-10] Cache state variables into a memory to avoid extra warm-accessed `sload`](#gq-10-cache-state-variables-into-a-memory-to-avoid-extra-warm-accessed-sload)**
- **[[GQ-11] Use `unchecked{}` for arithmetic operations where the operands cannot under/overflow](#gq-11-use-unchecked-for-arithmetic-operations-where-the-operands-cannot-underoverflow)**
- **[[G-12] Try `++i/--i` instead of `i++/i--`](#g-12-try-i--i-instead-of-ii)**
- **[[G-13] Try `unchecked{++i}` instead of `i++` in loops](#g-13-try-uncheckedi-instead-of-ii-in-loops)**
- **[[G-14] Consider marking onlyOwner functions as payable](#g-14-consider-marking-onlyowner-functions-as-payable)**
- **[[G-15] Use the recent version of solidity](#g-15-use-the-recent-version-of-solidity)**
- **[[G-16] Possible names optimization](#g-16-possible-names-optimization)**
- **[[G-17] Breakdown complex conditions into multiple `if/require` statements](#g-17-breakdown-complex-conditions-into-multiple-ifrequire-statements)**
- **[[G-18] Use const values instead of computing `type(uint256).max` every time](#g-18-use-const-values-instead-of-computing-typeuint256max-every-time)**
- **[[G-19] Avoid memory expansions](#g-19-avoid-memory-expansions)**
- **[[G-xx] Overall analysis](#overall-analysis)**



## **[GQ-01] Use `calldataload` for read-only arguments in functions**<a name="GQ-01"></a>

### ***Description:***
 - In EIP-2029, gas consumption for non-zero byte read from calldata was dicreased from 68 to ~16, therefore, try to use it to save gas per each transaction and on the deployment stage as well. Here you need to explicitly specify `calldataload`, or replace `memory` with `calldata`, if args are not supposed to be modified.
 

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/LBPair.sol 
      .....................
      
        // Lines: [466-480]
            // Comment: use calldata instead of memory for `_ids`, `_distributionX`, `_distributionY` 
                function mint(
                    uint256[] memory _ids,
                    uint256[] memory _distributionX,
                    uint256[] memory _distributionY,
                    address _to
                )
                    external
                    override
                    nonReentrant
                    returns (
                        uint256,
                        uint256,
                        uint256[] memory liquidityMinted
                    )
                {}

        // Lines: [616-620]
            // Comment: use calldata instead of memory for `_ids`, `_amounts` 
                function burn(
                    uint256[] memory _ids,
                    uint256[] memory _amounts,
                    address _to
                ) external override nonReentrant returns (uint256 amountX, uint256 amountY) {}

        // Lines: [688-693]
            // Comment: use calldata instead of memory for `_ids` 
                function collectFees(address _account, uint256[] memory _ids)
                    external
                    override
                    nonReentrant
                    returns (uint256 amountX, uint256 amountY)
                {}

        // Lines: [844-849]
            // Comment: use calldata instead of memory for `_bin` 
                function _getPendingFees(
                    Bin memory _bin,
                    address _account,
                    uint256 _id,
                    uint256 _balance
                ) private view returns (uint256 amountX, uint256 amountY) {}

        // Lines: [861-866]
            // Comment: use calldata instead of memory for `_bin` 
                function _updateUserDebts(
                    Bin memory _bin,
                    address _account,
                    uint256 _id,
                    uint256 _balance
                ) private {}

        // Lines: [880-886]
            // Comment: use calldata instead of memory for `_bin` 
                function _cacheFees(
                    Bin memory _bin,
                    address _user,
                    uint256 _id,
                    uint256 _previousBalance,
                    uint256 _newBalance
                ) private {}

    ```
    ```Solidity
        file: src/LBQuoter.sol 
        ......................

        // Lines: [54-58]
            // Comment: use calldata instead of memory for `_route` 
                function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn)
                    public
                    view
                    returns (Quote memory quote)
                {

        // Lines: [134-138]
            // Comment: use calldata instead of memory for `_route` 
                function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
                    public
                    view
                    returns (Quote memory quote)
                {

    ```
    ```Solidity
        file: src/LBRouter.sol
        ......................

        // Lines: [42-46]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                modifier verifyInputs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath) {
                    if (_pairBinSteps.length == 0 || _pairBinSteps.length + 1 != _tokenPath.length)
                        revert LBRouter__LengthsMismatch();
                    _;
                }

        // Lines: [134-138]
            // Comment: use calldata instead of memory for `_route` 
                function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut)
                    public
                    view
                    returns (Quote memory quote)
                {

        // Lines: [207-211]
            // Comment: use calldata instead of memory for `_liquidityParameters` 
                function addLiquidity(LiquidityParameters memory _liquidityParameters)
                    external
                    override
                    returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
                {

        // Lines: [230-235]
            // Comment: use calldata instead of memory for `_liquidityParameters` 
                function addLiquidityAVAX(LiquidityParameters memory _liquidityParameters)
                    external
                    payable
                    override
                    returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
                {

        // Lines: [274-284]
            // Comment: use calldata instead of memory for `_ids`, `_amounts`
                function removeLiquidity(
                    IERC20 _tokenX,
                    IERC20 _tokenY,
                    uint16 _binStep,
                    uint256 _amountXMin,
                    uint256 _amountYMin,
                    uint256[] memory _ids,
                    uint256[] memory _amounts,
                    address _to,
                    uint256 _deadline
                ) external override ensure(_deadline) returns (uint256 amountX, uint256 amountY) {}

        // Lines: [308-317]
            // Comment: use calldata instead of memory for `_ids`, `_amounts`
                function removeLiquidityAVAX(
                    IERC20 _token,
                    uint16 _binStep,
                    uint256 _amountTokenMin,
                    uint256 _amountAVAXMin,
                    uint256[] memory _ids,
                    uint256[] memory _amounts,
                    address payable _to,
                    uint256 _deadline
                ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {}

        // Lines: [352-359]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapExactTokensForTokens(
                    uint256 _amountIn,
                    uint256 _amountOutMin,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to,
                    uint256 _deadline
                ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {}

        // Lines: [377-384]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapExactTokensForAVAX(
                    uint256 _amountIn,
                    uint256 _amountOutMinAVAX,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address payable _to,
                    uint256 _deadline
                ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {}

        // Lines: [407-413]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapExactAVAXForTokens(
                    uint256 _amountOutMin,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to,
                    uint256 _deadline
                ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {}

        // Lines: [431-438]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapTokensForExactTokens(
                    uint256 _amountOut,
                    uint256 _amountInMax,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to,
                    uint256 _deadline
                ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {}

        // Lines: [459-466]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapTokensForExactAVAX(
                    uint256 _amountAVAXOut,
                    uint256 _amountInMax,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address payable _to,
                    uint256 _deadline
                ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256[] memory amountsIn) {}

        // Lines: [496-506]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapAVAXForExactTokens(
                    uint256 _amountOut,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to,
                    uint256 _deadline
                )
                    external
                    payable
                    override
                    ensure(_deadline)
                    verifyInputs(_pairBinSteps, _tokenPath)
                    returns (uint256[] memory amountsIn)
                {}

        // Lines: [531-538]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapExactTokensForTokensSupportingFeeOnTransferTokens(
                    uint256 _amountIn,
                    uint256 _amountOutMin,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to,
                    uint256 _deadline
                ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {}

        // Lines: [561-568]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapExactTokensForAVAXSupportingFeeOnTransferTokens(
                    uint256 _amountIn,
                    uint256 _amountOutMinAVAX,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address payable _to,
                    uint256 _deadline
                ) external override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {}

        // Lines: [594-600]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function swapExactAVAXForTokensSupportingFeeOnTransferTokens(
                    uint256 _amountOutMin,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to,
                    uint256 _deadline
                ) external payable override ensure(_deadline) verifyInputs(_pairBinSteps, _tokenPath) returns (uint256 amountOut) {}

        // Lines: [642-647]
            // Comment: use calldata instead of memory for `_ids`, `_amounts`
                function sweepLBToken(
                    ILBToken _lbToken,
                    address _to,
                    uint256[] memory _ids,
                    uint256[] memory _amounts
                ) external override onlyFactoryOwner {}

        // Lines: [656-660]
            // Comment: use calldata instead of memory for `_liq` 
                function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair)
                    private
                    ensure(_liq.deadline)
                    returns (uint256[] memory depositIds, uint256[] memory liquidityMinted)
                {}

        // Lines: [701-706]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_pairs`, `_tokenPath`
                function _getAmountsIn(
                    uint256[] memory _pairBinSteps,
                    address[] memory _pairs,
                    IERC20[] memory _tokenPath,
                    uint256 _amountOut
                ) private view returns (uint256[] memory amountsIn) {}

        // Lines: [741-748]
            // Comment: use calldata instead of memory for `_ids`, `_amounts` 
                function _removeLiquidity(
                    ILBPair _LBPair,
                    uint256 _amountXMin,
                    uint256 _amountYMin,
                    uint256[] memory _ids,
                    uint256[] memory _amounts,
                    address _to
                ) private returns (uint256 amountX, uint256 amountY) {}

        // Lines: [762-768]
            // Comment: use calldata instead of memory for `_pairs`, `_pairBinSteps`, `_tokenPath` 
                function _swapExactTokensForTokens(
                    uint256 _amountIn,
                    address[] memory _pairs,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to
                ) private returns (uint256 amountOut) {}

        // Lines: [816-822]
            // Comment: use calldata instead of memory for `_pairs`, `_pairBinSteps`, `_tokenPath`, `_amountsIn`
                function _swapTokensForExactTokens(
                    address[] memory _pairs,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    uint256[] memory _amountsIn,
                    address _to
                ) private returns (uint256 amountOut) {}

        // Lines: [864-869]
            // Comment: use calldata instead of memory for `_pairs`, `_pairBinSteps`, `_tokenPath`
                function _swapSupportingFeeOnTransferTokens(
                    address[] memory _pairs,
                    uint256[] memory _pairBinSteps,
                    IERC20[] memory _tokenPath,
                    address _to
                ) private {}

        // Lines: [941-945]
            // Comment: use calldata instead of memory for `_pairBinSteps`, `_tokenPath`
                function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
                    private
                    view
                    returns (address[] memory pairs)
                {}

    ```
    ```Solidity
        file: src/LBToken.sol 
        ......................

        // Lines: [79-86]
            // Comment: use calldata instead of memory for `_accounts`, `_ids`
                function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
                    public
                    view
                    virtual
                    override
                    checkLength(_accounts.length, _ids.length)
                    returns (uint256[] memory batchBalances)
                {}

        // Lines: [149-161]
            // Comment: use calldata instead of memory for `_ids`, `_amounts` 
                function safeBatchTransferFrom(
                    address _from,
                    address _to,
                    uint256[] memory _ids,
                    uint256[] memory _amounts
                )
                    public
                    virtual
                    override
                    checkLength(_ids.length, _amounts.length)
                    checkAddresses(_from, _to)
                    checkApproval(_from, msg.sender)
                {}

    ```
    ```Solidity
        file: src/libraries/FeeDistributionHelper.sol 
        ..............................................

        // Lines: [25-30]
            // Comment: use calldata instead of memory for `_fees`
                function flashLoanHelper(
                    FeeHelper.FeesDistribution memory _fees,
                    FeeHelper.FeesDistribution storage _pairFees,
                    IERC20 _token,
                    uint256 _reserve
                ) internal {}

        // Lines: [49-53]
            // Comment: use calldata instead of memory for `_fees`
                function getTokenPerShare(FeeHelper.FeesDistribution memory _fees, uint256 _totalSupply)
                    internal
                    pure
                    returns (uint256)
                {}

    ```
    ```Solidity
        file: src/libraries/FeeHelper.sol 
        ..................................

        // Lines: [55-55]
            // Comment: use calldata instead of memory for `_fp`
                function updateVariableFeeParameters(FeeParameters memory _fp, uint256 _activeId) internal view {}

        // Lines: [49-53]
            // Comment: use calldata instead of memory for `_fp`
                function updateVolatilityAccumulated(FeeParameters memory _fp, uint256 _activeId) internal pure {}

        // Lines: [91-91]
            // Comment: use calldata instead of memory for `_fp`
                function getBaseFee(FeeParameters memory _fp) internal pure returns (uint256) {}

        // Lines: [100-100]
            // Comment: use calldata instead of memory for `_fp`
                function getVariableFee(FeeParameters memory _fp) internal pure returns (uint256 variableFee) {}

        // Lines: [116-116]
            // Comment: use calldata instead of memory for `_fp`
                function getFeeAmount(FeeParameters memory _fp, uint256 _amount) internal pure returns (uint256) {}

        // Lines: [124-124]
            // Comment: use calldata instead of memory for `_fp`
                function getFeeAmountFrom(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {}

        // Lines: [133-133]
            // Comment: use calldata instead of memory for `_fp`
                function getFeeAmountForC(FeeParameters memory _fp, uint256 _amountPlusFee) internal pure returns (uint256) {}

        // Lines: [142-146]
            // Comment: use calldata instead of memory for `_fp`
                function getFeeAmountDistribution(FeeParameters memory _fp, uint256 _fees)
                    internal
                    pure
                    returns (FeesDistribution memory fees)
                {}

        // Lines: [157-157]
            // Comment: use calldata instead of memory for `_fp`
                function getTotalFee(FeeParameters memory _fp) private pure returns (uint256) {}

    ```
    ```Solidity
        file: src/libraries/SwapHelper.sol 
        ..................................

        // Lines: [31-45]
            // Comment: use calldata instead of memory for `bin`, `fp`
                function getAmounts(
                    ILBPair.Bin memory bin,
                    FeeHelper.FeeParameters memory fp,
                    uint256 activeId,
                    bool swapForY,
                    uint256 amountIn
                )
                    internal
                    pure
                    returns (
                        uint256 amountInToBin,
                        uint256 amountOutOfBin,
                        FeeHelper.FeesDistribution memory fees
                    )
                {}

        // Lines: [87-81]
            // Comment: use calldata instead of memory for `bin`, `pairFees`, `fees`
                function updateFees(
                    ILBPair.Bin memory bin,
                    FeeHelper.FeesDistribution memory pairFees,
                    FeeHelper.FeesDistribution memory fees,
                    bool swapForY,
                    uint256 totalSupply
                ) internal pure {}

        // Lines: [107-113]
            // Comment: use calldata instead of memory for `bin`, `pair`
                function updateReserves(
                    ILBPair.Bin memory bin,
                    ILBPair.PairInformation memory pair,
                    bool swapForY,
                    uint112 amountInToBin,
                    uint112 amountOutOfBin
                ) internal pure {}

    ```
## **[GQ-02] Unused the named return variables**<a name="GQ-02"></a>

### ***Description:***

- Unnecessary gas usage on a deployment side.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/libraries/BitMath.sol
      ...............................

        // Lines: [39-49]
            // Comment: `id` hasn't been used, try `id = mostSignificantBit(x) - shift` instead of `return mostSignificantBit(x) - shift;` 
                function closestBitRight(uint256 x, uint8 bit) internal pure returns (uint256 id) {
                    unchecked {
                        uint256 _shift = 255 - bit;
                        x <<= _shift;

                        if (x == 0) return type(uint256).max;

                        // can't overflow as it's non-zero and we shifted it by `_shift`
                        return mostSignificantBit(x) - _shift;
                    }
                }

        // Lines: [56-64]
            // Comment: `id` hasn't been used, try `id = leastSignificantBit(x) + bit` instead of `return leastSignificantBit(x) + bit;` 
            function closestBitLeft(uint256 x, uint8 bit) internal pure returns (uint256 id) {
                unchecked {
                    x >>= bit;

                    if (x == 0) return type(uint256).max;

                    return leastSignificantBit(x) + bit;
                }
            }
    ``` 

## **[GQ-03] Consider `a = a + b` instead of `a += b`, in case of `a` being state variable**<a name="GQ-03"></a>

### ***Description:***

- It has an impact on the deployment side and on each distinct transaction as well, since in a case of `a += b` some extra copies of `a` are performed.

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBPair.sol
      ...............................

        // Lines: [452-453]
            _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
            _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
    ```
  
    ```Solidity
      file: src/LBToken.sol 
      ...............................

        // Lines: [211-211]
            _totalSupplies[_id] += _amount;

        // Lines: [241-241]
            _totalSupplies[_id] -= _amount;

    ```
    ```Solidity
        file: src/libraries/FeeDistributionHelper.sol 
        ..............................................

        // Lines: [41-43]
            unchecked {
                _pairFees.protocol += _fees.protocol;
            }
    ```
    ```Solidity
        file: src/LBToken.sol 
        ...............................

            // Lines: [716-721]
                if (amountX != 0) {
                    _pairInformation.feesX.total -= uint128(amountX);
                }
                if (amountY != 0) {
                    _pairInformation.feesY.total -= uint128(amountY);
                }
    ```
## **[GQ-04] Re-arrange `require/if` statements according to the gas usage**<a name="GQ-04"></a>

### ***Description:***

  - Let's take an example: 
    ```Solidity
        if (!creationUnlocked && msg.sender != _owner) 
            revert LBFactory__FunctionIsLockedForUsers(msg.sender);
    ```
    The tx should revert in any other cases: except `true && true`, but checking `msg.sender != _owner` first and then checking other part costs less gas in a cases, where the second part fails. Therefore, it's reasonable to put lightest conditions first.

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBFactory.sol 
      ....................
      
        // Lines: [241-241]
            // Comments: put the second condition first
            if (!creationUnlocked && msg.sender != _owner) revert LBFactory__FunctionIsLockedForUsers(msg.sender);

        // Lines: [245-245]
            // Comments: could be placed before the line `address _owner = owner();`
                if (_LBPairImplementation == address(0)) revert LBFactory__ImplementationNotSet();

        // Lines: [249-249]
            // Comments: could be placed before the line `address _owner = owner();`
                if (_tokenX == _tokenY) revert LBFactory__IdenticalAddresses(_tokenX);

        // Lines: [254-256]
            // Comments: could be placed before any `sload happens` 
                if (address(_tokenA) == address(0)) revert LBFactory__AddressZero();
                if (address(_LBPairsInfo[_tokenA][_tokenB][_binStep].LBPair) != address(0))
                    revert LBFactory__LBPairAlreadyExists(_tokenX, _tokenY, _binStep);

        // Lines: [543-555]
            // Comments: make some re-arrangment here as weel, if it's needed
                if (_binStep < MIN_BIN_STEP || _binStep > MAX_BIN_STEP)
                    revert LBFactory__BinStepRequirementsBreached(MIN_BIN_STEP, _binStep, MAX_BIN_STEP);

                if (_baseFactor > Constants.BASIS_POINT_MAX)
                    revert LBFactory__BaseFactorOverflows(_baseFactor, Constants.BASIS_POINT_MAX);

                if (_filterPeriod >= _decayPeriod) revert LBFactory__DecreasingPeriods(_filterPeriod, _decayPeriod);

                if (_reductionFactor > Constants.BASIS_POINT_MAX)
                    revert LBFactory__ReductionFactorOverflows(_reductionFactor, Constants.BASIS_POINT_MAX);

                if (_protocolShare > MAX_PROTOCOL_SHARE)
                    revert LBFactory__ProtocolShareOverflows(_protocolShare, MAX_PROTOCOL_SHARE); 
    ```
    ```Solidity
      file: src/LBRouter.sol 
      .......................
      
        // Lines: [243-243]
            // Comments: possible re-arrangment 
                if (_liquidityParameters.tokenX == wavax && _liquidityParameters.amountX == msg.value) {}

        // Lines: [246-246]
            // Comments: possible re-arrangment 
                } else if (_liquidityParameters.tokenY == wavax && _liquidityParameters.amountY == msg.value) {}

    ```
    ```Solidity
      file: src/libraries/FeeHelper.sol 
      ..................................
      
        // Lines: [58-58]
            // Comments: possible re-arrangment 
                if (_deltaT >= _fp.filterPeriod || _fp.time == 0) {}

    ```
## **[GQ-05] Consider `a++/++a` instead of a += 1**<a name="GQ-05"></a>

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/libraries/BitMath.sol 
      ...............................
      
        // Lines: [100-100]
            msb += 1;

        // Lines: [139-139]
            lsb += 1;

    ```
    ```Solidity
      file: src/libraries/Math512Bits.sol
      ....................................
      
        // Lines: [99-99]
            if (mulmod(x, y, 1 << offset) != 0) result += 1;

        // Lines: [159-159]
            if (mulmod(x, 1 << offset, denominator) != 0) result += 1;

    ```
## **[GQ-06] Redundant checks**<a name="GQ-06"></a>

### ***Description:***

- Possible redundant checks. 

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/LBPair.sol
      ...............................
        // Lines: [111-111]
            // Comment: it's possible to avoid the second check, since they are sorted, if you pass `_tokenA` and `_tokenB` in `LBFactory.sol::createLBPair()::268` as `_tokenX` and `_tokenY` in `LBPair.sol::initialize()`

            if (address(_tokenX) == address(0) || address(_tokenY) == address(0)) revert LBPair__AddressZero(); 
        ```

## **[GQ-07] Add `require()/reverts before some computations**<a name="GQ-07"></a>

### ***Description:***

- Everyting above `require()` takes some gas for execution, therefore if the statement reverts gas will not be retrieved.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/LBPair.sol 
      .....................
      
        // Lines: [377-377]
            // Comment: could be moved up(before `_bins[_pair.activeId] = _bin`)
                if (status != ProposalStatus.Ready && status != ProposalStatus.InProgress) {
                    revert BadProposalStatusError(status);
                }
    ```
    ```Solidity
        // Lines: [42-42]
            // Comment: could be moved up
            if (x == 0) revert Math128x128__LogUnderflow();
    ```

## **[GQ-08] Cache only the needless field of any heavy object instead of caching all attributes**<a name="GQ-08"></a>

### ***Description:***

  - Caching the whole struct or any other object into the memory requires `GCOLDSLOAD` for every single field. If the object is used for read-only purposes, there is no any reason to fetch the whole. Cache only the parts that you need and hold them as a stack variables. 

### ***All occurances:***

- Contracts:


    ```Solidity
      file: src/LBFactory.sol 
      .....................
      
        // Lines: [195-207]
            // Comments: here you can simply use `_LBPairsInfo[_tokenA][_tokenB][i].LBPair`, `_LBPairsInfo[_tokenA][_tokenB][i].createdByOwner`, `_LBPairsInfo[_tokenA][_tokenB][i].ignoredForRouting`  directly from storage. Caching into the memory is redundant here. 

                for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
                    if (_avLBPairBinSteps.decode(1, i) == 1) {
                        LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][i];

                        LBPairsAvailable[_index] = LBPairInformation({
                            binStep: i.safe24(),
                            LBPair: _LBPairInformation.LBPair,
                            createdByOwner: _LBPairInformation.createdByOwner,
                            ignoredForRouting: _LBPairInformation.ignoredForRouting
                        });
                        if (++_index == _nbAvailable) break;
                    }
                }

        // Lines: [320-327]
            // Comments: the same here ...

                LBPairInformation memory _LBPairInformation = _LBPairsInfo[_tokenA][_tokenB][_binStep];
                if (address(_LBPairInformation.LBPair) == address(0)) revert LBFactory__AddressZero();

                if (_LBPairInformation.ignoredForRouting == _ignored) revert LBFactory__LBPairIgnoredIsAlreadyInTheSameState();

                _LBPairsInfo[_tokenA][_tokenB][_binStep].ignoredForRouting = _ignored;

                emit LBPairIgnoredStateChanged(_LBPairInformation.LBPair, _ignored);

    ```
    ```Solidity
      file: src/LBPair.sol 
      .....................
      
        // Lines: [219-230]
            // Comments: great job by fetching only `activeId` from `_pairInformation`
                if (timestamp < _lookUpTimestamp) {
                    FeeHelper.FeeParameters memory _fp = _feeParameters;
                    uint256 _activeId = _pairInformation.activeId;
                    _fp.updateVariableFeeParameters(_activeId);

                    unchecked {
                        uint256 _deltaT = _lookUpTimestamp - timestamp;

                        cumulativeId += _activeId * _deltaT;
                        cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
                    }
                }

        // Lines: [282-289]
            // Comments: caching `_bins[_id]` doesn't make too much sense here, maybe I'm missing something :)

                if (_balance != 0) {
                    Bin memory _bin = _bins[_id];

                    (uint256 _amountX, uint256 _amountY) = _getPendingFees(_bin, _account, _id, _balance);

                    amountX += _amountX;
                    amountY += _amountY;
                } 

        // Lines: [705-713]
            // Comments: caching `_bins[_id]` doesn't make too much sense here, maybe I'm missing something :)

                if (_balance != 0) {
                    Bin memory _bin = _bins[_id];

                    (uint256 _amountX, uint256 _amountY) = _getPendingFees(_bin, _account, _id, _balance);
                    _updateUserDebts(_bin, _account, _id, _balance);

                    amountX += _amountX;
                    amountY += _amountY;
                }

        // Lines: [850-854]
            // Comment: just use storage `ref.debtX`, `ref.debtY`
                Debts memory _debts = _accruedDebts[_account][_id];
                amountX = _bin.accTokenXPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET) - _debts.debtX;
                amountY = _bin.accTokenYPerShare.mulShiftRoundDown(_balance, Constants.SCALE_OFFSET) - _debts.debtY;
    ```

## **[GQ-09] Use `private/internal` for `public` `const/immutable/state` variables**<a name="GQ-09"></a>

### ***Description:***

- Optimization comes from not having to create a getter function for each `public` instance.
 

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBQuoter.sol
      ......................
      
        // Lines: [21-25]
            // Comments: use `private` instead
                address public immutable routerV2;
                /// @notice Dex V1 factory address
                address public immutable factoryV1;
                /// @notice Dex V2 factory address
                address public immutable factoryV2;
    ```

## **[GQ-10] Cache state variables into a memory to avoid extra warm-accessed `sload`**<a name="GQ-10"></a>

### ***Description:***

- `mload` costs only 3 units of gas, `sload`(warm access) is about 100 units. Therefore, cache, when it's possible.

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBPair.sol 
      .........................................
      
        // Lines: [304-309]
            // Comment: it's possible to cache `tokenX` and `tokenY` here
            function swap(bool _swapForY, address _to)
                external
                override
                nonReentrant
                returns (uint256 amountXOut, uint256 amountYOut)
            {}
    ```
## **[GQ-11] Use `unchecked{}` for arithmetic operations where the operands cannot under/overflow**<a name="GQ-11"></a>

### ***Description:***

  - If there is 0% chance to run into overflow/underflow, it makes sense to put the logic inside of a `unchecked{}` block to avoid redundant checks. 

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBFactory.sol 
      ....................
      
        // Lines: [560-564]
            // Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
            // It returns 18 decimals as:
            // decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
            uint256 _prod = uint256(_maxVolatilityAccumulated) * _binStep;
            uint256 _maxVariableFee = (_prod * _prod * _variableFeeControl) / 100;
    ```

## **[G-12] Try `++i/--i` instead of `i++/i--`**<a name="G-12"></a>

### ***Description:***

  - In case of `i++`, the compiler has to to create a temp variable to return `i` (if there's a need) and then `i` gets incremented.  
  - In case of `++i`, the compiler just simply returns already incremented value.

### ***All occurances:***

- Contracts:

    ```Solidity
      file: 2022-10-traderjoe/src/LBQuoter.sol 
      .........................................
      
        // Lines: [75-75]
            for (uint256 i; i < swapLength; i++) {}
    ```

## **[G-13] Try `unchecked{++i};` instead of `i++;`/`++i;` in loops**<a name="G-13"></a>

### ***Description:***

  - Since [Solidity 0.8.0](https://github.com/ethereum/solidity/releases/tag/v0.8.0), all arithmetic operations revert on over- and underflow by default Therefore, `unchecked` box can be used to prevent all unnecessary checks, if it's no a way to get a reversion.
  
  - There are ~1e80 atoms in the universe, so 2^256 is closed to that number, therefore it's no a way to be overflowed, because of the gas limit as well. 

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBFactory.sol 
      ...............................
      
        // Lines: [165-165]
            for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {}
    ```


## **[G-14] Consider marking onlyOwner functions as payable**<a name="G-14"></a>

### ***Description:***

- This one is a bit questionable, but you can try that out. So, the compiler adds some extra conditions in case of non-payable, but we know that `onlyOwner` modifier will be reverted, if the user invoke following methods.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: src/LBFactory.sol 
      ...............................
      
        // Lines: [313-317]
            function setLBPairIgnored(
                IERC20 _tokenX,
                IERC20 _tokenY,
                uint256 _binStep,
`                bool _ignored
            ) external override onlyOwner {}
        ```

## **[G-15] Use the recent version of solidity**<a name="G-15"></a>

### ***Description:***

  - Version >= 0.8.2 provides some automatic inlining features
  - Version >= 0.8.3 comes with better struct packing
  - Version >= 0.8.10 allows to ignore contract existence checks for external calls, if it has some return value

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBPair.sol 
      ....................
      
        // Lines: [3-3]
            pragma solidity ^0.8.0;
    ```

## **[G-16] Possible names optimization**<a name="G-16"></a>

### ***Description:***

  -  The compiler sorts in alphabetic order all ids of methods (state var's generated getters) to retrieve them when invokes happen. Therefore, it's possible to change the name of a frequently-called function to get some leading zeroes in the beginning of a needed selector. 
    - `function execTransaction` -> `0x6a761202`
    - `function execTransaction32785586` -> `0x00000081`
  which will the first in a view-table after sorting, thus saves some gas avoiding extra jums each time.
  - The downside of it is code polution. Ans also, it's worth doing for a contracts with a huge amount of instances.

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBRouter.sol 
      ......................
      
        // Lines: [27-975]
            // Comment: to check out all signatures, run: `slither --solc-remaps "ds-test/=lib/forge-std/lib/ds-test/src/ forge-std/=lib/forge-std/src/ openzeppelin/=lib/openzeppelin-contracts/contracts/" src/LBRouter.sol --print function-id >& routerSelectors`

    ```
## **[G-17] Breakdown complex conditions into multiple `if/require` statements**<a name="G-17"></a>

### ***Description:***

  -  It's possible to optimize by simply removing `AND` `OR` opcodes.

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBPair.sol 
      .....................
      
        // Lines: [278-278]
            // Comments: if (i != 0) {... put the second condition and revert in it} 
                if (_lastId >= _id && i != 0) revert LBPair__OnlyStrictlyIncreasingId();
    ```


## **[G-18] Use const values instead of computing `type(uint256).max` every time**<a name="G-18"></a>

### ***Description:***

  - Using already predefined const variables instead of computing them in every single tx might not be sufficient. But I agree, there is a balance between readability and optimization. 

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBRouter.sol 
      .....................
      
        // Lines: [673-679]
            // Comments: use `0x18` instead type(uint24).max, also you can comment out that `0x18` is `type(uint24).max` for readability purposes

                depositIds = new uint256[](_liq.deltaIds.length);
                for (uint256 i; i < depositIds.length; ++i) {
                    int256 _id = int256(_activeId) + _liq.deltaIds[i];
                    if (_id < 0 || uint256(_id) > type(uint24).max) revert LBRouter__IdOverflows(_id);
                    depositIds[i] = uint256(_id);
                }
    ```



## **[G-19] Avoid memory expansions**<a name="G-19"></a>

### ***Description:***

- The memory cost function is linear up to 724 bytes of memory used, at which point additional memory costs substantially more. 
 

### ***All occurances:***

- Contracts:

    ```Solidity
      file: src/LBRouter.sol
      ......................
      
        // Lines: [360-360]
            // Comments: if the _pairs.length is about 25, it's already 800 bytes which lead to memory expansion. Be carefull with it...
                address[] memory _pairs = _getPairs(_pairBinSteps, _tokenPath);
    ```
# **Overall analysis**<a name="G-xx"></a>

## Optimization Table
  - Note that here I've highlighted only approximate amount of gas units being saved based on a theory + practical analysis using remix on test cases. The mean precision of the evaluation is about ~80%. 

| ***GQ-xx***       | ***instances***  |   ***Saved intrinsic gas per inst***    |  ***Saved deployment gas per inst***  | ***Overall deployment gas saved*** |   ***Loss in readability?***   |
| :---              |    :----:        |     :---:                               |               :---:                   |     :----:                         |   ----:                        |
| **[GQ-01](#gq-01-use-calldataload-for-read-only-arguments-in-functions)**         | 47               |               ~20-25k                   |                ~50k                   |      ~2,5bil                       |         not at all             |
| **[GQ-02](#gq-02-unused-the-named-return-variables)**         | 2                |                  ~200                   |                 ~15k                  |      ~30k                          |         not at all             |
| **[GQ-03](#gq-03-consider-a--a--b-instead-of-a--b-in-case-of-a-being-state-variable)**         | 5                |                  ~30                    |                 ~2k                   |       ~10k                         |         not at all             |
| **[GQ-04](#gq-04-re-arrange-requireif-statements-according-to-the-gas-usage)**         | 8                |                   N/A                   |               N/A                     |           N/A                      |            low                 |
| **[GQ-05](#gq-05-consider-aa-instead-of-a--1)**         | 4                |                   ~200                  |             ~34k                      |            ~130k                   |        not at all              |
| **[GQ-06](#gq-06-redundant-checks)**         | 1                |                    0                    |              ~22                      |           ~22                      |         not at all             |
| **[GQ-07](#gq-07-add-requirereverts-before-some-computations)**         | 2                |                   N/A                   |            N/A                        |        N/A                         |   not at all                   |
| **[GQ-08](#gq-08-cache-only-the-needless-field-of-any-heavy-object-instead-of-caching-all-attributes)**         | 6                |                   ~6-10k                |         ~20k                          |    ~120k                           |  not at all                    |
| **[GQ-09](#gq-09-use-privateinternal-for-public-constimmutablestate-variables)**         | 3                |                    N/A                     |          ~40k                             |       ~120k                             |  not at all       |
| **[GQ-10](#gq-10-cache-state-variables-into-a-memory-to-avoid-extra-warm-accessed-sload)**         | 1                |                    ~5k                     |             ~10k                          |          ~10k                          |      not at all          |
| **[GQ-11](#gq-11-use-unchecked-for-arithmetic-operations-where-the-operands-cannot-underoverflow)**         | 1                |                     ~15k                    |              N/A                         |          N/A                          |     not at all             |
| **[G-12](#g-12-try-i--i-instead-of-ii)**          | 3                |                     ~120                    |              ~800                         |          ~2,5k                          |    not at all            |
| **[G-13](#g-13-try-uncheckedi-instead-of-ii-in-loops)**          | 3                |                       ~6k                  |                ~30k                       |           ~90k                         |       low                         |
| **[G-14](#g-14-consider-marking-onlyowner-functions-as-payable)**          | 16               |                      ~50                   |               ~4k                        |             ~64k                       |             medium        |
| **[G-15](#g-15-use-the-recent-version-of-solidity)**          | N/A              |                      N/A                   |                N/A                       |             N/A                       |        not at all |
| **[G-16](#g-16-possible-names-optimization)**          | N/A              |                     ~1.5k                |                0                       |                  0                  |           high              |
| **[G-17](#g-17-breakdown-complex-conditions-into-multiple-ifrequire-statements)**          | 13               |                      ~50                   |             ~2k                          |           ~30k                         |        low                        |
| **[G-18](#g-18-use-const-values-instead-of-computing-typeuint256max-every-time)**          | 68               |                     0                    |                 ~12k                      |              ~816k                      |      medium                          |
| **[G-19](#g-19-avoid-memory-expansions)**          | N/A              |                     N/A                    |                 N/A                      |           N/A                         |      not at all           |



## Conclusion

  - All of the following contracts within the scope were optimized pretty well. Huge kudos for devs of `Trader Joe` for taking that much effort on that.
  - In a case if this report gets max score from `Trader Joe team`, please allocate 10% of my credits for second/third best reports as well.