## 1.OR CONDITIONS COST LESS THAN THEIR EQUIVALENT, AND CONDITIONS (“NOT(SOMETHING IS FALSE)” COSTS LESS THAN “EVERYTHING IS TRUE”)
the equivalent of `(a && b)` is ` !(!a || !b)`

Even with the 10k Optimizer enabled, `OR` conditions cost less than their equivalent `AND` conditions.

### Proof of Concept.
 Compare in Remix this example contract’s 2 diffs (or any test contract of your choice, as experimentation always shows the same results).
```
pragma solidity 0.8.13;
contract Test {
    bool isOpen;
    bool channelPreviouslyOpen;

    function boolTest() external view returns (uint) {
-       if (isOpen && !channelPreviouslyOpen) {
+       if (!(!isOpen || channelPreviouslyOpen)) {
            return 1;
-       } else if (!isOpen && channelPreviouslyOpen) {
+       } else if (!(isOpen || !channelPreviouslyOpen)) {
            return 2;
        }
    }
    function setBools(bool _isOpen, bool _channelPreviouslyOpen) external {
        isOpen = _isOpen;
        channelPreviouslyOpen= _channelPreviouslyOpen;
    }
}
```
 effectively saving 12 gas.

### Affected Code
It’s possible to save a significant amount of gas by replacing the `&&` conditions with their `||` equivalent in the solution.
## LBFactory.sol
[LBFactory.sol#L241](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L241)
```
EXAMPLE
Use : if (!(creationUnlocked || msg.sender == _owner))
Instead of : if (!creationUnlocked && msg.sender != _owner)
```

## LBPair.sol
[LBPair.sol#L278](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L278)
[LBPair.sol#L328](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L328)
[LBPair.sol#L500](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L500)
[LBPair.sol#L651](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L651)
[LBPair.sol#L820](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L820)
[LBPair.sol#L826](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L826)

## LBQuoter.sol
[LBQuoter.sol#L79](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L79)
[LBQuoter.sol#L82](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L82)
[LBQuoter.sol#L99](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L99)
[LBQuoter.sol#L157](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L157)
[LBQuoter.sol#L160](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L160)
[LBQuoter.sol#L176](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L176)
[LBQuoter.sol#L183](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L183)


## LBRouter.sol
[LBRouter.sol#L243](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L243)
[LBRouter.sol#L246](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L246)
[LBRouter.sol#L662](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L662)

## LBToken.sol
[LBToken.sol#L284](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L284)
[LBToken.sol#L300](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L300)

## libraries/Oracle.sol
[Oracle.sol#L118](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L118)

## libraries/TreeMath.sol
[TreeMath.sol#L33](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L33)
[TreeMath.sol#L45](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L45)