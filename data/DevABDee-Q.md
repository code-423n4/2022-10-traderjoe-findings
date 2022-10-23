## 1. `Pragma` Not fixed.
Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the pragma ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

## 2. Unnecessary use of `SafeMathLib`.
SafeMath is no longer needed starting with Solidity 0.8.x. The compiler now has built-in overflow checking