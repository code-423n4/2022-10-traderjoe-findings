# Trader Joe v2 Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unspecific Compiler Version Pragma (17 instances)
2. Named return variables are not used (3 instances)

Total 20 instances of 2 issues.

## 1. Unspecific Compiler Version Pragma (17 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
LBErrors.sol::3 => pragma solidity ^0.8.0;

LBFactory.sol::3 => pragma solidity ^0.8.0;

LBPair.sol::3 => pragma solidity ^0.8.0;

LBQuoter.sol::3 => pragma solidity ^0.8.0;

LBRouter.sol::3 => pragma solidity ^0.8.0;

LBToken.sol::3 => pragma solidity ^0.8.0;

interfaces/IJoeFactory.sol::3 => pragma solidity ^0.8.0;

interfaces/IJoePair.sol::3 => pragma solidity ^0.8.0;

interfaces/IJoeRouter01.sol::3 => pragma solidity ^0.8.0;

interfaces/IJoeRouter02.sol::3 => pragma solidity ^0.8.0;

interfaces/ILBFactory.sol::3 => pragma solidity ^0.8.0;

interfaces/ILBFlashLoanCallback.sol::3 => pragma solidity ^0.8.0;

interfaces/ILBPair.sol::3 => pragma solidity ^0.8.0;

interfaces/ILBRouter.sol::3 => pragma solidity ^0.8.0;

interfaces/ILBToken.sol::3 => pragma solidity ^0.8.0;

interfaces/IPendingOwnable.sol::3 => pragma solidity ^0.8.0;

interfaces/IWAVAX.sol::3 => pragma solidity ^0.8.0;
```

## 2. Named return variables are not used (3 instances)

Consider changing the variable to be an unnamed one, or use the named returns variables instead of `return` statements.

```solidity
File: src/LBPair.sol::132-143

    function getReservesAndId()
        external
        view
        override
        returns (
            uint256 reserveX,
            uint256 reserveY,
            uint256 activeId
        )
    {
        return _getReservesAndId();
    }
```

```solidity
File: src/LBPair.sol::151-163

    function getGlobalFees()
        external
        view
        override
        returns (
            uint256 feesXTotal,
            uint256 feesYTotal,
            uint256 feesXProtocol,
            uint256 feesYProtocol
        )
    {
        return _getGlobalFees();
    }
```

```solidity
File: src/LBPair.sol::251-253

    function getBin(uint24 _id) external view override returns (uint256 reserveX, uint256 reserveY) {
        return _getBin(_id);
    }
```
