- Use of floating pragma
	- Description: Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

- The imported interface ILBPair in the contract LBErrors is not used.
	- Instances:
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol#L5

- Unused file:
	- IJoeRouter02 and IJoeRouter01 are not used.

- Unused code:
	- The error defined is not called anywhere. https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBErrors.sol#L182