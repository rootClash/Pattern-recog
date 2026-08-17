## PoC: Divison before calculation

### Pattern
Rounding Error

### Root Cause
The protocol is a vault which has a functionality of reawarding the validators. it use the formula 
```validatorCounts[idx] * (_reward / totalActiveValidators) shares. Such share calculation suffers from a rounding error caused by division before multiplication.```
inside the function _mintRawShares() was uesd to calculate it. But as it is using , divison before multiplication it creates the rounding error.

### Assumption
reward already collected by the vault
block.timestamp > lastRewardTimestamp

### Broken Invariant


### Checklist
-> focus on math of DEFI that is important
-> test those formula

### Mitigation
-> multiply before division = (validatorCounts[idx] * _reward ) / totalActiveValidators

-------------------------------------------------------------------------------------

## PoC: Trucating the decimal in two steps

### Pattern
Rounding Error

### Root Cause
truncating the decimal in two steps reduce the reward rate

### Assumption
OperatorRewardsShare already calculated
gloabalFess are calculated

### Attack Story
No such attack story . the issue is inside the solidity behaviour dealing with the decimals

### Checklist
check the math : how divide works
eg : x = A / B  -> 1st truncate
y = X x C / z -> 2nd truncate

### Mitigation
example with above redemetion : x = A x C / z x B
this will reduce the truncation in one step but it can be scaled using 1e18 to preserve the value