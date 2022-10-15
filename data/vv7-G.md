I. LBPair.sol

1)
```
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

...
```

_ids.length is being used on lines #482, #492, #497.
Consider caching it in a local variable.

Also, _ids and _distributionX/Y arrays are stored in 'memory'.
As these arrays are not changed during mint() call, I'd suggest changing their location to 'calldata',
to get rid of unnecessary copies.

Functions:

```
function pendingFees(address _account, uint256[] memory _ids)
    external
    view
    override
    returns (uint256 amountX, uint256 amountY);

function collectFees(address _account, uint256[] memory _ids)
        external
        override
        nonReentrant
        returns (uint256 amountX, uint256 amountY);    
```

Similar issues,  consider to cache the value of _ids.length in a local variable
and change 'uint256[] memory _ids' to 'uint256[] calldata _ids'   

2)
```
function _beforeTokenTransfer(
        address _from,
        address _to,
        uint256 _id,
        uint256 _amount
    ) internal override(LBToken) {
        unchecked {
            super._beforeTokenTransfer(_from, _to, _id, _amount);

[1]          Bin memory _bin = _bins[_id];

[2]           if (_from != _to) {
                if (_from != address(0) && _from != address(this)) {
                    uint256 _balanceFrom = balanceOf(_from, _id);

                    _cacheFees(_bin, _from, _id, _balanceFrom, _balanceFrom - _amount);
                }

                if (_to != address(0) && _to != address(this)) {
                    uint256 _balanceTo = balanceOf(_to, _id);

                    _cacheFees(_bin, _to, _id, _balanceTo, _balanceTo + _amount);
                }
            }
        }
    }
```

If check on line #2 fails, "if" block never will be executed so line #1 is a waste of gas.
I'd suggest to move the declaration of variable _bin inside if block.

II. LBQuoter.sol

function findBestPathFromAmountIn(address[] memory _route, uint256 _amountIn);
Loop on line #100:
```
    for (uint256 j; j < LBPairsAvailable.length; j++) {
```
Notes:
- cache LBPairsAvailable.length in a local variable.
- use ++j instead of j++
- change "_route"  storage type to calldata

Same applies to function findBestPathFromAmountOut(address[] memory _route, uint256 _amountOut).

III. LBRouter.sol

1. function _addLiquidity(LiquidityParameters memory _liq, ILBPair _LBPair)

"_liq.deltaIds.length" is used a lot in this function.
I'd suggest to cache it in a local variable.

2. function _swapExactTokensForTokens(),  function _swapTokensForExactTokens(), 
function _swapSupportingFeeOnTransferTokens(), function _getAmountsIn(),

- _pairs.length can be cached in a local variable.
- address[] memory _pairs need to changed to address[] calldata _pairs

3. 
```
function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
        private
        view
        returns (address[] memory pairs)
    {
        pairs = new address[](_pairBinSteps.length);

        IERC20 _token;
        IERC20 _tokenNext = _tokenPath[0];
        unchecked {
            for (uint256 i; i < pairs.length; ++i) {
                _token = _tokenNext;
                _tokenNext = _tokenPath[i + 1];

                pairs[i] = _getPair(_pairBinSteps[i], _token, _tokenNext);
            }
        }
    }
```

As _pairBinSteps.length == pairs.length i would use a single local variable for both.


II. LBToken.sol

```

function balanceOfBatch(address[] memory _accounts, uint256[] memory _ids)
        public
        view
        virtual
        override
        checkLength(_accounts.length, _ids.length)
        returns (uint256[] memory batchBalances);
```

- cache _accounts.length in a local variable
- change memory type to "calldata"

Same for function safeBatchTransferFrom().