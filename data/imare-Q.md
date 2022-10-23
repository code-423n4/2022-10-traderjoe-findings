### [L01]  Wrong parameter order inside ``_beforeTokenTransfer`` in ``LBtoken::_burn`` call

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L237

should be

```
_beforeTokenTransfer(_account, address(0), _id, _amount);
```

### [L02] Inconsistent use of ``safe128`` cast in ``LBPair`` contract

In ``mint`` function ``safe128`` is used 

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L490-L491

but in ``swap`` for a similar block of code is not used

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L313-L314


### [L03] better name convention for ``LBToken``s/``LBPair``s

The name and symbol for LBTokens is fixed:

https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L29-L30

But we have different ``pairs``/LBPairs with different ``binSteps`` for this tokens.
To better differentiate them maybe use something like this:

When constructing them in LBFactory::CreateLBPair we could pass the information for a better naming like this:
```
name = string(abi.encodePacked(IERC20(tokenX).symbol(), "/", IERC20(tokenY).symbol(), "-", _binStep));
symbol = string(abi.encodePacked(IERC20(tokenX).symbol(), "/", IERC20(tokenY).symbol(), "-", _binStep));
```

or similar