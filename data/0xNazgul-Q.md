## [NAZ-L1] Missing Zero-address Validation
**Severity**: Low
**Context**: [`LBQuoter.sol#L40`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L40), [`LBRouter.sol#L52`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L52)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L2] Missing Time locks
**Severity**: Low 
**Context**: [`LBFactory.sol#L312`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L312), [`LBFactory.sol#L340`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L340), [`LBFactory.sol#L396`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L396), [`LBFactory.sol#L423`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L423), [`LBFactory.sol#L468`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468), [`LBFactory.sol#L474`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474), [`LBFactory.sol#L485`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485), [`LBFactory.sol#L493`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L493), [`LBFactory.sol#L502`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L502), [`LBFactory.sol#L520`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L520), [`LBPair.sol#L788`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L788)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a timelock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorised owners have no time for any planned incident response.


## [NAZ-L3] Lack of Event Emission For Critical Functions
**Severity**: Low
**Context**: [`LBPair.sol#L792`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L792)

**Description**:
Several functions update critical parameters that are missing event emission. These should be performed to ensure tracking of changes of such critical parameters.

**Recommendation**:
Consider adding events to functions that change critical parameters.


## [NAZ-L4] `receive()` Function Should Emit An Event
**Severity**: Low
**Context**: [`LBRouter.sol#L63`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L63)

**Description**:
Consider emitting an event inside this function with `msg.sender` and `msg.value` as the parameters. This would make it easier to track incoming ether transfers.

**Recommendation**:
Consider adding events to the `receive()` functions. 


## [NAZ-N1] Array length mismatch
**Severity**: Informational
**Context**: [`LBRouter.sol#L280-L281`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L280-L281)

**Description**:
These fail to perform input validation on arrays to verify the lengths match. A mismatch could lead to an exception or undefined behavior.

**Recommendation**:
Perform input validation on the arrays to verify that the lengths match.


## [NAZ-N2] Line Length
**Severity**: Informational
**Context**: [`LBFactory.sol#L38`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L38), [`LBFactory.sol#L44`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L44), [`LBFactory.sol#L56`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L56), [`LBFactory.sol#L307`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L307), [`LBPair.sol#L298`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L298), [`LBPair.sol#L458`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L458), [`LBPair.sol#L571`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L571), [`LBPair.sol#L610`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L610), [`LBRouter.sol#L122`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L122), [`LBRouter.sol#L139`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L139), [`Math512Bits.sol#L213`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L213), [`Math512Bits.sol#L232`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L232), [`Math512Bits.sol#L237`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L237), [`Math512Bits.sol#L246-L248`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Math512Bits.sol#L246-L248), [`Oracle.sol#L69-L70`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L69-L70), [`Oracle.sol#L134-L137`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L134-L137)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N3] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`LBQuoter.sol#L123`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L123), [`LBQuoter.sol#L203`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L203), [`LBToken.sol#L326`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L326)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N4] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`LBFactory.sol#L32`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L32), [`LBToken.sol#L29-L30`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29-L30), [`BinHelper.sol#L14`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/BinHelper.sol#L14), [`Constants.sol#L9-L13`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Constants.sol#L9-L13), [`FeeHelper.sol#L157`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L157), [`Oracle.sol#L145`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L145), [`TreeMath.sol#L70`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/TreeMath.sol#L70)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N5] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`LBFactory.sol#L215`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L215), [`LBQuoter.sol#L27`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L27), [`LBRouter.sol#L148`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L148), [`LBToken.sol#L63`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L63), [`Oracle.sol#L106`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L106), [`ReentrancyGuardUpgradeable.sol#L31`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/ReentrancyGuardUpgradeable.sol#L31)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N6] Unindexed Event Parameters
**Severity** Informational
**Context**: [`ILBFactory.sol#L53`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L53), [`ILBPair.sol#L152`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L152)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N7] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`LBQuoter.sol#L85`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L85), [`LBQuoter.sol#L163`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L163), [`JoeLibrary.sol#L39`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#l39), [`JoeLibrary.sol#L51`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/JoeLibrary.sol#L51)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N8] Spelling Errors
**Severity**: Informational
**Context**: [`LBQuoter.sol#L229 (considred => considered)`](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L229)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.


## [NAZ-N9] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N10] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-10-traderjoe/tree/main/src)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.
