## PoC : Unchecked `approve( )` return causes permanent fund loss in UDA.sol

### Pattern
Unchecked Return

### Root Cause
no return value after `approve()` function

### Assumption
No return value check from ERC20 function.

### Broken Invariant
The return value of ERC20 function are checked.

### Attack Story
No such attack Vector.The use Fund will get stucked in the Protocol. 

### Checklist
[ ]check whether which type of ERC20 tokens are allowed?
[ ]check does the WEIRD ERC20 token are manageable?
[ ]check the return value from every ERC20 function?

### Mitigation
+ use SafeERC20.sol from openzepplin.
+ use yul to check the return value and determine the returned reuslt of the function.
+ use allowist for ERC20 tokens.

